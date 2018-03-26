# java_node
[TOC]
### 集合

#### LinkedHashMap

##### removeEldestEntry 方法

- 参数：
  1. 集合中最旧的条目
- 返回：
  1. true 从Map中移除最旧的条目
  2. 保留集合中最旧的条目

```java
protected boolean removeEldestEntry(Map.Entry<K, V> eldest)
```

1. 如果此映射移除其最旧的条目， 则返回true。 
2. 在将新条目插入到映射后， put 和 putAll 将调用些方法
3. 此方法通常不修改Map， 但使用此方法直接修改Map是允许的， 如果执行了此操作， 则一定返回false。
4. 此实现默认仅返回false

> 示例用法：此重写允许映射增加到 100 个条目，然后每次添加新条目时删除最旧的条目，始终维持 100 个条目的稳定状态

```java
private static final int MAX_ENTRIES = 100;

protected boolean removeEldestEntry(Map.Entry eldest) {
    return size() > MAX_ENTRIES;
}
```



#### WeakHashMap

​	WeakHashMap中对象的生命周期是由键的外部引用决定的， 而不是值

#####不存在 WeakHashSet

> 可以通过**Collections.newSetFromMap(Map<E, Boolean) map)**快速得到一个**WeekHashSet**

```java
Set<Object> seakHashSet = Collections.newSetFromMap(
	new WeakHashMap<Object, Boolean>());
```

> 参数中的Map必需是Empty的。

###cglib与jdk动态代理机制

####jdk动态代理
##### 涉及的类InvocationHandler,Proxy

1. InvocationHandler 
  用来编写具体的代理逻辑

      public Object invoke(Object proxy, Method method,
          Object[] args) throws Throwable;
      //参数:1 代理对象, 2 代理的方法，3 代理方法的参数

2. Proxy
  用来生成代理对象

      Proxy.newProxyInstance(getClass().getClassLoader(),
      	obj.getClass().getInterfaces(), this);
      //参数:1 类加载器, 2 代理接口的数组, 3 InvocationHandler类型的对象具体的代理逻辑

> 注意: 在调用代理方法时, method.invoke(arg1, arg2), 第一个参数应该传入真实对象

##### src
```java
package com.uioqv.test;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;




public class MyJDKProxy {
    static interface UserService {
        boolean login(String username, String password);
    }

    static class UserServiceImpl implements UserService {

        @Override
        public boolean login(String username, String password) {
            if("zhaoyun".equals(username) && "123456".equals(password)) {
                System.out.println("登录成功");
                return true;
            }
            System.out.println("登录失败");
            return false;
        }

    }



    static class MyInvocationHandler<T> implements InvocationHandler {
        T obj;
        @SuppressWarnings("unchecked")
        public T proxy(T obj) {
            this.obj = obj;
            obj = (T) Proxy.newProxyInstance(getClass().getClassLoader(),
                                             obj.getClass().getInterfaces(), this);
            return obj;
        }	

        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            System.out.println("方法执行前");
            Object ret = method.invoke(obj, args);
            System.out.println("方法执行后");
            return ret;
        }


    }

    public static void main(String[] args) {
        UserService userService = new UserServiceImpl();
        System.out.println("-----------------------------------------目标对象执行");
        userService.login("zhaoyun", "123456");

        System.out.println("-----------------------------------------代理对象执行");
        MyInvocationHandler<UserService> myProxy = new MyInvocationHandler<>();
        userService = myProxy.proxy(userService);
        userService.login("zhaoyun", "123456");
    }

}
```
#### cglib动态代理
##### 涉及的类 MethodInterceptor, Enhancer

1. MethodInterceptor 
  用来编写具体的代理逻辑

      public Object intercept(Object proxy, Method method,
          Object[] args, MethodProxy methodProxy) throws Throwable;
      //参数: 1 代理类对象, 2 Method 类型的代理方法对象, 3 代理方法参数, 4 MethodProxy 类型的代理方法对象

2. Enhancer
  用来生成代理对象

      enhancer.setSuperclass(obj.getClass());
      //参数:1 设置要代理的类, 设置代理的类, 继承只可以设置一个, 也可以设置要代理的接口, 可以设置多个
      enhancer.setCallback(this);
      //参数:1 设置回调的MethodInterceptor类型对象
      enhancer.create();
      //创建代理对象
> 注意: 使用cglib需要导入两个jar包cglib与asm, 若出现继承了一个接口的异常, 可能是导错asm的jar包, 应导入org.ow2的asm jar包

##### src
```java
package com.uioqv.test;

import java.lang.reflect.Method;
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

public class MyCglibProxy {
    static interface UserService {
        boolean login(String username, String password);
    }

    static class UserServiceImpl implements UserService {

        @Override
        public boolean login(String username, String password) {
            if("zhaoyun".equals(username) && "123456".equals(password)) {
                System.out.println("登录成功");
                return true;
            }
            System.out.println("登录失败");
            return false;
        }

    }

    static class MyMethodIntercepter<T> implements MethodInterceptor {
        T obj;

        @SuppressWarnings("unchecked")
        public T proxy(T obj) {
            this.obj = obj;
            Enhancer enhancer = new Enhancer();
            enhancer.setSuperclass(obj.getClass());
            enhancer.setCallback(this);
            return (T) enhancer.create();
        }


        @Override
        public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
            System.out.println(method.getClass());
            System.out.println(methodProxy.getClass());
            System.out.println("方法执行之前");
            Object obj = methodProxy.invoke(this.obj, args);

            //Object obj = method.invoke(this.obj, args);
            System.out.println("方法执行之后");
            return obj;
        }



    } 

    public static void main(String[] args) {
        UserService userService = new UserServiceImpl();
        MyMethodIntercepter<UserService> proxy = new MyMethodIntercepter<UserService>();
        System.out.println("-------------------------------------代理前");
        userService.login("zhaoyun", "123456");
        userService = proxy.proxy(userService);
        System.out.println("-------------------------------------代理后");
        userService.login("zhaoyun", "123456");
    }

}

```
### java 的四种引用方式

> 强引用， 软引用， 弱引用， 虚引用

- java中提供这四种引用类型主要有两个目的
  1. 可以主程序员通过代码的方式决定某些对象的生命周期
  2. 有利于JVM进行垃圾回收

#### 强引用

​	是指创建一个对象并把这个对象赋给一个引用变量。

> 示例

```java
Object object = new Object();
String str = "hello";
```

​	强引用有引用变量指向时永远不会被垃圾回收， JVM宁愿抛出OutOfMemory也不会回收这种对象。

​	如果想中断强引用和某个对象之间的关联， 可以显示地将引用赋值为null， 这样JVM会在合适的时间回收该对象。

#### 软引用（SoftReference)

1. 内存空间足够， 垃圾回收器就不会回收它， 
2. 若内存不足， 在JVM抛出OutOfMemoryError之前， 就会回收仅被软引用的对象。
3. 使用SoftReference类所提供的get()方法返回java对象的强引用， 一旦回收了该java对象后 get()方法将返回null。

> 软引用可用来实现内在敏感的调整缓存， 使用引用软引用能防止内存泄露

```java
MyObject aRef = new MyObject();
SoftReference aSoftRef = new SoftRefernce(aRef);
//结束MyObject的强引用
aRef = null;
```

##### 使用ReferenceQueue清除推动了软引用对象的softReference

​	当软引用指向的对象被回收之后， 虽然这个SoftReference 对象的 get() 方法返回null, 但这个SoftReference 对象已经不再具有存在的价值， 需要一个适当的清除机制， 避免大量SoftReference对象带来的内在泄漏。 在java.lang.ref包里还提供了 **ReferenceQueue**。

> ReferenceQueue的使用

```java
ReferenceQueue queue = new ReferenceQueue();
SoftReference ref = new SoftReference(aMyObject, queue);
```

​	当这个**SoftReference**所引用的aMyObject被垃圾回收的同时， ref所强引用的SoftRefertence对象被列入**ReferenceQueue**。

​	**ReferenceQueue**中保存的对象是SoftReference对象， 而且是已经失去了它所引用对象的SoftReference对象。 

​	如果队列为空， 将返回一个**null**， 否则该方法返回队列中前面的一个Reference对象。利用这个方法， 我们可以检查哪个**SoftReference**所引用的对象已经被回收。 于是我们可以把这些失去引用对象的SoftReference对象清除掉

```java
SoftRefeence ref = null;
while((ref = (EmployeeRef) q.poll()) != null) {
    //清除ref
}
```

####  弱引用（WeakReference)

弱引用也是用来描述非必需对象

1. 当JVM进行垃圾回收时， 无论内存是否充足， 都会回收被仅被弱引用的对象
2. 用java.lang.ref.WeakReference类来表示
3. 使用get() 方法返回引用对象， 若被垃圾回收则返回**null**
4. 弱引用也可以与ReferenceQueue配合使用

#### 虚引用(PhantomReference)

1. 如果一个对象与虚引用关联， 就跟没有引用与之关联一样， 在任何时候都可能被垃圾回收
2. 虚引用不影响对象的生命周期
3. 用java.lang.ref.PhantomReference类表示
4. 虚引用必须和ReferenceQueue关联使用

#### ReferenceQueue

1. 当引用指向的对象被垃圾回收时，get()方法会返回null， 此时引用无意义， 使用ReferenceQueue， 当引用指向的对象被垃圾回收时，会加入该队列， 可以清除对get()返回空的无用引用的引用。

