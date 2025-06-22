# Mysql数据库

## SQL分类

- DDL	Data Definition Language 数据定义语言
  - 用来操作数据库对象，表，字段
- DML       Data Manipulation Language 数据操作语言
  - 对数据库表中的数据进行增删改的
- DQL       Data Query Language 数据查询语言
  - 数据查询
- DCL        Data Control Language 数据控制语言
  - 创建数据库用户，控制数据库的访问权限

### DDL-数据库操作

- 查询
  - SHOW DATABASES;查询所有数据库
  - SELECT DATABASE(); 查询当前数据库
- 创建
  - create database [if not exists] 数据库名 [default charset 字符集] [collate 排序规则];
- 删除
  - drop database [if exists] 数据库名;
  - drop database [if exists] 数据库名;
- 使用
  - use 数据库名;

### DDL-表操作-查询
- 查询当前数据库所有表
	- show tables;
- 查询表结构
	- desc 表名;
- 查询指定表的建表语句
	- show create table 表名;
### DDL-表操作-创建

```mysql
create table 表名(
	字段1 字段1类型[comment 注释],
	字段2 字段2类型[comment 注释],
	字段3 字段3类型[comment 注释],
	......
	字段n 字段n类型[comment 注释]
	)[comment 表注释];
```

### DML-数据库

```mysql
update 表名 set 类名 = 数据 where 行名;
```



### DQL-查询

```mysql
select * from 表名 where age < 20;
select * from tablename where age <= 20;
select * from tablename where idcard is null; -- idcard为空
select * from tablename where idcard is not null;
select * from tn where age != 88;
select * from tn where age <> 88; -- 查询年龄不等于88
select * from tn where age >= 15 && age <= 20; 在[15,20]
select * from tn where age >= 15 and age <= 20; 在[15,20]
select * from tn where age between 15 and 20; -- 数字写反查不到
select * from tn where age < 25 and gender = '女';
where age = 18 or age = 20 or age = 40;
where age in(18,20,40); -- 和上面一样
where columnname like '__'; -- '两个下划线'匹配两个字符
where columnname like '%X';
```

***

==聚合函数==

| 函数 | 功能 |
| ---- | ---- |
|count|统计数量 |
| max |最大值 |
| min |最小值 |
| avg |平均值 |
| sum |求和 |
```mysql
-- columnName列名
select 聚合函数(字段列表) from 表名;
select count(idcard) from tableName; --统计数量
select avg(age) from emp;
select max(age) from columnName;
select min(age) from columnName;
select sum(age) from tn where cn = '西安' -- 统计西安地区员工的年龄之和
```
***
==分组查询==
```mysql
select 字段列表 from 表名 [where 条件] group by 分组字段名 [having 分组后过滤条件];
-- 1.根据性别分组,统计男性员工 和 女性员工的数量
select gender, count(*) from tn group by gender;
-- 2.根据性别分组,统计男性和女性员工的平均年龄
select gender, avg(age) from tn froup by gender;
-- 3.查询年龄小于45的员工,并根据工作地址分组,获取员工数量大于等于3的工作地址
select name,workaddress,count(*) from tn where age < 45 group by workaddress having count(*) >= 3;
-- where > 聚合函数(分组) > having
```
where和having的区别
-  执行时机不同,分组前where分组后执行having
-  where不能对聚合函数进行判断,而having可以

- ==分组之后,查询的字段一般为聚合函数和分组这段,查询其他字段无意义==

***

### 排序查询 order by

```mysql
select 字段列表 from 表名 order by 字段一 排序方式一,字段二 排序方式二;
-- asc(升序)默认 --DESC(降序)
-- 1.根据年龄对公司的员工进行升序排序
select * from tn order by age asc;
-- 2.根据入职时间对公司的员工进行降序排序
select * from tn order by entrydate desc;
-- 3.根据年龄对公司的员工进行升序排序,年龄相同,在按照入职时间进行降序排序
select * from tn order by age asc,entrydate desc
```

***

### 分页查询

```mysql
select 字段列表 from limit 起始索引,查询记录数;
select * from tn limit 10;
select * from tn limit 10,10;
```

==注意==

- 起始索引从0开始,起始索引 = (查询页码 -1) *每页记录数;
- 分页查询时数据库的方言,不同的数据库有不同的是实现,mysql中是limit
- 如果查询的是第一页的数据,起始索引可以省略,直接写成limit 10

```mysql
select name , age from tn where age <= 35 order by age asc , entrydate desc;
select * from tn where gender = '男' and age between 20 and 40 order by age asc , entrydate asc limit 5;
```

### DCL-管理用户

```mysql
use mysql;
select * from user;
create user '用户名'@'主机名' identified by '密码';
alter user '用户名'@'主机名' identified with mysql_native_password by '新密码';
drop user '用户名'@'主机名';

```

### DCL-权限控制

```mysql

```

| 权限               | 说明               |
| ------------------ | ------------------ |
| all,all privileges | 所有权限           |
| select             | 查询数据           |
| insert             | 插入数据           |
| update             | 修改数据           |
| delete             | 删除数据           |
| alter              | 修改表             |
| drop               | 删除数据库/表/视图 |
| create             | 创建数据库/表      |

```mysql
-- 查询权限
show grants for '用户名'@'主机名';
-- 授予权限
grant 权限列表 on 数据库.表名 '用户名'@'主机名'
-- 撤销权限
revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名'
```

==注意==

- 多个权限之间用逗号分割
- 授权时,数据库名和表名可以使用 * 进行通配,代表所有

## 函数

==函数==是指一段可以直接被另一段程序调用的程序或代码

### 字符串函数

| 函数                     | 功能                                                    |
| ------------------------ | ------------------------------------------------------- |
| concat(s1,s2,s3,sn)      | 字符串拼接,将这几个拼接                                 |
| lower(Str)               | 将这个str字符串小写                                     |
| upper(str)               | 大写                                                    |
| lpad(str,n,pad)          | 左填充,用字符串pad对str的左边进行填充,达到n个字符串长度 |
| rpad(Str,n,pad)          | 右填充,用字符串pad对str的右边进行填充,达到n个字符串长度 |
| trim(Str)                | 去掉字符串头部和尾部的空格                              |
| substring(str,start,len) | 返回从字符串str从start位置起的len个 长度的字符串        |

substring(helloss,1,5)返回hello

### 数值函数

| 函数       | 功能                              |
| ---------- | --------------------------------- |
| cel(x)     | 向上取整数                        |
| floor(x)   | 向下取整数                        |
| mod(x,y)   | 返回x/y的模 !!!!!                 |
| rand()     | 返回0-1内随机数                   |
| round(x,y) | 求参数x的四舍五入的值,保留y位小数 |

### 日期函数

| 函数                              | 功能                                              |
| --------------------------------- | ------------------------------------------------- |
| curdate()                         | 返回当前日期                                      |
| curtime()                         | 返回当前时间                                      |
| now()                             | 返回当前日期和时间                                |
| year(date)                        | 获取指定date的年份                                |
| month(date)                       | 获取指定date的月份                                |
| day(date)                         | 获取指定date的日期                                |
| date_add(date,interval expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| datediff(date1,date2)             | 返回起始时间date1和结束时间date2之间的天数        |

```mysql
select now();
select day(now());
select data_add(now(), interval 70 day);
select datediff('2021-12-01' , '2021-10-01')
-- 案例 as 'entrydays' 这个是起个别名
select name,datediff(curdate(),entrydate) as'entrydays' from tableName order by entrydays desc;
```

### 流程函数

流程函数也是很常用的一类函数,可以在sql语句中实现筛选,从而提高语句的效率

| 函数                                                       | 功能                                                     |
| ---------------------------------------------------------- | -------------------------------------------------------- |
| if(value,t,f)                                              | 如果value为true,则返回t,否则返回f                        |
| ifnull(value1,value2)                                      | 如果value1不为空返回value1,否则返回value2                |
| case when [val1] then [res1] ... else [default] end        | 如果val1为true,返回res1, ... 否则返回default默认值       |
| case [expr] when [val1] then [res1] ... else [default] end | 如果expr的值等于val1,返回res1, ... 否则返回default默认值 |

```mysql
select if(false,'ok','Error');
select ifnull('ok','Default');	-- ok
select ifnull('','Default');	-- ''
select ifnull(null,'Default');	-- Default
-- 需求: 查询emp表中的员工姓名和工作地址(北京/上海 ----> 一线城市, 其他-----> 二线城市)
select 
	name,
	(case  workaddress when'北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end) as '工作地址' from emp;
```

## 约束

### 概述

1. 约束是作用的表中字段上的规则,用于限制存储在表中的数据
2. 目的:保证数据库中数据的正确性,有效性,和完整性
3. 分类

| 约束               | 描述                                                    | 关键字      |
| ------------------ | ------------------------------------------------------- | ----------- |
| 非空约束           | 非空                                                    | not null    |
| 唯一约束           | 唯一                                                    | unique      |
| 主键约束           | 非空且唯一标识                                          | primary key |
| 默认约束           | 默认值                                                  | default     |
| 检查约束(8.0.16后) | 保证字段满足某一个条件                                  | check       |
| 外键约束           | 用来让两张表之间的数据建立连接,保证数据的完整性和一致性 | foreing key |

### 外键约束

外键约束那个表是子表(从表),而主键那个表为父表(主表)

==添加外键删除外键==

```mysql
-- 添加外键 外键名称和外键字段不一样
alter table 表名 add constraint 外键名称 foreign key (外键字段名) references 主表名(主表主键);
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);
-- 删除外键
alter table 表名 drop foreign key 外键名称;

```

删除||更新行为

```mysql
-- cascade 有外键主键变了你也变 删除一条就全字段删除
-- set null 主键删除了,外键设置为空,外表数据只是那个字段变为了空
alter table 表名 add constraint 外键名称 foreign key (外键字段名) references 主表名(主表主键字段名) on update cascade on delete cascade
```

## 多表查询

### 多表关系

- 一对多
  - 在多的一方建立外键,指向一的一方的主键
- 多对多
  - 建立第三张中间表,中间表至少包含两个外键,分别关联两方主键
- 一对一
  - 在任意一方加入外键,关联另外一方的主键,并且设置外键为唯一的(unique)

### 多表查询概述

从多张表中查询数据

```mysql
-- 多表查询 笛卡尔积 17*6=102条数据
select * from emp,dept where emp.dept_id = dept.id;

```

### 连接查询

#### 内连接

交集

```mysql
select emp.name, dept.name from emp,dept where emp.dept_id = dept.id;
-- emp的别名为e 表名起了别名就不能再用原名了
select e.name, d.name from emp e,dept d where e.dept_id = d.id;
select e.name, d.name from emp e inner join dept d on e.dept.id = d.id;

```



#### 外连接

==左外连接==	左边所有,以及两张表的交集

==右外连接==	右边所有,以及两张表的交集部分

```mysql
select 字段列表 from 表1 left [outer] join 表2 on 条件 ...;
select 字段列表 from 表1 right [outer] join 表2 on 条件 ...;
select e.*,d.name from emp e left outer join dept d on e.dept_id = d.id;
select e.*,d.* from emp e right outer join dept d on e.dept_id = d.id;
-- 右外可以改为左外
```

#### 自连接

当前表与自身的连接查询,自连接==必须使用表别名==

```mysql
select 字段列表 from 表A 别名A join 表A 别名B on 条件...;
-- 查询员工,以及 所属领导的名字
select a.name, b.name from emp a , emp b where a.managerid = b.id;
用外连接,才能显示全部的员工数据,左外连接
select a.name '员工' , b.name '领导' from emp a left join emp b on a.managerid = b.id; 
```

### 联合查询 - union, union all

```mysql
select 字段列表 from 表A ...
union [all]
select 字段列表 from 表B ...;
-- 将两次查询的结果合并,重复的就会出现两次
select * from emp where salary < 5000
union all
select * from emp where age > 50;
-- 去到all 就会去重
select * from emp where salary < 5000
union 
select * from emp where age > 50;
-- union 要求两个语句查出的列数保持一致,字段类型也需要保持一致

```

### 子查询

sql语句中切套select语句,称为嵌套查询,也称为==子查询==

```mysql
select * from t1 where column1 = (select column1 from t2);
/* 外部语句可以是 insert update delete select 任意一个
-- * 结果不同
-- 标量子查询(单个值)
-- 列子查询(单列)
-- 行子查询(单行)
-- 表子查询(多行多列)
-- * 子查询位置不同
分为: where之后;from之后;select之后

*/

```

#### 标量子查询(单个值)

常用操作符: = <> > >= < <=

```mysql
select id from dept where = '销售部';
-- 由于子查询的数据是单个值
select * from emp where dept_id = (select id from dept where = '销售部');
select entrydate from emp where = '方东白';
select * from emp where entrydate > (select entrydate from emp where = '方东白');
```

#### 列子查询(单列)

常用操作符: in	not in 	any	some	all

| 操作符 | 描述                                      |
| ------ | ----------------------------------------- |
| in     | 在指定的范围内                            |
| not in | 不在指定的范围内                          |
| any    | 子查询返回的列表中,有任意一个满足条件即可 |
| some   | 于上一个等同,使用some的地方都可以用any    |
| all    | 子查询返回列表的所有值都必须满足          |

```mysql
select id from dept where name = '销售部' or '市场部';
select * from emp where dept_id in (2,4);
将上一条的查出的2,4直接换成上条语句 (in 里面是集合,表示多种2,4)
select * from emp where dept_id in (select id from dept where name = '销售部' or '市场部');
-- 查询出来的是一列数据,all的含义就是要全部满足
select * from emp where salary > all (select salary from emp where dept_id = (select id from dept where name = '财务部'))
-- 比子查询列的任意一个高就行(some和any一样)
select * from emp where salary > any (select salary from emp where dept_id = (select id from dept where name = '研发部'))
```

#### 行子查询(单行)

常见的操作符: = 	<> 	in 	not in

```mysql
select salary ,managerid from emp name = '张无忌';
select * from emp where (salary,managerid) = (var1 , var2)
---->
select * from emp where (salary,managerid) = (select salary ,managerid from emp name = '张无忌');
```

#### 表子查询(多行多列)

常见的操作符: in

```mysql
select job ,salary from emp where name = '鹿杖客' or '宋远桥';
select * from emp where (job,salary) in (select job ,salary from emp where name = '鹿杖客' or '宋远桥');
------------------------------------------------
-- from 之后作为一张表来左外连接查询表
select * from emp where entrydate > '2006-01-01';
select e.* , d.* from (select * from emp where entrydate > '2006-01-01') e left join dept d on e.dept.id = d.id;
```

### 多表查询案例

1. 查询员工的姓名,年龄,职位,部门信息

2. 查询年龄小于30岁的员工姓名,年龄,职位,部门信息

3. 查询拥有员工的部门id,部门名称

4. 查询所有年龄大于40岁的员工,及其归属的部门名称,如果没有分配部门,也需要展示出来

5. 查询所有员工的工资等级

6. 查询研发部所有员工的信息及工资等级

7. 查询研发部员工的平均工资

8. 查询工资比灭绝高的员工信息

9. 查询比平均薪资高的员工信息

10. 查询低于本部门平均工资的员工信息

11. 查询所有的部门信息,并统计部门的员工人数

12. 查询所有学生的选课情况,展示出学生名称,学号

    

```mysql
-- 1. 查询员工的姓名,年龄,职位,部门信息
-- 表:emp dept
-- 连接条件: emp.dept_id = dept.id
select e.name , e.age , e.job , d.name from emp e , dept d where e.dept_id = d.id;
-- 2. 查询年龄小于30岁的员工姓名,年龄,职位,部门信息
-- 表:emp dept
-- 连接条件: emp.dept_id = dept.id
select e.name , e.age , e.job , d.name form emp e inner join dept d on e.dept_id = d.id where e.age < 30;
-- 3. 查询拥有员工的部门id,部门名称
select distinct d.id d.anme from emp e ,dept d where e.dept_id = d.id;
-- 4. 查询所有年龄大于40岁的员工,及其归属的部门名称,如果没有分配部门,也需要展示出来
select e.* , d.name from emp e left join dept d on e.dept_id  = d.id where e.age > 40;\
-- 5. 查询所有员工的工资等级
-- 表: emp salgrade
-- 连接条件:emp.salary >= salgrade.losal and emp.salary <= salgrade.hisal
select e.name , s.grade from emp e , salgrade s where e.salary >= s.local and e.salary <= s.hisal;
-- 简写为
select e.name , s.grade from emp e , salgrade s where e.salary between s.losal and s.hisal;
-- 6.查询研发部所有员工的信息及工资等级
/*
表结构: emp , salgrade , dept 
连接条件:至少有n-1个连接条件
e.dept_id = d.id; 
emp.salary between s.losal and s.hisal;
*/
select e.* , s.grade from emp e ,dept d , salgrade s where e.dept_id = d.id and (e.salary between s.losal and s.hisal) and d.anme = '研发部';
-- 7. 查询研发部员工的平均工资
select avg(e.salary) from emp e , dept d where e.dept_id  = d.id and d.name = '研发部';
-- 8. 查询工资比灭绝高的员工信息
select salary from emp where name = '灭绝';
select * from emp where salary > (select salary from emp where name = '灭绝');
-- 9. 查询比平均薪资高的员工信息
select avg(salary) from emp;
select * from emp where salary > (select avg(salary) from emp);
-- 10. 查询低于本部门平均工资的员工信息
select avg(e1.salary) from emp e1 where e1.dept_id = 1;
select * ,(select avg(e1.salary) from emp e1 where e1.dept_id = e2.dept_id) '平均'
from emp e2 
where e2.salary < 
(select avg(e1.salary) 
 from emp e1 
 where e1.dept_id = e2.dept_id);
-- 11. 查询所有的部门信息,并统计部门的员工人数
select id , name from dept;
select d.id , d.name , select count(*) from emp where e.dept_id = d.id ) '人数' from dept d;
-- 12. 查询所有学生的选课情况,展示出学生名称,学号
-- 连接条件: student.id = student_course.studentid , course.id = student_course.courseid
-- 多表联查 !!!!消除笛卡尔积
select s.name , s.no , c.name 
from student s ,student_course sc , course c 
where 
s.id = sc.studentid
and sc.courseid = c.id;
```

## 事务

### 事务简介

==事务==是一组操作的集合,它是一个不可分割的工作单位,事务会把所有的操作作为一个整体一起向系统提交或者撤销操作请求,即这些操作要么同时发生,要么同时失败

```mysql
select @@autocommit;
set @@autocommint = 0;
commit; -- 提交事务
rollback; -- 回滚事务
-- 方式二-----------------------------
-- 开启事务
start transaction; 或者 begin;
-- 事务操作
```

### 事务的四大特性

原子性,一致性,隔离性,持久性

### 并发事务所引发的问题

- 脏读,数据在读取时,数据还没有被处理
- 不可重复读,两次读到的数据不一致
- 幻读,查询的时候发现没有,但是我插入的时候由发现有了这个数据

### 事务隔离级别

| 隔离级别                   | 脏读 | 不可重复读 | 幻读 |
| -------------------------- | ---- | ---------- | ---- |
| read uncommitted           | √    | √          | √    |
| read committed             | ×    | √          | √    |
| repeatable read(mysql默认) | ×    | ×          | √    |
| serializable               | ×    | ×          | ×    |



```mysql
-- 查看事务隔离级别
select @@transaction_isolation;
-- 设置事务隔离级别
set [session | global] transaction isolation level {事务隔离级别}
```

## 存储引擎

### mysql体系结构

- 连接层

最上层是一些客户端和链接服务,主要完成一些类似于连接处理,授权认证,级相关的安全方案。服务器也会为安全接入的每个客户端验证它所具有的操作权限。

- 服务层

主要完成大多数的核心服务功能，如sql接口，并完成缓存的查询，sql的优化和分析，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程，函数等。

- 引擎层

存储引擎真正的负责了musql中数据的存储和提取，服务器通过api和存储引擎通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎。

- 存储层

主要是将数据存储在文件系统之上，并完成于存储引擎的交互。

### 存储引擎简介

存储引擎就是存储数据，建立索引，更新、查询数据等技术的实现方式，存储引擎是基于表的，而不是基于库的，所以存储引擎也可以被称为表类型。
`show engines;`在创建表的时候`create table tableName()engine = 引擎名字`

### 存储引擎特点

- innoDB

innodb是一种兼顾高可靠性和高性能的通用存储引擎，在musql5.5之后，就是msyql默认的存储引擎。

==特点==：DML支持ACID模型，支持==事务==；==行级锁==，提高了并发访问性能；支持==外键约束==，保证数据的完整性和正确性。

==文件==：xxx.ibd 存储该表的表结构（frm，sdi），数据和索引。

逻辑存储结构 表空间，段，区，页，行

- myisam

介绍：早期的默认引擎

特点：不支持事务；支持表锁，不支持行锁；访问速度快

文件：xxx.sdi：存储表结构；xxx.MYD：存储数据；xxx.MYI；存储索引

- Memory

缓存数据库，内存中的临时表，或缓存使用

 hash索引

文件：xxx.sdi：存储表结构信息

### 存储引擎选择

根据特点选择

## 索引

### 索引概述

是帮助mysql==髙效获取数据结构(有序地)==,在数据之外,数据库系统还维护着满足特定查找算法的数据结构,这些数据结构以某种方式引用（指向）数据，这样就可以摘这些数据结构上实现高级查找算法，这种数据结构就是索引。

| 优点                                   | 缺点                                   |
| -------------------------------------- | -------------------------------------- |
| 提高数据索引的效率，降低数据库的io成本 | 索引也需要占据空间                     |
| 降低排序成本，降低cpu的消耗            | 提高了查询效率，但是降低了更新表的速度 |

### 索引结构

b+树最重要

| 索引结构              | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| b树                   | 最常见的索引，大部分存储引擎都支持                           |
| hash索引              | 底层数据结构式哈希表实现的，只有精确匹配索引列的查询才会有效，不支持范围查询 |
| R—tree（空间索引）    | 空间索引是MyLSAM引擎的一个特殊的索引类型，主要用于地理空间数据类型，使用少 |
| full-text（全文索引） | 1是一种通过倒排索引，快速匹配文档的方式，类似于lucene，lolr，es |

### 索引结构

B+树 hash结构

### 索引的分类

主键索引，唯一索引，常规索引，全文索引

聚集索引，二级索引（辅助索引）下面挂的是唯一也就是聚集索引id

- 如果存在主键，主键就是聚集索引
- 没有，唯一索引就是聚集索引
- 都没有，生成一个rowid作为隐藏聚集索引

先走二级索引，在走聚集索引，然后这个过程叫做回表查询

### 索引语法

- 创建索引

  - ```mysql
    create [unique | fulltext] index index_name on table_name (index_COl_name,...);
    ```

- 查看索引 `show index from table_name;`

- 删除索引`drop`

```mysql
-- 创建索引，
create index idx_user_name on tb_user(name);
create unique index idx_user_phone on tb_user(phone);
create index idxxx_user_pro_age_sta on rb_user(profession,age,status);
create index idx_user_email on tb_user(email); 
drop index idx_user_email on tb_user;
```

### sql性能分析

sql执行频率 `show global status like 'Com___'一个下滑线代表一个字符`

- ==慢查询日志==

`show variables like 'slow_query_log';`查看慢查询是否开启

slow_query_log=1	long_query_time=2

日志在 /var/lib/mysql/localhost-slow.log

命令：`tail -f localhost-slow.log`

- ==profile详情==

通过` select @@have_profiling;`查看当前数据库支不支持

看看开启没，默认关闭。查看是否开启`select @@profiling;` `set profiling = 1;`

通过`show profiles;`查看当前会话全部sql语句的执行耗时情况。可以看到id

查看指定qyery_id的sql语句各个阶段的耗时情况`show profile for query id;`

查看指定query_id的sql语句cpu的使用情况。`show profile cpu for query id;`

- ==explain执行计划==

explain或者desc命令可以获取到如何执行select语句的信息，包括在select语句执行过程中表如何连接和连接的顺序。

`[explain|desc] select 字段列表 from 表名 where 条件;`

结果分析：

- id： 表的查询顺序，id值越大越先执行，id值相同，从上到下执行； 

- select_type
- ==type== 表示连接的类型，性能由好到差的连接类型为null ,system,const（主键和唯一键）,eq_ref,ref(非唯一索引查询),range,index（用了索引）,all（全表扫描）
- ==possible_key==：显示可能用在这张表上的索引，一个或者多个。
- ==key==：实际使用的索引，如果为null，则没有用到索引。
- ==ley_len==：表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，在不损失精度的前提下，长度越短越好。
- rows：执行查询的行数，这是一个预估值。
- filtered：表示返回结果的行数占所需读取行数的百分比，filtered的值越大越好。
- Extra：其他

### 索引使用

- 验证索引效率

看大数据时在sql语句最后加入\G;

创建索引 `create index idx_sku_sn on tb_sku(sn)` 这个过程构建b+树的过程

- 最左前缀法则

在查询时，从索引的==最左列==开始，并且不跳过索引中的列。如果跳跃了某一列，==索引将部分失效（后面的字段索引失效==）。跟where的存放位置没关系

- 范围查询

在联合索引中，出现范围查询 <>  ,==右边的列索引失效==。使用>=这种右边的列就不会失效。 

- 索引列运算

不要在索引上进行运算操作，索引将失效。

- 字符串不加引号

在字符串的类型不加单引号，索引将失效。

- 模糊查询

如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引将失效。头部模糊就会失效。如 %工%

- or连接条件

用or分割开来的条件，如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及到的索引都不会被用到。

- 数据分布影响

mysql的评估使用索引比全表更慢，则不会使用索引。如字段中都是null，where字段is null走全表扫描，因为前提条件

- sql提示

sql提示，是优化数据库的一个重要手段，简单来说，就是sql语句中加入一些人为的提示来达到优化操作的目的。

use index：用哪个索引。建议用这个

ignore index：忽略哪个索引

force index：强制mysql用这个索引

- ==覆盖索引==

尽量使用覆盖索引（查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到，减少了select * 。回表查询导致二级索引==子节点挂着的是id==回表查询了聚集索引

- 前缀索引

如果索引很大，我们可以将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高效率。

`create index idx_xxxx on table_name(column(n));`n是长度

n的选择 `select count(distinct email)/count(*) from tb_user;`

`select count(distinct substring(email,1,10))/count(*) from tb_user;`

- 单列索引与联合索引

单列索引和联合索引，如果存在多个查询条件，考虑到针对查询字段建立索引时，建议建立联合索引，而非单列索引。避免回表查询！！！！！。注意最左前缀法则

### 索引设计原则

1. 数据量较大，且查询比较频繁的表建立索引。
2. 针对于常作为查询条件，排序，分组操作的字段建立索引。
3. 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，索引的效率越高
4. 字段较长，采用前缀索引
5. 尽量使用联合索引，避免回表查询
6. 要控制索引的数量，索引并不是多多益善，维护也需要代价
7. 索引列不能是空值，用not null约束一下，当优化器知道每列是否包含空值，它可以更好的确定哪个索引最有效的利用与查询。

## sql优化

### 插入数据

- 批量插入
- 手动事务提交
- 主键循序插入

- 大批量插入数据

插入数据时也用顺序插入会提高性能 

数据库连接 `mysql --local-infile -u root -p` 

查看开关是否开启`select @@local_infile;`

 `set global local_infile = 1;`设置全局参数，将本地文件导入开关开启

`hand 文件名`

load命令` load data local infile '文件位置' into table tb_user fields terminated by ',' lines terminated by '\n';`

### 主键优化

表数据都是根据主键顺序组织存放的，这种储存方式的表称为索引组织表（IOT）

主键乱序插入就会发生页分裂。

当数据的页占到了50%就会发生页合并

尽量降低主键的长度

尽量不要使用自然主键如UUID身份证号

对业务操作不要改主键，会动索引结构

### order by 优化

1. using filesort：通过表索引或者全表扫描，读取到满足的行，然后在排序缓冲区sort buffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫fileSort排序。
2. using index 通过有序索引顺序扫描直接返回有序数据，这种情况即为using index，不需要额外排序，操作效率高。

- 根据排序字段建立合适的索引，多字段排序时，也遵循左前缀法则
- 尽量使用覆盖索引
- 对字段排序，一个升序，一个降序，此时需要注意联合索引在创建初期的规则（asc/desc)
- 如果不可避免的出现了lilesort，大数据排序时，可以适当增大排序缓存区大小 sort_buffer_size(默认256K)

### group by优化

```mysql
drop index 索引名 on tb_user;
select profession,count(*) from tb_user group by profession;
sreate index idx_user_pro_age_sta on tb_user(profession,age,status);
explain select age , count(*) from tb_user where profession = '软件工程' group by age;
```

### limit优化

通过覆盖索引加子查询来优化

```mysql
show tables;
select * from tb_sku limit 0 , 10;
select * from tb_sku limit 1000000 , 10;
select id from tb_sku limit 9000000 , 10;
select s.* from tb_sku s (select in from tb_sku order by id limit 9000000 , 10) a where s.id = a.id;
```

### count优化

大数据量的count计数,优化思路:自己计数;

```mysql
explain select count(*) from tb_user;
show tables;
select * from tb_user;
select count(*) from tb_user;
select count(id) from tb_user;
select count(profession) from tb_user; 如果为null就不会计数;
select count(1) from tb_user; 行不为空,都会计数;
count(主键) count(字段)是否有 not null count(1) 按行进去累加; count(*) 不取值,直接累加;
* and 1 > 主键 > 字段; 尽量用*
```

### update优化

```mysql
 -- 当开启事务时,更改一行数据
 name这个字段没有索引,根据name字段来找到某一行更新,这时,行锁就会升级成表锁,影响并发;	
 所以就得用索引更新数据,且索引不能失效,这样才是行锁;
```



## 视图/存储过程/触发器

==还有一个存储函数这4个被称为称为存储对象==

### 介绍-视图的检查选项

```mysql
创建视图;
create [or replace] view 视图名称[(列名列表)] as select语句 [ with [ cascaded | local ] check option ]
create or replace view stu_v_1 as select id , name from student where id <=  10;
查询视图;
show create view stu_v_1;
select * from 视图名称 ...;
select * from stu_v_1 where id = 3;
修改视图;
方式一: create or replace view stu_v_1 as select id , name , no from student where id <=  10;
方式二: alter view stu_v_1 as select id , name , from student where id <=  10;
删除视图:
drop view if exists stu_v_1;
```

```mysql
create or replace view stu_v_1 as select id,name,from student where id <= 20;
select * from stu_v_1;
insert into stu_v_1 values(6,'tom');
实际插入到了基表中了
insert into stu_v_1 values(30,'tom');
这个30插入之后视图是查不到的这时候就得使用检查选项
然后操作视图就会报错
```

视图检查选项，例如插入，更新，删除，以使其符合视图的定义。mysql允许基于另一个视图创建视图，它还会检查依赖视图中的规则以保持一致性。为了确定检查的范围，mysql提供了两个选项：cascaded和local，默认值为cascaded。

cascaded（级联）：不仅会检查v2这个条件还会检查v1这个条件代码如下：满足多层依赖都会检查

```mysql
create view v1 as select id , name from student where id <=20;
create view v2 as select id , name from v1 where id >= 10 with cascaded check option; 
```

local：它也会递归检查条件但是也会判断被依赖的视图是否有检查，有检查才会报错

```mysql
create view v1 as select id , name from student where id <=20;
create view v2 as select id , name from v1 where id >= 10 with local check option; 
```

### 视图的更新

要使视图可更新，==视图中的行与基础表中的行之间必须存在一对一的关系==。如果视图包含以下的任何一项，这还试图不可更新：

1. 含有聚合函数或者窗口函数（sum(),min(),max(),count()）
2. distinct
3. group by
4. having
5. union 或者 union all

### 视图作用

- 简单：视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视图，从而使得用户不必为以后的操作每次指定全部的条件。
- 安全：数据库可以授权，但不能授权到特定的列和行上面。通过视图用户只能查询或者修改他们所能见到的数据。
- 数据独立：视图可帮助用户屏蔽真实表结构变化带来的影响。

****

视图案例

```mysql
为了保证数据库表的安全性，开发人员在操作user表时，只能看到用户的基本字段，屏蔽手机号和邮箱两个字段。
create view tb_user_view as select id,name,profession,age,gengder,status,createtime from tb_user;
查询每个学生所修课程（三张表联查），这个功能在很多业务中都有使用到，为了简化操作，定义一个视图。
select s.name,s.no,c.name form student s student_course sc course c where s.id = sc.studentid and sc.courseid = c.id;
create view tb_student_course_view as 
select s.name stuent_name, s.no student_no, c.name cource_name form student s student_course sc course c where s.id = sc.studentid and sc.courseid = c.id;
直接查询视图
select * from tb_stu_course_view;
```

### 存储过程

将多条sql语句封装到存储过程中，这样批量的使用数据库，在网络传输上提高效率。sql语句的复用。

特点

- 封装，复用
- 可以接受参数，也可以返回数据
- 减少网络交互，效率提高

==基本语法==

```mysql
-- 创建
create procedure 存储过程名称([参数列表])
begin
	sql语句;
end
-- 调用
call 名称([参数])
-- 查看
select * from information_schema.routines where routine_schema = 'xxx'; -- xxx 为数据库名称; 查看某个数据库的存储过程;
show create procedure 存储过程名称; -- 查看某个存储过程的定义
-- 删除
drop procedure [if exists] 存储过程名称;
```

==注意==：;会结束sql，利用`delimiter $$`，再来执行存储过程创造语句。这个会使接下来的语句都必须用$$结束。

- ==变量==

系统变量：是mysql服务器提供，不是用户定义的，属于服务器层面。分为全局变量（global），会话变量（session）。

系统变量相关语法

查看系统变量

```mysql
show [session | global] variables; 查看所有系统变量
show [session | global] variables like '........' ; 可以通过模糊匹配来查找变量
select @@[session | global] 系统变量名; 查看指定变量的值
```

设置系统变量

```mysql
set [session | global] 系统变量名 = 值;
set @@[session | global] 系统变量名 = 值;
全局变量服务器重启又会恢复原来的
可以在/etc/my.cnf中配置

```

***

用户变量：用户不用提前声明，在用的时候直接用 “@变量名”使用就好了。其作用域为当前连接。一个@符是用户，两个@是系统变量。

```mysql
赋值
推荐 := 这种符号
set @varname = 'wei';
set @varage := 10;
select var_name := 'wei',.....; 
set @varname := 'wei',@varage := 10;
select count(*) into @mycount from tb_user; 查询结果直接赋值
查看
select @varname,@varage;
```

==用户定义的变量是不用初始化，没赋值就是null==

局部变量 访问之前，需要declare声明。可以用作存储过程内的局部变量和输入参数，局部变量的范围==是在其内声明的begin ......end块==。

```mysql
声明
declare 变量名 变量类型[default ...]; --可以指定默认值
赋值
set 变量名 = 值;
set 变量名 := 值;
select 字段名 into 变量名 from 表名 ...;
```

- ==if==

```mysql
if 条件1 then
	.....
elseif 条件2 then 可选
else
	.....	可选
end if;
例如
create procedure p3()
begin
	declare score int default 58;
	declare result varchar(10);
	if score >= 85 then
		set result := '优秀';
	elseif score >=60 then
		set result := '及格';
	else 
		set result := '不及格';
	end if;
	select result;
end;
参数写死，判定结果只是展示出来了
```

参数

| 类型  | 含义               | 备注 |
| ----- | ------------------ | ---- |
| in    | 输入               | 默认 |
| out   | 输出               |      |
| inout | 都可以出入或者输出 |      |



```mysql
create procedure p4 (in score int,out result varchar(10))
begin
	if score >= 85 then
		set result := '优秀';
	elseif score >=60 then
		set result := '及格';
	else 
		set result := '不及格';
	end if;
end;
call p4(68,@result);
select @result;
```

```mysql
create procedure p5(inout score double)
begin
	set score := score *0.5;
end;
set @score = 78;
call p5(@score);
select @score;
```

==case==

```mysql
case case_value
	when when_value1 then statement_list1
	[when when_value2 then statement_list2]
	[else statement_list]
end case;
方式二
case
	when search_condition1 then statement_list1
	[when search_condition2 then statement_list2]
	[else statement_list]
end case;
案例传入的月份，判定月份所属的季节
create procedure p6(in month int)
begin
	declare result varchar(10);
	case
		when month >= 1 and month <= 3 then
			set result := '第一季度';
		when month >= 4 and month <= 6 then
			set result := '第二季度';
		when month >= 7 and month <= 9 then
			set result := '第三季度';
		when month >= 10 and month <= 12 then
			set result := '第四季度';
		else
			set result := '非法参数';
	end case;
	
	select concat('您输入的月份为:' ,month, ',所属的季度为:' ,result);
end;
call p6(4);

```

循环 while

```mysql
while 条件 do
	sql逻辑...
end while;
create procedure p7(in n int)
begin
	declare total int default 0;
	while n > 0 do
		set total := total + n;
		set n := n -1;
	end while;
	select total;
end;
call p7(100);
```

repeat 循环 当满足条件退出循环

```mysql
repeat
	sql逻辑...
	until 条件
end repeat;

create procedure p8(in n int)
begin
	declare total int default 0;
	repeat
		set total := total + n;
		set n := n - 1;
	until n <= 0
	end repeat;
end;
call p8(100);
```

loop 循环

loop实现简单的循环，如果不在sql中增加退出循环的条件，可以用其来实现简单的死循环。loop可以配合一下两个语句使用：

- leave: 配合循环使用退出循环。
- iterate：必须用在循环中，作用是跳出当前循环剩下的语句，直接进入下一个循环。

```mysql
[begin_local:] loop
	sql逻辑...
end loop [end_label];
leave label; --退出指定标记的循环体
iterate label; 直接进入下一次循环
create procedure p9(in n int)
begin
	declare total int default 0;
	sum:loop
		if n <= 0 then 
			leave sum;
		end if;
		set total := total + n;
		set n := n - 1;
	end loop sum;
	select total;
end;
call p9(10);
create procedure p10(in n int)
begin
	declare total int default 0;
	sum:loop
		if n <= 0 then 
			leave sum;
		end if;
		if n%2 = 1 then
			set n := n - 1;
			iterate sum;
		end if;
			set total := total + n;
			set n := n - 1;
	end loop sum;
	select total;
end;
call p10(100);
```

==游标==

用来存储查询结果集的数据类型，在存储过程和函数中可以使用游标对结果集进行循环的处理。游标的使用包括游标的声明，open，fetch和close，其语法如下

```mysql
声明游标
declare 游标名称 cursor for 查询语句;
打开游标
open 游标名称;
获取游标
fetch 游标名称 into 变量 [,变量];
关闭游标
close 游标名称;
例子
create procedure p11(in uage int)
begin
	
    declare uname varchar(100);
	declare upro varchar(100); 游标的声明得在普通变量之后
	declare u_cursor cursor for select  name ,profession form
    tb_user where id <=  uage;
	
	drop table if exists tb_user_pro;
	create table if no exist tb_user_pro(
    	id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
    );
    
    open u_cursor;
    while true do
    	fetch u_cursor into uname,upro;
    	insert into tb_user_pro values (null, nname, upro);
    end while;
    close u_cursor;
end;

```

条件处理程序可以用来定义在流程控制结构执行中遇到的问题时相应的处理步骤。具体语法为



```mysql
declare handler_action handler for condition_value [,condition-value] ... statement;
handler_action
contiune
exit
condition_value
	sqlstate sqlstate_value: 状态码 如02100
	sqlwarning:所有以01开头的sqlstate代码的简写
	not found:所有以02开头的sqlstate代码的简写
	sqlexception:没被上述两个捕获的sqlstate代码的简写
```

完善这个

```mysql
create procedure p11(in uage int)
begin
	
    declare uname varchar(100);
	declare upro varchar(100); 游标的声明得在普通变量之后
	declare u_cursor cursor for select  name ,profession form
    tb_user where id <=  uage;
    声明一个条件处理程序，满足02000，关闭游标
    declare exit handler for sqlstate '02000' close u_cursor
	
	drop table if exists tb_user_pro;
	create table if no exist tb_user_pro(
    	id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
    );
    
    open u_cursor;
    while true do
    	fetch u_cursor into uname,upro;
    	insert into tb_user_pro values (null, nname, upro);
    end while;
    close u_cursor;
end;
```

### 存储函数 

存储函数是有返回值的存储过程，存储函数的参数只能是in类型。具体语法如下

```mysql
create function 存储函数名称([参数列表])
returns type [characteristic ...]
begin
	sql语句
	return ....;
end;
characteristic 说明:
- deterministic :相同的输入参数总会产生相同的结果
- nosql：bu包含sql语句
- reads sql data：包含读取数据的语句，但不包含写入数据的语句。
例子
create function fun1(n int)
returns int deterministic
begin
	declare total int default 0;
	while n>0 do 
		set total := total + n;
		set n := n - 1;
	end while;
	return total;
end;
调用函数
select fun1(100);
```

### 触发器

什么是触发器

于表有关的数据库对象，只在insert、update、delete之前或者之后，触发并执行触发器中定义的sql语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性，日志记录，数据校验等操作。

使用别名old和new来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器只支持行级触发，不支持语句级触发。

| 触发器类型 | new和old                                               |
| ---------- | ------------------------------------------------------ |
| insert     | new表示将要或者已经修改新增的数据                      |
| update     | old表示修改之前的数据，new表示将要或者已经修改后的数据 |
| delete     | old表示将要或者已经删除的数据                          |

语法

```mysql
创建触发器
create trigger trigger_name
before/after insert/update/delete
on tbl_name for each row --行级触发器
begin
		trigger_stmt;
end;
查看
show triggers;
删除
drop trigger [schema_name.]trigger_name; -- 如果没有指定schema_name,默认为当前数据库。
例子
日志表表结构
create trigger tb_user_insert_trigger
	after insert on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, opertae_params) values (null, 'insert' , now(), new.id , concat('插入的数据内容为：id=',new.id,'name = ',new.name,',phone=',new.phone,',email=',new.email,',profession=',new.profession));
end;
-- 查看触发器
show triggers;
删除触发器
drop trigger tb_user_insert_trigger;


```

更新触发器

```mysql
create trigger tb_user_update_trigger
	after update on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, opertae_params) values (null, 'update' , now(), new.id , 
concat(
'更新之前的数据内容为：id=',old.id,'name=',old.name,',phone=',old.phone,',email=',old.email,',profession=',old.profession
'更新之后的数据内容为：id=',new.id,'name=',new.name,',phone=',new.phone,',email=',new.email,',profession=',new.profession  ));
end;
show triggers
update tb_user set age = 32 where id = 23;

```

删除数据的触发器

```mysql
create trigger tb_user_update_trigger
	after delete on tb_user for each row
begin
	insert into user_logs(id, operation, operate_time, operate_id, opertae_params) values (null, 'delete' , now(), old.id , 
concat(
'删除之前的数据内容为：id=',old.id,'name=',old.name,',phone=',old.phone,',email=',old.email,',profession=',old.profession
));
end;
```

## 锁

### 概述

锁是计算机协调多个进程或者线程并发访问某一资源的机制。在数据库中，除了传统的计算资源（cpu，RAM，i/o）的征用外，数据也是一种提供给许多用户共享的资源。如何保证资源的并发访问的一致性，有效性是所有数据库必须要解决的一个问题，锁冲突也是数据库访问性能的一个重要的因素。从这个角度来看，锁对数据库显得尤为重要，也更加复杂。

### 全局锁

只读状态，dml，和ddl都会阻塞

做全局的逻辑备份，对所有的表进行锁定，从而获得一致性视图，保证数据的一致性。

```mysql
flush tables with read lock;
mysqldump -uroot -p1234 itcast>itcast.sql 
unlock tables;
例子
在windows的窗口上访问
mysqldump -uroot -p1234 itcast>itcast.sql --itcast 数据库名称;
itcast.sql文件地址

```

在innoDB中,我们可以用这样的参数来完成不加锁的一致性数据备份

```mysql
mysqldump --single-transaction -uroot -p1234 itcast>itcast.sql
```

### 表级锁

分为表锁，元数据锁，意向锁

对于表锁我们分为表共享读锁和表独占写锁。

```mysql
加锁
lock tables 表名 ... read/write
释放锁
unlock tables / 客户端断开连接
读锁不会阻塞查询操作，但是会阻塞其他操作
写锁对于当前客户端又能写又能读，但是对于其他客户端不能读也不能写
```

==对于元数据锁==，在访问一张表会自动加上。MDL锁主要是作用是维护表元数据的数据一致性，在表上有活动事务的时候，不可以对元数据进行写入操作。==为了避免DML和DDL语句冲突，保证读写的正确性。

在musql5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁（共享）；当对表结构变更操作的时候，加MDL写锁（排他）

```mysql
查看元数据锁
select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.netadata_locks;

```

| 对应SQL                                    |                锁类型                 | 说明                                                      |
| ------------------------------------------ | :-----------------------------------: | --------------------------------------------------------- |
| lock tables xxx read/write                 | shared_read_only/shared_no_read_write |                                                           |
| select,select ... lock in share mode       |              shared_read              | shared_read   shared_write这两种锁兼容但是和exclusive互斥 |
| insert,update,delete,select ... for update |             shared_write              | shared_read   shared_write这两种锁兼容但是和exclusive互斥 |
| alter table ...                            |               exclusive               | 于其他DML都互斥                                           |

==对于意向锁==

为了避免DML在执行过程中，加的==行锁和表锁冲突==，在innodb中引入了意向锁，使的表锁不用检查每行数据是否加锁，使用意向锁来减少表锁的检查。

1. 意向共享锁（is），由语句select ... lock in share mode 添加。
2. 意向排他锁（ix），由insert,update,delete,select ...for update 添加。

- is：与表锁共享锁兼容，于表锁排他锁互斥。
- ix：于表锁共享锁及排他锁都互斥。意向锁之间不会互斥。

```mysql
查看意向锁
select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.data_locks
```

```mysql
select * from score where id = 1 lock in share mode;
-- lock in share mode代表的是加上这一行的共享锁，同时为score加上意向锁。
在事务中执行，update语句会自动加上改动行的行锁，并且加上这张表的ix意向排他锁。
这个时候另一个线程去读写锁都加不上 在事务提交之后。这个表锁才加的上。

```

### 行级锁

发生锁冲突的概率最低，并发最高，引用与innodb中

innodb的数据是基于索引组织的，行锁是通过对索引上的索引项加锁实现的，而不是对记录加的锁。对于行级锁，主要分为三类：

1. 行锁（Record lock）：锁定单个记录的锁，防止其他事务对此进行update和delete。在RC，RR隔离级别下都支持。也叫记录锁 。-- RC

2. 间隙锁（gap lock）：锁定索引记录间隙（不含该记录），确保索引间隙不变，防止其他事务在这个间隙进行insert，产生幻读。在RR隔离级别下都支持
3. 临键锁（next-key lock）：行锁和间隙锁组合，同时锁住数据和前面的间隙。在RR隔离级别下支持。

==行锁==

1. 共享锁 S：允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁。
2. 排他锁 x：允许获取排他锁的事务更新数据，阻止其他事务获得相同的数据集的共享锁和排他锁。

| 当前锁\请求锁 | 共享锁s | 排他锁x |
| ------------- | ------- | ------- |
| s             | 兼容    | 互斥    |
| x             | 互斥    | 互斥    |

| sql                           | 行锁类型 | 说明                                      |
| ----------------------------- | -------- | ----------------------------------------- |
| insert ...                    | 排他锁   | 自动加锁                                  |
| update ...                    | 排他锁   | 自动加锁                                  |
| delete ...                    | 排他锁   | 自动加锁                                  |
| select (正常)                 | 不加锁   |                                           |
| select ... lock in share mood | 共享锁   | 需要手动在select之后加 lock in share mode |
| select  ... for update        | 排他锁   | 需要手动在select之后加 for update         |

默认情况下innodb在repeatable read 事务隔离级别运行，innodb使用next -key 锁进行搜索和索引扫描，以防止幻读。

1. 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，会自动优化成行锁。
2. inodb的行锁是针对索引加的锁，不通过索引条件检索数据，那么innodb将对表中的所有记录加锁，此时==就会升级为表锁==

```mysql
select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.data_locks;
select * from stu where id = 1 lock in share mode;加入共享锁和表意向锁

```

1. 索引上的等值查询（唯一索引）,给不存在的记录加锁时，优化为间隙锁。
2. 索引上的等值查询（普通索引），向右遍历时最后一个值不满足查询需求时，next-key lock 退化为间隙锁。
3. 索引上的等值查询（唯一索引）--会访问到不满足条件的第一个值为止。

```mysql
--设表中有三个字段 id,name,age
id有1 3 8 11 ...等
begin;
update stu set age = 10 where id = 5;
这会我这条语句就会创造一个x,gap 间隙锁，锁住 3 - 8这个间隙。
线程B中进行如下操作
begin;
insert into stu values(7,'ruby',7);
这条语句会被阻塞
在A线程中
commit; 提交事务释放锁然后B中的阻塞就会放开。插入成功。
这是间隙锁的案例
```

```mysql
--设表中有三个字段 id,name,age
id有1 3 8 11 ...等
为age字段建立索引
create index idx_stu_age on stu(age);
begin;
select * from stu where age = 3 lock in share mode;
select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.data_locks;
发现对id为3的数据加了行锁，S,rec_not_gap (没有间隙) ,还有s,3,3这是一个临键锁,还有间隙锁s,gap 7,7
```

```mysql
线程A
begin;
select * from stu where id >= 19 lock in share mode;
加锁情况
19记录加一个行锁，还有一个临键锁19的下一个数据以及之前的间隙，还有一个临键锁 supremum pseu这个是为了锁25之后的间隙。
```

间隙锁唯一的目的就是为了其他事务插入间隙造成幻读现象，间隙锁可以共存，一个事物采用的间隙锁不会阻止另一个事务在同一个间隙上采用间隙锁。

## innoDB引擎

### 逻辑存储结构

  表，段，区，页，行

表空间（ibd文件），一个mysql实例可以对应多个表空间，用于存储记录，索引等数据。

段，分为数据段（leaf node segment），索引段（non-leaf node segment），回滚段(rollback segment)，innodb是索引组织表，数据段就是B+树的叶子子节点，索引段即为B+树的非叶子结点。段用来管理多个区(extent)。

区，表空间的单元结构，每个区的大小为1M。默认情况下，inndb存储引擎页大小为16k，即一个区中有64个连续的页。1024k = 16k * 64

页，是innodb磁盘管理最小的单元，每个页默认16kB，为了保证页的连续性，innodb存储引擎每次都会从磁盘申请4-5个区；

行，是按行进行存放的。trx_id：每次对某条记录进行改动时，都会把对应的事务id赋值给trx_id隐藏列。 Roll_pointer：每次对某条引记录进行改动时，都会把旧的版本写入undo日志中，然后这个隐藏列就相当于一个指针，可以通过它来找到该记录修改前的信息。

### 架构

innodb它擅长事务处理，具有恢复崩溃特性，在日常开发中使用非常广泛。

内存结构：

==buffer pool==：缓冲池是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增删改查操作时，先操作缓冲池中的数据（若缓冲池中没有数据，则从磁盘加载并缓存），然后再以一定频率刷新到磁盘，从而减少磁盘io，加快处理速度。

缓存池以page为单位，底层采用链表数据结构管理page。根据状态，将page分为三种：

- free page：空闲page，未被使用。
- clean page： 被使用page，数据没有被修改过。
- dirty page ：脏页，被使用page，数据被修改过，页中数据与磁盘数据产生不一致。

==change buffer==：更改缓存区（针对的是非唯一的二级索引页），在执行DML语句时，如果这些数据page没有在puffer pool中，不会直接操作磁盘，而会将数据变更存储在更改缓冲区change buffer中，在未来数据被读取时，在将数据合并恢复到buffer pool中，在将合并的数据刷新到磁盘中。

这种缓冲区批量进行磁盘io

==adaptive hash index== ：自适应hash索引，用于优化对buffer pool数据的查询。innodb存储引擎会监控对表上各索引页的查询，如果观察到hash索引可以提升速度，则建立hash索引，称之为自适应hash索引。

自适应哈希索引，无需人工干预，是系统根据情况自动完成。参数：adantive_hash_index

==log buffer==：日志缓冲区，用来保护要写入到磁盘中的log日志数据（redo.log ,undo.log），默认大小为16MB，日志缓冲区的日志会定期刷新到磁盘中。如果需要更新，插入，或者删除许多行的事务，增加日志缓冲区的大小可以节省i/0。

参数：innodb_log_buffer_size：缓冲区大小 	innodb_flush_log_at_trx_commit：日志刷新到磁盘时机

1. 1：日志在每次事务提交时写入并刷新到磁盘
2. 0：每秒将日志写入并刷新到磁盘一次。
3. 2：日志在每次事务提交后写入，并每秒刷新到磁盘一次。

```mysql
show variables like '%log_buffer_size%';
```

***

磁盘结构

==system tablespace==：系统表空间是更改缓冲区的存储区域。如果表是在系统表空间而不是每个表文件或者通用表空间创建的，它也可能包含表和索引数据。

参数：innodb_data_file_path

==file-per-table tablespaces==：每个表的文件表空间包含单个innodb表的数据和索引，并存储在文件系统上的单个数据文件中。

参数：innodb_file_per_table

==general tablespaces==：通用表空间，需要通过create tablespace 语法来创建通用表空间，在创建表时，可以指定该表空间。 

```mysql
创建表空间
create tablespace ts_inheima add datafile 'mytheima.ibd' engine = innodb;
user itcaset;
创建表的时候自己指定表空间
create table a(id int primary key auto_increment ,name varchar(10))engine = innodb tablespace ts_itheima;

```

==undo tablespaces==：撤销表空间，mysql实例在初始化时会自动创建两个默认的undo表空间（初始大小为16M），用于存储undo log日志。

==temporary tablespaces==：innodb使用会话临时表空间和全局临时表空间。存储用户临时创建的临时表等数据。

==doublewrite buffer files==：双写缓冲区，innodb引擎将数据页从buffer pool刷新到磁盘前，先将数据页写入双写缓冲区文件中，便于系统异常时恢复数据。dblwr表示

==redo.log==：重做日志，是用来实现事务的持久性。该日志文件由两部分组成：重做日志缓冲（redo log buffer） 以及重做日志文件（redo log），前者是在内存中，后者在磁盘中。当事务提交之后会把所有的修改信息都存放在改日志中，用于在刷新脏页到磁盘时，发生错误时，进行数据恢复使用。 ib_logfile0 or ib_logfile1

***

后台线程

1. master thread

核心后台线程，负责调度其他线程，还负责将缓存池总的数据异步刷新到磁盘中，保持数据的一致性，还包括脏页的刷新，合并并插入缓存，undo页的回收。

2. io thread

在innodb存储引擎中大量使用了AIO异步非阻塞io来处理io请求，这样可以极大的提高数据库的性能，而iothread主要负责这些io请求的回调。

| 线程类型             | 默认个数 | 职责                       |
| -------------------- | -------- | -------------------------- |
| read thread          | 4        | 负责读                     |
| write thread         | 4        | 负责写                     |
| log thread           | 1        | 负责将日志缓冲区刷新到磁盘 |
| insert buffer thread | 1        | 负责将写缓存区刷新到磁盘   |

```mysql
show engine innodb status;
查看引擎状态
```

3. purge thread

主要用于回收事务以及提交的undo log，在事务提交之后，undo log可能不用了，就用他来回收。

4. page cleaner thread

协助master thread刷新脏页到磁盘的线程，它可以减轻master thread的工作压力，减少阻塞。

### 事务原理

隔离级别

ACD：由两份日志保证 undo log redo log， I隔离性由这两个锁机制和MVCC来保证

==redo log== 持久性

重做日志，记录的是事务提交时数据页的物理修改，是用来实现事务的持久性。

undo log 原子性

回滚日志，用于记录数据被修改前的信息，作用包含两个：提供回滚和mvcc（多版本并发控制）。

undo log记录的是逻辑日志。它用来记录操作性，比如你delete删除一个记录，就会记录一条insert记录，你更新一条，他就会记录一条反向update记录 ，当执行回滚操作时，就可以冲undo log中的逻辑读取到相应的内容并进行回滚。

undo log 在事务提交时产生，事务提交后，不会立刻删除，因为还会用于mvcc

undo log存储：undo log采用段的方式进行管理和记录，存放在前面介绍的rollback segment回滚段中，内部包含1024个undo log segment

==一致性是由redo log 和 undo log共同维护的。==

### MVCC

多版本并发控制

==当前读==：读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁。对于我们日常的操作，如：select ... lock in share mode(共享锁)，select ... for update 、update、insert、delete（排他锁）都是一种当前读。

==快照读==：简单的select（不加锁）就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据，不加锁，是非阻塞读。

- read Committed：每次select都生成快照读
- repeatable read：开启事务后第一个select语句才是快照读的地方
- serializable：快照读会退化为当前读。

multi-version concurrency control 多版本并发控制。指维护一个数据的多个版本，使得读写操作没有冲突，快照读为mysql实现mvcc提供了一个给阻塞读功能。mvcc的具体实现，还需要依赖于数据库中的三个隐式字段、undo log日志、readview。

***

记录中的隐藏字段

db_trx_id :

db_roll_ptr:

db_row_id:

| 隐藏字段    | 含义                                                         |
| ----------- | ------------------------------------------------------------ |
| db_trx_id   | 最近修改事务id，记录插入这条记录或最后一次修改该记录的事务id |
| db_roll_ptr | 回滚指针，指向这条记录的上一个版本，用于配合undo log ，指向上一个版本 |
| db_row_id   | 隐藏主键，如果表结构没有指定主键，将会生成该隐藏字段         |

```mysql
cd /var/lib/mysql
cd itcast/
ll
ibd2sdi stu.ibd 查看该文件
```

undo log

在insert的时候，只在回滚时候需要，

在update、delete的时候，不仅回滚需要，快照读也需要。

undo log 版本链

不同事物或者相同的事务对同一条记录修改，导致该记录的undolog生成一条记录版本的链条。链表的头部是最新的版本，尾

部是最早的版本

readview

是快照读sql执行时mvcc提取数据的依据，记录并维护了当前的活跃事务（未提交的）id。readview包含了4个核心字段：

| 字段           | 含义                           |
| -------------- | ------------------------------ |
| m_ids          | 当前活跃的事务id集合           |
| min_trx_id     | 最小活跃事务id                 |
| max_trx_id     | 预分配事务id，当前最大事务id+1 |
| creator_trx_id | readview创建者的事务id         |

版本链数据访问规则

trx_id：代表的是当前事务id

1. trx_id == creator_trx_id 可以访问该版本 --->成立，说明数据是当前事务更改的
2. trx_id < min_trx_id 可以访问该版本 --->成立，说明事务已经提交了，因为他都小于最小活跃id了
3. trx_id > max_trx_id 不可访问该版本------>成立在readview生成之后开启的
4. min_trx_id <= trx_id <= max_trx_id 如果不在这个范围（m_ids）内可以访问该版本----->成立说明数据已经提交了

- read Committed：每次select都生成快照读
- repeatable read：开启事务后第一个select语句才是快照读的地方，后续复用快照读

## mysql管理 

### 系统数据库

==information_schema==：提供了访问数据库元数据的各种表和视图

==performance_schema==：为mysql服务器运行时状态提供了一个底层监控功能，主要用于收集数据库服务器性能参数

==sys==：方便DBA和开发人员利用performance_schema性能数据库进行性能调优和诊断的视图

==mysql==：存储mysql服务器正常运行所需要的各种信息（时区，主从，用户，权限）

常用工具

```mysql
musql -h192.168.121.134 -P3306 -uroot -p123456 db01 -e "select *  form stu";
```

```mysql
mysqladmin --help
```

```mysql
mysqlbinlog [options] log-files log-files2 ..
```

```mysql
二进制日志在 /var/lib/mysql/中
```

```mysql
musqlbinlog -s binlog.000011 显示的简单

```

```mysql
mysqlshow [options][db_name[table_name[col_name]]]
--count 
-i 查看数据库，表，字段状态

mysqlshow -uroot -p1234 --count
mysqlshow -uroot -p1234 db01 --count 就看db01
mysqlshow -uroot -p1234 db01 course id --count 这张表,id这列

```

```mysql
mysqldump 数据库备份，迁移 

```

```mysql

```

