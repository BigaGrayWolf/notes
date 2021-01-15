# C++基础知识

## 三大特性

1.封装性(Encapsulation) 

  所谓封装，就是将某些东西包装和隐藏起来，让外界无法直接使用，只能通过某些特定的方式才能访问。OO将万物都视为“对象”(Object)，任何对象都具有特性和行为。我们将其特性称为“成员变量” (MemberVarible)，将其行为称之为“成员函数"(Member Function)，被封装的特性只能通过特定的行为去访问。 

2.继承

   通过继承机制，可以利用已有的[数据类型](http://baike.baidu.com/view/675645.htm)来定义新的数据类型。所定义的新的[数据类型](http://baike.baidu.com/view/675645.htm)不仅拥有新定义的成员，而且还同时拥有旧的成员。我们称已存在的用来派生新类的类为基类，又称为父类。由已存在的类派生出的新类称为[派生类](http://baike.baidu.com/view/535532.htm)，又称为子类。

3.多态

   通过父类指针的引用或者指针来实现运行时的一个动态绑定，它不同于重载的静态绑定。

## C++内存区域

全局变量存放在静态存储区,位置是固定的。 局部变量在栈空间,栈地址是不固定的。

- 栈：就是那些由编译器在需要的时候分配，在不需要的时候自动清楚的变量的存储区。里面的变量通常是局部变量、函数参数等。
- 堆：就是那些由new分配的内存块，他们的释放编译器不去管，由我们的应用程序去控制，一般一个new就要对应一个delete。如果程序员没有释放掉，那么在程序结束后，操作系统会自动回收。
- 自由存储区：就是那些由malloc等分配的内存块，他和堆是十分相似的，不过它是用free来结束自己的生命的。
- 全局存储区（静态存储区）：**全局变量和静态变量的存储是放在一块的**，初始化的全局变量和静态变量在一块区域， 未初始化的全局变量和未初始化的静态变量在相邻的另一块区域。程序结束后由系统释放。
- 常量存储区：这是一块比较特殊的存储区，他们里面存放的是常量，不允许修改。

## static关键字

### 1.全局静态变量

- 在全局变量前加上关键字static，全局变量就定义成一个全局静态变量.
- 存储在静态存储区，整个程序运行期间一直存在
- 初始化：未初始化的全局静态变量会被自动初始化为0，因为c++在程序运行前会对静态存储区清0；
- 作用域：在声明它的文件之外是不可见的，从定义之处开始到文件结尾

### 2. 局部静态变量

- 在局部变量前加上static
- 存储在静态存储区
- 初始化同上
- 作用域：仍为局部作用域，只是定义它的函数或者语句块结束的时候局部静态变量并没有被销毁，一直留在内存中，直到改函数再次被调用。
- 局部静态变量的值不会改变。

### 3.静态函数

- 在函数返回类型前加static，函数就定义为静态函数。
- 函数的声明的定义默认情况下都是extern的，**但静态函数只在本``cpp``内使用，不会和其他``cpp``中的同名函数冲突 **

### 4.类的静态成员

- 类的静态成员为所有对象所共享的，为类所有。不是某个对象的成员。
- 可以实现多个对象之间的数据共享。
- 只存储一处，供所有对象试用。

``` cpp
{
public:
	static int a;
	A() {};
	~A() {};
};
int A::a = 0;
```

静态成员的定义需要在类外进行。

另外，类的函数，虚表也是类所有的。每个对象中都有一个虚表指针来指向这个虚表（对于虚函数的对象来讲）。

### 5.类的静态函数

- 属于类。对静态成员的引用不需要对象名
- **静态成员函数的实现不能直接引用类中声明的非静态成员，只可以引用类中的声明的静态成员。**这是因为静态成员**函数没有this指针**，无法访问类中的非静态成员。但是可以通过对象引用来访问非静态成员

``` cpp
{
public:
	static int a;
	int b = 0;
	A() {};
	static void print() {
		cout << a << endl;
	}
	/*static void printb() {
		cout << b << endl; //报错,不能引用非静态变量
	}*/
	static void printb(const A& B) {
		cout << B.b << endl;
	}
	~A() {};
};
int A::a = 1;
```



## 四种cast转换

C++中四种类型转换是：static_cast, dynamic_cast, const_cast, reinterpret_cast。全部用于指针

### 1.const_cast

用于**移除**类型的``const``、``volatile``和``__unaligned``属性

``` cpp
{
	const int constant = 21;
	int* modifier = &constant;//报错,非法转换 from const int* to int*
	system("pause");
	return 0;
}
```



```cpp
int main()
{
	const int constant = 21;
	const int* const_p = &constant;
	int* modifier = const_cast<int*>(const_p);
	*modifier = 7;
	system("pause");
	return 0;
}
```

常量指针被转换成非常量指针，并且**仍然指向原来的对象**；常量引用被转换成非常量引用，并且仍然引用原来的对象。

### 2.static_cast（编译时检查）

用于各种隐式转换，比如非const转const，void*转指针等, **static_cast能用于多态向上转化，如果向下转能成功但是不安全，**结果未知；

- **编译时类型检查**:static_cast < type-id > ( expression )，该运算符把expression转换为type-id类型，**但没有运行时类型检查来保证转换的安全性**，它主要有如下几种用法：
  1. 用于基本数据类型之间的转换，如把int转换为char，把int转换成enum，**但这种转换的安全性需要开发者自己保证**（这可以理解为保证数据的精度，即程序员能不能保证自己想要的程序安全），如在把int转换为char时，如果char没有足够的比特位来存放int的值（int>127或int<-127时），那么static_cast所做的只是简单的截断，及简单地把int的低8位复制到char的8位中，并直接抛弃高位。
  2. 把空指针转换成目标类型的空指针,是极其不安全的。
  3. 把任何类型的表达式类型转换成void类型
  4. 用于类层次结构中父类和子类之间指针和引用的转换。
- 对于以上第（4）点，存在两种形式的转换，即**上行转换（子类到父类）和下行转换（父类到子类）**。
- **对于static_cast，上行转换是安全的，而下行转换是不安全的。**因为static_cast的转换是粗暴的，它仅根据类型转换语句中提供的信息（尖括号中的类型）来进行转换。
- 这种转换方式对于上行转换，由于子**类总是包含父类的所有数据成员和函数成员**，因此从子类转换到父类的指针对象可以没有任何顾虑的访问其（指父类）的成员。**而对于下行转换为什么不安全，是因为static_cast只是在编译时进行类型检查**，没有运行时的类型检查，因为转换失败时它不返回NULL。

### 3. dynamic_cast（运行时类型检查）

[C++中static_cast和dynamic_cast强制类型转换](https://blog.csdn.net/lovemysea/article/details/79559800)

用法：同static_cast

- dynamic_cast主要用于类层次结构中父类和子类之间指针和引用的转换。
- **由于具有运行时类型检查，因此可以保证下行转换的安全性**
- 何为安全性？即转换成功就返回转换后的正确类型指针，**如果转换失败，则返回NULL，**之所以说static_cast在下行转换时不安全，是因为即使转换失败，它也不返回NULL。
- 对于上行转换，dynamic_cast和static_cast是一样的。
- 在C++中，一般是可以用父类指针指向一个子类对象，如parent* P1 = new Children(); **但这个指针只能访问父类定义的数据成员和函数**，这是C++中的静态联翩。

``` cpp
#include <iostream>
#include <typeinfo>
using namespace std;
class A {
public:
	virtual void print() {
		cout << "A" << endl;
	}
};
class B :public A {
	void print() {
		cout << "A-B" << endl;
	}
	virtual void printB() {
		cout << "B" << endl;
	}
};

int main()
{
	A* pA = new B;
	pA->print();//类A中有这个函数，但因为是虚函数，会在运行确认调用B的虚表，所以输出 A-B
	pA->printB();//报错，因为pA是类A的指针，并没有B中的函数
	system("pause");
	return 0;
}
```



- 在上行转换中，static_cast和dynamic_cast效果是一样的，而且都比较安全，因为向上转换的对象一般是指向子类对象的子类类型指针；
- 而在下行转换中，由于可以定义就不同了指向子类对象的父类类型指针，同时**static_cast只在编译时进行类型检查，而dynamic_cast是运行时类型检查**，则需要视情况而定。

``` cpp
class Base  
{  
    virtual void fun(){}  
};  
class Derived :public Base
{
};
Base *P = new Derived();
Derived *pd1 = static_cast<Derived *>(P);
Derived *pd2 = dynamic_cast<Derived *>(P);
```

以上转换都能成功。因为指向的是一个子类对象。

但是，如果 **P** 指向的不是子类对象，而是父类对象，如下所示：

``` cpp
Base *P = new Base;  
Derived *pd3 = static_cast<Derived *>(P);
Derived *pd4 = dynamic_cast<Derived *>(P);
```

- 在以上转换中，static_cast转换在编译时不会报错，也可以返回一个子类对象指针（假想），但是这样是不安全的，在运行时可能会有问题，因为子类中包含父类中没有的数据和函数成员，这里需要理解转换的字面意思，转换是什么？转换就是把对象从一种类型转换到另一种类型，如果这时用 pd3 去访问子类中有但父类中没有的成员，就会出现访问越界的错误，导致程序崩溃。

- 而dynamic_cast由于具有**运行时类型检查功能，它能检查P的类型，由于上述转换是不合理的，所以它返回NULL。**对于引用抛``std::bad_cast``异常。

#### dynamic_cast 什么情况下不能转换？

1、两个类要在同一个继承体系才可以转换，而且要是public继承，非public继承不可转换

2、类没有虚函数不可转换

### 4.reinterpret_cast

几乎什么都可以转，比如将``int``转指针，可能会出问题，尽量少用；

转换的原理是重解释。

用法：``reinpreter_cast<type-id> (expression)``

- type-id必须是一个指针、引用、算术类型、函数指针或者成员指针。它可以把一个指针转换成一个整数，也可以把一个整数转换成一个指针（先把一个指针转换成一个整数，在把该整数转换成原类型的指针，还可以得到原先的指针值）。

### 5.为什么不使用C的强制转换

C的强制转换表面上看起来功能强大什么都能转，但是转化不够明确，不能进行错误检查，容易出错。

## C/C++ 中指针和引用的区别

引用的底层实现是指针。

1. 指针有自己的一块空间，而引用只是一个别名；
2. 使用sizeof看一个指针的大小是4，而引用则是被引用对象的大小；
3. 指针可以被初始化为NULL，而引用必须被初始化且必须是一个已有对象 的引用；
4. 作为参数传递时，指针需要被解引用才可以对对象进行操作，而直接对引 用的修改都会改变引用所指向的对象；
5. 可以有const指针，但是没有const引用；
6. 指针在使用中可以指向其它对象，但是引用只能是一个对象的引用，不能 被改变；
7. 指针可以有多级指针（**p），而引用至于一级；
8. 指针和引用使用++运算符的意义不一样；
9. 如果返回动态内存分配的对象或者内存，必须使用指针，引用可能引起内存泄露。

## 智能指针

C++里面的四个智能指针: auto_ptr, shared_ptr, weak_ptr, unique_ptr 其中后三个是c++11支持，并且第一个已经被11弃用。

### 1.auto_ptr（c++98的方案，cpp11已经抛弃）

采用所有权模式，可以剥夺。

``` cpp
auto_ptr< string> p1 (new string ("I reigned lonely as a cloud.”));
auto_ptr<string> p2;
p2 = p1; //auto_ptr不会报错.
```

此时不会报错，p2剥夺了p1的所有权，但是当程序运行时访问p1将会报错。所以auto_ptr的缺点是：**存在潜在的内存崩溃问题！**

### 2. unique_ptr（替换auto_ptr）

unique_ptr实现**独占式拥有**或严格拥有概念，保证**同一时间内只有一个智能指针可以指向该对象**。一样采取所有权

``` cpp
unique_ptr<string> p3 (new string ("auto"));   //#4
unique_ptr<string> p4；                       //#5
p4 = p3;//此时会报错！！
```

unique_ptr不能使用左值进行赋值操作，因为这会造成同时有两个指针指向同一个对象。但可以进行右值赋值。

```cpp
unique_ptr<string> pu1(new string ("hello world"));
unique_ptr<string> pu2;
pu2 = pu1;  // #1 not allowed
unique_ptr<string> pu3;
pu3 = unique_ptr<string>(new string ("You"));   // #2 allowed
```

#1为将一个左值pu1赋值pu2，赋值后pu1不会消失。就会存在两个指针指向同一个对象。所以编译报错

#2为将一个临时变量赋值给pu3，合法。

```cpp
unique_ptr<string> ps1, ps2;
ps1 = demo("hello");
ps2 = move(ps1);
ps1 = demo("alexia");
cout << *ps2 << *ps1 << endl;
```

或者使用move将左值转换为右值使用。

### 3. shared_ptr

- 头文件**#include\<memory\>**
- shared_ptr实现共享式拥有概念。多个智能指针可以指向相同对象，该对象和其相关资源会在“**最后一个引用被销毁**”时候释放。
- 使用计数机制来表明资源被几个指针共享。可以通过成员函数**use_count()**来查看资源的所有者个数。
- 除了可以通过new来构造，还可以通过传入auto_ptr, unique_ptr,weak_ptr来构造。
- 当我们调用release()时，当前指针会释放资源所有权，计数减一。**当计数等于0时，资源会被释放。**

成员函数：

- use_count 返回引用计数的个数

- unique 返回是否是独占所有权( use_count 为 1)

- swap 交换两个 shared_ptr 对象(即交换所拥有的对象)

- reset 放弃内部对象的所有权或拥有对象的变更, 会引起原有对象的引用计数的减少。既当前这个指针不在指向原有对象，原有对象引用计数减少。

- get 返回内部对象(指针), 由于已经重载了()方法, 因此和直接使用对象是一样的.如 shared_ptr<int> sp(new int(1)); sp 与 sp.get()是等价的

### 4. weak_ptr

- weak_ptr 它指向一个 shared_ptr 管理的对象.
- 它只可以从一个 shared_ptr 或另一个 weak_ptr 对象构造, **它的构造和析构不会引起引用记数的增加或减少。**
- weak_ptr是用来解决shared_ptr相互引用时的死锁问题。如果说两个shared_ptr相互引用,那么这两个指针的引用计数永远不可能下降为0,资源永远不会释放。
- 它是对对象的一种弱引用，不会增加对象的引用计数，和shared_ptr之间可以相互转化，shared_ptr可以直接赋值给它，它可以通过调用lock函数来获得shared_ptr。
- **不能通过weak_ptr直接访问对象的方法，**比如B对象中有一个方法print(),我们不能这样访问，pa->pb__->print(); 英文pb_是一个weak_ptr，应该先把它转化为shared_ptr,如：shared_ptr p = pa->pb_.lock(); p->print();

```cpp
class B;
class A{
public:
	weak_ptr<B> pb_;
	~A()
	{
		cout << "A delete\n";
	}
};
class B{
public:
	shared_ptr<A> pa_;
	~B()
	{
		cout << "B delete\n";
	}
};
```



## 数组和指针的区别

| 指针                                                         | 数组                                 |
| ------------------------------------------------------------ | ------------------------------------ |
| 保存数据的地址                                               | 保存数据                             |
| 间接访问数据，首先获得指针的内容，然后将其作为地址，从该地址中提取数据 | 直接访问数据，                       |
| 通常用于动态的数据结构                                       | 通常用于固定数目且数据类型相同的元素 |
| 通过Malloc分配内存，free释放内存                             | 隐式的分配和删除                     |
| 通常指向匿名数据，操作匿名函数                               | 自身即为数据名                       |

### 野指针

野指针就是指向一个已删除的对象或者未申请访问受限内存区域的指针

## 大端和小端

所以针对一个32位的数值，如**0x1A2B3C4D**，总共四个字节，两个十六进制数表示一个字节，高位字节为**0x1A**，低位字节为**0x4D**；中间两个字节分别为**0x2B**和**0x3C**；

数值**0x1A2B3C4D**想要在计算机中正确使用，就必须要考虑在内存中将其对应的四个字节合理存储。假设内存的地址都是从低到高分配的，那么对于一个数值多个字节顺序存储就有两种存储方式：

**方式一**、数值的高位字节存放在内存的低地址端，低位字节存放在内存的高地址端：

内存低地址 --------------------> 内存高地址

0x1A | 0x2B | 0x3C | 0x4D

高位字节 <-------------------- 低位字节

**方式二**、数值的低位字节存放在内存的低地址端，高位字节存放在内存的高地址端：

内存低地址 --------------------> 内存高地址

0x4D | 0x3C | 0x2B | 0x1A

低位字节 --------------------> 高位字节



**方式一 ，**我们就称之为 大端模式；即**高位字节放在内存的低地址端**，低位字节放在内存的高地址端。

**方式二 ，**我们就称之为 小端模式；即**低位字节放在内存的低地址端**，高位字节放在内存的高地址端。



**画图更直观理解一下：**

![img](https://pic2.zhimg.com/80/v2-1a4d887d6b9a2701a50114e2eac530f5_1440w.jpg)

![img](https://pic3.zhimg.com/80/v2-28edbce28d58b8c4f4ff02fc1730929e_1440w.jpg)

**总结下：**

- 大端小端是不同的字节顺序存储方式，统称为**字节序**；
- **大端模式**，是指数据的高字节位 保存在 内存的低地址中，而数据的低字节位 保存在 内存的高地址中。这样的存储模式有点儿类似于把数据当作字符串顺序处理：地址由小向大增加，而数据从高位往低位放。和我们”从左到右“阅读习惯一致。
- **小端模式**，是指数据的高字节位 保存在 内存的高地址中，而数据的低字节位 保存在 内存的低地址中。这种存储模式将地址的高低和数据位权有效地结合起来，高地址部分权值高，低地址部分权值低，和我们的逻辑方法一致。

## 虚函数

c++允许定义一个基类指针，指向子类。但是这个指针只能调用基类中的函数，基类中的虚函数可能被子类重写。运行时根据指向的对象来确定实在子类还是基类的虚表中查找对应的函数。

子类继承父类时，会copy一份父类的虚表，若重写了某个虚函数就改变虚表中对应的虚函数地址。

``` cpp
#include <iostream>
#include <typeinfo>
using namespace std;
class A {
public:
	virtual void print() {
		cout << "A" << endl;
	}
};
class B :public A {
	void print() {
		cout << "A-B" << endl;
	}
	virtual void printB() {
		cout << "B" << endl;
	}
};

int main()
{
	A* pA = new B;
	pA->print();//调用B的虚表，所以输出 A-B
	pA->printB();//报错，因为pA是类A的指针，并没有B中的函数
	system("pause");
	return 0;
}
```

### 虚函数与析构函数

为什么析构函数必须是虚函数？为什么C++默认的析构函数不是虚函数 

- 将可能会被继承的父类的析构函数设置为虚函数，可以保证当我们new一个子类，然后使用基类指针指向该子类对象，**释放基类指针时可以释放掉子类的空间，防止内存泄漏。**
- C++默认的析构函数不是虚函数是因为虚函数需要**额外的虚函数表和虚表指针，占用额外的内存**。而对于不会被继承的类来说，其析构函数如果是虚函数，就会浪费内存。因此C++默认的析构函数不是虚函数，而是只有当需要当作父类时，设置为虚函数。

```c++
#include <iostream>
using namespace std;

class A {
public:
	virtual ~A() {
		cout << "delete A" << endl;
	}
};
class B:public A
{
public:
	
	~B() {
		cout << "delete B" << endl;
	}

private:

};
int main() {

	do {
		class A* ptr = new B;
		delete ptr;
		ptr = nullptr;
	} while (false);
	
	system("pause");
	return 0;
}
```

![image-20201015230939165](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\image-20201015230939165.png)

```c++
#include <iostream>
using namespace std;

class A {
public:
	~A() {
		cout << "delete A" << endl;
	}
};
class B:public A
{
public:
	
	~B() {
		cout << "delete B" << endl;
	}

private:

};
int main() {

	do {
		class A* ptr = new B;
		delete ptr;
		ptr = nullptr;
	} while (false);
	
	system("pause");
	return 0;
}
```

![image-20201015231025208](C:\Users\Admin\AppData\Roaming\Typora\typora-user-images\image-20201015231025208.png)

## 默认构造、析构函数

### 默认初始化

- 定义在**函数体内**的**内置对象**如果没有初始化，则其值未定义。
- 定义在函数体外的变量被初始化为0.

c++在程序开始前，会把静态存储区清0，所以会被初始化为0.

```c++
int a;
int main() {
	int b;
    string c;
	cout << a << endl;  //输出0，a被默认初始化为0.
    //cout << b << endl; //报错，使用了未初始化的局部变量“b”
	cout << c << endl;  //输出为空，调用了string的默认构造函数。
	system("pause");
}
```

### 构造函数

一个类会用一个或几个特殊的成员函数来控制其对象的初始化过程，这些函数被称为构造函数。

只要对象被创建，就会执行构造函数。

- 构造函数不能被声明为``const``的，因为当我们创建一个``const``对象时，直到构造函数完成初始化过程之后，对象才真正取得其“常量”的属性，因此构造函数在``const``对象的构造过程中可以向其写值。

- 构造函数不能被声明为虚函数

  C++对象在三个地方构建：（1）函数栈；（2）自由存储区，或称之为堆；（3）静态存储区。无论在那里构建，其过程都是两步：首先，分配一块内存；其次，调用构造函数。好，问题来了，如果构造函数是虚函数，那么就需要通过**``vtable``** 来调用，但此时面对一块 raw memeory，到哪里去找 **``vtable``** 呢？毕竟，**``vtable``** 是在构造函数中才初始化的啊，而不是在其之前。因此构造函数不能为虚函数。

  简单讲就是，如果构造函数时虚函数，则需要虚表来调用，而虚表这个时候又没有初始化，是空的。

### 合成的默认构造函数

类通过默认构造函数来控制默认初始化过程，默认构造函数无需任何实参。

- 如果类没有显示的定义构造函数，那么编译器会为我们隐式地定义一个默认构造函数。称为合成的默认构造函数。

合成构造函数的初始化类数据成员的规则：

- 如果存在类内的初始值（既定义时给定了初值``int a=1``），用它来初始化成员。
- 否则，默认初始化该成员。

### 合成的默认构造函数不适用于某些类

- 只有类没有声明任何构造函数时，编译器才会自动地生成默认构造函数。如果定义了自己的构造函数，还想要编译器合成的构造函数可以使用default

```c++
class A {
public:
	A() = default;
	A(string& b) {
		s = b;
	}
public:
	string s;
};
```



- 如果类内包含有**内置类型或者复合类型**（比如数组或者指针）的成员，则只有当这些成员全部被赋予了类内的初始值时，这个类才适合用合成的默认构造函数。因为**块内的内置类型不会被默认初始化**，所以需要给定初值，或者定义一个构造函数来显示的初始化。

```c++
#include<iostream>
#include<string>
using namespace std;
class A {
public:
	void print() {
		cout << a << endl;
	}
private:
	int a; //不会被合成的默认构造函数初始化
};
int main() {
	A test;
	test.print();  //输出为-858993460，取决与内存中的随机数值
	system("pause");
}

class A {
public:
	A() :a(0) {} //使用构造函数初始化
	void print() {
		cout << a << endl;
	}
private:
	int a=0; //使用初始值初始化
};
```

- 编译器不能为某些类合成默认的构造函数。例如当类中包含一个其他类型的成员且这个成员的类型没有默认的构造函数。

**也就是说要想使用默认的构造函数，类的数据成员必须都能够被默认初始化。**

### 拷贝，赋值，析构

若不主动定义这些操作，编译器将替我们合成它们，但对于某些类来说合成的版本无法正常工作。

**特别是类需要分配类对象之外的资源时。**也就是通过new动态分配内存的时候，不能使用合成的函数。

- 所以当类内有new分配的内存时，我们需要定义自己的析构函数。

## RTTI机制

RTTI是”Runtime Type Information”的缩写，意思是运行时类型信息，它提供了运行时确定对象类型的方法。

### 1. ``typeid``函数

输出C++内置数据类型

```cpp
     short s = 2;
     unsigned ui = 10;
     int i = 10;
     cout<<typeid(s).name()<<endl; // short
     cout<<typeid(ui).name()<<endl; // unsigned int
     cout<<typeid(i).name()<<endl; // int 
```

也可以输入自己创建的类对象

```cpp
#include <iostream>
#include <typeinfo>
using namespace std;

class A
{
public:
     void Print() { cout<<"This is class A."<<endl; }
};

class B : public A
{
public:
     void Print() { cout<<"This is class B."<<endl; }
};

struct C
{
     void Print() { cout<<"This is struct C."<<endl; }
};

int main()
{
     A *pA1 = new A();
     A a2;

     cout<<typeid(pA1).name()<<endl; // class A *
     cout<<typeid(a2).name()<<endl; // class A

     B *pB1 = new B();
     cout<<typeid(pB1).name()<<endl; // class B *

     C *pC1 = new C();
     C c2;

     cout<<typeid(pC1).name()<<endl; // struct C *
     cout<<typeid(c2).name()<<endl; // struct C

     return 0;
}
```

### 2.RTTI的核心

RTTI会在运行时类型检查，在C++层面主要体现在``dynamic_cast``和``typeid``,VS中虚函数表的-1位置存放了指向type_info的指针。对于存在虚函数的类型，``typeid``和dynamic_cast都会去查询type_info

```cpp
#include <iostream>
#include <typeinfo>
using namespace std;

class A
{
public:
     void Print() { cout<<"This is class A."<<endl; }
};

class B : public A
{
public:
     void Print() { cout<<"This is class B."<<endl; }
};

int main()
{
     A *pA = new B();
     cout<<typeid(pA).name()<<endl; // class A *
     cout<<typeid(*pA).name()<<endl; // class A
     return 0;
}
```

上面的``pA``指向的是``class B``但输出是``class A``这是因为``class A``没有虚函数，``typeid``发生在编译期。

```cpp
#include <iostream>
#include <typeinfo>
using namespace std;

class A
{
public:
     virtual void Print() { cout<<"This is class A."<<endl; }
};

class B : public A
{
public:
     void Print() { cout<<"This is class B."<<endl; }
};

int main()
{
     A *pA = new B();
     cout<<typeid(pA).name()<<endl; // class A *
     cout<<typeid(*pA).name()<<endl; // class B
     return 0;
}
```

当A中有虚函数时，``typeid``是运行时期的事情，是动态类型。

1. **当类中不存在虚函数时，``typeid``是编译时期的事情**

2. **当类中存在虚函数时，``typeid``是运行时期的事情**

   使用type_info类中重载的==和!=比较两个对象的类型是否相等
   这个会经常用到，通常用于比较两个带有虚函数的类的对象是否相等

   

### 3. dynamic_cast机制

```cpp
#include <iostream>
#include <typeinfo>
using namespace std;

class A
{
public:
     virtual void Print() { cout<<"This is class A."<<endl; }
};

class B
{
public:
     virtual void Print() { cout<<"This is class B."<<endl; }
};

class C : public A, public B
{
public:
     void Print() { cout<<"This is class C."<<endl; }
};

int main()
{
     A *pA = new C;
     //C *pC = pA; // Wrong 编译器会提示错误
     C *pC = dynamic_cast<C *>(pA);
     if (pC != NULL)
     {
          pC->Print();
     }
     delete pA;
}
```

当直接把``pA``赋值给``pC``时编译器会报错，需要使用``dynamic_cast``进行类型转换。**dynamic_cast主要用于多态，它允许在运行时刻进行类型转换，从而使程序在一个类层次结构中安全的转换类型。**

当类中存在虚函数时，编译器就会在类的成员变量中添加一个指向虚函数表的``vptr``指针，每一个class所关联的type_info object也经由virtual table被指出来，通常这个type_info object放在表格的第一个slot。

当我们进行dynamic_cast时，编译器会帮我们进行语法检查。

1. 如果指针的静态类型和目标类型相同，那么就什么事情都不做；
2. 否则，首先对指针进行调整，使得它指向``vftable``，并将其和调整之后的指针、调整的偏移量、静态类型以及目标类型传递给内部函数。
3. 其中最后一个参数指明转换的是指针还是引用。两者唯一的区别是，如果转换失败，前者返回NULL，后者抛出bad_cast异常。

```cpp
#include <iostream>
#include <typeinfo>
using namespace std;

class A
{
public:
     virtual void Print() { cout<<"This is class A."<<endl; }
};

class B : public A
{
public:
     void Print() { cout<<"This is class B."<<endl; }
};

class C : public A
{
public:
     void Print() { cout<<"This is class C."<<endl; }
};

void Handle(A *a)
{
     if (dynamic_cast<B*>(a))
     {
          cout<<"I am a B truly."<<endl;
     }
     else if (dynamic_cast<C*>(a))
     {
          cout<<"I am a C truly."<<endl;
     }
     else
     {
          cout<<"I am alone."<<endl;
     }
}

int main()
{
     A *pA = new B();
     Handle(pA);
     delete pA;
     pA = new C();
     Handle(pA);
     return 0;
}
```

转换失败时回返回NULL，可以作为if的判断

### 4. RTTI 实现底层实现的原理：

简单的讲，是在一个类的虚函数表里面添加了一个新的条目。

这个条目是 class所关联的type_info objec。

## C++中类继承

### 访问权限public、protected、private

- C++通过 public、protected、private 三个关键字来控制成员变量和成员函数的访问权限，
- 它们分别表示公有的、受保护的、私有的，被称为成员访问限定符。
- 在类的内部（定义类的代码内部），无论成员被声明为 public、protected 还是 private，**都是可以互相访问的，没有访问权限的限制。**
- 在类的外部（定义类的代码之外），**只能通过对象访问成员，并且通过对象只能访问 public 属性的成员**，不能访问 private、protected 属性的成员
- class 默认的继承为private，struct默认继承为public
- class默认访问权限是private，struct默认访问权限是public

| 访问     | public | protected | private |
| -------- | ------ | --------- | ------- |
| 同一个类 | yes    | yes       | yes     |
| 派生类   | yes    | yes       | no      |
| 外部的类 | yes    | no        | no      |

一个派生类继承了所有的基类方法，但下列情况除外：

- **基类的构造函数、析构函数和拷贝构造函数。**
- **基类的重载运算符。**
- **基类的友元函数。**

### 继承类型

``class derived-class: access-specifier base-class``

当一个类派生自基类，该基类可以被继承为 **public、protected** 或 **private** 几种类型。继承类型是通过上面讲解的访问修饰符 access-specifier 来指定的。

我们几乎不使用 **protected** 或 **private** 继承，通常使用 **public** 继承。当使用不同类型的继承时，遵循以下几个规则：

- **公有继承（public）：**当一个类派生自**公有**基类时，基类的**公有**成员也是派生类的**公有**成员，基类的**保护**成员也是派生类的**保护**成员，基类的**私有**成员不能直接被派生类访问，但是可以通过调用基类的**公有**和**保护**成员来访问。
- **保护继承（protected）：** 当一个类派生自**保护**基类时，基类的**公有**和**保护**成员将成为派生类的**保护**成员。
- **私有继承（private）：**当一个类派生自**私有**基类时，基类的**公有**和**保护**成员将成为派生类的**私有**成员。

### 多继承

多继承即一个子类可以有多个父类，它继承了多个父类的特性。

C++ 类可以从多个类继承成员，语法如下：

```cpp
class <派生类名>:<继承方式1><基类名1>,<继承方式2><基类名2>,…
{
<派生类类体>
};
```

### 多继承(环状继承)

A->D, B->D, C->(A，B)，例如：

```cpp
class D{......};
class B: public D{......};
class A: public D{......};
class C: public B, public A{.....};
```

这种继承会使得D创建两个对象，要解决上面问题就要用**虚拟继承格式**既虚继承

格式：class 类名: virtual 继承方式 父类名

```cpp
class D{......};
class B: virtual public D{......};
class A: virtual public D{......};
class C: public B, public A{.....};
```

``` cpp
#include <iostream>
 
using namespace std;
//基类
 
class D
{
public:
    D(){cout<<"D()"<<endl;}
    ~D(){cout<<"~D()"<<endl;}
protected:
    int d;
};
 
class B:virtual public D
{
public:
    B(){cout<<"B()"<<endl;}
    ~B(){cout<<"~B()"<<endl;}
protected:
    int b;
};
 
class A:virtual public D
{
public:
    A(){cout<<"A()"<<endl;}
    ~A(){cout<<"~A()"<<endl;}
protected:
    int a;
};
 
class C:public B, public A
{
public:
    C(){cout<<"C()"<<endl;}
    ~C(){cout<<"~C()"<<endl;}
protected:
    int c;
};
 
int main()
{
    cout << "Hello World!" << endl;
    C c;   //D, B, A ,C
    cout<<sizeof(c)<<endl;
    return 0;
}
```

![img](https://img-blog.csdn.net/20180829202729898?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2EzMTkyMDQ4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 友元函数

- 类的友元函数是定义在类外部，但有权访问类的**所有私有（private）成员和保护（protected）成员。**
- 尽管友元函数的原型有在类的定义中出现过(类中声明过)，但是**友元函数并不是成员函数。**

- 友元可以是一个函数，该函数被称为友元函数；
- 友元也可以是一个类，该类被称为友元类，在这种情况下，整个类及其所有成员都是友元。

如果要声明函数为一个类的友元，需要在类定义中该函数原型前使用关键字 **friend**，如下所示：

```cpp

class Box
{
   double width;
public:
   double length;
   friend class ClassTwo;          //友元类
   friend void printWidth( Box box );  //友元函数
   void setWidth( double wid );

};
```

**友元函数的使用**

因为友元函数没有this指针，则参数要有三种情况： 

- 要访问非static成员时，需要对象做参数；

- 要访问static成员或全局变量时，则不需要对象做参数；

- 如果做参数的对象是全局对象，则不需要对象做参数.

- 可以直接调用友元函数，不需要通过对象或指针

```cpp
class INTEGER
{
	int a = 0;
	static int b;
public:
	friend void Print(const INTEGER& obj);//声明友元函数
	friend void Printb();//声明友元函数
};
int INTEGER::b = 1;
void Print(const INTEGER& obj){
		cout << obj.a << endl;
}
void Printb() {
	cout << INTEGER::b << endl;
}
void main()
{
	INTEGER obj;
	Print(obj);//直接调用
	Printb();
	system("pause");
}
```

### C++类内可以定义引用数据成员吗？

可以，但必须通过成员函数初始化列表初始化。

```cpp
class A {
public:
	int& a;
	A(int _a) :a(_a) {}
	void print() {
		cout << a << endl;
	}
};

void main()
{
	int _a = 1;
	A test(_a);
	test.print();
	system("pause");
}
```

**列表初始化的顺序和列表的顺序无关，只和变量定义的顺序有关**

```cpp
class A {
public:
	int a;
	int b;
	A(int _b) :b(_b),a(b+1) {}
	void print() {
		cout << b <<" "<< a << endl;
	}
};

void main()
{
	int _b = 1;
	A test(_b);
	test.print(); //1 -858993459
	system("pause");
}
```

由于a定义在b前边，所有类的构造函数先给a赋值，这时b是内存中的一个随机数，所以a被初始化为了-858993459而不是2.

之后才会用_b来初始化b为1.

## c++字节对齐

类型 

1. **对齐方式**（变量存放的起始地址相对于结构的起始地址的偏移量） 

***\*Char\**** 
偏移量必须为sizeof(char)即1的倍数 
***\*int\**** 
偏移量必须为sizeof(int)即4的倍数 
***\*float\**** 
偏移量必须为sizeof(float)即4的倍数 
***\*double\**** 
偏移量必须为sizeof(double)即8的倍数 
***\*Short\**** 
偏移量必须为sizeof(short)即2的倍数 

2. **字节边界数**

总的占用的空间需要为结构的字节边界数（即结构中占用最大空间的类型所占用的字节数的倍数），不够的话需要填充

```cpp
struct MyStruct 
{ 
	double dda1; 
	char dda; 
	int type 
};
```

1. 先为第一个成员dda1分配空间，其起始地址跟结构的起始地址相同（刚好偏移量0刚好为sizeof(double)的倍数），该成员变量占用sizeof(double)=8个字节
2. 接下来为第二个成员dda分配空间，这时下一个可以分配的地址对于结构的起始地址的偏移量为8，是sizeof(char)的倍数，所以把dda存放在偏移量为8的地方满足对齐方式，该成员变量占用 sizeof(char)=1个字节；
3. 接下来为第三个成员type分配空间，这时下一个可以分配的地址对于结构的起始地址的偏移量为9，不是sizeof (int)=4的倍数，为了满足对齐方式对偏移量的约束问题，VC自动填充3个字节（这三个字节没有放什么东西），这时下一个可以分配的(4)地址对于结构的起始地址的偏移量为12，刚好是sizeof(int)=4的倍数，所以把type存放在偏移量为12的地方，该成员变量占用sizeof(int)=4个字节；
4. 这时整个结构的成员变量已经都分配了空间，总的占用的空间大小为：8+1+3+4=16，刚好为结构的字节边界数（即结构中占用最大空间的类型所占用的字节数sizeof(double)=8）的倍数，所以没有空缺的字节需要填充。所以整个结构的大小为：sizeof(MyStruct)=8+1+ 3+4=16，其中有3个字节是VC自动填充的，没有放任何有意义的东西。

```cpp
struct MyStruct 
{ 
	char dda;　　　　　　　　　　　　　　//偏移量为0，满足对齐方式，dda占用1个字节； 
	double dda1;　　　　　　　　　　　　//下一个可用的地址的偏移量为1，不是sizeof(double)=8的倍数，需要补足7个字节才能使偏移量变为8（满足对齐方式），										因此VC自动填充7个字节，dda1存放在偏移量为8的地址上，它占用8个字节。 
	int type；　　　　　　　　　　　　　//下一个可用的地址的偏移量为16，是sizeof(int)=4的倍数，满足int的对齐方式，所以不需要VC自动填充，type存放在偏										移量为16的地址上，它占用4个字节。 
};　　　　　　　　　　　　　　　　　//所有成员变量都分配了空间，空间总的大小为1+7+8+4=20，不是结构的节边界数（即结构中占用最大空间的类型所占用的字节数									sizeof(double)=8）的倍数，所以需要填充4个字节，以满足结构的大小为sizeof(double)=8的倍数。 
```

3. **\#pragma pack(n)来设定变量以n字节对齐方式。**

n字节对齐就是说变量存放的起始地址的偏移量有两种情况：

(1)如果n大于等于该变量所占用的字节数，那么偏移量必须满足默认的对齐方式，
(2)如果n小于该变量的类型所占用的字节数，那么偏移量为n的倍数，不用满足默认的对齐方式。
结构的总大小也有个约束条件，分下面两种情况：如果n大于所有成员变量类型所占用的字节数，那么结构的总大小必须为占用空间最大的变量占用的空间数的倍数；否则必须为n的倍数（两者相比，取小。）

## 函数指针解读

简单的函数指针

```c++
void (*fp)(int);
```

``fp``为指针名，左边的``*``表示他是个指针，右边的``(int)``是它接受的参数类型，前面的void是它的返回类型。

```c++
void* (*(*fp)(int))[10];
```

从内向外，先看右边。逐层解析

- 看中间，``fp``，``fp``是什么呢？
- 看右边，右边什么也没有，遇到括号表示告一段落。
- 看左边，是一个``*``，所以``fp``是一个指针，是什么样的指针呢？
- 看右边，``(int)``，是一个接受``int``为参数的函数指针。
- 看左边，是一个``*``，也就是返回值是一个指针，是什么指针？
- 看右边，是``[10]``,也就是返回一个指向数组的指针，这个数组包含10个元素，10个什么元素呢？
- 看左边，是``void*``,也就是包含是个``void*``的数组。

## 常见问题

### C++中析构函数的作用

析构函数与构造函数对应，当对象结束其生命周期，如对象所在的函数已调用完毕时，系统会自动执行析构函数。

析构函数名也应与类名相同，只是在函数名前面加一个位取反符\~，例如\~stud( )，以区别于构造函数。它不能带任何参数，也没有返回值（包括void类型）。只能有一个析构函数，不能重载。

如果用户没有编写析构函数，编译系统会自动生成一个缺省的析构函数（即使自定义了析构函数，编译器也总是会为我们合成一个析构函数，并且如果自定义了析构函数，编译器在执行时会先调用自定义的析构函数再调用合成的析构函数），它也不进行任何操作。所以许多简单的类中没有用显式的析构函数。

如果一个类中有指针，且在使用的过程中动态的申请了内存，那么最好显示构造析构函数在销毁类之前，释放掉申请的内存空间，避免内存泄漏。

**类析构顺序：1）派生类本身的析构函数；2）对象成员析构函数；3）基类析构函数。**

### 静态函数和虚函数的区别

静态函数在**编译的时候**就已经确定运行时机，虚函数在**运行的时候**动态绑定。虚函数因为用了虚函数表机制，调用的时候会**增加一次内存开销**

### 重载和覆盖

重载：两个函数名相同，但是参数列表不同（个数，类型），返回值类型没有要求，在同一作用域中,C++会将重载的函数在编译时加上参数来做区别
重写：子类继承了父类，父类中的函数是虚函数，在子类中重新定义了这个虚函数，这种情况是重写

### 虚函数和多态

多态的实现主要分为**静态多态**和**动态多态**

- 静态多态主要是重载，在编译的时候就已经确定；
- 动态多态是用虚函数机制实现的，在运行期间动态绑定。举个例子：一个父类类型的指针指向一个子类对象时候，使用父类的指针去调用子类中重写了的父类中的虚函数的时候，会调用子类重写过后的函数，在父类中声明为加了virtual关键字的函数，在子类中重写时候不需要加virtual也是虚函数。
- 虚函数的实现：在有虚函数的类中，**类的最开始部分是一个虚函数表的指针**，这个指针指向一个虚函数表，表中放了虚函数的地址，实际的虚函数在代码段(.text)中。当子类继承了父类的时候也会继承其虚函数表，当子类重写父类中虚函数时候，会将其**继承到的虚函数表中的地址替换为重新写的函数地址**。使用了虚函数，会增加访问内存开销，降低效率。

### C++里是怎么定义常量的？常量存放在内存的哪个位置？

常量在C++里的定义就是一个top-level const加上对象类型，常量定义必须初始化。**对于局部对象，常量存放在栈区**，对于全局对象，常量存放在全局/静态存储区。对于字面值常量，常量存放在常量存储区。

### const修饰成员函数的目的是什么？

- const修饰的成员函数表明函数调用不**会对对象做出任何更改，**
- 如果确认不会对对象做更改，就应该为函数加上const限定，这样无论const对象还是普通对象都可以调用该函数。
- const对象只能调用const成员函数。

```cpp
#include <iostream>
#include<queue>
#include<vector>
#include<algorithm>
using namespace std;

class A {
public:
	int a = 0;
	void print1() {
		cout << "not const" << endl;
	}
	void print2() const {
		cout << "const" << endl;
	}
	
	
};
int main()
{
	const A test;
	test.print1(); //输出const
	test.print2();//错误，const对象不能调用非const成员函数
	system("pause");

	return 0;
}
```

### 如果同时定义了两个函数，一个带const，一个不带，会有问题吗？

不会，这相当于函数的重载。

如果是对类成员函数做这种操作则非常量对象会优先调用不带const的成员函数（如果只有带const的成员函数，也可以调用）。

常量对象只能调用带const的成员函数

### 类型转换

- reinterpret_cast：可以用于任意类型的指针之间的转换，对转换的结果不做任何保证
- dynamic_cast：dynamic本身只能用于存在虚函数的父子关系的强制类型转换，对于指针，转换失败则返回nullptr，对于引用，转换失败会抛出异常。属于类型安全的，因为转换失败会返回null或者抛出异常。
- const_cast：对于未定义const版本的成员函数，我们通常需要使用const_cast来去除const引用对象的const，完成函数调用。另外一种使用方式，结合static_cast，可以在非const版本的成员函数内添加const，调用完const版本的成员函数后，再使用const_cast去除const限定。
- static_cast：完成基础数据类型；同一个继承体系中类型的转换；任意类型与空指针类型void* 之间的转换。向下转换类型不安全。

### C++函数栈空间的最大值

默认是1M，不过可以调整

### extern“C”

C++调用C函数需要extern C，因为C语言没有函数重载。

在c函数前面添加 extern “C”

### new/delete与malloc/free的区别是什么

- new/delete是C++的关键字
- malloc/free是C语言的库函数
- 后者使用必须指明申请内存空间的大小，对于类类型的对象，后者不会调用构造函数和析构函数。前者会调用构造函数和析构函数。
- malloc返回的指针是无类型的（void *）需要强制转换，new则不需要。

### 虚函数表具体是怎样实现运行时多态的?

子类若重写父类虚函数，虚函数表中，该函数的地址会被替换，对于存在虚函数的类的对象，在VS中，**对象的对象模型的头部存放指向虚函数表的指针，通过该机制实现多态**

### C++如何处理返回值？

生成一个临时变量，把它的引用作为函数参数传入函数内。

### C++中拷贝赋值函数的形参能否进行值传递？

不能。如果是这种情况下，调用拷贝构造函数的时候，首先要将实参传递给形参，这个传递的时候又要调用拷贝构造函数。如此循环，无法完成拷贝，栈也会满。

为了解决以上问题，该形参应该声明为const 引用

- 引用是为了解决上面的循环构造
- const是为了防止对形参的意外修改。

`` CBox (const CBox &initB);``

### map和set有什么区别，分别又是怎么实现的？

map和set都是C++的关联容器，其底层实现都是**红黑树（RB-Tree）**。由于 map 和set所开放的各种操作接口，RB-tree 也都提供了，所以几乎所有的 map 和set的操作行为，都只是转调 RB-tree 的操作行为。

map和set区别在于：

1. map中的元素是key-value（关键字—值）对：关键字起到索引的作用，值则表示与索引相关联的数据；Set与之相对就是关键字的简单集合，set中每个元素只包含一个关键字。

2. set的**迭代器是const**的，不允许修改元素的值；map允许修改value，但**不允许修改key**。其原因是因为map和set是根据关键字排序来保证其有序性的，如果允许修改key的话，那么首先需要删除该键，然后调节平衡，再插入修改后的键值，调节平衡，如此一来，严重破坏了map和set的结构，导致iterator失效，不知道应该指向改变前的位置，还是指向改变后的位置。所以STL中将set的迭代器设置成const，不允许修改迭代器的值；而map的迭代器则不允许修改key值，允许修改value值。

3. **map支持下标操作，set不支持下标操作**。map可以用key做下标，map的下标运算符[ ]将关键码作为下标去执行查找，**如果关键码不存在，则插入一个具有该关键码和mapped_type类型默认值的元素至map中**，因此下标运算符[ ]在map应用中需要慎用，const_map不能用，只希望确定某一个关键值是否存在而不希望插入元素时也不应该使用，mapped_type类型没有默认值也不应该使用。**如果find能解决需要，尽可能用find。**

```cpp

int main()
{
	map<int, char> T;
	map<int, char> T2;
	map<int, char>T3;
	if (T[1])
		cout << 1 << endl;
	cout << T.size() << endl;  //1
	if (T2.count(1))
		cout << 1 << endl;
	cout << T2.size() << endl;  //0
	if (T3.find(1)!=T3.end())
		cout << 1 << endl;
	cout << T3.size() << endl;  //0
	system("pause");
	return 0;
}
```

### STL迭代器删除元素

这个主要考察的是迭代器失效的问题。

1. 对于序列容器vector,deque来说，使用erase(itertor)后，**后边的每个元素的迭代器都会失效**，但是后边每个元素都会往前移动一个位置**，但是erase会返回下一个有效的迭代器；**
2. 对于关联容器map set来说，使用了erase(iterator)后，**当前元素的迭代器失效**，但是其结构是红黑树，**删除当前元素的，不会影响到下一个元素的迭代器，所以在调用erase之前，记录下一个元素的迭代器即可。**
3. 对于list来说，它使用了不连续分配的内存，**并且它的erase方法也会返回下一个有效的iterator**，因此上面两种正确的方法都可以使用。

### STL迭代器

迭代器实际上是对“遍**历容器”这一操作进行了封装。**
在编程中我们往往会用到各种各样的容器，但由于这些容器的底层实现各不相同，所以对他们**进行遍历的方法也是不同的。**例如，数组使用指针算数就可以遍历，但链表就要在不同节点直接进行跳转。
这是**非常不利于代码重用**的。例如你有一个简单的查找容器中最小值的函数``findMin``，如果没有迭代器，那么你就必须定义适用于数组版本的``findMin``和适用于链表版本的``findMin``，如果以后有更多容器需要使用``findMin``，那就只好继续添加重载……而如果每个容器又需要更多的函数例如``findMax``，``sort``，那简直就是重载地狱……
我们的救星就是迭代器啦！如果我们将这些遍历容器的操作都封装成迭代器，那么诸如findMin一类的算法就都**可以针对迭代器编程而不是针对具体容器编程**，工作量一下子就少了很多！

**所以迭代器是为了把算法和容器独立起来**

### MAP数据存放形式

- map底层使用红黑树。
- unordered map底层结构是哈希表

### map与multimap

`` multimap<int, char> T;``

1、Map映射，map 的所有元素都是 pair，同时拥有实值（value）和键值（key）。pair 的第一元素被视为键值，第二元素被视为实值。所有元素都会根据元素的键值自动被排序。不允许键值重复。

底层实现：红黑树

适用场景：**有序键值对不重复映射**

2、Multimap

多重映射。multimap 的所有元素都是 pair，同时拥有实值（value）和键值（key）。pair 的第一元素被视为键值，第二元素被视为实值。所有元素都会根据元素的键值自动被排序。允许键值重复。

底层实现：红黑树

适用场景：**有序键值对可重复映射**

### vector和list的区别，应用，越详细越好

1、概念：

**1）Vector**

连续存储的容器，动态数组，**在堆上分配空间**

底层实现：**数组**

两倍容量增长：

vector 增加（插入）新元素时，如果未超过当时的容量，则还有剩余空间，那么直接添加到最后（插入指定位置），然后调整迭代器。

如果没有剩余空间了，则会重新配置原有元素个数的两倍空间，然后将原空间元素通过复制的方式初始化新空间，再向新空间增加元素，最后析构并释放原空间，之前的迭代器会失效。

性能：

访问：O(1)

插入：在最后插入（空间够）：很快

在最后插入（空间不够）：需要内存申请和释放，以及对之前数据进行拷贝。

在中间插入（空间够）：内存拷贝

在中间插入（空间不够）：需要内存申请和释放，以及对之前数据进行拷贝。

删除：在最后删除：很快

在中间删除：内存拷贝

适用场景：**经常随机访问，且不经常对非尾节点进行插入删除。**

**2) List**

动态链表，在**堆上分配空间**，每插入一个元数都会分配空间，每删除一个元素都会释放空间。

底层：**双向链表**

性能：

访问：随机访问性能很差，只能快速访问头尾节点。

插入：很快，一般是常数开销

删除：很快，一般是常数开销

适用场景：经常插入删除大量数据

**2、区别：**

1）vector底层实现是数组；list是双向 链表。

2）vector支持随机访问，list不支持。

3）vector是顺序内存，list不是。

4）vector在中间节点进行插入删除会导致内存拷贝，list不会。

5）vector一次性分配好内存，不够时才进行2倍扩容；list每次插入新节点都会进行内存申请。

6）vector随机访问性能好，插入删除性能差；list随机访问性能差，插入删除性能好。

**3、应用**

vector拥有一段连续的内存空间，因此支持随机访问，如果需要高效的随即访问，而不在乎插入和删除的效率，使用vector。

list拥有一段不连续的内存空间，如果需要高效的插入和删除，而不关心随机访问，则应使用list。

### STL里resize和reserve的区别

- resize()：改变当前容器内含有元素的数量size()
- eg: vector\<int>v; v.resize(len);v的size变为len,如果原来v的size小于len，那么容器新增（len-size）个元素，元素的值为默认为0.当v.push_back(3);之后，则是3是放在了v的末尾，即下标为len，此时容器是size为len+1；
- reserve()：改变当前容器的最大容量（capacity）,它不会生成元素，只是确定这个容器允许放入多少对象，如果reserve(len)的值大于当前的capacity()，那么会重新分配一块能存len个对象的空间，然后把之前v.size()个对象通过copy construtor复制过来，销毁之前的内存；

```cpp
#include <iostream>
#include <vector>
using namespace std;
int main() {
    vector<int> a;
    a.reserve(100);
    a.resize(50);
    cout<<a.size()<<"  "<<a.capacity()<<endl;
        //50  100
    a.resize(150);
    cout<<a.size()<<"  "<<a.capacity()<<endl;
        //150  200
    a.reserve(50);
    cout<<a.size()<<"  "<<a.capacity()<<endl;
        //150  200
    a.resize(50);
    cout<<a.size()<<"  "<<a.capacity()<<endl;
        //50  200    
}
```



## 9.24更新



### sizeof(shared_ptr)多大

[参考链接](https://blog.csdn.net/Jxianxu/article/details/72858536)

- shared_ptr的大小为普通指针的两倍，在64为机器上为16
- 因为shared_ptr中有两个指针，**一个指针指向它所管理的资源，第二个指针指向一个称为“*Control Block*”的控制块**

![img](https://img-blog.csdn.net/20170604133221130?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSnhpYW54dQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



- 控制块中有一个引用计数字段（*Reference Count*），用来记录指向*Object*的共享指针数量，当我们声明并初始化一个共享指针时，*Reference Count*的值为*1*，当有另一个共享指针指向该*Object*时（比如通过赋值或拷贝构造函数，将一个共享指针赋值给另一共享指针），Reference count 的值递增1
- Weak Count，也是一个引用计数，它用来计数指向该*Object*的*std::weak_ptr*指针的数量

1. Std::shared_ptr的大小两倍于普通指针。
2. 控制块必须是动态分配的，这是由于被*shared_ptr*所管理的对象并不知道有“引用计数”的存在，所以在被管理对象中并没有空间来存储这个引用计数。
3. **引用计数的增减必须是原子性的**，否则在多线程环境下，由于线程竞争的存在，会导致对*Reference count*的脏读、脏写。

### final关键字

**一、禁用继承**

C++11中允许将类标记为final，方法时直接在**类名称后面使用关键字final，**如此，意味着继承该类会导致编译错误。

实例如下：



```cpp
class Super final
{
  //......
};
```

**二、禁用重写**

C++中还允许**将方法标记为fianal**，这意味着无法在子类中重写该方法。这时final关键字至于方法参数列表后面，如下



```cpp
class Super
{
public:

    Supe();

    virtual void SomeMethod() final;

};
```

### 虚继承

**1.为什么要引入虚拟继承**

虚拟继承是多重继承中特有的概念。**虚拟基类是为解决多重继承而出现的。**如:类D继承自类B1、B2，而类B1、B2都继承自类A，因此在类D中两次出现类A中的变量和函数。为了节省内存空间，可以将B1、B2对A的继承定义为虚拟继承，**而A就成了虚拟基类**。实现的代码如下：

class A

class B1:public virtual A;

class B2:public virtual A;

class D:public B1,public B2;

虚拟继承在一般的应用中很少用到，所以也往往被忽视，这也主要是因为在C++中，多重继承是不推荐的，也并不常用，而一旦离开了多重继承，虚拟继承就完全失去了存在的必要因为这样只会降低效率和占用更多的空间。

```cpp
第一种情况：　　　　　　　　	 	第二种情况：　　　　　　　　　　	第三种情况　　　　　　　　　　　　	第四种情况：
class a　　　　　　　　　　　	  class a　　　　　　　　　　　　  	class a　　　　　　　　　　　　　　class a
{　　　　　　　　　　　　　  	{　　　　　　　　　　　　　　　 	{　　　　　　　　　　　　　　　　　	{
    virtual void func();　　　　　　virtual void func();　　　　　　virtual void func();　　　　　　virtual void func();
};　　　　　　　　　　　　　 	};　　　　　　　　　　　　　　　　　	 	char x;　　　　　　　　　　　　　　char x;
class b:public virtual a　　　class b :public a　　　　　　　 };　　　　　　　　　　　　　　　　	 };
{　　　　　　　　　　　　　　		{　　　　　　　　　　　　　　 　	class b:public virtual a　　　　class b:public a
    virtual void foo();　　　　　　  virtual void foo();　　　{　　　　　　　　　　　　　　　　   {
};　　　　　　　　　　　　　 	};　　　　　　　　　　　　　　　　　　	virtual void foo();　　　　　　　　virtual void foo();
　　　　　　　　　　　　　　　　　　　　　　　　　　　　　　		　  };　　　					    };
```

如果对这四种情况分别求sizeof(a）, sizeof(b)。下面是输出结果：（在vc6.0中运行）
**第一种：8，24**
**第二种：8，8**
**第三种：16，32**
**第四种：16，16**

子类虚继承基类时，**会多一个虚基类指针vbptr指向虚基类表vbtable**，然后还要包含a的字节数，最后再加上自己的字节数。

 ### **构造函数不能为虚函数**

C++对象在三个地方构建：（1）函数堆栈；（2）自由存储区，或称之为堆；（3）静态存储区。无论在那里构建，其过程都是两步：首先，分配一块内存；其次，调用构造函数。好，问题来了，如果构造函数是虚函数，那么就需要通过**vtable** 来调用，但此时面对一块 raw memeory，到哪里去找 **vtable** 呢？毕竟，**vtable** 是在构造函数中才初始化的啊，而不是在其之前。因此构造函数不能为虚函数。



简单讲就是，如果构造函数时虚函数，则需要虚表来调用，而虚表这个时候又没有初始化，是空的。

### 二叉树的存储方式

对于完全二叉树使用数组进行存储，内存连续

对于普通二叉树使用链表存储，内存可以不连续

## 9.29更新

### c++对释放过的内存再次释放会发生什么？

引发异常，程序崩溃。

### c++析构函数

c++类一定要定义析构函数吗？

c++默认的析构函数会做哪些事情？

如果一个的成员变量只有一个``int``，需要定义自己的析构函数吗？

如果一个的成员变量只有一个``int``数组比如``int[5]``，需要定义自己的析构函数吗？

如果一个的成员变量只有一个``struct``，这个``struc``t里面只有一个``int``需要定义自己的析构函数吗？

如果一个的成员变量只有一个``int``，需要定义自己的析构函数吗？

c++最厉害的就是可以自己做内存管理，最难的也是内存管理。

c++的堆和栈内存，都有什么区别。各自有多大

仔细看一下c++的内存管理。

模板的类型确定是发生在编译时期还是运行时期。

类的序列化，如何把一个float变量对应的二进制转换成一个int，既让两者的二进制存储一致

32的地址，位置只能用4GB

C++为什么引入迭代器，为了把算法和容器独立起来

书籍:《C Primer Plus》《深入理解C++对象内存模型》《effective》