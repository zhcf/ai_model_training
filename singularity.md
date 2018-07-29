# Singulariy 介绍
http://singularity.lbl.gov/#home  
https://github.com/singularityware/singularity  

**1. Linux上安装Singularity**  
```
#VERSION=2.4.2
#wget https://github.com/singularityware/singularity/releases/download/$VERSION/singularity-$VERSION.tar.gz
#tar xvf singularity-$VERSION.tar.gz
#cd singularity-$VERSION
#./configure --prefix=/usr/local
#make
#make install
```

**2. 创建 Singularity镜像**   
Singularity没有镜像和实例之分，只有镜像，镜像可以直接运行，镜像是一个只读文件。 下面是3种创建镜像的方式    
（1）使用bootstrap定义文件创建镜像（bootstrap定义文件类似docker中的dockerfile），下面例子是使用bootstrap文件来创建一个CentOS的镜像:   
bootstrap文件centos.def
```
BootStrap: yum
OSVersion: 7
MirrorURL: http://mirror.centos.org/centos-%{OSVERSION}/%{OSVERSION}/os/$basearch/
Include: yum
```
Linux下执行如下命令创建centos镜像，镜像是一个只读的二进制文件
```
#singularity create Centos-7.img
#singularity bootstrap Centos-7.img centos.def
```
（2）使用docker镜像创建singularity镜像，镜像是一个只读的二进制文件

```
#singularity build debian1.simg docker://debian:latest
```
（3）对已有镜像进行修改后保存
```
#singularity build --sandbox debian1 debian1.simg
#singularity shell --writable debian1
#singularity build debian2.simg debian1
```
debian1是一个目录，debian1.simg是一个只读的二进制的镜像文件，先对已经存在的进行解压（第一步），然后以可写的方式打开和修改镜像（第二步的命令是进入镜像，然后就可以在镜像里面安装包），修改后保存为另一个镜像文件debian2.simg   

**3. 使用 Singualarity镜像**   

Singularity没有镜像和实例之分，只有镜像，镜像可以直接运行，镜像是一个只读文件。   
首先，当使用一个镜像的时候，当前用户的home目录会自动映射到镜像中。    

singularity shell debian1 .simg 进入镜像，然后在镜像里面操作，由于simg是只读镜像，因此当你退出镜像的时候，所有操作不都不会保留。如果要保留操作，需要将只读镜像先解压到一个目录singularity build --sandbox debian1 debian1.simg，然后singularity shell --writable debian1进入镜像进行操作。    

singularity exec debian1.simg /home/user/a.sh是直接运行进行中的命令    
singulariy run debian1 .simg 是运行镜像中的/.singularity.d/runscript    
    

# 为什么选择Singularity而不是Docker
- Maturity	
1.Docker is more mature than Singularity, and people are more familiar with docker and creating docker image.
2.Singularity is just 1-2 years old,  it can convert the docker image to singularity image to use.
- Performance	
1.Singularity performance is higher than docker
- Security
1.Singularity blocks privilege escalation within the container so if you want to be root inside the container, you first must be root outside the container
2.Docker instance must be launched by the root user, it has the security issues.
- Container for running job	
1.Both Docker and Singularity  support
- Container for VM usage
1.Singularity instance share the same port, same network with host OS, so it can not have the independent ip.   
2.Docker instance can have the independent ip.   
- Easy to user	
1.Singularity is more easy than docker, it does not need daemon service running in the host, it just is a library and tool.

 
