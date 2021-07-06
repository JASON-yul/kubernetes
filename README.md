k8s学习：https://www.yuque.com/u1835249/xx770n/wxegb0

         https://jimmysong.io/kubernetes-handbook/   （重要**************************************************************）

k8s对接ceph参考：  https://codeleading.com/article/92503363236/
  
                   https://kubesphere.com.cn/forum/d/3324-ceph-rbd-pvc/2
  

7台机器k8s扩容安装：https://github.com/kubernetes-sigs/kubespray#requirements

# kubernetes
环境部署：
1.配置主机名：hostnamectl set-hostname xx.host.com

2.关闭防火墙：systemctl stop firewalld && systemctl disable firewalld  && \
  setenforce 0 &&sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config
  
3.设置网卡cat /etc/sysconfig/network-scripts/ifcfg-eth0 
TYPE=Ethernet
BOOTPROTO=none
NAME=eth0
DEVICE=eth0
ONBOOT=yes
IPADDR=192.168.11.1
NETMASK=255.255.255.0
GATEWAY=192.168.1.254
DNS1=192.168.1.254

4.设置yum源
wget -O /etc/yum.repos.d/CentOS-Base.repo  http://mirrors.aliyun.com/repo/Centos-7.repo
wget -O /etc/yum.repos.d/epel.repo  http://mirrors.aliyun.com/repo/epel-7.repo
yum clean all
yum makecache

5.安装基础工具
yum install wget net-tools telnet tree nmap sysstat lrzsz dos2unix bind-utils -y

6.安装bind9详见bind
7.签发证书
8.安装docker-ce（17.6以上）
9.安装私有镜像仓库harbor
