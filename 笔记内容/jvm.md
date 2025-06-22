一次编写，到处运行

- 自动内存管理，垃圾回收功能
- 数组下表越界检查
- 多态，（虚方法表）

比较

jvm

 jre：jvm+基础类库

 jdk：java开发工具包

- 理解底层的实现原理

自动拆装箱，动态代理，for each增强

jvm版本hotspot

# Class Loader类加载器

1. ### ==类文件结构==

```java
ClassFile{
  u4	magic; 这个表示文件的类型ca fe ba be
 	u2	minor_version; 小版本
  u2	major_version; 组版本 00 34(十六进制) (52)
  u2	constant_pool_count; 第8~9字节就是常量池长度了 00 23 （35）表示有1~34项第0项不算
    第一项0a表示一个method信息，10代表方法引用，
    接下来的4个字节代表一些信息，00 06 00 15代表前两个代表类名，后两个代表方法名。
  cp_info	constant_pool[constant_pool_count-1];
  u2 access_flags;访问标识 00 21代表公共类加起来
  u2 this_class; 找到本类的限定名；00 05
  u2 super_class  00 06
  u2 interfaces_count; 00 00
  u2 interfaces[]接口信息
  u2 fields_count; 00 00
  field_info fields[fields_count];
  u2 	methods_count; 00 02 
  method_info	methods[methods_count]
  u2	attributies_count;附加属性
  attribute_info	attributes[attributes_count];
}
```

==类文件补充==，16进制ascll值 

还有，在类的访问修饰上，有公共01， 最终类final10，20表示他是个类，200表示她是一个接口，400抽象类，1000人工合成的，2000annotation注解，4000枚举

类的成员变量 B字节 C char D double F float I int J long L className;   引用类型 S short Z boolean [ 一维信息

一个方法有，访问修饰符，名称，参数描述，方法属性数量，方法属性组成。

参考文献

https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-4.html

### ==字节码文件指令==

```java
public cn.itcast.jvm.t5.HelloWorld();
2a b7 00 01 b1;
2a 在局部变量表中aload_0槽位的变量加载到操作数栈上;
b7 预备调用构造方法
00 01 引用常量池中#1项 Method java/lang/Object."<init>":()V
b1 表示返回
```

```java
public static void main(java.lang.String[]);
b2 00 02 12 03 b6 00 04 b1
b2 getstatic 用来加载静态变量，哪个静态变量呢;
00 02 常量池#2项，Field java/lang/System.out:Ljava/io/PrintStream;
12 加载参数
03 常量池 String hello world
b6 预备调用成员方法，哪个方法呢;
00 04 Method java/io/PrintStream.println:(Ljava/lang/String;)V
b1 返回
```

在java中，先是对象==方法，参数==。

在虚拟机中，对象，==参数方法==

javap工具来反编译class文件

命令：javap -v helloWorld.class

字节码指令和操作数栈，常量池的关系。

逻辑结构分为，堆，执行引擎，栈帧，运行时常量池（在方法区中），方法区

首先源代码变为字节码，然后常量池载入运行时常量池 ，方法字节码载入方法区。main线程来时运行，分配栈帧内存，这个里面有栈深度，局部变量表。然后执行引擎读到方法区的字节码开始执行

```java

```

bipush10 将一个byte压入操作数栈，补齐4个字节，long不行就拆成两个放入栈中，小的就和字节码指令存在一起，超过soort范围的数字存入常量池

istore_1：将操作数栈顶数据弹出，存入局部变量表中的slot 1

ldc#3：因为超出了short范围存入操作数栈

istore_2：弹出存入slot2

iload_1：读出slot1到操作数栈上

iload_2: 读出slot2到操作数栈上

iadd:加法运算

istore_3:弹出到3槽位

getstatic#4:将堆中的System.out的对象的引用放入操作数栈中

iload_3 将3号槽位放入操作数中

invokevirtual#5，找到常量池#5项，地位到方法区java/io/PrintStream.print:(I)V方法，生成新的栈帧，分配Locals，stack等。传递参数，执行新栈帧中的字节码将main操作栈中的那个int参数放入println栈帧，打印操作执行完毕，这个println栈帧从方法栈中弹出，return，弹出主方法栈帧，结束程序

==异常处理==

有个异常方法表，Exception table 这个表中的from to type分别表示from2to5这个就是含头不含尾也就是说他检测2到4总计字节码指令，发生了异常，就会让发生的异常和type进行匹配

可以看到finally中的代码被复制了三份，分别放入try流程，catch流程以及catch剩余的异常类型流程

==Synchronized==该对象的内置锁，包含解锁和开锁少一步都不行，锁住的那部分，代码，会进行异常检测，开锁也会检测异常

==编译期处理==：

默认构造器，无参的默认构造方法。

```java
public class Candy1{
  public Candy1(){
    super();用来调用父类的无参构造方法。
  }
}
```

### 自动拆装箱，这个特性在jdk5开始加入的，

```java
public class Candy2{
  public static void main (String[] args){
    integer x = 1;
    int y = x;
  }
}
```

这段代码的jdk5之前无法通过编译，得改为以下的

```java
public class Candy2{
  public static void main(String[] args){
    integer x = Integer.valueOf(1);
    int y = x.intValue();
  }
}
```

需要再基本类型和包装类型来回转换（尤其是集合类中的操作的都是包装类型）

泛型集合，也是jdk5特性。在java编译泛型代码之后，会进行一个泛型擦除的动作，即泛型信息在编译为字节码文件后就丢失了，实际的类都是当做Object类来处理

```java
public class Candy3{
  public static void main(String[] args){
    List<Integer> list = new ArrayList<>();
    list.add(10);  //实际调用的是list.add(object e)
    Integer x = list.get(0); //实际调用的是Object obj = List.get(int index);
  }
}
```

通过强制类型转换将object转换成integer 字节码指令checkcast

所以在取值时，编译器真正生成的字节码中，还要额外做一个类型转换的操作：

```java
Integer x = (Integer)list.get(0);
int x = ((Integer)list.get(0)).intValue();进行了拆箱
```

擦除的是字节码上的泛型信息，有个局部变量类型表LIcalVariableTypeTable 这些就包含了泛型信息，没有办法==通过反射==来==拿到这种局部变量的泛型信息==。==只有你在方法的参数，返回值上，可以通过反射来拿到==

```java
使用反射，任然能够获得这些信息
public Set<Integer> test(list<String> list, Map<Integer,Object> map){
  
}
```

```java
Method test = Candy3.class.getMethod("test",List.class,Map.class);
Type[] types = test.getGenericParameterTypes();
for(Type type : types){
  if (type instanceof ParameterizedType){
    ParameterizedType parameterizedType = (ParameterizedType) type;
    parameterizedType.getRawType();得到原始类型
    Type[] arguments = parameterizedType.getActualTypeArguments();
    for(int i  = 0; i< arguments.length; i++){
      //printf使用
      System.out.printf("泛型参数[%d] - %s\n", i, arguments[i]);
    }
  }
}
```

### jdk5的可变参数

```java
public class Candy4{
  public static void foo(String... args){
    String[] array = args; // 直接赋值
    System.out.println(array);
  }
  public static void main(String[] args){
    foo("hello", "world");
  }
}
```

String... args 和 String[] args是一样的

```java
public class Candy4{
  public static void foo(String[] args){
    String[] array = args;
    System.out.println(array);
  }
  public static void main(String[] args){
    foo(new String[]{"hello", "world"});
  }
}
```

==注意==：如果调用了foo() 则代码等价为foo(new String[]{})，创建一个空的数组，而不会传递null进入

### ==foreach循环==：任然是jdk5开始引入的语法糖，数组的循环：

```java
public calss Candy5_1{
  public static void main(String[] args){
    int[] array = {1, 2, 3, 4, 5};
    for (int e : array) {
      System.out.println(e);
    }
  }
}
```

会被编译器转化为

```java
public class Candy5_1 {
  public Candy5_1() {
    
  }
  public static void main(String[] args) {
    int[] array = new int[]{1, 2, 3, 4, 5};
    for(int i = 0; i < array.length; ++i) {
      int e = array[i];
      System.out.println(e);
    }
  }
}
```

而集合的循环

```java
public calss Candy5_1{
  public static void main(String[] args){
    List<Integer> list = Arrays.asList(1,2,3,4,5);
    for (Integer i : list) {
      System.out.println(i);
    }
  }
}
```

实际被编译器转换为对迭代器的调用：

```java
public class Candy5_2 {
  public Candy5_2() {
  }
  public static void main(String[] args) {
    List<Integer> list = Arrays.asList(1,2,3,4,5);
    Interator iter = list.iterator();
    while(iter.hasNext()) {
      Integer e = (Integer)iter.next(); // 泛型擦除
      System.out.println(e);
    }
  }
}
```

==注意==：foreach循环写法，能够配合数组，以及所有实现了Iterable接口的集合类一起使用，其中Iterable用来获取集合的迭代器（Iterator）

### ==switch字符串==

从jdk7开始，switch可以作用于字符串和枚举类，这个功能其实也是语法糖，例如

```java
public class Candy6_1 {
  public static void choose(String str){
    switch(str) {
      case "hello": {
        System.out.println("h");
        break;
      }
      case "world": {
        System.out.println("w");
        break;
      }
    }
  }
}
```

switch配合String和枚举类使用时，变量不能为null，原因分析完语法糖转换后的代码应当自然清楚，会被编译器转换为：

```java
public class Candy6_1 {
  public Candy6_1(){
    
  }
  public static void choose(String str) {
    byte x = -1;
    switch(str.hashCode()) {
      case 99162322: //hello的hashCode 使用这个提高比较效率
        if(str.equals("hello")) {	//equals为了防止hashCode冲突
          x = 0;
        }
        break;
      case 113318802: //world的hashCode
        if (str.equals("world")) {
          x = 1;
        }
    }
    switch(x) {
      case 0:
        System.out.println("h");
        break;
      case 1:
        System.out.println("w");
        break;
    }
  }
}
```

```java
public calss Candy6_2 {
  public Candy6_2() {}
	public static void choose(String str){
    byte x = -1;
    switch(str.hashCode()) {
      case 2123:
        if (str.equals("C.")) {
          x = 1;
        } else if (str.equals("BM")) {
          x = 0;
        }
      default:
        switch(x) {
          case 0:
            System.out.println("h");
          case 1:
            System.out.println("W");
        }
    }
  }
}

```

### switch枚举

```java
enum Sex {
  MALE, FEMALE
}
```

```java
public class Candy7 {
  public static void foo(Sex sex) {
    switch(sex) {
      case MALE:
        System.out.println("男");break;
      case FEMALE:
        System.out.println("女");break;
    }
  }
}
```

转换后代码：

```java
public class Candy7 {
  /**
  * 定义了一个合成类（仅jvm使用，对我们不可见）
  * 用来映射枚举的ordinal与数组元素的关系
  * 枚举的ordinal表示枚举对象的序号，从 0 开始
  * 即 MALE 的 ordinal()=0,FEMALE de ordinal()=1
  */
  static class $MAP {
    static int[] map = new int[2];
    static {
      map[Sex.MALE.ordinal()] = 1;
      map[Sex.FEMALE.ordinal()] = 2;
    }
  }
  public static void foo(Sex sex) {
    int x = $MAP.map[sex.ordinal()];
    switch (x) {
      case 1:
        System.out.println("男");
        break;
      case 2:
        System.out.println("女");
        break;
    }
  }
}
```

### jdk7新增了枚举类

```java
enum Sex {
  MALE, FEMALE
}
转换后代码
public final class Sex extends Enum<Sex> {
  public static final Sex MALE;
  public static final Sex FEMALE;
  private static final Sex[] $VALUES;
  static {
    MALE = new Sex("MALE", 0);
    FEMALE = new Sex("FEMALE",1);
    $VALUES = new Sex[]{MALE,FEMALE};
  }
  private Sex(String name, int ordinal) {
    super(name,ordinal);
  }
  public static Sex[] values() {
    return $VALUES.clone();
  }
  public static Sex valueOf(String name){
    return Enum.valueOf(Sex.class,name);//枚举实例，枚举实例名称
  }
}
```

### JDK7 开始新增了对需要关闭的资源处理的特殊语法try-with-resources

```java
try (资源变量 = 创建资源对象) {
  
} catch( ) {
  
}
```

其中的资源对象需要实现AutoCloseable接口，例如InputStream、OutputStream、Connection、Statement、ResultSet等接口都实现了AutoCloseable，使用try-with-resources 可以不用写 finally 语句块，编译器会帮助生成关闭资源代码，例如：

```java
public class Candy9 {
  public static void mian(String[] args) {
    try(InputStream is = new FileInputStream("d:\\1.txt")) {
      System.out.println(is);
    } catch (IoException e) {
      e.printStackTrace();
    }
  }
}
```

会被转化成

```java
public class Candy9 {
  public Candy9() {}
  public static void main(String[] args) {
    try{
      InputStream is = new FileInputSteam("d:\\1.txt");
      Throwable t = null;
      try{
        System.out.println(is);
      } catch (Throwable e1) {
      // t是我们代码出现的异常
      	t = el;
      	throw el;
      }finally {
         // 判断了资源不为空
        if (is != null) {
          //如果我们的代码有异常
          if (t != null) {
            try {
              is.close();
            } catch (throwable e2) {
              // 如果 close 出现异常，最为被压制异常添加
              t.addSuppressed(e2);
            }
          } else {
            // 如果我们的代码没有异常，close 出现后的异常就是最后 catch 块中的 e
            is.close();
          }
        }
      }
    } catch (IOException e) {
      e.printStackTrace();
    }
  }
}
```

### 方法重写时的桥接方法

我们都知道，方法重写时对返回值分两种情况：

- 父子类的返回值完全相等
- 子类返回值可以是父类返回值的子类（如下）

```java
class A {
  public Number m() {
    return 1;
  }
}
class B extends A {
  @Override
  // 子类m方法的返回值是Integer是父类m方法返回值Number的子类
  public Integer m() {
    return 2;
  }
}
```

对于子类，java编译器会做如下处理

```java
class B extends A {
  public Integer m() {
    return 2;
  }
  // 这个最为一个桥接手段，这样就符合重写的规则
  public synthetic bridge Number m() {
    return m();
  }
}
可以这样做的是向上转型。我把一个具体的还给一个抽象的当然可以，也就是子类
```

### 匿名内部类

```java
public class Candy11 {
  public static void main(String[] args) {
    Runable runnable = new Runnable() {
      @Override
      public void run() {
        System.out.println("ok");
      }
    };
  }
}
```

转换后的代码

```java
// 额外生成的类
final class Candy11$1 implements Runnable {
  Candy11$1() {}
  public void run() {
    System.out.println("ok");
  }
}
public class Candy11 {
  public static void main(String[] args) {
    Runnable runnable = new Candy11$1();
  }
}
```

```java
如果在本类里面有一个变量，但是匿名内部类需要使用如何实现
final class Candy11$1 implements Runnable {
  int val$x;
  Candy11$1(int x) {
    this.val$x = x;
  }
  public void run() {
    System.out.println("ok:" + this.val$x)
  }
}
public class Candy11 {
  public static void test(final int x) {
    Runnable runnable = new Candy11$1(x);
  }
}
// 注意在创建匿名内部类的时候将x复制给了对象的属性，如果x变化了，属性没有机会一起变化
```

### 类加载阶段

==加载==

底层是C++的一个instanceKlass描述java类。重要的field有

- java类镜像，就是 .class 文件。将Klass暴露给java使用
- super 父类
- _fields 成员变量
- _methods 方法
- _constants 常量池
- _class_loader 类加载器
- _vtable 虚方法表
- itable 接口

加载和下面的链接是交替进行的

java类镜像在堆中（jvm内存中），而那个Klass是存储在方法区（1.8是在元空间，本地内存）

==链接==

验证：是否是符合jvm规范，安全检查

准备

1.7静态变量已经跟着.class文件存储在堆中了。分配空间在准备阶段执行，赋值在初始化阶段完成，因为类的构造方法在初始化完成。`static final int c = 20;`在编译期间就已经赋值了。`static final String d = "hello";`这个在准备阶段就已经好了，引用类型就不行，赋值类还得在初始化阶段完成

解析

将常量池中的符号引用解析为直接引用,符号就是仅仅是一个符号，经过了解析以后，确切的知道常量池中常量的一个具体的位置。

//loadClass方法

```java
public class Load2 {
  ClassLoader calssloader = Load2.class.getClassLoader();
  Class<?> c = classloader.loadClass("cn.itcast.t3.load.C");
  
}

```

new会触发类的加载，解析，初始化

==类初始化==

- main方法所在类会先初始化
- 首次访问这个类的静态变量或者静态方法时
- 子类初始化，如果父类还没有初始化，会引发父类初始化
- 子类访问父类的静态变量，只会触发父类的初始化
- Class.forName
- new

不会导致初始化

- 访问类的 static final 静态常量（基本类型和字符串）不会触发类的初始化。准备阶段就完成了
- 类对象.class不会触发初始化
- 创建该类的数组不会触发初始化
- 类加载器的loadClass方法
- Class.forName的参数2为false时

==启动类加载器==：Bootstrap ClassLoader	加载类 JAVA_HOME/jre/lib C++编写的

==扩展类加载器==：Extension ClassLoader		JAVA_HOME/jre/lib/ext

==应用程序类加载器==：Application ClassLoader		classpath

==自定义加载器==:		自定义

双亲委派的类加载器这种模式，翻译为上级似乎更合适，因为他们并没有继承关系

jdk某些时候需要打破这个这种模式，Service Provider Interface(SPI)

线程上下文加载器就是应用程序类加载器

### 运行时优化

JVM将执行状态分为5个层次

- 解释执行
- 使用C1即时编译器编译执行（不带profiling）
- 使用C1即时编译器编译执行（带基本的profiling）这种JIT是直接执行机器码
- 使用C1即时编译器编译执行 （带完全的profiling）
- 使用C2即时编译器编译执行

hotspot由来热点代码

- 逃逸分析

- 方法内联

- 字段优化

  - 方法内联会影响成员变量读取操作
  - 尽可能使用局部变量，没有禁用jvm的方法内联，jvm会优化

反射优化

  

# jvm内存结构

## java Memory model

java内存模型

定义了一套多线程读写共享数据时（成员变量，数组）时，对数据的可见性，有序性，和原子性的规则保障。

原子性：synchronized（obj）

monitor这是对象的区，包括owner正在用的线程，EntryList等待的线程，waitSet。这个synchronized写在循环外，不用重复加解锁的指令。注意：得锁住同一个对象，不然没法锁住同房间。

对一个静态变量的自增自减并不是原子操作

对i++而言，实际会产生如下的jvm字节码指令，这个操作在操作数栈上执行，不是槽上。局部变量在槽上进行i++

```java
getstatic i // 获取静态变量的值
iconst_1 	//准备常量1
iadd	//自增 isub减法
putstatic	i	//将修改后的值存入静态变量i
```

在共享的静态变量是放在主存中的。线程是在公作内存中。在工作内存中执行加法后写回主存中。在单线程中不会产生交错代码的执行是一个块整体。

```java
public class DemoThread {
  volatile static boolean run = true; // 加上volatile会体现可见性
  public static void main(String[] args) throws InterruptedException {
    Thread t = new Thread(()->{
      while (run){
        
      }
    });
    t.start();
    thread.sleep(1000);
    run = false; // 线程不会如预想的停下来
  }
}
```

每次都会循环读取静态变量的值，在反复的用的时候，jit编译器就会将这个变量放入高速缓存中，之后的读取就直接到高速缓存中取，这个就不会停下来。

==volatile（易变关键字）==

它可以用来修饰成员变量和静态成员变量，它可以避免线程从自己的工作缓存中查找变量的值，必须到主存中获取他的值，线程操作volatile变量都是直接操作主存



==assert断言==

```java
assert expression : message;
expression:布尔值
message:抛出错误的错误信息
```

==transient==

修饰类的字段表示该字段（也就是类的成员变量）不会参与对象的序列化和反序列化过程

```java
import java.io.Serializable;

public class Person implements Serializable {
    private String name;
    private transient int age; // age字段被标记为transient，不会参与序列化过程
    
    // 省略其他代码
}

```

一个线程对volatile变量的修改对另一个线程可见，不能保证原子性，仅用在一个写线程多个读线程的情况。它并不能保证原子性，如果多个写线程的不好用了

==有序性==

```java
public class ConcurrencyTest {
  int num = 0;
  boolean ready = false;
  public void actor1(I_Result r) {
    if (ready) {
      r.r1 = num + num;
    } else {
      r.r1 = 1;
    }
  }
  public void actor2(I_Result r) {
    num = 2;
    ready = true;
  }
}
```

在这个代码中，两个线程分别执行actor1,2这两个方法，会出现 1 4  这两种情况 也会出现0 = 0 + 0这种情况，利用volatile可以避免

```java
double-checked locking 双重检查	
public final class Singleton {
  private Singleton() {}
  public static Singleton getInstance() {
    if (INSTANCE == null) {
      synchronized (Singleton.class) {
        if(INSTANCE == null) {
          INSTANCE = new Singleton();
        }
      }
    }
    return INSTANCE;
  }
}
```

new Singleton()对应的字节码为

```java
new  #2 // class cn/wei/Singleton
dup
4：invokespecial	#3 // Method "<init>":()V
7：putstatic #4 // Field INSTANCE:Lcn/wei/Singleton
```

第4和7步有可能顺序不是固定的的，也许赋值給INSTANCE操作在执行构造方法之前。

```java
1：t1 线程执行到 new
2：t1 线程分配空间，为Singleton对象生成了引用地址（0处）
3：t1线程将引用地址赋值给了INSTANCE，这时 INSTANCE != null
4：t2进入了getInstance()方法，发现，不为空直接返回
5：t1执行Singleton的构造方法处
```

这样就导致了构造方法还没有结束，你的对象已经返回了，可以用volatile在5以后才有效。在禁用指令重排

==happens-before==

这个规定了那些写操作对其他线程的读操作可见，它是一套可见性和有序性的一套规律总结

- 线程解锁 m 之前对变量的写，对于接下来对 m 加锁的其他线程对该变量的读可见
```java
  static int x;
  static Object m = new Object();
  new Thread(()->{
    synchronized(m) {
      x = 10;
    }
  },"t1").start();
  
  new Thread(()->{
    synchronized(m) {
      System.out.println(x);
    }
  },"t2").start();
```

- 线程对 volatile 变量的写，对接下来其他线程对该变量的读可见
- 线程start前对变量的写，对该线程开始后对该变量的读可见
- 线程结束前对变量的写，对其他线程在得知它结束后的读可见。比如其他线程可以调用t1.isAlive()或者t1.join()
- 线程1打断2前对变量的写，对于其他线程得知2被打断后对变量的读可见
- 对变量默认值的写（0，false，null），对其他线程对该变量读可见
- 具有传递性，wohbni nihbta wohbta

==变量指的是成员变量或者静态成员变量==

==CAS与原子类==

CAS即 compare and swap 体现的是一种乐观锁的思想，比如多个线程对一个共享整型变量

```java
while (true) {
  int 旧值 = 共享变量;
  int 结果 = 旧值 + 1;
  if(comapreAndSwap(旧值,结果)) {
    //成功推出循环
  }
}
```

因为没加锁，线程不会阻塞，但是一直循环，cpu的时间一直在这个代码里面跑

但如果竞争激烈，重试就会一直发生

但是这个共享变量必须是volatile

实现调用UnSafe这个类通过反射拿到对象调方法，拿到类对象和偏移量作为方法参数

上边的实现已经封装好了在jdk中

==原子操作类==

AtomicInteger	AtomicBoolean	

==sychronized优化==

轻量级锁

每个线程的栈帧都会包含一个锁记录的结构，内部可以存储锁定对象的Mark Word

01表示无锁，线程一访问同步块A，把mark赋值到线程1的锁记录这个锁记录是栈，然后CAS修改Mark为线程1锁记录地址，成功加锁，00表示轻量级锁，线程1锁记录地址。然后进入第二个同步代码块，交换名片，Mark复制到线程一的锁记录，CAS修改Mark为线程1锁记录地址。但是这个对象的锁记录地址已经是自己的锁了，然后锁重入，然后执行同步块B。弹出B ，A。还回去MarkWord，把Markword的锁标记改为01

==锁膨胀==

如果在你尝试加轻量级锁的过程中，CAS无法操作成功，这时候，就会发生锁膨胀，将清量级锁变为重量级锁。01无锁 00轻量级锁（10）重量级锁 在对象头里面加入一个重量锁指针。通过这个指针唤醒阻塞中的线程。这时拥有重量级锁的那个线程第一次解锁会失败，但是按照重量级的解锁就好了，唤醒阻塞线程，然后再竞争重量级锁。

==竞争重量级锁，自旋优化==

在线程二竞争的时候，访问同步块，竞争monitor。减少了线程上下文的线程阻塞唤醒这个过程。

==偏向锁==

jdk6中在锁重入时加入偏向锁的概念。只有第一次的时候才使用CAS将线程id设置到Mark Word头，之后发现这个线程iD是自己就表示没有竞争，不用重新CAS。一旦有其他线程来竞争，撤销偏向锁升级为轻量，这个过程需要所有线程暂停STW。其他线程访问对象对象的hashCode也会撤销偏向锁。偏向了线程1的任由机会偏向线程2，重偏向会重置对象的线程id。偏向和重偏向都是以类为单位，批量进行。如过撤销偏向的次数过多，就会整个类不可偏向，可以禁用偏向锁。

==对线程的其他优化==

1. 减少上锁时间
   1. 同步代码块中尽量短
2. 减少锁的粒度

​	将一个锁拆分成多个锁提高并发度，例如

​	ConcurrentHashMap

​	LongAdder：并发累加

​	LinkedBlockingQueue入队和出队使用不同的锁，相对与LinkedBlockingArray只有一个锁效率高

3. 锁粗化

   1. StringBuffer类线程安全，把多个Synchronized合并粗化为一个

    ```java
    new StringBuffer().append("a").append("b").append("c");   
    ```


4. 锁消除

   1. 例如某个加锁对象是方法内局部变量，不会被其他线程所访问到，这时候即时编译器会忽略所有同步操作。

5. 读写分离

   1. CopyOnWriteArrayList
   2. ConyOnWriteSet

   读操作不用同步，写同步就好了

## method Area 

方法区，是所用线程共享的一个区域，跟类的结构有关的，成员方法，构造器方法。

在虚拟机启动时被创造，逻辑上是堆的一部分，具体的jvm厂商不一样。1.8之前永久代，jvm内存。1.8之后包含1.8元空间实现，本地内存

==方法区==也有内存溢出的

类的信息，运行时常量池，类加载器

类加载器可以加载类的二进制字节码

Class Writer类作用生成类的二进制字节码

-XX:MaxPermSize=8m

-XX: Max Meta space Size=8m

```java
ClassWriter cw = new ClassWriter(0);
cw.visit(Opcodes.v1_8,Opcodes.ACC_PUBLIC,"class" + i,null,java/lang/object,null);
byte[] code = cw.toByteArray();
test.defineClass("Class" + i,code,0,code.length)
```

在运行期间动态生成类的字节码文件，完成动态的类加载。我们的代理技术里面广泛的运用了这种字节码的动态生成技术。

垃圾回收也是元空间管理了。

==运行池常量池==

常量池：给虚拟机指令找到方法名，类名呀，参数类型，字面量等信息

运行时常量池：常量池是*.class文件中的，当该类被加载，它的常量池信息就会放入运行时常量池，并把里面的符号地址变为真实地址

常量池中的信息，都会被加载到运行时常量池中，这时a b ab 都是常量池中的符号，==还没有变为java对象==。当主方法执行时，==ldc #2 会把a符号变为"a"字符串对象==，当执行完String s1 = "a"这行代码，==串池中就会有"a"这个字符串对象放入串池==。这个==行为上是一个懒惰的==。==利用串池的机制，来避免重复创建字符串对象，字符串变量拼接的原理是StringBuilder（1.8），字符串常量拼接的原理是编译期优化==。可以使用intern方法，将字符串对象尝试放入串池。

==String table（串池）==

数据结构上是哈希表，不能扩容。只会存在一份对象。不可重复

二进制字节码，第一部分是类的基本信息，类中的常量池constant pool，类方法定义包含了虚拟机指令

```shell
javap -v HelloWorld.class
```

位置不同，在1.8变为了堆中，而1.6在方法区中的那个永久代常量池中

==串池垃圾回收GC==，当一个对象没调用，而且，占用了大量内存就会触发垃圾回收

StringTableSize：桶个数

串池唯一性可以减少堆内存的使用

****

## 直接内存：系统内存

直接内存是系统内存和java虚拟机共享的一个一个内存

- 常见与nio操作，用于数据缓存区
- 分配回收成本较高，但读写性能高
- 不受jvm内存回收机制管理

这个是java底层的一个类来实现的

==虚方法调用==

invokevirtual 这个就是多态的底层原理

当执行invokevirtual指令时，

1. 先通过栈帧中的对象引用找到对象
2. 分析对象头，找到对象实际class
3. class结构中有vtable，他在类加载的链接阶段就已经个体根据方法的重写规则生成好了
4. 查表得到方法的具体地址
5. 执行方法的字节码



## 垃圾回收

引用计数法，可达性分析算法

java中通过可达性分析算法来探索可以回收的变量。

根对象

==五种引用==

软引用，内存不足回收。弱引用，不管内存够不够回收

- 场景

以下这两个都得配合引用队列使用

虚引用，调用类来释放直接内存

终结器引用。object.finallize()方法

==垃圾回收算法==：标记清除，速度快，空间不连续，容易造成碎片。标记整理：先标记然后整理，整理涉及到对象移动。速度慢。复制算法：两个区，一个from，在这个区中先是标记，然后清除，在全部复制给一个to区，然后两个区交换位置。缺点占用空间多

==分代回收==：新生代，划分为伊甸园，幸存区from，幸存区to。在新生代中，是一些存活周期短的垃圾。老年代，存活周期长，在新生代长期存活的对象，根据寿命晋升到老年代，随着越来越多的垃圾，MinorGC的触发已经满足不了对象的创建，触发fullGC这个会清理全部的代。

新生代使用的是复制，老年代标记整理

==分代回收要点：==对象首先分配在伊甸园中，新生代空间不够了，触发minor gc，伊甸园和from存活的对象使用copy复制到to中，存活的对象的年龄加一并且交换from to

minor gc 会引发stop the world这个意思是对象移动了，导致其他的用户线程，让我的垃圾工作线程先工作，完了，用户线程恢复运行。

当我们的对象寿命超过阈值，会晋升至老年代，最大寿命15（4bit），不同的垃圾回收器，没到15也会被回收

当老年代空间不足，先尝试minor gc，空间还是不足，那我就触发full gc。

大对象自己晋升，新生代放不下，晋升老年代。	

线程中的一个线程out of memory error不会导致整个进程意外错误

相关vm参数：-Xms:堆初始大小；

-Xmx或者-XX:MaxHeapSize=size：堆最大大小

-Xmn或者（-XX:NewSize=size+-XX:MaxNewSize=size）新生代大小

-XX:InitialSurvivorRatio-=ratio和-XX:+UseAdapticeSizePolicy 幸存区比例动态

-XX:SurvivorRatior=ratio 幸存区比例

-XX:MaxTenuringThreshold=threshold 晋升阈值

-XX:+PrintTenuringDistribution 晋升详情

-XX:+PrintGCDetails -verbose:gc GC详情

-xx:+scavengeBeforeFullGC fullGC前的minorgc

==垃圾回收器==

1. 串行，单线程的垃圾回收器
   1. +UseSerialGC = serial + SerialOld
   2. 堆内存较小，适合个人电脑cpu个数少
2. 吞吐量
   1. UseParallelGC UseParalleloldGC 开一个都会开启
   2. 多线程，堆内存较大，多核
   3. 这个讲究的是垃圾回收时间和整个运行时间占比最小
3. 响应时间优先
   - 跟多线程，可以和用户线程并行执行
   - 并行用户线程产生浮动垃圾，得预留位置
   - STW时间越短越好。

进程会停到一个安全点



## heap

堆里面放的是对象

==通过new关键词==，创建对象都会使用堆内存。

特点

- 它是线程共享的，堆中对象都需要考虑线程安全问题
- 有垃圾回收机制

### 堆内存溢出问题

jps工具

- 查看当前系统中有哪些进程。

jmap工具 jamp -heap进程id

- 查看堆内存占用情况

jconsole工具

- 图形界面，多功能的检测工具，可以连续监测。

==案例==

- 垃圾回收，内存占用依旧很高

jvisualvm 可视化工具来检测堆内存，什么东西在占用

堆转储

## jvm Stacks

调用方法时

这个栈是线程运行需要的内存空间，

而栈帧是每个方法运行时需要的内存，每个线程只能有一个活动栈，对应这当前正在执行的那个方法

==方法内的局部变量是否线程安全==

-  如果方法内局部变量没有逃离方法的作用范围，它是线程安全的
- 如果这个局部变量引用了对象，并逃离方法的作用方法，需要考虑线程安全

==栈内存溢出==

- 栈帧过多导致栈内存溢出
- 栈帧过大

==cpu占用过多==

```shell

ps H -eo pid,tid,%cpu | grep 32655
jstack 进程id
可以根据线程id找到有问题的线程，进一步定位到问题代码的源码行数

```

==死锁问题==

## pc register

程序计数器

记住下一条jvm指令的执行地址

计数器是线程私有的

不会存在内存溢出

## native Method stacks



# 执行引擎

interpreter

jit compiler

GC

# 还需要一些操作系统提供的一些接口和实现==本地方法接口==