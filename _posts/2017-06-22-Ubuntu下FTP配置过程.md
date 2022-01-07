---
layout: post
title: Ubuntu下FTP配置过程
tags: FTP
math: true
date: 2017-06-22 15:32 +0800
---

1)先安装vsftpd

``` shell
sudo apt-get install vsftpd
``` shell
2)修改配置文件
``` shell
sudo vim /etc/vsfpd.conf
```
修改以下内容：
``` shell
listen=YES
anonymous_enable=NO (关系到匿名用户是否能登陆)
write_enable=YES(关系到能否上传文件，这一行默认是注释掉的，去掉前面的#号)
pam_service_name=ftp(默认值是vsftpd，如果默认的方式登陆不上，就改成ftp)
chroot_local_user=NO
chroot_list_file=/etc/vsftpd.chroot_list
```
3)启动vsftpd
``` shell
sudo service vsftpd start
```
4)添加用户
``` shell
sudo useradd -d /home/ftp_testusesr -m ftp_testuser
sudo passwd ftp_testuser
```

其中：ftp_testusesr为用户名，/home/ftp_testusesr为用户的工作路径。


注意：ftp默认的路径是/srv/ftp下，可以在里面建立一个目录当工作路径，有一个小问题需要注意，文件夹的权限一定要控制好，不然传不上文件。一般用chmod 777 filename 修改文件夹权限。

 

事实上这样的配置在Ubuntu下一般不给力，会出现530 登陆错误，解决的办法是：

修改配置文件
``` shell
sudo vim /etc/vsfpd.conf
```

``` shell
listen_ipv6=NO
#chroot_local_user=NO
#chroot_list_file=/etc/vsftpd.chroot_lis
```

