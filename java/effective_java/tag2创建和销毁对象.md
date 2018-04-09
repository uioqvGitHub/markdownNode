[TOC]

##第二章 创建和销毁对象

### 第一条： 考虑用静态工厂方法代替构造器

##### 静态工厂方法的优势

######1. 静态工厂方法有名称

   	一个类只能有一个带有指定签名的构造器。 而静态工厂方法不受此限制。 当一个类需要多个带有相同签名的构造器时，就用静态工厂方法代替构造器， 并且慎重地选择名称以便突出它们之前的区别。

######2. 不必在每次调用静态工厂方法时都创建一个对象

   	这使得不可变类可以使用预先构建好的实例，或者将构建好的实例缓存起来，进行重复利用，从而避免创建不必要的对象。

		能够为重复的调用返回相同对象， 这样有助于类总能严格控制在某个时刻哪些实例应该存在。 这种类被称为**实例受控的类**。

######3. 可以返回原返回类型的任何子类型的对象

######4. 在创建参数化类型实例的时候， 它们使代码变得更加简洁

   ```java
   Map<String, List<String> m = 
       new HashMap<String, List<String>>();
   //若提供了这个静态工厂
   public static <K, V> HashMap<K, V> newInstance() {
       return new HashMap<K, V>();
   }
   //就可以用下面这名简洁的代码代替上面
   Map<String, List<String>> m = HashMap.newInstance();
   ```

##### 静态工厂方法的缺点

###### 1. 类如果继承不含公有的或受保护的构造器，  就无法被实例化

###### 2. 不像构造器可以在API文档明确标识出来

   	因此，对于提供了静态工厂方法而不是构造器的类来说，想要查明如何实例化一个类，这是非常困难的。

   	通过在类或者接口注释中标出，并遵守标准的**命名**习惯，可以弥补这一劣势。

#####  静态工厂方法的命名习惯

   命名 | 含义
   --- | ---
   valueOf | 类型转换方法
   of | valueOf 的一种更简洁的替代
   getInstance | 返回实例通过方法参数描述
   newInstance | 像getInstance一样，但newInstance能够确保返回的每个实例都与所有其他实例不同
   getType | 像getInstance一样， 但是在工厂方法处理不同的类中的时候使用。Type表示工厂方法所返回的对象类型。
   newType | 像newInstance一样， 但是在工厂方法处理不同的类中的时候使用。Type表示工厂方法所返回的对象类型。

### 第二条： 遇到多个构造器参数时要考虑用构建器

##### 1. 重叠构造器

   	这种模式， 提供第一个只有必要参数的构造器， 第二个构造器有一个可选参数， 第三个有两个可选参数， 依此类推， 最后一个构造器包含所有可选参数。

   ```java
public class NutritionFacts {
    private final int servingSize;	//(mL)				required
    private final int servings;		//(per container)	required
    private final int calories; 	//					optional
    private final int fat;			//(g)				optional
    private final int sodium; 		//(mg)				optional
    private final int carbohydrate;	//(g)				optional

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories) {
        this(seringSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }

    public NutritionFacts(int servingSize, int servings,
                          int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize 	= servingSize;
        this.servings		= servings;
        this.calories		= calories;
        this.fat			= fat;
        this.sodium			= sodium;
        this.carbohydrate	= carbohydrate;
    }
}
   ```

> 仅仅六个参数时，重叠构造器还不算太糟， 但随着参数数目的增加， 很快就会失去控制。

###### 重叠构造器的缺陷

​	当有许多参数的时候， 客户端代码会很难编写， 并且难以阅读

##### 2. JavaBeans 模式

​	遇到许多构造器参数的时候， 还有第二种代替方法， 即 JavaBeans模式， 在这种模式下调用一个无参构造器来创建对象，然后调用setter方法来设置每个必要的参数， 以及每相关的可选参数。

```java
public class NutritionFacts {
    private int ServingSize 	= -1;
    private int servings		= -1;
    private int calories		= 0;
    private int fat				= 0;
    private int sodium			= 0;
    private int carbohydrate	= 0;
    
    public NutritionFacts() { }
    
    public void setServingSize(int val) { servingSize = val; }
    public void setServings(int val)	{ servings = val; }
    public void setCalories(int val)	{ calories = val; }
    public void setFat(int val) 		{ fat = val; }
    public void setSodium(int val)		{ sodium = val; }
    public void setCarohydrate(int val)	{ carbohydrate = val; }
}
```

这种模式弥补了重叠构造器模式的不足。

```java
NutritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCalories(100);
cocaCola.setSodium(35);
cocaCola.setCarbohydrate(27);
```

###### JavaBeans模式的缺点。

1. 因为构造过程被分到了几个调用中，在构造过程中JavaBean可能处于不一致的状态。
2. JavaBeans模式阻止了把类做成不可变的可能， 这就需要程序员付出额外的努力来确保它的线程安全

##### 3. Builder 构建器模式

​	不直接生成想要的对象， 而是让客户端利用所有必要的参数调用构造器（或者静态工厂)， 得到一个**builder对象**。 然后客户端在builder对象上调用类似于setter的方法，来设置每个相关的可靠参数。 最后， 客户端调用无参的**build方法**来生成不可变的对象。 这个builder 是它构建的类的静态成员类。

```java
public class NutritionFacts {
    private final int servingSize;	
    private final int servings;	
    private final int calories; 
    private final int fat;	
    private final int sodium; 
    private final int carbohydrate;	
    
    public static class Builder {
        private final int servingSize;
        private final int servings;
        
        private int calories		= 0;
        private int fat				= 0;
        private int carbohydrate	= 0;
        private int sodium			= 0;
        
        public Builder(int servingSize, int servings) {
            this.servingSize 	= servingSize;
            this.servings		= servings;
        }
        
        public Builder calories(int val) {
            calories = val;
            return this;
        }
        
        public Builder fat(int val) {
            fat = val;
            return this;
        }
        
        public Builder carbohydrate(int val) {
            cabohydrate = val;
            return this;
        }
        
        public Builder sodium(int val) {
            sodium = val;
            return this;
        }
        
        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }
    
    private NutritionFacts(Builder builder) {
        servingSize 	= builder.servingSize;
        servings		= builder.servings;
        calories		= builder.calories;
        fat				= builder.fat;
        sodium			= builder.sodium;
        carbohydrate	= builder.carbohydrate;
    }
}
```

NutritionFacts是不可变的， 所有的默认参数值都单独放在一个地方。 builder的setter方法返回builder本身， 以便可以把调用链接起来。 

```java
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8).calories(100).sodium(35).carbohydrate(27).build();
```

###### Builder 的缺陷

​	为了创建对象， 必须先创建它的构建器。 虽然创建构建器的开销在实践中可能不那么明显， 但是在某些十分注重性能的情况下， 就会成为问题了。

​	Builder模式比重叠构造器模式更加冗长， 因此它只在有很多参数的时候才使用， 比如4个或者更多个参数。

> 如果一开始就使用构造器或者静态工厂， 等到类需要多个参数时才添加构建器，就会无法控制， 那些过时的构造器或者静态工厂显得十分不协调。 因此， 通常最好一开始就使用构建器。

### 第三条： 用私有构造器或者枚举类型强化Singleton属性

​	Singleton 指仅仅被实例化一次的类。 Singleton 通常被用来代表那些本质上唯一的系统组件， 比如窗口管理器或者文件系统。 使类成为Singleton会使它的客户端测试变得十分困难， 因为无法给Singleton替换模拟实现， 除非它实现一个充当其类型的接口。

> 在java 1.5发行版本之前， 实现Singleton有两种方法。

###### 1. 公有静态成员是个final域

   ```java
   public class Elvis {
       public static final Elvis INSTANCE = new Elvis();
       private Elvis() { ... }
       
       public void leaveTheBuilding() { ... }
   } 
   ```

###### 2. 公有的成员是个静态工厂方法

   ```java
   public class Elvis {
       private static final Elvis INSTANCE = new Elvis();
       private Elvis() { ... }
       public static Elvis getInstance() { return INSTANCE; }
       
       public void leaveTheBuilding() { ... }
   }
   ```


   > 为了实现Singleton 类变成是可序列化的 (Serializable)， 仅仅在声明中加上“implements Serializable"是不够的。 为了维护并保证Singleton， 必须声明所有实例域都是瞬时(transient) 的， 并提供一个readResolve方法， 否则， 每次返序列化一个序列化的实例时， 都会创建一个新的实例。

   ```java
   private Object readResolve() {
       return INSTANCE;
   }
   ```

> 从Java 1.5发行版本起，  实现Singleton还有第三种方法。

###### 3. 只需要编写一个包含单个元素的枚举类型：

   ```java
   public enum Elvis {
       INSTANCE;
       
       public void leaveTheBuilding() { ... }
   }
   ```

### 第四条： 通过私有构造器强化不可实例化的能力

​	有时候， 你可能需要编写只包含静态方法和静态域的类。

> 企图通过将类做成抽象类来强制该类不可被实例化， 这是行不通的。该类可以被子类化， 并且该子类也可以被实例化。 这样做甚至会误导用户， 认为这种类是专门为了继承而设计的。

​	因此我们只要让这个类包含私有构造器， 它就不能被实例化了：

```java
public class UtilityClass {
    private UtilityClass() {
        throw new AssertionError();
    }
}
```

​	这种用法也有副作用， 它使得一个类的子类不能被实例化。 所有的构造器都必须显式或隐式地调用超类(superclass) 构造器。

### 第五条： 避免创建不必要的对象

###### 1.重用不可变类对象

​	最好能重用对象而不是在每次需要的时候就创建一个相同功能的新对象， 如果对象是不可变的， 它就始终可以被重用。

> 一个反面例子

```java
String s = new String("sringette");
```

​	该语句每次被执行的时候都创建一个新的String实例， 但是这些创建对象的动作全都 是不必要的。 

> 改进后的版本

```java
String s = "stringette";
```

​	对于同时提供了静态工厂方法和构造器的不可变类， 通常可以使用静态工厂方法而不是构造器， 以避免创建不必要的对象。构造器在每次被调用的时候都会创建一个新的对象， 而静态工厂方法则从来不要求这样做。

```java
Boolean.valueOf(String);
```

###### 2. 重用已知不会被修改的可变类对象

​	除了重用不可变的对象之外， 也可以重用那些已知不会被修改的可变对象。 

> 一个反面例子

```java
public class Person {
    private final Date birthDate;
    
    public boolean isBabyBoomer() {
        Calendar gmtCal = 
            Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
        Date boomStart = gmtCal.getTime();
        getCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
        Date boomEnd = gmtCan.getTime();
        return birthDate.compareTo(boomStart) >= 0 &&
            birthDate.compareTo(boomEnd) < 0;
    }
}
```

​	isBabyBoomer每次被调用的时候， 都会新建一个Calendar、 一个TimeZone和两个Date 实例， 这是不必要的。 

> 改进后的版本

```java
public class Person {
    private final Date birthDate;
    
    private static final Date BOOM_START;
    private static final Date BOOM_END;
    
    static {
        Calendar gmtCal = 
            Calendar.getInstance(TimeZone.getTimeZone("GMT"));
        gmtCal.set(1946, Calendar.JANUARY, 1, 0, 0, 0);
        BOOM_START = gmtCal.getTime();
        gmtCal.set(1965, Calendar.JANUARY, 1, 0, 0, 0);
        BOOM_END = gmtCal.getTime();
    }
    
    public boolean isBabyBoomer() {
        return birthDate.compareTo(BOOM_START) >= 0 &&
            birthDate.compareTo(BOOM_END) < 0;
    }
}
```

​	如果改进后的Person类被初始化了， 它的isBabyBoomer方法却永远不会被调用， 那就没必要初始化BOOM_START 和BOOM_END 域。 通过**延迟初始化**， 即把对这些域的初始化延迟到isBabyBoomer方法第一次被调用的时候进行。

​	 但是不建议这样做， 这样做会使方法的实现更加复杂， 从而无法将性能显著提高到超过已经达到的水平。

###### 3. 不要错误地认为本条目所介绍的内容暗示着”创建对象的代价非常昂贵，我们应该要尽可能地避免创建对象“

​	由于小对象的构造器只做很少量的显式工作， 所以， 小对象的创建和回收动作是非常廉价的。 通过创建附加的对象， 提升程序的清晰性、 简洁性和功能性， 这通常是件好事。

​	反之， 通过维护自己的对象池（object pool) 来避免创建对象并不是一种好的做法， 除非池中的对象是非常**重量级**的。 

> 真正正确使用对象池的典型对象救命就是数据库连接池。建立数据库连接代价是非常昂贵的， 因此征用这些对象非常有意义。
>
> > 但是， 一般而言， 维护自己的对象池必定会把代码弄得很乱， 同时增加内存占用（footprint）， 并且还会损害性能。

### 第六条： 消除过期的对象引用

#### 内存泄漏的常见来源

##### 1. 过期引用

> 一个简单实现栈的例子

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INTIAL_CAPACITY = 16;
    
    public Stack() {
        elements = new Object[DEFAULT_INTIAL_CAPACITY];
    }
    
    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }
    
    public Object pop() {
        if( size == 0 ) 
            throw new EmptyStakException();
        return elements[--size];
    }
    
    private void ensureCapacity() {
        if( elements.length == size )
            elements = Arrays.copyOf(elements, 2*size +1);
    }
}
```

​	这个程序中隐藏着一个问题， 这段程序有一个“内在泄漏”， 如果一个栈先是增长，然后再收缩，那么，从栈中弹出来的对象将不会被当作垃圾回收。 这是因为栈内部维护着对这些对象的过期引用。

​	在本例中， 凡是在elements数组“活动部分”之外的任何引用都是过期的。 活动部分是指elements中下标小于size的那些元素。

- 清除过期引用

> 修复pop的内存泄漏

```java
public Object pop() {
    if( size == 0 ) 
        throw new EmptyStackException();
    Object result = elemets[--size];
    elements[size+1] = null;
    
}
```

​	清除过期引用的另一个好处是， 如果它们以后又被错误地解除引用， 程序就会立即抛出NullPointerException异常， 而不是悄悄地错误运行下去。

> 清空对象引用应该是一种例外， 而不是一种规范行为。 消除过期引用最好的方法是让包含该引用的变量结束其生命周期。 

- 清除过期引用的时机

		Stack 类自己管理内存。 管理一个代表栈的数组，它引用着其他对象。  处理出栈操作时，只是将栈指针下移， 过期元素处理自由状态， 但因为数组对其的引用， 垃圾回收器无法得知。 

> 一般而言， 只要类是自己管理内在， 程序员就应该警惕内在泄漏问题。

##### 2. 缓存

​	一旦把对象引用放到缓存中， 它就很容易被遗忘掉， 可以使用**WeakHashMap**代表缓存， 当缓存中的项过期之后，它们就会自动被删除。

> 只有当缓存项的生命周期是由该键的外部引用而不是值决定时，**WeakHashMap**才有用处。

- 缓存项的生命周期是否有意义

		 随着时间的推移， 其中的项会变得越来越没有价值。在这种情况下， 缓存应该时不时地清除掉没用的项。 

1. 这项清除工作可以由一个后台线程(可能是TImer或者ScheduledThreadPollExcutor) 来完成
2. 也可以在给缓存添加新条目的时候顺便进行清理（可以利用重写LinkedHashMap类的removeEldestEntry方法来实现）

##### 3. 监听器和其他回调

​	如果你实现了一个API， 客户端在这个API中注册回调， 却没有显式地取消注册， 它他们就会积聚。

- 确保回调立即被当作垃圾回收的最佳方法是只保存它们的弱引用(weak reference)

  > 例如， 只将它们保存成WeakHashMap中的键

### 第七条： 避免使用终结方法

#### 终结方法不能保证会被(及时地)执行

​	从一个对象变得不可到达开始，到它的终结方法被执行，所花费的时间是任意长的。

​	`System.gc`和`System.runFinalization`只是增加了终结方法被执行的机会，但是它们并不保证终结方法一定会被执行。

​	唯一声称保证终结方法被执行的方法是`System.runFinalizersOnExit`， 以及`Runtime.runFinalizersOnExit`。这两个方法有致使的缺陷，已经被废弃了

​	使用终结方法有一个在创建和销毁对象上非常严重的性能损失， 

​	如果类的对象中封装的资源（例如文件或者线程）需要终止， 可以**提供一个显示的终止方法**

​	显示终止方法必须在一个私有域中记录下“记对象已经不再有效”。 如果这些方法是在对象已经终止后被调用， 其他的方法就必须检查这个域， 并抛出`IllegalStateException`异常。

​	显示终止方法通常与`try-finally`结构结合起来使用， 以确保及时终止。 在finally子句内部调用显式的终止方法

- 终止方法有两种合法的用途：

  1. 充当安全网

     ​	当对象的所有者忘记调用前面段落中建议的显式终止方法时，终结方法可以充当安全网。

     ​	如果终结方法发现资源还未终止，则应该在日志中记录一条警告， 因为这表示客户端代码中的一个Bug, 应该得到修复。

     ​

