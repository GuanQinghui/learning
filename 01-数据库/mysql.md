# Mysql语法

## DDL

> 数据定义语言，用来定义数据库对象：库、表、列

### “数据库”操作语法

```python
查看所有数据库名称：SHOW DATABASES；
切换数据库：USE mydb1;
创建数据库：CREATE DATABASE [IF NOT EXISTS] mydb1；
删除数据库：DROP DATABASE [IF EXISTS] mydb1；
修改数据库编码：ALTER DATABASE mydb1 CHARACTER SET utf8
```

```python
# 创建表
CREATE TABLE stu(
    sid     CHAR(6),
    sname   VARCHAR(20),
    age     INT,
    gender  VARCHAR(10) 
);

CREATE TABLE emp(
    eid     CHAR(6),
    ename   VARCHAR(50),
    age     INT,
    gender  VARCHAR(6),
    birthday    DATE,
    hiredate    DATE,
    salary  DECIMAL(7,2),
    resume  VARCHAR(1000)
);
```

### "数据表"操作语法

```python
查看当前数据库中所有表名称：SHOW TABLES；
查看指定表的创建语句：SHOW CREATE TABLE emp;
查看表结构：DESC emp;
删除表：DROP TABLE emp;

修改表：
# 1.修改之添加列：给stu表添加classname列：
    ALTER TABLE stu ADD (classname varchar(100));
# 2.修改之修改列类型：修改stu表的gender列类型为CHAR(2)：
    ALTER TABLE stu MODIFY gender CHAR(2);
# 3.修改之修改列名：修改stu表的gender列名为sex：
    ALTER TABLE stu change gender sex CHAR(2);
# 4.修改之删除列：删除stu表的classname列：
    ALTER TABLE stu DROP classname;
# 5.修改之修改表名称：修改stu表名称为student：
    ALTER TABLE stu RENAME TO student;
```

## DML

> 数据操作语言，用来定义数据库记录（数据）

### 增

```python
INSERT INTO stu(sid, sname) VALUES('s_1001', 'zhangSan');
INSERT INTO stu VALUES('s_1002', 'liSi', 32, 'female');
```

### 删

```python
DELETE FROM stu WHERE sname=’chenQi’ OR age > 30;
DELETE FROM stu; 
# truncate 是先DROP TABLE，再CREATE TABLE。而且TRUNCATE删除的记录是无  法回滚的，但DELETE删除的记录是可以回滚的
TRUNCATE TABLE stu;
```

### 改

```python
UPDATE stu SET sname=’liSi’, age=’20’ WHERE age>50 AND gender=’male’;
```

## DCL

> 数据控制语言，用来定义访问权限和安全级别

### 创建用户

> CREATE USER 用户名@地址  IDENTIFIED BY '密码';

```python
CREATE USER user1@localhost IDENTIFIED BY ‘123’; 	#地址为主机地址，用户连接 MySQL 时所用主机的地址;如果在创建的过程中，只给出了用户名，而没指定主机名，那么主机名默认为“%”，表示一组主机，即对所有主机开放权限。
CREATE USER user2@’%’ IDENTIFIED BY ‘123’; 			#需要使用高权限账户才能创建用户
```

### 用户授权

> GRANT 权限1,权限2, .... ,权限n ON 数据库.* TO 用户名;	#用户名需带地址

```python
GRANT CREATE,ALTER,DROP,INSERT,UPDATE,DELETE,SELECT ON mydb1.* TO user1@localhost;
GRANT ALL ON mydb1.* TO user2@localhost;
```

### 撤销授权

> REVOKE 权限1,权限2, .... ,权限n ON 数据库.* TO 用户名;

```PYTHON
REVOKE CREATE,ALTER,DROP ON mydb1.* FROM user1@localhost;
```

### 全局级授权

```
grant select,insert on *.* to test@'%' identified by 'test';

权限查看命令：select * from mysql.user；
```

### 数据库级授权

```
grant select,insert,update,delete on MyDB.* to test@'%' identified by 'test';

权限查看命令：select * from mysql.db；
```

### 表级授权

```
grant all on MyDB.kkk to test@'%' identified by 'test';

权限查看命令：select * from mysql.tables_priv；
```

### 查看所有用户

```
SELECT *
FROM mysql.`user`;
```

### 查看用户权限

> SHOW GRANTS FOR 用户名;

```python
SHOW GRANTS FOR user1@localhost;
```

### 删除用户

> DROP USER 用户名;

```python
DROP USER user1@localhost;
```

### 修改用户密码

> USE mysql;
>
> UPDATE USER SET PASSWORD=PASSWORD('密码') WHERE User='用户名' and Host='IP';
>
> FLUSH PRIVILEGES;

```PYTHON
UPDATE USER SET PASSWORD=PASSWORD('1234') WHERE User='user2' and Host=’localhost’;
FLUSH PRIVILEGES;
```

## DQL

> 数据查询语言，用来查询记录

```PYTHON
语法：
SELECT selection_list /*要查询的列名称*/
  FROM table_list /*要查询的表名称*/
  WHERE condition /*行条件*/
  GROUP BY grouping_columns /*对结果分组*/
  HAVING condition /*分组后的行条件*/
  ORDER BY sorting_columns /*对结果分组*/
  LIMIT offset_start, row_count /*结果限定*/
```

### 条件查询

* =、!=、<>、<、<=、>、>=；
* BETWEEN…AND；
* IN(set)；
* IS NULL;
* AND；
* OR；
* NOT；

```python
SELECT * FROM stu 
WHERE sid IN ('S_1001','S_1002','S_1003');
SELECT * FROM stu
WHERE sname IS NOT NULL;
```

### 模糊查询

* “_”:匹配任意一个字母，5个“__”表示5个任意字母
* “%”:匹配0~n个任何字母 “

```python
# 查询姓名中第2个字母为“i”的学生记录
SELECT * FROM stu
WHERE sname LIKE '_i%';
```

### 字段控制查询

* 去除重复记录 :distinct

> SELECT DISTINCT sal FROM emp;

* 给列名添加别名

> SELECT *, sal+IFNULL(comm,0) AS total FROM emp;

### 排序

```python
SELECT * FROM emp
ORDER BY sal DESC,empno ASC;
```

### 聚合函数

* COUNT()：统计指定列不为NULL的记录行数；
* MAX()：计算指定列的最大值，是字符串类型，那么使用字符串排序运算；
* MIN()：计算指定列的最小值，是字符串类型，那么使用字符串排序运算；
* SUM()：计算指定列的数值和，不是数值类型，计算结果为0；
* AVG()：计算指定列的平均值，不是数值类型，那么计算结果为0；

### 分组（GROUP BY）查询

```python
SELECT deptno,COUNT(*)
FROM emp
WHERE sal>1500
GROUP BY deptno;
```

### HAVING子句

```python
SELECT deptno, SUM(sal) FROM emp
GROUP BY deptno
HAVING SUM(sal) > 9000;
#注：WHERE是对分组前记录的条件，如果某行记录没有满足WHERE子句的条件，那么这行记录不会参加分组；而HAVING是对分组后数据的约束
```

### LIMIT

> limit 起始行 , 查询行数 //起始行从0开始，为开区间

```PYTHON
# 查询从第四行开始的10行记录
SELECT * FROM emp LIMIT 3, 10;
```

## 完整性约束

### 主键

> primary key

```python
    创建表：定义列时指定主键

    创建表：定义列之后独立指定主键

    修改表时指定主键

    ALTER TABLE stu ADD PRIMARY KEY(sid);

    删除主键

    ALTER TABLE stu DROP PRIMARY KEY;
```

### 主键自增长

> auto_increment（主键必须是整型才可以自增长）

```python
创建表时设置主键自增长：
CREATE TABLE stu(
    sid INT PRIMARY KEY AUTO_INCREMENT,
    sname   VARCHAR(20),
    age     INT,
    gender  VARCHAR(10)
);

修改表时设置主键自增长：
ALTER TABLE stu CHANGE sid sid INT AUTO_INCREMENT;

修改表时删除主键自增长：
ALTER TABLE stu CHANGE sid sid INT;
```

### 非空

> NOT NULL

```PYTHON
字段设为非空后，插入记录时必须给值
```

### 唯一

> UNIQUE

```python
字段指定唯一约束后，字段的值必须是唯一的
```

### 外键

```python
外键是另一张表的主键 ！！
外键就是用来约束这一列的值必须是另一张表的主键值！!
```

```python
# 创建表时设置外键

CREATE TABLE t_section(
    sid INT PRIMARY KEY AUTO_INCREMENT,
    sname   VARCHAR(30),
    u_id    INT,
    CONSTRAINT fk_t_user FOREIGN KEY(u_id) REFERENCES t_user(uid)
);

# 修改表时设置外键

ALTER TABLE t_session
ADD CONSTRAINT fk_t_user
FOREIGN KEY(u_id)
REFERENCES t_user(uid);

# 修改表时删除外键

ALTER TABLE t_section
DROP FOREIGN KEY fk_t_user;
```

## Mysql数据库备份与还原

### 生成SQL脚本（备份）

```python
脚本文本中只包含数据库的内容，而不会存在创建数据库的语句!
未登录mysql状态下：
mysqldump –u 用户名 –p 密码 数据库名>生成的脚本文件路径
mysqldump -uroot -p123456 javaclass>E:\javaclass.sql
```

### 执行SQL脚本

```python
执行SQL脚本需要登录mysql，进入指定数据库，执行SQL脚本！
无需登录mysql
mysql –u用户名 –p密码 数据库<要执行脚本文件路径 :
mysql -uroot -p123456 javaclass<E:\javaclass.sql
```

## 多表查询

* 合并结果集

* 连接查询

  1. 内连接
  2. 外连接
     1. 左外连接
     2. 右外连接
     3. 全外连接（MYSQL不支持）

     3.自然连接

* 子查询

### 合并结果集

* UNION：去除重复记录 `SELECT * FROM t1 UNION SELECT * FROM t2；`
* UNION ALL:不去除重复记录`SELECT * FROM t1 UNION ALL SELECT * FROM t2;`

### 连接查询

#### 内连接

```python
# 方言版
SELECT e.ename,e.sal,e.comm,d.dname 
FROM emp AS e,dept AS d
WHERE e.deptno=d.deptno;
# 标准版
SELECT * 
FROM emp e 
INNER JOIN dept d 
ON e.deptno=d.deptno;
```

#### 左连接

```python
SELECT * FROM emp e 
LEFT OUTER JOIN dept d 
ON e.deptno=d.deptno;
```

#### 右连接

```python
SELECT * FROM emp e 
RIGHT OUTER JOIN dept d 
ON e.deptno=d.deptno;
```

#### 自然连接

```python
SELECT * FROM emp NATURAL JOIN dept;
SELECT * FROM emp NATURAL LEFT JOIN dept;
SELECT * FROM emp NATURAL RIGHT JOIN dept;
```

### 子查询

```python
嵌套查询，即SELECT中包含SELECT，如果一条语句中存在两个，或两个以上SELECT，那么就是子查询语句了。
子查询出现的位置：
    where后，作为条件的一部分
    from后，作为被查询的一条表
```

### 字符集

```
SHOW VARIABLES LIKE 'character%';    #查询当前数据库的字符集情况
```

修改数据库的字符集

vim /etc/my.cnf

```
character_set_server=utf8               
```

修改完之后需要重启Mysql 服务

修改已创建数据库的字符集：

```
ALTER DATABASE Mysql CHARACTER SET 'utf8';
```

修改已创建数据表的字符集：

```
ALTER TABLE Customers CONVERT TO CHARACTER SET 'utf8';
```

## mysql数据库目录结构

1. 数据库文件存放路径：/var/lib/mysql/
2. 数据目录对应着一个变量：datadir

```
SHOW VARIABLES LIKE 'datadir';          #查询的结果就是数据库文件存放路径
```

3. 命令相关目录：/usr/bin

![image-20221020222910585](D:\Person_data\02-数据库\mysql\image-20221020222910585.png)

4. 配置文件目录：/usr/share/mysql和/etc/my.cnf
5. 系统表空间  && 独立表空间

```
SHOW VARIABLES LIKE 'innodb_file_per_table';   #查看数据库是否使用独立表空间
```

系统表空间：/var/lib/mysql/ibdata1文件为系统表空间

独立表空间：/var/lib/mysql/Mysql相应数据库对应的目录下的xxxx.ibd文件即为独立表空间