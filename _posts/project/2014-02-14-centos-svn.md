---
layout: post
title: CentOS下通过yum安装svn及配置 
author: Panda
category: project
description: 如题，纪录下自己的搭建过程。
---

## 1.环境
centos6.5

## 2.安装svn
yum -y install subversion

## 3.建立版本库目录

mkdir /www/svn/

## 4.启用

svnserve -d -r /www/svn

## 5.查看是否启动了

ps -ef|grep svn

## 6.检测启用是否正常

lsof -i:3690

## 7.建立版本库

svnadmin create /www/svn/data1

## 8.配置

cd /www/svn/data1/conf

vi svnserve.conf

anon-access=none

auth-access=write

password-db=passwd

authz-db=authz

注：修改的文件前面不能有空格，否则启动svn server出错

vi passwd

[users]

zhangtaifeng=123456

vi authz

[groups]

admin = zhangtaifeng

[/]

@admin=rw

## 9.修改权限

chown svn:svn –R /www/svn

## 10.重启svn服务

ps -ef|grep svn

kill -9 pid

svnserve -d -r /www/svn

## 10.把某个目录里的资料加入到版本库里的方法：

localhost ~ # svn import directory  /www/svn/data1 (把directory目录下的资料加入到版本库data1里面)

## 11.实现SVN与WEB同步

1)设置WEB服务器根目录为/www/webroot

2)checkout一份SVN

svn co svn://localhost/data1 /www/webroot

## 12.修改权限为WEB用户

chown -R nginx:nginx /www/webroot/

## 13.建立同步脚本

cd /www/svn/data1/hooks/

cp post-commit.tmpl post-commit

## 14.编辑post-commit,在文件最后添加以下内容

export.UTF-8

SVN=/usr/bin/svn

WEB=/www/webroot/

$SVN update $WEB –username rsync –password rsync

chown -R nginx:nginx $WEB

## 15.增加脚本执行权限

chmod +x post-commit

## 16..设置开机启动

编辑rc.local文件

#vi /etc/rc.d/rc.local

/usr/bin/svnserve -d -r /www/svn

=============================================

## 17.客户端连接

svn checkout svn://ip/data1 --username zhangtaifeng --password 123456
