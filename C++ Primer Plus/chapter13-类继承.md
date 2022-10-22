- 类继承作用：代码复用，扩展和修改原有类
## 13.1 一个简单的基类
```C++
// tabtenn0.h -- a table-tennis base class
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
// tabtenn0.cpp -- simple base-class methods
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
### 13.1.1 派生一个类
- 使用**公有派生**，基类的公有成员将成为派生类的公有成员；基类的私有部分也成为派生类的一部分，但只能通过基类的公有和保护方法访问
	- 派生类对象存储了基类的数据成员（派生类继承了基类的实现）
	- 派生类对象可以使用基类的方法（派生类继承了基类的接口）

- 需要在继承特性中添加什么？
	- 派生类需要自己的构造函数
	- 派生类可以分局需要添加额外的数据成员和成员函数 

### 13.1.2 构造函数：访问权限的考虑
- [ ] 派生类不能直接访问基类的私有成员，而必须通过基类的方法进行访问。具体来说，派生类构造函数必须使用基类构造函数
- 如果省略成员初始化列表，情况将如何？
	- 必须首先创建基类对象，如果不调用基类构造函数，程序将使用默认的基类构造函数。因此上述代码与下面等效
- 除非要使用默认构造函数，否则应显式调用正确的基类构造函数 
- 第12章介绍过，如果需要使用复制构造函数但没有定义，编辑器将自动生成一个

- [ ] 如果愿意，也可以对派生类成员使用成员初始化列表语法。在这种情况下，应在列表中使用成员名，而不是类名。所以第二个构造函数可以按照下述方式编写

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
// tabtenn1.h -- a table-tennis base class
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
// tabtenn1.cpp -- simple base-class methods
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
	: TableTennisPlayer(tp), rating(r)
{

}
```

```C++

```





