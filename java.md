##### IDEA项目结构

项目->模块->包->.java文件

##### 快速生成语句

快速生成main方法：psvm,回车

快速生成输出语句：sout,回车

##### 内容辅助键

ctrl+alt+space(内容提示、代码补全)

##### 快捷键

注释

单行：选中代码，ctrl+/

多行：选中代码，ctrl+shift+/

格式化(“让格式看起来好看一点”) ctrl+alt+L



### 数组

##### java内存分配：

栈内存：存储局部变量，使用完毕，立即消失

例：int[] arr

堆内存：存储new出来的内容(对象、实体)，使用完毕，会在垃圾会抽起空闲时被回收。

例： new int[3]

##### 静态初始化

int[] arr = new int[]{1,2,3};

or

int[] arr = {1,2,3};

##### 获取数组元素数量

arr.length



### 方法

##### 定义格式：

public static 返回值类型 方法名（参数）{  //为了方便在静态的main方法内调用，也采用静态方法

//方法体

return 数据；

}

##### 调用格式：

方法名（）；

##### 注意事项：

方法不能嵌套定义

##### 方法重载：

同一个类下，方法名相同，但参数不同的情况

与返回值无关，即不能通过返回值不同来判断

##### 方法的参数传递：

基本类型，形参不会影响外部实参的值

引用类型，形参会影响实参的值



#### 类

##### 成员变量

类方法外，堆内存，有默认初始化值

##### 局部变量

方法内或方法声明上，栈内存，无默认初始化值



#### API(Application Programming Interface)

JAVA API:JDK中提供的各种功能的java类



#### String

字符串底层原理和效果上均相当于字节数组byte[]

JDK9及以后

##### 构造方法

无参|字符数组|字节数组|直接赋值

推荐直接赋值的方式

##### 对象特点

以引号方式给出同样的String序列，JVM只会建立一个String对象-->在常量池里

如果是数组方式给出的，会在内存堆里建立相应的存储空间

##### 字符串比较

==：地址值（对所有引用类对象）

.equals方法：值



#### StringBuilder

意义:字符串拼接时，会在常量池中构造多个常量，为避免这种情况，选择可变类型的字符串



#### ArrayList



#### 包

其实就是文件夹，对类进行分类管理

定义格式 package 包名； （多级包用.分开）



#### 权限修饰符

| 修饰符    | 同一个类中 | 同一个包中子类、无关类 | 不同包的子类 | 不同包的无关类 |
| --------- | ---------- | ---------------------- | ------------ | -------------- |
| private   | ok         |                        |              |                |
| 默认      | ok         | ok                     |              |                |
| protected | ok         | ok                     | ok           |                |
| public    | ok         | ok                     | ok           | ok             |

被final修饰的方法，不能被重写

被final修饰的类，不能被继承

被final修饰的基本类型变量，不能被再次赋值

被final修饰的引用类型变量，类型变量地址不能变，但内容可以变



static修饰变量，对类内所有变量共享，可用类名访问

static修饰方法，只能调用静态的变量和方法



#### 多态

##### 父类引用指向子类对象

成员变量：编译时要看左边的父类，运行时也要看左边

成员方法：编译看左边，运行时看右边子类

原因：成员方法有重写，成员变量没有

##### 多态的好处和弊端

好处：提供良好的扩展性，定义方法时使用父类型作为参数，使用时传入子类型即可

弊端：不能访问子类特有功能

弊端消除：向下转型（其实就是强转为子类对象）



#### 抽象类

abstract修饰

不能直接创建对象，但可以用多态的形式创建。

抽象类的子类，要不本身为抽象类，要不重写抽象类中所有抽象方法



#### 接口

#### 接口成员特点

成员变量默认被final和static修饰，是常量，可以直接通过接口名访问。

接口里没有构造方法

接口里不能有抽象方法



接口和接口之间，可以多继承extends

类可以同时implements多个接口



#### 形参和返回值

##### 类名作为形参和返回值

类名作为形参--需要该类的对象

类型作为返回值--需要返回该类的对象

##### 抽象类名作为形参和返回值

多态的形式

##### 接口名作为形参和返回值

多态的形式，其实要的是该接口的实现类对象





#### 内部类

内部类可访问外部类成员，包括私有

外部类要访问内部类成员，必须创建对象

外界使用：Outer.Inner oi = new Outer().new Inner();

一般而言，内部类会用private修饰，不希望外界能够访问该类

##### 局部内部类

一个方法内部定义类，外界无法访问，只能通过外部类调用该方法才能访问（该方法内部创建对象）

##### 匿名内部类

本质是一个匿名类对象，继承了某个类或者实现了某个接口

格式：

new 类名或者接口名（）{

​       //重写方法

}； 

new 类名或者接口名（）{

​       //重写方法

}.方法名（）；

 类名或者接口名 i = new 类名或者接口名（）{

​       //重写方法

}； 



#### 常用API(类)

Math

System

Object



#### 异常

Throwable

Error ||Exception(RuntimeException||非RuntimeException)



Throwable对象：

getMessage()||toString()||printStackTrace()

详细信息||简短描述||将异常错误原因输出在控制台



#### 集合

##### Collection

add remove clear contains isEmpty size等方法

遍历：迭代器

iterator<E>  iterator()

迭代器方法 

E next()

boolean hasNext()

在使用迭代器过程中进行修改，会报并发修改异常

##### List

有序：存储和取出的元素一致

可重复：存储的元素可以重复

特有方法:

void add(int index, E element)

E remove(int index)

E set(int index, E element)

E get(int index)

###### 列表迭代器ListIterator

hasPrevious()||previous()

允许沿任意方向遍历，在迭代期间可修改列表



#### 增强for循环

for(元素数据类型 变量名：数组或Collection集合){

}



#### LinkedList集合的特有功能

void addFirst(E e)

void addLast(E e)

E getFirst()

E getLast()

E removeFirst()

E removeLast()



#### Set

`Set<String> set = new HashSet<String>();`

不包含重复元素

遍历：增强for||迭代器



#### LinkedHashSet

是由哈希表和链表实现的Set接口，具有可预测的迭代次序

链表保证元素有序

哈希表保证元素不重复



#### TreeSet

元素有序存储，分为以下两种情况：

TreeSet():根据其元素的自然排序进行排序

TreeSet(Comparator comparator):根据指定的比较器进行排序

没有带索引的方法，所以不能使用普通for循环遍历，但由于是Set集合，所以不包含重复元素的集合

类需要implements一个Comparable接口

实现一个compareTo方法：返回正数，this排在后面，参数内的对象排在前面，负数相反，0则两者相同。



#### 泛型方法

范例：public <T> void show(T t){ }

#### 类型通配符

List<?> #元素可为任意类型

List<? extends Number>类型通配符上限，Number或其子类型

List<? super Number>类型通配符下限，它表示Number或其父类型



#### 可变参数

可变参数定义格式

方法名（数据类型...变量名）{}  //三个英文句号

变量其实是一个数组

方法有多个参数时，可变参数要放到最后



#### Map

接口Map<key,value>

通过多态的方式用实现类HashMap创建,如果需要有序的，用TreeMap

添加元素：put方法，保证键的唯一性：会有替换操作

put||remove||clear||containsKey||containsValue||isEmpty||size

get||Set<K> keySet()||Collection<V> values()

Set<Map.Entry<K,V>> entrySet() : 获取所有键值对对象的集合

对Map.Entry<K,V>类型，getkey()||getValue()



#### Collections类

常用方法sort||reverse||shuffle

#### File

createNewFile||mkdir||mkdirs

获取和判断

isDirectory()||isFile()||exists()||getAbsolutePath()||getPath()||getName()||list()||listFiles()

删除功能

delete()

如果一个目录中有内容，不能直接删除目录，而应先删除目录内容再删目录



#### 字节流

FileOutputStream

1、调用系统功能创建了文件

2、创建了字节输出流对象

3、让字节输出流对象指向创建好的文件

最后要close掉系统资源

#### 字节流

写数据的方式

write(int b)

write(byte[] b)

write(byte[] b, int off, int len)



字节流实现换行：

不同系统：

windows:  \r\n

linux: \n

mac: \r



字节缓冲流

BufferedOutputStream(OutputStream)

BufferedInputStream(InputStream)

字符流

OutputStreamWriter(OutputStream)

InputStreamReader(InputStream)

要刷新 flush方法









#### 字符缓冲流

##### BufferedWriter

newLine()写行分隔符

##### BufferedReader

readLine()读一行文字，但不读入换行符的|null：到达结尾





#### 标准输入流

InputStream //字节流

InputStreamReader //字符流

//一次读入一行，是字符缓冲输入流的特有方法

BufferedReader br = new BufferedReader(new InputStreamReader(System in))

String line = br.readLine()

int i = Integer.parseInt(br.readLine())

#####  java提供了一个类 Scanner



#### 标准输出流

PrintStream ps = System.out; //标准输出流



#### 字节打印流

PrintStream ps = new PrintStream(filename)

ps.print(97)

#### 字符打印流

PrintWriter pw = new PrintWriter(filename) //还有第二个参数，可以设置自动刷新

ps.write("hello")

pw.flush()



#### 对象序列化流

ObjectOutputStream(OutputStream out)

void writeObject(object obj)//将指定对象写入ObjectOutputStream

对象需要完成Serializable接口，但没有方法需要重写

##### 反序列化流

ObjectInputStream(InputStream in)

Object readObject()

注意有一个ID值，如果写入|读取前后不一样会报错，可以给对象所属的类加一个ID成员变量：

private static final long serialVersionUID = 42L

被关键字 transient 修饰的成员变量，不参与序列化过程



#### Properties

Properties pro = new Properties(); //没有泛型

prop.put("key","value");

##### 特有方法

setProperty(String key, String value)

String getProperty(String key)

Set<String> stringPropertyNames()

IO相关 load、store



### 多线程

方式1：定义继承Thread类，重写run()方法，创建类对象，start

设置获取线程名称 setName(String name)||getName()

设置线程优先级 setPriority(int 1-10)

方式2：implements Runable,和Thread类无关

给Thread类对象传一个实现Runable接口的类对象



#####  线程控制

sleep

join等待线程死亡

setDaemon(boolean on)标记为守护线程，当运行的线程都是守护线程时，java虚拟机将退出



##### 线程同步

同步代码块

synchronized(任意对象){

​    多条语句操作共享数据的代码

}

同步方法

非静态方法：锁是this

用synchronized修饰方法

静态方法：锁是类字节码文件对象:类名.class



##### 线程安全的类

StringBuffer

Vector

Hashtable

里面的方法加了synchronized

注：可以通过

List<String> list = Collections.synchronizedList(new ArrayList<String>())

获取线程安全的类



##### Lock锁

Lock lock = new ReentrantLock();

lock.lock() //加锁 try代码块中

lock.unlock() //释放锁 finally代码块中



线程等待或唤醒

wait()

notify()

notifyAll()





 





