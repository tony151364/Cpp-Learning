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
// 10.1 stock00.h -- stock class interface
// version 00
#ifndef STOCK00_H_
#define STOCK00_H_

#include <string>

class Stock  // class declaration
{
private:
	std::string company; 
	long shares;  // 所持有的股票数量
	double share_val;  // 每股的价格
	double total_val;  // 股票总价格
	void set_tot() { total_val = shares * share_val; } 
public:
	void acquire(const std::string& co, long n, double pr);
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
// 10.2 stock00.cpp -- implementing the Stock class
// version 00
#include <iostream>
#include "stock00.h"

// 管理对某个公司股票的首次购买
void Stock::acquire(const std::string& co, long n, double pr)
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

// 增加持有的股票，同时确保买入的股票不为负数
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

// 减少持有的股票，同时确保卖出的股票不为负数
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

void Stock::show()
{
	using std::cout;
	using std::ios_base;

	// set format to #.###
	ios_base::fmtflags orig =
		cout.setf(ios_base::fixed, ios_base::floatfield);
	std::streamsize prec = cout.precision(3);

	std::cout << "Company: " << company
		<< "Share: " << shares << '\n'
		<< " Share Price: $" << share_val;

	// set format to #.##
	cout.precision(2);
	std::cout << " Total Worth: $" << total_val << '\n';

	// restore original format
	cout.setf(orig, ios_base::floatfield);
	cout.precision(prec);
}
```
#### 2. 内联方法
- 其定义位于类声明中的函数都将自动成为内联函数，因此Stock::set_tot()是一个内联函数。为此，只需要在类实现部分中定义函数时使用inline限定符即可
- 内联函数的特殊规则要求在每个使用它们的文件中都对其进行定义。确保内联定义对多个文件程序中的所有文件都可用、最简便的方法是：**将内联定义放在定义类的头文件中**（有些开发系统包含智能链接程序，允许将内联定义放在一个独立的实现文件中）。
- [ ] 可以复习下前面的内联函数相关内容
- 程序清单10.1中的set_tot()的内联定义与上述代码（定义紧跟在类声明后面）是等价的。

#### 3.方法使用哪个对象
- 在OOP中，调用成员函数被称为发送消息
- 因此将同样的消息发送给两个不同的对象将调用同一个方法，但该方法被用于两个不同的对象
	
### 10.2.4 使用类
- 要创建类对象，可以声明类变量，也可以使用new为类对象分配存储空间。可以将对象作为函数的参数和返回值，也可以一个对象赋给另一个。C++提供了一些工具，可用于初始化对象、让cin和cout识别对象，甚至在相似的类对象之间进行自动类型转换。
	
```C++
// 10.3 usestock0.cpp -- the client program
// compile with stock00.cpp
#include <iostream>
#include "stock00.h"

int main()
{
	Stock fluffy_the_cat;
	
	fluffy_the_cat.acquire("ManoSmart", 20, 12.50);
	fluffy_the_cat.show();
	
	fluffy_the_cat.buy(15, 18.125);
	fluffy_the_cat.show();

	fluffy_the_cat.sell(400, 20.00);
	fluffy_the_cat.show();

	fluffy_the_cat.buy(300000, 40.125);
	fluffy_the_cat.show();

	fluffy_the_cat.sell(300000, 0.125);
	fluffy_the_cat.show();

	return 0;
}
```

#### 客户/服务器模型
- OOP程序员常依照客户/服务器模型来讨论设计程序。在这个概念中，客户是实用类的程序。类声明（包括类方法）构成了服务器。它……。这样程序员独立地对客户和服务器进行改进，对服务器的修改不会对客户的行为造成意外的影响。

### 10.2.5 修改实现
- 已修改

### 10.2.6 小结
- 公有部分的内容构成了设计的抽象部分——公有接口。将数据封装到私有部分中可以保护数据的完整性，这被称为数据隐藏。因此，C++通过类使得实现抽象、数据隐藏和封装等OOP特性很容易。
```C++
class className
{
private:
	data member declarations
public:
	member function prototypes;
}
```
- 公有部分的内容构成了设计的抽象部分——公有接口。
- 设计三步骤：
	- 第一步：提供类声明
	- 第二步：实现类成员函数。需要用作用域解析运算符来指出成员函数属于哪个类。
	- 第三步：在main()函数使用即可 。

## 10.3 类的构造函数和析构函数
- C++的目标之一是让使用类对象就像使用标准类型一样，能初始化。
- 构造函数的原型和函数头有一个尤其的特征——虽然没有返回值，但没有被声明为void类型。实际上，构造函数没有声明类型。

### 10.3.1 声明和定义构造函数
- 现在需要

#### 成员名和参数名
- 构造函数的参数表示的不是类成员，而是赋给类成员的值。因此，参数名不能与类成员相同。
- 常见做法：
	- 1.在数据成员中使用m_前缀
	- 2.在成员名中使用后缀_

### 10.3.2 使用构造函数
- 两种使用构造函数的方式：
	- 1.显式调用构造函数``` Stock food = Stock("World Cabbage", 250, 1.25);  ``` 
	- 2.隐式地调用构造函数``` Stock food("World Cabbage", 250, 1.25);  ``` 
- 一般来说，使用对象来调用方法，但无法使用对象来调用构造函数，因为**在构造函数造出对象之前，对象是不存在的。** 因此构造函数被用来创建对象，而不能通过对象来调用。

### 10.3.3 默认构造函数
- 默认构造函数是在未提供显式初始值时，用来创建对象的构造函数。也就是说，它是用于下面这种声明的构造函数：``` Stock fluffy_the_cat;  // uses the default constructor ```
- 程序清单10.3就是这样做的！这条语句管用的原因在于，如果没有提供任何构造函数，则C++将自动提供默认的构造函数。它是默认构造函数的隐式版本，不做任何工作。对于Stock类来说，默认构造函数可能如下： ``` Stock::Stock() {} ```
- 如果提供了非构造函数(如Stock(const char* co, int n, double pr))，但没有提供默认构造函数，则创建的时候会出错。
- 这样做的原因可能是想禁止创建微初始化的对象。然而，如果要创建对象，而不现实的初始化，则必须定义一个不接受任何参数的默认构造函数。
- 定义默认构造函数的方式有两种：
	- 一种是给已有构造函数的所有参数提供默认值: ``` Stock(const string& co = "Error", int n = 0, double pr = 0.0); ``` 
	- 通过函数重载来定义另一个构造函数——一个没有参数的构造函数；``` Stock(); ```

- 由于只能有一个默认构造函数，因此不要同时采用这两种方式。实际上，通常应初始化所有的对象，以确保所有成员一开始就有已知的合理值。

```C++
// 下面是为Stock类定义的一个默认构造函数：
Stock::Stock()  // default constructor
{
	company = "no name";
	shares = 0;
	share_val = 0.0;
	total_val = 0.0;
}
```
- **提示：** 在设计类时，通常应提供对所用类成员做隐式初始化的默认构造函数。
```C++
// 使用上述任何一种方式（没有参数或所有参数都有默认值）创建了默认构造函数后，
// 便可以声明对象变量，而不对它们进行显式初始化：
Stock first;  // calls default constructor implicitly
Stock first = Stock();  // calls it explicitly
Stock *prelief = new Stock;  // calls it implicitly
// 然而，不要被非默认构造函数的隐式形式所误导：
Stock first("Concrete Conglomerate");  // calls constructor
Stock secend();  // declares a function
Stock third;  // calls default constructor
```
- 第一个声明调用非默认构造函数，即接受参数的构造函数；
- 第二个声明指出，second()是一个返回Stock对象的函数。隐式地调用默认构造函数时，不要使用圆括号。

### 10.3.4 析构函数
- 对象过期时，程序将自动调用一个特殊的成员函数——析构函数，并在发现导致对象被删除的代码后，提供默认析构函数的定义。
- 如果构造函数使用new来分配内存，则析构函数将使用delete来释放这些内存。
- 它和构造函数一样，可以显式声明，没声明时编译器也会默认生成一个，但是啥都不接收。
- 什么时候应调用析构函数？这由编译器决定，通常不应在代码中显式地调用析构函数
	- 如果创建的是**静态存储类对象**，则其析构函数将在程序结束时自动被调用。
	- 如果创建的是**自动存储类对象**，则其析构函数将在程序执行完代码块时（该对象是在其中定义的）自动被调用。
	- 如果创建的是**new创建的对象**，则它将驻留在栈内存或自由存储区中，当使用delete来释放内存时，则析构函数将自动被调用。
	- 最后，程序可以创建临时对象来完成特定操作，在这种情况下，程序将在结束对该对象的使用时自动调用其析构函数

### 10.3.5 改进Stock类

#### 1.头文件
- 将构造函数和析构函数的原型加入到原来的类声明中。删除了acquire函数——不需要了。
```C++
// 10.4 stock00.h -- stock class interface
// version 00
#ifndef STOCK10_H_  // 防止多次包含，有点像Python的 if __name__ == "__main__"
#define STOCK10_H_
#include <string>

class Stock  // class declaration
{
private:
	std::string company; 
	long shares;  // 所持有的股票数量
	double share_val;  // 每股的价格
	double total_val;  // 股票总价格
	void set_tot() { total_val = shares * share_val; }
public:
	// two constructors
	Stock();  // default constructor
	Stock(const std::string& co, long n = 0, double pr = 0.0);
	~Stock();
	void buy(long num, double price);
	void sell(long num, double price);
	void update(double price);
	void show();
};  // note semicolon at the end

#endif // STOCK00_H_
```

#### 2.实现文件
```C++
// 10.5 stock00.cpp -- implementing the Stock class
// version 00
#include <iostream>
#include "stock00.h"

// constructors (verbose versions)
Stock::Stock()  // default constructor
{
	std::cout << "Default constructor called\n";
	company = "no name";
	shares = 0;
	share_val = 0.0;
	total_val = 0.0;
}

Stock::Stock(const std::string& co, long n, double pr)
{
	std::cout << "Constructor using " << co << "called\n";
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

// class destructor
Stock::~Stock() 
{
	std::cout << "Bey, " << company << "\n";
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

void Stock::show()
{
	using std::cout;
	using std::ios_base;

	// set format to #.###
	ios_base::fmtflags orig =
		cout.setf(ios_base::fixed, ios_base::floatfield);
	std::streamsize prec = cout.precision(3);

	std::cout << "Company: " << company
		<< "Share: " << shares << '\n'
		<< " Share Price: $" << share_val;

	// set format to #.##
	cout.precision(2);
	std::cout << " Total Worth: $" << total_val << '\n';

	// restore original format
	cout.setf(orig, ios_base::floatfield);
	cout.precision(prec);
}
```

#### 3.客户文件
```C++
// 10.6 usestock2.cpp -- using the Stock class compile with stock10.cpp
#include <iostream>
#include "stock00.h"

int main()
{
	{
		using std::cout;
		cout << "Using constructors to create new objects\n";
		
		Stock stock1("NanoSmart", 12, 20.0);  // syntax 1
		stock1.show();
		Stock stock2 = Stock("Boffo Objects", 2, 2.0);  // syntax 2
		stock2.show();

		cout << "Assigning stock1 to stock2:\n";
		stock2 = stock1;
		cout << "Listing stock1 and stock2:\n";
		stock1.show();
		stock2.show();

		cout << "Using a constructor to reset an object\n";
		stock1 = Stock("Nifty Foods", 10, 50.0);  // temp object
		cout << "Revised stock1:\n";
		stock1.show();
		cout << "Done\n";
	}

	return 0;
}
```
```
// 输出内容
Using constructors to create new objects
Constructor using NanoSmart called
Company: NanoSmartShare: 12
 Share Price: $20.000 Total Worth: $240.00
Constructor using Boffo Objects called
Company: Boffo ObjectsShare: 2
 Share Price: $2.000 Total Worth: $4.00
Assigning stock1 to stock2:
Listing stock1 and stock2:
Company: NanoSmartShare: 12
 Share Price: $20.000 Total Worth: $240.00
Company: NanoSmartShare: 12
 Share Price: $20.000 Total Worth: $240.00
Using a constructor to reset an object
Constructor using Nifty Foods called
Bey, Nifty Foods
Revised stock1:
Company: Nifty FoodsShare: 10
 Share Price: $50.000 Total Worth: $500.00
Done
Bey, NanoSmart  // stock2（被stock1传入到stock2中）
Bey, Nifty Foods  //stock1被重置为“Nifty Food”
```
- **提示**：为什么main()中多＋一个大括号？

#### 4.程序说明
- C++标准允许编译器使用两种方式来执行第二种语法。一种是使其行为和第一种语法完全相同：略
- 另一种方式是允许调用构造函数来创建一个临时对象，然后将临时对象复制到stock2中，并丢弃它。如果编译器使用的是这种方式，则将会生成下面的输出。略
- **注意：**在默认情况下，将一个对象赋给同类型的另一个对象时，C++将源对象的每个数据成员的内容复制到目标对象中相应的数据成员中
- 在main中``` stock1 = Stock("Nifty Foods", 10, 50.0); ``` stock1对象已经存在，因此这条语句不是对stock1进行初始化，而是将新值赋给它。这是通过让构造程序创建一个新的、临时的对象，然后将其内容复制给stock1来实现的。随后程序调用析构函数，以删除该临时对象
- 因为局部变量（stock1和stock2）放在栈中，因此最后创建的对象将最先被删除

- **提示：**如果既可以通过初始化，也可以通过赋值来设置对象的值，则应采用初始化方式。通常这种方式的效率更高。

#### 5.C++列表初始化
- C++11中可以将列表初始化应用于类
	
#### 6.const成员函数
```C++
const Stock land = Stock("Lasldfoas sdlf !");
land.show();
```
- 对于C++来说，编译器拒绝第二行。因为show()的代码无法确保调用对象不能被修改——调用对象和const一样，不应被修改。我们以前通过将函数参数声明为const引用或指向const的指针来解决这种问题。但这里存在语法问题：show()方法没有任何参数。相反，它所使用的对象是由方法调用隐式地提供的。需要一种新的语法——保证函数不会修改调用的对象。
```C++
void show() const;  // 将const关键字放在函数的括号后面

void stock::show() const;  // 函数头的定义
```
- 以这种方式声明和定义的类函数被称为const成员函数。就像应尽可能将const引用和指针用作函数形参一样，只要类方法不修改调用对象，就应将其声明为const。从现在开始，我们将遵守这一规则。

### 10.3.6 构造函数和析构函数小结
```C++
Bozo(const char * fname, const char * lname);  // constructor prototype

Bozo bozetta = Bozo("Bozetta", "Biggens");  // primary form
Bozo fufu("Fufu", "O'Dweeb");  // short form
Bozo* pc = new Bozo("Popo", "Le Peu");  // dynamic object

Bozo bozetta = {"Bozetta", "Biggens"};  // C++11
Bozo fufu {"Fufu", "O'Dweeb"};  // C++11
Bozo* pc = new Bozo {"Popo", "Le Peu"};  // C++11

```
- **警告：** 接受一个参数的构造函数允许使用赋值语法将对象初始化为一个值：``` classname object = value; ```

- 默认构造函数可以没有任何参数；如果有，则必须给所有参数都提供默认值：
```C++
Bozo();  // default constructor prototype
Bistro(const char* s = "Chez Zero");  // default for Bistro class

// 对于未被初始化的对象，程序将使用默认构造函数来创建
 Bozo bubi;  // use default
 Bozo *pb = new Bozo;  // use default
```

- 如果构造函数使用了new，则必须提供使用了delete的析构函数

## 10.4 this 指针
- 有时候方法可能涉及两个对象，在这种情况下需要使用C++的this指针。
- 要让程序知道存储的数据，最直接的方法是提供一个返回值。
```C++
const Stock& topval(const Stock& s) const;
```
- ↑该函数隐式地访问一个对象，而显式的访问另一个对象，并返回其中一个对象的引用。括号中的const表明，该函数不会修改被显式地访问的对象；而括号后面的const表示，该函数不会修改被隐式地访问的对象。由于该函数返回了两个const对象之一的引用，因此返回类型也应为const引用。
- 假设要对Stock对象stock1和stock2进行比较，并将其中股价总值较高的那一个赋给top对象，则可以使用下面两条语句之一：
```C++
top = stock1.topval(stock2);  // 隐式访问stock1，而显式访问stock2
top = stock2.topval(stock1);  // 隐式访问stock2，而显式访问stock1
```
- 实际上，这种表示法有些混乱。如果可以使用关系运算符 > 来比较这两个对象，将更为清晰。可以使用运算符重载（参见第11章）完成这项工作。
- this指针指向用来调用成员函数的对象（this被作为隐藏参数传递给方法）。一般来说，所有的类方法都将this指针设置为调用它的对象的地址。确实，topval()中的total_val只不过是this->total_val的简写（第4章使用->运算符，通过指针来访问结构成员。这也适用于类成员）
- **注意：** 每个成员函数（包括构造函数和析构函数）都有一个this指针。this指针指向调用对象。如果方法需要引用整个调用对象，则可以使用表达式\*this。在函数的括号后面使用const限定符将this限定为const，这样将不能使用this来修改对象的值。
- 然而，要返回的并不是this。因为this是对象的地址，而是对象本身，即\*this（将解除引用运算符\*用于指针，将得到指针的值）。现在，可以将\*this作为调用对象的别名完成前面的方法定义。

- 返回类型为引用意味着返回的是调用对象本身，而不是其副本。

```C++
#pragma once
// 10.7 stock20.h -- argument version
#ifndef STOCK20_H_  // 防止多次包含，有点像Python的 if __name__ == "__main__"
#define STOCK20_H_
#include <string>

class Stock  // class declaration
{
private:
	std::string company;
	long shares;  // 所持有的股票数量
	double share_val;  // 每股的价格
	double total_val;  // 股票总价格
	void set_tot() { total_val = shares * share_val; }
public:
	// two constructors
	Stock();  // default constructor
	Stock(const std::string& co, long n = 0, double pr = 0.0);
	~Stock();
	void buy(long num, double price);
	void sell(long num, double price);
	void update(double price);
	void show() const;
	const Stock& topval(const Stock& s) const;
};  // note semicolon at the end

#endif // STOCK20_H_
```

```C++
// 10.8 Stock20.h.cpp -- augmented version
//
#include "Stock20.h"
#include <iostream>


// constructors (verbose versions)
Stock::Stock()  // default constructor
{
	std::cout << "Default constructor called\n";
	company = "no name";
	shares = 0;
	share_val = 0.0;
	total_val = 0.0;
}

Stock::Stock(const std::string& co, long n, double pr)
{
	std::cout << "Constructor using " << co << "called\n";
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

// class destructor
Stock::~Stock()
{
	std::cout << "Bey, " << company << "\n";
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

void Stock::show() const
{
	using std::cout;
	using std::ios_base;

	// set format to #.###
	ios_base::fmtflags orig =
		cout.setf(ios_base::fixed, ios_base::floatfield);
	std::streamsize prec = cout.precision(3);

	std::cout << "Company: " << company
		<< "Share: " << shares << '\n'
		<< " Share Price: $" << share_val;

	// set format to #.##
	cout.precision(2);
	std::cout << " Total Worth: $" << total_val << '\n';

	// restore original format
	cout.setf(orig, ios_base::floatfield);
	cout.precision(prec);
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
```

## 10.5 对象数组
- 初始化对象数组的方案是，首先使用默认构造函数创建数组元素，然后花括号中的构造函数将创建临时对象，然后将临时对象的内容复制到相应的元素中。因此，要创建类对象数组，则这个类必须有默认构造函数。
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
		stocks[st].show();
	};
	
	// set pointer to first element
	const Stock* top = &stocks[0];
	for (st = 1; st < STKS; st++)
	{
		top = &top->topval(stocks[st]);
	}

	// now top points to the most valuable holding
	std::cout << "\nMost valuable holding:\n";
	top->show();
	return 0;
}
```

## 10.6 类作用域
- 在类中定义的名称（如类数据成员名和类成员函数名）的作用域都为整个类，作用域为整个类的名称只在该类中是已知的，在类外是不可知的。因此，可以在不同类中使用相同的类成员名而不会引起冲突。
### 10.6.1 作用域位为类的常量
- 两种方式
	- 1.在类中声明一个枚举。但用这种方式声明枚举并不会创建类数据成员。也就是说，每个独立的对象并不会把枚举包含进去。另外，Months只是一个符号名称，在作用域为整个类的代码中遇到它时，编译器将用30来替代它。
	- [ ] 没看懂
	- 2.用static。该常量将与其他静态变量存储在一起，而不是存储在对象在。因此，只有一个Moinths常量，被所有Bakery对象共享。

## 10.6.2 作用域内枚举(C++11)
- 枚举可能会重名，C++11提供新类型的枚举来解决这种问题，就是在变量名前加上class或struct。
- C++11还提高了作用域内枚举的类型安全。在有些情况下，常规枚举将转换为整形，如将其赋给int变量或用于比较表达式时，但作用域内枚举不能隐式地转换为整形:
```C++
enum egg_old {Small, Medium, Large, Jumbo};  // unscoped
enum class t_shirt {Small, Medium, Large, Xlarge};  // scoped
egg_old one = Medium;  // unscoped
.....

// 但在必要时，可进行显式类型转换
int Frodo = int(t_shirt::Small);  // Frodo set to 0
// C++11中，默认情况下，枚举的底层类型为int。还提供了一种语法，可以修改
enum class : short pizza {Small, Medium, Large, XLarge};  // 底层类型指定为short
```

## 10.7 抽象数据类型
```C++
// 10.10 stack.h -- class definition for the stack ADT
#ifndef STACK_H_
#define STACK_H__

typedef unsigned long Item;

class Stack
{
private:
	enum {MAX = 10};  // constant specific to class
	Item items[MAX];  // holds stack items
	int top;  // index for top stack item
public:
	Stack();
	bool isempty() const;
	bool isfull() const;
	// push() returns false if static already is full, true otherwise
	bool push(const Item& item);  // add item to stack
	// pop() returns false if stack already is empth, true otherwise
	bool pop(Item& item);  // pop top into item
};
#endif
```
```C++
// 10.11 stack.cpp -- Stack member functions
#include "stack.h"
Stack::Stack()  // Crate an empty stack
{
	top = 0;
}

bool Stack::isempty() const
{
	return top == 0;
}

bool Stack::isfull() const
{
	return top == MAX;
}
bool Stack::push(const Item& item)
{
	if (top < MAX)
	{
		items[top++] = item;
		return true;
	}
	else
	{
		return false;
	}
}

bool Stack::pop(Item& item)
{
	if (top > 0)
	{
		item = items[--top];
		return true;
	}
	else
	{
		return false;
	}
}
``` 
```C++
// 10.12 stacker.cpp -- testing the Stack class
#include <iostream>
#include <cctype>
#include "stack.h"
int main()
{
	using namespace std;
	Stack st;  // create an empty stack
	char ch;
	unsigned long po;

	cout << "Please enter A to add a purchese order,\n"
		<< "P to process a PO, or Q to quit.\n";
	while (cin >> ch && toupper(ch) != 'Q')
	{
		while (cin.get() != '\n')
		{
			continue;
		}
		if (!isalpha(ch))
		{
			cout << '\a';
			continue;
		}
		switch (ch)
		{
		case 'A':
		case 'a':
			cout << "Enter a PO number to add: ";
			cin >> po;
			if (st.isfull())
			{
				cout << "stack already full\n";
			}
			else
			{
				st.push(po);
			}
			break;
		case 'P':
		case 'p':
			if (st.isempty())
				cout << "stack already empty\n";
			else
			{
				st.pop(po);
				cout << "PO #" << po << " popped\n";
			}
			break;
		}
		cout << "Please enter A to add a purchase order, \n"
			<< "P to process a Po, or Q to qiut.\n";
	}
	cout << "Bye\n";
	return 0;
}
```

## 10.9 复习题
1. 类包含了数据以及对数据的操纵方法，类的特征是：抽象、封装、继承、多态；课后答案：类是用户定义的类型的定义。。。。
2. 抽象有点不太知道。封装和数据隐藏是通过函数，或者说成员函数来实现。get和set方法这些。课后答案：类表示人们可以用类方法的公有接口对类对象执行的操作，这是抽象。
3. 对象是类的实例化。一个类可以有多个对象，一个对象只属于一个类。课后答案：类和对象之间的关系同标准类型与其变量之间的关系相同。
4. 函数是对数据的操纵。内部可以直接访问数据和函数
5. 代码如下：
```C++
class BankAccount
{
public:
	enum{Len = 20};
	BankAccount(const char* name, const char* id, double initbal);
	void Display();
	bool Deposit(double money);
	bool Withdraw(double money);

private:
	char holderName[Len];
	char accountID[Len];
	double balance;
};
```
6. 构造函数在创建时被调用，析构函数在对象删除时调用。父类构造函数先于子类调用，子类析构函数先于父类调用。
7. 代码如下
```C++
BankAccount::BankAccount(const char* name, const char* id, double initBal) : balance(initBal)
{
	strncpy_s(holderName, name, strlen(holderName));

	strncpy_s(accountID, id, strlen(accountID));
}
```
8. 当在类中没有写构造函数是，编译器自动创建默认构造函数。好处就是做一些默认的初始化工作。课后答案：默认构造函数是没有参数或所有参数都有默认值的构造函数。拥有默认构造函数后，可以声明对象，而不初始化它，即使已经定义了初始化构造函数。它还使得能够声明数组。
9. 代码如下：
```C++

```
10. this指针是当前对象所在地址，指向用于调用方法的对象。\*this是对象本身

## 10.10 编程练习
1. 代码如下:
```C++
class BankAccount
{
public:
	enum{Len = 20};
	BankAccount(const char* name, const char* id, double initbal);
	void Display();
	bool Deposit(double money);
	bool Withdraw(double money);

private:
	char holderName[Len];
	char accountID[Len];
	double balance;
};
```
```C++
#include "account.h"
#include <iostream>
#include <cstring>

BankAccount::BankAccount(const char* name, const char* id, double initBal) : balance(initBal)
{
	strncpy_s(holderName, name, strlen(holderName));

	strncpy_s(accountID, id, strlen(accountID));
}

void BankAccount::Display()
{
	std::cout << "Holder Name: " << holderName << std::endl;
	std::cout << "Account Number: " << accountID << std::endl;
	std::cout << "Balance: " << balance << std::endl;
}

bool BankAccount::Deposit(double money)
{
	if (money > 0.0)
	{
		balance += money;
		return true;
	}

	return false;
}

bool BankAccount::Withdraw(double money)
{
	if (money <= 0.0)
	{
		return false;
	}
	else if (money > balance)
	{
		std::cout << "Insufficient balance!" << std::endl;
		return false;
	}
	else
	{
		balance -= money;
		return true;
	}
}

int main() {
	BankAccount account{ "John Doe", "1234567890", 1000 };
	account.Display();
	account.Deposit(500);
	account.Display();
	account.Withdraw(200);
	account.Display();
	account.Withdraw(2000);

	return 0;
}
```
2. 代码如下：
```C++
// person.h
#include <iostream>

using std::string;
using std::cout;
using std::endl;

class Person
{
public:
	Person() { lname = ""; fname[0] = '\0'; }
	Person(const string& ln, const char* fn = "Heyyou");
	void Show() const;
	void FormalShow() const;

private:
	static const int LIMIT = 25;
	string lname;
	char fname[LIMIT];
};
```
```C++
#include "person.h"

Person::Person(const string& ln, const char* fn) : lname(ln)
{
	strncpy_s(fname, fn, LIMIT);
}

void Person::Show() const
{
	cout << "Name: " << fname << " " << lname << endl;
}

void Person::FormalShow() const
{
	cout << "Name: " << lname << " " << fname << endl;
}

int main()
{
	Person one;
	Person two("Smythecraft");
	Person three("Dimwiddy", "Sam");
	one.Show();
	one.FormalShow();
	two.Show();
	two.FormalShow();
	three.Show();
	three.FormalShow();
	return 0;
}
```
3. 代码如下：
```C++
// golf.h
class golf
{
public:
	golf();
	golf(const char* name, int hc = 0);
	
	void handicap(int hc);
	void showgolf();

private:
	static const int Len = 40;
	char m_fullname[Len];
	int m_handicap;
};
```
```C++
// golf.cpp
#include "golf.h"
#include <iostream>

golf::golf()
{
	std::cout << "Enter fulll name:";
	std::cin.getline(m_fullname, Len);

	if (0 == strcmp(m_fullname, ""))
	{
		return;
	}

	std::cout << "Enter Handicap:";
	std::cin >> m_handicap;
	std::cin.get();
}

golf::golf(const char* name, int hc): m_handicap(hc)
{
	strcpy_s(m_fullname, Len, name);
}

void golf::handicap(int hc)
{
	m_handicap = hc;
}

void golf::showgolf()
{
	std::cout << "fullname: " << m_fullname << std::endl;
	std::cout << "Handicap: " << m_handicap << std::endl;
}

int main()
{
	golf g;

	std::cout << "The results are as follows:" << std::endl;
	g.showgolf();
	return 0;
}
```
4. 代码如下：
```C++
#ifndef _SALES_H_
#define _SALES_H_

namespace SALES
{
    class Sales
    {
    private:
        static const int QUARTERS = 4;
        double sales[QUARTERS];
        double average;
        double max;
        double min;

    public:
        Sales(const double ar[], int n);
        Sales();
        void showSales() const;
    };
} // namespace SALES

#endif // _SALES_H_
```

```C++
#include <iostream>
#include "sales.h"

namespace SALES
{
    Sales::Sales(const double ar[], int n)
    {
        int i;
        double total = 0.0;
        max = min = ar[0];

        for (i = 0; (i < n) && (i < QUARTERS); i++)
        {
            sales[i] = ar[i];
            max = (max < ar[i]) ? ar[i] : max;
            min = (min > ar[i]) ? ar[i] : min;

            total += ar[i];
        }

        average = total / i;

        if (n < QUARTERS)
        {
            for (int k = n; k < QUARTERS; k++)
            {
                sales[k] = 0;
            }
        }
    }

    Sales::Sales()
    {
        std::cout << "Enter 4 sales quarters: " << std::endl;

        for (int i = 0; i < QUARTERS; i++)
        {
            std::cin >> sales[i];
            average += sales[i];

            if (i == 0)
            {
                max = min = sales[i];
            }

            if (sales[i] > max)
            {
                max = sales[i];
            }

            if (sales[i] < min)
            {
                min = sales[i];
            }
        }

        average /= QUARTERS;
    }

    void Sales::showSales() const
    {
        std::cout << "Sales of 4 quarters: " << std::endl;

        for (int i = 0; i < QUARTERS; i++)
        {
            std::cout << sales[i] << std::endl;
        }

        std::cout << "Average = " << average << std::endl;
        std::cout << "Max = " << max << std::endl;
        std::cout << "Min = " << min << std::endl;
    }
} // namespace SALES

#include "sales.h"
using namespace SALES;
```
```C++
int main()
{
    double ar[4] = {11.1, 22.2, 33.3, 44.4};
    Sales sl(ar, 3);
    sl.showSales();

    Sales s;
    s.setSales();
    s.showSales();

    return 0;
}

```
5. 代码如下：
```C++

#ifndef STACK_H_
#define STACK_H__
#include <iostream>
struct customer
{
	char fullname[35];
	double payment;
};
typedef customer Item;

class Stack
{
private:
	enum { MAX = 10 };
	Item items[MAX];
	int top;
public:
	Stack();
	bool isempty() const;
	bool isfull() const;
	bool push(const Item& item);
	bool pop(Item& item);
};
#endif
```

```C++
// 10.11 stack.cpp -- Stack member functions
#include "stack.h"
Stack::Stack()  // Crate an empty stack
{
	top = 0;
}

bool Stack::isempty() const
{
	return top == 0;
}

bool Stack::isfull() const
{
	return top == MAX;
}
bool Stack::push(const Item& item)
{
	if (top < MAX)
	{
		items[top++] = item;
		return true;
	}
	else
	{
		return false;
	}
}

bool Stack::pop(Item& item)
{
	if (top > 0)
	{
		item = items[--top];
		return true;
	}
	else
	{
		return false;
	}
}
```

```C++
// 10.12 stacker.cpp -- testing the Stack class
#include <iostream>
#include <cctype>
#include "stack.h"
int main()
{
	using namespace std;
	Stack st;  // create an empty stack
	char ch;
	Item po;
	double tot_payment = 0;

	cout << "Please enter A to add a purchese order,\n"
		<< "P to process a PO, or Q to quit.\n";
	while (cin >> ch && toupper(ch) != 'Q')
	{
		while (cin.get() != '\n')
		{
			continue;
		}
		if (!isalpha(ch))
		{
			cout << '\a';
			continue;
		}
		switch (ch)
		{
		case 'A':
		case 'a':cout << "Enter a customer's name:  ";
			cin.getline(po.fullname, 35);
			cout << "Enter the customer's payment: ";
			cin >> po.payment;

			if (st.isfull())
			{
				cout << "stack already full\n";
			}
			else
			{
				st.push(po);
			}
			break;
		case 'P':
		case 'p':
			if (st.isempty())
				cout << "stack already empty\n";
			else
			{
				st.pop(po);
				cout << "PO # " << po.fullname << ": " << po.payment << "$ popped\n";
				tot_payment += po.payment;
				cout << "Now the total of payment is: " << tot_payment << '$' << endl;
			}
			break;
		}
		cout << "Please enter A to add a purchase order, \n"
			<< "P to process a Po, or Q to qiut.\n";
	}
	cout << "Bye\n";
	return 0;
}
```
6. 代码如下：
```C++
#ifndef _MOVE_H_
#define _MOVE_H_

#include <iostream>

class Move
{
public:
	Move(double a = 0, double b = 0) : x(a), y(b) {};
	void showmove() const;
	Move add(const Move& m) const;
	void reset(double a = 0, double b = 0);
private:
	double x;
	double y;
};
#endif  // _MOVE_H_
```

```C++
#include "move.h"

void Move::showmove() const
{
    std::cout << "x = " << x << ", y = " << y << std::endl;
}

Move Move::add(const Move& m) const
{
    return Move(x + m.x, y + m.y);
}

void Move::reset(double a, double b)
{
    x = a;
    y = b;
}

int main()
{
	Move m1(3, 4);
	Move m2(1.5, 2.5);

	m1.showmove(); // Output: x: 3, y: 4
	m2.showmove(); // Output: x: 1.5, y: 2.5

	Move m3 = m1.add(m2);
	m3.showmove(); // Output: x: 4.5, y: 6.5

	m1.reset();
	m1.showmove(); // Output: x: 0, y: 0

	return 0;
}
```

7. 代码如下：
```C++

#ifndef _PLORG_H_
#define _PLORG_H_

class Plorg
{
public:
	Plorg(const char* name = "Plorga", int ci = 50);
	void setCI(int ci);
	void report();
	
private:
	static const int NAME_SIZE = 20;
	char plorgName[NAME_SIZE];
	int plorgCI;
};

#endif  // _PLORG_H_
```
```C++
#include "plorg.h"
#include <iostream>
#include <cstring>

Plorg::Plorg(const char* name, int ci)
{
	strcpy_s(plorgName, NAME_SIZE-1, name);
	plorgName[NAME_SIZE - 1] = '\0';
	plorgCI = ci;
}

void Plorg::setCI(int ci)
{
	plorgCI = ci;
}

void Plorg::report()
{
	std::cout << "Plorg Name: " << plorgName << std::endl;
	std::cout << "Plorg CI: " << plorgCI << std::endl;
}

int main()
{
	Plorg p1; // 使用默认名称和满意指数创建Plorg对象
	Plorg p2("Tony", 75); // 使用自定义名称和满意指数创建Plorg对象

	p1.report(); // 输出默认创建的Plorg对象的名称和满意指数
	p2.report(); // 输出自定义创建的Plorg对象的名称和满意指数

	p1.setCI(60); // 修改满意指数
	p1.report(); // 输出修改后的满意指数
}
```

8. 代码如下：
```C++
#ifndef _LIST_H_
#define _LIST_H_

typedef int Item;

class List
{
public:
	List();
	void AddItem(const Item& value);
	bool IsEmpty();
	bool IsFull();
	Item getValue(int index);
	void visit(void (*pf)(Item&));

private:
	static const int MAX = 10;
	int m_curSize;
	Item m_item[MAX];
};

#endif // _LIST_H_

```

```C++
#include "list.h"

List::List()
{
	m_curSize = 0;
}

void List::AddItem(const Item& value)
{
	if (IsFull())
	{
		return;
	}

	m_item[m_curSize] = value;
	m_curSize++;
}

bool List::IsEmpty()
{
	return 0 == m_curSize;
}

bool List::IsFull()
{
	return MAX == m_curSize;
}

Item List::getValue(int index)
{
	if (index < 0 || index > MAX)
	{
		return Item();
	}

	return m_item[index];
}

void List::visit(void(*pf)(Item&))
{
	for (int i = 0; i < m_curSize; i++)
	{
		pf(m_item[i]);
	}
}

```

```C++
#include <iostream>
#include "list.h"

void printItem(Item& item)
{
	std::cout << item << " ";
}

int main()
{
	List myList;

	std::cout << "Is list empty? " << (myList.IsEmpty() ? "Yes" : "No") << std::endl;

	myList.AddItem(10);
	myList.AddItem(20);
	myList.AddItem(30);

	std::cout << "Is list empty? " << (myList.IsEmpty() ? "Yes" : "No") << std::endl;
	std::cout << "Is list full? " << (myList.IsFull() ? "Yes" : "No") << std::endl;

	std::cout << "Items in the list: ";
	myList.visit(printItem);  // 牛啊！！！
	std::cout << std::endl;

	return 0;
}
```
