# <center>准备Host环境</center>
**1.安装Redhat 7.4或者CentOS 7.4.**

**2.Redhat7.4或者CentOS7.4上安装cuda**

安装cuda时候，要求系统不能是以桌面方式启动的，先设置系统从命令行启动
```
# systemctl set-default multi-user.target
# reboot
```
下载cuda_8.0.61_375.26_linux-run，建议run方式安装cuda，不要用yum方式安装cuda
```
# ./cuda_8.0.61_375.26_linux-run --silent --driver --toolkit --samples  --no-opengl-libs --verbose
# cd /etc/ld.so.conf.d
# vi cuda.conf
/usr/local/cuda/lib64

# ldconfig
# cd /root/NVIDIA_CUDA-8.0_Samples/1_Utilities/deviceQuery
# make
# ./deviceQuery
```
cudnn，这一步可选，如果需要在host上安装caffe，需要安装cudnn，如果使用镜像，不需要在host上安装cudnn。如果要安装cudnn，下载cudnn-8.0-linux-x64-v7.tgz，然后安装cudnn
```
# tar -xvf cudnn-8.0-linux-x64-v7.tgz
# cd cuda
# cp include/cudnn.h /usr/local/cuda/include/
# cp lib64/* /usr/local/cuda/lib64/.
# ldconfig
```
选择CUDA版本时需要注意，Host上的cuda驱动和镜像里面CUDA的版本要兼容 

**3.Redhat7.4或者CentOS7.4上安装Singularity**


# <center>使用镜像<center>
## 使用基础镜像 

**1.下载镜像**   
https://pan.baidu.com/s/1o8Wxx8I  
CentOS：centos.simg  
CentOS with VNC：centos_vnc.simg  
CentOS with VNC with cuda80/cudnn：centos_vnc_cuda80.simg  
CentOS with VNC with cuda91/cudnn：centos_vnc_cuda91.simg  

**2. 使用镜像**  
用户可以使用基础镜像创建新的镜像  
普通镜像  
```
# singularity build --sandbox centos centos.simg
# singularity shell --w centos
```
支持vnc的镜像,可以在镜像里面启动vncserver，默认启动的vnc端口是5909，vnc密码是vncpasswd,可以参考镜像中vncstartup下的readme文件修改默认端口和密码
```
# singularity build --sandbox centos_vnc centos_vnc.simg
# singularity shell --w centos_vnc
# cd /vncstartup
# ./vnc_startup.sh
```
支持cuda的镜像，启动的时候必须带--nv 
```
# singularity build --sandbox centos_vnc_cuda80 centos_vnc_cuda80.simg
# singularity shell --w --nv centos_vnc_cuda80
```
如果出现如下的WARNING: Skipping user bind, non existent bind point (file) in container: '/bin/nvidia-smi'，镜像里面使用touch /bin/nvidia-smi创建一个空的/bin/nvidia-smi文件, 然后exit退出镜像，重新登录镜像。   
如果在运行Caffe或者Caffe2的时候出现error == cudaSuccess. 30 vs 0，这时候需要在host主机上运行devicequery。 
```
#cd /root/NVIDIA_CUDA-8.0_Samples/1_Utilities/deviceQuery
#make
#./deviceQuery
```
##  使用Caffe镜像
**1.下载镜像**  
https://pan.baidu.com/s/1o8Wxx8I  
centos_vnc_cuda80_caffe.simg  

**2.使用镜像**  
使用下面的命令，进入镜像，并在镜像内启动vncserver  
```
#singularity build --sandbox centos_vnc_cuda80_caffe centos_vnc_cuda80_caffe.simg
#singularity shell --w --nv centos_vnc_cuda80_caffe
#cd /vncstartup
#./vnc_startup.sh
```
客户端使用vncviewer访问这个镜像，ip为host主机的ip，端口为5909，密码为vncpasswd   
vnc登录后， 在vnc桌面上点击pycharm，启动pycharm开发工具, pycharm的工程在/opt/project下面   
caffe安装在镜像的/opt目录下，详细使用见镜像/opt下的readme   

##  使用Caffe2镜像
**1.下载镜像**  
https://pan.baidu.com/s/1o8Wxx8I  
centos_vnc_cuda80_caffe2.simg   
centos_vnc_cuda91_caffe2.simg  

**2.使用镜像**  
使用下面的命令，进入镜像，并在镜像内启动vncserver   
```
# singularity build --sandbox centos_vnc_cuda80_caffe2 centos_vnc_cuda80_caffe2.simg
# singularity shell --w --nv centos_vnc_cuda80_caffe2
# cd /vncstartup
# ./vnc_startup.sh
```
在客户端使用vncviewer访问这个镜像，ip为host主机的ip，端口为5909，密码为vncpasswd   
vnc登录后， 在vnc桌面上点击pycharm，启动pycharm开发工具, pycharm的工程在/opt/project下面   
caffe2安装在镜像的/opt目录下，详细使用见镜像/opt下的readme     

##  使用Caffe2的镜像，镜像支持faster r-cnn, mask r-cnn等
**1.下载镜像**   
https://pan.baidu.com/s/1o8Wxx8I   
centos_vnc_cuda80_caffe2_detectron.simg  
centos_vnc_cuda91_caffe2_detectron.simg  

**2.使用镜像**  
使用下面的命令，进入镜像，并在镜像内启动vncserver  
```
# singularity build --sandbox centos_vnc_cuda80_caffe2_detectron centos_vnc_cuda80_caffe2_detectron.simg
# singularity shell --w --nv centos_vnc_cuda80_caffe2_detectron
```
在客户端使用vncviewer访问这个镜像，ip为host主机的ip，端口为5909，密码为vncpasswd    
vnc登录后， 在vnc桌面上点击pycharm，启动pycharm开发工具, pycharm的工程在/opt/project下面，工程就是物体识别的代码    
caffe2安装在镜像的/opt目录下，详细使用见镜像/opt下的readme    

##  使用TensorFlow镜像  
**1.下载镜像**   
https://pan.baidu.com/s/1o8Wxx8I  
centos_vnc_cuda80_tensorflow.simg    

**2.使用镜像**   
使用下面的命令，进入镜像，并在镜像内启动vncserver  
```
# singularity build --sandbox centos_vnc_cuda80_tensorflow centos_vnc_cuda80_tensorflow.simg
# singularity shell --w --nv centos_vnc_cuda80_tensorflow
# cd /vncstartup
# ./vnc_startup.sh
```
在客户端使用vncviewer访问这个镜像，ip为host主机的ip，端口为5909，密码为vncpasswd  
vnc登录后， 在vnc桌面上点击pycharm，启动pycharm开发工具, pycharm的工程在/opt/project下面  

