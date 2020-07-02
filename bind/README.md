一、配置yum源，安装依赖
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# yum install epel-release
# yum install wget net-tools telnet tree nmap sysstat lrzsz dos2unix bind-utils -y
二、基础环境配置
修改配置
1.vi /etc/named.conf
~]# named-checkconf
2.vim /etc/named.rfc1912.zones
