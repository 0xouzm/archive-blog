---
title: "mysql root用户修改密码"
date: 2018-10-24 21:40
categories: mysql
tags: chaos
---

# 修改密码



1、安装

```
$ sudo apt-get install mysql-server
$ apt install mysql-client
$ apt install libmysqlclient-dev
```

 以此在终端输入上述代码，等待安装。装好后，输入以下代码检查下是否安装成功。

```
$ sudo netstat -tap | grep mysql
$ netstat -tap | grep mysql
tcp6        0       0       [::]:mysql    [::]:*    LISTEN    7510/mysqld
```

 如果出现第三行提示，则表明安装成功。

2、登入MySQL

```
$ mysql -u root -p
```

 输入密码，如果可以进入则下面的不用看了；如果提示不能登入，则我们尝试启用安全模式登入MySQL，这样可以绕过密码登入，登入后再修改密码。

3、安全模式登入MySQL

```
$ sudo /etc/init.d/mysql stop
-------------------------------------
[sudo] wl 的密码：
[ ok ] Stopping mysql (via systemctl): mysql.service.
$ sudo /usr/bin/mysqld_safe --skip-grant-tables --skip-networking &
```

 输入第一行终止MySQL运行，成功，会提示下面两行；输入第四行，成功，没有任何报错则可以另外打开一个终端窗口进行下一步操作；但是一般会报错，比如提示`mysqld_safe Directory ‘/var/run/mysqld’ for UNIX socket file don’t exists`

因此我们尝试输入以下代码

```
$ sudo mkdir -p /var/run/mysqld
$ sudo chown mysql:mysql /var/run/mysqld
```

 最后再次输入：

```
sudo /usr/bin/mysqld_safe ``--skip-grant-tables --skip-networking &
```

 到了这里不在提示错误，可以打开另一个终端端口了，尝试无密码登入MySQL。

```
mysql -u root
```

 到这里应该可以进入MySQL了，继续操作

```
> use mysql;
> update user set authentication_string=PASSWORD("这里输入你要改的密码") where User = 'root'; #更改密码
> update user set plugin= "mysql_native_password"; #如果没这一行可能也会报一个错误，因此需要运行这一行
> flush  privileges ; #更新所有操作权限
> quit;
```

 4、使用修改的密码登入MySQL

经过上面一系列的操作，应该可以正常使用你更改的密码登入了。

```
> sudo /etc/init.d/mysql stop
> 
> sudo /etc/init.d/mysql start # reset mysql
> mysql -u root -p
```

 第一行先终止数据库运行，第二行重启数据库服务，第三行root用户登入。



# 远程权限

### 开放3306端口

首先确认3306端口是否对外开放，mysql默认状态下是不开放对外访问功能的。查看方法如下：

```
# netstat -an | grep 3306
tcp    0   0 127.0.0.1:3306      0.0.0.0:*         LISTEN
```

从上面可以看出，mysql的3306端口只是监听本地连接`127.0.0.1`。我们做下修改，使其对外其他地址开放。
 打开`/etc/mysql/my.cnf`文件

```
# vim /etc/mysql/my.cnf
```

找到`bind-address = 127.0.0.1`这一行，大概在47行，我们将它注释掉。




![img](https:////upload-images.jianshu.io/upload_images/31920-1427a7891cea11f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/591/format/webp)

Paste_Image.png



###  授权用户远程访问

为了让访问mysql的客户端的用户有访问权限，我们可以通过如下方式为用户进行授权：
 首先进入mysql

```
# mysql -uroot -pyour_password
```

授权：

```
mysql> grant all on *.* to user_name@'%' identified by 'user_password';
```

### 重启mysql服务，使配置生效

```
# /etc/init.d/mysql restart
```

# 远程db没有mysql库，添加root密码

1. 关闭mysql

   `service mysqld stop`

2. 屏蔽权限

```
# mysqld_safe --skip-grant-table
 or 
# /usr/bin/mysqld_safe --skip-grant-table
```

3. 新开终端

   ```
   mysql -u root mysql
   UPDATE user SET Password=PASSWORD('newpassword') where USER='root';//重置root密码
    
   delete from user where USER='';//删除空用户
    
   FLUSH PRIVILEGES;//记得要这句话，否则如果关闭先前的终端，又会出现原来的错误
   exit
   
   ```

4. `service mysqld restart`