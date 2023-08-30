# 第13章 类继承
## 本章内容包括：
- is-a关系的继承
- 如何以公有方式从一个类派生出另一个类
- 保护访问
- 构造函数成员初始化列表
- 向上和向下强制转换
- 虚成员函数
- 早期（静态）联编与晚期（动态）联编
- 抽象基类
- 纯虚函数
- 何时及如何使用公有继承

## 小结
- 类继承作用：代码复用，扩展和修改原有类

## 13.1 一个简单的基类
```C++
// 13.1 tabtenn0.h -- a table-tennis base class
#ifndef TABTENN0_H_
#define TABTENN0_H_
#include <string>
using std::string;

class TableTennisPlayer
{
private:
	string firstname;
	string lastname;
	bool hasTable;
public:
	TableTennisPlayer(const string& fn = "none",
		const string& ln = "none", bool ht = false);
	void Name() const;
	bool HasTable() const { return hasTable; };
	void ResetTable(bool v) { hasTable = v; };
};
#endif
```

```C++
// 13.2 tabtenn0.cpp -- simple base-class methods
#include "tabtenn0.h"
#include <iostream>

TableTennisPlayer::TableTennisPlayer (const string& fn,
	const string& ln, bool ht):firstname(fn),
	lastname(ln), hasTable(ht) {}

void TableTennisPlayer::Name() const
{
	std::cout << lastname << ", " << firstname;
}
```
- TableTennisPlayer类只是记录会员的姓名以及是否有球桌。

```C++
// 13.3 usett0.cpp -- useing a base class
#include <iostream>
#include "tabtenn0.h"

int main()
{
	using std::cout;
	TableTennisPlayer player1("Chuck", "Blizzard", true);
	TableTennisPlayer player2("Tara", "Boomdea", false);

	player1.Name();
	if (player1.HasTable())
	{
		cout << ": has a table.\n";
	}
	else
	{
		cout << ": hasn't a table.\n";
	}

	player2.Name();
	if (player2.HasTable())
	{
		cout << ": has a table.\n";
	}
	else
	{
		cout << ": hasn't a table.\n";
	}

	return 0;
}
```
- 实例化对象时将C-风格字符串作为参数，但构造函数的形参类型被声明为const string&。这导致类型不匹配，但与第12章创建的String类一样，string类有一个将const char*作为参数的构造函数，使用C-风格字符创初始化string对象时，将自动调用这个构造函数。

### 13.1.1 派生一个类
- 使用**公有派生**，基类的公有成员将成为派生类的公有成员；基类的私有部分也成为派生类的一部分，但只能通过基类的公有和保护方法访问
- 上述（书本中）代码完成了那些工作呢？Ratedplayer对象将具有以下特征：
	- 派生类对象存储了基类的数据成员（派生类继承了基类的实现）
	- 派生类对象可以使用基类的方法（派生类继承了基类的接口）

- 需要在继承特性中添加什么？
	- 派生类需要自己的构造函数
	- 派生类可以根据需要添加额外的数据成员和成员函数 

### 13.1.2 构造函数：访问权限的考虑
- [x] 派生类不能直接访问基类的私有成员，而必须通过基类的方法进行访问。具体来说，派生类构造函数必须使用基类构造函数
- 如果省略成员初始化列表，情况将如何？必须首先创建基类对象，如果不调用基类构造函数，程序将使用默认的基类构造函数。因此上述代码与下面等效
- 除非要使用默认构造函数，否则应显式调用正确的基类构造函数 
- 第12章介绍过，如果需要使用复制构造函数但没有定义，编辑器将自动生成一个
- [x] 如果愿意，也可以对派生类成员使用成员初始化列表语法。在这种情况下，应在列表中使用成员名，而不是类名。所以第二个构造函数可以按照下述方式编写
```C++
// alternative version
RatedPlayer::RatedPlayer(unsigned int r, const TableTennisPlayer& tp) : TableTennisPlayer(tp), rating(r)
{

}
```
- 有关派生类构造函数的要点如下：
	- 首先创建基类对象
	- 派生类构造函数应通过成员列表将基类信息传递给基类的构造函数
	- 派生类构造函数应初始化派生类新增的数据成员 

#### 成员初始化列表
```C++
derived::derived(type1 x, type2 y): base(x, y)  // initializer list
{
	...
}
```
- 除了虚基类外(chapter14)，类只能将值传递回相邻的基类，但后者可以使用相同的机制将信息传递给相邻的基类，以此类推。
- 如果没有在成员初始化列表中提供基类的构造函数，程序将使用默认的基类构造函数。成员初始化列表只能用于构造函数。

### 13.1.2 使用派生类
```C++
// 13.4 tabtenn1.h -- a table-tennis base class
#ifndef TABTENN1_H_
#define TABTENN1_H_
#include <string>
using std::string;

// simple base class
class TableTennisPlayer
{
private:
	string firstname;
	string lastname;
	bool hasTable;
public:
	TableTennisPlayer(const string& fn = "none",
		const string& ln = "none", bool ht = false);
	void Name() const;
	bool HasTable() const { return hasTable; };
	void ResetTable(bool v) { hasTable = v; };
};

// simple derived class
class RatedPlayer :public TableTennisPlayer
{
private:
	unsigned int rating;

public:
	RatedPlayer(unsigned int r = 0, const string& fn = "none",
		const string& ln = "none", bool ht = false);
	RatedPlayer(unsigned int r, const TableTennisPlayer& tp);
	unsigned int Rating() const { return rating; }
	void ResetRating(unsigned int r) { rating = r; }
};
#endif
```

```C++
// 13.5 tabtenn1.cpp -- simple base-class methods
#include "tabtenn1.h"
#include <iostream>

TableTennisPlayer::TableTennisPlayer(const string& fn,
	const string& ln, bool ht) :firstname(fn),
	lastname(ln), hasTable(ht) {}

void TableTennisPlayer::Name() const
{
	std::cout << lastname << ", " << firstname;
}

// RatedPlayer methods
RatedPlayer::RatedPlayer(unsigned int r, const string& fn,
	const string& ln, bool ht) :TableTennisPlayer(fn, ln, ht)
{
	rating = r;
}

RatedPlayer::RatedPlayer(unsigned int r, const TableTennisPlayer& tp)
	: TableTennisPlayer(tp), rating(r)  // 调用基类的默认构造函数
{

}
```
```C++
// 13.6 usett1.cpp -- using a base class and derived class
#include <iostream>
#include "tabtenn1.h"

int main()
{
	using std::cout;
	using std::endl;

	TableTennisPlayer player1("Chuck", "Blizzard", false);
	RatedPlayer rPlayer1(1140, "Mallory", "Duck", true);

	rPlayer1.Name();
	if (rPlayer1.HasTable())
	{
		cout << ": has a table.\n";
	}
	else
	{
		cout << ": hasn't a table.\n";
	}

	player1.Name();
	if (player1.HasTable())
	{
		cout << ": has a table.\n";
	}
	else
	{
		cout << ": hasn't a table.\n";
	}

	cout << "Name: ";
	rPlayer1.Name();

	cout << "; Rating: " << rPlayer1.Rating() << endl;

	// initialize RatedPlayer using TableTennisPlayer object
	RatedPlayer rPlayer2(1212, player1);
	cout << "Name: ";
	rPlayer2.Name();
	cout << "; Rating: " << rPlayer2.Rating() << endl;

	return 0;
}
```

### 13.1.4 派生类和基类之间的特殊关系
- 1.派生类对象可以调用基类的方法，条件是方法不是私有的
- 2.基类指针可以在不进行显式类型转换的情况下指向派生类对象
- 3.基类引用可以在不进行显示类型转换的情况下引用派生类对象
```C++
RatedPlayer rplayer(1140, "Mallory", "Duck", true);
TableTennisPlayer& rt = rplayer;
TableTennisPlayer* pt = &rplayer;
rt.Name();  // invoke Name() with reference
pt->Name();  // invoke Name() with pointer
```
- 然鹅，基类指针或引用只能用于调用基类方法，因此不能使用rt或pt来调用派生类的ResetRanking方法
- 不可以将基类对象和地址赋给派生类对象。
- [x] 如果允许基类隐式地引用派生类对象，则可以使用基类引用派生类对象调用基类的方法。因为派生类继承了基类的方法，所以这样做不会出现问题。如果可以将基类对象赋给派生类引用，将发生什么情况呢？派生类引用能够使基对象调用派生类方法，这样做会出问题。
- 如果基类引用和指针可以指向派生类对象，将出现一些很有趣的结果。其中之一是基类引用定义的函数或指针参数可以用于基类对象或派生类对象。例如，下面：
```C++
void Show(const TableTennisPlayer& rt)
{
	using std::cout;
	cout << "Name: ";
	rt.Name();
	cout << "\nTable: ";
	if (rt.HasTable())
		cout << "yes\n";
	else
		cout << "no\n";
}
```
- 形参rt是一个基类引用，它可以指向基类对象或派生类对象，所以可以在Show()中使用TableTennis参数或Ratedplayer参数：
```C++
TableTennisPlayer player1("Tara", "Boomdea", false);
RatedPlayer rplayer1(1140, "Mallory", "Duck", true);
Show(player1);  // works with TableTennisPlayer argument
Show(rplayer1);  // works with RatedPlayer argument
```
- 对于形参为指向基类的指针的函数，也存在相似的关系。它可以使用基类对象的地址或派生类对象的地址作为实参

- 引用兼容性属性也让您能够将基类对象初始化为派生类对象，尽管不那么直接。假设有这样的代码
```C++
RatedPlayer olaf1(1840, "Olaf", "Loaf", true);
TableTennisPlayer olaf2(olaf1);

// 要初始化olaf2，匹配的构造函数的原型如下：
TableTennisPlayer(const RatedPlayer&);  // doesn't exist

// 类定义中没有这样的构造函数，但存在隐式复制构造函数
// implicit copy constructor
TableTennisPlayer(const TableTennisPlayer&);  // 形参是基类的引用，因此它可以引用派生类。
```
- 这样，将olaf2初始化为olaf1时，将要使用该构造函数，它复制firstname、lastname和hasTable成员。换句话来说，它将olaf2初始化为嵌套在RatedPlayer对象olaf1中的TableTennisPlayer对象。

```C++
// 同样，也可以将派生对象赋给基类对象：
RatedPlayer olaf1(1840, "Olaf", "Loaf", true);
TableTennisPlayer winner;
winner = olaf1;  // assign derived to base object

// 在这种情况下，程序将使用隐式重载赋值运算符：
TableTennisPlayer& operator = (const TableTennisPlayer&) const;
```

## 13.2 继承：is-a 关系
- 继承可以在基类的基础上添加属性，但不能删除基类的属性
- C++三种继承：
	- 公有继承
	- 私有继承
	- 保护继承
- 公有继承最常用，建立起**is-a**(is a kind of)的关系，即派生类对象也是一个基类对象，可以对基类对象执行任何操作，也可以对派生类对象执行。

## 13.3 多态公有继承
- 结合李建忠老师所说的面向对象的核心是多态，想到一句话：继承的目的是为了多态
- 若希望同一个方法在派生类和基类中的行为是不同的。换句话说，方法的行为应取决于调用该方法的对象。这种较复杂的行为称为多态——具有多种形态。
- 有两种方法来实现：
	- 在派生类中重新定义基类的方法
	- 使用虚方法
- **is-a**关系通常是不可逆的。也就是说，水果不是香蕉；同样，Brass对象不具备BrassPlus对象的所有功能。

### 13.3.1 开发Brass类和BrassPlus类
```C++
// 13.7 brass.h -- bank account classes
#ifndef BRASS_H_
#define BRASS_H_
#include <string>

class Brass
{
private:
	std::string fullName;
	long acctNum;
	double balance;

public:
	Brass(const std::string& s = "Nullbody", long an = -1,
		double bal = 0.0);
	void Deposit(double amt);
	virtual void Withdraw(double amt);
	double Balance() const;
	virtual void ViewAcct() const;
	virtual ~Brass() {};
};

// Brass Plus Account Class
class BrassPlus :public Brass
{
private:
	double maxLoan;
	double rate;
	double owesBank;
public:
	BrassPlus(const std::string& s = "Nullbody", long an = -1,
		double bal = 0.0, double ml = 500, double r = 0.11125);
	BrassPlus(const Brass& ba, double ml = 500, double r = 0.11125);

	virtual void ViewAcct() const;
	virtual void Withdraw(double amt);
	void ResetMax(double m) { maxLoan = m; }
	void ResetRate(double r) { rate = r; }
	void ResetOwes() { owesBank = 0; }
};
#endif
```

- 虚方法(virtual method)
	- 如果没有使用关键字virtual，程序将根据引用类型或指针类型选择方法；
	- 如果使用了关键字virtual，程序将根据引用类型或指针指向的对象的类型来选择方法
	- 方法在基类中被声明为虚的后，它在派生类将自动成为虚方法。然而，在派生类声明中使用关键字virtual来指出哪些函数是虚函数也不失为一个好办法。
```C++
// 如果ViewAcct()是虚的，则行为如下：
// behavior with virtual ViewAcct()
// method chosen according to object type
Brass dom("Dominic Banker", 11224, 4183.45);
BrassPlus dot("Dorothy Banker", 12118, 2592.00);
Brass& b1_ref = dom;
Brass& b2_ref = dot;
b1_ref.ViewAcct();  // use Brass::ViewAcct()
b2_ref.ViewAcct();  // use BrassPlus::ViewAcct()
```

- 虚析构函数：这样做是为了确保释放派生类对象时，按正确的顺序调用析构函数。本章后面将详细介绍这个问题。

#### 1.类实现
```C++
// 13.8 brass.cpp -- bank account class methods
#include <iostream>
#include "brass.h"
using std::cout;
using std::endl;
using std::string;

// formatting stuff
typedef std::ios_base::fmtflags format;
typedef std::streamsize precis;
format setFormat();
void restore(format f, precis p);

// Brass methods
Brass::Brass(const string& s, long an, double bal)
{
	fullName = s;
	acctNum = an;
	balance = bal;
}

void Brass::Deposit(double amt)
{
	if (amt < 0)
	{
		cout << "Negative deposit not allowed; "
			<< "deposit is cancelled.\n";
	}
	else
	{
		balance += amt;
	}
}

void Brass::Withdraw(double amt)
{
	// set up ###.## format
	format initialState = setFormat();
	precis prec = cout.precision(2);

	if (amt < 0)
	{
		cout << "Withdrawal amount must be positive; "
			<< "withdrawal canceled.\n";
	}
	else if (amt <= balance)
	{
		balance -= amt;
	}
	else
	{
		cout << "Withdrawal amount of $" << amt
			<< " exceeds your balance.\n"
			<< "Withdrawl canceled.\n";
	}

	restore(initialState, prec);
}

double Brass::Balance() const
{
	return balance;
}

void Brass::ViewAcct() const
{
	// set up ###.## format
	format initialState = setFormat();
	precis prec = cout.precision(2);
	cout << "Client: " << fullName << endl;
	cout << "Account Number: " << acctNum << endl;
	cout << "Balance:$" << balance << endl;
	restore(initialState, prec);  // restore original format
}

// BrassPlus Methods
BrassPlus::BrassPlus(const string& s, long an, double bal,
	double ml, double r) :Brass(s, an, bal)
{
	maxLoan = ml;
	owesBank = 0.0;
	rate = r;
}

BrassPlus::BrassPlus(const Brass& ba, double ml, double r) 
	: Brass(ba)  // uses implicit copy constructor
{
	maxLoan = ml;
	owesBank = 0.0;
	rate = r;
}

// redefine how ViewAcct() works
void BrassPlus::ViewAcct() const
{
	// set up ###.## format
	format initialState = setFormat();
	precis prec = cout.precision(2);

	Brass::ViewAcct();  // display base portion
	cout << "Maximum loan: $ " << maxLoan << endl;
	cout << "Owed to bank: $" << owesBank << endl;
	cout.precision(3);
	cout << "Loan Rate: " << 100 * rate << "%\n";

	restore(initialState, prec);  // restore original format
}

// redefine how Withdraw() works
void BrassPlus::Withdraw(double amt)
{
	// set up ###.## format
	format initialState = setFormat();
	precis prec = cout.precision(2);

	double bal = Balance();
	if (amt <= bal)
	{
		Brass::Withdraw(amt);
	}
	else if (amt <= bal + maxLoan - owesBank)
	{
		double advance = amt - bal;
		owesBank += advance * (1.0 + rate);
		cout << "Bank advance: $" << advance << endl;
		Deposit(advance);
		Brass::Withdraw(amt);
	}
	else
	{
		cout << "Credit limit exceeded, Transaction cancelled.\n";
	}

	restore(initialState, prec);
}

// 设置定点表示法，并返回以前的标记设置
format setFormat()
{
	return cout.setf(std::ios_base::fixed,
		std::ios_base::floatfield);
}

// 重置格式和精度
void restore(format f, precis p)
{
	cout.setf(f, std::ios_base::floatfield);
	precis prec = cout.precision(p);
}
```
- 派生类构造函数在初始化基类私有数据时，采用的是成员初始化列表方法，将基类信息传递给基类构造函数，然后使用构造函数初始化BrassPlus类新增的数据项。
- 在派生类方法中，使用域解析运算符来调用基类方法。
- 对于派生类没有重定义的方法，不用使用域解析运算符。

#### 2.使用Brass和BrassPlus类
```C++
// 13.9 usebrass1.cpp -- testing bank account classes
// compile with brass.cpp
#include <iostream>
#include "brass.h"

int main()
{
	using std::cout;
	using std::endl;

	Brass Piggy("Porcelot Pigg", 381299, 4000.00);
	BrassPlus Hoggy("Horatio Hogg", 382288, 3000.00);
	
	Piggy.ViewAcct();
	cout << endl;

	Hoggy.ViewAcct();
	cout << endl;

	cout << "Depositing $1000 into the Hogg Account:\n";
	Hoggy.Deposit(1000.00);
	cout << "Now balance:$" << Hoggy.Balance() << endl;
	
	cout << "Withdrawing $4200 form the Pigg Account:\n";
	Piggy.Withdraw(4200);
	cout << "Piggy account balance:$" << Piggy.Balance() << endl;

	cout << "Withdrawing $4200 from the Hogg Account:\n";
	Hoggy.Withdraw(4200.00);
	Hoggy.ViewAcct();

	return 0;
}
```

#### 3.演示虚方法的行为
- 可以创建指向Brass的指针数组。这样，每个元素的类型都相同，但由于使用的是公有继承模型，因此Brass指针既可以指向Brass对象，也可以指向BrassPlus对象。因此可以使用一个数组来表示多种类型的对象。这就是多态性
```C++
// 13.10 usebrass2.cpp -- polymorphic example
// compile with brass.cpp
#include <iostream>
#include <string>
#include "brass.h"
const int CLIENTS = 4;

int main()
{
	using std::cin;
	using std::cout;
	using std::endl;

	Brass* p_clients[CLIENTS];
	std::string temp;
	long tempnum;
	double tempbal;
	char kind;

	for (int i = 0; i < CLIENTS; i++)
	{
		cout << "Enter client's name: ";
		getline(cin, temp);
		cout << "Enter client's account number: ";
		cin >> tempnum;
		cout << "Enter opening balance:$";
		cin >> tempbal;
		cout << "Enter 1 for Brass Account or "
			<< "2 for BrassPlus Account: ";

		while (cin >> kind && (kind != '1' && kind != '2'))
		{
			cout << "Enter either 1 or 2: ";
		}

		if (kind == '1')
		{
			p_clients[i] = new Brass(temp, tempnum, tempbal);
		}
		else
		{
			double tmax, trate;

			cout << "Enter the overdraft limit: $";
			cin >> tmax;

			cout << "Enter the interest rate "
				<< "as a decimal fraction: ";
			cin >> trate;

			p_clients[i] = new BrassPlus(temp, tempnum, tempbal, tmax, trate);
		}

		while (cin.get() != '\n')
		{
			continue;
		}
	}
	cout << endl;

	for (int i = 0; i < CLIENTS; i++)
	{
		p_clients[i]->ViewAcct();
		cout << endl;
	}

	for (int i = 0; i < CLIENTS; i++)
	{
		delete p_clients[i];  // free memory
	}

	cout << "Done\n";
	return 0;
}
```

- [ ] 如果Brass::ViewAcct()被声明为虚的，则在任何情况下都将调用Brass::ViewAcct()

#### 4.为何需要虚析构函数
- 如果析构函数不是虚的，则将只调用对应于指针类型的析构函数。对于程序清单13.10，这意味着只有Brass的析构函数被调用，即使指针指向的是一个BrassPlus对象。如果析构函数是虚的，将调用相应对象类型的析构函数。因此，如果指针指向的是BrassPlus对象，将调用BrassPlus的析构函数，然后自动调用基类的析构函数。因此，使用虚析构函数可以确保正确的析构函数序列被调用。对于程序清单13.10，这种正确的行为并不重要，因此析构函数没有执行任何操作。然而，如果BrassPlus包含一个执行某些操作的析构函数，则Brass必须有一个虚析构函数，即使该析构函数不执行任何操作。

## 13.4 静态联编和动态联编
- 将源代码中的函数调用解释为执行特定的函数。在C++中，由于函数重载的缘故，这项任务更复杂。编译器必须查看函数参数以及函数名才能确定使用哪个函数。
- 然而，C/C++编译器可以在编译过程中完成这种联编。在编译过程中进行联编被称为静态联编(static binding)，又称为早期联编(early binding).
- 然而，虚函数使这项工作变得更困难。正如程序清单13.10所示的那样，使用那一个函数是不能在编译时确定的，因为编译器不知道用户将选择哪种类型的对象。所以，编译器必须生成能够在程序运行时选择正确的虚方法的代码，这被称为动态联编(dynamic binging)，又称为晚期联编(late binding)

### 13.4.1 指针和引用类型的兼容性
- 总之，编译器对非虚方法使用静态联编，对虚方法使用动态联编。

#### 1.为什么有两种类型的联编以及默认为静态联编
- 原因有两个：效率和概念模型。
- 首先来看效率：
	为使程序能够在运行阶段进行决策，必须采取一些方法来跟踪基类指针或引用指向的对象类型，这增加了额外的处理开销。例如，如果类不会用作基类，则不需要动态联编。同样，如果派生类(如RatedPlayer)不重新定义基类的方法，也不需要使用动态联编。
	- 在这些情况下，使用静态联编更合理，效率也更高。由于静态联编的效率更高，因此被设置为C++的默认选择。
	- Strousstrup说，C++的指导原则之一是，不要为不适用的特性付出代价（内存或者处理时间）。
- 再来看概念模型：	
	- 仅将那些预期将被重新定义的方法声明为虚的
	- 其他的不需要重定义它。
	
#### 2.虚函数工作原理
- C++规定了虚函数的行为，但将实现方法留给了编译器作者。不需要知道实现方法就可以使用虚函数，但了解虚函数的工作原理有利于更好地理解概念。
- 编译器处理虚函数的方法：给每个对象添加一个隐藏成员。隐藏成员中保存了一个指向函数地址数组的指针。这种数组称为**虚函数表(virtual function table, vtbl)**。虚函数表中存储了为类对象进行声明的虚函数的地址。
- 例如，基类对象包含一个指针，该指针指向基类中所有虚函数的地址表。派生类对象将包含一个指向独立地址表的指针。
- 如果派生类提供了虚函数的新定义，该虚函数表将保存新函数的地址；
- 如果派生类没有重新定义虚函数，该vtbl将保存函数原始版本的地址。如果派生类定义了新的虚函数，则该函数的地址也将被添加到vtbl。注意，无论类包含的虚函数是1个还是10个，都只需要在对象添加1个地址成员。

- 总之，使用虚函数时，在内存和执行速度方面有一定的成本，包括：
	- 每个对象都将增大，增大量为存储地址的空间；
	- 对于每个类，编译器都创建一个虚函数地址表（数组）；
	- 对于每个函数调用，都需要执行一项额外的操作，即到表中查找地址。

### 13.4.3 有关虚函数的注意事项

#### 1.构造函数
- 创建派生类对象时，将调用派生类的构造函数，而不是积累的构造函数，然后，派生类的构造函数将使用基类的一个构造函数，这种顺序不同于继承机制。因此，派生类不继承基类的构造函数，所以将类构造函数声明为虚的没什么意义。

#### 2.折构函数
- 析构函数应当是虚函数，除非类不用作基类。
- 通常基类提供一个虚析构函数，即使它并不需要析构函数。

#### 3.友元
- 友元不是类成员，所以也不是虚函数。如果由于这个原因引起了设计问题，可以通过让友元函数使用虚成员函数来解决。

#### 4.没有重定义

#### 5.重新定义将隐藏方法
- 基类定义一个虚函数，派生类又定义一个一样的，这就会出问题。
```C++
class Dwelling
{
public:
	virtual void showperks(int a) const;
...
};

class Hoverl:public->Dwelling
{
public：
	virtual void showperks() const;
};

// 这将导致问题，可能会出现类似于下面的编译器警告
Warning:Hovel::showperks(void) hides Dwelling::showperks(int)

// 也可能不会出现警告。但不管结果怎样，代码将具有如下含义：
Hover trump;
trump.showperks();  // valid
trump.showperks(5);  // invaild
```
- 总之，重新定义继承的方法并不是重载。如果在派生类中重新定义函数，将不是使用相同的特征标覆盖基类声明，而是隐藏同名的基类方法，不管参数特征标如何。
- 这引出了两条经验规则：
	- [ ] 第一，如果重新定义继承的方法，应确保与原来的原型完全相同，但如果返回类型是基类引用或指针，则可以修改为指向派生类的引用或指针（这种例外是新出现的）。这种特性被称为返回类型协变(convariance of return type)，因此允许返回类型随类型的变化而变化。
	- [ ] 第二，如果基类声明被重载了，则应在派生类中重新定义所有的基类版本。如果只重新定义一个版本，则另外两个版本将被隐藏，派生类对象将无法使用它们。如果不需要修改，则新定义可只调用基类版本。
	
```C++
void Hovel::showperks() const {Dwelling::showperks();}
```

## 13.5 访问控制：protected
- 对于外部世界来说，保护成员的行为与私有成员相似；但对于派生类来说，保护成员的行为与公有成员相似。
- 派生类可以直接访问基类中用protected修饰的变量，也可以直接修改。也就是说，对于派生类来说，该变量就是个公有变量。
- **警告**：最好对类数据成员采用私有访问控制，不要使用保护访问控制。同时通过基类方法使派生类能够访问基类数据。
- 然而，对于成员函数来说，保护访问控制很有用，它让派生类访问公众不能使用的内部函数。

## 13.6 抽象基类(abstract base class, ABC)
- C++通过纯虚函数(pure virtual function)提供未实现的函数。纯虚函数结尾处=0.
```C++
virtual double Area() const = 0;  // a pure virtual function
```
- [ ] 当类声明中包含纯虚函数时，则不能创建该类的对象。这里的理念是，包含纯虚函数的类只用作基类。要成为真正的ABC，必须至少包含一个纯虚函数。
- 总之，在原型中使用=0指出类是一个抽象基类，在类中可以不定义该函数。从ABC派生出的类将根据派生类的具体特征，使用常规虚函数来实现这种接口。
- 继承抽象类的类称为具体类(concrete class)

### 13.6.1 应用ABC概念
```C++
#pragma once
// 13.11 acctabc.h -- bank account classes
#ifndef ACCTABC_H_
#define ACCTABC_H_
#include <iostream>
#include <string>

// Abstract Base Class
class AcctABC
{
private:
	std::string fullName;
	long acctNum;
	double balance;
protected:
	struct Formatting
	{
		std::ios_base::fmtflags flag;
		std::streamsize pr;
	};
	const std::string& FullName() const { return fullName };
	long AcctNum() const { return acctNum; }
	Formatting SetFormat() const;
	void Restore(Formatting& f) const;

public:
	AcctABC(const std::string& s = "Nullbody", long an = -1,
		double bal = 0.0);
	void Deposit(double amt);
	virtual void Withdraw(double amt) = 0;  // pure virtual function
	double Balance() const { return balance; };
	virtual void ViewAcct() const = 0;  // pure virtual function
	virtual ~AcctABC() {};
};

class Brass:public AcctABC
{
public:
	Brass(const std::string& s = "Nullbody", long an = -1,
		double bal = 0.0) : AcctABC(s, an, bal) { };
	virtual void Withdraw(double amt);
	virtual void ViewAcct() const;
	virtual ~Brass() {};
};

// Brass Plus Account Class
class BrassPlus :public AcctABC
{
private:
	double maxLoan;
	double rate;
	double owesBank;
public:
	BrassPlus(const std::string& s = "Nullbody", long an = -1,
		double bal = 0.0, double ml = 500, double r = 0.11125);
	BrassPlus(const Brass& ba, double ml = 500, double r = 0.11125);

	virtual void ViewAcct() const;
	virtual void Withdraw(double amt);
	void ResetMax(double m) { maxLoan = m; }
	void ResetRate(double r) { rate = r; }
	void ResetOwes() { owesBank = 0; }
};
#endif
```

```C++
#pragma once
// 13.12 acctABC.cpp -- bank account class methods
#include <iostream>
#include "acctabc.h"
using std::cout;
using std::ios_base;
using std::endl;
using std::string;


// Abstract Base Class
AcctABC::AcctABC(const string& s, long an, double bal)
{
	fullName = s;
	acctNum = an;
	balance = bal;
}

void AcctABC::Deposit(double amt)
{
	if (amt < 0)
	{
		cout << "Negative deposit not allowed; "
			<< "deposit is cancelled.\n";
	}
	else
	{
		balance += amt;
	}
}

void AcctABC::Withdraw(double amt)
{
	balance -= amt;
}

// protected methods for formatting
AcctABC::Formatting AcctABC::SetFormat() const
{
	Formatting f;
	f.flag =
		cout.setf(ios_base::fixed, ios_base::floatfield);
	f.pr = cout.precision(2);
	return f;
}

// 重置格式和精度
void AcctABC::Restore(Formatting& f) const
{
	cout.setf(f.flag, ios_base::floatfield);
	cout.precision(f.pr);
}

// Brass methods
void Brass::Withdraw(double amt)
{
	if (amt < 0)
	{
		cout << "Withdrawal amount must be positive; "
			<< "withdrawal canceled.\n";
	}
	else if (amt <= Balance())
	{
		AcctABC::Withdraw(amt);
	}
	else
	{
		cout << "Withdrawal amount of $" << amt
			<< " exceeds your balance.\n"
			<< "Withdrawl canceled.\n";
	}
}

void Brass::ViewAcct() const
{
	Formatting f = SetFormat();
	cout << "Brass Client: " << FullName() << endl;
	cout << "Account Number: " << AcctNum() << endl;
	cout << "Balance:$" << Balance() << endl;
	Restore(f);
}

// BrassPlus Methods
BrassPlus::BrassPlus(const string& s, long an, double bal,
	double ml, double r) :AcctABC(s, an, bal)
{
	maxLoan = ml;
	owesBank = 0.0;
	rate = r;
}

BrassPlus::BrassPlus(const Brass& ba, double ml, double r)
	: AcctABC(ba)  // uses implicit copy constructor
{
	maxLoan = ml;
	owesBank = 0.0;
	rate = r;
}

// redefine how ViewAcct() works
void BrassPlus::ViewAcct() const
{
	Formatting f = SetFormat();

	cout << "BrassPlus Client: " << FullName() << endl;
	cout << "Account Number: " << AcctNum() << endl;
	cout << "Balance:$" << Balance() << endl;
	cout << "Maximum loan: $ " << maxLoan << endl;
	cout << "Owed to bank: $" << owesBank << endl;
	cout.precision(3);
	cout << "Loan Rate: " << 100 * rate << "%\n";

	Restore(f);  // restore original format
}

void BrassPlus::Withdraw(double amt)
{

	Formatting f = SetFormat();

	double bal = Balance();
	if (amt <= bal)
	{
		AcctABC::Withdraw(amt);
	}
	else if (amt <= bal + maxLoan - owesBank)
	{
		double advance = amt - bal;
		owesBank += advance * (1.0 + rate);
		cout << "Bank advance: $" << advance << endl;
		cout << "Finance advance: $" << advance * rate << endl;
		Deposit(advance);
		AcctABC::Withdraw(amt);
	}
	else
	{
		cout << "Credit limit exceeded, Transaction cancelled.\n";
	}

	Restore(f);  // restore original format
}


```

```C++
// 13.13 usebrass3.cpp -- polymorphic example
// compile with brass.cpp
#include <iostream>
#include <string>
#include "acctabc.h"
const int CLIENTS = 4;

int main()
{
	using std::cin;
	using std::cout;
	using std::endl;

	AcctABC* p_clients[CLIENTS];
	std::string temp;
	long tempnum;
	double tempbal;
	char kind;

	for (int i = 0; i < CLIENTS; i++)
	{
		cout << "Enter client's name: ";
		getline(cin, temp);
		cout << "Enter client's account number: ";
		cin >> tempnum;
		cout << "Enter opening balance:$";
		cin >> tempbal;
		cout << "Enter 1 for Brass Account or "
			<< "2 for BrassPlus Account: ";

		while (cin >> kind && (kind != '1' && kind != '2'))
		{
			cout << "Enter either 1 or 2: ";
		}

		if (kind == '1')
		{
			p_clients[i] = new Brass(temp, tempnum, tempbal);
		}
		else
		{
			double tmax, trate;

			cout << "Enter the overdraft limit: $";
			cin >> tmax;

			cout << "Enter the interest rate "
				<< "as a decimal fraction: ";
			cin >> trate;

			p_clients[i] = new BrassPlus(temp, tempnum, tempbal, tmax, trate);
		}

		while (cin.get() != '\n')
		{
			continue;
		}
	}
	cout << endl;

	for (int i = 0; i < CLIENTS; i++)
	{
		p_clients[i]->ViewAcct();
		cout << endl;
	}

	for (int i = 0; i < CLIENTS; i++)
	{
		delete p_clients[i];  // free memory
	}

	cout << "Done\n";
	return 0;
}
```

### 13.6.2 ABC理念
- 设计ABC之前，首先应开发一个模型——指出编程问题所需的类以及它们之间的相互关系。
- 一种学院派思想认为，如果要设计类继承层次，则只能将那些不会被用作基类的类设计为具体的类。这种方法更清晰，复杂程度更低。
- **可以将ABC看作是一种必须实施的接口。ABC要求具体派生类覆盖其纯虚函数——迫使派生类遵循ABC设置的接口原则。这种模型在基于组件的编程模式中很常见，在这种情况下，使用ABC使得组件设计人员能够制定“接口约定”，这样确保了从ABC派生的所有组件都至少支持ABC指定的功能**

## 13.7 继承和动态内存分配

- 如果基类使用动态内存分配，并重新定义复制和赋值构造函数，这将怎样影响派生类的实现呢？
- 这个问题取决于派生类的属性

```C++
// Base Class Using DMA
class baseDMA
{
private:
	char* label;
	int rating;
	
public:
	baseDMA(const char* l = "null", int r = 0);
	baseDMA(const baseDMA& rs);
	virtual ~baseDMA();
	baseDMA& operator=(const baseDMA& rs);
...	
}
```

### 13.7.1 第一种清况：派生类不使用new
- baseDMA派生出lackDMA，后者不使用new，也没啥特殊设计。name不需要为其写专门的显示析构函数、复制构造函数和赋值运算符。

```C++
// derived class without DMA
class lacksDMA :public baseDMA
{
private:
	char color[40];
public:
...
}
```

- 1. 析构函数：编译器可以自动生成默认的。派生类的默认构造函数执行后会调用基类析构函数。
- 2. 复制构造函数：
	- [ ] 默认复制构造函数执行成员复制，这对于动态内存分配来说是不合适的，但对于新的lacksDMA成员来说是合适的。
	- lacksDMA之间复制的时候，是基于基类baseDMA的，所以父类的复制构造函数也能完成对应的赋值
- 3. 赋值：对于赋值来说，也是如此。类的默认构造赋值运算将自动使用基类的赋值运算符来对基类组件进行赋值。因此默认复制构造函数也是合适的

- 派生类对象的这些属性也适用于本身是对象的类成员
- [ ] 第12章的string

### 13.7.2 第二种情况：派生类使用new
```C++
// 假设派生类使用了new
// derived class with DMA 
class hasDMA :public baseDMA
{
private:
	char* style;  // use new in constructors
public:
...
};
```

- 在这种情况下，必须为派生类定义显式析构函数、复制构造函数和赋值运算符。
- 1. 析构函数：派生类的析构释放派生类定义的，基类析构释放基类定义的。都要有
```C++
baseDMA::~baseDMA()  // takes case of baseDMA stuff
{
	delete [] label;
}

hasDMA::~hasDMA()  // takes case of hasDMA stuff
{
	delete [] style;
}
```
- 2. 复制构造函数：派生类独有的属性进行复制，就不能再用父类的赋值构造函数
	没有参数类型为hasDMA引用的baseDMA构造函数，也不需要这样的构造函数。因为复制构造函数baseDMA有一个baseDMA引用参数，而基类引用可以指向派生类型。因此，baseDMA复制构造函数将使用hasDMA参数的baseDMA部分来构造新对象的baseDMA部分。
```C++
baseDMA::baseDMA(const baseDMA& rs)
{
	label = new char[std::strlen(rs.label) + 1];
	std::strcpy(label, rs.label);
	rating = rs.rating;
}
```
	- hasDMA复制构造函数只能访问hasDMA的数据，因此它必须调用baseDMA复制构造函数来处理共享的baseDMA数据：
```C++
hasDMA::hasDMA(const hasDMA& hs)
		:baseDMA(hs)
{
	style = new char[std::strlen(hs.style) + 1];
	std::strcpy(style, hs.style);
}
```
- 3. 接下来来看赋值运算符
```C++
baseDMA& baseDMA::operator=(const baseDMA& rs)
{
	if (this == &rs)
		return *this;
	delete [] label;
	label = new char[std::strlen(rs.label) + 1];
	std::strcpy(label, rs.label);
	rating = rs.rating;
	return *this;
}
```
- 由于hasDMA也使用动态内存分配，所有它也需要一个显式赋值运算符。派生类的显式赋值运算符必须负责所有继承的baseDMA基类对象的赋值，可以通过显式调用基类赋值运算符来完成这项工作，如下所示：
```C++
hasDMA& hasDMA::operator=(const hasDMA& hs)
{
	if (this == &hs)
		return *this;
	baseDMA::operator=(hs);  // copy base portion
	delete [] style;  // prepare for new style
	style = new char[std::strlen(hs.style) + 1];
	std::strcpy(style, hs.style);
	return *this;
}
```

- 下述语句看起来有点奇怪：
```C++
baseDMA::operator=(hs);  // copy base portion
```
- 但通过使用函数表示法，而不是运算符表示法，可以使用作用域解析运算符。实际上，该语句的含义如下：
```C++
	*this = hs;  // use baseDMA::operator=()
```
- [ ]总之，基类和派生类都采用动态内存分配...

### 13.7.3 使用动态内存分配和友元的继承示例

```C++
// 13.14 dma.h -- inheritance and dynamic memory allocation
#ifndef DMA_H_
#define DMA_H_
#define _CRT_SECURE_NO_WARNINGS

#include <iostream>

// Base Class Using DMA
class baseDMA
{
private:
	char* label;
	int rating;

public:
	baseDMA(const char* l = "null", int r = 0);
	baseDMA(const baseDMA& rs);
	virtual ~baseDMA();
	baseDMA& operator=(const baseDMA& rs);
	friend std::ostream& operator<<(std::ostream& os, 
		const baseDMA& rs);
};

// derived class without DMA
class lacksDMA :public baseDMA
{
private:
	enum { COL_LEN = 40 };
	char color[COL_LEN];
public:
	lacksDMA(const char* c = "blank", const char* l = "null",
		int r = 0);
	lacksDMA(const char* c, const baseDMA& rs);
	friend std::ostream& operator<<(std::ostream& os,
		const lacksDMA& rs);
};

// derived class with DMA 
class hasDMA :public baseDMA
{
private:
	char* style;  // use new in constructors
public:
	hasDMA(const char* s = "none", const char* l = "null",
		int r = 0);
	hasDMA(const char* s, const baseDMA& rs);
	hasDMA(const hasDMA& hs);
	~hasDMA();
	hasDMA& operator=(const hasDMA& hs);
	friend std::ostream & operator<<(std::ostream& os,
		const hasDMA& hs);
};

#endif  // DMA_H_
```

```C++
// 13.15 dma.cpp --dma class methods

#include "dma.h"
#include <cstring>

// baseDMA methods
baseDMA::baseDMA(const char* l, int r)
{
	label = new char[std::strlen(l) + 1];
	std::strcpy(label, l);
	rating = r;
}

baseDMA::baseDMA(const baseDMA& rs)
{
	label = new char[std::strlen(rs.label) + 1];
	std::strcpy(label, rs.label);
	rating = rs.rating;
}

baseDMA::~baseDMA()  // takes case of baseDMA stuff
{
	delete[] label;
}

baseDMA& baseDMA::operator=(const baseDMA& rs)
{
	if (this == &rs)
		return *this;
	delete[] label;
	label = new char[std::strlen(rs.label) + 1];
	std::strcpy(label, rs.label);
	rating = rs.rating;
	return *this;
}

std::ostream& operator<<(std::ostream& os, const baseDMA& rs)
{
	os << "Label: " << rs.label << std::endl;
	os << "Rating: " << rs.rating << std::endl;
	return os;
}

// lacksDMA methods
lacksDMA::lacksDMA(const char* c, const char* l, int r)
	: baseDMA(l, r)
{
	std::strncpy(color, c, 39);
	color[39] = '\0';
}

lacksDMA::lacksDMA(const char* c, const baseDMA& rs)
{
	std::strncpy(color, c, COL_LEN - 1);
	color[COL_LEN - 1] = '\0';
}

std::ostream& operator<<(std::ostream& os, const lacksDMA& ls)
{
	os << (const baseDMA &)ls;
	os << "Color: " << ls.color << std::endl;
	return os;
}

// hasDMA methods
hasDMA::hasDMA(const char* s, const char* l, int r)
	: baseDMA(l, r)
{
	style = new char[std::strlen(s) + 1];
	std::strcpy(style, s);
}

hasDMA::hasDMA(const char* s, const baseDMA& rs)
	: baseDMA(rs)
{
	style = new char[std::strlen(s) + 1];
	std::strcpy(style, s);
}

hasDMA::hasDMA(const hasDMA& hs)
	:baseDMA(hs)  // invoke base class copy constructor
{
	style = new char[std::strlen(hs.style) + 1];
	std::strcpy(style, hs.style);
}

hasDMA::~hasDMA()  // takes case of hasDMA stuff
{
	delete[] style;
}

hasDMA& hasDMA::operator=(const hasDMA& hs)
{
	if (this == &hs)
		return *this;
	baseDMA::operator=(hs);  // copy base portion
	delete[] style;  // prepare for new style
	style = new char[std::strlen(hs.style) + 1];
	std::strcpy(style, hs.style);
	return *this;
}

std::ostream & operator<<(std::ostream& os, const hasDMA& hs)
{
	os << (const baseDMA &)hs;
	os << "Style: " << hs.style << std::endl;
	return os;
}
```
- [] What?

```C++
// 13.16 usedma.cpp -- inheritance, friends, and DMA
// compile with dma.cpp
#include <iostream>
#include "dma.h"

int main()
{
	using std::cout;
	using std::endl;

	baseDMA shirt("Portabelly", 8);
	lacksDMA balloon("red", "Blimpo", 4);
	hasDMA map("Mercator", "Buffalo Keys", 5);

	cout << "Display baseDMA Object:\n";
	cout << shirt << endl;
	cout << "Display lacksDMA Object:\n";
	cout << balloon << endl;
	cout << "Display hasDMA Object:\n";
	cout << map << endl;
	
	lacksDMA balloon2(balloon);
	cout << "Result of lackDMA copy:\n";
	cout << balloon2 << endl;

	hasDMA map2;
	map2 = map;
	cout << "Result of hasDMA assignment:\n";
	cout << map2 << endl;
	return 0;
}
```

## 13.8 类设计回顾

### 13.8.1 编译器生成的成员函数

#### 1.默认构造函数
- 默认构造函数要么没有参数，要么所有的参数都要默认值。
- 自动生成的默认构造函数的另一项功能是：**调用基类的默认构造函数以及调用本身是对象的成员所属类的默认构造函数**
- 另外，如果派生类构造函数的成员初始化列表中没有显示调用基类构造函数，则编译器将使用基类的默认构造函数来构造派生类对象的基类部分。在这种情况下，如果基类没有构造函数，将导致编译阶段报错。
- 如果定义了某种构造函数，编译器将不会定义默认构造函数。在这种情况下，如果需要默认构造函数，则必须自己提供。
- 提供构造函数的动机之一是确保对象总能被正确地初始化。另外如果类包含指针成员，则必须初始化这些成员。因此，最好提供一个显式默认构造函数，将所有的类数据成员都初始化为合理的值

#### 2.复制构造函数

- 复制构造函数接受其所属类的对象作为参数。
- 在下述情况中将使用复制构造函数：
  - 将新对象初始化为一个同类对象
  - 按值将对象传递给函数
  - 函数按值返回对象
  - 编译器生成临时对象
- 如果程序没有使用（显式或隐式）复制构造函数，编译器将提供原型，但不提供函数定义；否则程序将定义一个执行成员初始化的复制构造函数。也就是说，新对象的每个成员都被初始化为原始对象相应的值。如果成员为类对象，则初始化该成员时，将使用相应类的复制构造函数。

#### 3.赋值运算符

- 赋值的时候需要用到复制构造函数
- 如果需要显式定义复制构造函数，则基于相同的原因，也需要显式定义赋值运算符
- 第18章将讨论C++11新增的两个特殊方法：移动构造函数和移动赋值运算符。

### 13.8.2 其他的类方法

#### 3.转换
- 使用一个参数就可以调用的构造函数定义了从参数类型到类类型的转换。
- 在带一个参数的构造函数原型中使用explicit将禁止进行隐式转换，但仍允许显示转换

#### 5.返回对象和返回引用
- 因此，返回对象的时间成本包括调用复制构造函数来生成副本所需的时间和调用析构函数删除副本所需的时间。
- 直接返回对象和按值传递对象相似：它们都生成临时副本。同样，返回引用和按引用传递对象相似：调用和被调用的函数对同一个对象进行操作
- 总结：如果返回的是临时对象，就不要返回引用。吐过返回的是通过引用或指针传递给它的对象，则应按引用返回对象

#### 6. 使用const
```C++
// 使用const可以确保方法不修改参数：
Star::Star(const char* s) { ... }  // won't change the string to which a points

// 使用const确保方法不修改“调用它的对象”
void Star::show() const { ... }  // won't change invoking object
```
- 通常，可以将返回引用的函数放在赋值语句的左侧，这实际上意味着可以将值赋给引用的对象。但可以使用const来确保引用或指针返回的值不能用于修改对象中的数据
```C++
// 3const
const Stock& stock::topval(const Stock& s) const
{
	if (s.total_val > total_val)
		return s;  // argument object
	else
		return *this;  // invoking object
}
```
- 该方法返回对this或s的引用。因为this和s都被声明为const，所以函数不能对他们进行修改，这意味着返回的引用必须被声明为const
- 注意，如果函数将参数声明为指向const的引用或指针，则不能将改参数传递给另一个函数，除非后者也确保了参数不会被修改

### 13.8.3 公有继承的考虑因素
#### 1. is-a关系
- 请记住，表示is-a关系的方式之一是，无需进行显式类型转换，基类指针就可以指向派生类对象，基类引用可以引用派生类对象

#### 2. 什么不能被继承
- 构造函数不能被继承。在继承链中，每个类都可以使用成员初始化列表将信息传递给相邻的基类
- 析构函数不能被继承：
	- 释放对象时，程序将首先调用派生类的析构函数，然后调用基类的析构函数。
 	- 如果基类有默认析构函数，编译器将为派生类生成默认析构函数
  	- 通常，对于基类，其析构函数应设置为虚的   
- 赋值运算符不能被继承：
	- 原因很简单。派生类继承的方法的特征标与基类完全相同，但赋值运算符的特征标随类而异，这是因为它包含一个类型为其所属类的形参。
 	- 赋值运算符确实有一些有趣的特征，下面介绍它们。 

#### 3.赋值运算符
- 正如多次提到的，如果类构造函数使用new来初始化指针，则需要提供一个显式赋值运算符。
- [ ] 如果派生类也使用了new，必须给类的每个成员提供赋值运算符，而不仅仅是新成员。
```C++
hasDMA& hasDMA::operator=(const hadDMA& hs)
{
	if (this == &hs)
		return *this;
	baseDMA::operator=(hs);  // copy base portion
	delete[] style;  // prepare for new style
	style = new char[std::strlen(hs.style) + 1];
	std::strcpy(style, hs.style);
	return *this;
}
```
- [ ] 总之，可以将派生对象赋给基类对象，但这只涉及基类的成员。
```C++
// 基类直接赋值给派生类是不可行的，除非有下面的转换构造函数
BrassPlus(const Brass&);
BrassPlus(const Brass& ba, double ml = 500, double r = 0.1);  // 也是可行的

// 另一种方法是，定义一个用于将基类赋给派生类的赋值运算符
BrassPlus& BrassPlus::operator=(const Brass&) { ... }
// 该赋值运算符的类型与赋值语句完全匹配，因此无需类型转换
```
- 这两种方法都没有，用强制类型转换也可以，但可能会出现问题。

#### 4.私有成员和保护成员
- 将基类成员设置为私有的可以提高安全性，而将它们设置为保护成员则可简化代码的编写工作，并提供访问速度。
- Stroustrup 在其 《The Design and Evolution of C++》一书中指出，使用私有数据成员比保护数据成员更好，但保护方法很有用

#### 5.虚方法
- 如果不希望重新定义方法，则不必将其声明为虚的，这样虽然无法禁止他人重新定义方法，但表达了这样的意思：您不希望它被重新定义。

#### 6.析构函数
- 析构函数应当是虚的。这样，当通过指向对象的基类指针或删除派生对象时，程序将首先调用派生类的析构函数，然后调用基类的析构函数，而不仅仅是调用基类的析构函数。

#### 7.友元函数
- 由于友元函数并非类成员，因此不能被继承。
- 如果你希望派生类的友元函数能够基类的友元函数，可以通过强制类型转换来实现。
```C++
ostream& operator<<(ostream& os, const hasDMA& hs)
{
	// type cast to match operator<<(ostream&, const baseDMA&）
	os << (const baseDMA& ) hs;
	os << "Style: " << hs.style << endl;
	return os;
}
```
- 也可以使用第15章将讨论的dynamic_cast<>来进行强制类型转换：
```C++
os << dynamic_cast<const baseDMA& > (hs);
```

#### 8.有关使用基类方法的说明
- 派生类构造函数显式地调用成员初始化列表中指定的基类构造函数。
- 派生类可以使用作用域解析运算符来调用公有的和受保护的基类方法。

### 13.8.4 类函数小结
- [ ] 有些运算符既可以是成员函数，也可以是友元，而有些运算符函数只能是成员函数。表总结了这些特征

## 13.10 复习题

## 13.11 编程练习
```C++

```

```C++

```

```C++

```












