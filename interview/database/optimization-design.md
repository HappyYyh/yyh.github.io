---
title: 调优与设计
permalink: /interview/database/optimization-design
key: optimization-design
---

## 性能调优

### 【说一下数据库优化思路】 

1. **建立索引**

   慢SQL优化，建立索引

2. **分库、分表、分区**

3. **预处理**

   -实时数据放入缓存中

   -历史数据，将复杂sql语句执行出来的结果生成视图，查询的时候直接查询视图，速率显著提高。

4. **读写分离**

5. **增加服务器内存、CPU及网络带宽**  



### 【MySQL慢语句调优】

1、**查看慢查询**

~~~sql
show variables like '%slow%';
show global status like '%slow%';
~~~

2、**利用EXPAIN分析慢语句**

EXPAIN出现的字段解释：

- id:选择标识符;
- select_type:表示查询的类型;
- table:输出结果集的表;
- partitions:匹配的分区;
- type:表示表的连接类型;
- possible_keys:表示查询时，可能使用的索引;
- key:表示实际使用的索引;
- key_len:索引字段的长度;
- ref:列与索引的比较;
- rows:扫描出的行数(估算的行数);
- filtered:按表条件过滤的行百分比;
- Extra:执行情况的描述和说明;



### 【数据库压力很大怎么解决】

1. **加缓存，热点数据放入缓存中**
2. **加机器，分表分库、读写分离；**



### 【数据库的大表查询优化了解吗】

1、对查询进行优化，要尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。

2、应尽量避免在 where 子句中使用`null、 != 或 <>、or、in 和 not in`参数、函数，模糊查询like，否则将导致引擎放弃使用索引而进行全表扫描



### 【数据库宕机后恢复的过程】

主要使用回退段进行恢复，每次提交事务的时候，日志里会记录一条，同时回退段内会记录事务前和事务后的数据变化。当宕机后，数据库开始回退事务，数据从回退段取，保证数据的一致性




## 设计

### 【平常业务中的数据都怎么存储的】

PostgreSQL数据库存储数据记录、Redis作为缓存、利用三方插件存储文件(这里都放在阿里云服务器上)



### 【在建表的时候应该注意什么】

1. 大数据字段最好剥离出单独的表，以便影响性能

2. 使用varchar，代替char，这是因为varchar会动态分配长度，char指定为20，即时你存储字符“1”，它依然是20的长度

3. 给表建立主键，看到好多表没主键，这在查询和索引定义上将有一定的影响

4. 避免表字段运行为null，如果不知道添加什么值，建议设置默认值，特别int类型，比如默认值为0，在索引查询上，效率立显。

5. 建立索引，聚集索引则意味着数据的物理存储顺序，最好在唯一的，非空的字段上建立，其它索引也不是越多越好，索引在查询上优势显著，在频繁更新数据的字段上建立聚集索引，后果很严重，插入更新相当忙。

6. 组合索引和单索引的建立，要考虑查询实际和具体模式.



### 【SQL注入的概念以及怎么预防】 

**概念：**

> SQL注入是一种将SQL代码添加到输入参数中，传递到SQL服务器解析并执行的一种攻击手法

**如何预防SQL注入：**

- 严格检查输入格式的类型和格式（数据强校验 id）
- 过滤或者转义特殊字符
- 不要使用动态拼装sql
- 使用mybatis的时候使用#{}占位符代替${}



### 【数据库分页查询，如何分页，怎么实现】 

当数据量过大时，不可能直接把所有数据都展示出来，这样会加重服务器压力乃至崩溃，对于不同的数据库分页略有不同：

MySql（第一个参数是页码，第二个参数是取多少个记录）

~~~sql
select * from tbl_user limit 1,10//从第一页查询，查10条
~~~

Oracle(rownum不能使用>,所以用法是把其变成变量进行查询)

~~~sql
select * from (select rownum r,t.* from tbl_user t) tt where tt.r between 1 and 10;
~~~

PostgreSQL（第一个参数是取多少个，第二个参数是起点）

~~~sql
select * from persons limit  10  offset 0;//取10条记录
~~~



### 【如何自定义数据库连接池？如何保证连接存活，意义是什么】     

编写连接池需要实现 `javax.sql.DataSource` 接口。

~~~java
public class MyDataSource implements javax.sql.DataSource {

    private static LinkedList<Connection> connections = new LinkedList<>();

    static {
        try {
            //初始化10个连接出来
            for (int i = 0; i < 10; i++) {
                connections.add(new MyConnection(JdbcUtils.getConnection()));
            }
        } catch (Exception e) {
            e.printStackTrace();
            throw new ExceptionInInitializerError(e);
        }
    }

    private static MyDataSource myDataSource = new MyDataSource();

    public static MyDataSource getDataSource() {
        return myDataSource;
    }

    private MyDataSource() {
    }

    @Override
    public Connection getConnection() throws SQLException {
        if (connections.size() < 1) {
            throw new SQLException("数据库连接正忙，请稍后再试...");
        }
        return connections.removeFirst();
    }
    ....
    ....
    ....

    // 包装器类 ，对MyDataSource类 进行增强
    static class MyConnection implements Connection {
        private Connection connection;
        
        public MyConnection(Connection connection) {
            this.connection = connection;
        }

    	// 其他方法，直接调用 connection的方法。。
        @Override
        public Statement createStatement() throws SQLException {
            return connection.createStatement();
        }

        @Override
        public PreparedStatement prepareStatement(String sql) throws SQLException {
            return connection.prepareStatement(sql);
        }
    ....

    //  重点关注 close方法，对其进行增强，不将连接返回给数据库
   		@Override
        public void close() throws SQLException {
            System.out.println("连接池中的剩余连接量："+connections.size());
            connections.add(this) ;
            System.out.println("成功拦截关闭！将它们返给连接池。。");

            System.out.println("连接池中的剩余连接量："+connections.size());
        }
    ....
    }   
}
~~~

**保证连接存活：**

专门线程负责定时检查每个连接是否存活，将死链移除；还有线程负责创建连接，如果连接池有效连接数量不够，就会创建连接。



### 【分布式数据库的实现】

分布式数据库概念：https://www.jianshu.com/p/a8e46880b695

**DDBS(Distributed Database System)的分类**

1. 同构同质型DDBS：各个场地都采用同一类型的数据模型（譬如都是关系型），并且是同一型号的DBMS。
2. 同构异质型DDBS：各个场地采用同一类型的数据模型，但是DBMS的型号不同，譬如DB2、ORACLE、SYBASE、SQL Server等。
3. 异构型DDBS：各个场地的数据模型的型号不同，甚至类型也不同。随着计算机网络技术的发展，异种机联网问题已经得到较好的解决，此时依靠异构型DDBS就能存取全网中各种异构局部库中的数据。

**实现方式：**

进行主从复制（利用Mycat等中间件）

参考：https://www.jianshu.com/p/4503fe0ace87



### 【分布式数据库如何保证数据可靠性】  

1. **Redo Log**

2. **主从热备**

3. **备份/恢复**

4. **存储层数据校验**

   

### 【如何实现数据库动态扩容】

背景：

> 随着互联网的数据量越来越大，很多单表的数据量已经上亿了，甚至更多，这样单表的数据已经达到了查询的瓶颈，那么就需要将数据库进行拆分。
>
> 如何有效的进行数据库拆分呢，而且在互联网公司停机进行数据库处理不是很现实，因为影响了业务量。那么就需要更好的方法去进行解决（数据库动态扩容）。

解决方案：https://www.cnblogs.com/huangqingshi/p/11260249.html



### 【数据库分库分表】

​	关系型数据库本身比较容易成为系统瓶颈，所以需要分布式数据库；数据库分布式核心内容无非就是数据切分,数据切分根据其切分类型，可以分为两种方式：**垂直**（纵向）切分和**水平**（横向）切分

- **垂直（纵向）切分**

  **介绍：**

  垂直分库：根据业务耦合性，将关联度低的不同表存储在不同的数据库

  垂直分表：是基于数据库中的"列"进行，某个表字段较多，可以新建一张扩展表，将不经常用或字段长度较大的字段拆分出去到扩展表中

  **优点：**

  - 解决业务系统层面的耦合，业务清晰
  - 与微服务的治理类似，也能对不同业务的数据进行分级管理、维护、监控、扩展等
  - 高并发场景下，垂直切分一定程度的提升IO、数据库连接数、单机硬件资源的瓶颈

  **缺点：**

  - 部分表无法join，只能通过接口聚合方式解决，提升了开发的复杂度
  - 分布式事务处理复杂
  - 依然存在单表数据量过大的问题（需要水平切分）

  

- **水平（横向）切分**

  **介绍：**

  有库内分表与分库分表：根据表内数据内在的逻辑关系，将同一个表按不同的条件分散到多个数据库或多个表中，每个表中只包含一部分数据，从而使得单个表的数据量变小

  **优点：**

  - 不存在单库数据量过大、高并发的性能瓶颈，提升系统稳定性和负载能力
  - 应用端改造较小，不需要拆分业务模块

  **缺点：**

  - 跨分片的事务一致性难以保证
  - 跨库的join关联查询性能较差
  - 数据多次扩展难度和维护量极大

**切分时机**：

- 数据量过大，正常运维影响业务访问
- 随着业务发展，需要对某些字段垂直拆分
- 数据量快速增长
- 安全性和可用性

**分库分表中间件：**

- sharding-jdbc（当当）
- TSharding（蘑菇街）
- Atlas（奇虎360）
- Cobar（阿里巴巴）
- MyCAT（基于Cobar）
- Oceanus（58同城）
- Vitess（谷歌）



### 【对分库分表，避免热点是怎么处理的】  

**热点的含义：**对业务的操作集中到1个表中，其他表的操作很少。

**分库分表方案**：

1.  常用的方案：hash取模、range范围方案，分库分表方案最主要的就是路由算法，把路由的key按照指定的算法进行路由存放。        

2. **hash取模方案（无热点问题，扩容困难）**

   （一）首先预估一下总数据量m，设定每张表的最大数据量n，m/n=z 是表个数。
   （二）hash方案就是对指定的路由key（如：id）对z进行取模，"t_order_"+id%z 是表名字。
   （三）优缺点：优点是数据均匀的分布在每张表中，不会有热点问题；缺点是数据的迁移或者扩容会很麻烦。

3. **range范围方案（不需要迁移数据，有热点问题）**
   （一）range方案是以范围进行数据拆分：id=50在0~1000的在t_order_0表、id=1050在1000～2000的t_order_1表等。
   （二）优缺点：优点是扩容方便，不需要数据迁移；缺点是id在0-1000时，t_order_0会很忙，t_order_1，t_order_2...t_order_n...都没有数据的访问，一段时间只有一个热点表。

4. **range和hash组合方案**

   设计是比较简单的，就三张表，把group，db,table之间建立好关联关系。



### 【分库分表如何排序】

1、排序后合并再排序（性能较差）

2、在表中加个创建时间作为辅助字段，然后分页的数据根据创建时间进行排序，然后取出符合条件的最前面n条即可



### 【MySQL的读写分离、主从复制】

- **概念**：

  将数据库分为了主从库，一个主库用于写数据，多个从库完成读数据的操作。

- **原因**：

  因为数据库的“写”（写10000条数据到oracle可能要3分钟）操作是比较耗时的。

  但是数据库的“读”（从oracle读10000条数据可能只要5秒钟）

- **作用**：

  读写分离是用来**解决数据库的读性能瓶颈的**



### 【如何实现主从数据库间的同步】

- 1、**半同步复制**

  等主从同步完成之后，等主库上的写请求再返回

  MySQL的Replication默认是一个异步复制的过程，从MySQL5.5开始，MySQL以插件的形式支持半同步复制

- 2、**数据库中间件**

  **流程：**

  - 所有的读写都走数据库中间件，通常情况下，写请求路由到主库，读请求路由到从库

  - 记录所有路由到写库的key，在主从同步时间窗口内（假设是500ms），如果有读请求访问中间件，此时有可能从库还是旧数据，就把这个key上的读请求路由到主库。

  - 在主从同步时间过完后，对应key的读请求继续路由到从库。

  **相关的中间件有：**

  - canal:是阿里巴巴旗下的一款开源项目，纯Java开发,基于数据库增量日志解析，提供增量数据订阅&消费，目前主要支持了MySQL。

  - otter：也是阿里开源的一个分布式数据库同步系统，尤其是在跨机房数据库同步方面，有很强大的功能。它是基于数据库增量日志解析，实时将数据同步到本机房或跨机房的mysql/oracle数据库。



### 【数据库一致性方案】  

即主从同步，参照上题