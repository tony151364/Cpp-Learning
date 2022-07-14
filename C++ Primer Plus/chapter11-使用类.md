### 第11章 使用类
- 不要害怕犯错
- 轻松地使用这门语言

### 11.1 运算符重载
- 要重载运算符，需使用被称为运算符函数的特殊函数形式。运算符的函数格式如下
```C++
operatorop(argument-list)
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

### 11.2.2 重载限制
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

	adjusted = total * 1.5;  // use operator+()
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
- 具体用哪个版本，根据具体情况而定

## 11.5 再谈重载：一个矢量类

```C++
// 11.13
```






