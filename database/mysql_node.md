mysql 操作 Note 2018-03-23T16.05.49
========================

### 字段类型

	int long double float varchar char date timestamp clob blob text

### 自动递增

		create table dept(
				deptno int primary key auto_increment, 
				dname varchar(20),
				loc varchar(20)
		) default charset=utf8;

### 数据库操作

如果想对表操作，必需先使用某个库

1.	显未所有库名

		shwo databases;

	.	使用某个数据库

		use 库名;

	.	删除某个数据库

		drop database 库名

	.	创建数据库

		create database 库名 default charset utf8;

	.	显示创建库的sql语句

		show create database 库名;

### 表操作

表引擎， MyISAM、 InnoDB 等类型。 一般默认为 MyISAM, 建议时指定为 InnoDB 类型。 InnoDB 支持事务、 表行级锁;

1.	显示当前库所有表

		show tables;

	.	查看表结构
	
		desc 表名;

3. 其他表操作

	oracle 笔记
#### 分页 limit
	select * frrom 表名 limit  起始记录位置, 记录数量;

### 函数

