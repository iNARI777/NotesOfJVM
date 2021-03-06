# Java基础知识

## 1. 关于泛型

**泛型中的\<T>和<?>**

首先我们要明确，加入两个类`Parent`和`Son`有继承关系，但是他们的泛型，如：`List<Parent>`和`List<Son>`，是没有继承关系的，是两个不同的类。所以在用到`List<Parent>`的接口中是不能传入`List<Son>`的参数的，会在编译时报错。

但是为了实现多态，这个问题有了通配符`<?>`的解决方法。

假如说我们有一个容器List，`List<T>`就是泛型方法，而`List<?>`是限制通配符。

`<T>`一般在定义泛型类或泛型方法的时候使用，**代表一个具体的Java类型**，保持每个T为同一个类或者限制方法的参数之间或参数和返回结果之间的关系，也就是泛型化参数，我们在使用泛型方法或类的时候要给它赋一个具体的值。

`<?>`是在使用的时候对泛型参数起一个限制的作用，**代表一个未知的Java类型**，而泛型声明的时候是不可以使用`<?>`的。`<?>`更多的是使用在`<? extends T>`(上界通配符)和`<? super T>`(下界通配符)中。

关于上下界通配符看[这里](https://www.zhihu.com/question/20400700/answer/117464182)和[这里](https://www.zhihu.com/question/31429113)。

这里有一个坑要注意，如前所述：

	Plate<Fruit> p=new Plate<Apple>(new Apple());

是不能通过编译的，虽然`Apple`是`Fruit`的子类，但是`Plate<Fruit>`并不是`Plate<Apple>`的子类。

**静态方法上的泛型**

泛型类中的静态方法不能访问类上定义的泛型。如果静态方法的参数类型不确定的时候需要在静态方法上重新定义泛型。

**带上边界的泛型定义方法**

泛型类定义方法：

    public class Generic<T extends Number>{
       private T key;
    
       public Generic(T key) {
       this.key = key;
       }
    
       public T getKey(){
       return key;
       }
    }

泛型方法定义：

    public <T extends Number> T showKeyName(Generic<T> container){
       System.out.println("container key :" + container.getKey());
       T test = container.getKey();
       return test;
    }

## 2. JDK8的几个新特性

1. 引入了流API(Stream API)；
2. 引入了Lambda表达式；
3. 接口中可以添加静态方法以及默认方法；
4. 引入了新的Time和Date API。


## 3. RESTful风格和RPC风格

REST的全称是Representation State Transfer，即**表现层状态转化**，其中的表现层指的是资源表现层，如果客户端要和服务端进行交互、访问资源的话，势必涉及数据和状态的转化。但是HTTP是一个**无状态的协议**，因此所有的状态都保存在**服务端**，如果客户端想要操作这些资源，就需要使用HTTP的动词来进行操作。简单来说，就是通过URL定位资源，使用HTTP动词对资源进行操作。常用的操作如下：

* GET：返回资源
* POST：创建资源
* PUT：更新记录
* DELETE：删除记录

而RPC风格则是URL指明要调用的接口以及传递给接口的方法。

## 4. String和StringBuilder

### 4.1 String

`String`的底层是一个`char`类型的数组，在使用`String str = str1 + str2;`的时候不会直接在原来的字符串上进行拼接，而是直接生成一个新的`char[]`，组成新的字符串。

几种声明`String`的方法在内存的分配上也有所不同：

1. 使用`String str = "iNARI"`这种直接将字面量赋给字符串对象的时候，这个字面量的字符串对象实际在**编译期**被放到了常量池中，在运行的时候直接将**运行时常量池中的**`“iNARI”`这个字符串对象赋给`str`。
2. 使用`String str = new String("iNARI");`的时候，会涉及到两个对象，第一个是在编译期被放在class文件常量池中，在运行时加载到运行时常量池的字符串常量`"iNARI"`，另外一个是在运行时新建在堆中的`str`对象。这个时候如果使用`str == "iNARI"`的话实际上比较的是堆中的`str`对象和运行时常量池中的`"iNARI"`对象，两者显然是不相同的对象。
3. 使用`String str = new String("iNARI").intern()`则都是使用了运行时常量池中的对象，`intern()`方法会在运行时常量池中查找是否存在内容相同的字符串，如果存在则返回指向该字符串的引用，如果不存在，则会将该字符串入池，并返回一个指向该字符串的引用。

> 在为String类型对象赋值时，等号右侧**只有字符串常量拼接**的时候，如`String str = "i" + "NARI";`，编译器会在编译时将上述常量优化为同一个常量，这也是`String str += "i" + "NARI";`的效率高于`String str = str + "i" + "NARI";`的原因，因为前者会减少一次拼接。

更多例子可以看这篇[博客](http://www.cnblogs.com/dolphin0520/p/3778589.html)。

### 4.2 StringBuilder

与`String`一样，`StringBuilder`的底层也是`char[]`，默认长度是16，内部使用一个变量`count`来表示内部保存的字符串的长度。

在进行`append()`的时候，会向字符数组中直接添加字符，当字符数组长度不够的时候，对数字进行扩容，将数组长度变为**当前长度的2倍加2**，并将原来数组中的字符拷贝进去。

更详细的内容可以看这篇[博客](https://blog.csdn.net/qq_17505335/article/details/52806096)。

## 5. 异常的分类

Java中的异常继承体系如下图所示（只列出了部分异常）：

![](images/java/1.png)

错误：Error类以及他的子类的实例，代表了JVM本身的错误。错误不能被程序员通过代码处理，Error很少出现。因此，程序员应该关注Exception为父类的分支下的各种异常类。

异常：Exception以及他的子类，代表程序运行时发送的各种不期望发生的事件。可以被Java异常处理机制使用，是异常处理的核心。

异常又分为**受检查异常**和**非受检查异常**。

非检查异常（unckecked exception）：Error 和 RuntimeException 以及他们的子类。javac在编译时，不会提示和发现这样的异常，不要求在程序处理这些异常。所以如果愿意，我们可以编写代码处理（使用try...catch...finally）这样的异常，也可以不处理。对于这些异常，我们应该修正代码，而不是去通过异常处理器处理 。这样的异常发生的原因多半是代码写的有问题。

检查异常（checked exception）：除了Error 和 RuntimeException的其它异常。javac强制要求程序员为这样的异常做预备处理工作（使用try...catch...finally或者throws）。在方法中要么用try-catch语句捕获它并处理，要么用throws子句声明抛出它，否则编译不会通过。这样的异常一般是由程序的运行环境导致的。因为程序可能被运行在各种未知的环境下，而程序员无法干预用户如何使用他编写的程序，于是程序员就应该为这样的异常时刻准备着。

常见的非受检异常：ArithmeticException、ArrayIndexOutOfBoundsException、ClassCastException、IllegalArgumentException、NullPointerException、NumberFormatException等。

常见的受检异常：ClassNotFoundException、InterruptedException、NoSuchFieldException、NoSuchMethodException、FileNotFoundException

对于覆盖方法来说，子类方法的异常说明里只能比父类方法的异常说明更少。

## 6. finally语句

注意，finally语句中的语句一定会被执行。

如果finally块中有return，则返回值都以finally块中的为准，而不会执行try块或者catch块中的return语句。

如果在finally语句中也抛出异常，则可能导致try块中的异常被忽略。

如果finally块之前的try块中含有return语句，那么finally块中的语句在执行的时候会被插到return的紧跟前（这个时候return后跟的表达式已经执行完了）。

## 7. 字节序

1. Java中使用的字节序是大端法，和网络字节序相同。