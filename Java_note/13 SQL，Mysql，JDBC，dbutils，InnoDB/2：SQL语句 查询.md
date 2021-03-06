### 1：SQL(Structured Query Language) 结构化查询语言：在关系数据库上执行数据操作，检索及维护所使用的标准语言

SQL分为：
	1：数据定义语言（DDL）：用于创建，修改，删除数据库的对象，数据库对象包括：表，视图，索引，序列
	2：数据操作语言（DML）：用于改变数据表的数据。		增，删，改
	3：事物控制语言（TCL）：用于维护数据一致性的语句。	提交，回滚，保存点
	4：数据查询语言（DQL）：用于查询所需要的语言。		 
	5：数据控制语言（DCL）：用于执行权限的授予和回收操作，维护数据库。	授予，回收，创建用户

  注意：
       1:SQL语句本身不区分大小写，但是出于可读性的目的，我们通常会将SQL的关键字全部大写，非关键字全部小写
       1: ；是不必要的但是作为一次语句的结束，分隔符，delimiter
	% 	替代一个或多个字符
	_ 	仅替代一个字符

##### DDL语句：用于创建，修改，删除数据库的对象，数据库对象包括：表，视图，索引，序列

查看有多少个数据库  ：SHOW DATABESES；
选择数据库  	    ：USE 数据库名；
查看数据库中有多少表：SHOW TABLES；
查看表创建的语法    ：SHOW CREATE TABLE 表名 \G；
查看表的结构	    ：DESC 表名；

#####   创建表（CREATE）  //通常记事本写好直接copy

CREATE TABLE 表名（

?		id	数据类型（）PRIMARY KEY not null AUTO_INCREMENT，
?		列名	数据类型（）not null DEFAULT defalut_value，
?		列名	数据类型（），
?		PRIMARY KEY(id)，																					?	 	 ）;

###### 外键约束： foreign key references 引用外键表(列名) 

?	      ：  foreign key(列名) references 目标表名(目标列名)

创建相同的表：CREATE TABLE 表1 LIKE 表2；
从已有的表中创建新表： CREATE TABLE 新表1 SELECT 字段1，字段2...FROM 原表2；

###### 约束（5个）：既可以写在字段之后，也可在最后用括号用约束字段,PRIMARY KEY(id)

   	   1：NULL，NOT NULL - 指示某列存储可以为 NULL或者不能为NULL值
    	   2：PRIMARY KEY - 主键 确保某列（或两个列多个列的结合）有唯一标识，有助于更容易更快速地找到表中的一个特定的记录
   	   3：FOREIGN KEY - 外键 一个表中的 FOREIGN KEY 指向另一个表中的 PRIMARY KEY(唯一约束的键)
    	   4：DEFAULT - 规定没有给列赋值时的默认值
	   5: CHECK -  约束用于限制列中的值的范围，CHECK （条件）

###   修改表（ALTER）： 

1：修改表名

###### 	 AITER TABLE 表名 RENAME 新表名；

2：修改表的结构

###### 	增加新的字段：ALTER TABLE 表名 ADD 字段名... after 字段名|first；

###### 	修改现有字段：ALTER TABLE 表名 MODIFY 修改的字段

###### 	修改字段名：  ALTER TABLE 表名 RENAME column A to B;

###### 	删除现有字段：ALTER TABLE 表名 DROP 字段名；

?		

###   删除表（DROP）：

###### 	DROP TABLE 表名；        删除表

###### 	TRUNCATE TABLE 表名；    清空表中数据



### DML语句：对表中的数据进行操作

1：INSERT INTO 向表中插入数据

###### 	INSERT INTO 表名(id,name，birth) VALUES(1，'jack'，TO_DATE（'2009-06-05',‘YYYY-MM-DD’）)；

?	如果插入的时日期字段:TO_DATE（'2009-06-05',‘YYYY-MM-DD’）

2：DELETE 删除表中的数据，要加WHERE语句限定删除的记录，否则就是清空表操作

###### 	DELETE FROM 表名 WHERE id=i；

3：UPDATE：修改表中的数据

###### 	UPDATE 表名 SET 字段名=值，字段名=值 WHERE 限制记录的字段；

4:SELECT：查询表的内容，* 代表所有字段

###### 	SELECT * FROM 表名；  查看表的所有内容

  SELECT子句：后面跟要查询的字段，也可以是表中的具体字段函数或者表达式,直接在其后面加别名 as 别名；
	  那么结果集会以这个别名作为字段的名字，可以使用 "" 给别名区分大小写和添加空格

######   DISTINCT：对指定字段重复的记录去重，但是用多字段去重时，是字段的组合没有重复值(不同的)

?	例：SELECT DISTINCT column_name as new_name,column_name FROM table_name;  去重

######   FROM子句：用来制定数据表的来源，其后可以加 ORDER BY 字句排序；?		

######   WHERE子句：用来添加限制条件，可以有多个，只将满足条件的记录查询出来

?	     1：>,<,>=,<=,<>等价于！=，=		
?	     2：使用AND，OR，NOT，AND优先级高于OR，通过括号来提高

 	     3：IS NULL ,IS NOT NULL :判断是不是为空

?	     4：IN，NOT IN ：判断在不在列表中[]，常用来判断子查询的结果
?		例：WHERE id IN （1，2）；
?	     5：BETWEEN values1 AND values2  ：判断值在values1和values2之间()

?	     6：LIKE（模糊查询）：只知道其中某个字符
?		两个通配符：
?			    %：任意个字符，_：一个字符
?		name LIKE %lisi ;
?	     7：ANY，ALL是配合>,>=,<,<=,一个列表使用的，常用于子查询
?		1：>ANY(list)：大于列表最小的
?		2：>ALL（list）：大与列表最大的
?		3：<ANY (list) ：小于列表最小的
?		4：<ALL(list)：小于列表最小的



### 2：QUERY（查找）

投影(projector)：部分列组成的新的集合 
SELECT name as emp_name,sex,age FROM emp; 
选择(selector)：部分行组成新的集合 
SELECT * FROM stu WHERE id<10;

### 3：GROUP BY（分组）

将结果集按照其后指定的字段值相同的记录看做一组，常和分组函数联用

分组函数（聚合函数）：对某些字段的值进行统计的
    1：MAX（），MIN（）：求指定字段的最大值和最小值
    2：AVG，SUM：求平均值和总和
    3：COUNT（）函数：不是对给定字段的值进行统计，而是对给定字段不为NULL的记录数进行统计，
		          		实际上所有聚合函数都忽略NULL值的统计
   COUNT（*）:统计这个表有多少记录，有时候返回long，有时候返回BigInteger
   NVL（），配合进行

 select price, count(*) AS 记录数 from A group by price;

### 4：排序

ORDER BY 子句：可以根据指定的字段对结果集按照该字段的值进行升序或者降序排序

?	  ASC：升序，默认的不用写
?    	  DESC ：降序
 	  按照多字段排序时，首先按照第一个字段排序，当第一个字段有重复值时才会按照第二个字段排序方式进行排序
?	  以此类推，每个字段都可以单独的指定排序方式，在排序中NULL值被认为是最大值
?	   select * FROM stu1 ORDER BY age DESC;	
?		

### 5：WHERE 限制条件

WHERE ：不能使用聚合函数作为过滤条件，原因是过滤时机不对

WHERE是在数据库检索表中数据时，对数据逐条过滤以决定是否查询该数据是否使用的，所以WHERE用来确定结果集的数据的

若要使用聚合函数的结果作为过滤条件时，那么一定是数据从表中查询完毕得到的结果集，并且分组完毕才进行聚合函数统计结果，得到后才可以对分组进行过滤，由此可见这个过滤时机是在WHERE之后进行的，所以聚合函数的过滤条件要在HAVING子句中使用，
HAVING必须在GROUP BY之后

SELECT dep_id,count(dep_id) FROM emp GROUP BY dep_id HAVING count(dep_id)>1；



##### 6:查询语句的执行顺序：提高效率

1：FROM子句：执行顺序从后往前，从右往左，数据量较少的表尽量放在后面
2：WHERE子句：自下而上，从右到左

3：GROUP BY 子句：从左往
4：HAVING子句：消耗资源，两者结合使用

5：SELECT 子句：少用*号，尽量取字段名称
6：ORDER BY 子句：从左到右排序，消耗资源

### 7：分页查询 LIMIT 

SELECT * FROM emp LIMIT 3;//返回从第一条开始，查询三条， 实际是：0,3;
SELECT * FROM emp LIMIT 3,5;//从结果的第4条开始，查询5条



### 8:多表查询(关联查询)：

###  从多张表中查询对应记录的信息，关联查询的重点与这些表中的记录的对应关系，这个关系也称连接条件

N张表就有N-1个连接条件

注意：
1：当两张表有相同字段时，SELECT子句中必须明确指定该字段来自那张表
	   在关联查询中，表名也可以有别名，在表名其后直接写，这样可以简化语句的复杂度

2：关联查询要添加连接条件，否则会产生笛卡尔积
   它是一个无意义的结果集，它的记录数是与所有参与查询的表的记录数乘积的结果，当数据量大时，会出现内存溢出

3：连接条件，过滤条件全部写在WHERE中
   例：SELECT e.id,d.dname AS dep_name,e.name,e.sex,e.age FROM emp e,dep d WHERE e.dep_id=d.id;



### 9：连接查询(JOIN):用来完成关联查询

##### 内连接：获取两个表中字段匹配关系的记录，可以省略 INNER 使用 JOIN，效果一样

###### 	FROM 表名1 表1对象 INNER JOIN 表2名 表2对象 ON 连接条件 WHERE 过滤条件

例： select e.id,d.dname as dep_name,e.name,e.sex,e.age FROM emp e INNER JOIN dep d ON e.dep_id=d.id; 

##### 外链接：所有数据都显示

外连接分为：

######   左外连接：（LEFT JOIN）以JOIN左侧作为驱动表，获取左表所有记录，即使右表没有对应匹配的记录，用NULL 填充

  例：select e.id,d.dname as dep_name,e.name,e.sex,e.age from emp e LEFT JOIN dep d ON e.dep_id=d.id; 

######   外链接：（RIGHT JOIN）右外连：与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录，用NULL填充

  例：select e.id,d.dname as dep_name,e.name,e.sex,e.age from emp e RIGHT JOIN dep d ON e.dep_id=d.id;








