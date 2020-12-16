#MySQL数据库基本操作

## 一、初识SQL语句

SQL语言主要用于存取数据，查询数据，更新数据，和管理关系数据库系统，SQL语言分为五种类型：

* DDL语句

  数据库定义语言：数据库、表、视图、索引、存储过程。例如：CREATE、DROP、ALTER。

* DML语句

  数据库操作语言：插入数据INSERT、删除数据DELETE、更新数据UPDATE。

* DCL语句

  数据库控制语言：例如控制用户的访问权限GRANT、REVOKE。

* DQL语句

  数据库查询语言：查询数据SELECT。

* TCL语句

  事务控制语言：事务和事务的处理。

## 二、MySQL常见的数据类型

在MySQL数据库管理系统中，可以通过存储引擎来决定表的类型。同时，MySQL数据库管理系统也提供了数据类型决定表存储数据的类型。MySQL数据库管理系统提供的数据类型有：

###1、数值类型

#### 1.1、整数类型

​	TINYINT, SMALLINT, MEDIUMINT, INT, BIGINT

​	 如果不设置无符号还是有符号，默认是有符号，如果要设置无符号，需要添加unsigned关键字

​	如果插入的数值超出了整型的返回，会报out of range 异常，并且插入临界值

​	如果不设置长度，会有默认的长度，长度不够会用0在左边填充，但是必须搭配ZEROFILL使用，使用ZEROFILL后自动为无符号。

#### 1.2、浮点数类型

​	FLOAT(M,D), DOUBLE(M,D)

​	说明：M代表整数部分加小数部位的总长度，D表示小数点后的长度，如果超出，插入临界值

​		  MD可以省略，会根据插入数值的精度来决定

#### 1.3、定点数类型

​	DEC(M,D)

​	M默认是10，D模式为0,相对精确度较高。

#### 1.4、位类型

​	BIT

###2、字符串类型

#### 2.1、CHAR系列

​	CHAR, VARCHAR

​	CHAR代(M)表固定长度的字符，VARCHAR(M)代表可变长度字符

​	CHAR()的性能比VARCHAR()高，CHAR()比VARCHAR()耗费空间

​	CHAR 默认长度为1

#### 2.2、TEXT系列

​	TINYTEXT, TEXT, MEDIUMTEXT, LONGTEXT

#### 2.3、BLOB系列

​	TINYBLOB, BLOB, MEDIUMBLOB, LONGBLOB   用于保存较大的二进制

#### 2.4、BINARY系列

​	BINARY, VARBINARY  用于保存较短的二进制

#### 2.5枚举类型

​	ENUM  只能选一个插入

```mysql
creaet table tab_enum)( c ENUM("a","b","c"))  # 插入数据的时候只能是列表中(不区分大小写)
```

#### 2.6、集合类型

​	SET  可以选多个插入

```mysql
create table tab_set(s1 SET("A","B","c","D"))

insert into tab_set values("a");
insert into tab_set values("a","b");
insert into tab_set values("a","b","c");
```

###3、时间和日期类型

| 日期     | 字节 | 最小值              | 最大值              |
| -------- | ---- | ------------------- | ------------------- |
| DATE     | 4    | 1000-01-01          | 9999-12-31          |
| DATETIME | 8    | 1000-01-01 00:00:00 | 9999-12-31 23:59:59 |
| IMESTAMP | 4    | 19700101080001      | 2038年的某时刻      |
| TIME     | 3    | -838:59:59          | 838:59:59           |
| YEAR     | 1    | 1901                | 2155                |

##三、数据定义语言（DDL）

### 1、库的管理

#### 1.1、库的创建

##### 1.1.1、语法

```mysql
create database 库名；# 如果库存在会报错
create database 【if not exists】 库名； # 创建之前会进行判断
```

##### 1.1.2、案例

```mysql
#创建books库
create database books
```

#### 1.2、更改库的字符集

```mysql
ALTER DATABASE books CHARACTER SET gbk;
```

#### 1.3、库的删除

##### 1.3.1、语法

```mysql
drop database 库名；
drop database if exists 库名；
```

### 2、表的管理

#### 2.1、表的创建

##### 2.1.1、语法

```mysql
create tables [if not exists] 表名(
	字段1 字段的类型 【长度，约束】,
    字段2 字段的类型 【长度，约束】,
    ...
    字段n 字段的类型 【长度，约束】
)
```

##### 2.2.1、案例

```mysql
# 创建表book
create table book(
	id int, #书的标号
    bname varchar(20), #图书名
    price double, # 价格
    authorID int， #作者编号
    publishDate datetime #出版日期   
)

# 创建author表
create table author(
	id int,
    au_name varchar(20),
    nation varchar(10)
)
```

#### 2.2、表的修改

##### 2.2.1、修改列名

###### 语法

```MYSQL
ALTER TABLE 表名 CHANGE COLUMN 旧列名 新列名 字段类型；
```

###### 案例

```mysql
alter table book change column publishdate pubDate datetime;
```

##### 2.2.1、修改列的类型或约束

###### 语法

```mysql
ALTER TABLE 表名 MODIFY COLUMN 列名 字段类型
```

###### 案例

```mysql
alter table book modify column pubDate TIMESTAMP;
```

##### 2.2.3、添加新列

###### 语法

```MYSQL
ALTER TABLE 表名 ADD COLUMN 列名 字段类型 【first|after 列名】
```

###### 案例

```mysql
ALTER TABLE book ADD COLUMN annual DOUBLE;
```

##### 2.2.4、删除列

###### 语法

```mysql
ALTER TABLE 表名 DROP COLUMN 列名
```

###### 案例

```mysql
ALTER TABLE book DROP COLUMN annual;
```

##### 2.2.5、修改表名

###### 语法

```mysql
ALTER TABLE 表名 RENAME TO 新表名
```

###### 案例

```mysql
ALTER TABLE author RENAME TO book_author;
```

#### 2.3、表的删除

##### 2.3.1、语法

```mysql
DROP TABLE [if exists] 表名；
```

##### 2.3.2、案例

```mysql
drop table book_author;
```

#### 2.4、表的复制

##### 2.4.1、仅复制表的结构

###### 语法

```mysql
CREATE TABLE 新表名 LIKE 旧表名
```

###### 案例

```mysql
create table author_1 like author;
```

##### 2.4.2、复制表的结构外加数据

###### 语法

```mysql
CREATE TABLE 新表名 SELECT * FROM 旧表 【WHERE 筛选条件】； 
```

###### 案例

```MYSQL
# 全部数据
create table author_2 select * from author;

# 添加条件
create table author_3 select id,au_name from author where nation="中国";
```

##### 2.4.3、复制表的部分结构

###### 语法

```mysql
CREATE TABLE 新表名 SELECT 字段1，字段2....FROM 旧表 WHERE 0;
```

###### 案例

```mysql
# 只复制author表中的id,和au_name字段
create table author_4 select id, au_name from author where 0; 
```

### 3、常见约束

含义：一种限制，用于限制表中的数据，为了保证表中数据的准确性。

* not null 非空约束

  用于保证该字段的值不能为空。比如姓名，学号等。

* default 默认

  用于保证该字段有默认值。

* PRIMARY KEY 主键

  用于保证该字段中的值具有唯一性，且非空。(学号，员工编号等)

* UNIQUE 唯一

  用于保证该字段的值具有唯一性，可以为空。

* FOREIGN KEY 外键

  用于限制两个表的关系，用于保证该字段的值必须来自主表的关联列的值。

  在从表添加外键约束，用于引用主表中的某列值

#### 3.1、行级约束

注意：not null, default,primary key,unique支持表级约束

```mysql
create table stuinfo(
	id int primary key,   #主键
    stuName varchar(20) not null, # 不能为空
    gender char(1) default "男"，  # 默认值
    seat int unique,   # 唯一，可以为空
    age int default 18   
);
```

#### 3.2、表级约束

注意：primary key, unique, foreign key 支持表级约束

```mysql
create table major(
	id int PRIMARY KEY,
	majorName varchar(20)
)

create table stuinfo(
	id int,  
    stuName varchar(20),
    gender char(1), 
    seat int,
    age int,
    majorid int,
    
   constraint pk primary key(id),  #为id添加主键（完整格式）
   unique(seat),   #给seat添加唯一 （constraint + 名字，可以省略）
   foreign key(majorid) references major(id)  #外键 （constraint + 名字，可以省略）
);
```

#### 3.3、通用写法

```mysql
create table if not exists stuinfo(
		id int PRIMARY KEY,
		stuName varchar(20) not null,
		sex char(1),
		age int default 18,
		seat int UNIQUE,
		majorid int,
		
		CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)
)
```

#### 3.4、主键和唯一对比

|      | 保证唯一性 | 是否允许为空               | 一个表中可以有多少个 | 是否允许组合 |
| ---- | ---------- | -------------------------- | -------------------- | ------------ |
| 主键 | 保证唯一   | 不允许                     | 一个                 | 允许，不推荐 |
| 唯一 | 保证唯一   | 允许为null(但是只能有一个) | 多个                 | 允许，不推荐 |

```mysql
create table if not exists stuinfo(
		id int,
		stuName varchar(20) not null,
		sex char(1),
		age int default 18,
		seat int UNIQUE,
		majorid int,
		PRIMARY KEY(id,stuName),
		CONSTRAINT fk_stuinfo_major FOREIGN KEY(majorid) REFERENCES major(id)
)

# 在联合主键中，只要有其中一个字段不重复，就可以被插入，如上面的（id，stuName）字段组成了联合主键，那么我们在插入数据的时候，只需要id或者stuName不重复即可。

insert into stuinfo(id,stuName) values(1,"Negan"),(2,"Negan"),(3,"Negan");
```

#### 3.5、外键

* 外键要求在从表设置外键关系（维护关系的一方设置外键）
* 从表的外键列类型和主表的关联列的类型必须一致或兼容，名称无要求
* 主表关联列必须是一个key（一般是主键或唯一键）
* 插入数据时，先插入主表，在插入从表，删除数据时，先删除从表，在删除主表

### 4、修改表时添加/删除约束

```MYSQL
create table stuinfo(
	id int,  
    stuName varchar(20),
    gender char(1), 
    seat int,
    age int,
    majorid int
);

create table major(
	id int PRIMARY KEY,
	majorName varchar(20)
)
```

####4.1、添加列级约束

##### 4.1.1语法

```mysql
alter table 表名 modify column 字段名  字段类型 新约束；
```

##### 4.1.2、案例

```mysql
# 添加非空给stuName
alter table stuinfo modify COLUMN stuName varchar(20) not null;
# 添加默认值
alter table stuinfo modify COLUMN age int default 18;
# 添加主键
alter table stuinfo modify COLUMN id int PRIMARY KEY;
# 给seat设置唯一
alter table stuinfo modify column seat int unique;
```

####4.2、添加表级约束

#####4.2.1、语法

```mysql
alter table 表名 add [constraint 约束名] 约束类型(字段名) [外键的引用]
```

#####4.2.2、案例

```mysql
# 添加主键
alter table stuinfo add primary key(id);
# 设置唯一
alter table stuinfo add unique(seat);
# 添加外键
alter table stuinfo add constraint fk_stuinfo_major foreign key(majorid) REFERENCES major(id);
```

####4.3、删除约束

注意：删除唯一键和外键的时候，需要加上名字，可以通过`show index from stuinfo`查看约束信息。

```MYSQL
# 删除非空约束
alter table stuinfo modify column stuname varchar(20);

# 删除默认约束
alter table stuinfo modify column age int;

# 删除主键
alter table stuinfo drop primary key;

# 删除唯一键(需要加上名字)
alter table stuinfo drop index seat;

# 删除外键（需要加上名字）
alter table stuinfo drop foreign key fk_stuinfo_major;
```

### 5、 AUTO_INCREMENT

自增长列，不用手动插入值，系统提供默认的序列值。

只能搭配一个键来使用，一个表中只能有一个,一般搭配主键使用。

可以使用`show VARIABLES like "%autO_increment%";`查看默认值

可以使用`set auto_increment_increment= 3; `来修改步长，

只支持数值型。

```mysql
CREATE TABLE TEST (
	ID DOUBLE PRIMARY KEY AUTO_INCREMENT,
	CNAME VARCHAR(30)
) 

# 修改表时删除标示列
alter table test modify column id int ;
# 修改表示增加标示列
alter table test modify column id int auto_increment;

```

## 四、查询(DQL)

>  一般查询执行顺序：连表 > where > group by > having > select > order by > limit

###1、基础查询

####1.1、语法

```MYSQL
# 语法：
select 查询列表 from 表名；

# 查询列表可以是：表中的字段，常量值，表达式，函数
# 查询的结果是一个虚拟的表格
```

####1.2、查询

```mysql
# 1、查询表中的单个字段
SELECT last_name FROM employees;

# 2、查询表中多个字段
SELECT last_name,salary,email FROM employees;

# 3、查询表中的所有字段
SELECT * FROM employees;

# 4、查询常量值
SELECT 100;
SELECT 'john';

# 5、查询表达式
SELECT 100 % 98;

# 6、查询函数
SELECT VERSION();

# 7、起别名
/*
① 便于理解
② 如果要查询的字段有重名的情况，使用别用可以区分开来
*/
# 方式一：使用AS
SELECT 100 % 98 AS 结果;
SELECT last_name AS 姓, first_name AS 名 FROM employees;
# 方式二：使用空格
SELECT last_name 姓, first_name 名 FROM employees;
# 案例
# 由于out是sql中的关键字，所以下面这条语句会报错，我们在查询时候，应该加上“”
# SELECT salary AS out put FROM employees;
SELECT salary AS "out put" FROM employees;

# 8、去重(DISTINCT)
# 案例：查询员工表中涉及到所有部门编号
SELECT DISTINCT department_id FROM employees;

# 9、+号的作用
# 仅仅一个功能：运算符
SELECT 100 + 90;
SELECT '123' + 90;  
# 其中一方为字符型，试图将字符型数值转换成数值型，如果转换成功，则继续做加法运算，如果转换失败，则将字符型数值转换为0
SELECT last_name+first_name AS 姓名 FROM employees;   # 结果是一堆0
# 只要有一方为NULL，则结果为NULL
SELECT NULL + 100;  

# 10、CONCAT 字符串拼接，遇到NULL，则全为NULL
# 案例：查询员工名和姓连接成一个字段，并显示为姓名
SELECT CONCAT(last_name,",",first_name) AS 姓名 FROM employees;
# 案例：显示表employees的全部列，各个列之间用逗号连接，列头显示out_put
SELECT CONCAT(first_name,",",last_name,",",job_id,",",IFNULL(commission_pct,0)) AS out_put FROM employees;
```

### 2、条件查询

#### 2.1、语法

```mysql
select 查询列表 from 表名 where 筛选条件； 
```

#### 2.2、按条件表达式筛选

```mysql
# 条件运算符
>, <, =, !=, <>, >=, <=

# 查询工资>12000的员工信息
SELECT * FROM employees WHERE salary > 12000;

# 查询部门编号不等于90号的员工名和部门编号
SELECT last_name,department_id FROM employees WHERE department_id <> 90;
```

####2.3、按逻辑表达式筛选

```MYSQL
&&, ||, !
and,or,not
# 作用：用于连接条件表达式

# 查询工资在10000到20000之间的员工名，工资以及奖金
SELECT last_name,salary,commission_pct FROM employees WHERE salary>=10000 AND salary<=20000;
SELECT last_name,salary,commission_pct FROM employees WHERE salary BETWEEN 10000 AND 20000;

# 查询部门编号不在90到110之间，或者工资高于15000的员工信息
SELECT * FROM employees WHERE department_id < 90 OR department_id > 110 OR salary > 15000;
SELECT * FROM employees WHERE NOT (department_id > 90 AND department_id < 110) OR salary > 15000;
```

####2.4、模糊查询

```mysql
like, between and, in, is null,is not null

like说明
不区分大小写
% 任意多个字符，包含0个字符
_ 任意单个字符

# 查询员工名中包含字符a的员工信息
SELECT * FROM employees WHERE last_name LIKE '%a%';
# 查询员工名中第三个字符为e,第五个字符为a的员工名和工资
SELECT last_name,salary FROM employees WHERE last_name LIKE  "__n_l%";
# 查询员工名中第二个字符为_的员工
SELECT last_name,salary FROM employees WHERE last_name LIKE  "_\_%";  # 使用\转义
SELECT last_name,salary FROM employees WHERE last_name LIKE  "_#_%" ESCAPE "#"; # 使用ESCAPE

between and
注意事项:可以提高语句的简洁，包含临界值，两个临界值不能颠倒顺序
# 查询员工编号在100到120之间的员工信息
SELECT * FROM employees WHERE employee_id BETWEEN 100 AND 120;
SELECT * FROM employees WHERE employee_id >= 100 AND employees_id <= 120;

in (判断某字段的值是否属于in列表中的某一项，不支持通配符)
# 查询员工的工种编号是 IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号
SELECT last_name,job_id FROM employees WHERE job_id IN ("IT_PORT","AD_VP","AD_PRES");

is null
# 查询没有奖金的员工名和奖金率
SELECT last_name,commission_pct FROM employees WHERE commission_pct IS NULL;

<=> 安全等于，可以和null搭配，也可以判断普通类型的值
SELECT last_name,commission_pct FROM employees WHERE commission_pct <=> NULL;

is null 仅仅可以判断null,<=>既可以判断null，也可以判断普通的数据，但是可读性差*排序查询*
```

###3、排序查询

####3.1、语法

```shell
select 查询列表 from 表 [where 筛选条件] order by 排序列表 [asc|desc]

# asc代表升序，默认
# desc代表降序
# order by字句中可以支持单个字段，多个字段，表达式，函数，别名
# order by字句一般放在查询语句的最后面（limit除外）
```

####3.2、查询

```shell
# 查询员工信息，要求工资从高到低排序
SELECT * FROM employees ORDER BY salary DESC;

# 查询部门编号>=90的员工信息，按入职时间的先后进行排序
SELECT * FROM employees WHERE department_id >= 90 ORDER BY hiredate;

# 按年薪的高低显示员工的信息和年薪(按别名排序)
SELECT *, salary*12*(1+ IFNULL(commission_pct,0)) AS 年薪 FROM employees ORDER BY salary*12*(1+ IFNULL(commission_pct,0));
# order by后支持别名
SELECT *, salary*12*(1+ IFNULL(commission_pct,0)) AS 年薪 FROM employees ORDER BY 年薪;

# 按姓名的长度显示员工的姓名和工资（按函数排序）
SELECT LENGTH(last_name) 姓名长度, last_name, salary FROM employees ORDER BY 姓名长度;

# 查询员工信息，要求先按工资排序，再按员工编号排序【按多个字段排序】
# 查询的结果是先按照工资升序进行排序，工资相同的，按照员工编号进行降序排列
SELECT * FROM employees ORDER BY salary, employee_id DESC; 

# 练习
# 查询员工的姓名，部门编号，年薪，按年薪降序，姓名升序
SELECT last_name, department_id, salary * 12 * (1 + IFNULL(commission_pct,0)) AS 年薪 FROM employees ORDER BY 年薪 DESC, last_name;

# 查询工资不在8000到17000的员工姓名和工资，按工资降序
SELECT lat_name, salary FROM employees WHERE salary < 8000 OR salary > 17000 ORDER BY salary;
SELECT last_name, salary FROM employees WHERE salary NOT BETWEEN 8000 AND 17000 ORDER BY salary;

# 查询邮箱中包含e的员工信息，并先按邮箱的字节数降序，再按部门号升序
SELECT *,LENGTH(email) FROM employees WHERE email like "%e%" ORDER BY LENGTH(email) DESC,department_id;
```

###4、函数

####4.1、字符函数

#####4,1,1、 length   获取参数值的字节长度

```MYSQL
SELECT LENGTH("john")  # 4
SELECT LENGTH("张三分") # 9 utf8中一个中文字符占三个字节
```

#####4.1.2、concat  拼接字符串

```mysql
SELECT CONCAT(last_name,"_",first_name) 姓名 FROM employees;
```

#####4.1.3、upper和 lower

```mysql
SELECT UPPER("john")  # JOHN
SELECT LOWER("JOHN")  # john

# 案例：姓变大写，名变小写，进行拼接
SELECT CONCAT(UPPER(last_name),"_",LOWER(first_name)) FROM employees;
```

##### 4.1.4、substr / substring  截取字符，索引从1开始

> 说明：可以接收两个参数，也可以接收三个参数
>
> 第一个参数固定是字符串，第二个参数是索引，如果是两个参数，则从索引的位置截取并返回后面的字符串
>
> 如果是三个参数，最后一个参数是表示要截取几个字符

```mysql
# 截取从指定索引处后面所有字符
SELECT SUBSTR("李莫愁爱上了陆展元", 7)  # 陆展元
# 截取从指定索引处指定字符长度的字符
SELECT SUBSTR("李莫愁爱上了陆展元",1,3) # 李莫愁

# 案例：姓名中首字符大写，其他字符小写，然后用_拼接
SELECT CONCAT(UPPER(SUBSTR(last_name,1,1)),"_",LOWER(SUBSTR(last_name,2)) FROM employees;
```

#####4.1.5、instr   返回子串第一次出现的索引，如果找不到返回0

```mysql
SELECT INSTR("李莫愁爱上了陆展元","陆展元") # 7
```

#####4.1.6、trim   

```MYSQL
TRIM([remstr FROM] str)  # 去除字符串前后指定字符

SELECT LENGTH(TRIM("      李莫愁        "))  # 9 
SELECT TRIM('a' FROM 'aaaaaa李莫愁aaaaaa')   # 李莫愁
```

#####4.1.7、lpad  和 rpad 

```mysql
# 用指定的字符实现左/右填充，指定长度
# LPAD(str,len,padstr)
SELECT LPAD("李莫愁",5,"*")   # **李莫愁

# RPAD(str,len,padstr)
SELECT RPAD("李莫愁",5,"*")
```

#####4.1.8、replace  替换

```MYSQL
SELECT REPLACE("张无忌爱上了周芷若","周芷若","赵敏")  # 张无忌爱上了赵敏
```

####4.2、数学函数

#####4.2.1、round 四舍五入

```mysql
SELECT ROUND(1.55)  # 2
SELECT ROUND(1.567,2)  #1.67  # 小数点后面取两位
```

#####4.2.2、ceil   

```mysql
# 向上取整（返回>=该参数的最小整数）
SELECT (CEIL(1.52))   #2
```

#####4.2.3、floor 

```MYSQL
# 向下取整 （返回<=该参数的最大整数）
SELECT (floor(-9.9))   # -10
```

#####4.2.4、truncate 截断

```mysql
# TRUNCATE(X,D)   # 小数点后面保留几位
SELECT(TRUNCATE(1.999,2)) # 1.99
```

#####4.2.5、mod   取模

```mysql
select mod(10,3)
select 10 % 3
```

#####4.2.6、rand 

```mysql
# 获取随机数，返回0-1之前的小数
select(truncate(rand(),2))
```

####4.3、日期函数

#####4.3.1、now 

```mysql
#  返回当前系统日期+时间
SELECT  NOW()   # 2020-12-13 12:32:33
```

#####4.3.2、curdate 

```MYSQL
# 返回当前系统日期，不包含时间
SELECT  CURDATE()  # 2020-12-13
```

##### 4.3.3、curtime 

```mysql
# 返回当前时间，不包含日期
SELECT CURTIME()   # 12:34:09
```

#####4.3.4 可以获取指定的部分：年、月、日、时、分、秒

```mysql
SELECT DATA;   # 获取年份
SELECT YEAR(NOW());  #获取年份
SELECT YEAR("1990-1-1");
SELECT YEAR(hiredate) FROM employees;

SELECT MONTH(NOW());  # 获取月
SELECT MONTHNAME(NOW()); # 英文显示

SELECT DAY(NOW());  # 获取日期
SELECT HOUR(NOW());  # 获取小时
SELECT MINUTE(NOW());  # 获取分钟
SELECT SECOND(NOW()); # 获取秒
```

#####4.3.5、str_to_date 和 date_format   

| 格式符 |           功能            |
| :----: | :-----------------------: |
|   %Y   |        四位的年份         |
|   %y   |         2位的年份         |
|   %m   | 月份（01,02,03....11,12） |
|   %c   |  月份（1,2,3.....11,12）  |
|   %d   |        日（01,02）        |
|   %H   |     小时（24小时制）      |
|   %h   |      小时（12小时）       |
|   %i   |   分钟（00,01......59）   |
|   %s   |    秒（00,01......59）    |

* **str_to_date**

```MYSQL
# str_to_date # 将字符通过制定的格式转换成日期

SELECT STR_TO_DATE("1990-1-1","%Y-%c-%d")  # 1990-01-01

# 查询入职日期为1992-4-3的员工信息
SELECT * FROM employee WHERE hiredate = "1992-4-3"

SELECT * FROM employee WHERE hiredate = STR_TO_DATE("4-3 1992","%c-%d %Y")
```

* **date_format**

```mysql
# data_format   # 将日期转换为字符
SELECT DATE_FORMAT(NOW(),"%Y年%m月%d日");  #2020年12月13日

# 查询有奖金的员工名和入职日期（xx月/xx日 xx年）
SELECT last_name,DATE_FORMAT(hiredate,"%m月/%d日 %y年") FROM employees WHERE commission_pct IS NOT NULL;
```

#####4.3.6、datediff 

```MYSQL
# 返回两个日期之间相差天数
SELECT DATEDIFF(MAX(hiredate),MIN(hiredate)) FROM employees;
```

####4.4、其他函数

```mysql
SELECT VERSION(); 查看版本号
SELECT DATABASE(); 当前数据库
SELECT USER(); 当前用户
SELECT(password("字符"))  # 返回字符的密码形式
SELECT(MD5("字符"))  # 返回字符MD5加密形式
```

####4.5、流程控制函数

#####4.5.1、if

```mysql
IF(表达式，表达式1，表达式2) # 如果表达式成立，返回表达式1，否则返回表达式2

SELECT IF(10<5,"大","小")  # 小

SELECT last_name,commission_pct, IF(commission_pct,"有","无") FROM employees
```

##### 4.5.2、case

* 方式一：实现精确匹配

```mysql
# 使用一：switch .... case....结构  实现精确查找
case 要判断的变量或者表达式
when 常量1 then 要显示的值1或语句1；
when 常量2 then 要显示的值2或语句2；
...
else 要显示的值n或语句n;
end

/*查询员工的工资
要求：
部门号=30，显示工资为1.1倍
部门号=40，显示工资为1.2倍
部门号=50，显示工资为1.3倍
其他部门，显示工资为原工资*/

SELECT salary 原始工资, department_id,
CASE department_id
	WHEN 30 THEN
	salary * 1.1
	WHEN 40 THEN
	salary * 1.2
	WHEN 50 THEN
	salary * 1.3
	ELSE
		salary
END  AS 新工资
FROM employees;
```

* 方式二：实现区间判断

```mysql
# 使用二：if... elif ... 实现区间判断
case 
when 条件1 then 要显示的值1或语句1  # 如要后面是语句，后面加";"
when 条件2 then 要显示的值2或语句2
......
else 要显示的值n或语句n
end

/* 查询员工的工资
如果工资>20000,显示A
如果工资>15000,显示B
如果工资>10000,显示C
否则显示D*/

SELECT
	salary,
CASE	
		WHEN salary > 20000 THEN
		"A" 
		WHEN salary > 15000 THEN
		"B" 
		WHEN salary > 10000 THEN
		"C" ELSE "D" 
	END 工资级别 
FROM
	employees;
```

#### 4.6、聚合函数

##### 4.6.1、参数是数值型

* sum()  求和     
* avg()    平均值   

```mysql
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
```

#####4.6.2、参数是任意类型

* min()   最小值   
* max()  最大值   
* count()  计数   

***count()详细介绍***

```mysql
SELECT COUNT(*) FROM employees;

SELECT COUNT(1) FROM employees;  

# MYISAM存储引擎下，COUNT(*)的效率高
# INNODB存储引擎下，COUNT(*)和COUNT(1)的效率比COUNT(字段)的效率高。
```

>  说明：以上五个函数都可以和distinct搭配实现去重，并且都可以忽略NULL

###5、分组查询 GROUP BY（搭配聚合函数）

引入：查询每个部门的平均工资？

####5.1、语法

```MYSQL
SELECT 分组函数，列(要求出现在group by的后面)
FROM 表 [WHERE 筛选条件] GROUP BY 分组的列 [ORDER BY 字句]

# 注意；查询列表特殊，必须是聚合函数和group by后出现的字段
```

####5.2、查询

#####5.2.1、简单查询

```mysql
# 查询每个部门的平均工资
SELECT job_id,AVG(salary) FROM employees GROUP BY job_id; 

# 查询每个部门的最高工资
select job_id,MAX(salary) FROM employees GROUP BY job_id;

# 查询每个位置上的部门个数
SELECT COUNT(*), location_id FROM departments GROUP BY location_id;
```

##### 5.2.2、加上筛选条件(添加分组前的筛选)

```mysql
# 查询邮箱中包含a字符的，每个部门的平均工资
SELECT AVG(salary), department_id FROM employees WHERE emil like "%a%" GROUP BY department_id;

# 查询有奖金的每个领导手下员工的最高工资
SELECT MAX(salary), manager_id FROM employees WHERE commission_pct is NOT NULL GROUP BY manager_id;
```

##### 5.2.3、添加分组后的筛选

```mysql
# 查询哪个部门员工个数大于2
SELECT COUNT(*) as b, department_id FROM employees GROUP BY department_id HAVING COUNT(*) > 2;

# 查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
SELECT MAX(salary),job_id FROM employees WHERE commission_pct IS NOT NULL GROUP BY job_id HAVING max(salary) > 12000;

# 查询领导编号>102的每个领导手下的最低工资>5000的领导编号是哪个，以及其最低工资
select min(salary), manager_id from employees where manager_id > 102 GROUP BY manager_id HAVING min(salary) > 5000;
```

#####5.2.4、按表达式或函数分组

```mysql
# 按员工姓名的长度分组，查询每一组员工的个数，筛选员工个数>5的有哪些？
select count(*),LENGTH(last_name) from employees group by LENGTH(last_name) having count(*) > 5
```

#####5.2.5、按多个字段分组

```mysql
# 查询每个部门每个工种的员工的平均工资
SELECT AVG(salary), department_id, job_id from employees GROUP BY department_id,job_id;
```

#####5.2.6、添加排序

```mysql
# 查询每个部门每个工种的员工的平均工资，并且按平均工资的高低显示高于一万的
select avg(salary), department_id, job_id from employees where department_id is not null GROUP BY job_id,department_id having avg(salary) > 10000 order by avg(salary) desc;
```

> 总结：
>
> 1、分组查询中的筛选条件分为两类
>
> |            | 数据源         | 位置                 | 关键字 |
> | ---------- | -------------- | -------------------- | ------ |
> | 分组前筛选 | 原始表         | group by 子句的前面  | where  |
> | 分组后筛选 | 分组后的结果集 | group by  子句的后面 | having |
>
>  分组函数做条件肯定是放在having字句中，能用分组前筛选的尽量使用分组前筛选。
>
> 2、group by子句支持单个字段分组，多个字段分组，多个字段之间没有顺序要求，使用逗号间隔，表达式和函数也支持。
>
> 3、也可以添加排序，放在整个分组查询后面。

###6、连接查询（多表查询）

####6.1、SQL92内连接

```MYSQL
# 语法：
select 查询字段 FROM 表1，表2 .... WHERE 连接条件 AND ...
```

#####6.1.1、等值连接

###### ***普通查询***

```mysql
# 查询员工名和对应的部门名
SELECT last_name, department_name FROM employees,departments WHERE employees.department_id = departments.department_id;

# 查询员工名、工种号、工种名
SELECT last_name, employees.job_id,job_title FROM employees,jobs WHERE employees.job_id=jobs.job_id；
```

###### ***加筛选条件查询***

注意：先建立连接关系，后加条件限制

```mysql
# 查询由江门的员工名、部门名、以及奖金
SELECT last_name, job_title,commission_pct FROM employees AS e,jobs WHERE e.job_id = jobs.job_id AND commission_pct IS NOT NULL;

# 查询城市名中第二个字符为o的部门名和城市名
SELECT city, department_name FROM locations AS l,departments AS d WHERE l.location_id = d.location_id AND city like "_o%"; 
```

###### ***添加分组***

```mysql
# 查询每个城市的部门个数
SELECT COUNT(*), city FROM departments, locations GROUP BY city;

# 查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
SELECT department_name,d.manager_id,min(salary) FROM departments AS d, employees AS e WHERE d.department_id = e.department_id AND commission_pct IS NOT NULL GROUP BY department_name,d.manager_id;
```

###### ***添加排序***

```mysql
# 查询每个工种名和员工个数，并按员工个数降序
SELECT job_title, COUNT(*) FROM jobs AS j, employees AS  e WHERE j.job_id = e.job_id GROUP BY job_title ORDER BY COUNT(*) desc;
```

###### ***多表连接***

```mysql
#查询员工名、部门名和所在城市
SELECT last_name, department_name, city FROM employees AS e, departments AS d, locations AS l WHERE e.department_id = d.department_id AND d.location_id = l.location_id;
```

总结：多表等值连接的结果为多表的交集部分，n表连接，至少需要n-1个连接，多表连接顺序没有要求，一般需要为表起别名，可以搭配前面介绍的而所有字句使用（排序，分组，筛选等）

* 非等值连接

```mysql
# 查询员工的工资和工资级别，并按照升序显示
SELECT salary, grade_level FROM employees AS e, job_grades AS g WHERE salary BETWEEN g.lowest_sal AND g.highest_sal ORDER BY  salary asc;

# 查询员工的工资和工资级别，只显示“A”
SELECT salary, grade_level FROM employees AS e, job_grades AS g WHERE salary BETWEEN g.lowest_sal AND g.highest_sal  AND grade_level="A" ORDER BY  salary asc;
```

* 自连接

把一张表看成多张表进行连接查询

```mysql
# 查询员工名和上级领导名字，
#分析：我们不难发现，在员工表里，有员工的编号，和领导的编号，而且员工编号和领导编号有对应关系，通俗的说员工和领导在同一张表里。
SELECT e.employee_id,e.last_name, m.employee_id, m.last_name FROM employees AS e,employees AS m WHERE e.employee_id =m.manager_id;
```

####6.2、SQL99

```mysql
# 语法：
select 查询字段 FROM 表1 【连接类型】 join 表2 ON 连接条件 【WHERE 筛选条件】【GROUP BY 分组】【HAVING 筛选条件】【ORDER BY 排序字段】
```

##### 6.2.1、内连接  inner join

![1607926789693](C:\Users\Egon\Desktop\学习笔记\数据库\mysql\inner_join.png)

###### ***等值连接***

```mysql
# 查询员工名、部门名
SELECT last_name, department_name from employees AS e INNER JOIN departments AS d ON e.department_id = d.department_id;

# 查询名字中包含e的员工名和工种名（筛选）
SELECT last_name, job_title FROM employees AS e INNER JOIN jobs as j ON e.job_id = j.job_id WHERE last_name LIKE "%e%" AND job_title LIKE "%e%";

# 查询部门个数大于3的城市名和部门个数
SELECT city,count(*) FROM departments AS d INNER JOIN locations AS l ON d.location_id = l.location_id GROUP BY city HAVING count(*) > 3 ;

# 查询部门员工人数大于3的部门和员工个数，并降序显示。
SELECT d.department_name, count(*) from departments as d INNER JOIN employees AS e ON d.department_id = e.department_id GROUP BY department_name HAVING count(*) > 3 ORDER BY count(*) DESC; 

# 查询员工名、部门名、工种名，并按部门名降序
SELECT last_name, department_name, job_title from employees as e INNER JOIN departments as d on e.department_id=d.department_id INNER JOIN jobs as j on e.job_id = j.job_id ORDER BY job_title DESC;
```

> 特点：
>
> ①添加排序、分组、筛选
>
> ②inner可以省略
>
> ③筛选条件放在where后面，连接条件放在on后面，提高分离性，便于阅读
>
> ④inner join连接和sql92语法中的等值连接效果是一样的，都是查询多表的交集。

###### ***非等值连接***

```mysql
# 查询员工的工资级别
SELECT last_name, salary,grade_level from employees as e join job_grades as g ON e.salary BETWEEN g.lowest_sal AND g.highest_sal;

# 查询工资级别的个数大于4，并按工资级别降序
SELECT grade_level,count(*) from employees as e join job_grades as g ON e.salary BETWEEN g.lowest_sal AND g.highest_sal GROUP BY grade_level HAVING COUNT(*)>4 ORDER BY grade_level desc; 
```

###### ***自连接***	

```mysql
# 查询员工的名字、上级的名字
SELECT e.employee_id,e.last_name, m.employee_id,m.last_name from employees as e join employees as m on e.manager_id = m.employee_id
```

##### 6.2.2、外连接

应用场景：用于查询一个表中有，另一个表中没有的记录

特点：

* 特点一：	
  * 外连接查询结果为主表中的所有记录
  * 如果从表中有和它匹配的，则显示匹配的值
  * 如果表中没有和它匹配的，则显示null
  * 外连接查询结果等于内连接结果加上主表中有而从表中没有的记录
* 特点二：
  * 左外连接，left join左边的是主表
  * 右外连接，right join右边的是主表
* 左外和右外交换两个表的顺序，可以实现同样的效果

######左外连接

![left_join](C:\Users\Egon\Desktop\学习笔记\数据库\mysql\left_join.png)

![left_join_1](C:\Users\Egon\Desktop\学习笔记\数据库\mysql\left_join_1.png)

###### 右外连接

![right_join](C:\Users\Egon\Desktop\学习笔记\数据库\mysql\right_join.png)

![right_join_1](C:\Users\Egon\Desktop\学习笔记\数据库\mysql\right_join_1.png)

```mysql
# 左外连接
# 查询男朋友，不在男神表的女神名
SELECT * from beauty LEFT JOIN boys ON beauty.boyfriend_id = boys.id where boys.id is NULL;

# 右外连接
select beauty.`name` from boys RIGHT JOIN beauty on boys.id = beauty.boyfriend_id where boys.id is null;

# 查询哪个部门没有员工
SELECT department_name from departments as d LEFT JOIN employees as e on d.department_id = e.department_id WHERE employee_id is null;

SELECT department_name from employees as e right JOIN departments as d on e.employee_id = d.department_id where e.employee_id is null;
```

######全连接

![full_join](C:\Users\Egon\Desktop\学习笔记\数据库\mysql\full_join.png)



![full_join_1](C:\Users\Egon\Desktop\学习笔记\数据库\mysql\full_join_1.png)



**练习**

```mysql
# 查询编号>3的女神男朋友信息，如果有则列出详细信息，如果没有，用null填充
select name,boyName from beauty LEFT JOIN boys on beauty.boyfriend_id = boys.id WHERE beauty.id > 3;

# 查询哪个城市没有部门。
select city from locations as l left join departments  as d on l.location_id = d.location_id where d.location_id is null;

# 查询部门名为SAL或IT的员工信息
SELECT * from employees as e INNER JOIN departments as d on e.department_id=d.department_id WHERE department_name IN ("SAL","IT");
```

### 7、子查询

出现在其他语句中的select语句，称为子查询或内查询

#### 7.1、结果集的行列数不同

#####7.1.1、标量子查询（结果集只有一行一列）

##### 7.1.2、列子查询（结果集只有一列多行）

#####7.1.3、行子查询（结果集有一行多列）

##### 7.1.4、表子查询（结果集一般为多行多列）

#### 7.2、按子查询出现的位置

##### 7.2.1、select后面

​	仅仅支持标量子查询

```mysql
# 案例一：查询每个部门的员工个数
select d.*,(select count(*) from employees e where e.department_id = d.department_id) 个数 from departments d;

# 案例二：查询员工好为102的部门名
select (select department_name from departments d INNER JOIN employees e on e.department_id = d.department_id where e.employee_id=102) 部门名;
```

##### 7.2.2、from后面

​	仅仅支持表子查询

​	将子查询结果充当一张表，要求必须起别名

```mysql
# 案例一：查询每个部门的平均工资的工资等级
# 步骤1:查询出每个部门的id和平均工资，最为一个虚拟表
select avg(salary) ag,department_id from employees GROUP BY department_id
# 步骤2：工资级别表和步骤一的虚拟表进行连接查询
select ag_dep.*,g.grade_level from (select avg(salary) ag,department_id from employees GROUP BY department_id) ag_dep INNER JOIN job_grades g on ag_dep.ag BETWEEN lowest_sal and highest_sal;
```

##### 7.2.3、where或having后面

 * 标量子查询（单行）
 * 列子查询（多行）
 * 行子查询（一行多列，多行多列）

> 特点：①子查询放在小括号内
>
> ​	   ②标量子查询，一般搭配单行操作符使用（>,<,>=,<=,=,<>）
>
> ​	   ③列子查询，一般搭配着多行操作符使用（IN,ANY/SOME,ALL）
>
> ​      	   ④子查询的执行优先于主查询

###### 标量子查询（一列一行）

```mysql
# 案例1：谁的工资比Abel高？

# 步骤①：查询Abel的工资
select salary from employees where last_name = "Abel";

# 步骤②：查询员工的信息，满足salary>①的结果
select * from employees WHERE salary > (select salary from employees where last_name = "Abel");

# 案例2：返回job_id与141号员工相同，salary比143号员工多的员工信息，job_id和工资

# 步骤①：查询141员工的job_id
select job_id from employees where employee_id=141
# 步骤②：查询143号员工的工资
select salary from employees where employee_id = 143
# 将步骤①和步骤②当做条件进行查询
select last_name, job_id,salary from employees WHERE job_id= (select job_id from employees where employee_id=141) and salary > (select salary from employees where employee_id = 143);

# 案例三：返回公司工资最少的员工last_name,job_id和salary
# 步骤①：查找最低工资
select min(salary) from employees; 
# 步骤②：将步骤①当作条件
select last_name,job_id, salary from employees where salary = (select min(salary) from employees); 

# 查询最低工资大于50号部门最低工资的部门id和其最低工资
# 步骤①：查询50号部门的最低工资
select min(salary) from employees where department_id = 50
# 步骤②：求出各部门的最低工资（按照部门分组）
select department_id,min(salary) from employees GROUP BY department_id
# 步骤③：给步骤②求出的最低工资添加筛选条件
select department_id,min(salary) from employees GROUP BY department_id HAVING min(salary) > (select min(salary) from employees where department_id = 50);
```

###### 列子查询(一列多行)

```mysql
# 案例一：返回location_id是1400或1700的所有员工姓名
# 步骤1：先查出location_id是1400或1700所有的部门
select department_id from departments WHERE location_id = 1400 or location_id = 1700
# 步骤2：查询出这些部门中所有的人
select last_name from employees where department_id in (select distinct department_id from departments WHERE location_id = 1400 or location_id = 1700);

# 使用内联接方式
select last_name from employees inner join departments on employees.department_id = departments.department_id where location_id in (1400,1700);

# 案例二：返回其他工种中比job_id为IT_PROG工种任意一个员工工资低的员工信息。
# 步骤1： 查询job_id = "IT_PROG"工种的最高工资
select max(salary) from employees where job_id = "IT_PROG"
# 步骤2：把步骤1当做条件，同时保证job_id 不是“IT_PROG”
select last_name,employee_id,job_id, salary from employees where salary < (select max(salary) from employees WHERE job_id = "IT_PROG") and job_id != "IT_PROG"

# 案例三：返回其他工种中比job_id为IT_PROG工种中所有工资都低的员工信息。
select last_name,employee_id,job_id, salary from employees where salary < (select min(salary) from employees WHERE job_id = "IT_PROG") and job_id != "IT_PROG"
```

###### 行子查询（结果集一行多列或多行多列）

```mysql
# 案例：查询员工编号最小并且工资最高的员工信息
select * from employees where employee_id = (select min(employee_id) from employees) and salary = (select max(salary) from employees);

# 行子查询
select * from employees where (employee_id, salary) = (select min(employee_id), MAX(salary) from employees);
```

##### 7.2.4、exists后面（相关子查询）

​	仅仅支持表子查询

```mysql
# 语法
exists(完整的查询语句) #返回结果1或0

# 案例1：查询员工名和部门名


```

#### 7.3、练习

```MYSQL
# 题1：查询和Zlotkey相同部门的员工姓名和工资
select last_name, salary,department_id from employees where department_id = (select department_id from employees where last_name="Zlotkey" )

# 题2：查询工资比公司平均工资高的员工的员工号，姓名和工资
select employee_id,last_name,salary from employees where salary > (select avg(salary) from employees);

# 题3：查询各部门中工资比本部门平均工资高的员工的员工号，姓名和工资
select employee_id,last_name,salary,e.department_id from employees e LEFT JOIN (select department_id,AVG(salary) ag from employees GROUP BY department_id) a on a.department_id=e.department_id where e.salary>a.ag;

# 题4：查询和姓名中包含字母u的员工在相同部门的员工号和姓名
select employee_id, last_name from employees where department_id in (select DISTINCT department_id from employees where last_name like "%u%");

# 题5：查询location_id=1700的部门工作的员工信息
select employee_id,last_name from employees where department_id in (SELECT department_id from departments where location_id = 1700)

# 题6：查询领导是King的员工姓名和工资
select last_name, salary from employees where manager_id in (select employee_id from employees where last_name = "K_ing");

# 题7：查询工资最高的员工的姓名，要求first_name 和 last_name 显示为一列，列名为姓名
select concat(first_name,last_name) 姓名 from employees where salary = (select max(salary) from employees);
```

### 8、分页查询

**limit [offset,]size** 

offset:要显示条目的起始索引（从0开始）

size:要显示的条目数

```mysql
# 案例1： 查询前五条员工信息
select * from employees limit 0,5;

# 案例2：查询第11条-25条
select * from employees limit 10,15;

# 案例3：有奖金的员工信息，并且工资比较高的前10名
select * from employees where commission_pct is not null ORDER BY salary desc limit 10;
```

> 特点：limit语句放在查询语句的最后（语法和执行顺序都是在最后）
>
> ​	   公式：要显示的页数page, 每页条目数size,         limit (page-1)*size,size

### 9、练习

```mysql
# 题1：查询工资最低的员工信息：last_name, salary
select last_name, salary from employees where salary = (select min(salary) from employees);

# 题2：查询平均工资最低的部门信息
select * from departments where department_id = (select department_id  from employees GROUP BY department_id order by avg(salary)  limit 1);

# 题3：查询平均工资最低的部门信息和该部门的平均工资
select c.ag,d.* from (select avg(salary) ag,department_id  from employees GROUP BY department_id order by avg(salary)  limit 1) c join departments  d on c.department_id = d.department_id;

# 题4：查询平均工资最高的job信息
select jobs.*,b.ag from jobs inner join (select job_id,avg(salary) ag from employees GROUP BY job_id ORDER BY avg(salary) desc limit 1) b on jobs.job_id = b.job_id

# 题5： 查询平均工资高于公司平均工资的部门有哪些？
select department_id,avg(salary) from employees GROUP BY department_id HAVING avg(salary) > (select avg(salary) from employees)

# 题6：查询出公司中所有manager的详细信息
select * from employees where employee_id in (select DISTINCT manager_id from employees);

# 题7：各个部门中最高工资中最低的那个部门的最低工资是多少
select min(salary) from employees where department_id = (select department_id from employees GROUP BY department_id ORDER BY max(salary) limit 1);

# 题8：查询平均工资最高的部门的manager的详细信息
select * from employees where employee_id in (select d.manager_id from (select * from employees where department_id = (select department_id from employees GROUP BY department_id order by avg(salary) desc limit 1)) d);
```

### 10、union联合查询

将多条查询语句的结果合并成一个结果

> 引入案例：查询部门编号>90或邮箱包含a的员工信息

```mysql
select * from employees where email like '%a%' or department_id>90;

# 使用union
select * from employess where email like '%a%'
union
select * from employess where department_id>90;
```

#### 10.1、语法

```mysql
查询语句1
union
查询语句2
union
...
```

#### 10.2、应用场景

要查询的结果来自多个表，且多个表没有直接的连接关系，但查询的信息一致

#### 10.3、特点

* 要求多条查询语句的查询列数是一致的
* 要求多条查询语句的查询列的类型和顺序最好一致
* union关键字默认去重，如果使用union all 可以包含重复项

## 五、数据操作语言（DML）

### 1、插入语句

#### 1.1、语法

```mysql
insert into 表名（字段1，字段2，...）values(值1，值2，...);
```

#### 1.2、方式一：经典插入

```mysql
# 插入的值的类型要与列的类型一致或兼容
INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id) VALUES(13,"唐艺昕","女","1990-4-23","18988888888",NULL,2);

# 为null的列如何插入值？
# 方式一：列名不省略，用null关键字代替
# 方式二：列名和值都省略
INSERT INTO beauty(id,NAME,sex,borndate,phone,boyfriend_id) VALUES(14,"金星","女","1990-4-23","18988888888",2);

# 插入的字段顺序可以调换,但是要一一对应
insert into beauty(NAME,sex,id,phone) values("蒋欣","女",16,"110")

# 列数和插入值的个数必须一致
# 可以省略列名，默认所有列，而且列的顺序和表中列的顺序是一致的
insert into beauty values(18,"张飞","男",null,"119",null,null)
```

#### 1.3、方式二

```mysql
# 语法
insert into 表名 set 字段1=值1,字段2=值2,....;

insert into beauty set id = 19, NAME="刘涛", phone = "999";
```

#### 1.4、两种插入方式区别

* *方式一支持插入多行，方式二不支持*

```mysql
insert into beauty values(23,"糖糖1","女","1990-4-23","18988888888",null,2),
					   (24,"糖糖1","女","1990-4-23","18988888888",null,2),
					   (25,"糖糖1","女","1990-4-23","18988888888",null,2);
```

* *方式一支持子查询，方式二不支持*

```mysql
# 先查询结果集然后插入beauty
insert into beauty(id,name,phone) select 26,"宋茜","18988888888";

insert into beauty(name,phone) select boyname,"110" from boys where id < 4;
```

### 2、修改语句

#### 2.1、修改单表记录

##### 2.1.1、语法

```mysql
update 表名 set 字段1=新值1，字段2=新值2,... where 筛选条件
```

##### 2.1.2、案例

```mysql
# 修改beauty表中姓唐的女神的电话为13899999999
update beauty set phone="13899999999" where NAME LIKE "唐%";

# 修改boys表中id为2，名称为张飞，魅力值10
update boys set boyname="张飞", usercp=10 where id=2;
```

####2.2、修改多表记录

#####2.2.1、语法

```mysql
# sql92:
update 表1 别名，表2 别名 
set 字段1 = 值1, 字段2=值2...
where 连接条件
and 筛选条件

# sql99
update 表1 别名  inner|left|right join 表2 别名
on 连接条件
set 字段1=值1,字段2=值2...
where 筛选条件
```

#####2.2.2、案例

```mysql
# 修改多表的记录
# 修改张无忌女朋友的手机号为111000
update boys bo INNER JOIN beauty b on bo.id = b.boyfriend_id set phone="111000" where bo.boyName="张无忌";

# 修改没有男朋友的女神，男朋友的编号都为2号
update boys bo right join beauty b on bo.id = b.boyfriend_id set boyfriend_id = 2 where b.boyfriend_id is null;
```

### 3、删除语句

#### 3.1、delete

##### 3.1.1、单表删除

###### 语法

```mysql
delete from 表名 【where 筛选条件】【limit 条目数】
```

###### 案例

```mysql
# 删除手机号码以9结尾的女神信息
delete from beauty where phone like "%9"
```

##### 3.1.2、多表删除

###### 语法

```mysql 
# sql92:
delete 表1的别名,表2的别名
from 表1 别名, 表2 别名
where 连接条件
and 筛选条件

# sql99
delete 表1的别名,表2的别名
from 表1 别名 inner|left|right join 表2 别名 on 连接条件
where 筛选条件;
```

###### 案例

```mysql
# 删除张无忌女朋友的信息
delete b from beauty b INNER JOIN boys on b.boyfriend_id = boys.id where boys.boyName="张无忌";

# 删除黄晓明的信息以及他女朋友的信息
delete bo,b from boys bo inner join beauty b on bo.id = b.boyfriend_id where bo.boyName = "黄晓明";
```

#### 3.2、truncate(清空数据) 

##### 3.2.1、语法

```mysql
truncate table 表名
```

#### 3.3、两者区别

* delete可以加where条件，truncate不能加
* truncate删除效率比delete高
* 使用delete删除，再插入数据，自增长列值从断点处开始，truncate删除后，在插入数据，自增长列从0开始
* truncate删除没有返回值，delete删除会有返回值
* truncate删除不能回滚，delete删除可以回滚

## 六、事务控制语言（TTL）

事务：一个或多个sql语句组成，在这个单元中，每个sql语句都是相互依赖的，整个单独的单元作为一个不可分割的整体，如果单元中某条sql语句一旦执行失败或产生错误，整个单元将会回滚，所有受到影响的数据将返回到事务最开始的状态，如果单元中的所有sql都执行成功，则事务呗顺利执行。

### 1、事务的ACID属性

原子性：事务是不可再分割的工作单位，事务中的操作要么都成功，要么都失败。

一致性：事务的执行必须使数据库保持一致性状态，在一致性状态下，所有事务对一个数据的读取结果是相同的 

隔离性：一个事务所做的修改在最终提交之前，对其它事务是不可见的。

永久性：一旦事务提交成功，对数据的修改是永久性的 ;

### 2、事务的创建

隐式事务：事务没有明显的开启和结束标志。如insert,update,delete语句

显示事务：事务具有明显的开启和结束标志。(必须先设置自动提交功能为禁用)

#### 2.1、显示事务

```mysql
# 步骤1：开启事务
set autocommit = 0;
start transaction; # 可选

# 步骤2：编写事务中的sql语句（select,insert,update,delete）
# 步骤3：结束事务
commit 提交事务
rollback 回滚事务
```

#### 2.2、会出现哪些并发一致性问题

丢失修改：一个事务对数据进行了修改，在提交之前，另一个事务对同一个数据进行了修改，覆盖了之前的修改。

脏读：一个事务读取了被另一个事务修改，但未提交的数据的数据，造成两个事务得到的数据不一致

不可重复读：在同一事务中，某查询操作在一个时间读取某一行数据和之后一个时间读取该行数据，发现数据已经被修改

幻读：当同一查询多次执行时，由于其它事务在这个数据范围内执行了插入操作，会导致每次返回不同的结果集

#### 2.3、数据库的四种隔离级别

读未提交：在一个事务提交之前，它的执行结果对其他事务也是可见的，会导致脏读，不可重复读，幻读

读已提交：一个事务只能看到已经提交的事务所作的改变，可避免脏读

可重复读：可以确保同一个事务在多次读取同样的数据时得到相同的结果。（默认级别）

可串行化：强制事务串行执行，使之不可能相互冲突，从而解决幻读问题，可能导致大量超时现象和锁竞争。

#### 2.4、SAVEPOINT使用

只能搭配rollback使用

```mysql
set autocommit=0;
start transaction;
delete from account where id = 23;
savepoint a; #设置保存点
delete from account where id =28;
rollback a;   # 回滚到保存点
```

#### 2.5、补充

```mysql
# 查看当前MySQL连接的隔离级别
select @@tx_isolation

# 设置当前Mysql连接的隔离级别
set transation isolation level read committed;

# 设置数据库系统的全局隔离级别
set global transaction isolation level read committed;
```

## 七、视图

含义：虚拟表，和普通标一样使用。通过表动态生成的数据。只保存了sql逻辑，不保存查询结果。

应用场景：多个地方用到同样的查询结果，该查询结果使用的sql语句比较复杂。

### 1、创建视图

```
create view 视图名 as 查询语句
```

### 2 、示例

#### 2.1、查询姓张学生的名和专业名

#####2.1.1、传统写法

```mysql
select stuname, majorname from stuinfo inner join major on stuinfo.majorid = major.id where stuname like "张%"；
```

#####2.1.2、使用视图

```mysql
# 创建视图
create view v1 AS select stuname, majorname from stuinfo inner join major on stuinfo.majorid = major.id;

# 查询
select * from v1 where stuname like "张%"；
```

#### 2.2、查询邮箱中包含a字符的员工名、部门名和工种信息

##### 2.2.1、传统写法

```mysql
select last_name,job_title,department_name,email from employees e inner join  departments d on d.department_id = e.department_id INNER JOIN jobs j on e.job_id = j.job_id  where e.email like "%a%";
```

##### 2.2.2、使用视图

```mysql
create view v1 as select last_name,job_title,department_name,email from employees e inner join  departments d on d.department_id = e.department_id INNER JOIN jobs j on e.job_id = j.job_id;

select * from v1 where email like "%a%";  
```

#### 2.3、查询各部门的平均工资级别

##### 2.3.1、传统写法

```mysql
select a.id,a.dname,grade_level from (select d.department_id id,d.department_name dname, avg(salary) ag from employees as e inner join departments as d on e.department_id = d.department_id group by d.department_id) as a join job_grades on  a.ag BETWEEN  lowest_sal and highest_sal;
```

##### 2.3.2、使用视图

```mysql
create view v2 as select d.department_id id,d.department_name dname, avg(salary) ag from employees as e inner join departments as d on e.department_id = d.department_id group by d.department_id;

select v2.id,v2.dname, grade_level from v2 inner join job_grades  on v2.ag BETWEEN lowest_sal and highest_sal;
```

#### 2.4、查询平均工资最低的部门信息和工资

```mysql
select * from v2 order by ag limit 1;
```

### 3、视图的查看，修改和删除

#### 3.1、查看

```mysql
desc 视图名； # 查看结构

show create view 视图名； # 查看创建语句
```

#### 3.2、修改

```mysql
# 方式一：
create or replace view 视图名 as select语句；
# 方式二：
alter view 视图名 select语句
```

#### 3.3、删除

```mysql
drop view 视图名1，视图名2，...;
```

### 4、视图的数据更新

```mysql
# 创建一个视图（注意这个视图与原始表对比新增了一个字段）
create or replace view v3 as select last_name, salary * 12 * (1 + IFNULL(commission_pct,0)) "annual salary" from employees;

# 我们通过insert 语句向其插入数据
insert into v3 values("张飞","1000000")
# 发现报错了
1348 - Column 'annual salary' is not updatable

# 我们在创建一个视图（使用原始表的字段）
create or replace view v4 as select last_name, salary from employees

# 我们添加数据
insert into v4 values("张飞","1000000") # 发现视图中的数据和原始表中都新插了一条数据

# update (视图和原始表同时更新)
update v4 set last_name = "张无忌" where last_name = "张飞";

# delete
delete from v4 where last_name = "张无忌";
```

>1、包含分组函数，distinct，group by , having , union或union all 不允许更新
>
>2、常量视图不能更新
>
>3、select中包含子查询不能更新
>
>4、join等连接表不能更新

### 5、视图和表的区别

|      | 创建关键字   | 是否占用物理空间 | 使用                     |
| ---- | ------------ | ---------------- | ------------------------ |
| 视图 | create view  | 只保存sql逻辑    | 增删改查，一般不能增删改 |
| 表   | create table | 保存实际数据     | 增删改查                 |

## 八、存储过程和函数

### 1、存储过程

#### 1.1、创建语法

```mysql
create procedure 存储过程名（参数列表）
begin
	存储过程体（一组和发的sql语句）
end

# 如果存储过程体重仅仅只有一句话，那么began end 可以省略
# 存储过程提中的每条SQL语句的结尾要求必须加分号
# 存储过程的结尾可以使用delimiter重新设置
delimiter 结束标记
```

注意：参数列表包含三部分：参数模式，参数名，参数类型

**参数模式**

* in  该参数可以作为输入，也就是该参数需要调用方传入值
* out  该参数可以作为输出，可以作为返回值
* inout  该参数既可以作为输入，也可以输入。

#### 1.2、调用语法

```mysql
call 存储过程名(实参列表);
```

#### 1.3、参数模式

#####1.3.1、空参列表

```mysql
# 插入五条记录到admin表中
delimiter $
create PROCEDURE p1()
begin
	insert into admin(username,`password`) VALUES("Negan","110"),("Egon","111"),("Rick","112"),("Christian","113"),("大鹅","114");
end $

# 调用
call p1()
```

#####1.3.2、in模式参数的存储过程

```mysql
#案例1： 根据女神名，查询对应的男神信息
create PROCEDURE  p2(in beautyName varchar(20))
begin 
	select bo.* from beauty b left join boys bo on b.boyfriend_id = bo.id where b.name = beautyName;
end

call p2("苍老师")

# 判断用户是否登录成功（接收多个值）
create procedure p3(in username varchar(20), in password varchar(20))
begin
	declare result int default 0;  # 声明变量并初始化
	select count(*) into result # 赋值
	from admin 
	where admin.username = username and admin.`password` = password;
	
	select if(result>0,"成功","失败"); # 使用变量
	
end

call p3("张飞","1111")
```

#####1.3.3、out模式参数存储过程

```mysql
# 根据女神名，返回对应的男神名
create PROCEDURE p4(in beautyName varchar(20), out boyName varchar(20))
begin
	select bo.boyName into boyName   # 将bo.boyName赋值给boyName
	from boys bo inner join beauty b on bo.id=b.boyfriend_id
	where b.name=beautyName;
	
end

call p4("苍老师",@bName);
select @bName;

# 根据女神名，返回对应的男神名和魅力值
create PROCEDURE p5(in beautyName varchar(20), out boyName varchar(20),out userCP int)
begin
	select bo.boyName,bo.userCP into boyName,userCP   # 赋值
	from boys bo inner join beauty b on bo.id=b.boyfriend_id
	where b.name=beautyName;
	
end

call p5("苍老师",@bName,@userCP);
select @bName,@userCP;
```

#####1.3.4、inout模式参数存储过程

```mysql
# 案例1：传入a和b两个值，最终a和b翻倍并返回
create PROCEDURE p6(inout a int, inout b int)
begin 
	set a = a * 2;
	set b = b * 2;
	
end

set @m = 10;
set @n = 20;

call p6(@m,@n);
select @m,@n;
```

####1.4、练习

```mysql
# 创建存储过程实现传入用户名和密码，插入到admin表中
create PROCEDURE p7(in username varchar(20),in password varchar(20))
begin 
	insert into admin(admin.username,admin.`password`) values(username,password);
end；

call p7("小鹅","130");

# 创建存储过程实现出入女神编号，返回女神名称和电话
create procedure p8(in id int, out bname varchar(20), out pnum varchar(20))
begin
	select beauty.name, beauty.phone into bname,pnum
	from beauty where beauty.id = id;
	
end;

call p8(1,@name,@num);
select @name,@num;

# 创建存储过程实现传入两个女神编号，查询其生日，返回大小
create PROCEDURE p9(in id1 int,in id2 int, out res varchar(5))
begin
	declare r1 datetime;  # 声明变量并初始化
	declare r2 datetime;

	
	select borndate into r1 
	from beauty where id = id1;
	select borndate into r2 
	from beauty where id = id2;
	
	select if(datediff(r1,r2)<0,"大","小") into res;
end;

call p9(1,5,@res);
select @res

# 创建存储过程实现出入一个日期，格式化成xx年xx月xx日
create PROCEDURE p8(in mydata datetime, out strDate varchar(40))
begin
	select date_format(mydata,"%y年%m月%d日") into strDate;
end;

call p8(now(),@strDate);
select @strDate;

# 创建存储过程，实现出入女神名称，返回：女神AND男神
create PROCEDURE p1(in beautyName varchar(20), out str varchar(50))
begin 
		select concat(beautyName,"AND", boyName) into str
		from beauty b join boys on b.boyfriend_id = boys.id where b.name = beautyName;
end;

call p1("柳岩",@str);
select @str
```

####1.5、删除存储过程

```mysql
drop procedure 存储过程名   # 一次只能删除一个
```

####1.6、查看存储过程信息

```mysql
show create procedure 存储过程名；
```

### 2、函数

区别于存储过程，存储过程可以有0个返回值，也可以有多个返回值，函数有且只有一个返回值。

#### 2.1、创建语法

```mysql
create function 函数名(参数列表) returns 返回类型
begin
	函数体
end
```

参数列表包括两部分：参数名，参数类型；函数体肯定会有return语句，如果没有回报错。

当函数体中只有一句话，则可以省略begin end

#### 2.2、调用语法

```mysql
select 函数名(参数列表)
```

#### 2.3、案例演示

##### 2.3.1、无参有返回

```mysql
# 返回employees总共的数据条数
create function f1() returns int
begin 
	DECLARE c INT DEFAULT 0; #定义变量
	select COUNT(*) into c   # 赋值
	from employees; 
	return c;
end;

select f1();
```

##### 2.3.2、有参有返回

```mysql
# 根据员工名返回他的工资
create function f4(empName varchar(20)) returns DOUBLE
	begin
		SET @SAL = 0;
		select e.salary  into @SAL
		from employees e where e.last_name = empName;
		return @SAL;
end;

select f2("Valli");
```

#### 2.4、查看删除函数

##### 2.4.1查看函数

```mysql
show create function 函数名；
```

##### 2.4.2、删除函数

```mysql
drop function 函数名；
```

## 九、流程控制结构

### 1、顺序结构

​	程序从上往下依次执行

### 2、分支结构

​	程序从两条或多条路径中选择一条去执行

#### 2.1、if函数

​	功能：实现简单的双分支

```mysql
if (表达式1，表达式2，表达式2)
# 执行顺序，如果表达式1成立，则if函数返回表达式2的值，否则返回表达式3的值
```

#### 2.2、case结构

特点：可以作为表达式，嵌套在其他语句中使用，可以放在任何地方，begin end中或begin end的外面

​	   可以作为独一的语句去使用，只能放在begin end中。

​	   如果when中的值或者条件成立，则执行对应then后面的语句，并且结束case,如果都不满足执行else

​	   else可以省略，如果else省略了，when没有满足的值或语句，则返回null

##### 2.2.1、等值判断

``` mysql
case 变量|表达式|字段 
when 要判断的值1 then 返回的值1或语句;
when 要判断的值2 then 返回的值2或语句;
...
else 要返回的值n或语句
end case;
```

##### 2.2.2、区间判断

```mysql
case
when 要判断的条件1 then 返回的值1或语句;
when 要判断的条件2 then 返回的值2或语句;
...
else 要返回的值n
end case;
```

##### 2.2.3、案例

```mysql
# 创建存储过程，根据传入的成绩，来显示等级，比如传入的成绩：90-100，显示A，80-90.显示B,60-80,显示c,否则显示D

CREATE PROCEDURE TEST(IN score int)
begin
	case 
	when score >= 90 AND score<= 100 then select "A";
	when score >= 80  then select "B";
	when score >= 60 then select "C";
	ELSE select "D";
	END CASE;
end


CALL TEST(88)
```

#### 2.3、if结构

只能放在begin end中使用

#####2.3.1、语法

```mysql
if 条件1 then 语句1；
elseif 条件2 then 语句2；
else 语句n;
end if;
```

##### 2.3.2、案例

```mysql
# 创建存储过程，根据传入的成绩，来返回等级，比如传入的成绩：90-100，返回A，80-90,返回B,60-80,返回c,否则返回D

create function test_if(score int) returns char
begin
	if score >= 90 and score <=100 then return "A";
	ELSEIF score >= 80 THEN return "B";
	ELSE RETURN "C";
	END IF;
end

select(test_if(59)
```

### 3、循环结构

​	程序在满足一定条件的基础上，重复执行一段代码。

#### 3.1、循环控制结构

```mysql
iterate类似于continue, 结束本次循环，继续下一次
leave 类似于break,结束当前所在的循环
```

#### 3.1、while

##### 3.1.1、语法

```mysql
[标签:]while 循环条件 do 
	循环体；
end while[标签]

# 如果加标签可以搭配循环控制使用
```

##### 3.1.2、案例

```mysql
# 批量插入，根据次数插入到admin表中多条记录
create PROCEDURE pro_while(in insertCount int)
begin 
		DECLARE i int default 0;
		while i <= insertCount DO
				INSERT INTO admin(username,password) values(concat("小の", i), "88888");
				set i = i + 1;
				
		end while;
end 


call pro_while(1000);

# 批量插入，根据次数插入到admin表中多条记录,如果次数>20,则停止
create PROCEDURE pro_while(in insertCount int)
begin 
		DECLARE i int default 0;
		a:while i <= insertCount DO
				INSERT INTO admin(username,password) values(concat("小の", i), "88888");
				set i = i + 1;
				if i >= 20 then leave a;
				end if;
		end while a;
end 


call pro_while(1000);


# 批量插入，根据次数插入到admin表中多条记录,只插入偶数次
create PROCEDURE pro_while(in insertCount int)
begin 
		DECLARE i int default 0;
		a:while i <= insertCount DO
				set i = i + 1;
				if mod(i,2) != 0 then ITERATE a;
				end if;
				INSERT INTO admin(username,password) values(concat("小の", i), "88888");
				
		end while;
end 


call pro_while(100);
```

#### 3.2、loop

##### 3.2.1、语法

```mysql
[标签:]loop
	循环体；
end loop[标签]

# 可以用来模拟简单的死循环
```

#### 3.3、repeat

##### 3.3.1、语法

```mysql
[标签:]repeat
	循环体
until 结束循环的条件
end repeat[标签]
```

## 十、用到的数据

### 1、myemployees库

```mysql
/*
SQLyog Ultimate v10.00 Beta1
MySQL - 5.5.15 : Database - myemployees
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`myemployees` /*!40100 DEFAULT CHARACTER SET gb2312 */;

USE `myemployees`;

/*Table structure for table `departments` */

DROP TABLE IF EXISTS `departments`;

CREATE TABLE `departments` (
  `department_id` int(4) NOT NULL AUTO_INCREMENT,
  `department_name` varchar(3) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `location_id` int(4) DEFAULT NULL,
  PRIMARY KEY (`department_id`),
  KEY `loc_id_fk` (`location_id`),
  CONSTRAINT `loc_id_fk` FOREIGN KEY (`location_id`) REFERENCES `locations` (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=271 DEFAULT CHARSET=gb2312;

/*Data for the table `departments` */

insert  into `departments`(`department_id`,`department_name`,`manager_id`,`location_id`) values (10,'Adm',200,1700),(20,'Mar',201,1800),(30,'Pur',114,1700),(40,'Hum',203,2400),(50,'Shi',121,1500),(60,'IT',103,1400),(70,'Pub',204,2700),(80,'Sal',145,2500),(90,'Exe',100,1700),(100,'Fin',108,1700),(110,'Acc',205,1700),(120,'Tre',NULL,1700),(130,'Cor',NULL,1700),(140,'Con',NULL,1700),(150,'Sha',NULL,1700),(160,'Ben',NULL,1700),(170,'Man',NULL,1700),(180,'Con',NULL,1700),(190,'Con',NULL,1700),(200,'Ope',NULL,1700),(210,'IT ',NULL,1700),(220,'NOC',NULL,1700),(230,'IT ',NULL,1700),(240,'Gov',NULL,1700),(250,'Ret',NULL,1700),(260,'Rec',NULL,1700),(270,'Pay',NULL,1700);

/*Table structure for table `employees` */

DROP TABLE IF EXISTS `employees`;

CREATE TABLE `employees` (
  `employee_id` int(6) NOT NULL AUTO_INCREMENT,
  `first_name` varchar(20) DEFAULT NULL,
  `last_name` varchar(25) DEFAULT NULL,
  `email` varchar(25) DEFAULT NULL,
  `phone_number` varchar(20) DEFAULT NULL,
  `job_id` varchar(10) DEFAULT NULL,
  `salary` double(10,2) DEFAULT NULL,
  `commission_pct` double(4,2) DEFAULT NULL,
  `manager_id` int(6) DEFAULT NULL,
  `department_id` int(4) DEFAULT NULL,
  `hiredate` datetime DEFAULT NULL,
  PRIMARY KEY (`employee_id`),
  KEY `dept_id_fk` (`department_id`),
  KEY `job_id_fk` (`job_id`),
  CONSTRAINT `dept_id_fk` FOREIGN KEY (`department_id`) REFERENCES `departments` (`department_id`),
  CONSTRAINT `job_id_fk` FOREIGN KEY (`job_id`) REFERENCES `jobs` (`job_id`)
) ENGINE=InnoDB AUTO_INCREMENT=207 DEFAULT CHARSET=gb2312;

/*Data for the table `employees` */

insert  into `employees`(`employee_id`,`first_name`,`last_name`,`email`,`phone_number`,`job_id`,`salary`,`commission_pct`,`manager_id`,`department_id`,`hiredate`) values (100,'Steven','K_ing','SKING','515.123.4567','AD_PRES',24000.00,NULL,NULL,90,'1992-04-03 00:00:00'),(101,'Neena','Kochhar','NKOCHHAR','515.123.4568','AD_VP',17000.00,NULL,100,90,'1992-04-03 00:00:00'),(102,'Lex','De Haan','LDEHAAN','515.123.4569','AD_VP',17000.00,NULL,100,90,'1992-04-03 00:00:00'),(103,'Alexander','Hunold','AHUNOLD','590.423.4567','IT_PROG',9000.00,NULL,102,60,'1992-04-03 00:00:00'),(104,'Bruce','Ernst','BERNST','590.423.4568','IT_PROG',6000.00,NULL,103,60,'1992-04-03 00:00:00'),(105,'David','Austin','DAUSTIN','590.423.4569','IT_PROG',4800.00,NULL,103,60,'1998-03-03 00:00:00'),(106,'Valli','Pataballa','VPATABAL','590.423.4560','IT_PROG',4800.00,NULL,103,60,'1998-03-03 00:00:00'),(107,'Diana','Lorentz','DLORENTZ','590.423.5567','IT_PROG',4200.00,NULL,103,60,'1998-03-03 00:00:00'),(108,'Nancy','Greenberg','NGREENBE','515.124.4569','FI_MGR',12000.00,NULL,101,100,'1998-03-03 00:00:00'),(109,'Daniel','Faviet','DFAVIET','515.124.4169','FI_ACCOUNT',9000.00,NULL,108,100,'1998-03-03 00:00:00'),(110,'John','Chen','JCHEN','515.124.4269','FI_ACCOUNT',8200.00,NULL,108,100,'2000-09-09 00:00:00'),(111,'Ismael','Sciarra','ISCIARRA','515.124.4369','FI_ACCOUNT',7700.00,NULL,108,100,'2000-09-09 00:00:00'),(112,'Jose Manuel','Urman','JMURMAN','515.124.4469','FI_ACCOUNT',7800.00,NULL,108,100,'2000-09-09 00:00:00'),(113,'Luis','Popp','LPOPP','515.124.4567','FI_ACCOUNT',6900.00,NULL,108,100,'2000-09-09 00:00:00'),(114,'Den','Raphaely','DRAPHEAL','515.127.4561','PU_MAN',11000.00,NULL,100,30,'2000-09-09 00:00:00'),(115,'Alexander','Khoo','AKHOO','515.127.4562','PU_CLERK',3100.00,NULL,114,30,'2000-09-09 00:00:00'),(116,'Shelli','Baida','SBAIDA','515.127.4563','PU_CLERK',2900.00,NULL,114,30,'2000-09-09 00:00:00'),(117,'Sigal','Tobias','STOBIAS','515.127.4564','PU_CLERK',2800.00,NULL,114,30,'2000-09-09 00:00:00'),(118,'Guy','Himuro','GHIMURO','515.127.4565','PU_CLERK',2600.00,NULL,114,30,'2000-09-09 00:00:00'),(119,'Karen','Colmenares','KCOLMENA','515.127.4566','PU_CLERK',2500.00,NULL,114,30,'2000-09-09 00:00:00'),(120,'Matthew','Weiss','MWEISS','650.123.1234','ST_MAN',8000.00,NULL,100,50,'2004-02-06 00:00:00'),(121,'Adam','Fripp','AFRIPP','650.123.2234','ST_MAN',8200.00,NULL,100,50,'2004-02-06 00:00:00'),(122,'Payam','Kaufling','PKAUFLIN','650.123.3234','ST_MAN',7900.00,NULL,100,50,'2004-02-06 00:00:00'),(123,'Shanta','Vollman','SVOLLMAN','650.123.4234','ST_MAN',6500.00,NULL,100,50,'2004-02-06 00:00:00'),(124,'Kevin','Mourgos','KMOURGOS','650.123.5234','ST_MAN',5800.00,NULL,100,50,'2004-02-06 00:00:00'),(125,'Julia','Nayer','JNAYER','650.124.1214','ST_CLERK',3200.00,NULL,120,50,'2004-02-06 00:00:00'),(126,'Irene','Mikkilineni','IMIKKILI','650.124.1224','ST_CLERK',2700.00,NULL,120,50,'2004-02-06 00:00:00'),(127,'James','Landry','JLANDRY','650.124.1334','ST_CLERK',2400.00,NULL,120,50,'2004-02-06 00:00:00'),(128,'Steven','Markle','SMARKLE','650.124.1434','ST_CLERK',2200.00,NULL,120,50,'2004-02-06 00:00:00'),(129,'Laura','Bissot','LBISSOT','650.124.5234','ST_CLERK',3300.00,NULL,121,50,'2004-02-06 00:00:00'),(130,'Mozhe','Atkinson','MATKINSO','650.124.6234','ST_CLERK',2800.00,NULL,121,50,'2004-02-06 00:00:00'),(131,'James','Marlow','JAMRLOW','650.124.7234','ST_CLERK',2500.00,NULL,121,50,'2004-02-06 00:00:00'),(132,'TJ','Olson','TJOLSON','650.124.8234','ST_CLERK',2100.00,NULL,121,50,'2004-02-06 00:00:00'),(133,'Jason','Mallin','JMALLIN','650.127.1934','ST_CLERK',3300.00,NULL,122,50,'2004-02-06 00:00:00'),(134,'Michael','Rogers','MROGERS','650.127.1834','ST_CLERK',2900.00,NULL,122,50,'2002-12-23 00:00:00'),(135,'Ki','Gee','KGEE','650.127.1734','ST_CLERK',2400.00,NULL,122,50,'2002-12-23 00:00:00'),(136,'Hazel','Philtanker','HPHILTAN','650.127.1634','ST_CLERK',2200.00,NULL,122,50,'2002-12-23 00:00:00'),(137,'Renske','Ladwig','RLADWIG','650.121.1234','ST_CLERK',3600.00,NULL,123,50,'2002-12-23 00:00:00'),(138,'Stephen','Stiles','SSTILES','650.121.2034','ST_CLERK',3200.00,NULL,123,50,'2002-12-23 00:00:00'),(139,'John','Seo','JSEO','650.121.2019','ST_CLERK',2700.00,NULL,123,50,'2002-12-23 00:00:00'),(140,'Joshua','Patel','JPATEL','650.121.1834','ST_CLERK',2500.00,NULL,123,50,'2002-12-23 00:00:00'),(141,'Trenna','Rajs','TRAJS','650.121.8009','ST_CLERK',3500.00,NULL,124,50,'2002-12-23 00:00:00'),(142,'Curtis','Davies','CDAVIES','650.121.2994','ST_CLERK',3100.00,NULL,124,50,'2002-12-23 00:00:00'),(143,'Randall','Matos','RMATOS','650.121.2874','ST_CLERK',2600.00,NULL,124,50,'2002-12-23 00:00:00'),(144,'Peter','Vargas','PVARGAS','650.121.2004','ST_CLERK',2500.00,NULL,124,50,'2002-12-23 00:00:00'),(145,'John','Russell','JRUSSEL','011.44.1344.429268','SA_MAN',14000.00,0.40,100,80,'2002-12-23 00:00:00'),(146,'Karen','Partners','KPARTNER','011.44.1344.467268','SA_MAN',13500.00,0.30,100,80,'2002-12-23 00:00:00'),(147,'Alberto','Errazuriz','AERRAZUR','011.44.1344.429278','SA_MAN',12000.00,0.30,100,80,'2002-12-23 00:00:00'),(148,'Gerald','Cambrault','GCAMBRAU','011.44.1344.619268','SA_MAN',11000.00,0.30,100,80,'2002-12-23 00:00:00'),(149,'Eleni','Zlotkey','EZLOTKEY','011.44.1344.429018','SA_MAN',10500.00,0.20,100,80,'2002-12-23 00:00:00'),(150,'Peter','Tucker','PTUCKER','011.44.1344.129268','SA_REP',10000.00,0.30,145,80,'2014-03-05 00:00:00'),(151,'David','Bernstein','DBERNSTE','011.44.1344.345268','SA_REP',9500.00,0.25,145,80,'2014-03-05 00:00:00'),(152,'Peter','Hall','PHALL','011.44.1344.478968','SA_REP',9000.00,0.25,145,80,'2014-03-05 00:00:00'),(153,'Christopher','Olsen','COLSEN','011.44.1344.498718','SA_REP',8000.00,0.20,145,80,'2014-03-05 00:00:00'),(154,'Nanette','Cambrault','NCAMBRAU','011.44.1344.987668','SA_REP',7500.00,0.20,145,80,'2014-03-05 00:00:00'),(155,'Oliver','Tuvault','OTUVAULT','011.44.1344.486508','SA_REP',7000.00,0.15,145,80,'2014-03-05 00:00:00'),(156,'Janette','K_ing','JKING','011.44.1345.429268','SA_REP',10000.00,0.35,146,80,'2014-03-05 00:00:00'),(157,'Patrick','Sully','PSULLY','011.44.1345.929268','SA_REP',9500.00,0.35,146,80,'2014-03-05 00:00:00'),(158,'Allan','McEwen','AMCEWEN','011.44.1345.829268','SA_REP',9000.00,0.35,146,80,'2014-03-05 00:00:00'),(159,'Lindsey','Smith','LSMITH','011.44.1345.729268','SA_REP',8000.00,0.30,146,80,'2014-03-05 00:00:00'),(160,'Louise','Doran','LDORAN','011.44.1345.629268','SA_REP',7500.00,0.30,146,80,'2014-03-05 00:00:00'),(161,'Sarath','Sewall','SSEWALL','011.44.1345.529268','SA_REP',7000.00,0.25,146,80,'2014-03-05 00:00:00'),(162,'Clara','Vishney','CVISHNEY','011.44.1346.129268','SA_REP',10500.00,0.25,147,80,'2014-03-05 00:00:00'),(163,'Danielle','Greene','DGREENE','011.44.1346.229268','SA_REP',9500.00,0.15,147,80,'2014-03-05 00:00:00'),(164,'Mattea','Marvins','MMARVINS','011.44.1346.329268','SA_REP',7200.00,0.10,147,80,'2014-03-05 00:00:00'),(165,'David','Lee','DLEE','011.44.1346.529268','SA_REP',6800.00,0.10,147,80,'2014-03-05 00:00:00'),(166,'Sundar','Ande','SANDE','011.44.1346.629268','SA_REP',6400.00,0.10,147,80,'2014-03-05 00:00:00'),(167,'Amit','Banda','ABANDA','011.44.1346.729268','SA_REP',6200.00,0.10,147,80,'2014-03-05 00:00:00'),(168,'Lisa','Ozer','LOZER','011.44.1343.929268','SA_REP',11500.00,0.25,148,80,'2014-03-05 00:00:00'),(169,'Harrison','Bloom','HBLOOM','011.44.1343.829268','SA_REP',10000.00,0.20,148,80,'2014-03-05 00:00:00'),(170,'Tayler','Fox','TFOX','011.44.1343.729268','SA_REP',9600.00,0.20,148,80,'2014-03-05 00:00:00'),(171,'William','Smith','WSMITH','011.44.1343.629268','SA_REP',7400.00,0.15,148,80,'2014-03-05 00:00:00'),(172,'Elizabeth','Bates','EBATES','011.44.1343.529268','SA_REP',7300.00,0.15,148,80,'2014-03-05 00:00:00'),(173,'Sundita','Kumar','SKUMAR','011.44.1343.329268','SA_REP',6100.00,0.10,148,80,'2014-03-05 00:00:00'),(174,'Ellen','Abel','EABEL','011.44.1644.429267','SA_REP',11000.00,0.30,149,80,'2014-03-05 00:00:00'),(175,'Alyssa','Hutton','AHUTTON','011.44.1644.429266','SA_REP',8800.00,0.25,149,80,'2014-03-05 00:00:00'),(176,'Jonathon','Taylor','JTAYLOR','011.44.1644.429265','SA_REP',8600.00,0.20,149,80,'2014-03-05 00:00:00'),(177,'Jack','Livingston','JLIVINGS','011.44.1644.429264','SA_REP',8400.00,0.20,149,80,'2014-03-05 00:00:00'),(178,'Kimberely','Grant','KGRANT','011.44.1644.429263','SA_REP',7000.00,0.15,149,NULL,'2014-03-05 00:00:00'),(179,'Charles','Johnson','CJOHNSON','011.44.1644.429262','SA_REP',6200.00,0.10,149,80,'2014-03-05 00:00:00'),(180,'Winston','Taylor','WTAYLOR','650.507.9876','SH_CLERK',3200.00,NULL,120,50,'2014-03-05 00:00:00'),(181,'Jean','Fleaur','JFLEAUR','650.507.9877','SH_CLERK',3100.00,NULL,120,50,'2014-03-05 00:00:00'),(182,'Martha','Sullivan','MSULLIVA','650.507.9878','SH_CLERK',2500.00,NULL,120,50,'2014-03-05 00:00:00'),(183,'Girard','Geoni','GGEONI','650.507.9879','SH_CLERK',2800.00,NULL,120,50,'2014-03-05 00:00:00'),(184,'Nandita','Sarchand','NSARCHAN','650.509.1876','SH_CLERK',4200.00,NULL,121,50,'2014-03-05 00:00:00'),(185,'Alexis','Bull','ABULL','650.509.2876','SH_CLERK',4100.00,NULL,121,50,'2014-03-05 00:00:00'),(186,'Julia','Dellinger','JDELLING','650.509.3876','SH_CLERK',3400.00,NULL,121,50,'2014-03-05 00:00:00'),(187,'Anthony','Cabrio','ACABRIO','650.509.4876','SH_CLERK',3000.00,NULL,121,50,'2014-03-05 00:00:00'),(188,'Kelly','Chung','KCHUNG','650.505.1876','SH_CLERK',3800.00,NULL,122,50,'2014-03-05 00:00:00'),(189,'Jennifer','Dilly','JDILLY','650.505.2876','SH_CLERK',3600.00,NULL,122,50,'2014-03-05 00:00:00'),(190,'Timothy','Gates','TGATES','650.505.3876','SH_CLERK',2900.00,NULL,122,50,'2014-03-05 00:00:00'),(191,'Randall','Perkins','RPERKINS','650.505.4876','SH_CLERK',2500.00,NULL,122,50,'2014-03-05 00:00:00'),(192,'Sarah','Bell','SBELL','650.501.1876','SH_CLERK',4000.00,NULL,123,50,'2014-03-05 00:00:00'),(193,'Britney','Everett','BEVERETT','650.501.2876','SH_CLERK',3900.00,NULL,123,50,'2014-03-05 00:00:00'),(194,'Samuel','McCain','SMCCAIN','650.501.3876','SH_CLERK',3200.00,NULL,123,50,'2014-03-05 00:00:00'),(195,'Vance','Jones','VJONES','650.501.4876','SH_CLERK',2800.00,NULL,123,50,'2014-03-05 00:00:00'),(196,'Alana','Walsh','AWALSH','650.507.9811','SH_CLERK',3100.00,NULL,124,50,'2014-03-05 00:00:00'),(197,'Kevin','Feeney','KFEENEY','650.507.9822','SH_CLERK',3000.00,NULL,124,50,'2014-03-05 00:00:00'),(198,'Donald','OConnell','DOCONNEL','650.507.9833','SH_CLERK',2600.00,NULL,124,50,'2014-03-05 00:00:00'),(199,'Douglas','Grant','DGRANT','650.507.9844','SH_CLERK',2600.00,NULL,124,50,'2014-03-05 00:00:00'),(200,'Jennifer','Whalen','JWHALEN','515.123.4444','AD_ASST',4400.00,NULL,101,10,'2016-03-03 00:00:00'),(201,'Michael','Hartstein','MHARTSTE','515.123.5555','MK_MAN',13000.00,NULL,100,20,'2016-03-03 00:00:00'),(202,'Pat','Fay','PFAY','603.123.6666','MK_REP',6000.00,NULL,201,20,'2016-03-03 00:00:00'),(203,'Susan','Mavris','SMAVRIS','515.123.7777','HR_REP',6500.00,NULL,101,40,'2016-03-03 00:00:00'),(204,'Hermann','Baer','HBAER','515.123.8888','PR_REP',10000.00,NULL,101,70,'2016-03-03 00:00:00'),(205,'Shelley','Higgins','SHIGGINS','515.123.8080','AC_MGR',12000.00,NULL,101,110,'2016-03-03 00:00:00'),(206,'William','Gietz','WGIETZ','515.123.8181','AC_ACCOUNT',8300.00,NULL,205,110,'2016-03-03 00:00:00');

/*Table structure for table `jobs` */

DROP TABLE IF EXISTS `jobs`;

CREATE TABLE `jobs` (
  `job_id` varchar(10) NOT NULL,
  `job_title` varchar(35) DEFAULT NULL,
  `min_salary` int(6) DEFAULT NULL,
  `max_salary` int(6) DEFAULT NULL,
  PRIMARY KEY (`job_id`)
) ENGINE=InnoDB DEFAULT CHARSET=gb2312;

/*Data for the table `jobs` */

insert  into `jobs`(`job_id`,`job_title`,`min_salary`,`max_salary`) values ('AC_ACCOUNT','Public Accountant',4200,9000),('AC_MGR','Accounting Manager',8200,16000),('AD_ASST','Administration Assistant',3000,6000),('AD_PRES','President',20000,40000),('AD_VP','Administration Vice President',15000,30000),('FI_ACCOUNT','Accountant',4200,9000),('FI_MGR','Finance Manager',8200,16000),('HR_REP','Human Resources Representative',4000,9000),('IT_PROG','Programmer',4000,10000),('MK_MAN','Marketing Manager',9000,15000),('MK_REP','Marketing Representative',4000,9000),('PR_REP','Public Relations Representative',4500,10500),('PU_CLERK','Purchasing Clerk',2500,5500),('PU_MAN','Purchasing Manager',8000,15000),('SA_MAN','Sales Manager',10000,20000),('SA_REP','Sales Representative',6000,12000),('SH_CLERK','Shipping Clerk',2500,5500),('ST_CLERK','Stock Clerk',2000,5000),('ST_MAN','Stock Manager',5500,8500);

/*Table structure for table `locations` */

DROP TABLE IF EXISTS `locations`;

CREATE TABLE `locations` (
  `location_id` int(11) NOT NULL AUTO_INCREMENT,
  `street_address` varchar(40) DEFAULT NULL,
  `postal_code` varchar(12) DEFAULT NULL,
  `city` varchar(30) DEFAULT NULL,
  `state_province` varchar(25) DEFAULT NULL,
  `country_id` varchar(2) DEFAULT NULL,
  PRIMARY KEY (`location_id`)
) ENGINE=InnoDB AUTO_INCREMENT=3201 DEFAULT CHARSET=gb2312;

/*Data for the table `locations` */

insert  into `locations`(`location_id`,`street_address`,`postal_code`,`city`,`state_province`,`country_id`) values (1000,'1297 Via Cola di Rie','00989','Roma',NULL,'IT'),(1100,'93091 Calle della Testa','10934','Venice',NULL,'IT'),(1200,'2017 Shinjuku-ku','1689','Tokyo','Tokyo Prefecture','JP'),(1300,'9450 Kamiya-cho','6823','Hiroshima',NULL,'JP'),(1400,'2014 Jabberwocky Rd','26192','Southlake','Texas','US'),(1500,'2011 Interiors Blvd','99236','South San Francisco','California','US'),(1600,'2007 Zagora St','50090','South Brunswick','New Jersey','US'),(1700,'2004 Charade Rd','98199','Seattle','Washington','US'),(1800,'147 Spadina Ave','M5V 2L7','Toronto','Ontario','CA'),(1900,'6092 Boxwood St','YSW 9T2','Whitehorse','Yukon','CA'),(2000,'40-5-12 Laogianggen','190518','Beijing',NULL,'CN'),(2100,'1298 Vileparle (E)','490231','Bombay','Maharashtra','IN'),(2200,'12-98 Victoria Street','2901','Sydney','New South Wales','AU'),(2300,'198 Clementi North','540198','Singapore',NULL,'SG'),(2400,'8204 Arthur St',NULL,'London',NULL,'UK'),(2500,'Magdalen Centre, The Oxford Science Park','OX9 9ZB','Oxford','Oxford','UK'),(2600,'9702 Chester Road','09629850293','Stretford','Manchester','UK'),(2700,'Schwanthalerstr. 7031','80925','Munich','Bavaria','DE'),(2800,'Rua Frei Caneca 1360 ','01307-002','Sao Paulo','Sao Paulo','BR'),(2900,'20 Rue des Corps-Saints','1730','Geneva','Geneve','CH'),(3000,'Murtenstrasse 921','3095','Bern','BE','CH'),(3100,'Pieter Breughelstraat 837','3029SK','Utrecht','Utrecht','NL'),(3200,'Mariano Escobedo 9991','11932','Mexico City','Distrito Federal,','MX');

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
```

### 2、girls库

```mysql
/*
SQLyog Ultimate v10.00 Beta1
MySQL - 5.7.18-log : Database - girls
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`girls` /*!40100 DEFAULT CHARACTER SET utf8 */;

USE `girls`;

/*Table structure for table `admin` */

DROP TABLE IF EXISTS `admin`;

CREATE TABLE `admin` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(10) NOT NULL,
  `password` varchar(10) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

/*Data for the table `admin` */

insert  into `admin`(`id`,`username`,`password`) values (1,'john','8888'),(2,'lyt','6666');

/*Table structure for table `beauty` */

DROP TABLE IF EXISTS `beauty`;

CREATE TABLE `beauty` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) NOT NULL,
  `sex` char(1) DEFAULT '女',
  `borndate` datetime DEFAULT '1987-01-01 00:00:00',
  `phone` varchar(11) NOT NULL,
  `photo` blob,
  `boyfriend_id` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=13 DEFAULT CHARSET=utf8;

/*Data for the table `beauty` */

insert  into `beauty`(`id`,`name`,`sex`,`borndate`,`phone`,`photo`,`boyfriend_id`) values (1,'柳岩','女','1988-02-03 00:00:00','18209876577',NULL,8),(2,'苍老师','女','1987-12-30 00:00:00','18219876577',NULL,9),(3,'Angelababy','女','1989-02-03 00:00:00','18209876567',NULL,3),(4,'热巴','女','1993-02-03 00:00:00','18209876579',NULL,2),(5,'周冬雨','女','1992-02-03 00:00:00','18209179577',NULL,9),(6,'周芷若','女','1988-02-03 00:00:00','18209876577',NULL,1),(7,'岳灵珊','女','1987-12-30 00:00:00','18219876577',NULL,9),(8,'小昭','女','1989-02-03 00:00:00','18209876567',NULL,1),(9,'双儿','女','1993-02-03 00:00:00','18209876579',NULL,9),(10,'王语嫣','女','1992-02-03 00:00:00','18209179577',NULL,4),(11,'夏雪','女','1993-02-03 00:00:00','18209876579',NULL,9),(12,'赵敏','女','1992-02-03 00:00:00','18209179577',NULL,1);

/*Table structure for table `boys` */

DROP TABLE IF EXISTS `boys`;

CREATE TABLE `boys` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `boyName` varchar(20) DEFAULT NULL,
  `userCP` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;

/*Data for the table `boys` */

insert  into `boys`(`id`,`boyName`,`userCP`) values (1,'张无忌',100),(2,'鹿晗',800),(3,'黄晓明',50),(4,'段誉',300);

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

```









