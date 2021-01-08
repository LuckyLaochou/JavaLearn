

# Java基础之Lambda篇

> Hello，大家好！我是老丑，今天给大家分享的是Lambda的相关知识。

在这里，大家可能并不知道Lambda是啥玩意。没有关系，看完这篇推文，相信可以带你认识这位重量级嘉宾——Lambda。

## Demo

我们就以一个线程来进行举例。如果大家对线程不太熟悉的话，大家可以看以下几篇推文来认识它。

1. [Java线程基本操作与概念](https://mp.weixin.qq.com/s/pYZH-4gb7zUF9WyU6UAQyg)
2. [Java中的并发会有什么问题呢？如何解决呢？](https://mp.weixin.qq.com/s/BCFuZRby7puGDuTFYkLDtA)
3. [干货满满的synchronized详解！！！](https://mp.weixin.qq.com/s/cNKnoKtWc1LwdZ-10pfKKw)

目前线程相关只有三篇推文，后续会持续更新。但是我相信大家通过以上三篇文章足够对线程有一定的认识。

### 常规创建

```java
public class CreateThreadCommon {

    public static void createThreadCommon() {
        // 创建一个Runnable对象。大家可能好奇，一个接口为什么可以New，这里的底层知识是匿名内部类。如果不了解的，可以自行了解。
        Runnable r = new Runnable() {
            // 重写 run方法
            @Override
            public void run() {
                System.out.println("Hello FingerDance's Friend !");
            }
        };
        Thread thread = new Thread(r);
        thread.start();
        try {
            thread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        createThreadCommon();
    }

}
```

运行效果

```txt
Hello FingerDance's Friend !
```

一切安好

### Lambda创建

大家如果看不懂Lambda表达式，没关系。在这里先给大家混个眼熟。

```java
public class CreateThreadLambda {

    public static void createThreadLambda() {
        // 以Lambda的方式来创建
        Thread thread = new Thread(() -> System.out.println("Hello FingerDance's Friend !"));
        thread.start();
        try {
            thread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        createThreadLambda();
    }

}
```

运行效果

```txt
Hello FingerDance's Friend !
```

### 对比

从上面两种方式来创建线程，可以发现lambda的代码极其精简。

其中<font color="red">() -> System.out.println("Hello FingerDance's Friend !")</font>就是lambda表达式。下面会细讲Lambda表达式的一些东西的。请耐心看下去。

## Lambda

### Lambda的条件

Lambda使用起来也是需要条件的，你以为你想使用就使用？不存在。

其实在很早之前，有一个很流行的编程模式，<font color="red">函数式编程</font>。而Java的函数式编程一旦离开了Lambda，就像鱼离开了水。

**条件：**

如果想使用Lambda表达式来精简代码的话，**那么这个接口有且仅有一个抽象方法，可以有默认实现**。

大家可以看下，我们之前使用Lambda表达式来表示Runnable的代码。

```java
Thread thread = new Thread(() -> System.out.println("Hello FingerDance's Friend !"));
```

其实是等价于

```java
Runnable r = () -> System.out.println("Hello FingerDance's Friend !");
Thread thread = new Thread(r);
```

小伙伴们可以看到我们的<font color="red">() -> System.out.println("Hello FingerDance's Friend !");</font>表达式是用一个接口进行接收的。

我们来瞧瞧Runnable的接口里面的内容

```java
@FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
```

不知道大家注意到了**@FunctionalInterface**这个注解没有，如果小伙伴不了解注解的话，可以阅读[如何自定义注解](https://mp.weixin.qq.com/s/F9s4SuQDFNZieMBy254RFA) 进行初步了解。

**FunctionalInterface** ，翻译过来就是函数式接口。想满足函数式接口，就必须满足以下要求。

+ 一个接口
+ 有且仅有一个抽象方法（允许有默认方法）

OK，知道**函数式接口**的要求，我们可以看下我们JDK自带的Runnable是否满足呢，很明显满足。因此我们可以使用Lambda表达式来表示。

其实，要求有且仅有一个抽象方法，我不知道大家是否能理解。我们接下来会将。

### Lambda表达形式

Lambda 的通用形式

```java
// 其中 {} 如果是一条语句的话，是可以省略的。
(params ...) -> {
	xxx
}
```

`(params ...)` 表示是方法的参数，在这里可以不写参数类型，因为会做类型推断。

`->` 称之为Lambda运算符，大家可以理解为链接或者指向，指向方法体

`{}` `{}`代表的是方法体，一条语句是无需`{}`的。

**OK，参数有了，方法体有了。链接起来是不是可以作为一个方法呢？当然可以，如果接口的抽象方法唯一的话。** <font color="red">这也是为什么接口中有且仅有一个抽象方法的缘故</font>

**Lambda表达式其实有一些不同形式**，下面会一一列举

1. 不含参数，且方法体只有一条语句

   ```java
   () -> System.out.println("Hello FingerDance's Friend !");
   ```

   ```java
   // 一条语句也可以使用 {} ，但是没有必要。
   () -> {
       System.out.println("Hello FingerDance's Friend !");
   };
   ```

2. 不含参数，且方法体多条语句

   ```java
   // 多条语句就必须使用 {} ，来表示是代码块
   () -> {
   	System.out.println("Hello FingerDance's Friend !");
       System.out.println("Hello FingerDance's Friend !");
   }
   ```

3. 一个参数，且方法体只有一条语句

   ```java
   param -> System.out.println("Hello FingerDance's Friend !");
   ```

   ```java
   // 也可以用()将param括起来，但是没有必要
   (param) -> System.out.println("Hello FingerDance's Friend !");
   ```

   ```java
   // 参数类型显示化（这里必须要使用()来括住参数）
   // 这个参数类型是根据自己的接口定义而定。我这里只是举个列子。
   (Integer param) -> System.out.println("Hello FingerDance's Friend !");
   ```

   ```java
   // 一条语句也可以使用 {} ，但是没有必要。
   param -> {
       System.out.println("Hello FingerDance's Friend !");
   };
   ```

4. 多个参数

   ```java
   (param1, param2, ...) -> {
       System.out.println("Hello FingerDance's Friend !");
       System.out.println("Hello FingerDance's Friend !");
   };
   ```

   ```java
   // 将参数类型显示化
   (Integer param1, Integer param2, ...) -> {
       System.out.println("Hello FingerDance's Friend !");
       System.out.println("Hello FingerDance's Friend !");
   };
   ```

5. 带有返回值

   ```java
   () -> true;
   ```

   ```java
   // 多条语句采用以下
   () -> {
       System.out.println("Hello FingerDance's Friend !");
   	return true;
   }
   ```

## 函数式接口

### JDK中提供的一些函数式接口

我在这里，列举一些JDK中自带的函数式接口

1. **Consumer**

   ```java
   @FunctionalInterface
   public interface Consumer<T> {
   
       void accept(T t);
   
       
       // 注意，这里是允许有默认方法的
       default Consumer<T> andThen(Consumer<? super T> after) {
           Objects.requireNonNull(after);
           return (T t) -> { accept(t); after.accept(t); };
       }
   }
   ```

2. **Supplier**

   ```java
   @FunctionalInterface
   public interface Supplier<T> {
   
       T get();
   }
   ```

3. **Function**

   ```java
   @FunctionalInterface
   public interface Function<T, R> {
   
       
       R apply(T t);
   
       // 默认实现
       default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
           Objects.requireNonNull(before);
           return (V v) -> apply(before.apply(v));
       }
   
       
       default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
           Objects.requireNonNull(after);
           return (T t) -> after.apply(apply(t));
       }
   
      
       static <T> Function<T, T> identity() {
           return t -> t;
       }
   }
   ```

4. **Predicate**

   ```java
   @FunctionalInterface
   public interface Predicate<T> {
   
       boolean test(T t);
       
       default Predicate<T> and(Predicate<? super T> other) {
           Objects.requireNonNull(other);
           return (t) -> test(t) && other.test(t);
       }
       
       default Predicate<T> negate() {
           return (t) -> !test(t);
       }
       
       default Predicate<T> or(Predicate<? super T> other) {
           Objects.requireNonNull(other);
           return (t) -> test(t) || other.test(t);
       }
       
       static <T> Predicate<T> isEqual(Object targetRef) {
           return (null == targetRef)
                   ? Objects::isNull
                   : object -> targetRef.equals(object);
       }
   }
   ```

上面列举了一些**函数式接口**，大家都可以看到使用了**`@FunctionalInterface`**注解。当然大家如果感兴趣的话，可以阅读`java.util.function`包。

### 自定义函数式接口

不管是Runnable接口，还是上述列举的`java.lang.function`包中的接口，都是函数式接口，那么我们如何自定义函数式接口呢？

首先我们得**明确两点**

1. 必须是接口
2. 有且仅有一个抽象方法

**OK，开干**

**自定义函数式接口**

```java
package cn.laochou.lambda.define;

public interface DefineFunctionalInterface {

    boolean isFingerDanceFriend(String name);

}
```

可能小伙伴会问了，为什么接口没有打上**`@FunctionalInterface`**注解，其实如果我们阅读了这个接口，发现这个接口里面也是空空的。JDK会自动认识这是一个函数式接口，但是如果我们打上了**`@FunctionalInterface`**注解，编译器能帮我们做一些校验工作。

**应用**

```java
package cn.laochou.lambda.define;

public class Main {


    public static void main(String[] args) {
        DefineFunctionalInterface defineFunctionalInterface = name -> true;
        System.out.println(defineFunctionalInterface.isFingerDanceFriend("laochou"));
        System.out.println(defineFunctionalInterface.isFingerDanceFriend("you"));
    }

}

```

**结果**

```txt
true
true
```

## 实战

今天带来的实战场景是：

我们书库有一堆书，然后呢，我们有录下它们的名字，想在我们只想学习Java，因此我们需要找到我们Java系列的全部书名。

**函数式接口**

```java
package cn.laochou.lambda.code;

public interface JudgeInterface {

    // 传入书名，返回是否是我们需要的书名
    boolean judge(String name);

}
```

**Main**

```java
package cn.laochou.lambda.code;

import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

public class Main {

    // 书名库
    private static ArrayList<String> names = new ArrayList<>();

    static {
        names.add("Java实战");
        names.add("Python实战");
        names.add("Kotlin实战");
        names.add("Scala实战");
        names.add("Java高并发");
        names.add("Java虚拟机");
    }


    public static List<String> getTargetBooks(String target) {
        JudgeInterface judgeInterface = name -> name.contains(target);
        // stream() 这是Java8引进的流
        // 大家可能阅读这行代码有点困难，我做一个翻译
        // 1. 首先是将 names 集合转化为 Stream流。可以想象下水流。
        // 2. filter 将流中元素进行过滤，如果为true，就进行下一步操作
        	// 其中 filter中的 judgeInterface::judge 是方法引用。
        	// 可以看做 item -> judgeInterface.judge(item)。只是简化了一下
        // 3. collect 收集，将进入到下一步的元素收集起来，并装入我们的List里面。
        return names.stream().filter(judgeInterface::judge).collect(Collectors.toList());
    }


    public static void main(String[] args) {
        List<String> targetBooks = getTargetBooks("Java");
        // System.out::println也是方法引用
        targetBooks.forEach(System.out::println);
    }

}

```

在这里只是列举了一个非常简单的场景，当然我认为这个场景也算是常见的。至于**流式编程**，大家可以观看 **Bilibili的视频**进行了解。

## 建议

说实话，我个人挺喜欢函数式编程的，包括我学过一段时间的Kotlin并写过一些Kotlin程序，里面的函数式编程能把人绕晕。

大家在开发过程中，可能都想自己的代码优美、精简。但是**过多的层级函数式编程并不一定易读**，<font color="red">所以在做开发的时候，是需要保证代码的可读性</font>。因为一份健壮和易读的代码对于开发人员才是一份好代码，因为假设你溜了，别人也好接手啊。

推荐一本函数式编程的书籍给大家。

![函数式编程](http://luckylaochou.gitee.io/photo/Java%E5%9F%BA%E7%A1%80%E4%B9%8BLambda%E7%AF%87/%E5%87%BD%E6%95%B0%E5%BC%8F%E7%BC%96%E7%A8%8B.jpg)

这本书我看完了，但是只看过一篇。后续准备再温故温故。

## 最后

OK，整个推文到这里就基本结束了。

希望小伙伴们看完推文，能够有所收获。

> > 学习路上有FingerDance，不孤单。欢迎加群——进入公众号，菜单栏【关于我们】->【加入我们】！

觉得推文写得还不错的小伙伴，还请点赞、关注、转发支持下。

![](http://luckylaochou.gitee.io/photo/表情包/%E4%B9%88%E4%B9%88%E5%93%92.jpg)

我是老丑，一位又老又丑的少年！我们下期见。

<b>学习推荐：</b>  

- [学习目录](https://mp.weixin.qq.com/s/dAjaR5UZFipgcSkAOFhPxg)（极力推荐大家阅读，目前在做的整个Java序列）
- [超凡回归带大家全面认识单例模式](https://mp.weixin.qq.com/s/YkKqEKbJiXX9fOePnyXcZA)
- [数据结构之初识树](https://mp.weixin.qq.com/s/nf-ZXFkuNdiFAvx9bida6Q)
- [如何自定义注解](https://mp.weixin.qq.com/s/F9s4SuQDFNZieMBy254RFA)
- [告别2020，展望2021](https://mp.weixin.qq.com/s/0JNMQ_DBYxEfRmXdc0fP8A)

我们是FingerDance，欢迎加入我们！

![](http://luckylaochou.gitee.io/photo/FingerDance.jpg)



