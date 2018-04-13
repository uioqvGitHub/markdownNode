[TOC]

## 第三章 对于所有对象都通用的方法

​	尽管Object是一个具体类， 但是设计它主要是为了扩展。它所有的非final方法（equals、hashCode、toString、clone和finalize） 都了有明确的通用约定。 因为它们被设计成是要被覆盖的。

### 第8条： 覆盖equals时请遵守通用约定

#### 不实现equals方法

​	有许多覆盖方式会导致错误， 并且后果非常严重。 最容易避免这类问题的办法就是不覆盖equals方法， 在这种情况下，类的每个实例都只与它自身相等。 

1. 类的每个实例本质上都是唯一的

   对于代表活动实体而不是值的类。 如`Thread`。`Object`提供的`equals`实现对于这些类来说正是正确的行为。

2. 不关心类是否提供了 ”逻辑相等“ 的测试功能

   例如, `java.util.Random`覆盖了`equals`， 以检查两个`Random`实例是否产生相同的随机数序列， 但是设计者并不认为客户需要或者期望这样的/功能。 这种情况， 从`Object`继承得到的`equals`实现已经足够了。

3. 超类已经覆盖了`equals`， 从超类继承过来的行为对于子类也是合适的。

   例如， 大多数`Set`实现都从`AbstractSet`继承`equals`实现， `List`实现从`AbstractList`继承`equals`实现， `Map`实现从`AbstractMap`继承`equals`实现。

4. 类是私有的或是包权限的， 可以确定它的`equals`方法永远不会被调用。 

   在这种情况下， 应该覆盖equals方法， 以防止它被意外调用：

   ```java
   @Override
   public boolean equals(Object o) {
       throw new AssertionError();
   }
   ```

#### 覆盖`Object.equals`

​	如果类具有自己特有的 “逻辑相等” 概念（不同于对象等同的概念）， 而且超类还没有覆盖equals以实现期望的行为，这通常属于“值类”的情形。

- 在覆盖equals方法的时候， 必须遵守它的通用约定。

  1. 自反性(reflexive)	**对象必须等于其自身**

     对于任何非null 的引用值x, xequals(x) 必须返回true。

  2. 对称性(symmetric)    **任何两个对象必须等于其自身**

    对于任何非null的引用值x和y， 当且仅当y.equals(x) 返回true时, x.equals(y)必须返回true。

  3. 传递性(transitive)     **如果一个对象等于第二个对象， 并且第二个对象又等于第三个对象， 则第一个对象一定等于第三个对象**

    对于任何非null的引用值x、y和z， 如果x.equals(y)返回true， 并且y.equals(z) 也返回true， 那么 x.equals(z) 也必须返回true。

  4. 一致性(consistent)     **如果两个对象相等， 它们就必须始终保持相去**

     对于任何非null的引用值 x 和 y， 只要 equals 的比较操作在对象中所用的信息没有被修改， 多次调用 x.equals(y) 就会一致地返回 true, 或者一致的返回 false

  5. 对于任务非null 的引用值 x, x.equals(null) 必须返回 false.

- 实现高质量equals方法的诀窍：

  1. **使用`==`操作符检查 ”参数是否为这个对象的引用“**。 如果是， 则返回true。

     这只不过是一种性能优化， 如果比较操作有可能很昂贵， 就值得这么做。

  2. **使用`instanceof` 操作符检查 “参数是否为正确的类型”**。 如果不是， 则返回false。

  3. **把参数转换成正确的类型**。

  4. **对于该类中的每个 ”关键“ 域， 检查参数中的域是否与该对象中对应的域相匹配**。

  5. 当编写完成了 equals 方法后， 应该问自己三个问题， 它**是否是对称的、 传递的、 一致的**？

- 注意事项

  1. 覆盖equals时总要覆盖hashCode。
  2. 不要企图让equals方法过于智能。
  3. 不要将equals声明中的Object对象替换为其他类型

### 第9条： 覆盖 equals 时总要覆盖 hashCode

