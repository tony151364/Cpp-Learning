### 第11章 使用类
- 不要害怕犯错
- 轻松地使用这门语言

### 11.1 运算符重载
- 要重载运算符，需使用被称为运算符函数的特殊函数形式。运算符的函数格式如下
```C++
operator op(argument-list)
```
## 11.2 计算时间：一个运算符重载的示例
```C++
// 11.1 mytime0.h -- Time class before operator overloading
#ifndef MYTIME0_H_
#define MYTIME0_H_

class Time
{
private:
	int hours;
	int minutes;
public:
	Time();
	Time(int h, int m = 0);
	void AddMin(int m);
	void AddHr(int h);
	void Reset(int h = 0, int m = 0);
	Time Sum(const Time& t) const;
	void Show() const;
};

#endif // !MYTIME0_H_

```

```C++
// 11.2 mytime0.cpp -- implementing Time methods
#include <iostream>
#include "mytime0.h"

Time::Time()
{
	hours = minutes = 0;
}

Time::Time(int h, int m)
{
	hours = h;
	minutes = m;
}

void Time::AddMin(int m)
{
	minutes += m;
	hours += minutes / 60;
	minutes %= 60;
}

void Time::AddHr(int h)
{
	hours += h;
}

void Time::Reset(int h, int m)
{
	hours = h;
	minutes = m;
}

Time Time::Sum(const Time& t) const
{
	Time sum;
	sum.minutes = minutes + t.minutes;
	sum.hours = hours + t.hours + sum.minutes / 60;
	sum.minutes %= 60;
	return sum;
}

void Time::Show() const
{
	std::cout << hours << " hours, " << minutes << " minutes";
}
```

```C++
// 11.3 usetime0.cpp -- using the first draft of the Time class
// compile usetime0.cpp and mytime0.cpp together
#include <iostream>
#include "mytime0.h"

int main()
{
	using std::cout;
	using std::endl;

	Time planning;
	Time coding(2, 40);
	Time fixing(5, 55);
	Time total;

	cout << "planning time = ";
	planning.Show();
	cout << endl;

	cout << "coding time = ";
	coding.Show();
	cout << endl;

	cout << "fixing time = ";
	fixing.Show();
	cout << endl;

	total = coding.Sum(fixing);
	cout << "coding.Sum(fixing) = ";
	total.Show();
	cout << endl;

	return 0;
}
```
## 11.1.1 添加加法运算符
```C++
// 11.5 mytime1.h -- Time class before operator overloading
#ifndef MYTIME1_H_
#define MYTIME1_H_

class Time
{
private:
	int hours;
	int minutes;
public:
	Time();
	Time(int h, int m = 0);
	void AddMin(int m);
	void AddHr(int h);
	void Reset(int h = 0, int m = 0);
	Time operator+(const Time& t) const;
	void Show() const;
};

#endif // !MYTIME0_H_

```

```C++
// 11.6 mytime1.cpp -- implementing Time methods
#include <iostream>
#include "mytime1.h"

Time::Time()
{
	hours = minutes = 0;
}

Time::Time(int h, int m)
{
	hours = h;
	minutes = m;
}

void Time::AddMin(int m)
{
	minutes += m;
	hours += minutes / 60;
	minutes %= 60;
}

void Time::AddHr(int h)
{
	hours += h;
}

void Time::Reset(int h, int m)
{
	hours = h;
	minutes = m;
}

Time Time::operator+(const Time& t) const
{
	Time sum;
	sum.minutes = minutes + t.minutes;
	sum.hours = hours + t.hours + sum.minutes / 60;
	sum.minutes %= 60;
	return sum;
}

void Time::Show() const
{
	std::cout << hours << " hours, " << minutes << " minutes";
}
```
```C++
// 11.4 usetime1.cpp -- using the second draft of the Time class
// compile usetime1.cpp and mytime1.cpp together
#include <iostream>
#include "mytime1.h"

int main()
{
	using std::cout;
	using std::endl;

	Time planning;
	Time coding(2, 40);
	Time fixing(5, 55);
	Time total;

	cout << "planning time = ";
	planning.Show();
	cout << endl;

	cout << "coding time = ";
	coding.Show();
	cout << endl;

	cout << "fixing time = ";
	fixing.Show();
	cout << endl;

	total = coding + fixing;
	cout << "coding + fixing = ";
	total.Show();
	cout << endl;

	Time morefixing(3, 28);
	cout << "more fixing time = ";
	morefixing.Show();
	cout << endl;
	total = morefixing.operator+(total);
	cout << "morefixing.operator+(total) = ";
	total.Show();
	cout << endl;

	return 0;
}
```

### 11.2.2 重载限制
1. 用非成员函数进行重载
```C++
#include <iostream>
using namespace std;

class Num
{
public:
	int n;
public:
	Num();
	Num(int m);
	Num operator+(const Num& t) const;
	void Show() const;
};

Num::Num()
{
	n = 0;
}

Num::Num(int m)
{
	n = m;
}

Num Num::operator+(const Num& t) const
{
	Num temp;
	temp = n + t.n;

	return temp;
}

void Num::Show() const
{
	cout << "n = " << n << endl;
}

// 非成员函数实现运算符重载
Num operator-(const Num &m, const Num& l)
{
	Num temp;
	temp.n = m.n - l.n;
	return temp;
}

int main()
{
	Num a(10);
	a.Show();
	
	Num b(20);
	b.Show();

	Num c;
	c = a + b;
	c.Show();

	c = a - b;
	c.Show();
}
```
- 在类的定义中，如果没有对赋值运算符重载，当代码中出现将整数或者浮点数赋值给对象时，程序会调用与赋值语句右侧类型相关的构造函数
```C++
Num a;
a = 10; // a(10)
```
2. 为什么这四个运算符只能用成员函数进行重载
- 当非成员函数和构造函数都可以实现成员运算符的重载（如非成员重载赋值和构造函数），编译器编译时就会报错，不知道如何选择了。而其他的一些运算符，如+、-、都不会调用程序的构造函数。所以这四个运算符只能用成员函数进行重载。

### 11.2.3 其他重载运算符
```C++
// 11.6 mytime2.h -- Time class before operator overloading
#ifndef MYTIME2_H_
#define MYTIME2_H_

class Time
{
private:
	int hours;
	int minutes;
public:
	Time();
	Time(int h, int m = 0);
	void AddMin(int m);
	void AddHr(int h);
	void Reset(int h = 0, int m = 0);
	Time operator+(const Time& t) const;
	Time operator-(const Time& t) const;
	Time operator*(double n) const;
	void Show() const;
};
#endif // !MYTIME0_H_

```

```C++
// 11.8 mytime2.cpp -- implementing Time methods
#include <iostream>
#include "mytime2.h"

Time::Time()
{
	hours = minutes = 0;
}

Time::Time(int h, int m)
{
	hours = h;
	minutes = m;
}

void Time::AddMin(int m)
{
	minutes += m;
	hours += minutes / 60;
	minutes %= 60;
}

void Time::AddHr(int h)
{
	hours += h;
}

void Time::Reset(int h, int m)
{
	hours = h;
	minutes = m;
}

Time Time::operator+(const Time& t) const
{
	Time sum;
	sum.minutes = minutes + t.minutes;
	sum.hours = hours + t.hours + sum.minutes / 60;
	sum.minutes %= 60;
	return sum;
}
 
Time Time::operator- (const Time & t) const
{
	Time diff;
	int tot1, tot2;
	tot1 = t.minutes + 60 * t.hours;
	tot2 = minutes + 60 * hours;
	diff.minutes = (tot2 - tot1) % 60;
	diff.hours = (tot2 - tot1) / 60;
	return diff;
}

Time Time::operator* (double mult) const
{
	Time result;
	long totalminutes = hours * mult * 60 + minutes * mult;
	result.minutes = totalminutes % 60;
	result.hours = totalminutes / 60;
	return result;
}
void Time::Show() const
{
	std::cout << hours << " hours, " << minutes << " minutes";
}
```

```C++
// 11.9 usetime2.cpp -- using the third draft of the Time class
// compile usetime1.cpp and mytime1.cpp together
#include <iostream>
#include "mytime2.h"

int main()
{
	using std::cout;
	using std::endl;

	Time weeding(4, 35);
	Time waxing(2, 47);
	Time total;
	Time diff;
	Time adjusted;

	cout << "weeding time = ";
	weeding.Show();
	cout << endl;

	cout << "waxing time = ";
	waxing.Show();
	cout << endl;

	cout << "total work time = ";
	total = weeding + waxing;  // use operator+()
	total.Show();
	cout << endl;

	diff = weeding - waxing;
	cout << "weeding time - waxing time = ";
	diff.Show();
	cout << endl;

	adjusted = total * 1.5;  // use operator*()
	cout << "adjusted work time = ";
	adjusted.Show();
	cout << endl;
	return 0;
}
```

## 11.3 友元
- 友元有三种：
	- 友元函数
	- 友元类
	- 友元成员函数
- 大多数运算符都可以通过成员或非成员函数来重载
- 友元可以访问类的私有成员

### 11.3.1 创建友元
- 第一步：在函数原型前加关键字friend
- 第二步：编写函数定义
- 总之，类的友元函数是非成员函数，其访问权限与成员函数相同

- **友元函数是否有悖于OOP**
	- 类方法和友元函数只是表达类接口的两种不同机制

- **提示**： 如果要为类重载运算符，并将非类的项作为其第一个操作符，则可使用友元函数来反转操作符的顺序。

### 11.3.2 常用的友元：重载<<运算符

#### 1.<<的第一种重载版本
```C++
void operator << (ostream& os, const Time& t)
{
	os << t.hours << " hours, " << t.minutes << " minutes;
}
```
#### 2.第二种版本

```C++
// 11.10 mytime3.h -- Time class with friends
#ifndef MYTIME3_H_
#define MYTIME3_H_
#include <iostream>

class Time
{
private:
	int hours;
	int minutes;
public:
	Time();
	Time(int h, int m = 0);
	void AddMin(int m);
	void AddHr(int h);
	void Reset(int h = 0, int m = 0);
	Time operator+(const Time& t) const;
	Time operator-(const Time& t) const;
	Time operator*(double n) const;
	friend Time operator*(double m, const Time& t)
	{ return t * m; }  // 调用了上面的函数
	friend std::ostream& operator<<(std::ostream& os, const Time& t);
};
#endif
```

```C++
// 11.11 mytime2.cpp -- implementing Time methods
#include "mytime3.h"

Time::Time()
{
	hours = minutes = 0;
}

Time::Time(int h, int m)
{
	hours = h;
	minutes = m;
}

void Time::AddMin(int m)
{
	minutes += m;
	hours += minutes / 60;
	minutes %= 60;
}

void Time::AddHr(int h)
{
	hours += h;
}

void Time::Reset(int h, int m)
{
	hours = h;
	minutes = m;
}

Time Time::operator+(const Time& t) const
{
	Time sum;
	sum.minutes = minutes + t.minutes;
	sum.hours = hours + t.hours + sum.minutes / 60;
	sum.minutes %= 60;
	return sum;
}
 
Time Time::operator- (const Time & t) const
{
	Time diff;
	int tot1, tot2;
	tot1 = t.minutes + 60 * t.hours;
	tot2 = minutes + 60 * hours;
	diff.minutes = (tot2 - tot1) % 60;
	diff.hours = (tot2 - tot1) / 60;
	return diff;
}

Time Time::operator* (double mult) const
{
	Time result;
	long totalminutes = hours * mult * 60 + minutes * mult;
	result.minutes = totalminutes % 60;
	result.hours = totalminutes / 60;
	return result;
}
std::ostream& operator<<(std::ostream& os, const Time& t)
{
	os << t.hours << " hours, " << t.minutes << " minutes";
	return os;
}
```


```C++
// 11.12 usetime3.cpp -- using the fourth draft of the Time class
// compile usetime3.cpp and mytime3.cpp together
#include <iostream>
#include "mytime3.h"

int main()
{
	using std::cout;
	using std::endl;
	Time aida(3, 35);
	Time tosca(2, 48);
	Time temp;

	cout << "Aida and Tosca:\n";
	cout << aida << "; " << tosca << endl;
	temp = aida + tosca;  // operator+()
	cout << "Aida + Tosca: " << temp << endl;
	temp = aida * 1.17;  // member operator*()
	cout << "Aida * 1.17: " << temp << endl;
	cout << "10.0 * Tosca: " << 10.0 * tosca << endl;
	
	return 0;
}
```


## 11.4 重载运算符：作为成员函数还是非成员函数
```C++
Time operator+(const Time& t) const;  // member version

firend Time operator+(const Time& t1, const Time & t2);
```
- 必须只使用其中的一个格式，否则将会产生二义性，编译器将不能识别。
- 具体用哪个版本，根据具体情况而定。本章后面的“转换和友元”一节将更深入讨论这种情形。

## 11.5 再谈重载：一个矢量类
- 另外设计这个类时将使得用户修改了矢量的一种表示后，对象将自动更新另一种表示。使对象有这种智能，是C++类的另一个优点
```C++
#pragma once
// 11.13 vector.h -- Vector class with << , mode state
#ifndef VECTOR_H_
#define VECTOR_H_

#include <iostream>

namespace VECTOR
{
	class Vector
	{
	public:
		enum Mode { RECT, POL };  // RECT for rectangular, POL for Polar modes

	private:
		double x;
		double y;
		double mag;  // length of vector
		double ang;  // direction of vector in degrees
		Mode mode;  // RECT or POL

	private:
		void set_mag();
		void set_ang();
		void set_x();
		void set_y();

	public:
		Vector();
		Vector(double n1, double n2, Mode form = RECT);
		void reset(double n1, double n2, Mode form = RECT);
		~Vector();
		double xval() const { return x; }  // report x value
		double yval() const { return y; }
		double magval() const { return mag; }
		double angval() const { return ang; }
		void polar_mode();
		void rect_mode();

		// operator overloading
		Vector operator+ (const Vector& b) const;
		Vector operator- (const Vector& b) const;
		Vector operator- () const;
		Vector operator* (double n) const;

		// fiends
		friend Vector operator*(double n, const Vector& a);
		friend std::ostream& operator<<(std::ostream& os, const Vector& v);
	};
}
#endif
```

```C++
// 11.14 vector.cpp -- methods for the Vector class
#include <cmath>
#include "vect.h"  // include <iostream>
using std::sqrt;
using std::sin;
using std::cos;
using std::atan;
using std::atan2;
using std::cout;

namespace VECTOR
{
	// compute degrees in one radian
	const double Rad_to_deg = 45.0 / atan(1.0);
	// should be about 57.234523452

	// private methods
	// calculates magnitude from x and y
	void Vector::set_mag()
	{
		mag = sqrt(x * x + y * y);
	}
	
	void Vector::set_ang()
	{
		if (x == 0.0 && y == 0.0)
			ang = 0.0;
		else
			ang = atan2(y, x);
	}

	void Vector::set_x()
	{
		x = mag * cos(ang);
	}

	void Vector::set_y()
	{
		y = mag * sin(ang);
	}

	// public methods
	Vector::Vector()   // defualt construcot
	{
		x = y = mag = ang = 0.0;
		mode = RECT;
	}

	// construct vector form rectangular coordinates if form is r
	// (the default) or else from polar coordinates if form is p
	Vector::Vector(double n1, double n2, Mode form)
	{
		mode = form;
		if (form == RECT)
		{
			x = n1;
			y = n2;
			set_mag();
			set_ang();
		}
		else if (form == POL)
		{
			mag = n1;
			ang = n2 / Rad_to_deg;
			set_x();
			set_y();
		}
		else
		{
			cout << "Incorrect 3rd argument: to Vecor() -- ";
			cout << "vector set to 0\n";
			x = y = mag = ang = 0.0;
			mode = RECT;
		}

		
	}

	// reset vector from recangluar coodinate if 
	// RECT 
	// form is POL
	void Vector::reset(double n1, double n2, Mode form)
	{
		mode = form;
		if (form == RECT)
		{
			x = n1;
			y = n2;
			set_mag();
			set_ang();
		}
		else if (form == POL)
		{
			mag = n1;
			ang = n2 / Rad_to_deg;
			set_x();
			set_y();
		}
		else
		{
			cout << "Incorrect 3rd argument to Vector() -- ";
			cout << "vector set to 0\n";
			x = y = mag = ang  = 0.0;
			mode = RECT;
		}
	}

	Vector::~Vector()  // destructor
	{

	}

	void Vector::polar_mode()  // set to polar mode
	{
		mode = POL;
	}

	void Vector::rect_mode()  // set to rectangular mode
	{
		mode = RECT;
	}

	// operator overloading
	// add two Vectors
	Vector Vector::operator+(const Vector& b) const
	{
		return Vector(x + b.x, y + b.y);
	}

	// substract Vector b from a
	Vector Vector::operator-(const Vector& b) const
	{
		return Vector(x - b.x, y - b.y);
	}

	// reverse sign of Vector
	Vector Vector::operator-() const
	{
		return Vector(-x, -y);
	}

	Vector Vector::operator*(double n) const
	{
		return Vector(n * x, n * y);
	}

	// friend methods
	// multiply n by Vector a
	Vector operator*(double n, const Vector& a)
	{
		return a * n;
	}

	std::ostream& operator << (std::ostream& os, const Vector& v)
	{
		if (v.mode == Vector::RECT)
			os << "(x, y) = (" << v.x << ", " << v.y << ")";
		else if (v.mode == Vector::POL)
		{
			os << "(m, a) = (" << v.mag << ", "
				<< v.ang * Rad_to_deg << ")";
		}
		else
			os << "Vector object mode is invalid";
		return os;
	}
}  // end namespace VECTOR
```

### 11.5.1 使用状态成员
- mode控制构造函数，reset方法等函数使用那种形式，这样的成员被称为状态函数，因为这种成员描述的是对象所处的状态
- 类非常适于在一个对象中表示实体的不同方面。首先在一个对象中存储多种表示方式；然后，编写这样的类函数，以便给一种表示方式赋值时，将自动给其他方式赋值。

### 11.5.2 为Vector类重载算术运算符
- **提示：** 如果方法通过计算得到一个新的类对象，则应考虑是否可以使用类构造函数来完成这种工作。这样做不仅可以避免麻烦，而且可以确保新的对象是按照正确的方式创建的。

- **注意：** 因为运算符重载是通过函数来实现的，所以只要运算符函数的特征标不同，使用的运算符数量与相应的内置C++运算符相同，就可以多次重载同一个运算符。

### 11.5.3 对实现的说明
- 将接口与实现分离是OOP的目标之一，这样允许对实现进行调整，而无需修改使用这个类的程序中的代码。

### 11.5.4 使用Vector类来模拟随机漫步
```C++
// 11.15 randwalk.cpp -- using the Vector class
#include <iostream>
#include <cstdlib>  // rand(), srand() prototypes
#include <ctime>  // time() prototype
#include "vect.h"
int main()
{
	using namespace std;
	using VECTOR::Vector;
	srand(time(0));  // time(0) 函数返回当前的时间，通常为从某一个日期开始的秒数
	double direction;
	Vector step;
	Vector result(0.0, 0.0);
	unsigned long steps = 0;
	double target;
	double dstept;

	cout << "Enter target distance (q to qiut): ";
	while (cin >> target)
	{
		cout << "Enter step length: ";
		if (!(cin >> dstept))
			break;

		while (result.magval() < target)
		{
			direction = rand() % 360;
			step.reset(dstept, direction, Vector::POL);
			// 这条语句将result设置为RECT模式，而不管result和step的初始模式是什么
			result = result + step;  // 如果偏爱其他方式，例如，result保留原来的模式，可以通过为类定义赋值运算来覆盖默认的复制方式。第12章将介绍这样的示例
			steps++;
		}

		cout << "After " << steps << "steps, the subject "
			"has the following location:\n";
		cout << result << endl;
		result.polar_mode();
		cout << "or\n" << result << endl;
		cout << "Average outward distance per step = "
			<< result.magval() / steps << endl;
		steps = 0;
		result.reset(0.0, 0.0);
		cout << "Enter target distance (q to quit): ";
	}
	cout << "Bye!\n";
	cin.clear();
	while (cin.get() != '\n')
		continue;
	return 0;
}
```
- 因此，如果发现自己在随机漫步时，请保持自信，迈大步走。虽然在蜿蜒前进的过程中仍旧无法控制前进的方向，但至少会走得远一点
- [ ] 随机数种子的原理

## 11.6 类的自动类型转换和强制类型转换
- 将一个标准类型变量的值赋给另一种标准类型的变量时，如果这两种类型兼容，则C++自动将这个值转换为接受变量的类型。
```C++
// 下面的语句都将导致数值类型转换
long count = 8;  // int value 8 converted to type long
double time = 11;  // int value 11 convered to type double
int side = 3.33;  // double value 3.33 convered to type int 3
```


```C++
// 11.16 stonewt.h -- definition for the Stonewt class 
#ifndef STONEWT_H_
#define STONEWT_H_
class Stonewt
{
private:
	enum {Lbs_per_stn = 14};
	int stone;
	double pds_left;
	double pounds;
public:
	Stonewt(double lbs);
	Stonewt(int stn, double lbs);
	Stonewt();
	~Stonewt();
	void show_lbs() const;
	void show_stn() const;
};
#endif
```

```C++
// 11.17 stonewt.cpp -- Stonewt methods
#include <iostream>
using std::cout;
#include "stonewt.h"

Stonewt::Stonewt(double lbs)
{
	stone = int(lbs) / Lbs_per_stn;  // integer division
	pds_left = int(lbs) % Lbs_per_stn + lbs - int(lbs);
	pounds = lbs;
}

Stonewt::Stonewt(int stn, double lbs)
{
	stone = stn;
	pds_left = lbs;
	pounds = stn * Lbs_per_stn + lbs;
}

Stonewt::Stonewt()
{
	stone = pounds = pds_left = 0;
}

Stonewt::~Stonewt()  // destructor
{

}

void Stonewt::show_stn() const
{
	cout << stone << " stone, " << pds_left << " pounds\n";
}

// show weight in pounds
void Stonewt::show_lbs() const
{
	cout << pounds << " pounds\n";
}
```
- C++新增了关键字explicit，用于关闭这种自动转换的特性
```C++
explicit Stonewt(double lbs);  // no implicit conversions allowed

Stonewt myCat;  // create a Stonewt object
myCat = 19.6；  // not vaild if Stonewt(double) is declared as explicit
myCat = Stonewt(19.6);  // ok, an explicit conversion
myCat = (Stonewt) 19.6;  // ok, old form for explicit typecast
```

- **注意：** 只接受一个参数的构造函数定义了从参数类型到类类型的转换。如果使用关键字explicit限定了这种构造函数，则它只能用于显示转换

- 编译器在什么时候使用Stonewt(double)函数呢？如果在声明中使用了关键字explicit，则Stonewt（double）智能用于显示强制类型转换，否则还可以用于下面的隐式转换。
	- 将Stonewt对象初始化为double值时。
	- 将double值赋给Stonewt对象时。
	- 将double值传递给接受Stonewt参数的函数时。
	- 返回值被声明为Stonewt的函数视图返回double值时。
	- 在上述任意一种情况下，使用可转换为double类型的内置类型时。
- 下面详细介绍最后一点。函数原型化提供的参数匹配过程，允许使用Stonewt（double）构造函数来转换其他数值类型。也就是说，下面两条语句都首先将int转换为double，然后使用Stonewt（double）构造函数
```C++
Stonewt Jumbo(7000);  // uses Stonewt(double), converting int to double
Jumbo = 7300;  // uses Stonewt(double), converting int to double
```

```C++
// 11.18 stone.cpp -- user-defined conversions
// compile with stonewt.cpp
#include <iostream>
using std::cout;
#include "stonewt.h"
void display(const Stonewt& st, int n);

int main()
{
	Stonewt incognito = 275;  // uses constructor to initialize
	Stonewt wolfe(285.7);  // same as Stonewt wolfe = 285.7;
	Stonewt taft(21, 8);

	cout << "The celebrity weighed ";
	incognito.show_stn();
	cout << "The detective weighted ";
	wolfe.show_stn();
	cout << "The President weighted ";

	taft.show_lbs();
	incognito = 276.8;  // uses constructor for conversion
	taft = 325;  // same as taft = Stonewt(325);
	cout << "After dinner, the President weighted ";
	incognito.show_stn();
	cout << "After dinner, the President wieghted ";
	taft.show_lbs();
	display(taft, 2);
	cout << "The wrestler weighted even more.\n";
	display(422, 2);
	cout << "No stone left unearned\n";
	return 0;
}

void display(const Stonewt& st, int n)
{
	for (int i = 0; i < n; i++)
	{
		cout << "Wow! ";
		st.show_stn();
	}
}
```
### 11.6.1 转换函数
```C++
// 11.19 stonewt1.h -- revised definition for the Stonewt class 
#ifndef STONEWT_H_
#define STONEWT_H_
class Stonewt
{
private:
	enum { Lbs_per_stn = 14 };
	int stone;
	double pds_left;
	double pounds;
public:
	Stonewt(double lbs);
	Stonewt(int stn, double lbs);
	Stonewt();
	~Stonewt();
	void show_lbs() const;
	void show_stn() const;

	// conversion function
	operator int() const;
	operator double() const;
};
#endif
```

```C++
// 11.20 stonewt.cpp -- Stonewt class methods + conversion functions
#include <iostream>
using std::cout;
#include "stonewt1.h"

Stonewt::Stonewt(double lbs)
{
	stone = int(lbs) / Lbs_per_stn;  // integer division
	pds_left = int(lbs) % Lbs_per_stn + lbs - int(lbs);
	pounds = lbs;
}

Stonewt::Stonewt(int stn, double lbs)
{
	stone = stn;
	pds_left = lbs;
	pounds = stn * Lbs_per_stn + lbs;
}

Stonewt::Stonewt()
{
	stone = pounds = pds_left = 0;
}

Stonewt::~Stonewt()  // destructor
{

}

void Stonewt::show_stn() const
{
	cout << stone << " stone, " << pds_left << " pounds\n";
}

// show weight in pounds
void Stonewt::show_lbs() const
{
	cout << pounds << " pounds\n";
}

Stonewt::operator int() const
{
	return int(pounds + 0.5);
}

Stonewt::operator double() const
{
	return pounds;
}
```

```C++
// 11.21 stone.cpp -- user-defined conversions function
// compile with stonewt1.cpp
#include <iostream>
#include "stonewt1.h"

int main()
{
	using std::cout;

	Stonewt poppins(9, 2.8);
	double p_wt = poppins;

	cout << "Convert to double => ";
	cout << "Poppins: " << p_wt << " pounds.\n";
	cout << "Convert to int => ";
	cout << "Poppins: " << int(poppins) << " pounds.\n";
	return 0;
}
```
- 声明explicit关闭掉运算符的转换。有了这些声明后，需要强制转换时将调用这些运算符。
```C++
 explicit operator int() const;
 explicit operator double() const;
```

- 总之C++提供下面的类型转换
	- int等类型赋给Stonewt对象是调用类的构造函数完成转换，添加explicit可以防止隐式类型转换
	- 对象转换成**其他类型(除了基本数据类型，其他类的类型也可以）** 时使用**转换函数**，添加explicit可以防止隐式类型转换

### 11.6.2 转换函数和友元函数
- 在讨论Time类时指出过，可以使用成员函数或友元函数来重载加法。
```C++
// 下面为Stonewt类重载加法运算符
Stonewt Stonewt::operator+ (const Stonewt& st) const
{
	double pds = pounds + st.pounds;
	Stonewt sum(pds);
	return sum;
}
```

- 也可以将加法作为友元函数来实现，如下所示
```C++
Stonewt operator+(const Stonewt& st1, const Stonewt& st2)
{
	double pds = st1.pounds + st2.pounds;
	Stonewt sum(pds);
	return sum;
}
```

- 别忘了，可以提供方法定义或友元函数定义，但不能都提供。
```C++
// 上面任何一种格式都允许这样做
Stonewt jennySt(9, 12);
Stonewt bennySt(12, 8);
Stonewt total;
total = jennySt + bennySt;

// 如果提供了Stonewt(double) 构造函数，则也可以这样做
Stonewt JennySt（9, 12);
double kennyD = 176.0;
Stonewt total;
total = jennySt + kennyD;

// 但只有友元函数才允许这样做
Stonewt JennySt（9， 12);
double pennyD = 146.0;
Stonewt total;
total = pennyD + jennySt;
// 为了解其中的原因，将每一种加法都转换为相应的函数调用。
total = jennySt + bennySt;
// 被转换为：
total = jennySt + bennySt;  // member function
// 或
total = operator+(jennySt, bennySt);  // friend function
```

## 11.8 复习题
- 1
```C++
Stonewt operator*(double value) const;

Stonewt Stonewt::operator*(double n) const
{
	int tempStone = n * stone + int(n * pds_left) / Lbs_per_stn;
	int temp_pds_left = int(n * pds_left) % Lbs_per_stn + n * pds_left - int(n * pds_left);
	return Stonewt(tempStone, temp_pds_left);
}
```
```C++
// 课后题答案
Stonewt operator*(double mult) const;

Stonewt Stonewt::operator*(double mult) const
{
	return Stonewt(mult * pounds);
}
// 服啦，半天原来这么简单，不用做一堆复杂的转换。直接用第一个就好了。当时也是犯困状态不好。
```
- 2 
	- 我的答案： 友元函数不属于类的成员函数，但是与成员函数有相同的访问权限。友元函数在声明时需要添加friend关键字，且声明在该类中。成员函数则是类内部的函数。
	- 课后答案：成员函数是类定义的一部分，通过特定的对象来调用。成员函数可以隐式访问调用对象的成员，而无需使用成员运算符。友元函数不是类的组成部分，因此被称为直接函数调用。**友元函数不能隐式访问类成员，而必须将成员运算符用于作为参数传递的对象。**
- 3 
	- 我的答案：不一定。对于公有属性可以直接访问，对于私有属性可以用类内部提供的get和set方法访问。
	- 课后答案：要访问私有成员，它必须是友元，但要访问公有成员，可以不是友元。
	- chatgtp的回答：
- 4
```C++
friend Stonewt operator*(double n, const Stonewt& stone);

Stonewt operator*(double n, const Stonewt& stone)
{
	return stone * n;
}
```
```C++
// 课后答案
friend Stonewt operator*(double mult, const Stonewt& s);

Stonewt operator*(double mult, const Stonewt& s)
{
	return Stonewt(mult * s.pounds);
}
```
- 5 
	- 我的答案：p387页 （错了，这几个是只能用成员函数重载，而不是不能重载）
		- = ：赋值运算符
		- ()：函数调用运算符
		- \[]：下标运算符
		- ->：只用访问类成员的运算符 
	- 课后答案：下面的5个运算符不能重载。（因为它们的重载会导致语义混乱、歧义和不一致性）
		- sizeof
		- .
		- *
		- ::
		- ?
- 6 
	- 我的答案：忘记了
	- 课后答案：这些运算符必须使用成员函数定义
- 7
```C++
operator double() const;

Vector::operator double() const
{
	return sqrt(x * x + y * y);
}
```
```C++
// 课后答案
// prototype and inline definition
operator double() {return mag;}
// 服啦，我没发现这个属性
```

## 11.9 编程练习
- 1
```C++
// 11.p01 randwalk.cpp -- save to file
#include <iostream>
#include <cstdlib>  // rand(), srand() prototypes
#include <ctime>
#include <fstream>
#include "vect.h"
int main()
{
	using namespace std;
	using VECTOR::Vector;
	srand(time(0));  // time(0) 函数返回当前的时间，通常为从某一个日期开始的秒数
	double direction;
	Vector step;
	Vector result(0.0, 0.0);
	unsigned long steps = 0;
	double target;
	double dstept;

	int index;
	ofstream outFile;
	outFile.open("randwalk.txt"); 
	
	if (!outFile.is_open())
	{
		cout << "Could not open the file " << endl;
		cout << "Program terminating.\n";
		exit(EXIT_FAILURE);  // EXIT_FAILURE:用于同操作系统通信的参数值
	}

	cout << "Enter target distance (q to qiut): ";
	while (cin >> target)
	{
		index = 0;

		cout << "Enter step length: ";
		if (!(cin >> dstept))
			break;

		outFile << "Target Distance: " << target << ", Step Size: " << dstept << endl;
		outFile << index++ << ": " << result << endl;

		while (result.magval() < target)
		{
			direction = rand() % 360;
			step.reset(dstept, direction, Vector::POL);
			result = result + step;
			steps++;

			outFile << index++ << ": " << result << endl;
		}

		outFile << "After " << steps << "steps, the subject "
			"has the following location:\n";
		outFile << result << endl;

		result.polar_mode();
		outFile << "or\n" << result << endl;
		outFile << "Average outward distance per step = "
			<< result.magval() / steps << endl << endl;

		steps = 0;
		result.reset(0.0, 0.0);
		cout << "Enter target distance (q to quit): ";
	}
	cout << "Bye!\n";
	cin.clear();
	while (cin.get() != '\n')
		continue;

	outFile.close();
	return 0;
}
```
```
// randwalk.txt
Target Distance: 100, Step Size: 20
0: (x, y) = (0, 0)
1: (x, y) = (10, -17.3205)
2: (x, y) = (6.87131, 2.43326)
3: (x, y) = (26.3587, 6.93228)
4: (x, y) = (42.9395, -4.25158)
5: (x, y) = (62.3454, 0.58686)
6: (x, y) = (81.9779, -3.22932)
7: (x, y) = (101.203, 2.28343)
After 7steps, the subject has the following location:
(x, y) = (101.203, 2.28343)
or
(m, a) = (101.229, 1.29253)
Average outward distance per step = 14.4613

```

- 2
```C++
#pragma once
// 11.13 vector.h -- Vector class with << , mode state
#ifndef VECTOR_H_
#define VECTOR_H_

#include <iostream>

namespace VECTOR
{
	class Vector
	{
	public:
		enum Mode { RECT, POL };  // RECT for rectangular, POL for Polar modes

	private:
		double x;
		double y;
		Mode mode;  // RECT or POL

	private:
		void set_x(double mag, double ang);
		void set_y(double mag, double ang);

	public:
		Vector();
		Vector(double n1, double n2, Mode form = RECT);
		void reset(double n1, double n2, Mode form = RECT);
		~Vector();
		double xval() const { return x; }  // report x value
		double yval() const { return y; }
		double magval() const;
		double angval() const;
		void polar_mode();
		void rect_mode();

		// operator overloading
		Vector operator+ (const Vector& b) const;
		Vector operator- (const Vector& b) const;
		Vector operator- () const;
		Vector operator* (double n) const;

		// fiends
		friend Vector operator*(double n, const Vector& a);
		friend std::ostream& operator<<(std::ostream& os, const Vector& v);
	};
}
#endif
```
```C++
// 11.14 vector.cpp -- methods for the Vector class
#include <cmath>
#include "vect.h"  // include <iostream>
using std::sqrt;
using std::sin;
using std::cos;
using std::atan;
using std::atan2;
using std::cout;

namespace VECTOR
{
	// compute degrees in one radian
	const double Rad_to_deg = 45.0 / atan(1.0);
	// should be about 57.234523452

	// private methods
	// calculates magnitude from x and y
	void Vector::set_x(double mag, double ang)
	{
		x = mag * cos(ang);
	}

	void Vector::set_y(double mag, double ang)
	{
		y = mag * sin(ang);
	}

	// public methods
	Vector::Vector()   // defualt construcot
	{
		x = y = 0.0;
		mode = RECT;
	}

	// construct vector form rectangular coordinates if form is r
	// (the default) or else from polar coordinates if form is p
	Vector::Vector(double n1, double n2, Mode form)
	{
		mode = form;
		if (form == RECT)
		{
			x = n1;
			y = n2;

		}
		else if (form == POL)
		{
			double mag = n1;
			double ang = n2 / Rad_to_deg;
			set_x(mag, ang);
			set_y(mag, ang);
		}
		else
		{
			cout << "Incorrect 3rd argument: to Vecor() -- ";
			cout << "vector set to 0\n";
			x = y = 0.0;
			mode = RECT;
		}

	}

	// reset vector from recangluar coodinate if 
	// RECT 
	// form is POL
	void Vector::reset(double n1, double n2, Mode form)
	{
		mode = form;
		if (form == RECT)
		{
			x = n1;
			y = n2;
		}
		else if (form == POL)
		{
			double mag = n1;
			double ang = n2 / Rad_to_deg;
			set_x(mag, ang);
			set_y(mag, ang);
		}
		else
		{
			cout << "Incorrect 3rd argument to Vector() -- ";
			cout << "vector set to 0\n";
			x = y = 0.0;
			mode = RECT;
		}
	}

	Vector::~Vector()  // destructor
	{

	}

	double Vector::magval() const
	{
		return sqrt(x * x + y * y);
	}

	double Vector::angval() const
	{
		if (x == 0.0 && y == 0.0)
			return 0.0;
		else
			return atan2(y, x);;
	}

	void Vector::polar_mode()  // set to polar mode
	{
		mode = POL;
	}

	void Vector::rect_mode()  // set to rectangular mode
	{
		mode = RECT;
	}

	// operator overloading
	// add two Vectors
	Vector Vector::operator+(const Vector& b) const
	{
		return Vector(x + b.x, y + b.y);
	}

	// substract Vector b from a
	Vector Vector::operator-(const Vector& b) const
	{
		return Vector(x - b.x, y - b.y);
	}

	// reverse sign of Vector
	Vector Vector::operator-() const
	{
		return Vector(-x, -y);
	}

	Vector Vector::operator*(double n) const
	{
		return Vector(n * x, n * y);
	}

	// friend methods
	// multiply n by Vector a
	Vector operator*(double n, const Vector& a)
	{
		return a * n;
	}

	std::ostream& operator << (std::ostream& os, const Vector& v)
	{
		if (v.mode == Vector::RECT)
			os << "(x, y) = (" << v.x << ", " << v.y << ")";
		else if (v.mode == Vector::POL)
		{
			os << "(m, a) = (" << v.magval() << ", "
				<< v.angval() * Rad_to_deg << ")";
		}
		else
			os << "Vector object mode is invalid";
		return os;
	}
}  // end namespace VECTOR
```
- 3
```C++
// 11.15 randwalk.cpp -- using the Vector class
#include <iostream>
#include <cstdlib>
#include <ctime>
#include "vect.h"
int main()
{
	using namespace std;
	using VECTOR::Vector;
	srand(time(0));
	double direction;
	Vector step;
	Vector result(0.0, 0.0);
	unsigned long steps = 0;
	double target;
	double dstept;

	int N;
	unsigned long maxStep, minStep, sumStep;
	maxStep = minStep = sumStep = 0;

	cout << "Enter target distance (q to qiut): ";
	while (cin >> target)
	{
		cout << "Enter step length: ";
		if (!(cin >> dstept))
			break;

		cout << "Enter N times: ";
		if (!(cin >> N))
			break;

		for (int i = 0; i < N; i++)
		{
			while (result.magval() < target)
			{
				direction = rand() % 360;
				step.reset(dstept, direction, Vector::POL);
				result = result + step;
				steps++;
			}

			cout << "After " << steps << "steps, the subject "
				"has the following location:\n";
			cout << result << endl;

			result.polar_mode();
			cout << "or\n" << result << endl;
			cout << "Average outward distance per step = "
				<< result.magval() / steps << endl;

			if (i == 0)
			{
				maxStep = minStep = sumStep = steps;
			}
			else
			{
				maxStep = (steps > maxStep) ? steps : maxStep;
				minStep = (steps < minStep) ? steps : minStep;
				sumStep += steps;
			}

			steps = 0;
			result.reset(0.0, 0.0);
		}

		cout << "max step: " << maxStep << endl;
		cout << "min step: " << minStep << endl;
		cout << "average step: " << (sumStep / N) << endl;

		cout << "Enter target distance (q to quit): ";
	}

	cout << "Bye!\n";
	cin.clear();
	while (cin.get() != '\n')
		continue;
	return 0;
}
```

- 4
```C++
// 11.10 mytime3.h -- Time class with friends
#ifndef MYTIME3_H_
#define MYTIME3_H_
#include <iostream>

class Time
{
private:
	int hours;
	int minutes;
public:
	Time();
	Time(int h, int m = 0);
	void AddMin(int m);
	void AddHr(int h);
	void Reset(int h = 0, int m = 0);

	friend Time operator*(const Time& t, double n);
	friend Time operator*(double m, const Time& t) { return t * m; }
	friend Time operator+(const Time& t1, const Time& t2);
	friend Time operator-(const Time& t1, const Time& t2);

	friend std::ostream& operator<<(std::ostream& os, const Time& t);
};
#endif
```
```C++
// 11.11 mytime2.cpp -- implementing Time methods
#include "mytime3.h"

Time::Time()
{
	hours = minutes = 0;
}

Time::Time(int h, int m)
{
	hours = h;
	minutes = m;
}

void Time::AddMin(int m)
{
	minutes += m;
	hours += minutes / 60;
	minutes %= 60;
}

void Time::AddHr(int h)
{
	hours += h;
}

void Time::Reset(int h, int m)
{
	hours = h;
	minutes = m;
}

Time operator* (const Time& t, double mult)
{
	Time result;
	long totalminutes = t.hours * mult * 60 + t.minutes * mult;
	result.minutes = totalminutes % 60;
	result.hours = totalminutes / 60;
	return result;
}

Time operator+(const Time& t1, const Time& t2)
{
	Time sum;
	sum.minutes = t1.minutes + t2.minutes;
	sum.hours = t1.hours + t2.hours + sum.minutes / 60;
	sum.minutes %= 60;
	return sum;
}

Time operator-(const Time& t1, const Time& t2)
{
	Time diff;
	int tot1, tot2;

	tot1 = t1.minutes + 60 * t1.hours;
	tot2 = t2.minutes + 60 * t2.hours;
	diff.minutes = (tot1 - tot2) % 60;
	diff.hours = (tot1 - tot2) / 60;
	return diff;
}

std::ostream& operator<<(std::ostream& os, const Time& t)
{
	os << t.hours << " hours, " << t.minutes << " minutes";
	return os;
}
```

- 5
```C++

```
```C++

```
```C++

```
- 6
```C++
// 11.16 stonewt.h -- definition for the Stonewt class 
#ifndef STONEWT_H_
#define STONEWT_H_
class Stonewt
{
private:
	enum { Lbs_per_stn = 14 };
	int stone;
	double pds_left;
	double pounds;
public:
	Stonewt(double lbs);
	Stonewt(int stn, double lbs);
	Stonewt();
	~Stonewt();
	void show_lbs() const;
	void show_stn() const;

	bool operator > (const Stonewt& stn) const;
	bool operator >= (const Stonewt& stn) const;
	bool operator < (const Stonewt& stn) const;
	bool operator <= (const Stonewt& stn) const;
	bool operator == (const Stonewt& stn) const;
	bool operator != (const Stonewt& stn) const;

	operator int() const;
	operator double() const;

	friend std::ostream& operator<<(std::ostream& os, const Stonewt& stn);
};
#endif
```
```C++
// 11.17 stonewt.cpp -- Stonewt methods
#include <iostream>
using std::cout;
#include "stonewt.h"

Stonewt::Stonewt(double lbs)
{
	stone = int(lbs) / Lbs_per_stn;  // integer division
	pds_left = int(lbs) % Lbs_per_stn + lbs - int(lbs);
	pounds = lbs;
}

Stonewt::Stonewt(int stn, double lbs)
{
	stone = stn;
	pds_left = lbs;
	pounds = stn * Lbs_per_stn + lbs;
}

Stonewt::Stonewt()
{
	stone = pounds = pds_left = 0;
}

Stonewt::~Stonewt()  // destructor
{

}

void Stonewt::show_stn() const
{
	cout << stone << " stone, " << pds_left << " pounds\n";
}

// show weight in pounds
void Stonewt::show_lbs() const
{
	cout << pounds << " pounds\n";
}

bool Stonewt::operator>(const Stonewt& stn) const
{
	return (pounds > stn.pounds);
}

bool Stonewt::operator>=(const Stonewt& stn) const
{
	return (pounds >= stn.pounds);
}

bool Stonewt::operator<(const Stonewt& stn) const
{
	return (pounds < stn.pounds);
}

bool Stonewt::operator<=(const Stonewt& stn) const
{
	return (pounds <= stn.pounds);
}

bool Stonewt::operator==(const Stonewt& stn) const
{
	return (pounds == stn.pounds);
}

bool Stonewt::operator!=(const Stonewt& stn) const
{
	return (pounds != stn.pounds);
}

Stonewt::operator int() const
{
	return int(pounds + 0.5);
}

Stonewt::operator double() const
{
	return pounds;
}

std::ostream& operator<<(std::ostream& os, const Stonewt& stn)
{
	os << stn.pounds;
	return os;
}

```
```C++
// 11.18 stone.cpp -- user-defined conversions
// compile with stonewt.cpp
#include <iostream>
#include "stonewt.h"

int main()
{
	using namespace std;

	Stonewt compareStn(11.0);
	Stonewt stns[6] = { 11.0, 13.0, 15.0 };

	cout << "Enter 3 pounds of number: ";
	for (int i = 3; i < 6; i++)
	{
		double tempStn;
		cin >> tempStn;
		stns[i] = tempStn;
	}

	int count = (stns[1] >= compareStn) ? 1 : 0;
	Stonewt maxPounds, minPounds;
	maxPounds = minPounds = stns[1];

	for (int i = 1; i < 6; i++)
	{
		if (stns[i] < minPounds)
		{
			minPounds = stns[i];
		}
		else if (stns[i] > maxPounds)
		{
			maxPounds = stns[i];
		}

		if (stns[i] >= compareStn)
		{
			count++;
		}
	}

	cout << "max pounds: " << maxPounds << endl;
	cout << "min pounds: " << minPounds << endl;
	cout << "pounds than 11: " << count << endl;
}
```
- 7
```C++

```
```C++

```
```C++

```
