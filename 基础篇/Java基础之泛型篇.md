# Java基础之泛型编程

## 什么是泛型？

> 泛型是程序设计语言的一种特性。允许程序员在编写代码的时候可以定义一些可变部分，这些可变部分在使用前必须作出指明。
>
> 类型参数化以达到代码复用提高软件开发工作效率的一种方式。
>
> ——【百度百科】

OK，想必大家看到这些有些迷迷糊糊的，没事，大家可以继续往下看。看到这里，我希望大家记住一个比较重要的专业术语——>**类型参数化**。

## 泛型的例子

在这里，我们会列举一个使用泛型的案例。

为了让大家更好的理解泛型，因此我想了一个场景，不知道大家觉得场景怎么样哈！

**场景：**

```txt
现在有一群动物，在这群动物中有两个大类别，分别是狗和鸟。
动物园现在有两个动物园管理者，分别对应管理狗和鸟。
他们分别进行认领，并最后会进行一个舞台展示。
```

也不知道大家觉得这场景怎么样，如果你们说很垃圾的话，哎，那我也只能忍受了。

大家先看下代码的目录结构啦

![](http://luckylaochou.gitee.io/photo/Java基础之泛型编程篇/代码结构.png)

Animal

```java
package cn.laochou.entity;

public abstract class Animal {
    public abstract void eat();
}
```

Dog

```java
package cn.laochou.entity;

public class Dog extends Animal {

    @Override
    public void eat() {
        System.out.println("The dog is gnawing on a bone !");
    }

    @Override
    public String toString() {
        return "Dog";
    }
}
```

Bird

```java
package cn.laochou.entity;

public class Bird extends Animal {

    @Override
    public void eat() {
        System.out.println("The birds are eating worms !");
    }

    @Override
    public String toString() {
        return "Bird";
    }
}

```

Zooman

```java
package cn.laochou.operation;

import java.util.ArrayList;
import java.util.List;

/**
 * 动物园管理者(在这里就使用了泛型)
 * 注意这个T是泛型通配符
 */
public class Zooman<T> {

    public List<T> animals;

    public Zooman() {
        this.animals = new ArrayList<>();
    }

    public void getAnimals(T animal) {
        animals.add(animal);
    }

    public void show() {
        animals.forEach(System.out::println);
    }

}

```

Zooman类中的T便是泛型的体现，可以代表任何类型（泛指）。

Main

```java
package cn.laochou;

import cn.laochou.entity.Bird;
import cn.laochou.entity.Dog;
import cn.laochou.operation.Zooman;

public class Main {

    public static void main(String[] args) {
        Dog dogA = new Dog();
        Dog dogB = new Dog();
        Bird birdA = new Bird();
        Bird birdB = new Bird();
        // 注意，这里是在使用的时候进行确定Zooman中集合存储类型
        Zooman<Dog> dogZooman = new Zooman<>();
        Zooman<Bird> birdZooman = new Zooman<>();
        dogZooman.getAnimals(dogA);
        dogZooman.getAnimals(dogB);
        birdZooman.getAnimals(birdA);
        birdZooman.getAnimals(birdB);
        dogZooman.show();
        birdZooman.show();
    }

}

```

如果大家开发的时候泛型用的不是很多，可能觉得泛型没啥很大的作用。

但是大家设想下，如果我们这里不采用泛型的方式的话，那么我们可能需要做下面的工作来达到适配。

~~public List<T> animals;~~ ——> public List<Object> animals;

当然我们也可以针对管理不同种类的动物建立不同的类，但是本文的目的是让大家认识泛型，而不是来讲如何设计更合适。

如果你看到了这里，你已经体验过了泛型，但是大家可能没有意向到泛型的好处。下面就来讲讲泛型的好处。

## 泛型的好处

### 保证类型安全

泛型可以让编译器知道目前的对象限定类型，这样的话编译器可以帮我们在开发的过程中进行检查。我给这个过程取了一个名字——**自检**。

现在我在mian方法中，添加一行代码：

```java
public static void main(String[] args) {
    Dog dogA = new Dog();
    Dog dogB = new Dog();
    Bird birdA = new Bird();
    Bird birdB = new Bird();
    Zooman<Dog> dogZooman = new Zooman<>();
    Zooman<Bird> birdZooman = new Zooman<>();
    dogZooman.getAnimals(dogA);
    dogZooman.getAnimals(dogB);
    birdZooman.getAnimals(birdA);
    birdZooman.getAnimals(birdB);
    // 添加的一行代码，大家会发现有一个Error
    birdZooman.getAnimals(dogA);
    dogZooman.show();
    birdZooman.show();
}
```

![](http://luckylaochou.gitee.io/photo/Java基础之泛型编程篇/类型自检.png)

提示信息就是，我们要求的类型是Bird，而不是Dog。那么在开发过程中，是不是很方便帮助我们检查呢。

大家如果不相信，可以讲Zooman的泛型取消，将List<T> 改为List<Object>，然后这里就不会报错，但是这样是存在问题的。

### 消除强制类型转换

其实消除强制类型转换这个好处显而易见，大家设想一下，如果是通过List<Object>来进行存储的，那么从两种动物管理员，那么遍历并通过相对应的类型接收是需要类型转换的（不信大家可以一试）。

但是通过泛型，是无需进行类型转换的，取出及用

```java
public static void main(String[] args) {
    Dog dogA = new Dog();
    Dog dogB = new Dog();
    Bird birdA = new Bird();
    Bird birdB = new Bird();
    Zooman<Dog> dogZooman = new Zooman<>();
    Zooman<Bird> birdZooman = new Zooman<>();
    dogZooman.getAnimals(dogA);
    dogZooman.getAnimals(dogB);
    birdZooman.getAnimals(birdA);
    birdZooman.getAnimals(birdB);
    dogZooman.show();
    birdZooman.show();
	
    // 这是我额外加的循环结构，Bird同理。
    // 在这里是没有做类型转换的，但是如果我们Zooman类采用了List<Object>是需要强转的
    // 类型强转也是存在风险的。
    for(int i = 0; i < dogZooman.animals.size(); i++) {
        // 如果是List<Object> 在这里就需要通过(Dog) 进行强转
        Dog dog = dogZooman.animals.get(i);
        System.out.println(dog);
    }
}
```

## 泛型通配符

从上面可以看到，我们在Zooman类中使用了泛型通配符（T），其实泛型通配符并没有规定是什么。在我们的开发过程中或者阅读源码的过程中，会碰见很多不同的通配符，比如T，E，K，V等等，但是为了代码的阅读性，我们会有一些约束。

通常情况下你所看到的泛型通配符都是有一定含义的。

T ：Type（类型）

E ：Element（元素）

K ：Key（键）

V ：Value（值）所以K，V一般都使用在键值对的场景下

? ：表示不确定的类型，注意而T代表的是一个类型

### 无界通配符 ？

? 其实就是无界通配符。

因为在写代码的时候，很多时候，你并不能知道传过去的类型，因此必须使用无界通配符进行适配。

OK，为了让大家理解的更加清楚，我在之前的场景加了一个小细节

随意一个动物管理员都可以跟任何动物洗澡，我们在Zooman类中增加了一个函数

```java
public static void wish(List<?> animals) {
    System.out.println("给动物洗澡" + Arrays.toString(animals.toArray()));
}
```

在这里就使用到了无界通配符，因为谁都不知道是给哪些动物洗澡，可能是狗动物管理员给小鸟洗澡呢。

我们在主方法的代码块后面加入这一行代码

```java
// 狗动物管理员给鸟类洗澡
dogZooman.wish(birdZooman.animals);
```

想必大家看到这些，应该就能理解了（场景都是编纂的，主要是让大家认识认识泛型）

### 上届通配符 <? extends T>

OK，很明显的 extends关键词。extends大家应该都知道是继承的意思，那么 ? extends T的意思就是 此参数类型是T或者是T的子类。

就比如动物管理者的wish方法，其实也可以这样写。为什么？因为我们的 Dog类和Bird类都继承了Animal类。

```java
public static void wish(List<? extends Animal> animals) {
	System.out.println("给动物洗澡" + Arrays.toString(animals.toArray()));
}
```

### 下届通配符 <? super T>

super，如果大家在子类中调用父类的方法，应该知道super代表的是什么。理解的话，可以讲super作为父类的一层概念。

那么 ? super T 的意思就是 ? 这个类型是T或者 T的父类。

这与上面的上届通配符，理解起来一毛一样的。

## 类型擦除

泛型信息只存在于代码编译阶段，进入JVM之前，与泛型有关的信息就会被擦除掉。故我们称之为**类型擦除**。

我们来看一个很经典的例子，来证明泛型会被擦除

```java
List<Dog> dogs = new ArrayList<>();
List<Bird> birds = new ArrayList<>();
System.out.println(dogs.getClass() == birds.getClass());
```

最后打印的结果是 <font color="red">true</font>

我们在来看一个使用反射机制来加入元素的例子

```java
List<Dog> dogList = new ArrayList<>();
dogList.add(new Dog());
try {
     Method method = dogList.getClass().getDeclaredMethod("add", Object.class);
     method.invoke(dogList, new Dog());
     method.invoke(dogList, new Bird());
} catch (NoSuchMethodException e) {
     e.printStackTrace();
} catch (IllegalAccessException e) {
     e.printStackTrace();
} catch (InvocationTargetException e) {
     e.printStackTrace();
}
for(Object o : dogList) {
     System.out.println(o);
}
```

如果运行上面代码的话，会发现会成功加入的。

但是可能很多人会说：

你在这段代码中**Method method = dogList.getClass().getDeclaredMethod("add", <font color="red">Object.class</font>);** 使用的是Object.class 方法去获取的，当然加入Bird对象没有问题。如果你换成Dog.class，这样就无法加入Bird对象了。我只能说这位小伙伴太机智了，如果改成Dog.class会抛出异常，因为根本拿不到这个方法。大家可以自己试下。

**如果不设置上限的话，最后的底层都是使用List<Object>来实现的。** 这里也验证了上面为什么使用Object.class的参数列表来反射获取add方法。

**但是如果设置上限的话，那么最后的底层就是采用上线来进行实现的，在这里说上限，大家不太懂。**

<T extends String> 像这种的 String便是上限。如果是这种泛型的话，那么底层就是使用String来实现的。

## 泛型值得注意的地方

#### 泛型不支持Java8大基本类型作为参数类型

如果不知道Java有哪八大基本类型，可以阅读[Java有哪些基本类型]()来Get。

#### 注意接口泛型类型

一个类是无法同时实现同一个接口的两种的参数类型

```java
public interface Flyable<T> {}
public class Bird implements Flyable<Bird>{}
public class Fowl extends Bird implements Flyable<Fowl> {}
```

上面的代码是无法通过编译的，但是如果我们取消掉泛型，是编译通过的。

#### 重载

因为擦除，所以重载方法产生了相同的类型签名

```java
public class method<T, E> {
    void fun(T t) {}
    void fun(E e) {}
}
```

## 泛型在开发过程中的使用

我在这里举一个我在开发中会使用到泛型的例子，想必大家都会用到这个场景。

定制一个API接口返回的实体类。

```java
public class ReturnBody<T> {

    private int code;

    private String message;

    private T data;

    public int getCode() {
        return code;
    }

    public void setCode(int code) {
        this.code = code;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }
}
```

大家可以看到在这个返回实体类中就使用到了泛型，我们可以根据我们需要传输的数据类型来返回。

所以在开发的过程中，使用到泛型还是很常见的呢！

## 最后

好了，本文大概就介绍这么多。希望小伙伴们看完能Get到一些知识。

不知道这种讲述方式大家是否喜欢，可以通过私信告诉FingerDance。

> > 学习路上有FingerDance，不孤单。欢迎[加群（不失联）](https://mp.weixin.qq.com/s/tgIXQdrdKgeA_0kno7I9oQ)！

觉得推文写得还不错的小伙伴，还请点赞、关注、转发支持下。

![](http://luckylaochou.gitee.io/photo/无版权表情包/%E4%B9%88%E4%B9%88%E5%93%92.jpg)

我是老丑，一位又老又丑的少年！我们下期见（下期老丑可能会讲有关简历的一些东西，大家敬请期待）。

<b>往期推荐：</b>  

- [老丑在大学如何赚到"第一桶金"](https://mp.weixin.qq.com/s/qS4U4Tq6-3v9xiluMOD-mg)
- [双非本科拿到阿里和腾讯的Offer？](https://mp.weixin.qq.com/s/lEAN3mYczhZaVQNPrsYOHA)
- [双非本科拿到30w年薪经历了什么？](https://mp.weixin.qq.com/s/lbVHePuUv8WWp2NLUrWxXg)
- [只有快毕业了才有就业压力吗？](https://mp.weixin.qq.com/s/7Unjanm206AfW9J9hTcdAg)
- [BAT TMD 实习好玩吗？](https://mp.weixin.qq.com/s/vbsR-9o1S1vRlvgJJ-7i4Q)

我们是FingerDance，欢迎加入我们！

![](http://luckylaochou.gitee.io/photo/FingerDance.jpg)











