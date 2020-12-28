# Java基础之反射篇

> Hello，大家好！我是老丑。今天给大家带来的一个重头戏——反射

## 1. 反射是什么？

反射（Reflection），它允许在运行中的Java程序获取自身的信息，并且可以操作类或者对象的内部属性。

## 2. 反射可以做什么？

+ 在运行的时候分析类的能力
+ 在运行时查看对象
+ 利用Method对象
+ ...

## 3. 反射相关的类

下面会一一讲述

+ Class
+ Field
+ Method
+ Constructor

### 3.1 Class

Class，类对象，描述的是类的信息（注意，描述的是类，而不是对象）。

> > Java里面，万物皆对象。

**Class 类的一些方法**（列举部分）

```java
// 构造方法，参数为ClassLoader，类加载器
private Class(ClassLoader loader) {}
// 通过Class对象创建实例，在这里使用到了泛型的知识
// 大家如果对于泛型还不太熟悉的话，可以阅读Java基础之泛型篇
public T newInstance() throws InstantiationException,IllegalAccessException{}
// 这个方法可以根据全限定类名来获取类对象（注意这是类对象）。
public static Class<?> forName(String className) throws ClassNotFoundException {}
// 判断当前的类是否是接口
public native boolean isInterface();
// 判断当前的类是否是数组类
public native boolean isArray();
// 判断当前的类是否是注解类
public boolean isAnnotation() {}
// 根据类对象获取类名
public String getName() {}
// 获取加载该类的类加载器
public ClassLoader getClassLoader() {}
// 获取父类的名称
public native Class<? super T> getSuperclass();
// 获取包名
public Package getPackage()	{}
// 返回一个类数组，数组中包括该类中所有共同类和接口类的对象。
public Class<?>[] getClasses() {}
// 获取类的所有权限为public的变量（包括该类和超类或者超接口）
public Field[] getFields() throws SecurityException {}
// 获取本类的所有变量
public Field[] getDeclaredFields() throws SecurityException {}
// 获取类的所有权限为public的方法（包括该类和超类或者超接口）
public Method[] getMethods() throws SecurityException {}
// 获取本类的所有成员方法
public Method[] getDeclaredMethods() throws SecurityException {}
// 获取类的所有权限为public的构造方法
public Constructor<?>[] getConstructors() throws SecurityException {}
// 获取本类的所有构造方法
public Constructor<?>[] getDeclaredConstructors() throws SecurityException {}
```

### 3.2 Method

Method，翻译过来就是方法的意思，代表的是类的成员方法。

> > 在函数式编程里面，方法是一等公民！

**Method类的一些方法**（列举部分）

```java
// 获取方法名
public String getName() {}
// 获取返回值类型
public Class<?> getReturnType() {}
// 获取参数们的类型
public Class<?>[] getParameterTypes() {}
// 方法调用，传递参数obj就是对象本地，args就是该方法对应的参数
public Object invoke(Object obj, Object... args) {}
```

### 3.3 Field

Field，翻译过来就是字段，代表的是类的成员变量。

**Field类的一些方法**（列举部分）

```java
// 获取字段的类型
public Class<?> getType() {}
// 比较方法
public boolean equals(Object obj) {}
// 获取对象obj对应的属性值
public Object get(Object obj)throws IllegalArgumentException, IllegalAccessException {}
// 设置对象obj对应的属性的值为value
public void set(Object obj, Object value) throws IllegalArgumentException, IllegalAccessException {}
```

### 3.4 Constructor

Constructor，翻译过来就是构造函数的意思，代表的就是类的构造函数。

**Constructor类的一些方法**（列举部分）

```java
// 获取名称
public String getName() {}
// 获取参数们类型
public Class<?>[] getParameterTypes() {}
// 创建对应类的实例对象 initargs为参数
public T newInstance(Object ... initargs) throws InstantiationException, IllegalAccessException,IllegalArgumentException, InvocationTargetException
{}
```

## 4. 反射操作

> 在这里会列举一些基本操作。

### 4.1 前提

反射的前提是我们可以拿到Class对象（类对象）。

我们通过以下几种方式来Get我们的Class对象

```java
// 第一种
Class.forName(类名);
// 第二种，通过类来获取
Class.class;
// 第三种，通过对象的getClass方法来获取
obj.getClass();
```

**示例**

下面就是以上三种方法获取Class对象的具体示例。后续的代码只会使用第三种进行。

```java
People people = new People("laochou", 19, 1, "IdCard");
try {
    // 第一种
	Class<?> p2 = Class.forName("cn.laochou.entity.People");
} catch (ClassNotFoundException e) {
	e.printStackTrace();
}
// 第二种
Class<?> p1 = People.class;
// 第三种
Class<?> p = people.getClass();
```

如果有人问起<font color='red'>Class<?>，这个<?> 是什么意思？</font>，我只能说“年轻人，我劝你耗子尾汁，白嫖的[Java基础之泛型篇](https://mp.weixin.qq.com/s/RzELFPHXEvBOzPvaPB2LSg) 你都不要？”。

### 4.2 反射的基操

**声明：所有的类的字段都是为了演示目的，合理性目前暂不考虑**

**Animal类**

```java
package cn.laochou.entity;

public class Animal {

    private String name;

    private int age;

    private int sex;

    public String flag;

    public Animal(String name, int age, int sex) {
        this.name = name;
        this.age = age;
        this.sex = sex;
    }

    public static void call() {
        System.out.println("call");
    }
    
    @Override
    public String toString() {
        return "Animal{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", sex=" + sex +
                ", flag='" + flag + '\'' +
                '}';
    }
    
}

```

**People类**

```java
package cn.laochou.entity;

public class People extends Animal {

    private static int id = 1;
    
    public static int publicId = 2;

    private String idCard;

    public String tag;

    public People(String name, int age, int sex, String idCard) {
        super(name, age, sex);
        this.idCard = idCard;
    }
    
    private People(String name, int age, int sex) {
        super(name, age, sex);
    }

    public void sayHello() {
        System.out.println("hello");
    }

    public static void eat() {
        System.out.println("eat");
    }

    private void dance() {
        System.out.println("dance");
    }
    
    @Override
    public String toString() {
        return "People{" +
                "idCard='" + idCard + '\'' +
                ", tag='" + tag + '\'' +
                ", father='" + super.toString() + '\'' +
                '}';
    }
}

```

我们今天的反射的基本操作是建立在这两个类上。

#### 4.2.1 Main方法

```java
public static void main(String[] args) {
    People people = new People("laochou", 19, 1, "IdCard");
    Class<?> p = people.getClass();
//    printAllPublicMethods(p);
//    printAllMethods(p);
//    printAllPublicConstructor(p);
//    printAllConstructor(p);
//    printAllPublicField(p);
//    printAllField(p);
//    createObject(p);
//    methodInvoke(p, people);
//    setFieldValue(p, people);
}
```

大家可以对应main方法中的调用顺序进行阅读学习。

#### 4.2.2 打印所有权限为public的方法

```java
/**
 * 打印所有权限为public的方法
 * @param p 类对象
 */
public static void printAllPublicMethods(Class<?> p) {
    // 获取所有公有成员方法
    Method[] publicMethods = p.getMethods();
    for(Method method : publicMethods) {
    	System.out.println(method);
    }
}
```

**效果**

获取了所有公有方法（包括继承父类的）。

大家可以看到确实没有dance这个方法，因为dance方法被private修饰了

```java
public static void cn.laochou.entity.People.eat()
public void cn.laochou.entity.People.sayHello()
public static void cn.laochou.entity.Animal.call()
public final void java.lang.Object.wait() throws java.lang.InterruptedException
public final void java.lang.Object.wait(long,int) throws java.lang.InterruptedException
public final native void java.lang.Object.wait(long) throws java.lang.InterruptedException
public boolean java.lang.Object.equals(java.lang.Object)
public java.lang.String java.lang.Object.toString()
public native int java.lang.Object.hashCode()
public final native java.lang.Class java.lang.Object.getClass()
public final native void java.lang.Object.notify()
public final native void java.lang.Object.notifyAll()
```

#### 4.2.3 打印本类所有方法

```java
public static void printAllMethods(Class<?> p) {
    // 获取所有方法
    Method[] methods = p.getDeclaredMethods();
    for(Method method : methods) {
        System.out.println(method);
    }
}
```

**效果**

```java
public void cn.laochou.entity.People.sayHello()
public static void cn.laochou.entity.People.eat()
// 其中dance就是私有方法
private void cn.laochou.entity.People.dance()
```

#### 4.2.4 打印所有权限为public的构造方法

```java
/**
 * 打印权限为public的构造方法
 * @param p 类对象
 */
public static void printAllPublicConstructor(Class<?> p) {
	Constructor<?>[] constructors = p.getConstructors();
	for(Constructor<?> constructor : constructors) {
		System.out.println(constructor);
	}
}
```

**效果**

```java
public cn.laochou.entity.People(java.lang.String,int,int,java.lang.String)
```

#### 4.2.5 打印本类所有构造方法

```java
/**
 * 打印所有构造方法
 * @param p 类对象
 */
public static void printAllConstructor(Class<?> p) {
    Constructor<?>[] constructors = p.getDeclaredConstructors();
    for(Constructor<?> constructor : constructors) {
        System.out.println(constructor);
    }
}
```

**效果**

```java
public cn.laochou.entity.People(java.lang.String,int,int,java.lang.String)
// 私有构造方法
private cn.laochou.entity.People(java.lang.String,int,int)
```

#### 4.2.6 打印所有权限为public的变量

```java
/**
 * 打印所有权限为public的变量
 * @param p
 */
public static void printAllPublicField(Class<?> p) {
	Field[] fields = p.getFields();
	for(Field field : fields) {
		System.out.println(field);
	}
}
```

**效果**

tag和flag是被public修饰的。因此OK。

```java
public static int cn.laochou.entity.People.publicId
public java.lang.String cn.laochou.entity.People.tag
public java.lang.String cn.laochou.entity.Animal.flag
```

#### 4.2.7 打印本类所有变量

```java
/**
  * 打印本类所有成员变量
  * @param p 类对象
  */
public static void printAllField(Class<?> p) {
    Field[] fields = p.getDeclaredFields();
    for(Field field : fields) {
        System.out.println(field);
    }
}
```

**效果**

```java
private static int cn.laochou.entity.People.id
public static int cn.laochou.entity.People.publicId
private java.lang.String cn.laochou.entity.People.idCard
public java.lang.String cn.laochou.entity.People.tag
```

#### 4.2.8 反射获得构造方法创建对象

```java
public static void createObject(Class<?> p) {
    try {
        Constructor<?> constructor = p.getConstructor(String.class, int.class, int.class, String.class);
        String name = "laochou";
        int age = 19;
        int sex = 1;
        String idCard = "laochou";
        Object object = constructor.newInstance(name, age, sex, idCard);
        System.out.println(object);
    } catch (NoSuchMethodException | IllegalAccessException | InstantiationException | InvocationTargetException e) {
        e.printStackTrace();
    }
}
```

**效果**

这里是OK 的。

```java
People{idCard='laochou', tag='null', father='Animal{name='laochou', age=19, sex=1, flag='null'}'}
```

#### 4.2.9 反射调用方法

```java
/**
  * 通过反射来调用方法
  * @param p 类对象
  * @param obj 实例对象
  */
public static void methodInvoke(Class<?> p, Object obj) {
    try {
        Method method = p.getMethod("eat");
        method.invoke(obj);
    } catch (NoSuchMethodException | InvocationTargetException | IllegalAccessException e) {
        e.printStackTrace();
    }
}
```

**效果**

调用成功

```txt
eat
```

#### 4.2.10  反射来获取变量的值且设置变量的值

```java
/**
  * 通过反射来获取变量的值且设置变量的值
  * @param p
  * @param obj
  */
public static void setFieldValue(Class<?> p, Object obj) {
    try {
        Field field = p.getField("tag");
        System.out.println(field.get(obj));
        field.set(obj, "tag-2021");
        System.out.println(obj);
    } catch (NoSuchFieldException | IllegalAccessException e) {
        e.printStackTrace();
    }
}
```

**效果**

```txt
// 开始的值
null
// 后面设置完成之后打印的
People{idCard='IdCard', tag='tag-2021', father='Animal{name='laochou', age=19, sex=1, flag='null'}'}
```

## 5. 一些疑问或好奇？

> 不知道看到这里，大家有没有一些疑问或者好奇？
>
> 如果没有的话，我就引起大家的好奇。

### Class对象（类对象）为什么这么强大，什么都能获取。

首先，大家得知道Class对象是怎么来的。

因为Class对象是在类加载的过程中进行创建的，这里面涉及到部分虚拟机的一些知识。

大家如果没有接触过，就可以默认为在程序运行的时候，Java就可以帮我们创建一个Class对象。（后面会在虚拟机篇详细讲）

大家如果看过[让Java跑起来！](https://mp.weixin.qq.com/s/OJ8HoLL8s1dekJInlP1Cmg) 这篇文章的最后一步，写一个最简单的Java程序，在javac命令运行完的时候，大家细心的话，会发现桌面上多了一个Hello.class文件。

OK，其实Class对象类对象就是加载class文件而得到的。

下面我们简单的就People.class文件进行分析

```java
cafe babe 0000 0034 004d 0a00 1500 3009
0014 0031 0900 3200 3308 0034 0a00 3500
3608 0029 0800 2a07 0037 0a00 0800 3808
0039 0a00 0800 3a0a 0008 003b 0800 3c09
0014 003d 0800 3e0a 0015 003f 0a00 0800
3f09 0014 0040 0900 1400 4107 0042 0700
4301 0002 6964 0100 0149 0100 0870 7562
6c69 6349 6401 0006 6964 4361 7264 0100
124c 6a61 7661 2f6c 616e 672f 5374 7269
6e67 3b01 0003 7461 6701 0006 3c69 6e69
743e 0100 2928 4c6a 6176 612f 6c61 6e67
2f53 7472 696e 673b 4949 4c6a 6176 612f
6c61 6e67 2f53 7472 696e 673b 2956 0100
0443 6f64 6501 000f 4c69 6e65 4e75 6d62
6572 5461 626c 6501 0012 4c6f 6361 6c56
6172 6961 626c 6554 6162 6c65 0100 0474
6869 7301 001a 4c63 6e2f 6c61 6f63 686f
752f 656e 7469 7479 2f50 656f 706c 653b
0100 046e 616d 6501 0003 6167 6501 0003
7365 7801 0017 284c 6a61 7661 2f6c 616e
672f 5374 7269 6e67 3b49 4929 5601 0008
7361 7948 656c 6c6f 0100 0328 2956 0100
0365 6174 0100 0564 616e 6365 0100 0874
6f53 7472 696e 6701 0014 2829 4c6a 6176
612f 6c61 6e67 2f53 7472 696e 673b 0100
083c 636c 696e 6974 3e01 000a 536f 7572
6365 4669 6c65 0100 0b50 656f 706c 652e
6a61 7661 0c00 1c00 260c 0019 001a 0700
440c 0045 0046 0100 0568 656c 6c6f 0700
470c 0048 0049 0100 176a 6176 612f 6c61
6e67 2f53 7472 696e 6742 7569 6c64 6572
0c00 1c00 2801 000f 5065 6f70 6c65 7b69
6443 6172 643d 270c 004a 004b 0c00 4a00
4c01 0007 2c20 7461 673d 270c 001b 001a
0100 0a2c 2066 6174 6865 723d 270c 002b
002c 0c00 1600 170c 0018 0017 0100 1863
6e2f 6c61 6f63 686f 752f 656e 7469 7479
2f50 656f 706c 6501 0018 636e 2f6c 616f
6368 6f75 2f65 6e74 6974 792f 416e 696d
616c 0100 106a 6176 612f 6c61 6e67 2f53
7973 7465 6d01 0003 6f75 7401 0015 4c6a
6176 612f 696f 2f50 7269 6e74 5374 7265
616d 3b01 0013 6a61 7661 2f69 6f2f 5072
696e 7453 7472 6561 6d01 0007 7072 696e
746c 6e01 0015 284c 6a61 7661 2f6c 616e
672f 5374 7269 6e67 3b29 5601 0006 6170
7065 6e64 0100 2d28 4c6a 6176 612f 6c61
6e67 2f53 7472 696e 673b 294c 6a61 7661
2f6c 616e 672f 5374 7269 6e67 4275 696c
6465 723b 0100 1c28 4329 4c6a 6176 612f
6c61 6e67 2f53 7472 696e 6742 7569 6c64
6572 3b00 2100 1400 1500 0000 0400 0a00
1600 1700 0000 0900 1800 1700 0000 0200
1900 1a00 0000 0100 1b00 1a00 0000 0700
0100 1c00 1d00 0100 1e00 0000 6800 0400
0500 0000 0e2a 2b1c 1db7 0001 2a19 04b5
0002 b100 0000 0200 1f00 0000 0e00 0300
0000 0e00 0700 0f00 0d00 1000 2000 0000
3400 0500 0000 0e00 2100 2200 0000 0000
0e00 2300 1a00 0100 0000 0e00 2400 1700
0200 0000 0e00 2500 1700 0300 0000 0e00
1900 1a00 0400 0200 1c00 2600 0100 1e00
0000 5400 0400 0400 0000 082a 2b1c 1db7
0001 b100 0000 0200 1f00 0000 0a00 0200
0000 1300 0700 1400 2000 0000 2a00 0400
0000 0800 2100 2200 0000 0000 0800 2300
1a00 0100 0000 0800 2400 1700 0200 0000
0800 2500 1700 0300 0100 2700 2800 0100
1e00 0000 3700 0200 0100 0000 09b2 0003
1204 b600 05b1 0000 0002 001f 0000 000a
0002 0000 0017 0008 0018 0020 0000 000c
0001 0000 0009 0021 0022 0000 0009 0029
0028 0001 001e 0000 0025 0002 0000 0000
0009 b200 0312 06b6 0005 b100 0000 0100
1f00 0000 0a00 0200 0000 1b00 0800 1c00
0200 2a00 2800 0100 1e00 0000 3700 0200
0100 0000 09b2 0003 1207 b600 05b1 0000
0002 001f 0000 000a 0002 0000 001f 0008
0020 0020 0000 000c 0001 0000 0009 0021
0022 0000 0001 002b 002c 0001 001e 0000
0075 0002 0001 0000 0043 bb00 0859 b700
0912 0ab6 000b 2ab4 0002 b600 0b10 27b6
000c 120d b600 0b2a b400 0eb6 000b 1027
b600 0c12 0fb6 000b 2ab7 0010 b600 0b10
27b6 000c 107d b600 0cb6 0011 b000 0000
0200 1f00 0000 0e00 0300 0000 2400 2f00
2700 4200 2400 2000 0000 0c00 0100 0000
4300 2100 2200 0000 0800 2d00 2800 0100
1e00 0000 2500 0100 0000 0000 0904 b300
1205 b300 13b1 0000 0001 001f 0000 000a
0002 0000 0005 0004 0007 0001 002e 0000
0002 002f 
```

下面只会解析一部分，大家可以自行查阅资料进行解析。

大家首先映入眼帘的便是 cafe babe 。这个也确实很重要，"cafe babe"被称为魔数，大家可以理解为class文件的一个特殊标识。<font color="red">（在这里给大家普及下，对文件的后缀名进行辨别文件，其实是不安全的。玩过渗透的，懂的都懂。在这里，我只给大家普及下，大家想了解，可以自行了解）</font>

0000 0034：0000是次版本号，0034是主版本号，34的16进制转换成10进制就是52，虚拟机只能编译版本号比自己支持的最大版本号小的，超过了就会抛出异常。

在这里只列举了具有代表性的一些版本，其实每个版本还有很多小分支，可能有一定的误差。

| JDK版本 | 最大版本号 | 16进制对应  |
| ------- | ---------- | :---------: |
| 1.1     | 45.0       | 00 00 00 2D |
| 1.2     | 46.0       | 00 00 00 2E |
| 1.3     | 47.0       | 00 00 00 2F |
| 1.4     | 48.0       | 00 00 00 30 |
| 1.5     | 49.0       | 00 00 00 31 |
| 1.6     | 50.0       | 00 00 00 32 |
| 1.7     | 51.0       | 00 00 00 33 |
| 1.8     | 52.0       | 00 00 00 34 |

> > 因此，各位小伙伴是否知道我写这篇文章的时候JDK版本呢？

后面太多了，我们就不看了。大家有兴趣可以自行研究。（建议大家可以换个简单的class文件来研究，比如Hello.class。在这里我大意了，忘了闪）

**大家如果想解析class文件，可以使用Java自带的命令——javap**

后期也许会专门写一篇推文来解析Java命令。

## 6. 反射的应用

学了这么多，结果没用那不是等于白学。

放心，这就告诉你反射有多么强大。

+ 框架设计（著名的Spring framework 就用到了反射）
+ 开发工具
+ 动态代理（本来想在这里顺带给大家分享JDK Proxy，但是篇幅过长，后面在代理设计模式给大家分享）

## 7. 反射的优点与缺点

**优点：** 

+ 扩展性强

**缺点：** 

+ 性能低（因为反射涉及动态解析的类型，因此JVM无法对其进行优化。）
+ 安全限制，反射必须是在没有安全限制的情况下进行
+ 破坏封装性。

## Ending

推文到这里，就基本结束了。希望小伙伴能从这篇推文中，Get一些知识。

> > 学习路上有FingerDance，不孤单。欢迎加群不失联！
> >
> > 学习交流群在 【关于我们】->【加入我们】一栏。

我是老丑，一位又老又丑的少年！我们下期见。

要个赞不过分吧！

![](http://luckylaochou.gitee.io/photo/无版权表情包/%E4%B9%88%E4%B9%88%E5%93%92.jpg)

<b>往期推荐：</b>  

- [老丑在大学如何赚到"第一桶金"](https://mp.weixin.qq.com/s/qS4U4Tq6-3v9xiluMOD-mg)
- [双非本科拿到阿里和腾讯的Offer？](https://mp.weixin.qq.com/s/lEAN3mYczhZaVQNPrsYOHA)
- [双非本科拿到30w年薪经历了什么？](https://mp.weixin.qq.com/s/lbVHePuUv8WWp2NLUrWxXg)
- [只有快毕业了才有就业压力吗？](https://mp.weixin.qq.com/s/7Unjanm206AfW9J9hTcdAg)
- [BAT TMD 实习好玩吗？](https://mp.weixin.qq.com/s/vbsR-9o1S1vRlvgJJ-7i4Q)

我们是FingerDance，欢迎加入我们！

![](http://luckylaochou.gitee.io/photo/FingerDance.jpg)













