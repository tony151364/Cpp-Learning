# 第14章 C++中的代码重用

- C++的一个主要目标就是促进代码重用。
- [x] 本章介绍其他方法，其中之一就是使用这样的类成员：本身是另一个类的对象。这种方法称为包含(containment)、组合(composition)或层次化(layering)。
- [x] 另一种方法是使用私有或者保护继承。通常，包含、私有继承和保护继承用于实现has-a关系，即新的类包含另一个类的对象。
- 多重继承使得能够使用两个或更多的基类派生出新的类，将基类的功能组合在一起。
- 本章将介绍另一种重用代码的方法——类模板。类模板使我们能够使用通用术语定义类，然后使用模板来创建针对特定类型定义的特殊类。
- 例如：可以定义一个通用的栈模板。可以用该模板创建一个用于表示int值栈的类，也可创建用于表示double值栈的类，甚至可以创建一个由栈组成的栈

## 14.1 包含对象的类
- 姓名：string类
- 分数：balarray类

### 14.1.1 valarray类简介
- 顾名思义，这个类用于处理数值（或具有类似特性的类），它支持诸如将数组中所有元素的值相加以及在数组中找出最大值和最小的值等操作。
- valarray被定义为一个模板类，以便能够处理不同的数据类型
```C++
valarray<int> q_values;  // an array of int
valarray<double> weights;  // an array of double
```

- 下面是几个使用其构造函数的例子：
- [x] 有点类似第四章的vector和array类，可以对比一下。（这些都是STL里面的东西）
```C++
valarray<double> v1;  			// an array of double, size 0
valarray<int> v2(8);  			// an array of 8 int elements
valarray<int> v3(10, 8);		// an array of 8 int elements, each set to 10

double gpa[5] = {3.1, 3.5, 3.8, 2.9, 3.3};
valarray<double> v4(gpa, 4);  // an array of 4 elements, initialized to the first 4 elements of gpa

valarray<int> v5 = {20, 32, 17, 9};  // 在C++ 11 中也可以使用初始化列表
```
- 可以使用下标来访问元素

### 14.1.2 Student类的设计
- [x] is-a 模型：继承，子类继承父类，子类is-a父类，就是说子类也属于父类类型
- [x] has-a 模型：包含，即创建一个包含其他类对象的类。

```C++
// 例如：可以将Student类声明如下
class Student
{
private:
	string name;
	valarray<double> scores;
	...
};
```

- Student类获得了其成员对象的实现，但没有继承接口。例如Student对象使用了string的实现，但是并非天生就有使用函数string operator+=()的能力

#### 接口和实现
- 使用公有继承时，类可以继承接口，可能还有实现（基类的纯虚函数提供接口，但不提供实现）。获得接口是is-a关系的组成部分。而使用组合，类可以获得实现，但不能获得接口。不继承接口是has-a关系的组成部分。

- 对于has-a关系来说，类对象不能自动获得被包含对象的接口是一件好事。例如，string类将+运算符重载为将两个字符串连接起来；但从概念上说，将两个Student对象串接起来是没有意义的。这也是不要公有继承的一个原因

### 14.1.3 Student类示例
```C++
// 14.1 studentc.h -- define a Student class using containment
#ifndef STUDENTC_H_
#define STUDENTC_H_
#include <iostream>
#include <string>
#include <valarray>

class Student
{
private:
	typedef std::valarray<double> ArrayDb;
	std::string name;		// contained object（被包含的对象）
	ArrayDb scores;			// contained object
	std::ostream& arr_out(std::ostream& os) const;

public:
	Student() : name("Null"), scores() {}
	explicit Student(const std::string& s)
		: name(s), scores() {}
	explicit Student(int n)
		: name("Null"), scores(n) {}
	Student(const std::string& s, int n)
		: name(s), scores(n) {}
	Student(const std::string& s, const ArrayDb& a)
		: name(s), scores(a) {}
	Student(const char* str, const double* pd, int n)
		: name(str), scores(pd, n) {}

	~Student() {}
	
	double Average() const;
	const  std::string& Name() const;
	double& operator[](int i);
	double operator[](int i) const;

	// input
	friend std::istream& operator >>(std::istream& is, Student& std);  // 1 word
	friend std::istream& getline(std::istream& is, Student& stu);  // 1 line
	// output
	friend std::ostream& operator<< (std::ostream& os, const Student& stu);
};

#endif  // STUDENTC_H_
```
- 程序清单14.1所有Student类的构造函数都被定义为内联的：还提供了一些用于输入和输出的友元函数。
- explicit关键字只能用于修饰一个参数的类构造函数，它的作用是表明该构造函数是显式的，而非隐式的。跟它相对应的另一个关键字implicit，意思是隐藏的，类构造函数默认情况下即声明为implicit。
	- 好处是可以避免不合时宜的类型转换，缺点无。所以**Google让所有的单参数构造函数都声明为显式的**。
	- effective C++：这个东西很受欢迎，因为他们禁止编译器执行非预期的类型转换。除非我有一个好理由允许构造函数被用于隐式类型转换，否则我把它声明为explicit
- [x] 前面说过，用一个参数调用的构造函数将用作从参数类型到类类型的隐式转换函数：但这通常不是好主意
- [ ] 回忆一个参数的构造函数(转换函数，P413, chapter 11)
- 在C++中，接收一个参数的构造函数为将类型与该参数相同的值转换为类提供了蓝图，因此下面的构造函数用于将double类型的值转换为Stonewt类型

```C++
Stonewt(double lbs);  // template for double-to-Stonewt conversion
// 也就是说可以边写这样的代码
Stonewt myCat;		// create a Stonewt object
myCat = 19.6;		// use Stonewt(double) to convert 19.6 to Stonewt
/*
程序将使用构造函数Stonewt(double)来创建一个临时的对象，并将19.6作为初始值。随后，
采用逐成员赋值方式将该临时对象的内容复制到myCat中。这一过程称为隐式转换，因为它是自动进行的，而不需要显示器那个值类型转换。

只有接受一个参数的构造函数才能作为转换函数。

将构造函数用作自动类型转换似乎是一项不错的特性。然而当程序员拥有更丰富的C++经验时，将发现这种自动特性并非总是合乎需要的，
因为这会导致意外的类型转换。因此C++11新增了一个关键字explicit来关闭这种特性。
这将关闭上述示例中介绍的隐式转换，但仍然允许显式转换，即显式强制类型转换：
*/
Stonewt myCat;
myCat = 19.6;  // not valid if Stonewt(double) is declared as explicit
myCat = Stonewt(19.6);  // ok,an explict conversion 
myCat = (Stonewt) 19.6 // ik, old form for explicit typecast

// 只接收一个参数的构造函数定义了从参数类型到类类型的转换。
```

##### C++和约束
- C++包含让程序员能够限制程序结构的特性——使用explicit防止单参数构造函数的隐式转换，使用const限制方法修改数据，等等。这样做的根本原因：**在编译阶段出现错误优于在运行阶段出现错误。**

#### 1.初始化被包含的对象

- 成员列表初始化
	- 基类：调用构造函数
	- 成员对象：使用成员名

- C++要求在构建对象的其他部分之前，先构建继承对象的所有成员对象。因此如果省略初始化列表，C++将使用成员对象所属类的默认构造函数。

##### 初始化顺序
- 如果代码使用一个成员的值作为另一个成员的初始化表达式的一部分时，初始化顺序就非常重要
- 初始化的顺序一般为它们被声明的顺序
```C++
// 假设Student构造函数如下：
Student(const char* str, const double* pd, int n)
	: scores(pd, n), name(str) {}
```

#### 2.使用被包含对象的接口
- 被包含对象的接口不是公有的

```C++
// 14.2 studentc.cpp -- Student class using containment

#include "studentc.h"
using std::ostream;
using std::endl;
using std::istream;
using std::string;

double Student::Average() const
{
	if (scores.size() > 0)
	{
		return scores.sum() / scores.size();
	}
	else
	{
		return 0;
	}
}

const string& Student::Name() const
{
	return name;
}

double& Student::operator[](int i)
{
	return scores[i];				// use valarray<double>::operator[]()
}

double Student::operator[](int i) const
{
	return scores[i];
}

// private method
ostream& Student::arr_out(ostream& os) const 
{
	int i;
	int lim = scores.size();
	
	if (lim > 0)
	{
		for (i = 0; i < lim; ++i)
		{
			os << scores[i] << " ";

			if (i % 5 == 4)
			{
				os << endl;
			}
		}

		if (i % 5 != 0) // each row has five numbers
		{
			os << endl;
		}
		
	}
	else
	{
		os << "empty array ";
	}

	return os;
}

// friends
// use string version of operator >> ()
istream& operator >> (istream& is, Student& stu)
{
	is >> stu.name;
	return is;
}

// use string friend getline(ostream&, const string&)
istream& getline(istream& is, Student& stu)
{
	getline(is, stu.name);
	return is;
}

// use string version of operator << ()
ostream& operator << (ostream & os, const Student & stu)
{
	os << "Scores for " << stu.name << ": \n";
	stu.arr_out(os);  // use private method for scores
	return os;
}
```

#### 3.使用新的Student类
```C++
// 14.3 use_stuc.cpp -- using a composite class
// compile with studentc.cpp

#include <iostream>
#include "studentc.h"

using std::cin;
using std::cout;
using std::endl;

void set(Student& sa, int n);
const int pupils = 3;
const int quizzes = 5;

int main()
{
	Student ada[pupils] =
	{
		Student(quizzes), Student(quizzes), Student(quizzes)
	};

	int i;
	for ( i = 0; i < pupils; ++i)
	{
		set(ada[i], quizzes);
	}

	cout << "\nStuendt List:\n";
	for (i = 0; i < pupils; ++i)
	{
		cout << ada[i].Name() << endl;
	}

	cout << "\nResults:";
	for (i = 0; i < pupils; ++i)
	{
		cout << endl << ada[i];
		cout << "average: " << ada[i].Average() << endl;
	}

	cout << "Done.\n";
	return 0;
}

void set(Student& sa, int n)
{
	cout << "Please enter the student's name: ";
	getline(cin, sa);
	cout << "Please enter " << n << " quiz scores:\n";

	for (int i = 0; i < n; i++)
	{
		cin >> sa[i];
	}

	while (cin.get() != '\n') // 吃掉最后一个数后面的字符，包括换行符
		continue;
}
```

## 14.2 私有继承
- C++还有另一种实现has-a关系的途径——私有继承。(公有继承是is-a)
- **使用私有继承，基类的公有成员和保护成员都将称为派生类的私有成员。**
- 这意味着基类方法将不会成为派生对象公有接口的一部分，但可以在派生类的成员函数中使用它们。
- 总之，派生类不继承基类的接口。正如从被包含对象中看到的，这种不完全继承是has-a关系的一部分
- [ ] 使用私有继承，类将继承实现。例如，如果从String类派生出Student类，Student将有一个String类组件，可用于保存字符串 另外，Student方法可以使用String方法来访问String组件。
- 包含将对象作为一个命名的对象添加到类中，而私有继承将对象作为一个未命名的继承对象添加到类中。
- [ ] 我们将使用术语子对象(subobject)来表示通过继承或包含添加的对象。（虚幻不是就有CreateDefaultSubobject吗！）
- 因此私有继承提供的特性和包含相同:获得实现，但不获得接口。所以，私有继承也可以用来实现has-a关系。

### 14.2.1 Studnet类示例（新版本）
- private是默认值，因此省略访问限定符也将导致私有继承
- 多重继承（multiple inheritance， MI）
- [ ] 包含版本提供了两个被显示命名的对象成员，而私有继承提供了两个无名称的子对象成员。这是这两种方法的**第一个区别**

#### 1.初始化基类组件
- [ ] 隐式地继承组件而不是成员对象将影响代码的编写，因为再也不能使用name和scores来描述对象了，而必须使用用于公有继承的技术。（就是初始化列表）
```C++
Student(const char * str, const double * pd, int n)
	: name(str), scores(pd, n) {}		// use object names for containment
```

- 对于继承类，新版本的构造函数将使用成员列表初始化语法，它使用类名而不是成员名来标识构造函数,**这是第二个区别**
```C++
Student(const char* str, const double* pd, int n)
	: std::string(str), ArrayDb(pd, n) {}  // use class names for inheritance 
```

- 下面代码唯一不同的地方是，省略了显式对象名称，并在内联构造函数中使用了类名，而不是成员名。
```C++
#pragma once
// studenti.h  -- defining a Student class using private inheritance
#ifndef STUDENTC_H_
#define STUDENTC_H_

#include <iostream>
#include <valarray>
#include <string>

class Student : private std::string, private std::valarray<double>
{
private:
	typedef std::valarray<double> ArrayDb;
	// private method for scores output
	std::ostream& arr_out(std::ostream& os) const;

public:
	Student() : std::string("Null Student"), ArrayDb() {}
	
	explicit Student(const std::string& s) 
		: std::string(s), ArrayDb() {}

	explicit Student(int n) 
		: std::string("Nully"), ArrayDb(n) {}

	Student(const std::string& s, int n) 
		: std::string(s), ArrayDb(n) {}

	Student(const std::string& s, const ArrayDb& a) 
		: std::string("Nully"), ArrayDb(a) {}

	Student(const char* str, const double* pd, int n)
		: std::string(str), ArrayDb(pd, n) {}

	~Student() {}

	double Average() const;
	double& operator[](int i);
	double operator[](int i) const;
	const  std::string& Name() const;

	// input
	friend std::istream& operator>>(std::istream& is, Student& stu);  // 1 word
	friend std::istream& getline(std::istream& is, Student& stu);  // 1 line
	// output
	friend std::ostream& operator<< (std::ostream& os, const Student& stu);

};

#endif // !STUDENTC_H_
```

#### 2.访问基类的方法
- 图14.2 对象中的对象：私有继承
- 总之，使用包含时将使用用对象名来调用方法，而使用私有继承时将使用类名和作用域解析运算符来调用方法

#### 3.访问基类对象本身
- 但使用私有继承时，该string对象没有名称。那么，Student类的代码如何访问内部的string对象？答案是强制类型转换。将String对象转换为string对象；结果为继承而来的string对象

#### 4.访问基类的友元函数
- 用类名显式地限定函数名**不适合于友元函数**，这是因为友元不属于类。然而可以显式地转换为基类，然后就能调用对应的友元函数。
- 引用stu不会自动转换为string引用，根本原因在于，在私有继承中，在不进行显式类型转换的情况下，**不能将指向派生类的引用或指针赋给基类引用或指针**
- [ ] 也就是说用不了多态？
- [ ] 即便这个是公有继承，也必须使用显式类型转换。因为会导致递归调用
- 另一个原因是，由于这个类使用的是多重继承，编译器将无法确定应转换成那个基类，可能两个基类都提供了operator<<()

```C++
// 14.5 studenti.cpp -- Student class using private inheritance
#include "studenti.h"

using std::istream;
using std::ostream;
using std::string;
using std::endl;

// public methods
double Student::Average() const 
{
	if (ArrayDb::size() > 0)
	{
		return ArrayDb::sum() / ArrayDb::size();
	}
	else
	{
		return 0;
	}
}

const string& Student::Name() const
{
	return (const string&)*this;
}

double& Student::operator[](int i)
{
	return ArrayDb::operator[](i);
}

double Student::operator[](int i) const
{
	return ArrayDb::operator[](i);
}

// private method
ostream& Student::arr_out(ostream& os) const
{
	int i;
	int lim = ArrayDb::size();

	if (lim > 0)
	{
		for (i = 0; i < lim; ++i)
		{
			os << ArrayDb::operator[](i) << " ";

			if (i % 5 == 4)
			{
				os << endl;
			}
		}

		if (i % 5 != 0) // each row has five numbers
		{
			os << endl;
		}

	}
	else
	{
		os << "empty array ";
	}

	return os;
}

// friends
// use string version of operator >> ()
istream& operator >> (istream & is, Student & stu)
{
	is >> (string&) stu;
	return is;
}

// use string friend getline(ostream&, const string&)
istream& getline(istream & is, Student & stu)
{
	getline(is, (string&)stu);
	return is;
}

// use string version of operator << ()
ostream& operator << (ostream & os, const Student & stu)
{
	os << "Scores for " << (const string&)stu << ": \n";
	stu.arr_out(os);  // use private method for scores
	return os;
}
```

#### 5.使用修改后的Student类
- 两个版本的公有接口相同，所以可以用同一个程序测试，只是所包含的头文件名称换了一下
```C++
// 14.6 use_stui.cpp -- using a class with private inheritance
// compile with studenti.cpp

#include <iostream>
#include "studenti.h"

using std::cin;
using std::cout;
using std::endl;

void set(Student& sa, int n);
const int pupils = 3;
const int quizzes = 5;

int main()
{
	Student ada[pupils] =
	{
		Student(quizzes), Student(quizzes), Student(quizzes)
	};

	int i;
	for (i = 0; i < pupils; ++i)
	{
		set(ada[i], quizzes);
	}

	cout << "\nStuendt List:\n";
	for (i = 0; i < pupils; ++i)
	{
		cout << ada[i].Name() << endl;
	}

	cout << "\nResults:";
	for (i = 0; i < pupils; ++i)
	{
		cout << endl << ada[i];
		cout << "average: " << ada[i].Average() << endl;
	}

	cout << "Done.\n";
	return 0;
}

void set(Student& sa, int n)
{
	cout << "Please enter the student's name: ";
	getline(cin, sa);
	cout << "Please enter " << n << " quiz scores:\n";

	for (int i = 0; i < n; i++)
	{
		cin >> sa[i];
	}

	while (cin.get() != '\n') // 吃掉最后一个数后面的字符，包括换行符
		continue;
}
```

### 14.2.2 使用包含还是私有继承
- 基本上都是使用包含：
	- 首先，它易于理解。类声明中包含表示包含类的显式命名对象，代码可以通过名称引用这些对象。而使用将使关系 更抽象
 	- 其次，继承会引起很多问题，尤其从多个基类继承时。......总是使用包含不太可能遇到这样的麻烦。
  	- 另外，包含可以同时包含多个同类的子对象。某个类可能需要3个string对象，而继承只能有一个。 
- 然而，私有继承所提供的特性确实比包含多... 虚函数...
- 通常，应使用包含来建立has-a关系；如果新类需要访问原有类的保护成员，或需要重新定义虚函数，则应使用私有继承

### 14.2.3 保护继承
- 保护继承是私有继承的变体
- **使用保护继承时，基类的公有成员和保护成员都将成为派生类的保护成员。**
- [x] 和私有继承一样，基类的接口在派生类中也是可用的，但在继承层次之外是不可用的
- 当从派生类派生出另一个类是，私有继承和保护继承之间的的主要区别便呈现出来了。使用私有继承时，第三代类将不能使用基类的接口。这是因为基类的公有方法在派生类中将变成私有方法；
- 使用保护继承时，基类的公有方法在第二代中将变成受保护的，因此第三代派生类可以使用它们。
- 隐式向上转换(implicit upcasting)意味着无需进行显式类型转换，就可以将基类指针或引用指向派生类（私有继承没有隐式向上转换）

### 14.2.4 使用using重新定义访问权限
- 假设要让基类的方法在派生类外面可用，方法之一是定义一个使用该基类方法的派生类方法。重新在派生类的公有方法中定义一个Sum()

```C++
double Student::sum() const  // public Student method
{
	return std::valarray<double>::sum();  // use privately-inherited method
}
```

- 使用using声明重新定义了访问权限，牛逼！
- 另一种方法是，将函数调用包装在另一个函数调用中，即使用一个using声明（就像名称空间那样）来指出派生类可以使用特定的基类方法，即使采用的是私有派生。
```C++
class Student: private std::string, private std::valarray<double>
{
···
public:
	using std::valarray<double>::min;
	using std::valarray<double>::max;
	...
}

// 上述using声明使得valarray<double>::min()和valarray<double>::max()可用，就像它们是Student的公有方法：
cout << "high score: " << ada[i].max() << endl;

// 注意，using声明只使用成员名——没有圆括号、函数特征标和返回类型。例如,为使Student类可以使用valarray的operator[]()方法，只需在Student类声明的公有部分包含下面的using声明
using std::valarray<double>::operator[];
```
- using声明只适用于继承，而不适用于包含。

## 14.3 多重继承
- 与单继承一样，公有MI表示的也是is-a关系
- 请注意，必须使用关键字public来限定每一个基类。这是因为，除非特别之处，否则编译器将认为是私有派生：

```C++
class SingingWaiter : public Waiter, Singer {...};  // Singer is a private base
```

- 公有MI表示的也是is-a关系。
- 私有MI和保护MI可以表示has-a关系

- MI最重要的两个问题：
	- 从两个不同的基类继承同名方法
	- 从两个或更多相关基类哪里继承同一个类的多个实例
	
```C++
// 14.7 worker0.h  -- working classes
#ifndef WORKERO_H_
#define WORKERO_H_

#include <string>
using std::string;

class Worker  // an abstract base class
{
private:
	string fullname;
	long id;

public:
	Worker() : fullname("no one"), id(0L) {}
	Worker(const string& s, long n) : fullname(s), id(n) {}
	
	virtual ~Worker() = 0;  // pure virtual destructor
	virtual void Set();
	virtual void Show() const;
};

class Waiter : public Worker
{
private:
	int panache;  // 范
public:
	Waiter() : Worker(), panache(0) {}
	Waiter(const std::string& s, long n, int p = 0)
		: Worker(s, n), panache(p) {}
	Waiter(const Worker& wk, int p = 0)
		: Worker(wk), panache(p) {}
	
	void Set();
	void Show() const;
};

class Singer : public Worker
{
protected:
	enum {other, alto, contralto, soprano,
		bass, baritone, tenor};

	enum {Vtypes = 7};
private:
	// 存储了相应C-风格字符串的指针
	static const char* pv[Vtypes];   // string equivs of voice types
	int voice;

public:
	Singer() : Worker(), voice(other) {}
	Singer(const std::string& s, long n, int v = other)
		: Worker(s, n), voice(v) {}
	Singer(const Worker& wk, int v = other)
		: Worker(wk), voice(v) {}
	
	void Set();
	void Show() const;
};
#endif
```

```C++
// worker0.cpp -- working class methods
#include "worker0.h"
#include <iostream>

using std::cout;
using std::cin;
using std::endl;
// Worker methods

// must implement virtual destructor, even if pure
Worker::~Worker() {}

void Worker::Set()
{
	cout << "Enter worker's name: ";
	getline(cin, fullname);
	cout << "Enter worker's ID: ";
	cin >> id;
	while (cin.get() != '\n')
		continue;
}

void Worker::Show() const
{
	cout << "Name: " << fullname << "\n";
	cout << "Employee ID: " << id << "\n";
}

// Waiter methods
void Waiter::Set()
{
	Worker::Set();
	cout << "Enter waiter's panache rating: ";
	cin >> panache;

	while (cin.get() != '\n')
		continue;
}

void Waiter::Show() const
{
	cout << "Category: waiter\n";
	Worker::Show();
	cout << "Panache rating: " << panache << "\n";
}

// Singer methods
const char* Singer::pv[] = { "other", "alto", "contralto",
			"soproano", "bass", "baritone", "tenor" };

void Singer::Set()
{
	Worker::Set();

	cout << "Enter number for singer's vocal ranger:\n";
	int i;

	for (i = 0; i < Vtypes; i++)
	{
		cout << i << ": " << pv[i] << "    ";

		if (i % 4 == 3)
		{
			cout << endl;
		}
	}

	if (i % 4 != 0)
	{
		cout << endl;
	}

	while (cin >> voice && (voice < 0 || voice >= Vtypes))
	{
		cout << "Please enter a value >= 0 and < " << Vtypes << endl;
	}

	while (cin.get() != '\n')
	{
		continue;
	}
}

void Singer::Show() const
{
	cout << "Category: singer\n";
	Worker::Show();
	cout << "Vocal ranger: " << pv[voice] << endl;
}
```
	
	
```C++
// 14.9 worktest.cpp -- test worker class hierarchy
#include <iostream>
#include "worker0.h"

const int LIM = 4;

int main()
{
	Waiter bob("Bob Apple", 314L, 5);
	Singer bev("Beverly Hills", 522L, 3);
	Waiter w_temp;
	Singer s_temp;

	Worker* pw[LIM] = { &bob, &bev, &w_temp, &s_temp };

	int i;
	for (i = 2; i < LIM; i++)
	{
		pw[i]->Set();
	}

	for (i = 0; i < LIM; i++)
	{
		pw[i]->Show();
		std::cout << std::endl;
	}

	return 0;
}
```

### 14.3.1 有多少Worker
```C++
class SingingWaiter: public Singer, public Waiter { ... };
SingingWaiter ed;
Worker* pw = &ed;  // ambigous 出现二义性
// 通常，这种复制将把基类指针设置为派生对象中的基类对象的地址。但ed中包含两个Worker对象，有两个地址可供选择，所以应使用类型转换来指定对象
Worker* pw1 = (Waiter *) &ed;  // the Worker in Waiter
Worker* pw1 = (Singer *) &ed;  // the Worker in Singer
// 这将使得使用基类指针来引用不同的对象（多态化）复杂化
```
- 包含两个Worker对象拷贝还会导致其他的问题。然而，真正的问题是：为什么需要Worker对象的两个拷贝？唱歌的侍者和其他Worker对象一样，也应值包含一个姓名和一个ID
- C++引入多重继承的同时，引入了一种新技术——虚基类（virtual base class），使MI成为可能

#### 1.虚基类
- 虚基类使得从多个类（它们的基类相同）派生出的对象只继承一个基类对象。例如，通过在类声明中使用关键字virtual，可以使Worker被用作Singer和Waiter的虚基类（virtual 和 public的次序无关紧要
```C++
class Singer : virtual public Worker {...};
class Waiter : public virtual  Worker {...};
// 然后，可以将SingingWaiter类定义为：
class SingingWaiter: public Singer, public Waiter { ... };
```
- 现在，SingingWaiter对象只包含Worker对象的一个副本。从本质上说，它们共享一个Worker对象。
- 因为SingingWaiter现在只包含了一个Worker对象，所以可以使用多态。
- 首先：虚函数和虚基类之间并不存在明显的联系。C++对这种新特性也使用关键字virtual——有点像关键字重载
- 其次：略
- 最后，是否存在麻烦？是的，为了使虚基类能够工作，需要对C++规则进行调整，必须以不同的方式编写一些代码。还需要修改已有的代码...
		
#### 2.新的构造函数规则
- 如果Worker是虚基类，这种通过构造函数构造基类的方式将不起作用。对于下面的MI构造函数
```C++
SingingWaiter(const Worker& wk, int p = 0, int v = Singer::other)
	: Waiter(wk, p), Singer(Wk, p), Singer(wk, v) {}
```
- 存在的问题是：自动传递信息时,将通过2条不同的途径（Waiter和Singer）将wk传递给Worker对象。
- 为了避免这样的冲突，C++在基类是虚的时，禁止信息通过中间类自动传递给基类。因此，上述构造函数将初始化成员panache和voice，但wk参数中的信息将不会传递给子对象Waiter



