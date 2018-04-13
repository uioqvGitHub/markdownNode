执行原始的sql语句。

查询所有记录

```java
@Test
public void test14(){
    String sql = "select deptno,dname,loc from dept";
    Session session = HibernateUtil.getSession();
    NativeQuery<Dept> query = session.createNativeQuery(sql,Dept.class);
    List<Dept> list = query.getResultList();
    for(Dept dept:list){
        System.out.println(dept.getNo()+" "+dept.getName()+" "+dept.getLoc());
    }
    session.close();
}
```

带条件参数的查询

```java
@Test
public void test15(){
    String sql = "select deptno,dname,loc from dept where dname like :name";
    Session session = HibernateUtil.getSession();
    NativeQuery<Dept> query = session.createNativeQuery(sql,Dept.class);
    query.setParameter("name", "%java%");
    List<Dept> list = query.getResultList();
    for(Dept dept:list){
        System.out.println(dept.getNo()+" "+dept.getName()+" "+dept.getLoc());
    }
    session.close();
}
```



###注解映射描述

在实体类中使用注解替代hbm.xml描述映射信息.

1. 根据表编写实体类，追加注解标记

   ```java
   @Entity//实体
   @Table(name="EMP")//对应EMP表
   public class Emp implements Serializable{

       @Id
       @GeneratedValue(strategy=GenerationType.IDENTITY)
       @Column(name="ID")
       private int id;

       @Column(name="ENAME")
       private String ename;

       @Column(name="SEX")
       private String sex;

       @Column(name="AGE")
       private int age;

       @Column(name="BIRTHDAY")
       private Date birthday;

       //省略set和get方法

   }
   ```

2. 在hibernate.cfg.xml加载实体类

   ```xml
   <mapping class="cn.xdl.entity.Emp"/> 
   ```

### 关联映射

关联映射分为一对多、多对一、一对一、多对多等，分别使用<one-to-many>、<many-to-one>、<one-to-one>、<many-to-many>标记定义映射。

Dept --> DEPT (1、主键)

Emp --> EMP (n、主键、外键)

1. 案例：查询某个部门及其员工信息

   - 在Dept类追加emps集合属性

     ```
     private Set<Emp> emps;//通过关联映射加载emp员工
     ```

   - 在Dept映射描述中指定<one-to-many>

     ```
     <set name="emps">
         <!-- 指定外键字段名 -->
         <key column="deptno"></key>
         <!-- 指定关联映射对象 -->
         <one-to-many class="cn.xdl.entity.Emp"/>
     </set>
     ```

   - 编程

     ```
     @Test//多次sql单表抓取
     public void test1(){
         Session session = HibernateUtil.getSession();
         Dept dept = session.get(Dept.class, 22);
         System.out.println(dept.getNo()+" "+dept.getName()+" "+dept.getLoc());
         Set<Emp> emps = dept.getEmps();
         for(Emp emp:emps){
             System.out.println(emp.getId()+" "+emp.getEname());
         }
         session.close();
     }

     @Test//一次sql联合抓取
     public void test2(){
         Session session = HibernateUtil.getSession();
         //join fetch
         Query<Dept> query = session.createQuery(
             "from Dept d join fetch d.emps where d.no=?");
         query.setParameter(0, 20);
         Dept dept = query.uniqueResult();
         System.out.println(dept.getNo()+" "+dept.getName()+" "+dept.getLoc());
         Set<Emp> emps = dept.getEmps();
         for(Emp emp:emps){
             System.out.println(emp.getId()+" "+emp.getEname());
         }
         session.close();
     }
     ```