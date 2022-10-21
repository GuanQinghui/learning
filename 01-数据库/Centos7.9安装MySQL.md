1. 官网下载rpm安装包  [MySQL :: Download MySQL Community Server (Archived Versions)](https://downloads.mysql.com/archives/community/)

![image-20221021152136567](https://raw.githubusercontent.com/GuanQinghui/image-hosting/master/image-20221021152136567.png)

根据自身需要，选择合适的版本下载，比如8.0或者5.7

2. 将下载好的安装包解压后将RPM文件上传至Linux服务器上

![image-20221021152254281](https://raw.githubusercontent.com/GuanQinghui/image-hosting/master/image-20221021152254281.png)

3. 总体按照如下顺序安装

```
rpm -ivh mysql-community-common-5.7.39-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.39-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-5.7.39-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-5.7.39-1.el7.x86_64.rpm

8.0版本需要多安装一个icu的包，若出现mariadb的报错，直接yum remove即可。
```

4. 查看服务装态，并停止服务
5. 初始化数据库

```
mysqld --initialize --user=root           #生成root账号的初始密码，在/var/log/mysqld.log中查看
chown -R mysql:mysql /var/lib/mysql/	#为数据库文件目录授权，必做，否则启动服务可能出问题
```

6. 启动服务，并连接数据库

```
systemctl start mysqld.service

mysql -uroot -p      #使用root账号生成的初始密码登录
```

7. 修改root账号的临时密码

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'xxxxx';
```

8. 修改完账号密码后即可退出重新使用新密码登录。