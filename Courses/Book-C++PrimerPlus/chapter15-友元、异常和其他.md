# 第15章 友元、异常和其他

本章内容包括：  
- 友元类
- 友元类方法
- 嵌套类
- 引发异常、try块和catch块
- 异常类
- 运行阶段类型识别（RTTI）
- dynamic_cast和typeid
- static_cast、const_cast和reinterpret_cast

## 15.1 友元

- 友元类的所有方法都可以访问原始类的私有成员和保护成员。
- 也可以只将特定的成员函数指定为另一个类的友元。

### 15.1.1 友元类
- 友元声明可以位于公有、私有或保护部分，其所在的位置无关紧要
```C++
#pragma once

// 15.1 tv.h -- Tv and Remote classes
#ifndef _TV_H_
#define  _TV_H_

class Tv
{
public:
	friend class Remote;	// Remote can access Tv private parts
	enum { Off, On };
	enum { MinVal, MaxVa = 20 };
	enum { Antenna, Cable };  // 有线电视 or 天线
	enum { TV, DVD };

	Tv(int s = Off, int mc = 125) :state(s), volume(5),
		maxchannel(mc), channel(2), mode(Cable), input(TV) {}
	void onoff() { state = (state == On) ? Off : On; }
	bool ison() const { return state == On; }
	bool volup();
	bool voldown();
	void chanup();
	void chandown();
	void set_mode() { mode = (mode == Antenna) ? Cable : Antenna; }
	void set_input() { input = (input == TV) ? DVD : TV; }
	void setttings() const;	// display all settubgs

private:
	int state;				// on or off
	int volume;				// assumed to be digitized
	int maxchannel;			// maximum number of channels
	int channel;			// current channel setting
	int mode;				// broadcast or cable
	int input;				// TV or DVD
};

class Remote
{
private:
	int mode;				// control TV or DVD

public:
	Remote(int m = Tv::TV) : mode(m) {} 
	bool volup(Tv& t) { return t.volup(); } 
	bool voldown(Tv& t) { return t.voldown(); } 
	void onoff(Tv& t) { t.onoff(); } 
	void chanup(Tv& t) { t.chanup(); } 
	void chandown(Tv& t) { t.chandown(); } 
	void set_chan(Tv& t, int c) { t.channel = c; } 
	void set_mode(Tv& t) { t.set_mode(); } 
	void set_input(Tv& t) { t.set_input(); }
};
#endif  // _TV_H_
```
- 在附录E有按位运算的内容。可以用异或简化条件切换的代码 ``` void onoff() { state ^= 1; } ```
```C++
// 15.2 tv.cpp
#include "tv.h"
#include <iostream>

bool Tv::volup()
{
	if (volume < MaxVa)
	{
		volume++;
		return true;
	}
	else
	{
		return false;
	}
}

bool Tv::voldown()
{
	if (volume > MinVal)
	{
		volume--;
		return true;
	}
	else
	{
		return false;
	}
}

void Tv::chanup()
{
	if (channel < maxchannel)
	{
		channel++;
	}
	else
	{
		channel = 1;
	}
}

void Tv::chandown()
{
	if (channel > 1)
	{
		channel--;
	}
	else
	{
		channel = maxchannel;
	}
}

void Tv::setttings() const
{
	using std::cout;
	using std::endl;
	cout << "TV is " << (state == Off ? "Off" : "On") << endl;
	if (state == On)
	{
		cout << "Volume setting = " << volume << endl;
		cout << "Channel setting = " << channel << endl;
		cout << "Mode = " << (mode == Antenna?"Antenna" : "cable") << endl;
		cout << "Input = " << (input == TV ? "TV" : "DVD") << endl;
	}
}
```
```C++
// 15.3 use_tv.cpp -- using the Tv and Remote classes
#include <iostream>
#include "tv.h"

int main()
{
	using std::cout;

	Tv s42;
	cout << "Initial settings for 42\" TV:\n";
	s42.setttings();

	s42.onoff();
	s42.chanup();
	cout << "\nAdjusted settings for 42\" TV:\n";
	s42.setttings();

	Remote grey;
	grey.set_chan(s42, 10);
	grey.volup(s42);
	grey.volup(s42);
	cout << "\n42\" settings after using remote:\n";
	s42.setttings();

	Tv s58(Tv::On);
	s58.set_mode();
	grey.set_chan(s58, 28);
	cout << "\n58\" settings: \n";
	s58.setttings();
	return 0;
}
```
- 可以使用同一台遥控器控制两台不同的电视机

### 15.1.2 友元成员函数
- 上面其实只有```Romote::set_chan()```方法访问了类的成员。可以只将其作为另一个类的友元，只不过稍有些麻烦。
- 第一个麻烦：**能否像下面这样排列呢？**

```C++
class Remote;    // forward declaration
class Tv { .. };
class Remote { ... };
```
- 答案是不能。原因在于，编译器在Tv类的声明中看到Remote的一个方法被声明为Tv类的友元之前，应先看到Remote类的声明和set_chan()方法的声明。
- 第二个麻烦：Romote声明包含了内联代码 ``` void onfoo(Tv& t) { t.onoff(); } ``` 。代码中有Tv相关的声明，所以编译器必须已经看到了Tv类的声明，才知道有哪些方法。所以解决的方法是，在Remote声明中只包含方法声明，并将实际的定义放在Tv类之后。
```C++
class Tv;
class Remote { ... };
class Tv { ... };
// put Remotemethod definitions here
```

- 前向声明的作用：检查该原型时，所有的编译器都需要知道Tv是一个类

```C++
#pragma once

// 15.4 tvfm.h -- Tv and Remote classes using a friend member
#ifndef _TV_H_
#define _TV_H_

class Tv;

class Remote
{
private:
	int mode;				// control TV or DVD
	enum { Off, On };
	enum { MinVal, MaxVa = 20 };
	enum { Antenna, Cable };
	enum { TV, DVD };


public:
	Remote(int m = TV) : mode(m) {}
	bool volup(Tv& t);
	bool voldown(Tv& t);
	void onoff(Tv& t);
	void chanup(Tv& t);
	void chandown(Tv& t);
	void set_mode(Tv& t);
	void set_input(Tv& t);
	void set_chan(Tv& t, int c);
};

class Tv
{
public:
	friend void Remote::set_chan(Tv& t, int c);
	enum { Off, On };
	enum { MinVal, MaxVa = 20 };
	enum { Antenna, Cable };
	enum { TV, DVD };

	Tv(int s = Off, int mc = 125) :state(s), volume(5),
		maxchannel(mc), channel(2), mode(Cable), input(TV) {}
	void onoff() { state = (state == On) ? Off : On; }
	bool ison() const { return state == On; }
	bool volup();
	bool voldown();
	void chanup();
	void chandown();
	void set_mode() { mode = (mode == Antenna) ? Cable : Antenna; }
	void set_input() { input = (input == TV) ? DVD : TV; }
	void setttings() const;	// display all settubgs

private:
	int state;				// on or off
	int volume;				// assumed to be digitized
	int maxchannel;			// maximum number of channels
	int channel;			// current channel setting
	int mode;				// broadcast or cable
	int input;				// TV or DVD
};

// Remote methods as inline functions
inline bool Remote::volup(Tv& t) { return t.volup(); }
inline bool Remote::voldown(Tv& t) { return t.voldown(); }
inline void Remote::onoff(Tv& t) { t.onoff(); }
inline void Remote::chanup(Tv& t) { t.chanup(); }
inline void Remote::chandown(Tv& t) { t.chandown(); }
inline void Remote::set_mode(Tv& t) { t.set_mode(); }
inline void Remote::set_input(Tv& t) { t.set_input(); }
inline void Remote::set_chan(Tv& t, int c) { t.channel = c; }
#endif  // _TV_H_
```
- 本书前面介绍过，内联函数的链接性是内部的，这意味着函数定义必须在使用函数的文件中。在这个例子中，内联定义位于头文件中，因此在使用函数的文件中包括头文件可确保将定义放在正确的地方。也可以将定义放在实现文件中，但必须删除关键字inline，这样函数的链接性是外部的。
- 顺便说一句，让整个Remote类称为友元并不需要前向声明，因为友元语句本身已经指出Remote是一个类：```friend class Remote; ```

### 15.1.3 其他友元关系
- 电脑对手柄进行控制，比如上古卷轴5开门的时候手柄会震动。两个类互为友元（也不一定，可能只是调用外部某个方法）
- 需要记住的一点是：对于使用Remote对象的Tv方法，其原型可在Remote类声明之前声明，但必须在Remote类声明之后定义，以便编译器有足够的信息来编译该方法。  

```C++
// 彼此成为友元
class Tv
{
friend class Remote;
public:
	void buzz(Remote& r);
	...
};

class Remote
{
friend class Tv;
public:
	void Bool volup(TV& t) { t.volup(); }
	...
};

inline void Tv::buzz(Remote& r)
{
	...
}
```

### 15.1.4 共同的友元
- 有时候，函数需要访问两个类的私有数据。它可以是一个类的成员，同时是另一个类的友元，但有时将函数作为两个类的友元更合理
- 例如：假定有一个Probe类和一个Analyzer类，前者表示某种可编程的测量设备，后者表示某种可编程的分析设备。这两个类都有内部时钟，且希望它们能够同步，则应该包含下述代码行：
```C++
class Analyzer;  // forward declaration
class Probe
{
	friend void sync(Analyzer& a, const Probe& p);  //sycn a to p
	friend void sync(Probe& p, const Analyzer& a);  //sycn p to a
	// ...
};

class Analyzer
{
	friend void sync(Analyzer& a, const Probe& p);  //sycn a to p
	friend void sync(Probe& p, const Analyzer& a);  //sycn p to a
	// ...
};

// define the friend functions
inline void sync(Analyzer& a, const Probe& p)
{
	// ...
}

inline void sync(Probe& p, const Analyzer& a)
{
	// ...
}
```
- 前向声明使编译器看到Probe类声明中的与欧元声明时，知道Analyzer是一种类型

## 15.2 嵌套类(nested class)
- 包含类的成员函数可以创建和使用被嵌套类的对象
- 仅当声明位于公有部分，才能在包含类的外面使用嵌套类，而且必须使用作用域解析运算符。
- 包含：意味着将类对象作为另一个类的成员，而对类进行嵌套不创建类成员，而是定义了一种类型，该类型仅在包含嵌套类声明的类中有效。
- 嵌套：对类进行嵌套通常是为了帮助实现另一个类，并避免名称冲突。
- 嵌套结构体也是一种变相的嵌套类。因为结构是一种其成员在默认情况下为公有的类
- 假设想在方法文件中(.cpp文件)定义构造函数，则定义必须指出Node类是在Queue类中定义的。这是通过两次类解析运算符来完成的 ```C++  Queue::Node::Node(const Item& i) : item(i), next(0) { } ```

### 15.2.1 嵌套类的访问权限
- 有两种访问权限适合于嵌套类。（感觉说的是一个东西）
- 在那些地方可以使用嵌套类以及如何使用嵌套类，取决于作用域和访问权限

#### 1.作用域
- 如果嵌套类是在另一个类的**私有部分**声明的，则只有后者知道它。例子中，Queue成员可以使用Node对象和指向Node对象的指针，但是呈现出的其他部分甚至不知道Node类。
- 如果嵌套类是在另一个类的**保护部分**声明的，则它对于后者来说是可见的，但对于外部世界则是不可见的。这时候，派生类知道嵌套类，并可以直接创建这种类型的对象
- 如果嵌套类是在另一个类的**公有部分**声明的，允许后者、后者的派生类以及外部世界使用它。由于嵌套类为包含它的类，因此在外部世界使用它是，必须使用类限定符。
- 表 15.1

#### 2.访问控制
- 对嵌套类访问权的控制规则与对常规类相同。
- 总之，类声明的位置决定了类的作用域或可见性。类可见之后，访问控制规则（公有、保护、私有、友元）将决定程序对件套类成员的访问权限

### 15.2.2 模板中的嵌套
- 模板很适合用于实现诸如Queue等容器类

```C++
#pragma once
// 15.5 queuetp.h -- queue template with a nested class
#ifndef _QUEUETP_H_
#define _QUEUETP_H_

template <class Item>
class QueueTP
{
private:
	enum { Q_SIZE = 10 };

	// Node is a nested class definition
	class Node
	{
	public:
		Item item;
		Node* next;

		Node(const Item& i) :item(i), next(nullptr) {}
	};
	
	Node* front;
	Node* rear;
	int items;
	const int qsize;
	QueueTP(const QueueTP* q) : qsize(nullptr) { }
	QueueTP& operator=(const QueueTP& q) { return *this; }

public:
	QueueTP(int qs = Q_SIZE);
	~QueueTP();
	bool isempty() const { return items == 0; }
	bool isfull() const { return items == qsize; }
	int queuecount() const { return items; }
	bool enqueue(const Item& item);	
	bool dequeue(Item& item);
};

// QueueTP methods
template <class Item>
QueueTP<Item>::QueueTP(int qs) : qsize(qs)
{
	front = rear = nullptr;
	items = 0;
}

template<class Item>
QueueTP<Item>::~QueueTP()
{
	Node* temp;
	while (front != nullptr)  // while queue is not yet empty
	{
		temp = front;  // save address of front item
		front = front->next;
		delete temp;
	}
}

// Add item to queue
template<class Item>
bool QueueTP<Item>::enqueue(const Item& item)
{
	if (isfull())
	{
		return false;
	}

	Node* add = new Node(item);  // create node
	// on failue, new throws std::bad_alloc exception
	items++;
	if (front == nullptr)  // if queue is empty
	{
		front = add;  // place item at front
	}
	else
	{
		rear->next = add;  // else place at rear
	}
	rear = add;  // have rear point to new node
	return true;
}

// Place front item into item variable and remove front from queue
template<class Item>
bool QueueTP<Item>::dequeue(Item& item)
{
	if (front == nullptr)
	{
		return false;
	}

	item = front->item;  // set item to first item in queue
	items--;
	Node* temp = front;  // save location of first item
	front = front->next;
	delete temp;

	if (items == 0)
	{
		rear = nullptr;
	}
	return true;
}

#endif // _QUEUETP_H_
```
```C++
// nested.cpp -- using a queue that has a nested class
#include <iostream>
#include <string>
#include "queuetp.h"

int main()
{
	using std::string;
	using std::cin;
	using std::cout;

	QueueTP<string> cs(5);
	string temp;

	while (!cs.isfull())
	{
		cout << "Please enter your name. You will be "
			"served in the order of arrival.\n"
			"name: ";
		getline(cin, temp);
		cs.enqueue(temp);
	}

	cout << "The queue is full. Processing begins!\n";

	while (!cs.isempty())
	{
		cs.dequeue(temp);
		cout << "Now processing " << temp << "...\n";
	}

	return 0;
}
```
- 小结：这一节主要讲嵌套类的访问权限，没有太难的点。和之前一样公有外部访问，私有的内部访问，保护的派生类访问

## 15.3 异常
- 两个数的调和平均数：两个数字倒数的平均值的倒数。``` 2 * x * y / (x + y) ```
- 如果y是x的负值，则上述公式将导致被零除——一种不允许的运算。

### 15.3.1 调用abort()
- Abort()在cstdlib中，其典型实现时向标准错误流（即cerr使用的错误流）发送消息abnormal program termination（程序异常终止），然后终结程序。它还返回一个随实现而异的值，告诉操作系统（如果程序是由另一个程序调用的，则告诉父进程），处理失败。abort()是否刷新文件缓冲区（用于存储读写到文件中的数据的内存区域）取决于实现。如果愿意，也可以使用exit()，该函数刷新文件缓冲区，但不显示消息。

```C++
// 15.7 error1.cpp -- using the abort() function
#include <iostream>
#include <cstdlib>

double hmean(double a, double b);

int main()
{
	double x, y, z;

	std::cout << "Enter two numbers: ";
	while (std::cin >> x >> y)
	{
		z = hmean(x, y);
		std::cout << "Harmonic mean of " << x << " and " << y
			<< " is " << z << std::endl;
		std::cout << "Enter next set of numbers <q to quit>: ";
	}
	std::cout << "Bye\n";
	return 0;
}

double hmean(double a, double b)
{
	if (a == -b)
	{
		std::cout << "untenable arguments to hmean()\n";
		std::abort();
	}

	return 2.0 * a * b / (a + b);
}
```
- 注意：在hmean()中调用abort()函数将直接终止程序，而不是先返回main()。
- 靠程序员来执行这种检查是不安全。

### 15.3.2 返回错误码
- 一种比异常终止更灵活的方法是，使用函数的返回值来指出问题。
- 可使用指针参数或引用参数来将值返回给调用程序，并使用函数的返回值来指出成功还是失败。istream族重载>>运算符使用了这种技术的变体。通过告知调用程序是成功了还是失败了，是的程序可以采取除异常终止程序之外的其他措施。
```C++
// 15.8 error2.cpp -- returning an error code.
#include <iostream>
#include <cfloat>  // for DBL_MAX

bool hmean(double a, double b, double* ans);

int main()
{
	double x, y, z;

	std::cout << "Enter two numbers: ";
	while (std::cin >> x >> y)
	{
		if (hmean(x, y, &z))
		{
			std::cout << "Harmonic mean of " << x << " and " << y
				<< " is " << z << std::endl;
		}
		else
		{
			std::cout << "One value should not be the negative "
				<< " of the other - try again.\n";
		}
		
		std::cout << "Enter next set of numbers <q to quit>: ";
	}
	std::cout << "Bye\n";
	return 0;
}

bool hmean(double a, double b, double* ans)
{
	if (a == -b)
	{
		*ans = DBL_MAX;
		return false;
	}

	*ans = 2.0 * a * b / (a + b);
	return true;
}
```

### 15.3.3 异常机制
- 异常提供了将控制权从程序的一个部分传递到另一个部分的途径。对异常的处理有3个组成部分：
	- 引发异常
	- 使用处理程序捕获异常
	- 使用try块

- throw语句实际上是跳转，即命令程序跳到另一条语句。throw关键字表示引发异常，紧随其后的值（例如字符串或对象）指出了异常的特征。
- 程序使用异常处理程序(exception handler)来捕获异常，异常处理程序位于要处理问题的程序中，以catch关键字开头。
- catch关键字和异常类型用作标签，指出当异常被引发时，程序应跳到这个位置执行。因此，异常处理程序也被称为catch块。
- try块表示其中特定的异常可能被激活的代码块，它后面跟一个或多个catch块。
```C++
// 15.9 error3.cpp -- using an exception
#include <iostream>
#include <cfloat>
double hmean(double a, double b);

int main()
{
	double x, y, z;

	std::cout << "Enter two numbers: ";
	while (std::cin >> x >> y)
	{
		try
		{
			z = hmean(x, y);
		}
		catch (const char* s)
		{
			std::cout << s << std::endl;
			std::cout << "Enter a new pair of number: ";
			continue;
		}

		std::cout << "Harmonic mean of " << x << " and " << y
			<< " is " << z << std::endl;
		std::cout << "Enter next set of numbers <q to quit>: ";
	}
	std::cout << "Bye\n";
	return 0;
}

double hmean(double a, double b)
{
	if (a == -b)
	{
		throw "bad_hmean() arguments: a = -b not allowed";
	}

	return 2.0 * a * b / (a + b);
}
```

- 异常类型可以是字符串或其它C++类型；通常为类类型，本章后面说明这一点。
- 执行throw语句类似于执行返回语句，因为它也将终止函数的执行；但throw不是将控制权返回给调用程序，而是导致程序沿函数调用序列后退，直到找到包含try块的函数。
- 另外，在这个例子中，throw将程序控制权返回给main()。程序将在main()中寻找与引发的异常类型匹配的异常处理程序（位于try块的后面）。
```C++
catch (char* s) // start of exception handler
{
	std::cout << s << std::endl;
	std::cout << "Enter a new pair of number: ";
	continue;
}  // end of handler
```
- catch块点类似于函数定义，但并不是函数定义。关键字catch表明这是一个处理程序，而char*s表明该处理程序与字符串异常匹配。s与函数参数定义极其类似，因为匹配的引发将被赋给s。另外，当异常与该处理程序匹配时，程序将执行括号中的代码。（有点类型匹配的感觉）
- 执行完try块中的语句后，如果没有引发任何异常，则程序跳过try块后面的catch块，直接执行处理程序后面的第一条语句。
- 补充：如果函数引发了异常，而没有try块或没有匹配的处理程序时，将会发生什么情况。在默认情况下，程序最终将调用abort()函数，但可以修改这种行为。稍微讨论这个问题。

### 15.3.4 将对象用作异常类型
```C++
// 15.10 exc_mean.h -- exception classes for hmean(), gmean()
#include <iostream>

class bad_hmean
{
private:
	double v1;
	double v2;
public:
	bad_hmean(double a = 0, double b = 0) : v1(a), v2(b) {}
	void mesg();
};

inline void bad_hmean::mesg()
{
	std::cout << "hmean(" << v1 << ", " << v2 << "): "
		<< "invalid arguments: a = -b \n";
}

class bad_gmean
{
public:
	double v1;
	double v2;
	bad_gmean(double a = 0, double b = 0) : v1(a), v2(b) {}
	const char* mesg();
};

inline const char* bad_gmean::mesg()
{
	return "gmean() arguments should be >= 0 \n";
}
```
```C++
// 15.11 error4.cpp - using exception classes
#include <iostream>
#include <cmath>  // or math.h, unix users may need -1m flag
#include "exc_mean.h"

// function prototypes
double hmean(double a, double b);
double gmean(double a, double b);

int main()
{
	using std::cout;
	using std::cin;
	using std::endl;

	double x, y, z;

	cout << "Enter two number: ";
	while (cin >> x >> y)
	{
		try
		{
			z = hmean(x, y);
			std::cout << "Harmonic mean of " << x << " and " << y
				<< " is " << z << std::endl;
			cout << "Geometric mean of " << x << " and " << y
				<< " is " << gmean(x, y) << endl;
			std::cout << "Enter next set of numbers <q to quit>: ";
		}
		catch (bad_hmean& bg)
		{
			bg.mesg();
			cout << "Try again.\n";
			continue;
		}
		catch (bad_gmean& hg)
		{
			cout << hg.mesg();
			cout << "Values used: " << hg.v1 << ", "
				<< hg.v2 << endl;
			cout << "Sorry, you don't get to play any more.\n";
			break;
		}
	}
	cout << "Bye!\n";
	return 0;
}

double hmean(double a, double b)
{
	if (a == -b)
	{
		throw bad_hmean(a, b);
	}

	return 2.0 * a * b / (a + b);
}

double gmean(double a, double b)
{
	if (a < 0 || b < 0)
	{
		throw bad_gmean(a, b);
	}

	return std::sqrt(a * b);
}
```
- 异常类bad_gmean和bad_hmean使用的技术不同，具体地说，bad_gmean使用的是公有数据和公有方法，该方法返回一个C-风格的字符串

### 15.3.5 异常规范和C++11
- 有时候，一种理念看似有前途，但实际的使用效果并不好。
- 异常规范(exception specification)在C++11废除
- 调用层数太多，编译器很多错误就检查不了了
- C++11可以用 ```noexcept``` 指出函数不会引发异常。但大多数情况下不建议用。还有 ``` noexcept()``` 运算符判断操作数是否会引发异常。

### 15.3.6 栈解退
- 具体地说，程序将调用函数的指令的地址（返回地址）放到栈中。当被调用的函数执行完毕后，程序将使用改地址来确定从哪里开始继续执行。
- 现在假设函数出现异常（而不是由于返回）而终止，则程序也将释放栈中的内存，但不会再释放栈的第一个返回地址后停止，**而是继续释放栈，直到找到一个位于try块中的返回地址。** 随后，控制权将转到块尾的异常处理程序，而不是函数调用后面的第一条语句。这个过程被称为**栈解退。**（可以理解为直接跳转过去了）
- 引发机制的一个非常重要的特性是：和函数返回一样，对于栈中的自动类对象，类的析构函数将被调用。
- 然而，函数返回仅仅处理该函数放在栈中的对象。而throw语句则处理try和throw之间整个调用序列放在栈中的对象（这些对象的析构函数都会被调用）。

```C++
// 15.12 error5.cpp -- unwinding the stack
#include <iostream>
#include <cmath>
#include <string>
#include "exc_mean.h"

using namespace std;

class demo
{
private:
	string word;
public:
	demo(const string& str)
	{
		word = str;
		cout << "demo" << word << " created\n";
	}
	~demo()
	{
		cout << "demo " << word << " destroyed\n";
	}
	void show() const
	{
		cout << "demo" << word << " lives!\n";
	}
};

// function prototypes
double hmean(double a, double b);
double gmean(double a, double b);
double means(double a, double b);

int main()
{
	double x, y, z;

	{
		demo d1("found in block in main()");
		cout << "Enter two numbers: ";
		
		while (cin >> x >> y)
		{
			try
			{
				z = means(x, y);
				cout << "The mean mean of " << x << " and " << y
					<< " is " << z << endl;
				cout << "Enter next pair: ";
			}
			catch (bad_hmean& bh)
			{
				bh.mesg();
				std::cout << "Try again.\n";
				continue;
			}
			catch (bad_gmean& bg)
			{
				cout << bg.mesg();
				cout << "Values used: " << bg.v1 << ", "
					<< bg.v2 << endl;
				cout << "Sorry, you don't get to play any more.\n";
				break;
			}  // end of catch block
		}  // end of while
		d1.show();
	}  // end of normal block 

	cout << "Bye!\n";
	cin.get();
	cin.get();
	return 0;
}

double hmean(double a, double b)
{
	if (a == -b)
	{
		throw bad_hmean(a, b);
	}
	return 2.0 * a * b / (a + b);
}

double gmean(double a, double b)
{
	if (a < 0 || b < 0)
	{
		throw bad_gmean(a, b);
	}

	return std::sqrt(a * b);
}

double means(double a, double b)
{
	double am, hm, gm;  // 算数平均数、调和平均数和几何平均数
	demo d2("found in means()");
	am = (a + b) / 2.0;  // arithmetic mean

	try
	{
		hm = hmean(a, b);
		gm = gmean(a, b);
	}
	catch (bad_hmean& bh)
	{
		bh.mesg();
		std::cout << "Caught in means()\n";
		throw;  // rethrows the exception
	}

	d2.show();
	return (am + hm + gm) / 3.0;
}
```
- 栈解退释放栈中的局部变量

### 15.3.7 其他异常特性
- 虽然throw-catch机制类似于函数参数和函数返回机制，但还是有些不同之处的。
- 一个不同：控制权返回给能捕获异常的try-catch
- 另一个不同：引发异常时编辑器总是创建一个临时拷贝，即使异常规范和catch块中指定的是引用。
- 将引发异常和创建对象组合在一起将更简单：
```C++
throw problem();
```
- 使用引用捕获异常的原因是：基类引用可以执行派生类对象。
- 提示：如果有一个异常类继承层次结构，应这样排列catch块：将捕获位于层次结构最下面的异常类的catch语句放在最前面，将捕获基类异常的catch语句放在后面
- 不知道异常类型时可以用省略号捕获任何异常
```C++
// catches any type exception
catch (...)
{ 
	// statementes 
}
```
- 如果知道一些可能会引发的异常，可以将上述捕获所有异常的catch块放在最后面，这有点类似于switch语句中的default：
```C++
try {
	duper();
}
catch(bad_3& be)
{
	// statements
}
catch(bad_2& be)
{
	// statements
}
catch(bad_1& be)
{
	// statements
}
catch(bad_hmean& h)
{
	// statements
}
catch(...)
{
	// statements
}
```

### 15.3.8 exception类
- C++库定义了很多基于exception的异常类型
- what()返回字符串

#### 1.stdexcept的异常类
- logic_error
- runtime_error

#### 2.bad_alloc异常和new
- new可以引发bad_alloc异常，以前只返回空指针。
```C++
// 15.13 newexcep.cpp -- the bad_alloc exception
#include <iostream>
#include <new>
#include <cstdlib>  // for exit(), EXIT_FAILURE
using namespace std;

struct Big
{
	double stuff[20000];
};

int main()
{
	Big* pb;

	try
	{
		cout << "Trying to get a big block of memory:\n";
		pb = new Big[10000];  // 1,600,000,000 bytes
		cout << "Got past the new request:\n";
	}
	catch (bad_alloc& ba)
	{
		cout << "Caught the exception!\n";
		cout << ba.what() << endl;
		exit(EXIT_FAILURE);
	}

	cout << "Memory successfully allocated\n";
	pb[0].stuff[0] = 4;
	cout << pb[0].stuff[0] << endl;
	delete[] pb;
	return 0;
}
```

#### 3.空指针和new
- 有些编译器提供标记（开关）让用户选择所需的行为
```C++
int* pi = new (std::nothrow) int;
int* pa = new (std::nothrow) int[500];
```

### 15.3.9 异常、类和继承
- 异常、类和继承以三种方式相互关联。首先，可以像标准C++库所做的那样，从一个异常类派生出另一个；其次，可以在类定义中嵌套异常类声明来组合异常；第三，这种嵌套声明本身可被继承，还可用作基类。


```C++
// 15.14 sales.h  -- exceptions and inheritance
#include <stdexcept>
#include <string>

class Sales
{
public:
	class bad_index : public std::logic_error
	{
	private:
		int bi;  // bad index value
	public:
		explicit bad_index(int ix, const std::string& s = "Index error in Sales object\n");
		int bi_val() const { return bi; }
		virtual ~bad_index() throw() {}
	};

protected:
	enum { MONTHS = 12 };  // could be a static const

public:
	explicit Sales(int yy = 0);
	Sales(int yy, const double* gr, int n);
	virtual ~Sales() { }
	int Year() const { return year; }
	virtual double operator[](int i) const;
	virtual double& operator[](int i);

private:
	double gross[MONTHS];
	int year;
};

class LabeledSales : public Sales
{
public:
	class nbad_index : public Sales::bad_index
	{
	private:
		std::string lbl;
	public:
		nbad_index(const std::string& lb, int ix,
			const std::string& s = "Index error in LabeledSales object\n");
		const std::string& label_val() const { return lbl; }
		virtual ~nbad_index() throw() {}
	};

public:
	explicit LabeledSales(const std::string& lb = "none", int yy = 0);
	LabeledSales(const std::string& lb, int yy, const double* gr, int n);
	virtual ~LabeledSales() { }
	const std::string& Label() const { return label; }
	virtual double operator[](int i) const;
	virtual double& operator[](int i);

private:
	std::string label;
};
```

```C++
// 15.15 sales.cpp -- Sales implementation
#include "sales.h"
using std::string;

Sales::bad_index::bad_index(int ix, const string& s)
	: std::logic_error(s), bi(ix)
{
}

Sales::Sales(int yy)
{
	year = yy;

	for (int i = 0; i < MONTHS; ++i)
	{
		gross[i] = 0;
	}
}

Sales::Sales(int yy, const double* gr, int n)
{
	year = yy;
	int lim = (n < MONTHS) ? n : MONTHS;
	int i;

	for (i = 0; i < lim; ++i)
	{
		gross[i] = gr[i];
	}

	// for i > n and i < MONTHS
	for (; i < MONTHS; i++)
	{
		gross[i] = 0;
	}
}

double Sales::operator[](int i) const
{
	if (i < 0 || i >= MONTHS)
	{
		throw bad_index(i);
	}

	return gross[i];
}

double& Sales::operator[](int i)
{
	if (i < 0 || i >= MONTHS)
	{
		throw bad_index(i);
	}

	return gross[i];
}

LabeledSales::nbad_index::nbad_index(const std::string& lb, int ix,
	const std::string& s) : Sales::bad_index(ix, s)
{
	lbl = lb;
}

LabeledSales::LabeledSales(const std::string& lb, int yy)
	:Sales(yy)
{
	label = lb;
}

LabeledSales::LabeledSales(const std::string& lb, int yy,
	const double* gr, int n) : Sales(yy, gr, n)
{
	label = lb;
}

double LabeledSales::operator[](int i) const
{
	if (i < 0 || i >= MONTHS)
	{
		throw nbad_index(Label(), i);
	}

	return Sales::operator[] (i);
}

double& LabeledSales::operator[](int i)
{
	if (i < 0 || i >= MONTHS)
	{
		throw nbad_index(Label(), i);
	}

	return Sales::operator[] (i);
}
```

```C++
// 15.16 use_sales.cpp
#include <iostream>
#include "sales.h"

int main()
{
	using namespace  std;

	double vals1[12] =
	{
		1220, 1100, 1122, 2212, 1232, 2334,
		2884, 2393, 3302, 2922, 3002, 3544
	};

	double vals2[12] =
	{
		12, 11, 22, 21, 32, 34,
		28, 29, 33, 29, 32, 35
	};

	Sales sales1(2011, vals1, 12);
	LabeledSales sales2("Blogstar", 2012, vals2, 12);
	
	cout << "First try block:\n";
	try
	{
		int i;
		cout << "Year = " << sales1.Year() << endl;

		for (i = 0; i < 12; ++i)
		{
			cout << sales1[i] << ' ';

			if (i % 6 == 5)
			{
				cout << endl;
			}
		}

		cout << "Year = " << sales2.Year() << endl;
		cout << "Label = " << sales2.Label() << endl;

		for (i = 0; i <= 12; ++i)
		{
			cout << sales2[i] << " ";

			if (i % 6 == 5)
			{
				cout << endl;
			}
		}

		cout << "End of try block 1.\n";
	}
	catch (LabeledSales::nbad_index& bad)
	{
		cout << bad.what();
		cout << "Company: " << bad.label_val() << endl;
		cout << "bad_index: " << bad.bi_val() << endl;

	}
	catch (Sales::bad_index& bad)
	{
		cout << bad.what();
		cout << "bad index: " << bad.bi_val() << endl;
	}

	cout << "\nNext try block:\n";
	try
	{
		sales1[2] = 37.5;
		sales1[20] = 23345;
		cout << "End of try block 2.\n";
	}
	catch (LabeledSales::nbad_index& bad)
	{
		cout << bad.what();
		cout << "Company: " << bad.label_val() << endl;
		cout << "bad_index: " << bad.bi_val() << endl;

	}
	catch (Sales::bad_index& bad)
	{
		cout << bad.what();
		cout << "bad index: " << bad.bi_val() << endl;
	}

	cout << "done\n";
	return 0;
}
```

### 15.3.10 异常为何会迷失方向
- 意外异常（unexpected exception）：引发的异常与规范列表不匹配
- 未捕获异常（uncaught exception）：没找到匹配的catch块

- [ ] 一大坨看不懂，到时候看视频得了

### 15.3.11 有关异常的注意事项
- 应在设计程序时就加入异常处理功能，而不是以后再添加
- 缺点：
	- 1. 异常会增加代码，降低程序的运行效率
	- 2. 异常规范不适用于模板，因为模板引发的异常可能随特定的具体化而异。

#### 异常处理
- 理解库中异常处理像学习语言本身一样困难。
- 通过库文档和源代码了解到的异常和错误处理细节将使程序员和他的软件受益


## 15.4 RTTI
- RTTI是运行阶段类型识别(Runtime Type Identification)的简称。这是新添加到C++中的特性之一。
- RTTI旨在为程序在运行阶段确定对象的类型提供一种标准方式。

### 15.4.1 RTTI的用途
- 想要调用正确的函数版本，但派生对象可能包含不是继承而来的方法，在这种情况下，只有某些类型的对象可以使用该方法。也可能处于调试目的，想追踪生成的对象的类型。对于后两种情况，RTTI提供解决方案。

- 个人：感觉像是为了向上转换
- GTP：主要用于在运行时确定对象的类型。它可以用于向上类型转换，但它的主要目的是允许在运行时获取对象的实际类型信息，以便进行安全的类型检查和转换。

### 15.4.2 RTTI的工作原理
C++的3个支持RTTI的元素：
- `dynamic_cast`：使用一个指向基类的指针，来生成一个指向派生类的指针。否则返回0-空指针。
- `typeid`: 返回一个对象的类型信息
- `type_info`: 存储有关特定类型的信息。

`RTTI只适用于包含虚函数的类层次结构`，原因在于只有对于这种类层次结构，才应该将派生对象的地址赋给基类指针。

#### 1.`dynamic_cast` 运算符
- 该运算符是最常用的RTTI组件，因为它能回答“是否可以安全地将对象的地址赋给特定类型的指针”

- 注意，与问题“指针指向的是哪种类型的对象”相比，问题“类型转换是否安全”更通用，也更有用。

```C++
// 15.17 rtti.cpp -- using the RTTI dynamic_cast operator
#include <iostream>
#include <cstdlib>
#include <ctime>

using std::cout;

class Grand
{
private:
	int hold;
public:
	Grand(int h = 0) : hold(h) { }
	virtual void Speak() const { cout << "I am a grand class!\n"; }
	virtual int Value() const { return hold; }
};

class Superb : public Grand
{
public:
	Superb(int h = 0 ) 
		: Grand(h) 
	{ }
	
	void Speak() const 
	{ 
		cout << "I am a superb class!!\n"; 
	}

	virtual void Say() const
	{
		cout << "I hold the superb value of " << Value() << "!\n";
	}
};

class Magnificent : public Superb
{
private:
	char ch;
public:
	Magnificent(int h = 0, char c = 'A')
		: Superb(h)
		, ch(c)
	{}

	void Speak() const { cout << "I am a magnificent class!!!\n"; }

	void Say() const 
	{
		cout << "I hould the character " << ch << "and the integer " << Value() << "!\n";
	}
};

Grand* GetOne();

int main()
{
	std::srand(std::time(0));

	Grand* pg;
	Superb* ps;

	for (int i = 0; i < 5; i++)
	{
		if (pg = GetOne())
		{
			pg->Speak();
		}

		if (ps = dynamic_cast<Superb*>(pg))
		{
			ps->Say();
		}
	}

	return 0;
}

Grand* GetOne()  // generate one of three kinds of objects randomly
{
	Grand* p = nullptr;

	switch (std::rand() % 3)
	{
	case 0:
		p = new Grand(std::rand() % 100);
		break;
	case 1:
		p = new Superb(std::rand() % 100);
		break;
	case 2:
		p = new Magnificent(std::rand() % 100
							, 'A' + std::rand() % 26);
		break;
	default:
		break;
	}
	return p;
}
```

- [ ] 记得回头看前面的说明

#### 2.typeid运算符和type_info类

```C++
// 15.18 rtti2.cpp -- using dynamic_cast, typeid, and type_info 
#include <iostream>
#include <cstdlib>
#include <ctime>
#include <typeinfo>

using namespace std;

class Grand
{
private:
	int hold;
public:
	Grand(int h = 0) : hold(h) { }
	virtual void Speak() const { cout << "I am a grand class!\n"; }
	virtual int Value() const { return hold; }
};

class Superb : public Grand
{
public:
	Superb(int h = 0)
		: Grand(h)
	{ }

	void Speak() const
	{
		cout << "I am a superb class!!\n";
	}

	virtual void Say() const
	{
		cout << "I hold the superb value of " << Value() << "!\n";
	}
};

class Magnificent : public Superb
{
private:
	char ch;
public:
	Magnificent(int h = 0, char c = 'A')
		: Superb(h)
		, ch(c)
	{}

	void Speak() const { cout << "I am a magnificent class!!!\n"; }

	void Say() const
	{
		cout << "I hould the character " << ch << "and the integer " << Value() << "!\n";
	}
};

Grand* GetOne();

int main()
{
	std::srand(std::time(0));

	Grand* pg;
	Superb* ps;

	for (int i = 0; i < 5; i++)
	{
		if (pg = GetOne())
		{
			cout << "Now processing type " << typeid(*pg).name() << ".\n";
			pg->Speak();
		}

		if (ps = dynamic_cast<Superb*>(pg))
		{
			ps->Say();
		}

		if (typeid(Magnificent) == typeid(*pg))
		{
			cout << "Yes, you're really magnificent.\n";
		}
	}

	return 0;
}

Grand* GetOne()  // generate one of three kinds of objects randomly
{
	Grand* p = nullptr;

	switch (std::rand() % 3)
	{
	case 0:
		p = new Grand(std::rand() % 100);
		break;
	case 1:
		p = new Superb(std::rand() % 100);
		break;
	case 2:
		p = new Magnificent(std::rand() % 100
			, 'A' + std::rand() % 26);
		break;
	default:
		break;
	}
	return p;
}
```

#### 3.误用RTTI的例子


## 15.5 类型转换运算符
```C++
// 15.19 constcast.cpp -- using const_cast<>
#include <iostream>

using std::cout;
using std::endl;

void change(const int* pt, int n);

int main()
{
	int pop1 = 38383;
	const int pop2 = 2000;

	cout << "pop1, pop2: " << pop1 << ", " << pop2 << endl;
	change(&pop1, -103);
	change(&pop2, -103);
	cout << "pop1, pop2: " << pop1 << ", " << pop2 << endl;
	return 0;
}

void change(const int* pt, int n)
{
	int* pc;

	pc = const_cast<int*>(pt);
	*pc += n;
}
```

## 15.6 总结

## 15.7 复习题
```C++

```

## 15.8 编程练习
```C++

```