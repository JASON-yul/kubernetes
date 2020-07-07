一、配置yum源，安装依赖
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# yum install epel-release
# yum install wget net-tools telnet tree nmap sysstat lrzsz dos2unix bind-utils -y
二、基础环境配置
修改配置
1.vi /etc/named.conf
~]# named-checkconf

2.vim /etc/named.rfc1912.zones

3.本地域名
vim /var/named//var/named/host.com.zone

4.外部域名
/var/named/od.com.zone
查看配置解析情况
~]#named-checkconf
~]#systemctl restart named
~]#dig -t A hdss7-12.host.com @10.4.7.11 +short
到外部网路检查，如Windows系统cmd上
~]#ping hdss7-12.host.com

5.配置所有主机dns
vi /etc/sysconfig/network-scripts/ifcfg-eth0
DNS1=10.4.7.11
vi /etc/resolv.conf
search host.com
nameserver 10.4.7.11
systemctl restart network

6.若用Windows主机的wmware需配置
wmnet8网卡更改DNS：10.4.7.11
