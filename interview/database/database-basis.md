---
title: 数据库基础
permalink: /interview/database/database-basis
key: database-basis
---

## 数据库版本

### 【结构数据库和非结构数据库区别】

- **数据的组织形式:**

  1、SQL的数据都是结构化的,一个体现就是各种数据之间的关系（1对1，1对多等）另一个体现就是数据的定义严格

  2、NOSQL数据库它不需要结构化的数据设计，它的容错性就很强，也不存在太严格的设计，所以以后的扩展和修改都比较容易。并且里面不存在关系这个概念

- **原子操作:**

  1、结构数据库拥有事务处理机制可以支持原子操作

  2、然而在NOSQL数据库中不存在这样的机制

- **效率方面:**

  1、结构化数据库有很多方式可以提高数据的处理效率，比如说创建索引；但是因为结构化数据库天然的追求数据的完整性，所以它在效率方面还是存在一些瓶颈的

  2、NOSQL非结构化数据库就不存在这样的问题，它的写入和查询速度都非常快，尤其是在处理巨量数据的时候，这个优势特别明显。

- **扩展潜力:**

  1、横向扩展是指用多台服务器服务一个数据库，这种扩展的好处就是没有极限。这个对于结构化数据库来说，几乎是不可能的。非结构化数据库就可以做到横向扩展。

  2、纵向扩展是指通过提高硬件性能软件性能来提高整体服务器的性能。这种扩展的劣势就是总会达到极限。当然这种扩展对于结构化数据库和非结构化数据库都是适用的。

**总结：**

1、结构化数据库的目标是追求数据操作的完整性，但是对单机服务器的性能要求比较高。

2、非结构化数据库的设计，追求的是读写的效率和可扩展性，可以实现多机的协作。但是又不注重数据操作的完整性。同时会产生大量的冗余数据。



### 【你了解的非结构数据库有哪些 】

NoSQL数据库的四大分类:

1. **键值存储数据库(Key-Value)**： 

   主要会使用到一个哈希表，这个表中有一个特定的键和一个指针指向特定的数据。Key/value模型对于IT系统来说的优势在于简单、易部署。但是如果DBA只对部分值进行查询或更新的时候，Key/value就显得效率低下了。举例如：**Tokyo Cabinet/Tyrant, Redis, Voldemort, Oracle BDB.**

2. **列存储数据库：** 

   通常是用来应对分布式存储的海量数据。键仍然存在，但是它们的特点是指向了多个列。这些列是由列家族来安排的。如：**Cassandra, HBase, Riak.**

3. **文档型数据库：** 

   它的灵感是来自于Lotus Notes办公软件的，而且它同第一种键值存储相类似。该类型的数据模型是版本化的文档，半结构化的文档以特定的格式存储，比如JSON。文档型数据库可 以看作是键值数据库的升级版，允许之间嵌套键值。而且文档型数据库比键值数据库的查询效率更高。如：**CouchDB, MongoDb**. 国内也有文档型数据库**SequoiaDB**，已经开源。

4. **图形数据库(Graph)**：

   它与其它行列以及刚性结构的SQL数据库不同，它是使用灵活的图形模型，并且能够扩展到多个服务器上。NoSQL数据库没有标准的查询语言(SQL)，因此进行数据库查询需要制定数据模型。许多NoSQL数据库都有REST式的数据接口或者查询API。举例如：**Neo4J**

笔者目前只接触过Redis



### 【MySQL和Oracle数据库有哪些不同】

[原文：](https://blog.csdn.net/Augnita/article/details/95455472)

1. **本质的区别**

   Oracle数据库是一个对象关系数据库管理系统（ORDBMS）。它通常被称为Oracle RDBMS或简称为Oracle，是一个收费的数据库。

   MySQL是一个开源的关系数据库管理系统（RDBMS）。它是世界上使用最多的RDBMS，作为服务器运行，提供对多个数据库的多用户访问。它是一个开源、免费的数据库。

2. **数据库安全性**

   MySQL使用三个参数来验证用户，即用户名，密码和位置；Oracle使用了许多安全功能，如用户名，密码，配置文件，本地身份验证，外部身份验证，高级安全增强功能等。

3. **SQL语法的区别**

   Oracle的SQL语法与MySQL有很大不同。Oracle为称为PL / SQL的编程语言提供了更大的灵活性。Oracle的SQL * Plus工具提供了比MySQL更多的命令，用于生成报表输出和变量定义。

4. **存储上的区别：**

   与Oracle相比，MySQL没有表空间，角色管理，快照，同义词和包以及自动存储管理。

5. **对象名称的区别：**

   虽然某些模式对象名称在Oracle和MySQL中都不区分大小写，例如列，存储过程，索引等。但在某些情况下，两个数据库之间的区分大小写是不同的。

   Oracle对所有对象名称都不区分大小写；而某些MySQL对象名称（如数据库和表）区分大小写（取决于底层操作系统）。

6. **运行程序和外部程序支持：**

   Oracle数据库支持从数据库内部编写，编译和执行的几种编程语言。此外，为了传输数据，Oracle数据库使用XML。

   MySQL不支持在系统内执行其他语言，也不支持XML。

7. **MySQL和Oracle的字符数据类型比较：**

   两个数据库中支持的字符类型存在一些差异。对于字符类型，MySQL具有CHAR和VARCHAR，最大长度允许为65,535字节（CHAR最多可以为255字节，VARCHAR为65.535字节）。

   而，Oracle支持四种字符类型，即CHAR，NCHAR，VARCHAR2和NVARCHAR2; 所有四种字符类型都需要至少1个字节长; CHAR和NCHAR最大可以是2000个字节，NVARCHAR2和VARCHAR2的最大限制是4000个字节。可能会在最新版本中进行扩展。

8. **MySQL和Oracle的额外功能比较：**

   MySQL数据库不支持其服务器上的任何功能，如Audit Vault。另一方面，Oracle支持其数据库服务器上的几个扩展和程序，例如Active Data Guard，Audit Vault，Partitioning和Data Mining等。

9. **临时表的区别：**

   Oracle和MySQL以不同方式处理临时表。

   在MySQL中，临时表是仅对当前用户会话可见的数据库对象，并且一旦会话结束，这些表将自动删除。

   Oracle中临时表的定义与MySQL略有不同，因为临时表一旦创建就会存在，直到它们被显式删除，并且对具有适当权限的所有会话都可见。但是，临时表中的数据仅对将数据插入表中的用户会话可见，并且数据可能在事务或用户会话期间持续存在。

10. **MySQL和Oracle中的备份类型：**

    Oracle提供不同类型的备份工具，如冷备份，热备份，导出，导入，数据泵。Oracle提供了最流行的称为Recovery Manager（RMAN）的备份实用程序。使用RMAN，我们可以使用极少的命令或存储脚本自动化我们的备份调度和恢复数据库。

    MySQL有mysqldump和mysqlhotcopy备份工具。在MySQL中没有像RMAN这样的实用程序。

11. **Oracle和MySQL的数据库管理：**

    在数据库管理部分，Oracle DBA比MySQL DBA更有收益。与MySQL相比，Oracle DBA有很多可用的范围。

12. **数据库的认证：**

    MySQL认证比Oracle认证更容易。

    与Oracle（设置为使用数据库身份验证时）和大多数仅使用用户名和密码对用户进行身份验证的其他数据库不同，MySQL在对用户进行身份验证location时会使用其他参数。此location参数通常是主机名，IP地址或通配符。

    使用此附加参数，MySQL可以进一步将用户对数据库的访问限制为域中的特定主机或主机。此外，这还允许根据进行连接的主机为用户强制实施不同的密码和权限集。因此，从abc.com登录的用户scott可能与从xyz.com登录的用户scott相同或不同。



### 【你使用的MySQL版本，和之前版本的区别 】

​	笔者使用的版本是MySQL5.7

​	版本区别很难比较，推荐看：https://blog.csdn.net/eagle89/article/details/80936899



### 【MySQL中的UTF-8和mb4的区别】

**总结：**

要在 Mysql 中保存 4 字节长度的 UTF-8 字符，需要使用 utf8mb4 字符集;

**区别：**

三个字节的 UTF-8 最大能编码的 Unicode 字符是 0xffff，也就是 Unicode 中的基本多文种平面(BMP)。也就是说，任何不在基本多文本平面的 Unicode字符，都无法使用 Mysql 的 utf8 字符集存储。包括 **Emoji** 表情(Emoji 是一种特殊的 Unicode 编码，常见于 ios 和 android 手机上)，和很多不常用的汉字，以及任何新增的 Unicode 字符等等。

##    

## 范式  

### 【数据库的范式】  

一共有如下几种范式：

- 第一范式（1NF）
- 第二范式（2NF）
- 第三范式（3NF）
- 巴斯-科德范式（BCNF）
- 第四范式(4NF）
- 第五范式（5NF，又称完美范式）

一般要求数据库设计至少满足第三范式



### 【讲一下三大范式】  

- 第一范式：

  **概念：**

  当关系模式R的所有属性都不能在分解为更基本的数据单位时，称R是满足第一范式的，简记为1NF。满足第一范式是关系模式规范化的最低要求，否则，将有很多基本操作在这样的关系模式中实现不了。

  **简单解释：**

  数据库中的每一列都是不可分割的基本数据项，同一列中不能有多个值（每个字段是原子级别的）

  

- 第二范式：

  **概念：**

  如果关系模式R满足第一范式，并且R得所有非主属性都完全依赖于R的每一个候选关键属性，称R满足第二范式，简记为2NF。

  **简单解释：**

  属性完全依赖于主键（主键唯一）

  

- 第三范式：

  **概念：**

  设R是一个满足第一范式条件的关系模式，X是R的任意属性集，如果X非传递依赖于R的任意一个候选关键字，称R满足第三范式，简记为3NF。

  **简单解释：**

  一个数据库表中不包含已在其他表中已包含的非主关键字信息





## SQL语法

### 【SQL的语法】

~~~sql
(1) SELECT (2)DISTINCT<select_list>
(3) FROM <left_table>
(4) <join_type> JOIN <right_table>
(5)         ON <join_condition>
(6) WHERE <where_condition>
(7) GROUP BY <group_by_list>
(8) WITH {CUBE|ROLLUP}
(9) HAVING <having_condition>
(10) ORDER BY <order_by_condition>
(11) LIMIT <limit_number>
~~~



### 【SQL各关键字执行顺序】

~~~sql
(8) SELECT (9)DISTINCT<select_list>
(1) FROM <left_table>
(3) <join_type> JOIN <right_table>
(2)         ON <join_condition>
(4) WHERE <where_condition>
(5) GROUP BY <group_by_list>
(6) WITH {CUBE|ROLLUP}
(7) HAVING <having_condition>
(10) ORDER BY <order_by_list>
(11) LIMIT <limit_number>
~~~

1. FROM:对FROM子句中的左表和右表执行笛卡儿积，产生虚拟表VT1;
2. ON: 对虚拟表VT1进行ON筛选，只有那些符合的行才被插入虚拟表VT2;
3. JOIN: 如果指定了OUTER JOIN(如LEFT OUTER JOIN、RIGHT OUTER JOIN)，那么保留表中未匹配的行作为外部行添加到虚拟表VT2，产生虚拟表VT3。如果FROM子句包含两个以上的表，则对上一个连接生成的结果表VT3和下一个表重复执行步骤1~步骤3，直到处理完所有的表;
4. WHERE: 对虚拟表VT3应用WHERE过滤条件，只有符合的记录才会被插入虚拟表VT4;
5. GROUP By: 根据GROUP BY子句中的列，对VT4中的记录进行分组操作，产生VT5;
6. CUBE|ROllUP: 对VT5进行CUBE或ROLLUP操作，产生表VT6;
7. HAVING: 对虚拟表VT6应用HAVING过滤器，只有符合的记录才会被插入到VT7;
8. SELECT: 第二次执行SELECT操作，选择指定的列，插入到虚拟表VT8中;
9. DISTINCT: 去除重复，产生虚拟表VT9;
10. ORDER BY: 将虚拟表VT9中的记录按照进行排序操作，产生虚拟表VT10;
11. LIMIT: 取出指定街行的记录，产生虚拟表VT11，并返回给查询用户`



### 【where和having的区别】   

- **用的地方不一样**

  where可以用于select、update、delete和insert into values(select * from table where ..)语句中。

  having只能用于select语句中

- **执行的顺序不一样**

  where的搜索条件是在执行语句进行分组之前应用

  having的搜索条件是在分组条件后执行的

  即如果where和having一起用时，where会先执行，having后执行

- **子句有区别**

  where子句中的条件表达式having都可以跟，而having子句中的有些表达式where不可以跟；having子句可以用集合函数（sum、count、avg、max和min），而where子句不可以。

**总结**

1. WHERE 子句用来筛选 FROM 子句中指定的操作所产生的行。
2. GROUP BY 子句用来分组 WHERE 子句的输出。
3. HAVING 子句用来从分组的结果中筛选行



### 【Inner/Left/Right/Full  Join的区别】  

1. **inner join（内连接）**

   在两张表进行连接查询时，只保留两张表中完全匹配的结果集。

2. **left join（左外连接）**

   在两张表进行连接查询时，会返回左表所有的行，即使在右表中没有匹配的记录。

3. **right join（右外连接）**

   在两张表进行连接查询时，会返回右表所有的行，即使在左表中没有匹配的记录。

4. **full join（全连接）**

   在两张表进行连接查询时，返回左表和右表中所有没有匹配的行。



## SQL实践

### 【给定一个表，其中有三列（员工名称，工资，部门号），找出每个部门工资最高的员工】

1、group by分组以后select只能选择group by的字段以及函数字段，无法选择其他字段，这是可以使用`Group_Concat()`实现

~~~sql
select dept_no,Max(salary) as salary,GROUP_CONCAT(userName)
from dept
group by dept_no
~~~

2、left join

~~~sql
select userName from(
select dept_no,Max(salary) as salary
from dept
group by dept_no) as a
left join dept as b
on a.dept_no = b.dept_no
~~~

###   

### 【学生表 Student (S#,Sname,Sage,Ssex)，课程表 Course (C#,Cname)，成绩表SC (S#,C#,score)，查询平均成绩大于 60 分的同学的学号和平均成绩】

~~~sql
SELECT s.Sname,s.Sid,a.score FROM 
(
SELECT AVG(Score) as score,sc.Sid
from Score sc 
LEFT JOIN Course c
on sc.Sid = c.cid
GROUP BY sc.Sid
) as a
INNER JOIN Student s
on s.Sid = a.Sid
where a.score > 60
~~~

思路：首先利用成绩表关联课程表然后按照Sid进行分组，即可得到Sid的平均成绩，然后把其作为临时表关联学生表，选择其他字段，最后加上过滤条件 >60分



### 【一个SQL的TOP N问题】

这题可以参照上题，在上题的基础上加上 `order by` 进行排序，然后用limit 进行筛选出 TOP N