# 第10章 对象和类

## 10.1 过程性编程和面向对象编程
- 总之，采用OOP方法时，首先从用户的角度考虑对象——描述对象所需的数据以及描述用户与数据交互所需的操作。完成对接口的描述后，需要确定如何实现接口和数据存储。最后，使用新的设计方案创建出程序。

## 10.2 抽象和类
- 生活中充满复杂性，处理复杂性的方法之一是简化和抽象。
- 抽象是通往用户定义类型的捷径，在C++中，用户定义类型指的是实现抽象接口的类设计。

### 10.2.1 类型是什么
- 总之，指定基本类型完成了三项工作：
    - 决定数据对象需要的内存数量；
    - 决定如何解释内存中的位(long 和 float在内存中占得位数相同，但将它们转换为数值的方法不同);
    - 决定可使用数据对象执行的操作和方法。
- 对于内置类型来说，有关操作的信息被内置到编译器中。但在C++中定义用户自定义的类型时，必须自己提供这些信息。付出这些劳动换来了根据实际需要定制新数据类型的强大功能和灵活性。

### 10.2.2 C++中的类
- 类是一种将抽象转换为用户定义类型的C++工具，它将数据表示和操作数据的方法合成一个整洁的包。
- 通常，C++程序员将接口（类定义）放在头文件中，并将实现放在源代码文件中。

```C++
// 10.1 stock00.h
// version 00
#ifdef STOCK00_H_
#define STOCK00_H_

#include <string>

class Stock  // class declaration
{
private:
	std::string company;
	long shares;
	double share_val;
	double total_val;
	void set_tot() { total_val = shares * share_val; }  // 成员函数可以就地定义，也可以用原型表示
public:
	void Acquire(const std::string& co, long n, double pr);
	void buy(long num, double price);
	void sell(long num, double price);
	void update(double price);
	void show();
};  // note semicolon at the end

#endif // STOCK00_H_
```

#### 1.访问控制
- 使用类对象的程序都可以直接访问公有部分，但只能通过共有成员函数（或友元函数）来访问对象的私有成员。因此，公有成员函数是程序和对象的私有成员之间的桥梁，提供了对象和程序之间的接口。防止程序直接访问数据被称为数据隐藏。
- 关键字public标识组成类的公共接口的类成员（抽象）
- 类设计尽可能将公共接口与实现细节分开。公有接口表示设计的抽象组件。将实现细节放在一起并将它们与抽象分开被称为封装。数据隐藏（将数据放在类的私有部分中）是另一种封装。封装的另一个例子是，将类函数定义和类声明放在不同的文件中。

#### 2.控制对成员的访问：公有还是私有
- 由于隐藏数据是OOP主要的目标之一，因此数据项通常放在私有部分，组成类接口的成员函数放在公有部分。
- 通常，程序员使用私有成员函数来处理不属于公有接口的实现细节。
- 可以不在类声明中使用关键字peivate，因此这是类对象的默认访问控制。

##### 类和结构
- 类和结构唯一的区别：结构默认访问类型是public，而类为private。

### 10.2.3 实现类成员函数
- 成员函数有两个特殊的特征：
	- 定义成员函数时，使用作用域解析运算符(::)来表示函数所属的类；
	- 类方法可以访问类的private组件
- Stock类的其他成员函数不必使用作用域解析运算符，就可以使用update()方法，这是因为他们属于同一个类，因此update()是可见的。
- Stock::update()是函数的**限定**名(qualified name)；而简单的update()是全名的缩写（非限定名，unqualified name），它只能在类作用域中使用。
- 还需要创建类描述的第二部分：为那些由类声明中的原型表示的成员函数提供代码。成员函数定义与常规函数定义非常类似。但它们由两个特殊特征：
	- 定义成员函数时，使用作用域解析运算符（::）来标识函数所属的类。
	- 类方法可以访问类的private组件 
```C++
// stock00.cpp -- implementing the Stock class
// version 00
#include <iostream>
#include "stock00.h"

void Stock::acqire(const std::string& co, long n, double pr);
{
	company = co;
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
```
	
	
	
	
	
	
	
	
	
	
	
	
	
