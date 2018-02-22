---
title: mac安装DJango+MySQL
date: 2017-05-27 16:37:54
meta: [Mac,DJango,MySQL]
tags: [Mac,DJango,MySQL]
---
Python下有许多款不同的 Web 框架。Django是重量级选手中最有代表性的一位。许多成功的网站和APP都基于Django

<!-- more --> 

### 安装DJango
从 [**这里**](https://www.djangoproject.com/download/)下载最新的版本：Django-1.11.1.tar.gz。然后解压安装：

```
tar zxvf Django-1.11.1.tar.gz
cd Django-1.11.1
sudo python setup.py install
```

安装成功后，创建DJango项目:

```
django-admin.py startproject HelloWorld
```

启动服务:

```
cd HelloWorld # 切换到我们创建的项目
$ python manage.py runserver
……
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

显示以上信息，说明项目已经启动，在浏览器输入[http://127.0.0.1:8000](http://127.0.0.1:8000)就可进行访问了。

### 安装MySQL

1.下载MySQL

访问MySQL的官网[http://www.mysql.com/downloads/](http://www.mysql.com/downloads/),页面下方有个MySQL Community Edition (GPL)，点击**Community (GPL) Downloads »**进入下载页，选择对应的操作系统平台和dmg文件，进行下载就好了。
下载完，双击dmg进行安装，安装结束后，会出现如下提示：

```
2017-05-27T03:27:38.037069Z 1 [Note] A temporary password is generated for root@localhost: u.gnIYl_s3hc
If you lose this password, please consult the section How to Reset the Root Password in the MySQL reference manual.
```
上面有我们登陆数据库的root账号以及初始密码，记得先保存，后面登陆进行修改。

2.下载MySQL Workbench

MySQL Workbench提供可视化的数据库界面，访问[https://dev.mysql.com/downloads/](https://dev.mysql.com/downloads/) 在下面有一个MySQL Workbench(GUI Tool)的项，点击其下的DOWNLOAD即可进入下载界面。下载完成之后安装就非常简单，双击即可安装。安装完成之后我们在“应用程序”里面就能看到MySQL Workbench.app程序了。
启动MySQL Workbench.app程序,新建数据库链接，用上面安装MySQL的账号密码登陆。过一会儿，程序会提示你更新密码，修改root默认的初始密码为你自己的就好了。

至此，就安装好了DJango和MySQL了。

### 踩过的坑

1.最开始安装MySQL的时候，不是到MySQL的官网下载，而是直接使用brew命令进行安装：

```
brew install mysql
```
但是这样子进行的安装，后续在使用DJango创建app时，会提示你要先安装mysqlclient，而继续执行：

```
sudo pip install mysqlclient
```
进行mysqlclient的安装，会导致失败。所以，建议安装MySQL，直接到官网下载dmg文件，进行安装。

2.电脑如果同时有通过 **brew** 命令和用 **dmg文件**安装过mysql，那么安装mysqlclient时，也会导致失败。这时候，必须再次通过brew命令卸载mysql：

```
brew uninstall mysql
```
这时候再安装mysqlclient就能成功了。













