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

## 2.复制构造函数
- 复制构造函数用于将一个对象复制到新创建的对象中。也就是说，它用于初始化过程中（包括按值传递参数）而不是常规的赋值过程

## 12.2 改进后的新String类
















