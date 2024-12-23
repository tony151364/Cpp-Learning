# 第 12 章 类和动态内存分配
- 本章内容包括：
	- 对类成员使用动态内存分配
	- 隐式或显式复制构造函数
	- 隐式和显示重载赋值运算符
	- 在构造函数中使用new所必须完成的工作
	- 使用静态类成员
	- 将定位new运算符用于对象
	- 使用指向对象的指针
	- 实现队列抽象数据类型（ADT）

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
- 由于按值传递对象会调用拷贝构造函数，所以传递**对象推荐按引用传递**

#### 4.默认的复制构造函数的功能
- 默认的复制构造函数逐个复制非静态成员(成员复制也称为浅复制)，复制的是成员的值。
- [x] 如果成员本身就是类对象，则将使用这个类的复制构造函数来复制成员对象。静态函数(如num_strings)不受影响，因为他们不属于整个类


### 12.1.3 回到Stringbad：复制构造函数到底哪里出了问题
- 解决办法是提供一个对计数进行更新的显示构造函数
```C++
StringBad ...
```

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
Class_name& Class_name::operator=(const Class_name &){};
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

#### 一点思考
- 一个已存在的对象初始化另一个对象时调用复制构造函数
- 两个已存在的对象之间赋值时调用赋值运算符

## 12.2 改进后的新String类

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
String operator("The Magic Flute");

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

- [x] 构造函数难道不能直接用 = 吗，如果你重载了，构造函数是不是就不用std::strcpy()。太混乱，其实已存在的用赋值运算符，初始化用复制构造函数。
- [x] \[]的重载，是不是有点多余。并不是，有些情况比如字符流输出，限定const的话用非const的版本是不可行的。
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

	// overloaded operator friends
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
	{
		continue;
	}

	return is;
}
```
- 重载>>运算符提供了一种将键盘输入和读入到String对象中的简单方法。它假定输入的字符数不多于String::CINLIM的字符数，并丢弃多余的字符。在if条件下，由于某种原因（如到达文件尾或get(char*, int)读取的是一个空行）导致输入失败，istream对象的值将置为false。

```C++
// sayings1.cpp -- using expanded String class
// compile with string1.cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include "string1.h"

const int ArSize = 10;
const int MaxLen = 81;

int main()
{
	using std::cout;
	using std::cin;
	using std::endl;

	String name;
	cout << "Hi, what't your name?\n>> ";
	cin >> name;
	cout << name << ", please enter up to " << ArSize
		<< " short sayings <empty> line to quit>:\n";
	
	String sayings[ArSize];    // array of objects
	char temp[MaxLen];         // temporary string storage
	int i;
	for (i = 0; i < ArSize; i++)
	{
		cout << i + 1 << ": ";
		cin.get(temp, MaxLen);

		while (cin && cin.get() != '\n')
			continue;

		if (!cin || temp[0] == '\0')    // empty line?
		{
			break;						// i not incremented
		}
		else
		{
			sayings[i] = temp;
		}
	}

	int total = i;						// total # of lines read
	
	if (total > 0)
	{
		cout << "Here are your sayings:\n";

		for (i = 0; i < total; i++)
		{
			cout << sayings[i][0] << ": " << sayings[i] << endl;
		}

		int shortest = 0;
		int first = 0;

		for (i = 1; i < total; i++)
		{
			if (sayings[i].length() < sayings[shortest].length())
			{
				shortest = i;
			}

			if (sayings[i] < sayings[first])
			{
				first = i;
			}
		}

		cout << "Shortest saying:\n" << sayings[shortest] << endl;
		cout << "First alphabetically:\n" << sayings[first] << endl;
		cout << "This program used " << String::HowMany()
			<< " String objects.Bye\n";
	}
	else
	{
		cout << "No input! Bye.\n";
	}

	return 0;
}
```
- 注意：较早的get(char*, int)版本在读取空行后，返回的值不为false
- [x] 为什么main函数中，第49行要使用“sayings[i][0]” 而不是 “syaings[i]”。程序的要求而已，要输出字符串第一个字符

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
	- 复制构造函数：将自己拷贝给别人。自己 -> 别人
	- 赋值运算符：别人复制给自己。  别人 -> 自己

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
```C++
class Magazine
{
private:
	String title;
	string publisher;
}
```
- 对于 Magazine不需要编写复制构造函数和赋值运算符。如果你将一个Magazine对象复制或赋值给另一个Magazine对象，逐成员复制将使用成员类型定义的复制构造函数和赋值运算符。
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
```C++
// sayings2.cpp -- using pointers to objects
// compile with string1.cpp
#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <cstdlib>
#include "string1.h"
const int ArSize = 10;
const int MaxLen = 81;

int main()
{
	using namespace std;

	String name;
	cout << "Hi, what't your name?\n>> ";
	cin >> name;

	cout << name << ", please enter up to " << ArSize
		<< " short sayings <empty line to quit>:\n";

	String sayings[ArSize];    // array of objects
	char temp[MaxLen];         // temporary string storage
	int i;
	for (i = 0; i < ArSize; i++)
	{
		cout << i + 1 << ": ";
		cin.get(temp, MaxLen);

		while (cin && cin.get() != '\n')
			continue;

		if (!cin || temp[0] == '\0')    // empty line?
		{
			break;						// i not incremented
		}
		else
		{
			sayings[i] = temp;
		}
	}

	int total = i;						// total # of lines read

	if (total > 0)
	{
		cout << "Here are your sayings:\n";

		for (i = 0; i < total; i++)
		{
			cout << sayings[i][0] << ": " << sayings[i] << endl;
		}

		String* shortest = &sayings[0];  // initialize to first object
		String* first = &sayings[0];

		for (i = 1; i < total; i++)
		{
			if (sayings[i].length() < shortest->length())
			{
				shortest = &sayings[i];
			}

			if (sayings[i] < *first)
			{
				first = &sayings[i];
			}
		}

		cout << "Shortest saying:\n" << *shortest << endl;
		cout << "First alphabetically:\n" << *first << endl;
		
		srand(time(0));
		int choice = rand() % total; // pick index at random
		// uew new to create, initialize new String object
		String* favorite = new String(sayings[choice]);
		cout << "My favorite saying:\n " << *favorite << endl;
		delete favorite;
	}
	else
	{
		cout << "No input! Bye.\n";
	}

	return 0;
}
```
```C++
// 通常，如果Class_name是类，value的类型为Type_name，则下面的语句：
Class_name* pclass = new Class_name(value);
// 将调用如下构造函数：
Class_name(Type_name)
// 这里可能还有一些琐碎的转换，例如：
Class_name(const Type_name&);
// 另外，如果不存在二义性，则将发生由原型匹配导致的转换（如从int到double）。下面的初始化将调用默认构造函数：
Class_name* ptr = new Class_name;
```

### 12.5.1 再谈new和delete
- 如果对象是动态变量，则当执行完定义该对象的程序块时，将调用该对象的析构函数。
- 如果对象是静态变量（外部、静态、静态外部或来自名称空间），则在程序结束时调用对象的析构函数
- 如果对象是用new创建的，则仅当您显示使用delete删除对象时，其析构函数才会被调用
- 图12.4

### 12.5.2 指针和对象小结（核心）
- 图12.5 + 图12.6
使用对象指针时需要注意的几点：
- 1 使用常规表示法来声明指向对象的指针
```C++  String* glamour; ```
- 2 可以将指针初始化为指向已有的对象：
 ```C++  String* first = &sayings[0]; ```
- 3 可以使用new来初始化指针，这将创建一个新的对象
```C++
// invokes String(const String&)
String* favorite = new String (sayings[choice]);
 ```
- 4 对类使用new将调用相应的类构造函数来初始化新创建的对象
 ```C++
// invokes default constructor
String* gleep = new String;

// invokes the String(const char*) constructor
String* glop = new String("my my my");

// invokes the String(const String& ) constructor
String* favorite = new String(sayings[choice]);
```
- 5 可以使用->运算符通过指针访问类方法：
```C++
if (sayings[i].length() < shortest->length())
```
- 6 可以对对象指针应用解除引用运算符（*）来获得对象：
```C++
if (sayings[i] < *first)	// compare object values
	first = &sayings[i];	// assign object address
``` 

### 12.5.3 再谈定位new运算符
```C++
// placenew1.coo --  plant
#include <iostream>
#include <string>
#include <new>
using namespace std;
const int BUF = 512;
class JustTesting
{
private:
	string words;
	int number;
public:
	JustTesting(const string& s = "Just Testing", int n = 0)
	{
		words = s; number = n; cout << words << " constructed\n";
	}

	~JustTesting() 
	{
		cout << words << " destroyed\n";
	}

	void Show() const { cout << words << ", " << number << endl; }
};

int main()
{
	char* buffer = new char[BUF];			// get a block of memory
	JustTesting* pc1, * pc2;

	pc1 = new(buffer) JustTesting;			// place object in buffer
	pc2 = new JustTesting("Heap1", 20);		// place object on heap

	cout << "Memory block addewsses:\n" << "buffer: "
		<< (void*)buffer << " heap: " << pc2 << endl;  // 注意：如果转换为(char *)，打印出的依然是字符串
	cout << "Memory contents:\n";
	cout << pc1 << ": ";
	pc1->Show();
	cout << pc2 << ": ";
	pc2->Show();

	JustTesting* pc3, * pc4;
	pc3 = new (buffer) JustTesting("Bad Idea", 6);
	pc4 = new JustTesting("Heap2", 10);

	cout << "Memory contents:\n";
	cout << pc3 << ": ";
	pc3->Show();
	cout << pc4 << ": ";
	pc4->Show();

	delete pc2;
	delete pc4;
}
```
- 程序清单12.8 在使用定位new运算符时存在两个问题：
	- 首先，在创建第二个对象时，定位new运算符使用一个新对象来覆盖用于第一个对象的内存单元。显然，如果类动态地为其成员分配内存，这将引发问题。
	- 其次，将delete用于pc2和pc4时，将自动调用为pc2和pc4指向的对象调用析构函数；然而，将delete[ ]用于buffer时，不会为使用定位new运算符创建的对象调用析构函数。

- 这里的教训与第9章相同：程序员必须负责管理定位new运算符中使用的缓存器内存单元。可以这样做：
```C++
pc1 = new(buffer) JustTesting;
pc3 = new(buffer + sizeof(JustTesting)) JustTesting("Better Idea", 6);

// 释放时应该使用 delete[] buffer 。对于他们的析构函数，需要显式去调用
pc3->~JustTesting();
pc1->~JustTesting();
delete[] buffer
```

- 新的程序如下：
```C++
// placenew2.cpp -- new, placement new, no delete
#include <iostream>
#include <string>
#include <new>

using namespace std;
const int BUF = 512;
class JustTesting
{
private:
	string words;
	int number;
public:
	JustTesting(const string& s = "Just Testing", int n = 0)
	{
		words = s; number = n; cout << words << " constructed\n";
	}

	~JustTesting()
	{
		cout << words << " destroyed\n";
	}

	void Show() const { cout << words << ", " << number << endl; }
};

int main()
{
	char* buffer = new char[BUF];			// get a block of memory

	JustTesting* pc1, * pc2;

	pc1 = new(buffer) JustTesting;			// place object in buffer
	pc2 = new JustTesting("Heap1", 20);		// place object on heap

	cout << "Memory block addewsses:\n" << "buffer: "
		<< (void*)buffer << " heap: " << pc2 << endl;
	cout << "Memory contents:\n";
	cout << pc1 << ": ";
	pc1->Show();
	cout << pc2 << ": ";
	pc2->Show();

	JustTesting* pc3, * pc4;
	pc3 = new (buffer + sizeof(JustTesting)) JustTesting("Bad Idea", 6);
	pc4 = new JustTesting("Heap2", 10);

	cout << "Memory contents:\n";
	cout << pc3 << ": ";
	pc3->Show();
	cout << pc4 << ": ";
	pc4->Show();

	delete pc2;
	delete pc4;

	pc3->~JustTesting();
	pc1->~JustTesting();
	delete[] buffer;  // free buffer
	cout << "Done\n";
	return 0;
}
```

## 12.6 复习各种技术
### 12.6.1 重载<<运算符

### 12.6.2 转换函数
```C++
c_name(type_name value);

operator type_name();
```

### 12.6.3 其构造函数使用new的类
- 应定义一个分配内存（而不是将指针指向已有内存）的复制构造函数。这样程序将能够将类对象初始化为另一个类对象。这种构造函数的原型通常如下：
```C++
className(const className& )
```
- 应定义一个重载赋值运算符放类成员函数，其函数定义如下（其中c_pointer是c_name的类成员，类型为指向type_name的指针）。下面的示例假设使用new[]来初始化变量c_pointer:
```C++
c_name& c_name::operator=(const c_name& cn)
{
	if (this == &cn)
		return *this;
	
	delete[] c_pointer;
	// set size number of type_name units to be copied
	c_pointer = new type_name[size];
	// then copy data pointed to by cn.c_pointer to location pointed to by c_pointer
	return *this;
}
```

## 12.7队列模拟
### 嵌套结构和类
- 在类声明中声明的结构、类或枚举被称为是嵌套在类中，其作用域为整个类。这种声明不会创建数据对象，而只是制定了可以在类中使用的类型。
- 如果声明是在类的私有部分进行的，则只能在这个类使用被声明的类型
- 如果声明是在公有部分进行的，则可以从类的外部通过作用域解析运算符使用被声明的类型

#### 3.类方法
- 对于const数据成员，必须在执行到构造体之前，即创建对象时进行初始化。C++提供了一种特殊的语法来完成上述工作，它叫做成员初始化列表（member initializer list）
- **只有构造函数可以使用这种语法，对于const类成员必须使用这种语法**
- 另外对于被声明为引用的类成员，也必须使用这种语法。这是因为引用与const数据类似，只能在被创建是进行初始化。
- 对于本身是类对象的成员来说，使用成员初始化列表的效率更高

#### 成员列表初始化
- 初始化工作在对象创建时完成
- 这种格式只能用于构造函数
- 必须用这种格式来初始化非静态const数据成员（至少在C++11之前是这样的）
- 必须用这种格式来初始化引用数据成员
```C++
// 成员列表初始化也可用于常规初始化。也就是说，如果愿意，可以将下述代码
int games = 162;
double talk = 2.71828;
// 替换为
int games(162);
double talk(2.71828);
```
- 分配内存（创建）-> 初始化（构造函数花括号之前，参数列表之后）-> 赋值（构造函数花括号内）

#### C++11的类内初始化
```C++
class Classy
{
	int mem1 = 10;		// in-class initialization
	const int mem2 = 20;	// in-class initialization
};  // 丹哥之前让我用的方式
// 这与在构造函数中使用成员初始化列表等价：
Classay::Classy(int n):mem1(n) { .. }
```
- 如果两种都使用，成员列表初始化的值将覆盖类内初始化的值

#### 4.是否需要其他类方法
- 如果队列进行复制的话，没处理好可能导致两个队列共用节点，将会导致错误。这里暂时忽略队列的复制，为了防止队列复制在运行时产生错误，将复制构造函数和赋值运算符设置为私有的，避免了崩溃。（也就是说，不让复制和赋值了）
- 幸运的是，有一种小小的技巧可以避免这些额外的工作，并确保程序不会崩溃。这就是将所需的方法定义为伪私有方法：
```C++
class Queue
{
private:
	Queue(const Queue& q) : qsize(0) { }	// preemptive definition
	Queue& operator=(const Queue& q) {return *this;}
};

Queue snick(nip);  	// not allowed
tuck = nip;		// not allowed
```
- 这样有两个好处：
1. 它避免了本来自动生成的默认方法定义。
2. 因为这些方法是私有的，所以不能被广泛使用。
```C++
// 也就是说，如果nip和tuck是Queue对象，则编译器就不允许这样做：
Queue snick(nip);		// not allowed
tuck = nip;   			// npt allowed
```
- **所以，与其将来面对无法预料的运行故障，不如得到一个易于追踪的编译错误，指出这些方法是不可访问的。**
- C++11提供了另一种禁用方法的方式——使用了关键字delete，这将在第18章介绍。
- 还有一个点：当对象按值传递（返回）时，复制构造函数将被调用。然而，如果遵循优先采用按引用传递对象的惯例，将不会有任何问题。另外，赋值构造函数还被用于创建其他的临时对象，但Queue定义中并没有导致创建临时对象的操作，例如重载加法运算符

```C++
// 12.10 -- interface for a queu
#ifndef _QUEUE_H_
#define _QUEUE_H_
// This queue will contain Customer items
class Customer
{

private:
	long arrive = 0;				// arrival tiem for customer
	long processtime = 0;			// processing timer for customer

public:
	Customer(){};

	void set(long when);
	long when() const { return arrive; }
	int ptime() const { return processtime; }
};

typedef Customer Item;

class Queue
{
private:
	struct Node
	{
		Item item;
		struct Node* next;
	};
	enum {Q_SIZE = 10};
	Node* front;
	Node* rear;
	int items;
	const int qsize;
	Queue(const Queue* q): qsize(0){ }
	Queue& operator=(const Queue& q) { return *this; }

public:
	Queue(int qs = Q_SIZE);		// create queue with a qs
	~Queue();
	bool isempty() const;
	bool isfull() const;
	int queuecount() const;
	bool enqueue(const Item& item);		// add item to end
	bool dequeue(Item& item);			// remove item from front
};

#endif  // _QUEUE_H_
```

```C++
// queue.cpp -- Queue and Customer methods
#include "queue.h"
#include <cstdlib>  // for rand()

// Queue methods
Queue::Queue(int qs) : qsize(qs)
{
	front = rear = nullptr;
	items = 0;
}

Queue::~Queue()
{
	Node* temp;

	while (front != nullptr)
	{
		temp = front;
		front = front->next;
		delete temp;
	}
}

bool Queue::isempty() const
{
	return 0 == items;
}

bool Queue::isfull() const
{
	return qsize == items;
}

int Queue::queuecount() const
{
	return items;
}

bool Queue::enqueue(const Item& item)
{
	if (isfull())
	{
		return false;
	}

	Node* add = new Node;		// create node
	// on failure, new throws std::bad_alloc exception
	add->item = item;			// set node pointers
	add->next = nullptr;		// or nullptr
	items++;

	if (front == nullptr)
	{
		front = add;
	}
	else
	{
		rear->next = add;
	}

	rear = add;
	return true;
}

bool Queue::dequeue(Item& item)
{
	if (front == nullptr)
	{
		return false;
	}

	item = front->item;
	Node* temp = front;
	front = front->next;

	delete temp;

	if (items == 0)
	{
		rear = nullptr;
	}

	return true;
}

// time set to a random value in the range 1 - 3
void Customer::set(long when)
{
	processtime = std::rand() % 3 + 1;
	arrive = when;
}
```

### 12.7.3 ATM模拟
```C++
// 12.12 bank.cpp -- using the Queue interface
// compile with queue.cpp
#include <iostream>
#include <cstdlib>							// for rand() and srand()
#include <ctime>							// for time()
#include "queue.h"
const int MIN_PER_HR = 60;

bool newcustomer(double x);					// is there a new customer?

int main()
{
	using namespace std;
	srand(time(0));

	cout << "Case Study: Bank of Heather Automatic Teller\n";
	cout << "Enter maximum size of queue: ";
	int qs;
	cin >> qs;
	Queue line(qs);							// line queue holds up to qs people

	cout << "Enter the number of simulation hours: ";
	int hours;
	cin >> hours;
	// simulation will run 1 cycle per minute
	long cyclelimit = MIN_PER_HR * hours;	// # of cycles

	cout << "Enter the average number of customers per hour: ";
	double perhour;
	cin >> perhour;
	double min_per_cust;					// average time berween arrivals
	min_per_cust = MIN_PER_HR / perhour;

	Item temp;								// new customer data
	long turnaways = 0;						// turned away by full queue
	long customers = 0;						// joined the queue
	long served = 0;						// served during the simulation
	long sum_line = 0;						// cumulative line length
	int wait_time = 0;						// time until autoteller is free
	long line_wait = 0;						// cumulative time in line

	// runing the simulation
	for (int cycle = 0; cycle < cyclelimit; cycle++)
	{
		if (newcustomer(min_per_cust))		// have newcomer
		{
			if (line.isfull())
			{
				turnaways++;
			}
			else
			{
				customers++;
				temp.set(cycle);			// cycle = time of arrival
				line.enqueue(temp);			// add newcomer to line
			}
		}
		
		if (wait_time <= 0 && !line.isempty())
		{
			line.dequeue(temp);				// attend next customer
			wait_time = temp.ptime();		// for wait_time minutes
			line_wait += cycle - temp.when();
			served++;
		}

		if (wait_time > 0)
		{
			wait_time--;
		}

		sum_line += line.queuecount();
	}

	// reporting results
	if (customers > 0)
	{
		cout << "customers accepted: " << customers << endl;
		cout << " customers served: " << served << endl;
		cout << "		turnaways: " << turnaways << endl;
		cout << "average queue size: ";
		cout.precision(2);
		cout.setf(ios_base::fixed, ios_base::floatfield);
		cout << (double)sum_line / cyclelimit << endl;
		cout << " average wait time: "
			<< (double)line_wait / served << " minutes\n";
	}
	else
	{
		cout << "No customers!\n";
	}
	cout << "Done!\n";

	return 0;
}

// x = average time, in minutes, between customers
// return value is true if customer shows up this minute
bool newcustomer(double x)
{
	return (std::rand() * x / RAND_MAX < 1);
}
```
- [x] 每小时达到的客户从15名增加到30名时，等候时间并不是加倍，而是增加15倍。

## 12.8 总结
- 对于诸如复制构造函数等概念，都是在由于忽略它们而遇到了麻烦后逐步理解。
- 你可能已经注意到，与简单的C相比，C++需要注意的类细节要多得多。作为回报，C++的功能也更强。

## 12.9 复习题
- 1
```C++
// a：成员未初始化，指针是野指针，指向未知的内存块，可能引发程序崩溃。
// b：str与s指向同一内存地址，如果s为临时的字符串，s释放后str将引发错误。
// c：如果内存不够，str的长度将小于s，len的值将不能准确描述str的长度（X）
```
```C++
// 答案
// a: 设为NULL（nullptr）或用new来初始化
// b: 应当使用new[ ] 和strcpy()
// c: 没有分配内存空间，应当使用new char[len + 1]来分配适当数量的内存 
```
- 2
```C++
// 1.未在析构函数中释放。所以，需要及时在析构函数中释放
// 2.未重写复制构造函数，导致两个对象共用同一个实例。需要重写复制构造函数
// 3.未重写赋值运算符（=）。导致的问题和上面一样。
```
```C++
// 答案
// 1. 应当析构
// 2. 赋值时不指向相同的数据，否则析构时将释放两次出现错误。删除之前的，重新分配内存并进行赋值
// 3. 和我答的一样
```

- 3
```C++
// 1.构造函数。用于初始化操作
// 2.析构函数。用于释放操作
// 3.复制构造函数。用于拷贝对象
```
```C++
// 答案
// 1. 构造函数：不完成任何工作，但使得能够声明数组和未初始化的对象（ ？）
// 2. 复制构造函数：使用成员赋值
// 3. 赋值运算符：使用成员赋值
// 4. 析构函数：不完成任何工作
// 5. 地址运算符：隐式地址运算符返回调用对象的地址（即this指针的值）
```

- 4
```C++
// 答案：方案一
char personality[40];

public:
	nifty(const char* s)l
	friend ostream& operator<<(ostream& os, const nitify& n);
};

nifty::nitfy()
{
	personality[0] = '\0';
	talents = 0;
}

nifty::nifty(const char* s)
{
	strcpy(personality, s);
	talents = 0;
}

ostream& operator<<(ostream& os, const nitify& n)
{
	os << n.personality << '\n';
	os << n.talent << '\n';
	return os;
}
```
```C++
// 答案：方案二
char* personality;

nifty::nifty(const char* s)
{
	personality = new char[strlen(s) + 1];
	strcpy(personality, s);
	talents = 0;
}
```

- 5
```C++
// a:
// #1 Golfer();
// #2 Golfer(const char* name, int g = 0);
// #3 Golfer(const char* name, int g = 0);
// #4 Golfer(); Golfer(const Golfer& g);
// #5 Golfer(const Golfer& g); 
// #6 Golfer(const char* name, int g = 0);
// #7 默认赋值运算符
// #8 Golfer(const char* name, int g = 0);

// b :
// ~Golger(); // 析构函数用于使用字符串分配的空间
// Golfer friend operator = (const Golfer& g1, const Golfer& g2);
```
```C++
// 答案
// #4 只有默认构造函数，不需要拷贝构造函数
// #8 Golfer(const char* name, int g = 0);然后调用默认构造函数
// 注意：对于语句5和6，有些编译器还将调用默认的赋值运算符。
```

## 12.10 编程练习
- [ ] 第二题有点头大，字符串连接到底怎样才算合理
- 1
```C++
#ifndef _COW_H_
#define _COW_H_
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>

class Cow
{
	enum { HOBBY_SIZE = 20 };
	char name[20];
	char* hobby;
	double weight;
public:
	Cow();
	Cow(const char* nm, const char* ho, double wt);
	Cow(const Cow& c);
	~Cow();
	Cow& operator=(const Cow& c);
	void ShowCow() const;   // display all cow data
};

#endif
```

```C++
#include "cow.h"
#include <cstring>

Cow::Cow()
{
	strcpy(name, "DefaultCow");

	hobby = new char[4];
	strcpy(hobby, "Cow");

	weight = 10;
}

Cow::Cow(const char* nm, const char* ho, double wt)
{
	strcpy(name, nm);

	int len = strlen(ho);
	hobby = new char[len + 1];
	strcpy(hobby, ho);

	weight = wt;
}

Cow::Cow(const Cow& c)
{
	std::cout << "Cow(const Cow& c)" << std::endl;
	strcpy(name, c.name);

	int len = strlen(c.hobby);
	hobby = new char[len + 1];
	strcpy(hobby, c.hobby);

	weight = c.weight;
}

Cow::~Cow()
{
	delete[] hobby;
}

Cow& Cow::operator=(const Cow& c)
{
	std::cout << "call operator= "<< std::endl;
	if (this == &c)
	{
		return *this;
	}

	strcpy(name, c.name);

	delete[] hobby;
	int len = strlen(c.hobby);
	hobby = new char[len + 1];
	strcpy(hobby, c.hobby);

	weight = c.weight;
	return *this;
}

void Cow::ShowCow() const
{
	std::cout << "Name: " << name << std::endl;
	std::cout << "Hobby: " << hobby << std::endl;
	std::cout << "weight: " << weight << std::endl;
}
```
```C++
#include "cow.h"

int main()
{
	Cow c1;
	Cow c2("hahaha", "heiheihei", 200.3);
	Cow c3(c2);  // attention

	c1.ShowCow();
	c2.ShowCow();
	c3.ShowCow();

	c1 = c2;
	c1.ShowCow();
}
```

- 2
```C++
#include <iostream>

#ifndef _STRNG2_H_
#define _STRNG2_H_


using std::istream;
using std::ostream;

class String2
{
private:
	char* str;
	int len;
	static int num_strings;
	static const int CINLIM = 80;

public:
	// constructors and other methods
	String2(const char* s);
	String2();
	String2(const String2&);
	~String2();
	int length() const { return len; }
	void stringlow();
	void stringup();
	int has(const char& ch);

	// overloaded operator methods
	String2& operator=(const String2& st);
	String2& operator=(const char* s);
	char& operator[](int i);
	const char& operator[](int i) const;

	// overloaded operator friends
	friend String2 operator+(const String2& st1, const String2& st2);
	friend bool operator<(const String2& st1, const String2& st2);
	friend bool operator>(const String2& st1, const String2& st2);
	friend bool operator==(const String2& st1, const String2& st2);
	friend ostream& operator<<(ostream& os, const String2& st);
	friend istream& operator>>(istream& is, String2& st);

	// static function
	static int HowMany();
};

#endif  // _STRNG2_H_
```
```C++
#define _CRT_SECURE_NO_WARNINGS
#include "string2.h"
#include <cstring>	
#include <cctype>

using std::cout;

// initializing static class member
int String2::num_strings = 0;

// static method
int String2::HowMany()
{
	return num_strings;
}

// class methods
String2::String2(const char* s)
{
	len = std::strlen(s);
	str = new char[len + 1];
	std::strcpy(str, s);
	num_strings++;
}

String2::String2()
{
	len = 4;
	char* str = new char[4];
	std::strcpy(str, "C++");
	num_strings++;
}

String2::String2(const String2& st)
{
	num_strings++;
	len = st.len;
	str = new char[len + 1];
	std::strcpy(str, st.str);
}

String2::~String2()
{
	cout << "~String2(): " << str << std::endl;

	--num_strings;
	delete[] str;
}

void String2::stringlow()
{
	// method 0: 用不了，因为字符数组不是容器，没有迭代器
	//for (auto ch: str)
	//{
	//	ch = std::tolower(ch);
	//} 

	// method 1: 用指针
	for (char* ptr = str; *ptr != '\0'; ++ptr)
	{
		if (std::isupper(*ptr))
		{
			*ptr = std::tolower(*ptr);
		}
	}


}

void String2::stringup()
{
	// method2: 索引变量
	for (int i = 0; i < len; ++i)
	{
		if (std::islower(str[i]))
		{
			str[i] = std::toupper(str[i]);
		}
	}
}

int String2::has(const char& ch)
{
	int count = 0;

	for (char* ptr = str; *ptr != '\0'; ++ptr)
	{
		if (*ptr == ch)
		{
			count++;
		}
	}

	return count;
}

// overloaded operator methods
String2& String2::operator=(const String2& st)
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
String2& String2::operator=(const char* s)
{
	delete[] str;
	len = std::strlen(s);
	str = new char[len + 1];
	std::strcpy(str, s);
	return *this;
}

// read-write char access for non-const String
char& String2::operator[](int i)
{
	return str[i];
}

// read-only char access for const string
const char& String2::operator[](int i) const
{
	return str[i];
}

String2 operator+(const String2& st1, const String2& st2)
{
	int len = st1.len + st2.len;

	char* result = new char[len + 1];
	std::strcpy(result, st1.str);
	std::strcat(result, st2.str);

	String2 temp(result);
	delete[] result;

	return temp;
}

bool operator<(const String2& st1, const String2& st2)
{
	return (std::strcmp(st1.str, st2.str) < 0);
}

bool operator>(const String2& st1, const String2& st2)
{
	return st2 < st1;
}

bool operator==(const String2& st1, const String2& st2)
{
	return (std::strcmp(st1.str, st2.str) == 0);
}

ostream& operator<<(ostream& os, const String2& st)
{
	os << st.str;
	return os;
}

istream& operator>>(istream& is, String2& st)
{
	char temp[String2::CINLIM];
	is.get(temp, String2::CINLIM);

	if (is)
	{
		st = temp;
	}

	while (is && is.get() != '\n')
	{
		continue;
	}

	return is;
}
```
```C++
#include "string2.h"
#include <iostream>
using namespace std;

int main()
{
	String2 s1(" and I am a C++ student.");
	String2 s2 = "Please enter your name: ";
	String2 s3;

	cout << s2;
	cin >> s3;

	s2 = "My name is " + s3;

	cout << s2 << ".\n";
	s2 = s2 + s1;
	s2.stringup();

	cout << "The String\n" << s2 << "\ncontains " << s2.has('A')
		<< " 'A' characters in it.\n";

	s1 = "red"; // String(const char*)，
				// then String & operator= (const String&)

	String2 rgb[3] = { String2(s1), String2("green"),String2("blue") };
	cout << "Enter the name of a primary color for mixing light: ";
	String2 ans;
	bool success = false;
	while (cin >> ans)
	{
		ans.stringlow();
		for (int i = 0; i < 3; i++)
		{
			if (ans == rgb[i])  // overloaded == operator
			{
				cout << "That 's right ! \n";
				success = true;
				break;
			}

		}

		if (success)
			break;
		else
			cout << "Try again!\n";
	}
	cout << "Bye\n";
	return 0;
}
```
- 注意：有两种将C字符串和C++字符串连接的方法：
	- 第一种比较直接，但是针对C字符串和C++字符的连接需要写两个函数。
 	- 第二种表面上简单，用一个函数就可以完成C字符与C++字符串的连接和C++字符串之间的连接，但是需要注意当第一个参数是C字符串时，会调用构造函数先把C字符串构造成C++字符串，然后再进行连接。
```C++
// method 1: concatenate c string and C++ string
String2 operator+(const String2& st);
friend String2 operator+(const char* s, const String2& st);

// method 2: concatenate c string and C++ string
friend String2 operator+(const String2& st1, const String2& st2);
```
- 3
```C++
#ifndef STOCK20_H_
#define STOCK20_H_

#include <iostream>

using std::ostream;

class Stock
{
private:
	char* company;
	int len;
	long shares;  // 所持有的股票数量
	double share_val;  // 每股的价格
	double total_val;  // 股票总价格
	void set_tot() { total_val = shares * share_val; }
public:
	Stock();
	Stock(const char* co, long n = 0, double pr = 0.0);
	~Stock();
	void buy(long num, double price);
	void sell(long num, double price);
	void update(double price);

	const Stock& topval(const Stock& s) const;
	friend ostream& operator<<(ostream& os, const Stock& st);
};

#endif // STOCK20_H_
```

```C++
#include "stock20.h"
#include <cstring>

// constructors (verbose versions)
Stock::Stock()  // default constructor
{
	std::cout << "Default constructor called\n";
	len = 7;
	company = new char[len + 1];
	std::cout << "sizeof company: " << sizeof(company) << std::endl;
	strcpy_s(company, len + 1, "on name");

	shares = 0;
	share_val = 0.0;
	total_val = 0.0;
}

Stock::Stock(const char* co, long n, double pr)
{
	std::cout << "Constructor using " << co << "called\n";
	len = (int)std::strlen(co);
	company = new char[len + 1];
	strcpy_s(company, len + 1, co);

	if (n < 0)
	{
		std::cout << "Number of shares can't be negative; "
			<< company << " shares set to 0.\n";
		shares = 0;
	}
	else
		shares = n;
	share_val = pr;
	set_tot();
}

// class destructor
Stock::~Stock()
{
	std::cout << "Bey, " << company << "\n";
	delete[] company;
}

// other methods
void Stock::buy(long num, double price)
{
	if (num < 0)
	{
		std::cout << "Number of shares purchased can't be negative."
			<< "Transaction is aborted.\n";
	}
	else
	{
		shares += num;
		share_val = price;
		set_tot();
	}
}

void Stock::sell(long num, double price)
{
	using std::cout;
	if (num < 0)
	{
		cout << "Number of share sold can't be negative. "
			<< "Transaction is aborted.\n";
	}
	else if (num > shares)
	{
		cout << "You can't sell more than you have! "
			<< "Transaction is aborted.\n";
	}
	else
	{
		shares -= num;
		share_val = price;
		set_tot();
	}
}

void Stock::update(double price)
{
	share_val = price;
	set_tot();
}

const Stock& Stock::topval(const Stock& s) const {
	if (s.total_val > total_val)
	{
		return s;
	}
	else
	{
		return *this;
	}
}

ostream& operator<<(ostream& os, const Stock& st)
{
	using std::cout;
	using std::ios_base;

	// set format to #.###
	ios_base::fmtflags orig =
		cout.setf(ios_base::fixed, ios_base::floatfield);
	std::streamsize prec = cout.precision(3);

	std::cout << "Company: " << st.company
		<< "Share: " << st.shares << '\n'
		<< " Share Price: $" << st.share_val;

	// set format to #.##
	cout.precision(2);
	std::cout << " Total Worth: $" << st.total_val << '\n';

	// restore original format
	cout.setf(orig, ios_base::floatfield);
	cout.precision(prec);

	return os;
}

```
```C++
// 10.9 usestock2.cpp -- using the Stock class
// compile with stock20.cpp
#include "Stock20.h"
#include <iostream>

const int STKS = 4;

int main()
{
	Stock stocks[STKS] = {
		Stock("NanoSmart", 12, 20.0),
		Stock("Boffo Object", 200, 2.0),
		Stock("Monolithic Obelisks", 130, 3.25),
		Stock("Fleep Enterprises", 60, 6.5)
	};

	std::cout << "Stock holding:\n";
	int st;
	for (st = 0; st < STKS; st++)
	{
		std::cout << stocks[st] << std::endl;
	};

	// set pointer to first element
	const Stock* top = &stocks[0];
	for (st = 1; st < STKS; st++)
	{
		top = &top->topval(stocks[st]);
	}

	// now top points to the most valuable holding
	std::cout << "\nMost valuable holding:\n";
	std::cout << *top << std::endl;
	return 0;
}
```
- 4
```C++
// 10.10 stack.h -- class definition for the stack ADT
#ifndef STACK_H_
#define STACK_H__

typedef unsigned long Item;

class Stack
{
private:
	enum { MAX = 10 };  // constant specific to class
	Item* pItems;  // holds stack items
	int size;  // number or elements in stack
	int top;  // index for top stack item
public:
	Stack(int n = MAX);
	Stack(const Stack& st);
	~Stack();
	bool isempty() const;
	bool isfull() const;
	// push() returns false if static already is full, true otherwise
	bool push(const Item& item);  // add item to stack
	// pop() returns false if stack already is empth, true otherwise
	bool pop(Item& item);  // pop top into item
	Stack& operator=(const Stack& st);
};
#endif
```
```C++
#include "stack.h"

Stack::Stack(int n)
{
	top = 0;
	size = n;
	pItems = new Item[size];
}

Stack::Stack(const Stack& st)
{
	top = st.top;
	size = st.size;
	pItems = new Item[size];

	// 注意：
	for (int i = 0; i < top; ++i)
	{
		pItems[i] = st.pItems[i];
	}
}

Stack::~Stack()
{
	delete[] pItems;
}

bool Stack::isempty() const
{
	return top == 0;
}

bool Stack::isfull() const
{
	return top == size;
}

bool Stack::push(const Item& item)
{
	if (isfull())
	{
		return false;
	}
	else
	{
		pItems[top++] = item;
		return true;
	}
}

bool Stack::pop(Item& item)
{
	if (isempty())
	{
		return false;
	}
	else
	{
		item = pItems[--top];
		return true;
	}
}

Stack& Stack::operator=(const Stack& st)
{
	if (&st == this)
	{
		return *this;
	}

	delete[] pItems;

	top = st.top;
	size = st.size;
	pItems = new Item[size];

	for (int i = 0; i < top; ++i)
	{
		pItems[i] = st.pItems[i];
	}
	
	return *this;
}

```
- 测试代码由ChatGPT代写
```C++
#include <iostream>
#include "stack.h"

int main()
{
	using namespace std;

	Stack st1(5);  // Create a stack with size 5

	// Push items onto the stack
	st1.push(10);
	st1.push(20);
	st1.push(30);

	// Display the content of the stack
	cout << "Stack 1: ";
	while (!st1.isempty())
	{
		Item item;
		st1.pop(item);
		cout << item << " ";
	}
	cout << endl;

	// Create another stack using the copy constructor
	Stack st2 = st1;

	// Push items onto the second stack
	st2.push(100);
	st2.push(200);

	// Display the content of the second stack
	cout << "Stack 2: ";
	while (!st2.isempty())
	{
		Item item;
		st2.pop(item);
		cout << item << " ";
	}
	cout << endl;

	// Create a third stack and use the assignment operator
	Stack st3;
	st3 = st2;

	// Push items onto the third stack
	st3.push(1000);
	st3.push(2000);
	st3.push(3000);

	// Display the content of the third stack
	cout << "Stack 3: ";
	while (!st3.isempty())
	{
		Item item;
		st3.pop(item);
		cout << item << " ";
	}
	cout << endl;

	return 0;
}
```
- 5 CharGTP给了这个答案，说实话我也懒得看，因为这道题实在是太麻烦。
```C++
int main()
{
    // ... (existing code)

    // Number of hours for the simulation
    int hours;
    cout << "Enter the number of simulation hours: ";
    cin >> hours;

    // Target average waiting time (1 minute)
    double targetWaitTime = 1.0; // 1 minute

    // Initial guess for customers per hour
    double customersPerHour = 10.0; // You can adjust this based on your initial estimate

    // Run the simulation loop
    while (true)
    {
        Queue line(Q_SIZE); // Create a new queue for each iteration

        // ... (existing code for the simulation)

        // Calculate average waiting time
        double averageWaitTime = (double)line_wait / served;

        // Output the current results
        cout << "For an arrival rate of " << customersPerHour << " customers per hour:\n";
        cout << "Average wait time: " << averageWaitTime << " minutes\n";

        // Check if the average waiting time is close to the target
        if (fabs(averageWaitTime - targetWaitTime) < 0.1) // Adjust the threshold as needed
        {
            cout << "Simulation converged to target average waiting time.\n";
            break;
        }
        else
        {
            // Adjust the arrival rate for the next iteration
            customersPerHour += (targetWaitTime - averageWaitTime) * 0.1; // Adjust the factor as needed
        }
    }

    // ... (output additional statistics or results)

    return 0;
}

```

- 6 连ChatGPT都懒得写详细，可见这种题目多恶心。
```C++
#include "queue.h"

// 在 Bank 类中使用两个队列
class Bank
{
private:
    Queue line1; // 第一个队列
    Queue line2; // 第二个队列

public:
    // 其他成员函数不变
};

// 在主程序中修改部分代码
int main()
{
    // ...
    Bank bank; // 使用两个队列的银行

    // 修改模拟循环
    for (int cycle = 0; cycle < cyclelimit; cycle++)
    {
        if (newcustomer(min_per_cust)) // 有新客户
        {
            // 根据队列长度选择加入哪个队列
            if (bank.queue1length() <= bank.queue2length())
            {
                if (bank.queue1isfull())
                {
                    turnaways++;
                }
                else
                {
                    customers++;
                    temp.set(cycle);    // cycle = 到达时间
                    bank.enqueue1(temp); // 加入第一个队列
                }
            }
            else
            {
                if (bank.queue2isfull())
                {
                    turnaways++;
                }
                else
                {
                    customers++;
                    temp.set(cycle);    // cycle = 到达时间
                    bank.enqueue2(temp); // 加入第二个队列
                }
            }
        }

        // 依次处理两个队列
        // ...
    }
    // ...
}

```



