[TOC]



### MyBatis和Hibernate 区别

1. Hibernate 可以自动生成 SQL 对数据库操作; MyBatis 需要手写SQL 语句。
2. Hibernate 封装复杂度高、 学习难度大； MyBatis 简单容易使用。
3. Hibernate 关联映射有很多种类， 比如一对多， 多对一， 一对一， 多对多等 ；   MyBatis 只有两种加载一个记录、 加载多个记录。
4. Hibernate 缓存种类很多， 比如一级， 二级， 查询缓存; MyBatis 只有一种缓存。
5. Hibernate 自动生成SQL ， 自动映射SQL参数 ; MyBatis采用 #{key} 映射SQL参数。

### 添加操作

​	需要提交事务

```java
Session session = HibernateUtils.getSession();
Transaction tx = session.beginTransaction();
session.save(dept);
tx.commit();
session.close();
```

#### 主键生成策略

1. identity

   交给数据库自动生成， 适合MySql和SQL Server 

   > 建表时需要追加自动增长设置

   ```xml
   <id name="no" colum="deptno" type="integer">
   	<geterator class="identity"></geterator>
   </id>
   ```

2. sequence

   采用指定序列生成主键值，适合Oracle 和DB2

   ```xml
   <id name="no" colum="deptno" type="integer">
   	<geterator class="sequence">
       	<param name="sequence_name">xxx</param>
       </geterator>
   </id>
   ```

3. increment

   先查询主键最大值， 然后加1再插入， 适合各种数据库。

   ```xml
   <id name="no" colum="deptno" type="integer">
   	<geterator class="increment"></geterator>
   </id>
   ```

4. uuid

   采用uuid算法生成一个字符串类型主键值

   > 表主键字符类型为varchar

   ```xml
   <id name="no" colum="deptno" type="integer">
   	<geterator class="uuid"></geterator>
   </id>
   ```

5. native 

   会根据方言自动的 使用identity  和sequence 等

6. assigned

   忽略， 需要按程序员指定的id值进行添加 （默认）

7. 自定义主键生成器

   - 实现 IdentiferGenerator接口
   - 配置生成器

   ​