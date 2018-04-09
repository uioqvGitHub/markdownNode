[TOC]

## mongo 

### 数据库

`use xxx;` //创建一个库进入或直接进入库

`db.dropDatabase()` //删除当前库

`show databases` //查看有哪些库

`show dbs` //查看有哪些库

### 集合

1. 删除集合

   `db.dept.drop()`


2. 查看有哪些集合

   `show collections`

   `show tables`

### 添加

`db.xxx.insert(json值)`

### 删除

`db.xxx.remove({})` //删除所有记录

`db.xxx.remove(json值)` //删除指定条件记录

###更新

`db.xxx.update(条件,更新值)`//全部更新

`db.xxx.update(条件,{$set:更新值})` //局部更新

###查询

`db.xxx.find()` //查询所有

`db.xxx.find(json值) `//按条件查询

`db.xxx.find({"dno":{$gt:50}})` //查询dno>50的

其他:`$gt、$gte、$lt、$lte、$ne`

`db.xxx.find({"dname":/java6/}) `//查询dname 包含java6的记录

### 统计数量

`db.dept.count({"dname":/java6/})`



## MongoClient

### 获取MongoCollection

```java
MongoClient mongo = new MongoClient("localhost");
MongoDatabase java16 = mongo.getDatabase("java16");
MongoCollection<Document> dept = java16.getCollection("dept");
```

### 添加

```java
Document doc = new Document();
doc.put("deptno", 10);
doc.put("dname", "java");
dept.insertOne(doc); //insertMany(List<Document> docs);
mongo.close();
```

### 删除

```java
Document doc = new Document();
doc.put("deptno", 10);
dept.deleteOne(doc);//dept.deleteMany(doc);
mongo.close();
```

### 修改

```java
dept.updateMany(Filters.eq("deptno", 10),
                new Document("$set", new Document("dname", "hhjj")));
mongo.close();
```

### 查找

```java
FindIterable<Document> it = dept.find();
for (Document document : it) {
    System.out.println(document);
}
mongo.close();
```

## MongoTemplate

### pom.xml

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

### application.yml 配置

```yaml
spring:
  data:
    mongodb:
      host: 192.168.16.150
      port: 27017
```

### 添加

```java
Dept dept = new Dept();
dept.setDeptno(15);
dept.setDname("java16");
mongo.insert(dept);
```

### 删除

```java
mongo.remove(Query.query(
    Criteria.where("deptno").is(15)),
             Dept.class);
```

### 修改

```java
mongo.updateMulti(Query.query(
    Criteria.where("deptno").is(15)),
                  Update.update("dname", "java111"),
                  Dept.class);
```

### 查找

```java
BasicQuery query = new BasicQuery("{deptno : { $eq : 15}}");
List<Dept> result = mongo.find(query, Dept.class);
for (Dept dept : result) {
    System.out.println(dept);
}

result = mongo.find(Query.query(
    Criteria.where("deptno").is(15)),
                    Dept.class);
for (Dept dept : result) {
    System.out.println(dept);
}
```

