### 部署步骤
#####  1. 将slinux、iptables关闭

```
# systemctl stop firewalld.service
# systemctl disable firewalld.service
# setenforce 0
```
##### 2. 使用豆瓣pip源

```
# mkdir -p ~/.pip          
# vi ~/.pip/pip.conf
```

![这里写图片描述](https://img-blog.csdn.net/20180415181534686?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlYXNoYXBlcl8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

在`pip.conf`中写入如下内容：

```
[global]
index-url = http://pypi.douban.com/simple/
[install]
trusted-host = pypi.douban.com
```

< img src="https://img-blog.csdn.net/20180415181946184?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlYXNoYXBlcl8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" />

##### 3. 下载并安装epel
```
# wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
# rpm -ivh epel-release-latest-7.noarch.rpm
# yum repolist
```

< img src="https://img-blog.csdn.net/20180415182606115?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlYXNoYXBlcl8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70", width=70% />

< img src="https://img-blog.csdn.net/20180415183228872?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlYXNoYXBlcl8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70", width=75% />

##### 4. 安装必要的工具包

```
# yum install -y python-pip
# pip install --upgrade pip
# pip install -U os-testr 
# yum -y install bridge-utils  
# yum -y install net-tools  
# yum install -y git yum-utils
```
##### 5. 更新系统

```
# yum update
```
##### 6. 添加stack用户，分配权限

```
# sudo useradd -s /bin/bash -d /opt/stack -m stack
# sudo tee <<<"stack ALL=(ALL) NOPASSWD: ALL" /etc/sudoers
# sudo su - stack
```

<img src="https://img-blog.csdn.net/20180415183521352?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlYXNoYXBlcl8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70", width=80%>

##### 7. 克隆devstack

```
# git clone https://git.openstack.org/openstack-dev/devstack
# cd devstack
```
##### 8. 创建local.conf文件，写入如下内容（密码替换成自己的）

```
[[local|localrc]]
ADMIN_PASSWORD=secret
DATABASE_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD

enable_plugin barbican https://git.openstack.org/openstack/barbican
```

##### 9. 开始安装

```
# ./stack.sh
```

### 部署可能遇到的问题及解决方案

（1）报错内容
```
Command "python setuo.py egg_info" failed with error code 1 in...
```
解决方案
```
# sudo pip install --upgrade setuptools
# ./stack.sh
```
（2）

修改yum源为国内的yum源，首先备份本地yum源：
```
# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo_bak
```
获取阿里yum源配置文件：
```
# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```
更新cache：
```
# yum makecache 
```
查看：
```
# yum -y update
```
（3）报错内容
```
wget: unable to resolve host address
```
wget：无法解析主机地址，这就能看出是DNS服务器的问题。
解决方案：

 1. 进入root
 2. 打开`/etc/resolv.conf`
 
< img src="https://img-blog.csdn.net/20180415211612459?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlYXNoYXBlcl8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70", width=60% />

 3. 添加以下内容：
 
 ```
 nameserver 8.8.8.8    
 nameserver 8.8.4.4    # 两者都为本地DNS服务器的IP地址
 ```
