---
title: Mybatis
permalink: /interview/frameworkAndTools/mybatis
key: frameworkAndTools-mybatis
---



## 应用

### 【MyBatis框架的理解】

MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架，其主要就完成2件事情：

1. 封装JDBC操作
2. 利用反射打通Java类与SQL语句之间的相互转换

MyBatis的主要设计目的就是让我们对执行SQL语句时对输入输出的数据管理更加方便，所以方便地写出SQL和方便地获取SQL的执行结果才是MyBatis的核心竞争力。



### 【Mybatis中#和$的区别】

1. #传入的参数在SQL中显示为字符串，#方式能够很大程度防止sql注入；

2. $传入的参数在SqL中直接显示为传入的值，$方式无法防止Sql注入。

   

**总结：**一般能用#的就别用$.



### 【Mybatis的一二级缓存】  

缓存是介于应用程序和物理数据源之间,mybatis提供查询缓存，用于减轻数据压力，提高数据库性能。

- **一级缓存**

  **概念：**

  是sqlSession级别的缓存。在操作数据库时需要构造sqlSession对象，在对象中有一个数据结构(HashMap),用于存储缓存数据。不同的sqlSession之间的缓存区域(HashMap)是互不影响的。

  **使用：**

  Mybatis默认开启了一级缓存

  **原理：**

  缓存存在一个hash表中,通过查询SQL,查询数据库,客户端协议等作为key.在判断是否命中前,MySQL不会解析SQL,而是直接使用SQL去查询缓存,SQL任何字符上的不同,如空格,注释,都会导致缓存不命中.

  1. 服务器接收SQL,以SQL和一些其他条件为key查找缓存表(额外性能消耗)

  2. 如果找到了缓存,则直接返回缓存(性能提升)

  3. 如果没有找到缓存,则执行SQL查询,包括原来的SQL解析,优化等.

  4. 执行完SQL查询结果以后,将SQL查询结果存入缓存表(额外性能消耗)

     

- **二级缓存**

  **概念：**

  是mapper级别的缓存，多个sqlSession去操作同一个Mapper的sql语句，多个SqlSession可以公用二级缓存，二级缓存是跨sqlSession的

  **使用：**

  核心配置文件mybatis-config.xml

  ~~~xml
  <!-- 全局参数的配置 -->
  <settings>
     <!-- 开启二级缓存 -->
     <setting name="cacheEnabled" value="true"/>
  </settings> 
  ~~~

  或者在具体mapper.xml里使用cache标签

  ~~~xml
  <cache/>
  ~~~

  



### 【Mybatis如何进行分页】  

**1、sql分页**

mapper接口

~~~java
List<Student> queryStudentsBySql(Map<String,Object> data);
~~~

xml文件

~~~xml
<select id="queryStudentsBySql" parameterType="map" resultMap="studentmapper">
        select * from student limit #{currIndex} , #{pageSize}
</select>
~~~

service

~~~java
public List<Student> queryStudentsBySql(int currPage, int pageSize) {
    Map<String, Object> data = new HashedMap();
    data.put("currIndex", (currPage-1)*pageSize);
    data.put("pageSize", pageSize);
    return studentMapper.queryStudentsBySql(data);
}
~~~



**2、RowBounds分页**

数据量小时，RowBounds不失为一种好办法。

~~~java
@Override
public List<RoleBean> queryRolesByPage(String roleName, int start, int limit) {
    return roleDao.queryRolesByPage(roleName, new RowBounds(start, limit));
}
~~~





## 原理

### 【MyBatis工作流程原理】 

#### **核心组件**

1. **Configuration**

   MyBatis所有的配置信息都保存在Configuration对象之中，配置文件的大部分配置都会存储到该类中

2. **SqlSession**

   作为MyBatis工作的主要顶层API，表示和数据库交互时的会话，完成必要数据库增删改查功能

3. **Executor**

   MyBatis执行器，是MyBatis 调度的核心，负责SQL语句的生成和查询缓存的维护

   StatementHandler 封装了JDBC Statement操作，负责对JDBC statement 的操作，如设置参数等

4. **ParameterHandler**

   负责对用户传递的参数转换成JDBC Statement 所对应的数据类型

5. **ResultSetHandler**

   负责将JDBC返回的ResultSet结果集对象转换成List类型的集合

6. **TypeHandler**

   负责java数据类型和jdbc数据类型(也可以说是数据表列类型)之间的映射和转换

7. **MappedStatement**

   MappedStatement维护一条<select|update|delete|insert>节点的封装

8. **SqlSource**

   负责根据用户传递的parameterObject，动态地生成SQL语句，将信息封装到BoundSql对象中，并返回

9. **BoundSql**

   表示动态生成的SQL语句以及相应的参数信息

#### **工作原理**

- 第一步通过SqlSessionFactoryBuilder创建SqlSessionFactory
- 第二步通过SqlSessionFactory创建SqlSession
- 第三步通过SqlSession拿到Mapper对象的代理
- 第四步通过MapperProxy调用Maper中相应的方法



### 【XML映射文件和Mapper接口对应，这Mapper接口的原理是什么】  

**源码：**

以查询方法为例：

~~~java
RoleMapper roleMapper = sqlSession.getMapper(RoleMapper.class);
~~~

~~~java
  /**
   * Retrieves a mapper.
   * @param <T> the mapper type
   * @param type Mapper interface class
   * @return a mapper bound to this SqlSession
   */
  <T> T getMapper(Class<T> type);
~~~

该接口的默认实现是`DefaultSqlSession和SqlSessionManager`，这里关注其默认实现DefaultSqlSession

~~~java
//org.apache.ibatis.session.defaults.DefaultSqlSession#getMapper
@Override
public <T> T getMapper(Class<T> type) {
    return configuration.<T>getMapper(type, this);
}

//org.apache.ibatis.session.Configuration#getMapper
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    return mapperRegistry.getMapper(type, sqlSession);
}

//org.apache.ibatis.binding.MapperRegistry#getMapper
@SuppressWarnings("unchecked")
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    final MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory<T>) knownMappers.get(type);
    if (mapperProxyFactory == null) {
        throw new BindingException("Type " + type + " is not known to the MapperRegistry.");
    }
    try {
        //获取实例
        return mapperProxyFactory.newInstance(sqlSession);
    } catch (Exception e) {
        throw new BindingException("Error getting mapper instance. Cause: " + e, e);
    }
}

//org.apache.ibatis.binding.MapperProxyFactory#newInstance(org.apache.ibatis.session.SqlSession)
public T newInstance(SqlSession sqlSession) {
    final MapperProxy<T> mapperProxy = new MapperProxy<T>(sqlSession, mapperInterface, methodCache);
    return newInstance(mapperProxy);
}

//org.apache.ibatis.binding.MapperProxyFactory#newInstance(org.apache.ibatis.binding.MapperProxy<T>)
@SuppressWarnings("unchecked")
protected T newInstance(MapperProxy<T> mapperProxy) {
    //这里的mapperInterface是创建工厂时传入的 Mapper 接口
    return (T) Proxy.newProxyInstance(mapperInterface.getClassLoader(), new Class[] { mapperInterface }, mapperProxy);
}
~~~

**总结**

1. 通过 JDK 动态代理模式，创建 Mapper 接口的代理对象，拦截对接口方法的调用；
2. Mapper 接口中不能使用重载，具体原因参见`org.apache.ibatis.binding.MapperMethod.SqlCommand#SqlCommand`，MyBatis 是通过`mapperInterface.getName() + "." + method.getName()`去获取 xml 中解析出来的 SQL 的，具体可能还要看一下`org.apache.ibatis.session.Configuration#mappedStatements`。



### 【MyBatis是如何将sql执行结果封装为目标对象并返回的】  

**映射方式：**

- 第一种是使用<resultMap>标签，逐一定义列名和对象属性名之间的映射关系。
- 第二种是使用sql列的别名功能，将列别名书 写为对象属性名，比如T_NAME AS NAME，对象属性名一般是name，小写，但是列名不区分大小写，Mybatis会忽略列名大小写，智能找到与之对应对象属性名，你甚至可以写成 T_NAME AS NaMe，Mybatis一样可以正常工作。

**封装：**

​	有了列名与属性名的映射关系后，Mybatis通过**反射**创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。



### 【Mybatis的延迟加载以及实现原理】  

**延迟加载概念：**

> 在真正使用数据的时候才发起查询，不用的时候不查询关联的数据，延迟加载又叫按需查询（懒加载）

立即加载概念：

> 不管用不用，只要一调用方法，马上发起查询。

**使用场景**：

一对多、多对多通常情况下采用延迟加载

**如何使用：**

在MyBatis 的配置文件中通过设置settings的`lazyLoadingEnabled`属性为true进行开启全局的延迟加载，通过aggressiveLazyLoading属性开启立即加载

|         名称          |                             介绍                             |    可选值     |                    默认值                    |
| :-------------------: | :----------------------------------------------------------: | :-----------: | :------------------------------------------: |
|  lazyLoadingEnabled   | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | true \| false |                    false                     |
| aggressiveLazyLoading | 当开启时，任何方法的调用都会加载该对象的所有属性。 否则，每个属性会按需加载（参考 `lazyLoadTriggerMethods`)。 | true \| false | false （在 3.4.1 及之前的版本默认值为 true） |

**实现原理：**

依旧是动态代理