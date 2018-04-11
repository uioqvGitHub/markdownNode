[TOC]



### MyBatis和Hibernate 区别

1. Hibernate 可以自动生成 SQL 对数据库操作; MyBatis 需要手写SQL 语句。
2. Hibernate 封装复杂度高、 学习难度大； MyBatis 简单容易使用。
3. Hibernate 关联映射有很多种类， 比如一对多， 多对一， 一对一， 多对多等 ；   MyBatis 只有两种加载一个记录、 加载多个记录。
4. Hibernate 缓存种类很多， 比如一级， 二级， 查询缓存; MyBatis 只有一种缓存。
5. Hibernate 自动生成SQL ， 自动映射SQL参数 ; MyBatis采用 #{key} 映射SQL参数。