---
title: win10系统下安装mySQL（5.7）
date: 2017-05-24 17:59:10
tags: 
    hello 安装
    数据库
updated: 2017-05-24 17:59:10
---

## win10系统下安装mySQL

想着学学后台基础，就跟着廖雪峰老师的教程[JavaScript教程](http://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000)做了下，到了数据库这一步发现有几个坑，就整理下方便下次安装。

1. 要安装mysql，首先我们得去mysql官网[下载](https://dev.mysql.com/downloads/mysql/)我们需要的资源，这是今天我们要学习配置的版本。
 
2. 我下载的时候只有5.7版本的压缩包版，所以大家可以根据自己的实际情况选择下载。

3. 下载完成后，新建一个文件夹 *MySQL-5.7* ，然后解压到里面。

4. 解压完成后，在 *MySQL-5.7* 里面新建一个配置文件 *my.ini*，用记事本打开，输入mysql的基本配置：  
   ```
    [mysql]  
    ; 设置mysql客户端默认字符集  
    default-character-set=utf8  
    [mysqld]  
    ;设置3306端口  
    port = 3306   
    ; 设置mysql的安装目录  
    basedir=E:\mysql-5.7  
    ; 设置mysql数据库的数据的存放目录  
    datadir=E:\mysql-5.7\data  
    ; 允许最大连接数  
    max_connections=200  
    ; 服务端使用的字符集默认为8比特编码的latin1字符集  
    character-set-server=utf8  
    ; 创建新表时将使用的默认存储引擎  
    default-storage-engine=INNODB  
   ```

5. 用管理员的身份打开CMD窗口后，将目录切换到你解压的文件 *bin* 目录下。再输入 `mysqld init`。  
    >1）win10系统下右键点击开始菜单，选择Windows PowerShell（管理员）即可；  
    2）注意输入的是 ``mysqld`` ,而不是 `mysql`；  
    3）注意把解压后的 *bin* 路径配置到系统环境 *path* 中，不然下面的代码不会生效。

6. 接下来我们用 ``mysqld  --initialize`` 先初始化data目录。要不然mysql5.7的文件夹下面不会出现data文件夹。  

7. 启动mysql服务 ``net start mysql``  
如果启动报错系统错误2，找不到指定文件，则修改注册表  
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\MySQL的"ImagePath"键值，改为  "你的路径/bin/mysqld" MySQL  
再次启动mysql服务 ``net start mysql``  
关闭mysql服务 ``net stop mysql``  
   >也可以 ``Win + R`` 键，输入 ``services.msc`` 手动开启或关闭mysql服务

8. 修改登录密码  
在安装mysql5.7版本时，经常会遇到mysql -u root -p直接回车登陆不上的情况，原因在于5.7版本在安装时自动给了一个随机密码，坑爹的是在init步骤的时候不像linux系统会给出命令行提示;  
首先，关闭mysql服务，在提示命令管理工具（**管理员权限**）输入如下命令，进入安全模式：`mysqld --defaults-file="你的路径\my.ini" --console --skip-grant-tables`，这里的路径需要根据你安装的实际路径修改。好的，这个窗口我们让它就这么运行，然后重新打开一个命令提示窗口，记得一定是管理员权限。  
接着我们继续输入用户名和密码登陆。别急，这只是第一步。接下来我们还是要修改root的默认密码。  
1.进入mysql数据库：  
    ```
    mysql> use mysql;  
    Database changed    
    ```  
    
    2.给root用户设置新密码  
    ```
    mysql> update user set authentication_string=password("新密码") where user="root";  
    Query OK,1 rows affected(0.01 sec)Rows matched:1 Changed:1Warnings: 0
    ```
    3.刷新数据库 （一定要记得刷新）  
    ```
    mysql>flush privileges;  
    QueryOK, 0 rows affected (0.00 sec)
    ```
    4.退出mysql：  
    `mysql> quit`  
下次输入 `mysql -uroot -p`  就可以用新密码登录了。

 
