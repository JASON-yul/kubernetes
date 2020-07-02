一、docker命令详细简介（试用centos系统）

1.下载yum源
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
~]# yum install epel-release -y
yum clean all
yum makecache
2.查看docker源的版本，安装docker。
~]# yum list docker --show-duplicates
docker --version

3.安装docker-ce
~]# yum install -y yum-utils
~]# yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
~]# yum list docker-ce --show-duplicates
配置daemon（参考频教程）
~]# vi /etc/docker/daemon.json
{
  "graph": "/data/docker",
  "storage-driver": "overlay2",
  "insecure-registries": ["registry.access.redhat.com","quay.io"],
  "registry-mirrors": ["https://q2gr04ke.mirror.aliyuncs.com"],
  "bip": "172.7.5.1/24",
  "exec-opts": ["native.cgroupdriver=systemd"],
  "live-restore": true
}

~]# systemctl enable docker
~]# systemctl start docker
~]# docker info
~]# docker run hello-world

注解：To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
    
二、docker具体操作
http://dockerhub.com/  --> http://hub.docker.com
1.docker仓库授权
~]# docker login docker.io
~]# docker search alpine
~]# docker pull alpine

2.制作新的docker镜像，并存放到远端仓库
~]# docker pull alpine:3.10.1
~]# docker images|grep alpine:3.10.1
~]# docker tag 965ea09ff2eb harbor.od.com/public/alpine:v3.10.3
~]# docker push harbor.od.com/public/alpine:v3.10.3

删除镜像
~]# docker rmi harbor.od.com/public/alpine:v3.10.3
~]# docker ps -a

启动镜像
~]# docker run --name docker名称 -ti harbor.od.com/public/alpine:v3.10.3 /bin/sh

执行后删除
~]# docker run --rm harbor.od.com/public/alpine:v3.10.3 /bin/echo hello
~]# docker exec -ti e0b7fcf28418 /bin/sh
~]# docker start/stop/restart e0b7fcf28418

删除docker
docker stop 镜像id
~]# docker rm -f myalpine1
~]# for i in `docker ps -a|grep -i exit|awk '{print $1}'`;do docker rm -f $i;done

封装新的镜像，保留镜像更改内容（原有docker不要关闭）
~]# docker commit -p  原镜像名  新镜像名

打包镜像
~]# docker save 6c2009aef1cc > harbor.od.com/public/alpine:v3.10.3

3.导入镜像
~]# docker load < harbor.od.com/public/alpine:v3.10.3
~]# docker tag 6c2009aef1cc harbor.od.com/public/alpine:v3.10.3
~]# docker run --rm -ti --name myalpine harbor.od.com/public/alpine:v3.10.3 /bin/sh
~]# docker run hello-world 2>&1 >>/dev/null
~]# docker logs -f 镜像id

4.给镜像添加映射端口
~]# docker pull nginx:1.12.2
~]# docker run --rm --name mynginx -d -p81:80 harbor.od.com/nginx:v1.12.2
~]# mkdir html
html]# wget www.baidu.com -O index.html

挂载并查看挂载信息
~]# docker run -d --rm --name nginx_with_baidu -d -p82:80 -v /root/html:/usr/share/nginx/html harbor.od.com/nginx:v1.12.2
~]# docker inspect 8c440bed2ccb
~]# docker run --rm -e E_OPTS=abcdefg  harbor.od.com/alpine:latest printenv
~]# docker exec -ti nginx_with_baidu /bin/bash

tee /etc/apt/sources.list << EOF
deb http://mirrors.163.com/debian/ jessie main non-free contrib
deb http://mirrors.163.com/debian/ jessie-updates main non-free contrib
EOF

/# apt-get update && apt-get install curl -y

~]# docker commit -p 8c440bed2ccb oldboy1103/nginx:curl

~]# docker push oldboy1103/nginx:curl

vi /data/dockerfile/Dockerfile
FROM stanleyws/nginx:v1.12.2
USER nginx
WORKDIR /usr/share/nginx/html

dockerfile]# docker build . -t docker.io/oldboy1103/nginx:v1.12.2_with_user_workdir

dockerfile]# docker run --rm -ti --name nginx123 oldboy1103/nginx:v1.12.2_with_user_workdir /bin/bash
nginx@149ac8f528cc:/usr/share/nginx/html$ whoami
nginx
nginx@149ac8f528cc:/usr/share/nginx/html$ pwd
/usr/share/nginx/html

FROM stanleyws/nginx:v1.12.2
ADD index.html /usr/share/nginx/html/index.html
EXPOSE 80

dockerfile]# docker build . -t oldboy1103/nginx:v1.12.2_with_index_expose

dockerfile]# docker run --rm -d --name nginx123 -P oldboy1103/nginx:v1.12.2_with_index_expose

FROM centos:7
ENV VER 9.11.4
RUN yum install bind-$VER -y

dockerfile]# docker build . -t oldboy1103/bind:v9.11.4_with_env_run

FROM centos:7
RUN yum install httpd -y
CMD ["httpd","-D","FOREGROUND"]

dockerfile]# docker build . -t oldboy1103/httpd:test

FROM centos:7
ADD entrypoint.sh /entrypoint.sh
RUN yum install epel-release -q -y && yum install nginx -y
ENTRYPOINT /entrypoint.sh

#!/bin/bash

/sbin/nginx -g "daemon off;"

dockerfile]# docker run --rm -p84:80 oldboy1103/nginx:mynginx

FROM oldboy1103/nginx:v1.12.2
USER root
ENV WWW /usr/share/nginx/html
ENV CONF /etc/nginx/conf.d
RUN /bin/cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime &&\ 
    echo 'Asia/Shanghai' >/etc/timezone
WORKDIR $WWW
ADD index.html $WWW/index.html
ADD demo.od.com.conf $CONF/demo.od.com.conf
EXPOSE 80
CMD ["nginx","-g","daemon off;"]

dockerfile]# vi demo.od.com.conf

server {
   listen 80;
   server_name demo.od.com;

   root /usr/share/nginx/html;
}

dockerfile]# docker run -ti --rm --name lhwl2 --net=container:d62807b4af37 oldboy1103/nginx:curl /bin/bash
