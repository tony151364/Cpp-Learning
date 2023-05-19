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
	friend std::ostream& operator << (std::ostream& os, const StringBad& st);
};

#endif  // STRINGBAD_H_
```
- 该类使用char指针来表示姓名。这意味着类声明没有为字符串本身分配存储空间，而是在构造函数中使用new来为字符串分配空间。这避免了在类声明中预先定义字符串的长度。
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

#### 2.复制构造函数
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
- [x] 为啥CINLIM就可以在类声明中进行初始化？ 答：加了const的是可以的
```C++
#pragma once
#pragma once
// 12.4 string1.h  -- fixed and augmented string class defination
#include <iostream>
#ifndef STRNG1_H_
#define STRNG1_H_
using std::istream;
using std::ostream;

class String
{
private:
	char* str;  // pointer to string
	int len;  // length of string
	static int num_strings;  // number of objects
	static const int CINLIM = 80;  // cin input limit

public:
	// constructors and other methods
	String(const char* s);	// constructor
	String();				// default constructor
	String(const String&);  // copy constructor
	~String();				// destructor
	int length() const { return len; }

	// overloaded operator methods
	String& operator=(const String& st);
	String& operator=(const char* s);
	char& operator[](int i);
	const char& operator[](int i) const;

	// overloaded operatpr friends
	friend bool operator<(const String& st1, const String& st2);
	friend bool operator>(const String& st1, const String& st2);
	friend bool operator==(const String& st1, const String& st2);
	friend ostream& operator<<(ostream& os, const String& st);
	friend istream& operator>>(istream& is, String& st);

	// static function
	static int HowMany();
};

#endif  // STRNG1_H_
```

```C++
// 12.2 string1.cpp -- String class methods
#include <cstring>						// string.h for some
#include "string1.h"					// includes <iostream>
using std::cout;

// initializing static class member
int String::num_strings = 0;

// static method
int String::HowMany()
{
	return num_strings;
}

// class methods
String::String(const char* s)			// construct String from C string
{
	len = std::strlen(s);				// set size
	str = new char[len + 1];			// allot storage
	std::strcpy(str, s);				// initialize pointer
	num_strings++;						// set object count
}

String::String()
{
	len = 4;
	str = new char[4];
	std::strcpy(str, "C++");			// default string
	num_strings++;
}

String::String(const String& st)
{
	num_strings++;
	len = st.len;
	str = new char[len + 1];
	std::strcpy(str, st.str);
}

String::~String()						// necessary destructor
{
	--num_strings;
	delete[] str;
}

// overloaded operator methods
String& String::operator=(const String& st)			// assign a String to a String
{
	if (this == &st)
	{
		return *this;
	}

	delete[] str;
	len = st.len;
	str = new char[len + 1];
	std::strcpy(str, st.str);
	return *this;
}

// assign a C string to a String
String& String::operator=(const char* s)			
{
	delete[] str;
	len = std::strlen(s);
	str = new char[len + 1];
	std::strcpy(str, s);
	return *this;
}

// read-write char access for non-const String
char& String::operator[](int i)
{
	return str[i];
}

// read-only char access for const string
const char& String::operator[](int i) const
{
	return str[i];
}

bool operator<(const String& st1, const String& st2)
{
	return (std::strcmp(st1.str, st2.str) < 0);
}

bool operator>(const String& st1, const String& st2)
{
	return st2 < st1;
}

bool operator==(const String& st1, const String& st2)
{
	return (std::strcmp(st1.str, st2.str) == 0);
}

// simple String output
ostream& operator<<(ostream& os, const String& st)
{
	os << st.str;
	return os;
}

// quick and dirty String input（快速而简单的字符串输入）
// "Dirty" 在这里指的是简单、不完美但有效的方法，没有过多的细节或精确性。它通常用于表示快速解决问题的临时或不太优雅的方法。
istream& operator>>(istream& is, String& st)
{
	char temp[String::CINLIM];

	is.get(temp, String::CINLIM);

	if (is)
	{
		st = temp;
	}
	
	while (is && is.get() != '\n')
		continue;

	return is;
}
```
- 重载>>运算符提供了一种将键盘输入和读入到String对象中的简单方法。它嘉定输入的字符数不多于String::CINLIM的字符数，并丢弃多余的字符。在if条件下，由于某种原因（如到达文件尾或get(char*, int)读取的是一个空行）导致输入失败，istream对象的值将置为false。
```C++

```
## 12.3 在构造函数中使用new时应注意的事项
- 用new初始化对象的指针成员时必须特别小心
	- 如果在构造函数使用new来初始化指针成员，则应在析构函数中delete。
	- new和delete相互兼容。new对应于delete，new[]对应于delete[] 
	- 如果有多个构造函数，则必须以相同的方式使用new，要么都带中括号，要么都不带。因为只有一个析构函数，所有的构造函数都必须与它兼容。然而可以在一个构造函数中使用new初始化指针，而在另一个构造函数中将指针初始化为空（0或C++11中的nullptr），这是因为delete（无论是带中括号还是不带中括号）都可以用与空指针。

- NULL、0还是nullptr：
	- C程序员通常使用NULL（在很多头文件中，NULL是一个被定义为0的字符常量）
	- C++程序员以前喜欢使用0，C++11后多使用nullptr

- 应定义一个复制构造函数，通过深度复制将一个对象初始化为另一个对象。例如：
```C++
String::String(const String& st)
{
	num_string++;
	len = st.len;
	str = new char[len + 1];
	std::strcpy(str, st.str);  // copy string to new location
}
```

- 应对定义一个复制运算符，通过深度复制将一个对象复制给另一个对象
```C++
String& String::operator=(const String& st)
{
	if (this == &st)
		return *this;
		
	delete[] str;
	len = st.len;
	str = new char[len + 1];
	std::strcpy(str, st.str);
	return *this;
}
```
- 小结：
	- 拷贝构造函数：将自己拷贝给别人。自己 -> 别人
	- 复制构造函数：别人复制给自己。  别人 -> 自己

### 12.3.1 应该和不应该
- 对不是使用new初始化的指针使用delete是，结果将是不确定的，并可能有害
```C++
String::String()
{
	static conat char* s = "C++";
	len = std::strlen(s);
	str = new char[len + 1];
	std::strcpy(str, s);
}
```

### 12.3.2 包含类成员的类的逐成员复制
- 在一些复杂的情况下，需要显示调用String和string的复制构造函数和赋值运算符，这将在第13章介绍。

## 12.4 有关返回对象的说明
- 可以返回指向对象的引用、指向对象的const引用或const对象
- 现在介绍最后一种

### 12.4.1 返回执行const对象的引用
- 其实当函数的一个参数可以作为返回值时用这种比较好。比如返回两个对象最大的那个。下面两种实现都是可行的：
```C++
// version 1
Vector Max(const Vector& v1, const Vector& v2)
{
	if (v1.magval() > v2.magval())
		return v1;
	else
		return v2;
}

// version 2
const Vector& Max(const Vector& v1, const Vector& v2)
{
	if (v1.magval() > v2.magval())
		return v1;
	else
		return v2;
}
```
- 这里有三点需要说明：
	- 首先，返回对象将调用复制构造函数，而返回引用不会。因此第二个版本所做的工作更少，效率更高。
	- 其次，引用指向的对象应该在调用函数执行时存在。
	- 第三，v1和v2都被声明为const引用，因此返回类型必须为const，这样才匹配。

### 12.4.2 返回指向非const对象的引用
- 两种常见的返回非const对象情形是：
	- 重载赋值运算符。（这样做可以提高效率）
	- 重载与cout一起使用的<<运算符。（必须这样做，可能cout需要做一些非const的事情） 

### 12.4.3 返回对象
```C++
Vector Vector::operator+(const Vector& b) const
{
	return Vector(x + b.x, y + b.y);
}
```
- 在这种情况下，存在调用复制构造函数来创建被返回的对象的开销，然而这是无法避免的。
- 返回语句引发的对复制构造函数的饮食调用创建一个调用程序能够访问的对象。

### 12.4.4 返回const对象
```C++
net = force1 + force2;  							// 1: three Vector objects
force1 + force2 = net;  							// 2: dyslectic programing
cout << (force1+ force2 = net).magval() << endl;	// 3.demented programing
```
- 提出三个问题：为什么这样编写？这些语句为何可行？这些语句有什么功能？
	- 第一：程序员通常很有创造力，但这可能导致错误
	- 第二：这种代码之所以可行，是因为复制构造函数将创建一个临时对象来表示返回值。（force1 + force2 的结果为一个临时对象可以被赋值）
	- 第三：临时对象用完就被丢弃了（5555~）
	- 如果你担心这样的事情发生，一个简单的方法是：将返回类型声明为const Vector。这样语句2,3就非法了。
- 小结：
	- 总之如果方法或者函数要返回局部对象，则应返回对象，而不是指向对象的引用。调用什么复制构造函数.....
	- 如果方法或者函数要返回一个没有公有构造函数的类（如ostream）对象，它必须返回一个指向这种对象的引用。
	- 最后有些方法可以返回对象，也可以返回引用。一般首选引用，效率更好

## 12.5 使用指向对象的指针
程序清单中还使用一个指针来追踪新对象
```C++
String* favorite = new String(sayings(choice));
```
这里指针favorite指向new创建的未被命名对象。这种特殊的语法意味着使用对象saying[choice]来初始化新的String对象，这将调用复制构造函数。因为复制构造函数（const String&）的参数类型与初始化值（saying[choice]）匹配。









