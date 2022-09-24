- 除非同时执行一些列额外步骤，如扩展类析构函数、使所有的构造函数与new析构函数协调一致、编写额外的类方法来帮助正确完成初始化和赋值（当然，本章将介绍这些步骤），否则，在类构造函数中使用new将导致新问题。

## 12.1 动态内存和类
```C++
#pragma once
// 12.1 stringbad.h  -- flawed string class definition
#include <iostream>
#ifndef STRNGBAD_H_
#define STRNGBAD_H_

class StringBad
{
private:
	char* str;  // pointer to string
	int len;  // length of string
	static int num_strings;  // number of objects
public:
	StringBad(const char* s);  // constructor
	StringBad();  // default constructor
	~StringBad();  // destructor
// friend function
	friend std::ostream& operator << (std::ostream& os,
		const StringBad& st);
};

#endif  // STRINGBAD_H_
```
- 改类使用char指针来表示姓名。这意味着类声明没有为字符串本身分配存储空间，而是在构造函数中使用new来为字符串分配空间。这避免了在类声明中预先定义字符串的长度。
- num_strings成员作为静态存储类有一个特点：无论创建了多少对象，程序都只创建一个静态类变量副本。假设创建了10个StringBad对象，将有10个str成员和10个len成员，但只有一个共享的num_strings成员+

```C++
// 12.2 stringbad.cpp -- StringBad class methods
#include <cstring>  // string.h for some

#include "stringbad.h"
using std::cout;

// initializing static class member
int StringBad::num_strings = 0;  // 请不要在类声明中初始化静态成员变量，这是因为声明描述了如何分配内存，但并不分配内存

// class methods
// construct StringBad from C string
StringBad::StringBad(const char* s)
{
	len = std::strlen(s);  // set size
	str = new char[len + 1];  // allot storage
	std::strcpy(str, s);    // initialize pointer
	num_strings++;  // set object count
	cout << num_strings << ": \"" << str
		<< "\" object created\n";  // for your Information
}

StringBad::StringBad()
{
	len = 4;
	str = new char[4];
	std::strcpy(str, "C++");  // default string
	num_strings++;
	cout << num_strings << ": \"" << str
		<< "\" default object created\n";  // FYI
}

StringBad::~StringBad()
{
	cout << "\"" << str << "\" object delted, ";  // FYI
	--num_strings;  // required
	cout << num_strings << " left\n";  //FYI
	delete[] str;   // required
}

std::ostream& operator << (std::ostream& os, const StringBad& st)
{
	os << st.str;
	return os;
}
```
- **注意：** 静态数据成员在类声明中声明，在包含类方法的文件中初始化。初始化时使用作用域运算符来指出静态成员所属的类。但如果静态成员是整型或枚举型const，则可以在类声明中初始化。
- 该程序将对象声明放在一个内部代码块中，因为析构函数将在定义对象的代码块执行完毕时调用。如果不这样做，析构函数将在main()函数执行完毕时调用，导致您无法在执行窗口关闭前看到析构函数显示的消息。
```C++
// 12.3 vegnews.cpp -- using new and delete with classes
// compile with stringbad.cpp
#include <iostream>
using std::cout;
#include "stringbad.h"

void callmel(StringBad&);  // pass by reference
void callme2(StringBad);  // pass by value

int main()
{
	using std::endl;
	{
		cout << "Starting an inner block.\n";
		StringBad headline1("Celery Stalks at Midnight");
		StringBad headline2("Lettuce Prey");
		StringBad sports("Spinach Leaves Bowl for Dollars");
		cout << "headline1: " << headline1 << endl;
		cout << "headline2: " << headline2 << endl;
		cout << "sports: " << sports << endl;
		
		callmel(headline1);
		cout << "headline1: " << headline1 << endl;
		
		callmel(headline2);
		cout << "headline2: " << headline2 << endl;
		
		cout << "Initialize one object to another:\n";
		StringBad sailor = sports;
		cout << "sailor: " << sailor << endl;
		
		cout << "Assign one object to another:\n";
		StringBad knot;
		knot = headline1;
		cout << "knot: " << knot << endl;
		
		cout << "Exiting the block.\n";
	}
	cout << "End of main()\n";

	return 0;
}

void callmel(StringBad& rsb)
{
	cout << "String passed by reference:\n";
	cout << "    \"" << rsb << "\"\n";
}

void callme2(StringBad sb)
{
	cout << "String passed by value:\n";
	cout << "    \"" << sb << "\"\n";
}
```
- callme2()按值传递headline2，导致headline2作为函数参数来传递从而导致析构函数被调用。其次，虽然按值传递可以防止原始参数被修改，但时间少函数已使原始字符串无法识别。
```C++
StringBad saillor = sports;
// ↑ 等价于 ↓
StringBad sailor = StringBad(sports);  // constructor using sports
// 因为sports的类型为StringBad，因此相应的构造函数原型应该如下
StringBad(const StringBad& );

// 当您使用一个对象来初始化另一个对象时，编译器将自动生成上述构造函数（称为复制构造函数，因为它创建对象的一个副本）
// 自动生成的构造函数不知道需要更新静态变量num_string，因此会将计数方案乱搞
```
- 实际上，这个例子说明的所有问题都是由编译器自动生成的成员函数引起的，下面介绍这个主题


### 12.1.2特殊成员函数
- C++自动提供下面这些成员函数
	- 默认构造函数，如果没有定义构造函数。
	- 默认构造函数，如果没有定义。
	- 复制构造函数，如果没有定义
	- 赋值构造函数，如果没有定义
	- 地址运算符，如果没有定义

- C++11 提供了另外两个特殊成员函数：移动构造函数(move constructor)和移动赋值运算符(move assignment operator),将在第18章讨论

#### 1.默认构造函数
- 带参数的构造函数也可以是默认构造函数，只要所有参数都有默认值
```C++
// 但只能有一个构造函数。也就是说，不能这样做
Klunk() { klunk_ct = 0 }  // constructor #1
Klunk(int n = 0) { klunk_ct = n; }  // ambiguous construtor #2

// 第二个声明即与构造函数#1（没有参数）匹配，也与构造函数#2（使用默认参数 0）匹配。这将导致编译器发出一条错误消息
Klunk kar(10);  // clearly matches Klunt (int n)
Klunk bus;  // could match either constructor
```

####2.复制构造函数
- 复制构造函数用于将一个对象复制到新创建的对象中。也就是说，它用于初始化过程中（包括按值传递参数）而不是常规的赋值过程
```C++
// 类的赋值构造函数原型通常如下
Class_name(const Class_name &);
// 它接受一个指向类对象的常量引用作为参数。例如，String类的复制构造函数的原型如下：
StringBad(const StringBad& );
```
- 对于赋值构造函数需要知道两点
    - 何时调用
    - 有何功能
#### 3.何时调用赋值构造函数
- 新建一个对象并将其初始化为同类对象时，赋值构造函数都将被调用。
```C++
// 下面4种声明都将调用复制构造函数：
StringBad ditto(motto);  // calls StringBad(const StringBad&)
StringBad metoo = motto;  // calls StringBad(const StringBad &)
StringBad also = StringBad(motto);  // calls StringBad(const StringBad&)
StringBad* pStringBad = new StringBad(motto)  // call StringBad(const StringBad &)
```
- 其中中间的2种声明可能会使用复制构造函数直接创建metoo和also，也可能使用复制构造函数生成一个临时对象，这取决于具体的时间。最后一种声明使用motto初始化一个匿名对象，并将新对象的指针赋给pstring指针。

#### 4.默认的复制构造函数的功能
- 默认的复制构造函数逐个复制非静态成员(成员复制也称为浅复制)，复制的是成员的值。
- [ ] 如果成员本身就是类对象，则将使用这个类的复制构造函数来复制成员对象。静态函数(如num_strings)不受影响，因为他们属于整个类


### 12.1.3 回到Stringbad：复制构造函数到底哪里出了问题
- 解决办法是提供一个对计数进行更新的显示构造函数
```C++
StringBad ...
```

- [ ] 感觉还是有点说不太通

#### 1.定义一个显示复制构造函数以解决问题
- （浅拷贝和深拷贝，这Python的列表也有啊）
- 解决类设计中这种问题的方法是进行深度复制(deep copy),保证每个对象都有自己的字符串，而不会释放已经释放的字符串
```C++
// 可以这样编写String的复制构造函数
StringBad::StringBad(const StringBad& st)
{
    num_Strings++;  // handle static member update
    len = st.len;  // same length
    str = new char [len + 1];
    std::strcpy(str, st.str);

    cout << num_strings << ": \""  <<str
    << " \" object created\n";
}
```

- 必须定义构造函数的原因在于，一些类成员是使用new初始化的、指向数据的指针

### 12.1.4  Stringbad 的其他问题：赋值运算符
- ANSI C 允许结构复制，而C++允许类对象赋值，这是通过自动为类重载赋值运算符实现的。这种运算符的原型如下：
```C++
Class_name& Class_name::operator={const Class_name &};
```

#### 1.赋值运算符的功能以及何时使用它
```C++
StringBad metoo = knot;  // use copy constructor, possibly assignment, too
```
- 这条语句是显示可能分两步来处理这条语句：使用复制构造函数创建一个临时对象，然后通过赋值将临时对象的值复制到新对象中。也就是说，初始化会调用复制构造函数，而使用=运算符也可能调用赋值运算符。
- 与复制构造函数相似，赋值运算符的隐式实现也对成员进行逐个复制。如果成员本身就是类对象，则程序将使用为整个类定义的赋值运算符来复制该成员

#### 2.赋值的问题出在哪里
- 出现的问题与隐式复制构造函数相同：数据受损。这也是成员复制的问题，将导致headline1.str和knot.str指向相同的地址。因此，当对knot调用析构函数时，将删除字符串"Celery Stalks at Midnight”；当对headline1调用析构函数时，将试图删除前面已经删除的字符串。
- 独立声明(Declaration of Independence)

#### 3.解决赋值的问题
- 解决办法就是进行深度复制
    - 由于目标对象可能引用了以前分配的数据，所以函数应使用delete[]来释放这些数据。
    函数应避免将对象赋给自身；否则，给对象重新复制前，释放内存操作可能删除对象的内容。
    函数返回一个指向调用对象的引用
```C++
S0 = S1 = S2;
// 是用函数表示法，上述代码为：
S0.operator=(S1.operatpr=(S2)));
因此，S1.operator=(S2)的返回值是函数S0.operatpr=()的参数
因为返回值是一个指向StringBad对象的引用，因此参数类型是正确的
```
- 下面的代码说明了如何为StringBad类编写赋值运算符：

```C++
StringBad& StringBad::operator=(const StringBad& st)
{
    if (this == &st)  // 检查自我复制
        return *this;
    
    delete [] str;
    len = st.len;
    str = new char [len + 1];
    std::strcpy(str, st.str);
    return *this;
}
```
- 第10章介绍过，赋值运算符是只能由类成员函数重载的运算符之一
- 复制操作并不创建新的对象，因此不需要调整静态数据成员num_strings的值

- 将复制构造函数和赋值运算符调价到StringBad类后，所有的问题都解决了。

## 12.2 修订后的默认构造函数

### 12.2.1 修订后的默认构造函数
```C++
String::String()
{
	len = 0;
	str = new char[1];
	str[0] = '\0';
}

// 为什么代码是
str = new char[1];
// 而不是
str = new char;
// 上面两种分配方式的内存量相同，区别在于前者与类析构函数兼容，而后者不兼容。因此对于下述代码
str = new char[1];
str[0] = '\0';  .. default strubg
// 可修改为：
str = 0;  // sets str to the null pointer
```
- [ ] 对于其他方式初始化的指针，使用delete[] 时，结果是不确定的：

#### C++11 空指针
- C++98中：字面值0有两个含义，数字零值，也可以表示空指针
- 有些程序员使用0来表示空指针，有些使用NULL
- C++11 使用nullptr表示空指针

### 12.2.2 比较成员函数
- 将比较函数作为友元，有助于将String对象与常规的C字符串进行比较。例如假设answer是String对象，则下面的代码：
```C++
if ("love" == answer)
// 将被转换成
if (operator == ("love", answer))
// 然后，编辑器将使用某个构造函数将代码转换为
if (operator == (String("love"), answer))
// 这与原型是匹配的
```

### 12.2.3 使用中括号表示法访问字符
- C-风格字符串，使用中括号来访问其中的字符
- C++中，中括号是一个运算符，可以进行重载。对于中括号运算符而言：
	- 一个操作数位于第一个中括号的前面city
	- 另一个操作数位于中括号的中间\[0]
	- city\[0] 

```C++
String opera("The Magic Flute");

opera.operatpr[](4)

char& String::operator[](int i)
{
	return str[i];
}
// 将返回类型声明为char &，便可以给特定元素赋值
String means("might");
means[0] = 'r';  // 被转化为means.operator[][0] = 'r';
// 这里将r赋给方法的返回值，而函数返回的是指向means.str[0]的引用，因此上述代码等同于下面的代码：
means.str[0] = 'r';
// 代码的最后一行访问的是私有数据，但由于operator[]()是类的一个方法，因此能够修改数组的内容。最终的结果由“might”被改为“right”

```
- 常量版本的注意事项

### 12.2.4 静态类成员函数
- 可以将成员函数声明为静态的（函数声明必须包括关键字static，但如果函数定义是独立的，则其中不能包含关键字static）这样做有两个重要的后果
- 首先，不能通过对象调用静态成员函数；实际上，静态成员函数甚至不能使用this指针。如果静态成员函数是在公有部分声明的，则可以使用类名和作用域解析运算符来调用它。
```C++
// 例如，可以给String类添加一个名为HowMany()的静态成员函数，方法是在类生命中添加如下原型/定义：
static int HowMany() { return num_strings; }

// 调用它的方式如下：
int count = String::HowMany();  // invoking a static member function
```
- 其次，由于静态成员函数不与特定的对象相关联，因此只能使用静态数据成员。例如，静态方法HowMany()可以访问静态成员num_string，但不能访问str和len。
- [ ] 同样，也可以使用静态成员函数设置类级(classwide)标记，以控制某些类接口的行为。例如，类级标记可以控制显示类内容的方法所使用的格式。

### 12.2.5 进一步重载赋值运算符
```C++
String name;
char temp[40];
cin.getline(temp, 40);
name = temp;  // use constructor to convert type
```
- [ ] 先来回顾一下最后一条语句是怎样工作的：
	- 1.程序使用构造函数String(const char \*) 来创建一个临时String对象, 其中包含temp中的字符串副本。第11章介绍过，只有一个参数的构造函数被用作转换函数
	- 2.本章后面的程序清单12.6中的程序使用Sting & String::operator = (const String &)函数将临时对象中的信息复制到name对象中。
	- 3.程序调用析构函数~删除临时对象

- 为了提高效率，最简单的方式是重载赋值运算符，使之能够直接使用常规字符串，这样就不用创建和删除临时对象了。下面是一种可能的实现
```C++
String & String::operator=(const char * s)
{
	delete [] str;
	len = std::strlen(s);
	str = new char[len + 1];
	std::strcpy(str, s);
	return *this;
```
- 一般来说，必须释放str指向的内存，并为新字符串分配足够的内存。

- [ ] 构造函数难道不能直接用 = 吗，如果你重载了，构造函数是不是就不用std::strcpy()
- [ ] \[]的重载，是不是有点多余
- [ ] 为啥CINLIM就可以在类声明中进行初始化？













