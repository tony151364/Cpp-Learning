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
- 类并非只能拥有友元函数，也可以将类作为友元。在这种情况下，友元类的所有方法都可以访问原始类的私有成员和保护成员。另外，也可以做更严格的限制，只将特定的成员函数指定为另一个类的友元。那些函数、成员函数或类为友元是由类定义的，而不能从外部强加友情。因此尽管友元被授予从外部访问类的私有部分的权限，但它们并不与面向对象编程思想相悖；相反，它们提高了公有接口的灵活性

### 15.1.1 友元类
- 前向声明（forward delaration）
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

### 15.1.2 友元成员函数

- **能否像下面这样排列呢？**
```C++
class Remote;    // forward declaration
class Tv { .. };
class Remote { ... };
```
- 答案是不能。原因在于，编译器在Tv类的声明中看到Remote的一个方法被声明为Tv类的友元之前，应先看到Remote类的声明和set_chan()方法的声明。
- 前向声明的作用：检查该原型时，所有的编译器都需要知道Tv是一个类
- 只让set_chan（）方法能够访问Tv对象的私有成员
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
- 顺便说一句，让整个Remote类称为友元并不需要前向声明，因为友元语句本身已经指出Remote是一个类：```C++ friend class Remote; ```

### 15.1.3 其他友元关系
- 电脑对手柄进行控制，比如上古卷轴5开门的时候手柄会震动。两个类互为友元
- **需要记住的一点是**，对于使用Remote对象的Tv方法，其原型可在Remote类声明之前声明
- 
```C++
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
- [ ] 没搞懂

## 15.2 嵌套类
- 包含类的成员函数可以创建和使用被嵌套类的对象
- 仅当声明位于公有部分，才能在包含类的外面使用嵌套类，而且必须使用作用域解析运算符。
- 包含：意味着将类对象作为另一个类的成员，而对类进行嵌套不创建类成员，而是定义了一种类型，该类型仅在包含嵌套类声明的类中有效。
- 嵌套：对类进行嵌套通常是为了帮助实现另一个类，并避免名称冲突。
- 嵌套结构体也是一种变相的嵌套类。因为结构是一种其成员在默认情况下为公有的类
- [ ] 假设想在方法文件中定义构造函数，则定义必须指出Node类是在Queue类中定义的。这是通过两次类解析运算符来完成的 ```C++  Queue::Node::Node(const Item& i) : item(i), next(0) { } ```

### 15.2.1 嵌套类的访问权限
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
		struct Node* next;

		Node(const Item& i) :item(i), next(0) {}
	};
	
	Node* front;
	Node* rear;
	int items;
	const int qsize;
	QueueTP(const QueueTP* q) : qsize(0) { }
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

#endif // _QUEUETP_H_
```
```C++

```
```C++

```
