---

title: MySQL 的安装与远程连接

subtitle: 在 Linux 上安装，在 Windows 上访问

tags: [DataBase, Linux]

---

MySQL 是目前最流行的数据库管理系统之一，而且是开源的。
本文简单罗列了在 Linux 系统（Debian）的云服务器上安装和允许远程连接的步骤，提供在 Windows 上远程连接 MySQL 的方法。

# 安装 MySQL

更新 packages
```shell
sudo apt update
sudo apt upgrade
```

安装 wget
```shell
sudo apt install wget
```

下载 MySQL APT package
```shell
wget https://dev.mysql.com/get/mysql-apt-config_0.8.22-1_all.deb
```

安装 package
```shell
sudo apt install ./mysql-apt-config_0.8.22-1_all.deb
```

安装 MySQL
```shell
sudo apt update
sudo apt install mysql-server
```

安装完成后，MySQL 自动启动。检查 MySQL 状态
```shell
sudo service mysql status
```

输出应该类似这样，代表安装成功
```
 mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2022-02-07 09:32:48 UTC; 17s ago
       Docs: man:mysqld(8)
             http://dev.mysql.com/doc/refman/en/using-systemd.html
   Main PID: 101929 (mysqld)
     Status: "Server is operational"
      Tasks: 38 (limit: 1148)
     Memory: 369.3M
        CPU: 805ms
     CGroup: /system.slice/mysql.service
             └─101929 /usr/sbin/mysqld

Feb 07 09:32:48 demo systemd[1]: Starting MySQL Community Server...
Feb 07 09:32:49 demo systemd[1]: Started MySQL Community Server.
```

# 允许远程连接

登录 MySQL，输入密码
```shell
mysql -u root -p
```

选择数据库
```mysql
USE mysql
```

创建用户，替换其中的 your_user_name 和 your_password
```mysql
CREATE USER 'your_user_name'@'%' IDENTIFIED BY 'your_password';
```

授予权限给刚才创建的用户，“\*.\*“代表所有数据库的所有表，“%”代表来自所有地址
```mysql
GRANT ALL ON *.* TO 'your_user_name'@'%' WITH GRANT OPTION;
```

刷新访问权限表
```mysql
FLUSH PRIVILEGES;
```

检查用户列表，应该出现一行，其中用户名是刚才设置的，host 是%
```mysql
SELECT user,host from user;
```

最后在服务器后台配置防火墙，允许 TCP 协议的 3306 端口。

# 远程连接

在 Windows 上，可以下载一个 [MySQL Installer for Windows](https://dev.mysql.com/downloads/installer/) ，选择Client only安装，连接。

或者用 Pycharm ，点击右侧“数据库”标签，”新建“->”数据源“->”MySQL“。“主机”栏填服务器 IP 地址，用户名和密码填刚才设置的。点下面的“测试连接”，成功后按“确定”。然后会出现一个 console，输入 SQL 语句之后运行即可。
