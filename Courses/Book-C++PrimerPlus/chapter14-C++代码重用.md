# 第14章 C++中的代码重用
- C++的一个主要目标就是促进代码重用。
- [x] 本章介绍其他方法，其中之一就是使用这样的类成员：本身是另一个类的对象。这种方法称为包含(containment)、组合(composition)或层次化(layering)。
- [x] 另一种方法是使用私有或者保护继承。通常，包含、私有继承和保护继承用于实现has-a关系，即新的类包含另一个类的对象。
- 多重继承使得能够使用两个或更多的基类派生出新的类，将基类的功能组合在一起。
- 本章将介绍另一种重用代码的方法——类模板。类模板使我们能够使用通用术语定义类，然后使用模板来创建针对特定类型定义的特殊类。
- 例如：可以定义一个通用的栈模板。可以用该模板创建一个用于表示int值栈的类，也可创建用于表示double值栈的类，甚至可以创建一个由栈组成的栈

## 14.1 包含对象的类
- 存姓名时，用字符数组会被限制长度，用char*指针和动态内存分配又需要提供大量支持的代码。这时候，用别人提供的类如string类是一个更好的选择。
- 姓名：string类
- 分数：valarray类

### 14.1.1 valarray类简介
- 顾名思义，这个类用于处理数值（或具有类似特性的类），它支持诸如将数组中所有元素的值相加以及在数组中找出最大值和最小的值等操作。
- valarray被定义为一个模板类，以便能够处理不同的数据类型
```C++
valarray<int> q_values;  // an array of int
valarray<double> weights;  // an array of double
```

- 下面是几个使用其构造函数的例子：
- [x] 有点类似第四章的vector和array类，可以对比一下。（这些都是STL里面的东西，不过valarray不属于STL，算是一个简单的模板类容器吧）
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
```

- 程序将使用构造函数Stonewt(double)来创建一个临时的对象，并将19.6作为初始值。随后，采用逐成员赋值方式将该临时对象的内容复制到myCat中。这一过程称为隐式转换，因为它是自动进行的，而不需要显示器那个值类型转换。

- 只有接受一个参数的构造函数才能作为转换函数。

- 将构造函数用作自动类型转换似乎是一项不错的特性。然而当程序员拥有更丰富的C++经验时，将发现这种自动特性并非总是合乎需要的，因为这会导致意外的类型转换。因此C++11新增了一个关键字explicit来关闭这种特性。这将关闭上述示例中介绍的隐式转换，但仍然允许显式转换，即显式强制类型转换：

```C++
Stonewt myCat;
myCat = 19.6;  // not valid if Stonewt(double) is declared as explicit
myCat = Stonewt(19.6);  // ok,an explict conversion 
myCat = (Stonewt) 19.6 // ik, old form for explicit typecast
```
- 只接收一个参数的构造函数定义了从参数类型到类类型的转换。

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

- C++还有另一种实现has-a关系的途径——私有继承。(公有继承是is-a)。对应了私有派生类和私有派生类
- **使用私有继承，基类的公有成员和保护成员都将称为派生类的私有成员。**
- 这意味着基类方法将不会成为派生对象公有接口的一部分，但可以在派生类的成员函数中使用它们。
- 总之，派生类不继承基类的接口。正如从被包含对象中看到的，这种不完全继承是has-a关系的一部分
- 使用私有继承，类将继承实现。例如，如果从String类派生出Student类，Student将有一个String类组件，可用于保存字符串 另外，Student方法可以使用String方法来访问String组件。
- 包含将对象作为一个命名的对象添加到类中，而私有继承将对象作为一个未命名的继承对象添加到类中。
- 我们将使用术语子对象(subobject)来表示通过继承或包含添加的对象。（虚幻不是就有CreateDefaultSubobject吗！）
- 因此私有继承提供的特性和包含相同:获得实现，但不获得接口。所以，私有继承也可以用来实现has-a关系。

### 14.2.1 Studnet类示例（新版本）
- private是默认值，因此省略访问限定符也将导致私有继承
- 多重继承（multiple inheritance， MI）
- 前面的包含（组合）版本提供了两个被显示命名的对象成员，而私有继承提供了两个无名称的子对象成员。这是这两种方法的**第一个区别**

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
// 14.4 studenti.h  -- defining a Student class using private inheritance
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
	const std::string& Name() const;

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
- 但使用私有继承时，该string对象没有名称。那么，Student类的代码如何访问内部的string对象？答案是**强制类型转换**。将String对象转换为string对象；结果为继承而来的string对象

#### 4.访问基类的友元函数
- 用类名显式地限定函数名**不适合于友元函数**，这是因为友元不属于类。然而可以显式地转换为基类，然后就能调用对应的友元函数。

- 引用stu不会自动转换为string引用，根本原因在于，在私有继承中，在不进行显式类型转换的情况下，**不能将指向派生类的引用或指针赋给基类引用或指针**

- [x] 上面这段就是说私有继承不能使用多态：由于私有继承将基类的公共成员变成了私有成员，基类的指针或引用无法直接访问派生类对象。因此，无法通过基类指针或引用来实现多态。尽管私有继承不直接支持多态，但你可以通过公共继承或将基类的成员函数重新公开来实现多态

- [x] 即便这个是公有继承，也必须使用显式类型转换。因为会导致递归调用。（GTP给出了解释）
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

- 然而，私有继承所提供的特性确实比包含多... 
	- 需要重新定义虚函数时。（包含不能）
	- 私有派生类可以使用基类的保护成员

- 通常，应使用包含来建立has-a关系；如果新类需要访问原有类的保护成员，或需要重新定义虚函数，则应使用私有继承

- 不想让别人访问基类的方法就可以用私有继承。或者说想用基类的保护成员时，也可以使用私有继承

#### ChatGTP解答
选择指南:

- 私有继承时，表示派生类依赖于基类的实现，但不希望基类接口暴露给外部。你想利用基类的实现而不是其接口，并且可能需要访问基类的保护成员。

- 包含（成员变量）时，表示类之间的松耦合关系，没有继承关系，类之间的关系只是"有一个"的关系。它使设计更灵活，更容易理解，并且避免了继承带来的复杂性。

结论:

- 选择私有继承或包含取决于你希望如何表达类之间的关系，以及你需要如何访问基类的功能。如果你只需要利用实现而不想暴露接口，私有继承是一个选择。如果你需要更灵活、更易维护的设计，组合（包含）通常是更好的选择。

### 14.2.3 保护继承
- 保护继承是私有继承的变体

- **使用保护继承时，基类的公有成员和保护成员都将成为派生类的保护成员。**

- [x] 和私有继承一样，基类的接口在派生类中也是可用的，但在继承层次之外是不可用的。

- 当从派生类派生出另一个类是，私有继承和保护继承之间的的主要区别便呈现出来了。使用私有继承时，第三代类将不能使用基类的接口。这是因为基类的公有方法在派生类中将变成私有方法；

- 使用保护继承时，基类的公有方法在第二代中将变成受保护的，因此第三代派生类可以使用它们。

- 隐式向上转换(implicit upcasting)意味着无需进行显式类型转换，就可以将基类指针或引用指向派生类（私有继承没有隐式向上转换）


### 14.2.4 使用using重新定义访问权限
- 假设要让基类的方法在派生类外面可用，方法之一是定义一个使用该基类方法的派生类方法。重新在派生类的公有方法中定义一个Sum()（曲线救国方法）

```C++
double Student::sum() const  // public Student method
{
	return std::valarray<double>::sum();  // use privately-inherited method
}
```

- 另一种方法是，将函数调用包装在另一个函数调用中，即使用一个using声明（就像名称空间那样）来指出派生类可以使用特定的基类方法，即使采用的是私有派生。
- using声明只适用于继承，而不适用于包含。
- using声明总是引入一个方法的所有重载版本。
```C++
class Student: private std::string, private std::valarray<double>
{
···
public:
	using std::valarray<double>::min;
	using std::valarray<double>::max;
	...
}
```

- 上述using声明使得valarray<double>::min()和valarray<double>::max()可用，就像它们是Student的公有方法：
```C++
cout << "high score: " << ada[i].max() << endl;
```

- 注意，using声明只使用成员名——没有圆括号、函数特征标和返回类型。例如,为使Student类可以使用valarray的operator[]()方法，只需在Student类声明的公有部分包含下面的using声明
```C++
using std::valarray<double>::operator[];
```

- 使用using声明重新定义了访问权限，牛逼！
```C++
// 使用这两个using之后，之前的arr_out()就不需要了，可以直接写在operator<<()函数里面
public:
	using std::valarray<double>::size;
	using std::valarray<double>::operator[];
```

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
	int panache; // 魅力值
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
补充：C++中定义常量的几种方式：

```C++
// 1.使用 enum 定义常量
enum { CONSTANT_VALUE = 10 };
// 2. 使用 static const 定义常量(仅限于整型)
static const int CONSTANT_VALUE = 10;
// 3. 使用 static constexpr 定义常量(C++11)
static constexpr int CONSTANT_VALUE = 10;
// 4. 使用 inline 静态成员变量（C++17及以后，支持所有类型）
inline static const int CONSTANT_VALUE = 10;
// 5. 使用 const 成员变量
const int CONSTANT_VALUE;
// 6. 使用 #define 预处理指令（不推荐）
#define CONSTANT_VALUE 10
```

```C++
// 14.8 worker0.cpp -- working class methods
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
// 这将使得使用基类指针来引用不同的对象（多态化）变得复杂化
```
- 包含两个Worker对象拷贝还会导致其他的问题。然而，真正的问题是：为什么需要Worker对象的两个拷贝？唱歌的侍者和其他Worker对象一样，也应只包含一个姓名和一个ID
- C++引入多重继承的同时，引入了一种新技术——虚基类（virtual base class），使MI成为可能

#### 1.虚基类
- 虚基类使得从多个类（它们的基类相同）派生出的对象只继承一个基类对象。例如，通过在类声明中使用关键字virtual，可以使Worker被用作Singer和Waiter的虚基类（virtual 和 public的次序无关紧要）
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
	: Waiter(wk, p), Singer(Wk, v) {}
```
- 存在的问题是：自动传递信息时,将通过2条不同的途径（Waiter和Singer）将wk传递给Worker对象。
- 为了避免这样的冲突，C++在基类是虚的时，禁止信息通过中间类自动传递给基类。因此，上述构造函数将通过p,v参数初始化成员panache和voice，但wk参数中的信息将不会传递给子对象Waiter。然而，编译器必须在构造派生对象之前构造基类对象组件；在上述情况下，编辑器将使用Worker的默认构造函数
```C++
SingingWaiter(const Worker& wk, int p = 0, int v = Singer::other)
	: Worker(wk), Waiter(wk,p), Singer(wk, v) {}
```
- [x] 上述代码将显式地调用构造函数worker(const Worker&)。请注意，这种用法是合法的，对于虚基类必须这样做。但对于非虚基类，则是非法的。
- [x] **警告：** 如果类有间接虚基类，则除非只使用该虚基类的默认构造函数，否则必须显式的调用该虚基类的某个构造函数。

### 14.3.2 哪个方法
- 没有在派生类中重新定义Show()方法，调用时，在单继承中将使用最近祖先中的定义。而在多重继承中，每个组件都有一个Show()函数，这将导致二义性
```C++
// 可以使用作用域解析运算符来澄清编程者的意图：
SingingWaiter newhire("Elise Hawks", 2005, 6, soprano);
newhire.Singer:Show();  // use Singer version
```
- 更好的方法是在SingerWaiter中重新定义
- 对于单继承，可用递增调用派生类方法。每个派生类调用基类显示信息并添加自己的信息
```C++
void Worker::Show() const
{
	cout << "Name: " << fullname << "\n";
	cout << "Employee ID: " << id << "\n";
}

void Waiter::Show() const
{
	Worker::Show();
	cout << "Panache rating: " << panache << "\n"l
}

void HeadWaiter::Show() const
{
	Waiter::Show();
	cout << "Presence rating: " << presence << "\n";
}
```
- 对于多继承：
```C++
// 对于多继承的SingerWaiter来说，下面的方法不可行。因为它忽略了Waiter组件
void SingerWaiter::Show()
{
	Singer::Show();
};

// 可以同时调用waiter的Show()版本来补救
void SingerWaiter::Show()
{
	Singer::Show();
	Waiter::Show();
};
```
- 如果同时两个基类的都调用，姓名和ID将显式两次。该怎么办呢？
- 一种方法是采用模块化方式，而不是递增方式。
```C++
void Worker::Data() const
{
	cout << "Name: " << fullname << "\n";
	cout << "Employee ID: " << id << "\n";
}

void Waiter::Data() const
{
	cout << "Panache rating: " << panache << "\n"l
}

void Singer::Data() const
{
	cout << "Vocal ranger: " << pv[voice] << "\n";
}

void SingingWaiter::Data() const
{
	Singer::Data();
	Waiter::Data();
}

void SingingWaiter::Show() const
{
	cout << "Category: singing waiter\n";
	Worker::Data();
	Data();
}
```
- 由于Data()方法只在类内部可用，所以可用private、或protected。由于private将阻止Waiter(派生类)中的代码使用Worker::Data()(基类), 所以这是protected的用武之地。

- 如果Data()方法是保护的，则只能在继承层次结构中的类中使用它，在其他地方则不能使用。

- [ ] 另一种方法是将所有的数据组件都设置为保护的，而不是私有的，不过使用保护方法（而不是保护数据）将可以更严格地控制对数据的访问。（具体看代码）
```C++
// 14.10 workermi.h  -- working classes with MI
#ifndef WORKERMI_H_
#define WORKERMI_H_

#include <string>
using std::string;

class Worker  // an abstract base class
{
private:
	string fullname;
	long id;

protected:
	virtual void Data() const;
	virtual void Get();

public:
	Worker() : fullname("no one"), id(0L) {}
	Worker(const string& s, long n) : fullname(s), id(n) {}

	virtual ~Worker() = 0;  // pure virtual destructor
	virtual void Set() = 0;
	virtual void Show() const = 0;
};

class Waiter : virtual public Worker
{
private:
	int panache;

protected:
	virtual void Data() const;
	virtual void Get();

public:
	Waiter() : Worker(), panache(0) {}
	Waiter(const std::string& s, long n, int p = 0)
		: Worker(s, n), panache(p) {}
	Waiter(const Worker& wk, int p = 0)
		: Worker(wk), panache(p) {}

	void Set();
	void Show() const;
};

class Singer : virtual public Worker
{
protected:
	enum {
		other, alto, contralto, soprano,
		bass, baritone, tenor
	};
	enum { Vtypes = 7 };

	virtual void Data() const;
	virtual void Get();

private:
	static const char* pv[Vtypes];
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

class SingingWaiter : public Singer, public Waiter
{
protected:
	virtual void Data() const;
	virtual void Get();

public:
	SingingWaiter() {}
	SingingWaiter(const std::string& s, long n, int p = 0, int v = other)
		: Worker(s, n), Waiter(s, n, p), Singer(s, n, v) {}
	SingingWaiter(const Worker& wk, int p = 0, int v = other)
		: Worker(wk), Waiter(wk, p), Singer(wk, v) {}
	SingingWaiter(const Waiter& wt, int v = other)
		: Worker(wt), Waiter(wt), Singer(wt, v) {}
	SingingWaiter(const Singer& wt, int p = 0)
		: Worker(wt), Waiter(wt, p), Singer(wt) {}

	void Set();
	void Show() const;
};

#endif  // WORKERMI_H_
```
```C++
// 14.11 workermi.cpp -- working class methods with MI
#include "workermi.h"
#include <iostream>

using std::cout;
using std::cin;
using std::endl;
// Worker methods
Worker::~Worker() 
{
	// must implement virtual destructor, even if pure
}  

void Worker::Data() const
{
	cout << "Name: " << fullname << "\n";
	cout << "Employee ID: " << id << "\n";
}

void Worker::Get()
{
	cout << "Enter worker's name: ";
	getline(cin, fullname);
	cout << "Enter worker's ID: ";
	cin >> id;
	while (cin.get() != '\n')
		continue;
}

// Waiter methods
void Waiter::Set()
{
	cout << "Enter waiter's name";
	Worker::Get();
	Get();
}

void Waiter::Show() const
{
	cout << "Category: waiter\n";
	Worker::Data();
	Data();
}

void Waiter::Data() const
{
	cout << "Panache rating: " << panache << endl;
}

void Waiter::Get()
{
	cout << "Enter waiter's panache rating: ";
	cin >> panache;

	while (cin.get() != '\n')
		continue;
}

// Singer methods
const char* Singer::pv[] = { "other", "alto", "contralto",
			"soproano", "bass", "baritone", "tenor" };

void Singer::Set()
{
	cout << "Enter singer's name: ";
	Worker::Get();
	Get();
}

void Singer::Show() const
{
	cout << "Category: singer\n";
	Worker::Data();
	Data();
}

void Singer::Data() const 
{
	cout << "Vocal range: " << pv[voice] << endl;
}

void Singer::Get()
{
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

// SingingWaiter methods
void SingingWaiter::Data() const
{
	Singer::Data();
	Waiter::Data();
}

void SingingWaiter::Get()
{
	Waiter::Get();
	Singer::Get();
}

void SingingWaiter::Set()
{
	cout << "Enter singing waiter's name: ";
	Worker::Get();
	Get();
}

void SingingWaiter::Show() const 
{
	cout << "Category: singing waiter\n";
	Worker::Data();
	Data();
}
```
```C++
#pragma once
// 14.12 workmi.cpp -- multiple inheritance
#include <iostream>
#include <cstring>
#include "workermi.h"
const int SIZE = 5;

int main()
{
	using std::cin;
	using std::cout;
	using std::endl;
	using std::strchr;

	Worker* lolas[SIZE];

	int ct;
	
	for (ct = 0; ct < SIZE; ++ct)
	{
		char choice;

		cout << "Enter the employee category:\n"
			<< "w: waiter s: singer t: singing waiter q: quit\n";
		cin >> choice;
		
		while (strchr("wstq", choice) == nullptr)
		{
			cout << "Please enter a w, s, t, or q: ";
			cin >> choice;
		}

		if ('q' == choice)
		{
			break;
		}

		switch (choice)
		{
		case 'w': 
			lolas[ct] = new Waiter;
			break;
		case 's':
			lolas[ct] = new Singer;
			break;
		case 't':
			lolas[ct] = new SingingWaiter;
			break;
		}
		cin.get();
		lolas[ct]->Set();
	}

	cout << "\nHere is your staff:\n";
	int i;
	for (i = 0; i < ct; i++)
	{
		cout << endl;
		lolas[i]->Show();
	}
	for (i = 0; i < ct; i++)
	{
		delete lolas[i]; // 直接delete[] lolas不行，会试图释放 lolas 本身所指向的内存块
	}

	cout << "Bye.\n";
	return 0;
}
```
下面介绍一些有关MI的问题：

#### 1.混合使用虚基类和非虚基类
- 如果基类是虚基类，派生类将包含基类的一个子对象。如果基类不是虚基类，派生类将包含多个子对象。
- [ ] 当虚基类和非虚基类混合使用时，情况将如何？

#### 2.虚基类和支配
- 使用虚基类将改变C++解析二义性的方式。
- 使用虚基类将不一定会导致二义性。在这种情况下，如果某个名称优先于（dominates）其他所有名称，则使用它时，即便不使用限定符，也不会导致二义性。
- [ ] 那么一个成员名如何优先于零一个成员名呢？派生类中的名称优先于直接或间接祖先类中的相同名称


### 14.3.3 MI小结

## 14.4 类模板
- 不如把前面的Queue类和Stack类编写成泛型（独立于类型的）形式。这样可以用通用的代码生成存储不同类型值的栈
- 第10章使用typedef来处理这种需求。这种方法有两个缺点：
	- 首先，每次修改类型时都需要编辑头文件
 	- 其次，在每个程序中只能使用这种技术生成一种栈，即不能让typedef同时代表两种不同的类型，因此不能使用这种方法在同一个程序中同时定义int栈和string栈。
- 模板提供参数化（parameterized）类型 ，即能够将类型名作为参数传递给接收方来建立类或函数。传入个int就能创建个int队列

### 14.4.1 定义类模板
- 可以把关键字class看作是变量的类型名，该变量接受类型作为其值，把Type看作是该变量的名称
```C++
// 每个函数头都将以相同的模板声明打头
template <class Type>

// 还需要将类限定符从Stack::改为Stack<Type>::
template <class Type>  // or template <typename Type>
bool Stack<Type>::push(const Type& item)
{
	...
}
```
- 如果在类声明中定义了方法（内联定义），则可以省略模板前缀和类限定符
- 这些模板不是类和成员函数定义。它们是C++编译**指令** ，说明了如何生成类和成员函数定义。
- 不能将模板成员函数放在独立的实现文件中。由于模板不是函数，他们不能单独编译。
- [x] 模板必须与特定的模板实例化请求一起使用。为此，最简单的方法是将所有模板信息放在一个头文件中，并在要使用这些模板的文件中包含该头文件
```C++
// 14.13 stacktp.h -- a stack template
#ifndef _STACKTP_H_
#define _STACKTP_H__

template <typename Type>
class Stack
{
private:
	enum { MAX = 10 };
	Type items[MAX];
	int top;
public:
	Stack();
	bool isempty() const;
	bool isfull() const;
	bool push(const Type& item);
	bool pop(Type& item);
};

template <typename Type>
Stack<Type>::Stack()  // Create an empty stack
{
	top = 0;
}

template <typename Type>
bool Stack<Type>::isempty() const
{
	return top == 0;
}

template <typename Type>
bool Stack<Type>::isfull() const
{
	return top == MAX;
}

template <typename Type>
bool Stack<Type>::push(const Type& item)
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

template <typename Type>
bool Stack<Type>::pop(Type& item)
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
#endif  // _STACKTP_H__
```

### 14.4.2 使用模板类
- 仅在程序包含模板并不能生成模板类，而必须请求实例化。
```C++
Stack<int> kernels;  // create a stack of ints
Stack<string> colonels;  // create a stack of string objects
```
- 泛型标识符——例如这里的Type——称为类型参数（type parameter），这意味着它们类似于变量，但赋给它们的不能是数字，而只能是类型。
- 类模板必须显式提供所需的类型。常规的模板类型可以由编译器来确定的。

```C++
// 14.15 stacktem.cpp -- testing the template Stack class
#include <iostream>
#include <string>
#include <cctype>
#include "stacktp.h"
int main()
{
	using namespace std;
	Stack<std::string> st;  // create an empty stack
	char ch;
	std::string po;

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

### 14.4.3 深入探讨模板类
- 可以使用char指针创建栈，但是如果不对程序做重大修改，将无法很好地工作。切忌盲目使用模板。
- 下面介绍14.14不太适合使用指针栈的原因，然后介绍一个指针栈很有用的例子。

  #### 1. 不正确地使用指针栈
- 三个版本揭示一些模板类的教训。这个3个示例都以完全正确的Stack\<Type>模板为基础
```C++
Stack<char*> st;  // create a stack for pointers-to-char 
```

##### 版本1 
```C++
// 将程序清单14.14中的：
string po;
// 替换为
char* po;
```
- 这旨在用char指针而不是string对象来接收键盘输入。这种方法很快就失败了，因为仅仅创建指针，没有创建用于保存输入的字符串的空间（程序将通过编译，但在cin试图将输入保存在某些不合适的内存单元中时崩溃）

##### 版本2
```C++
string po;
// 替换为
char po[40];
```
- 这为输入的字符串分配了空间。另外，po的类型为char*，因此可以放在栈中。但数组完全与pop()方法的假设相冲突：
```C++
template <class Type>
bool Stack<Type>::pop(Type& item)
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
- 首先，引用变量item必须引用某种类型的左值，而不是数组名。其次，代码假设可以给item赋值。即使item能够引用数组，也不能为数组名赋值。因此这种方法失败了。

##### 版本3
```C++
string po;
// 替换为
char* po = new char[40];
```
- 这为输入的字符串分配了空间。另外，po是变量，因此与pop()的代码兼容。
- 然而，这里将会遇到最基本的问题：只有一个pop变量，该变量总是指向相同的内存单元。每次压栈的地址都一样，弹栈时得到的地址总是指向最后读入的字符串。
- 总的来说，栈并没有保存每一个新字符串，因此没有任何用途。

#### 2.正确使用指针栈
- 使用指针栈的方法之一是，让调用程序提供一个指针数组，其中每个指针都指向不同的字符串。
- 注意：创建不同指针是调用程序的职责，而不是栈的职责。栈的任务是管理指针，而不是创建指针。
- （例子）用一个指针数组模拟Plodson这些行为，其中的指针指向表示车中文件的字符串。每个字符串都包含文件所描述的人的姓名。可以用栈表示收取篮，并使用第二个指针数组来表示发出篮
- 栈的大小可变。下面的例子使用了动态数组。因此，Stack类需要包含一个析构函数、一个复制构造函数和一个赋值运算符。
- 另外，通过将多个方法作为内联函数，精简了代码
```C++
// 14.15 stacktp1.h -- modified stack template
#ifndef _STACKTP1_H_
#define _STACKTP1_H_

template <typename Type>
class Stack
{
private:
	enum { SIZE = 10 };  // default size
	int stacksize;
	Type* items;  // holds stack items
	int top;  // index for top stack item
public:
	explicit Stack(int ss = SIZE);
	Stack(const Stack& st);
	~Stack() { delete[] items; }
	bool isempty() const { return top == 0; }
	bool isfull() const { return top == stacksize; }
	bool push(const Type& item);
	bool pop(Type& item);
	Stack& operator=(const Stack& st);
};

template <typename Type>
Stack<Type>::Stack(int ss) : stacksize(ss), top(0)
{
	items = new Type[stacksize];
}

template <typename Type>
Stack<Type>::Stack(const Stack& st)
{
	stacksize = st.stacksize;
	top = st.top;
	items = new Type[stacksize];

	for (int i = 0; i < top; i++)
	{
		items[i] = st.items[i];
	}
}

template <typename Type>
bool Stack<Type>::push(const Type& item)
{
	if (top < stacksize)
	{
		items[top++] = item;
		return true;
	}
	else
	{
		return false;
	}
}

template <typename Type>
bool Stack<Type>::pop(Type& item)
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

template <typename Type>
Stack<Type> & Stack<Type>::operator=(const Stack<Type>& st)
{
	if (this == &st)
	{
		return *this;
	}

	delete[] items;
	stacksize = st.stacksize;
	top = st.top;
	items = new Type[stacksize];

	for (int i = 0; i < top; i++)
	{
		items[i] = st.items[i];
	}

	return *this;
}

#endif  // _STACKTP1_H_
```

- 原型将赋值运算符函数的返回类型声明为Stack引用，而实际的模板函数定义将类型定义为`Stack<Type>`。前者是后者的缩写，只能在类中使用。即可以在模板声明或模板函数定义内使用Stack，但在类的外面，即指定返回类型或使用作用域解析运算符时，必须使用完整的`Stack<Type>`。

```C++
// 14.16 stkoptr1.cpp -- testing stack of pointers
#include <iostream>
#include <cstdlib>
#include <ctime>
#include "stcktp1.h"
const int Num = 10;

int main()
{
	std::srand(std::time(0));  // randomize rand()
	std::cout << "Please enter stack size: ";
	int stacksize;
	std::cin >> stacksize;

	// create an empty stack with stacksize slots
	Stack<const char*> st(stacksize);

	// in basket
	const char* in[Num] =
	{
		" 1: Hank Gilgamesh", " 2: Kiki Ishtar",
		" 3: Betty Rocker", " 4: Ian Flagranti",
		" 5: Wolfgang Kibble", " 6: Portia Koop",
		" 7: Joy Almondo", " 8: Xaverie Paprika",
		" 9: Juan Moore", " 10: Misha Mache"
	};

	// out basket
	const char* out[Num];

	int processed = 0;
	int nextin = 0;

	while (processed < Num)
	{
		if (st.isempty())
		{
			st.push(in[nextin++]);
		}
		else if (st.isfull())
		{
			st.pop(out[processed++]);
		}
		// 这里通过随机生成0和1，来模拟掷硬币的结果
		else if (std::rand() % 2 && nextin < Num)  // 50-50 chance
		{
			st.push(in[nextin++]);
		}
		else
		{
			st.pop(out[processed++]);
		}
	}

	for (int i = 0; i < Num; i++)
	{
		std::cout << out[i] << std::endl;
	}
	
	std::cout << "Bye\n";
	return 0;
}
```

### 14.4.4 数组模板示例和非类型参数
- 模板常用作容器类，这是因为类型参数的概念非常适合于将相同的存储方案用于不同的类型。确实，为容器类提供可重用代码是引入模板的主要动机。
- [x] 下面探讨一些非类型（或表达式）参数以及如何使用数组来处理继承族。

GTP：
- 非类型模板参数指的是，在模板参数中不仅可以传递类型（如 int、double、class 等），还可以传递 常量值、指针、引用、枚举值 等类型的值。这种参数称为 "非类型模板参数"。（如下面中int n的n）

```C++
// 14.17 arraytp.h -- Array Template
#ifndef ARRAYTP_H_
#define ARRAYTP_H_

#include <iostream>
#include <cstdlib>

template <typename T, int n>
class ArrayTP
{
private:
	T ar[n];
public:
	ArrayTP() {}
	explicit ArrayTP(const T& v);
	virtual T& operator[](int i);  // 读取+修改
	virtual T operator[](int i) const;  // 只读
};

template<typename T, int n>
inline ArrayTP<T, n>::ArrayTP(const T& v)
{
	for (int i = 0; i < n; i++)
	{
		ar[i] = v;
	}
}

template<typename T, int n>
inline T& ArrayTP<T, n>::operator[](int i)
{
	if (i < 0 || i >= n)
	{
		std::cerr << "Error in array limits: " << i
			<< " is out of range\n";
		std::exit(EXIT_FAILURE);
	}

	return ar[i];
}

template<typename T, int n>
inline T ArrayTP<T, n>::operator[](int i) const
{
	if (i < 0 || i >= n)
	{
		std::cerr << "Error in array limits: " << i
			<< " is out of range\n";
		std::exit(EXIT_FAILURE);
	}

	return ar[i];
}

#endif
```

```C++
template<typename T, int n>
```
- 这种参数（指定特殊的类型而不是用作泛型名）称为非类型（non-type）或表达式（expression）参数。
- 表达式参数有一些限制。表达式参数可以是整型、枚举、引用或指针。因此，double m是不合法的，但double* pm是合法的。另外，模板代码不能修改参数的值，也不能使用参数的地址。所以在ArrayTP模板中不能使用诸如n++和&n等表达式。另外，实例化模板时，用作表达式参数的值必须是常量表达式。
- 与Stack中使用的构造函数方法相比，这种改变数组大小的方法有一个优点。构造函数方法使用的是通过new和delete管理的堆内存，而表达式参数方法使用的是为自动变量维护的内存栈。这样，执行速度将更快，尤其是在使用了很多小型数组是。

- 缺点：每种数组大小都将生成自己的模板。也就是说，下面的声明将生成两个独立的类声明：
```C++
ArrayTP<double, 12> eggweights;
ArrayTP<double, 13> dounts;
```
- 但下面的声明只生成一个类声明，并将数组大小信息传递给类的构造函数：
```C++
Stack<int> eggs(12);
Stack<int> dunkers(13);
```
- 另一个区别是，构造函数方法更通用，这是因为数组大小是作为类成员（而不是硬编码）存储在定义中。这样可以将一种尺寸的数组赋给另一种尺寸的数组，也可以创建允许数组大小可变的类。

### 14.4.5 模板多功能性
- 可以将用于常规类的技术用于模板类。模板类可用作基类，也可用作组件类，还可用作其他模板的类型参数。
- 例如，可以使用数组模板实现栈模板，也可以使用数组模板来构造数组——数组元素是基于栈模板的栈。
- 即可以编写下面的代码：
```C++
template <typename T>  // or <class T>
class Array
{
private:
	T entry;
	...
};

template <typename Tp>
class Stack
{
	Array<Tp> ar;  // use an Array<> as a component
	...
};

...

Array <Stack<int>> asi;  // an array of stacks of int
```
- 在最后一条语句中，C++98要求使用至少一个空白字符将两个>符号分开，以免与运算符>>混淆。C++11不要求这样做。

#### 1.递归使用模板
- 另一个模板多功能性的例子是，可以递归使用模板。例如，对于前面的数组模板，可以这样使用它：
```C++
ArrayTP<ArrayTP<int, 5>, 10> twodee;
```
- 这使得twodee是一个包含10个元素的数组，其中每个元素都是一个包含5个int元素的数组。与之等价的常规数组声明如下：
```C++
int twodee[10][5];
```
- 请注意，在模板语法中，维的顺序与等价的二维数组相反。

```C++
// 14.18 twod.cpp -- making a 2-d array
#include <iostream>
#include "arraytp.h"

int main(void)
{
	using std::cout;
	using std::endl;
	ArrayTP<int, 10> sums;
	ArrayTP<double, 10> aves;
	ArrayTP< ArrayTP<int, 5>, 10> twodee;

	int i, j;

	for (i = 0; i < 10; i++)
	{
		sums[i] = 0;
		for (j = 0; j < 5; j++)
		{
			twodee[i][j] = (i + 1) * (j + 1);
			sums[i] += twodee[i][j];
		}
		
		aves[i] = (double)sums[i] / 10;
	}

	for (i = 0; i < 10; i++)
	{
		for (j = 0; j < 5; j++)
		{
			cout.width(2);
			cout << twodee[i][j] << ' ';
		}

		cout << ": sum = ";
		cout.width((3));
		cout << sums[i] << ", average = " << aves[i] << endl;
	}

	cout << "Done.\n";
}
```

#### 2.使用多个类型参数
- 模板可以包含多个类型参数。例如，假设希望类可以保存两种值，则可以创建并使用Pair模板来保存两个不同的值（STL提供了类似的模板，名为pair）。
```C++
// 14.19 pairs.cpp -- defining and using a Pair template
#include <iostream>
#include <string>

template <typename T1, typename T2>
class Pair
{
private:
	T1 a;
	T2 b;
public:
	T1& first();
	T2& second();

	T1 first() const { return a; }
	T2 second() const { return b; }
	Pair(const T1& aval, const T2& bval): a(aval), b(bval) {}
	Pair() {}
};

template <typename T1, typename T2>
T1& Pair<T1, T2>::first()
{
	return a;
}

template<typename T1, typename T2>
T2& Pair<T1, T2>::second()
{
	return b;
}

int main()
{
	using std::cout;
	using std::endl;
	using std::string;

	Pair<string, int> ratings[4] =
	{
		Pair<string, int>("The Purpled Duck", 5),
		Pair<string, int>("Jaquie's Frisco AI Fresco", 4),
		Pair<string, int>("Cafe Souffle", 5),
		Pair<string, int>("Bertie's Eats", 3)

	};

	int joints = sizeof(ratings) / sizeof(Pair<string, int>);  // 666，还可以这样写
	
	cout << "Rating:\t Eatery\n";
	for (int i = 0; i < joints; i++)
	{
		cout << ratings[i].second() << ":\t"
			<< ratings[i].first() << endl;
	}

	cout << "Oops! Revised rating:\n";
	ratings[3].first() = "Bertie's Fab Eats";
	ratings[3].second() = 6;

	cout << ratings[3].second() << ":\t"
		<< ratings[3].first() << endl;
	return 0;
}
```
- [x] 好奇函数调用时编译器是如何区分左值和右值的。如：first()的这两个函数。（根据表达式的上下文来决定）
	- 如果函数返回的值被用于赋值（修改），那么编译器会选择返回引用的版本，这样允许通过返回的引用来修改内部数据。
	- 如果函数返回的值仅用于读取（右值），编译器会选择返回值的 const 版本，保证数据不被修改。

#### 3.默认类型模板参数
```C++
// 类模板的另一项新特性是，可以为类型参数提供默认值：
template <class T1, class T2 = int> class Topo {...};

// 这样，如果省略T2的值，编译器将使用int：
Topo<double, double> m1;  // T1 is double, T2 is double
Topo<double> m2;  // T1 is double, T2 is int
```

- 第16章将讨论的标准模板库经常使用该特性，将默认类型设置为类。
- [x] 虽然可以为**类模板**类型类型参数提供默认值，但不能为**函数模板**参数提供默认值。然而，可以为非类型参数提供默认值
	- GTP：函数模板的类型参数不能提供默认值，这是因为函数模板的类型通常可以通过参数推导自动推断出，而不需要显式指定。这是函数模板和类模板之间的一个重要区别
```C++
template <typename T1, typename T2>
void func(T1 a, T2 b) {
    // Function implementation
}

func(3, 4.5);  // 编译器推导出 T1=int, T2=double
```

### 14.4.6 模板的具体化
- 类模板与函数模板很相似，因为可以有隐式实例化、显式实例化和显式具体化，它们统称为具体化（specialization)。模板以泛型的方式描述类，而具体化是使用具体的类型生成类声明。

#### 1.隐式实例化
- 截止目前，本章所有的模板示例使用的都是隐式实例化（implicit instantiation），即它们声明一个或多个对象，指出所需的类型，而编译器使用通用模板提供的处方生成具体的类定义：
```C++
ArrayTP<int, 100> stuff;  // implicit instantiaion

// 编译器在需要对象之前，不会生成类的隐式实例化：
ArrayTP<double, 30>*pt;  // a pointer, no object needed yet
pt = new ArrayTP<double, 30>;  // now an object is needed
// 第二题语句导致编译器生成类定义，并根据该定义创建一个对象。
```

#### 2.显式实例化
- 当使用关键字template并指出所需类型来声明类时，编译器将生成类声明的显式实例化（explicit instantiation)。
- [x] 声明必须位于模板定义所在的名称空间中。
```C++
template class ArrayTP<string, 100>;  // generate ArrayTP<string, 100> class
```
- 在这种情况下，虽然没有创建或提及类对象，编译器也将生成类声明（包括方法定义）。和隐式实例化一样，也将根据通用模板来生成具体化。
- 显示实例化**优点**：加快编译速度（因为提前准备好了）；降低二进制代码膨胀。
```C++
template<typename T>
T add(T a, T b) {
    return a + b;
}

// 显式实例化
template int add<int>(int, int);

int main() {
    int result = add(5, 10);  // 这里编译器将使用已显式实例化的add<int>(int, int)
    return 0;
}
```

#### 3.显式具体化
- 显式具体化（explicit specialization）是特定类型（用于替换模板中的泛型）的定义。有时候，可能需要在为特殊类型实例化时，对模板进行修改，使其行为不同。在这种情况下，可以创建显式具体化。例如：假设已经为用于表示排序后的数组的类（元素加入时被排序）定义了一个模板：
```C++
template <typename T>
class SortedArray
{
	... // details omitted
};
```

- 另外，假设模板使用>运算符来对值进行比较。对于数字，这管用；如果T表示一种类，则只需要定义T::operator>()方法，这也管用；但如果T是由const char*表示的字符串，这将不管用。因为会按地址字母顺序排序。这种情况下，可以提供一个显式模板具体化。当具体化模板和通用模板都与实例化请求匹配时，编译器将使用具体化版本。

```C++
// 具体化类模板定义的格式如下：
template <> 
class Classname<specialized-type-name> { .. };

// 早期的编译器可能只能识别早期的格式，这种格式不包括前缀template<>:
class Classname<specialized-type-name> { ... };

// 要使用新的表示法提供一个专供const char*类型使用的SortedArray模板，可以使用类似下面的代码：
template <> 
class SortedArray<const char*>
{
	...  // detauks omitted
}

// 其中的实现代码将使用strcmp()(而不是 ‘>’ )来比较数组值。现在，当请求const char* 类型的SortedArray模板是，编译器将使用上述专门的定义，而不是通用定义
SortedArray<int> scores;  // use general defination
SortedArray<const char *> dates;  // use specialized definition
```
- [x] 代码内还有些疑问。ChatGPT例子：
```C++
// 提问：给出一个简单的显式具体化的例子
#include <iostream>
#include <string>

// 通用模板
template <typename T>
class Printer {
public:
    void print(const T& value) {
        std::cout << "Value: " << value << std::endl;
    }
};

// 针对std::string类型的显式具体化
template <>
class Printer<std::string> {
public:
    void print(const std::string& value) {
        std::cout << "String value: " << value << std::endl;
    }
};

int main() {
    Printer<int> intPrinter;
    intPrinter.print(42);  // 使用通用模板

    Printer<double> doublePrinter;
    doublePrinter.print(3.14159);  // 使用通用模板

    Printer<std::string> stringPrinter;
    stringPrinter.print("Hello, World!");  // 使用显式具体化的模板

    return 0;
}
```


#### 4.部分具体化
- C++还允许部分具体化（partial specialization），即部分限制模板的通用性。例如，部分具体化可以给类型参数之一指定具体的类型：
```C++
// general template
template <class T1, class T2> 
class Pair { ... };

// specialization with T2 set to int
template <class T1> 
class Pair<T1, int> { ... };
```
- 关键字template后面的<>声明的是没有被具体化的类型参数。因此，上述第二个声明将T2具体化为int，但T1保持不变。注意，如果指定所有的类型，则<>内将为空，这将导致显式具体化：
```C++
// specialization with T1 and T2 set to int
template <> 
class Pair<int, int> { ... };

```
- 如果有多个模板可供选择，编译器将使用具体化程度最高的模板。给定上述三个模板，情况如下：
```C++
Pair<double, double> p1;  // use general Pair template
Pair<double, int> p2;  // use Pair<T1, int> partial specialization
Pair<int, int> p3;  // use Pair<int, int> explicit specialization
```
- [x] 为什么第三个是显示具体化？
	- 具体化程度的优先级总结: 显式具体化（最具体化） > 部分具体化 > 通用模板（最不具体化）
	- C++ 编译器会根据模板的具体化程度来选择合适的模板。**具体化程度指的是模板参数中具体类型的数量和程度**。模板越具体，它的优先级就越高。在例子中，`Pair<int, int>` 比 Pair`<T1, int`> 更具体，因此编译器会优先使用显式具体化的模板。

- 也可以通过为指针提供特殊版本来部分具体化现有的模板：
```C++
template<class T>  // general version
class Feeb { ... };

template<class T*>  // pointer partial specialization 
class Feeb { ... };  // modified code
```

- 如何提供的类型不是指针，则编译器将使用通用版本；如果提供的是指针，则编译器将使用指针具体化版本：
```C++
Feeb<char> fb1;  // use general Feeb template, T is char
Feeb<char*> fb2;  // use Feeb T* specialization, T is char
```

- 如果没有进行部分具体化，则第二个声明将使用通用模板，将T转换为char* 类型。如果进行了部分具体化，则第二个声明将使用具体化模板，将T转换为char。
- 部分具体化特效是的能够设置各种显示。例如，可以这样做：
```C++
// general template
template <class T1, class T2, class T3> class Trio{ ... };

// specialization with T3 set to T2
template <class T1, class T2> class Trio<T1, T2, T3> { ... };

// specialization with T3 and T2 set to T1*
template <class T1> class Trio<T1, T1*, T1*> { ... };
```

- 给定上述声明，编译器将作出如下选择：
```C++
Trio<int, short, char*> t1;  // use general template
Trio<int, short> t2;  // use Trio<T1, T2, T2>
Trio<char, char*, char*> t3;  // use Trio<T1, T1*, T1*>
```
- ChatGPT给出的代码实例：
```C++
#include <iostream>
#include <string>

// 通用模板类
template <typename T1, typename T2>
class Pair {
public:
    Pair(const T1& first, const T2& second) : first_(first), second_(second) {}

    void print() const {
        std::cout << "First: " << first_ << ", Second: " << second_ << std::endl;
    }

private:
    T1 first_;
    T2 second_;
};

// 针对第二个模板参数为int的部分具体化
template <typename T1>
class Pair<T1, int> {
public:
    Pair(const T1& first, int second) : first_(first), second_(second) {}

    void print() const {
        std::cout << "First: " << first_ << ", Second (int): " << second_ << std::endl;
    }

private:
    T1 first_;
    int second_;
};

// 针对两个参数均为同一类型的部分具体化
template <typename T>
class Pair<T, T> {
public:
    Pair(const T& first, const T& second) : first_(first), second_(second) {}

    void print() const {
        std::cout << "First (same type): " << first_ << ", Second (same type): " << second_ << std::endl;
    }

private:
    T first_;
    T second_;
};

int main() {
    Pair<double, int> pair1(3.14, 42);
    pair1.print();  // 使用部分具体化：第二个模板参数为int

    Pair<int, int> pair2(7, 42);
    pair2.print();  // 使用部分具体化：两个参数均为同一类型

    Pair<std::string, double> pair3("Pi", 3.14);
    pair3.print();  // 使用通用模板

    return 0;
}

```

### 14.4.7 成员模板
- 模板可用作结构、类或模板类的成员。要完全实现STL的设计，必须使用这项特性。程序清单 14.20 是一个简短的模板类示例，该模板类将另一个模板类和模板函数作为其成员
```C++
// 14.20 tempmemb.cpp  -- template members
#include <iostream>
using std::cout;
using std::endl;

template <typename T>
class beta
{
private:
	template <typename V>  // nested template class member
	class hold
	{
	private:
		V val;
	public:
		hold(V v = 0) : val(v) {}
		void show() const { cout << val << endl; }
		V Value() const { return val; }
		
	};

	hold<T> q;  // template object
	hold<int> n;  // template object

public:
	beta(T t, int i) : q(t), n(i) {}

	template<typename U>  // template method
	U blab(U u, T t) { return (n.Value() + q.Value()) * u / t; }

	void Show() const { q.show(); n.show(); }
};

int main()
{
	beta<double> guy(3.5, 3);
	cout << "T was set to double\n";
	guy.Show();
	cout << "V was set to T, which is double, then V was set to int\n";
	cout << guy.blab(10, 2.3) << endl;
	cout << "U was set to int\n";
	cout << guy.blab(10.0, 2.3) << endl;
	cout << "U was set to double\n";
	cout << "Done\n";
	return 0;
}
```
- ChatGTP: 在 C++ 中，当调用模板类的模板方法时，如果编译器能够从参数推导出模板参数的类型，则不需要显式地为模板方法指定模板参数。换句话说，编译器可以自动推导出模板参数类型，因此可以省略尖括号 <> 中的显式类型指定。

- 如果将嵌套的模板在类声明外面定义，则代码如下：
```C++
template <typename T>
class beta
{
private:
	template <typename V>  // declaration
	class hold;
	hold<T> q;
	hold<int> n;

public:
	beta(T t, int i) : q(t), n(i) {}
	template<typename U>  // declaration
	U blab(U u, T t);
	void Show() const { q.show(); n.show(); }
};

// member definition
template <typname T>
	template<typename V>
	class beta<T>::hold
	{
	private:
		V val;
	public:
		hold(V v = 0) : val(v) {}
		void show() const { cout << val << endl; }
		V Value() const { return val; }
	};
	

// member definition
template <typname T>
	template<typename U>
	U beta<T>::blab(U u, T t)
	{
		return (n.Value() + q.Value()) * u / t;
	}
```

- 因为模板是嵌套的，必须使用下面的语法：
```C++
template <typname T>
	template<typename V>
```
- 而不能使用下面的语法
```C++
template <typname T, typename V>
```
- 定义还必须指出hold和blab是beta<T>类的成员，这是通过使用作用域解析运算符来完成的

### 14.4.8 将模板用作参数
- 模板可以包含类型参数（typename T）和非类型参数（如 int n）
- 模板还可以包含本身就是模板的参数，这种参数是模板新增的特性，用于实现STL
```C++
// template <typename T> class 是类型，Thing 是参数
template <template <typename T> class Thing>
```

- ```Thing<int>``` 被实例化为 ```Stack<int>```
- ```Thing<double>``` 被实例化为 ```Stack<double>```
- Crab类可以使用任何与Thing类型声明匹配的，并包含push()和pop()方法的模板类

```C++
// tempparm.cpp
// 14.21 tempparm.cpp -- templates as parameters
#include <iostream>
#include "stacktp.h"

template <template <typename T> class Thing>
class Crab
{
private:
	Thing<int> s1;
	Thing<double> s2;

public:
	Crab() {};
	// assumes the Thing class has push() and pop() members
	bool push(int a, double x) { return s1.push(a) && s2.push(x); }
	bool pop(int& a, double& x) { return s1.pop(a) && s2.pop(x); }
};

int main()
{
	using std::cout;
	using std::cin;
	using std::endl;
	Crab<Stack> nebula;

	// Stack must match template <typename T> class thing
	int ni;
	double nb;
	cout << "Enter int double pairs, such as 4, 3.5 (0 0 to end):\n";

	while (cin >> ni >> nb && ni > 0 && nb > 0)
	{
		if (!nebula.push(ni, nb))
		{
			break;
		}
	}

	while (nebula.pop(ni, nb))
	{
		cout << ni << ", " << nb << endl;
	}

	cout << "Done.\n";
	return 0;
}
```
- 可以混合使用模板参数和常规参数，例如，Crab类的声明可以像下面这样打头：
```C++
template <template <typename T> class Thing, typename U, typename V>
class Crab
{
private:
	Thing<U> s1;
	Thing<V> s2;
	...
}

// 现在，成员s1和s2可存储的数据类型为泛型，而不是用硬编码指定的类型。这要求将程序中nebula的声明改成下面这样：
Crab<stack, int, double> nebula;  // T=Stack, U=int, V=double
// 模板参数T表示一种模板类型，而类型参数U和V表示非模板类型。
```

### 14.4.9 模板类和友元
- 模板类声明也可以有友元。模板的友元分3类：
	- 非模板友元
	- 约束（bound）模板友元，即友元的类型取决于类被实例化时的类型；
	- 非约束（unbound）模板友元，即友元的所有具体化都是类的每一个具体化的友元

#### 1.模板类的非模板友元函数
**不提供模板类参数：**
```C++
template <class T>
class HasFriend
{
	friend void counts();  // firend to all HasFriend instantiations
	...
}
```
- 上述声明使`counts()`函数成为模板所有实例化的友元。例如，它将是类`HasFriend<int>`和`HasFriend<string>`的友元
- `count()`函数不通过对象调用（它是友元，不是成员函数），也没有对象参数，那么它如何访问`HasFriend`对象呢？有很多种可能性。它可以访问全局对象；可以使用全局指针访问非全局对象；可以创建自己的对象；可以访问独立于对象的模板类的静态数据成员。

**为友元类函数提供模板类参数：**
- 假设要为友元函数提供模板类参数，可以如下所示来进行友元声明吗？
```C++
friend void report(HasFriend&);  // possible?
```
- 答案是不可以。原因是不存在HasFriend这样的对象，而只有特定的具体化。
```C++
template <class T>
class HasFriend
{
	friend void report(HasFriend<T>&);  // bound template friend
	...
}
```
- 这样的话。HasFriend\<int>参数的report将成为HasFriend\<int>的友元。同样，HasFriend\<double>参数的report将成为HasFriend\<double>的友元。
- 注意，report()本身并不是模板函数，而只是使用一个模板作为参数。这意味着必须要为使用的友元定义显示具体化。
```C++
void report(HasFriend<short>&) { ... };  // explicit specialization for short
void report(HasFriend<int>&) { ... };  // explicit specialization for int
```

```C++
// 14.22 frnd2tmp.cpp
#include <iostream>
using std::cout;
using std::endl;

template <typename T>
class HasFriend
{
private:
	T item;
	static int ct;
public:
	HasFriend(const T& i) : item(i) { ct++; }
	~HasFriend() { ct--; }
	friend void counts();
	friend void report(HasFriend<T>&);  // template parameter
};

// each specialization has its own static data member
template <typename T>
int HasFriend<T>::ct = 0;

// non-template friend to all HasFriend<T> classes
void counts()
{
	cout << "int count: " << HasFriend<int>::ct << "; ";
	cout << "double count: " << HasFriend<double>::ct << endl;
}

// non-template friend to all HasFriend<T> classes
void report(HasFriend<int>& hf)
{
	cout << "HasFriend<int>: " << hf.item << endl;
}

// 不是模板函数，只是将模板作为参数，所以不同的情况要单独写
void report(HasFriend<double>& hf)
{
	cout << "HasFriend<double>: " << hf.item << endl;
}

int main()
{
	cout << "No objects declared: ";
	counts();

	HasFriend<int> hfi1(10);
	cout << "After hfi1 declared: ";
	counts();
	
	HasFriend<int> hfi2(20);
	cout << "After hfi2 declared: ";
	counts();

	HasFriend<double> hfdb(10.5);
	cout << "After hfdb declared: ";
	counts();

	report(hfi1);
	report(hfi2);
	report(hfdb);

	return 0;
}
```

#### 2.模板类的约束模板友元函数
- 修改前一个示例，使友元函数本身成为模板。具体地说，为约束模板友元作准备，使类的每一个具体化都获得与友元匹配的具体化。这比非模板友元复杂些，包含以下3步。
- 首先，在类定义的前面声明每个模板函数。
- 然后，在函数中再次将友元声明为模板。这些语句根据类模板参数的类型声明具体化。
	- 声明中的<>指出这是模板具体化。对于report()，<>可以为空，因为可以从函数参数推断如下模板类型参数
	```C++
	HasFriendT<TT>
	// 然而也可以使用：
	report< HasFriendT<TT>> >(HasFriendT<TT>&)
	```
	- 但`counts()`函数没有参数，因此必须使用模板参数语法`(<TT>)`来指明具体化。还需要注意的是，TT是`HasFriend`类的类型参数。
- 程序必须满足的第三个要求是：为友元提供模板定义。
	- 因为count()函数调用没有可被编译器用来推断出所需具体换的函数参数，所以这些调用使用`count<int>()`和`count<double>()`指明具体化
	- 对于`report()`调用，编译器可以从参数类型推断出要是用的具体化。使用<>格式可以达到同样的效果
	```C++
	report< HasFriendT<int> >(hfi2);  // same as report(hfi2);
	```

```C++
// 14.22 frnd2tmp.cpp -- template friends to a template class
#include <iostream>
using std::cout;
using std::endl;

// template prototypes
template <typename T> 
void counts();
template <typename T> 
void report(T&);

template <typename TT>
class HasFriend
{
private:
	TT item;
	static int ct;
public:
	HasFriend(const TT& i) : item(i) { ct++; }
	~HasFriend() { ct--; }
	friend void counts<TT>();
	friend void report<>(HasFriend<TT>&);  // template parameter
};

// each specialization has its own static data member
template <typename T>
int HasFriend<T>::ct = 0;

// template friend functions definitions
template <typename T>
void counts()
{
	cout << "template size: " << sizeof(HasFriend<T>::ct) << "; ";
	cout << "template counts(): " << HasFriend<T>::ct << endl;
}

template <typename T>
void report(T& hf)
{
	cout << hf.item << endl;
}

int main()
{
	counts<int>();
	HasFriend<int> hfi1(10);
	HasFriend<int> hfi2(20);
	HasFriend<double> hfdb(10.5);

	report(hfi1);  // generate report (HasFriendT<int> &)
	report(hfi2);  // generate report (HasFriendT<int> &)
	report(hfdb);  // generate report (HasFriendT<double> &)

	cout << "counts<int>() output:\n";
	counts<int>();
	
	cout << "counts<double>() output:\n";
	counts<double>();

	return 0;
}
```

#### 3.模板类的非约束模板友元函数
```C++
// 14.24 manyfrnd.cpp -- unbound template friend to a template class
#include <iostream>
using std::cout;
using std::endl;

template <typename T>
class ManyFriend
{
private:
	T item;
public:
	ManyFriend(const T& i) : item(i) {}
	template <typename C, typename D> friend void show2(C&, D&);
};

template <typename C, typename D> void show2(C& c, D& d)
{
	cout << c.item << ", " << d.item << endl;
}

int main()
{
	ManyFriend<int> hfi1(10);
	ManyFriend<int> hfi2(20);
	ManyFriend<double> hfdb(10.5);
	cout << "hfi1, hfi2: ";
	show2(hfi1, hfi2);
	cout << "hfdb, hfi2: ";
	show2(hfdb, hfi2);

	return 0;
}
```
- 函数调用`show2(hfi1, hfi2)`与下面的具体化匹配
```C++
void show2<ManyFriend<int>&, ManyFriend<int>& >
	(ManyFriend<int>& c, ManyFriend<int>& d)
```
- 函数调用`show2(hfdb, hfi2)`与下面的具体化匹配
```C++
void show2<ManyFriend<double>&, ManyFriend<int>& >
	(ManyFriend<double>& c, ManyFriend<int>& d)
```

### 14.4.10 模板别名（C++11）
- C++11新增了一项功能——使用模板提供一些列别名，如下所示：
```C++
// 将arrtype定义为模板别名
template<typename T>
	using arrtype = std::array<T, 12>;  // template to create multiple aliases
// 用arrtype来指定类型
arrtype<double> gallons;  // gallons is type std::array<double, 12>
arrtype<int> days;  // days is type std::array<int, 12>
arrtype<std::string> months;  // months is type std::array<std:string, 12>
// 总之， arrtype<T>表示类型std::array<T, 12>
```
- C++11允许将语法using=用于非模板。用于模板时，这种语法与常规typedef等价
```C++
typedef const char* pc1;  // typedef syntax
using pc2 = const char*;  // using = syntax
typedef const int *(*pa1)[10];  // typedef syntax
using pa2 = const int *(*)[10];  // using = syntax
```
- 习惯这种语法后，您可能发现其可读性更强，因为它让类型名和类型信息更清晰。
- C++可变参数模板(variadic template)，将在第18章介绍

## 14.5 总结
- 代码重用主要就是 **继承** 和 **模版**（类模板+函数模板）。
## 14.6 复习题

## 14.7 编程题
