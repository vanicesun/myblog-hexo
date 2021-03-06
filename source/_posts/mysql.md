---
title: Linux安装mysql5.7
date: 2019-11-07 16:32:53
categories: mysql linux
tags: mysql
---

### linux上安装mysql5.7:
1.下载tar包，这里使用wget从官网下载
```
 wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz
```
2.将mysql安装到/usr/local/mysql下
```
# 解压
tar -xvf mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz
```
```
# 移动
mv mysql-5.7.22-linux-glibc2.12-x86_64 /usr/local/
```
```
# 重命名
mv /usr/local/mysql-5.7.22-linux-glibc2.12-x86_64 /usr/local/mysql
```
3.新建data目录
```
mkdir /usr/local/mysql/data
```
4.新建mysql用户、mysql用户组
```
# mysql用户组
groupadd mysql
# mysql用户
useradd mysql -g mysql
```
5.将/usr/local/mysql的所有者及所属组改为mysql
```
chown -R mysql.mysql /usr/local/mysql
```
6.配置
```
/usr/local/mysql/bin/mysql_install_db --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data
```
**如果出现以下错误：**
```
2018-07-14 06:40:32 [WARNING] mysql_install_db is deprecated. Please consider switching to mysqld --initialize
2018-07-14 06:40:32 [ERROR]   Child process: /usr/local/mysql/bin/mysqldterminated prematurely with errno= 32
2018-07-14 06:40:32 [ERROR]   Failed to execute /usr/local/mysql/bin/mysqld --bootstrap --datadir=/usr/local/mysql/data --lc-messages-dir=/usr/local/mysql/share --lc-messages=en_US --basedir=/usr/local/mysql
-- server log begin --

-- server log end --
```

 
**则使用以下命令：**
```
/usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data --initialize
```
**如果出现以下错误：**
```
/usr/local/mysql/bin/mysqld: error while loading shared libraries: libnuma.so.1: cannot open shared object file: No such file or directory
```
**则执行以下命令：**
```
yum -y install numactl
```
**完成后继续安装：**
```
/usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data --initialize
```
**编辑/etc/my.cnf**
```
[mysqld]
datadir=/usr/local/mysql/data
basedir=/usr/local/mysql
socket=/tmp/mysql.sock
user=mysql
port=3306
character-set-server=utf8
# 取消密码验证
skip-grant-tables
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# skip-grant-tables
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

### 开启服务
```
# 将mysql加入服务

cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql

# 开机自启

chkconfig mysql on

# 开启

service mysql start

#重启

service mysql restart
```
### 设置密码
```
# 登录（由于/etc/my.cnf中设置了取消密码验证，所以此处密码任意）

/usr/local/mysql/bin/mysql -u root -p

# 操作mysql数据库

>>use mysql;

# 修改密码

>>update user set authentication_string=password('你的密码') where user='root';

>>flush privileges;

>>exit;
```
### 将/etc/my.cnf中的skip-grant-tables删除

### 登录再次设置密码
（不知道为啥如果不再次设置密码就操作不了数据库了）
```
/usr/local/mysql/bin/mysql -u root -p

 >>ALTER USER 'root'@'localhost' IDENTIFIED BY '修改后的密码';

>>exit;
```
### 允许远程连接
```
/usr/local/mysql/bin/mysql -u root -p

>>use mysql;

>>update user set host='%' where user = 'root';

>>flush privileges;

>>eixt;
```
### 添加快捷方式
```
ln -s /usr/local/mysql/bin/mysql /usr/bin
```
### 开放3306端口
```
开放3306端口：firewall-cmd --zone=public --add-port=3306/tcp --permanent
重启服务：systemctl restart firewalld.service
```
### 若开放端口是出现相关问题，请参考下面博客内容：
https://blog.csdn.net/weixin_42100064/article/details/93970096
