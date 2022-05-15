- C++自带了一个包含函数的大型库（标准ANSI库加上多个C++类），但真正的编程乐趣在于编写自己的函数

## 7.1 复习函数的基本知识
- 要使用C++函数，必须：
  - 提供函数定义；
  - 提供函数原型；
  - 调用函数； 

### 7.1.1 定义函数
- 可以将函数分为两类：
```C++
// 1.没有返回值的函数(void)
void funtionName(parameterList)  // parameter指定了传递给函数的参数类型和数量
{
    statement(s);
    return;  // 可选的,返回语句标记了函数的结尾
}
// 2.有返回值的函数
typeName funtionName(parameterList)
{
    statement(s);
    return value;  // 必须有返回语句
}
```
- 返回值：除了数组，可以是任何类型，包括指针、结构、对象！虽然不能直接返回数组，但是可以将数组作为结构或对象的组成部分来返回。

```C++
#include <iostream>
using namespace std;
void cheers(int);
double cube(double x);

int main()
{
	cheers(5);  // function call
	cout << "Give me a number: ";
	double side;
	cin >> side;
	double volume = cube(side);  // function call
	cout << "A " << side << "-foot cube has a volume of ";
	cout << volume << " cubic feet.\n";
	cheers(cube(2));  // prototype protection at work
	return 0;
}

void cheers(int n)
{
	for (int i = 0; i < n; ++i)
		cout << "Cheers! ";
	cout << endl;
}

double cube(double x)
{
	// 返回语句使用了一个表达式，函数将计算该表达式的值，并将其返回
	return x * x * x;
}
```
- 函数原型将返回值类型告知调用程序，而函数定义命令被调用函数应返回什么类型的数据。
- [ ] **在原型中提供与定义中相同的信息似乎有些多余**，但这样做确实有些道理。要让信差从办公室的办公桌取走一些物品，则向信差和办公室中的同事交代自己的意图，将提高信差顺利完成这样工作的概率。(需要仔细揣摩一下, 结合图7.1)（原型给调用函数的人提出意见，定义给函数本身提供协助）

#### 1.为什么需要原型
- 原型描述了函数到编译器的接口，，它将函数返回值的类型以及参数的类型告诉编译器
  - 参数类型：当参数类型不对，或者没有提供时，编译器能捕获这种错误。
  - 返回值类型：知道了返回值类型，如double，编译器才知道应该检索多少个字节以及如何解释它们。
- 难道编译器不能自己在文件中自己查找，了解函数是如何定义的吗？
  - 问题1：效率不高。编译器在搜索文件的剩余部分时将必须停止对main()的编译。
  - 问题2：函数可能不在当前文件中。C++允许将一个程序放在多个文件中，单独编译这些文件，然后再将它们组合起来。在这种情况下，编译器在编译main()时，可能无权访问函数代码。如果函数位于库中，情况也将如此。
- 避免使用函数原型的唯一方法：在首次使用函数之前定义它，但这并不是总是可行的。而且，C++的编程风格是将main()放在最前面，因为它通常提供了程序的整体结构。

#### 2.原型的语法
- 原型是一条语句，因此必须以分号结束。获得原型最简单的方法是：复制函数定义中的函数头，并添加分号。
- 函数原型不要求提供变量名，但是也可以包括。原型中的变量名相当于占位符，因此不必与函数定义中的变量名相同。
#### C++原型与ANSI原型
- 在C++中，括号为空与在括号中使用关键字void是等效的——意味着函数没有参数。
- 在ANSI C中，括号为空意味着不指出参数——这意味着将在后面定义参数列表。
- C++中，不指定参数列表是应使用省略号：``` void say_bye(...); ```

#### 3.原型的功能
- 功能：
  - 编译器正确处理函数返回值
  - 编译器检查使用的参数数目是否正确
  - 编译器检查使用的参数类型是否正确。如果不正确，则转换为正确的类型（如果可能的话）
- 在C++中，原型不是可选的。
- C++自动将传递的值转换为原型中指定的类型，条件是两者都是算术类型。``` cheers(cube(2)) ```
- 通常，原型自动将被传递的参数强制转换为期望的类型。（但第8章将介绍的函数重载可能导致二义性，因此不允许某些自动强制类型转换）
- 较大的类型转换为较小的类型时，有些编译器将发出警告，指出这可能会丢失数据。
- 仅当有意义时，原型化才会导致类型转换。例如，原型不会将整数转换为结构或指针。
- 在编译阶段进行的原型化被称为静态类型检查(static type checking)。可以看出，静态类型检查可捕获许多在运行阶段非常难以捕获的错误。

## 7.2 函数参数和按值传递
- C++通常按值传递参数，这意味着将数值参数传递给函数，而后者将其赋值给一个新的变量。
- 用于接收传递值的变量称为形参。传递给函数的值被称为实参。
- [ ] 处于标准化的目的，C++标准使用argument来表示实参(actural argument)，使用parameter来表示形参(formal argument)，因此参数传递，将argument传给parameter。（书上这里是不是错了？）
### 7.2.1 多个参数
```C++
// 7.3 twoarg.cpp -- a function with 2 arguments
#include <iostream>
using namespace std;
void n_chars(char, int);

int main()
{
	int times = 0;
	char ch;

	cout << "Enter a character: ";
	cin >> ch;
	while (ch != 'q')  // q to quit
	{
		cout << "Enter an integer: ";
		cin >> times;
		n_chars(ch, times);  // function with two arguments
		cout << "\nEnter another character or press the"
			" q-key to quit: ";
		cin >> ch;
	}
	cout << "The value of times is " << times << ".\n";
	cout << "Bye\n";
	return 0;
}

void n_chars(char c, int n)
{
	while (n-- > 0)  // continue untill n reaches 0
		cout << c;
}
```
### 7.2.2 另外一个接受两个参数的函数
- 该函数将演示局部变量的用法，而不是形参的用法。
- 当数字非常大时，这种交替进行乘除的运算的策略可以防止中间结果超出最大的浮点数。

```C++
// 7.4 lotto.cpp -- probability of winning
#include <iostream>
long double probability(unsigned numbers, unsigned picks);

int main()
{
	using namespace std;
	double total, choices;
	cout << "Enter the total number of number of choices on the game card and\n"
		"the number of picks allowed:\n";
	while ((cin >> total >> choices) && choices <= total)
	{
		cout<< fixed;
		cout << "You have one chance in ";
		cout << probability(total, choices);  // compute the odds
		cout << " of winning.\n";
		cout << "Next two numbers (q to quit): ";
	}
	cout << "Bye\n";
	return 0;
}

long double probability(unsigned numbers, unsigned picks)
{
	long double result = 1.0;
	long double n;
	unsigned p; // = unsigned int

	for (n = numbers, p = picks; p > 0; n--, p--)
		result = result * n / p;
	
	return result;
}
```
- 形参与其他局部变量的主要区别是：形参从调用probability()的函数那里获得自己的值，而其他变量是从函数中获得自己的值

## 7.3 函数和数组
```C++
// 7.5 arrfun1.cpp -- functions with an array argument
#include <iostream>
const int ArSize = 8;
int sum_arr(int arr[], int n);

int main()
{
	using namespace std;
	int cookies[ArSize] = { 1, 2, 4, 8, 16, 32, 64, 128 };

	int sum = sum_arr(cookies, ArSize);
	cout << "Total cookies eaten: " << sum << "\n";
	return 0;
}

int sum_arr(int arr[], int n)  // int sum_arr(int* arr, int n)
{
	int total = 0;
	
	for (int i = 0; i < n; i++)
		total += arr[i];
	return total;
}
```
- 方括号指出arr是一个数组，而方括号为空表明，可以将任何长度的数组传递给该函数。**arr实际上是一个指针！**
- 下面讨论为何该程序管用

### 7.3.1 函数如何使用指针来处理数组
- 大多数情况下，C++和C语言一样，将数组名视为指针。第4章说过，C++将数组名解释为第一个元素的地址，是个常量。
- 该规则的一些例外：
	- 数组声明使用数组名标记存储位置
	- 对数组名只有sizeof将得到整个数组的长度（以字节为单位）
	- 如第4章指出，将地址运算符&用于数组名时，将返回整个数组的地址，例如&cookies将返回一个32字节内存块的地址（如果int长4字节
- 在C++中，当且仅当用于函数头和函数原型中，int* arr 和int arr[ ]的含义才是相同的。数组表示法(int arr[])提示用户，arr不仅指向int，还指向int数组的第一个int。
- 当指针指向数组的第一个元素时，本书使用数组表示法；当指针指向一个独立的值时，使用指针表示法
- 在其他上下文中,int* arr 和int arr[ ]的含义并不相同。例如，不能在函数体中使用int tip[ ] 来声明指针
```C++
arr[i] = *(ar + i);
&arr[i] = ar + i;
```

### 7.3.2 将数组作为参数意味着什么
- 如果数组很大，拷贝数组的开销非常大，还需要更多的计算机内存，所以传递地址是个好事。
```C++
// 7.6 arrfun2.cpp -- functions with an array argument
#include <iostream>
const int ArSize = 8;
int sum_arr(int arr[], int n);
// use std:: instead of using derective
int main()
{
	int cookies[ArSize] = { 1, 2, 4, 8, 16, 32, 64, 128 };

	std::cout << cookies << " = array address, "; 
	std::cout << sizeof cookies << " = sizeof cookies\n";  // cookies是int类型指针常量

	int sum = sum_arr(cookies, ArSize);
	std::cout << "Total cookies eaten: " << sum << std::endl;
	sum = sum_arr(cookies, 3);
	std::cout << "First three eaters ate " << sum << std::endl;
	sum = sum_arr(cookies+4, 4); // same as sum_arr(&a[4], 4);
	std::cout << "Last four eaters ate " << sum << std::endl;
	return 0;
}

int sum_arr(int arr[], int n)  // int sum_arr(int* arr, int n)
{
	int total = 0;

	std::cout << arr << " = arr, ";  // arr是一个int类的指针变量
	std::cout << sizeof arr << " = sizeof arr\n";
	
	for (int i = 0; i < n; i++)
		total += arr[i];
	return total;
}
```
- cookies与arr的sizeof值不同也解释了，为什么必须显式传递数组长度，而不能在sum_arr()中使用sizeof arr的原因：指针本身没有指出数组长度。
- [ ] 使用原始数据增加了破坏数据的风险？&引用？

### 7.3.3 更多数组函数示例
- 确保显式数组的函数不修改数组，可在形参声明时使用关键字const。这意味着指针ar指向的是常量数据，意味着不能修改该数据

```C++
// 7.7 arrfun3.cpp -- array functions and const
#include <iostream>
const int Max = 5;
int fill_array(double ar[], int limit);
void show_array(const double ar[], int n);
void revalue(double r, double ar[], int n);
using namespace std;

int main()
{
	double properties[Max];
	int size = fill_array(properties, Max);
	show_array(properties, size);

	if (size > 0)
	{
		cout << "Enter revaluation factor: ";
		double factor;
		while (!(cin >> factor))  // bad input
		{
			cin.clear();
			while (cin.get() != '\n')
				continue;
			cout << "Bad input; Please enter a number: ";
		}
		revalue(factor, properties, size);
		show_array(properties, size);
	}
	cout << "Done.\n";
	return 0;
}

int fill_array(double ar[], int limit)
{
	double temp;
	int i;
	for (i = 0; i < limit; i++)
	{
		cout << "Enter value #" << i + 1 << ": ";
		cin >> temp;
		if (!cin)
		{
			cin.clear();
			while (cin.get() != '\n')
				continue;
			cout << "Bad input; input process terminated.\n";
			break;
		}
		else if (temp < 0)  // signal to terminate
			break;
		ar[i] = temp;
	}
	return i;
}

void show_array(const double ar[], int n)
{
	for (int i = 0; i < n; ++i)
	{
		cout << "Property #" << i + 1 << ": $";
		cout << ar[i] << endl;
	}
}

// multiplies each element of ar[] by r
void revalue(double r, double ar[], int n)
{
	for (int i = 0; i < n; i++)
		ar[i] *= r;
}
```

### 7.3.4 使用数组区间的函数
- 另一种给函数提供所需信息的方法，即指定元素区间(range)，这可以通过传递两个指针来完成：一个指针标识数组开头，另一个指针标识数组的尾部。
- STL方法使用“超尾”概念来指定区间（STL将在第16章介绍）

```C++
// 7.8 arrfun4.cpp -- functions with an array range
#include <iostream>
const int ArSize = 8;
int sum_arr(const int* begin, const int* end);

int main()
{
	int cookies[ArSize] = { 1, 2, 4, 8, 16, 32, 64, 128 };

	// cookies + ArSize指向区间中最后一个元素后面的一个位置
	int sum = sum_arr(cookies, cookies + ArSize);
	std::cout << "Total cookies eaten: " << sum << std::endl;
	sum = sum_arr(cookies, cookies + 3);
	std::cout << "First three eaters ate " << sum << std::endl;
	sum = sum_arr(cookies + 4, cookies + 8);
	std::cout << "Last four eaters ate " << sum << std::endl;
	return 0;
}

int sum_arr(const int* begin, const int* end)  // int sum_arr(int* arr, int n)
{
	const int* pt;
	int total = 0;

	for (pt = begin; pt != end; pt++)
		total += *pt;
	return total;
}
```
- 根据指针减法规则，在sum_arr()中，表达式end-begin是一个整数值，等于数组的元素数目

### 7.3.5 指针和const
- 可以用两种不同的方式将const关键字用于指针
	- 1.让指针指向一个常量对象，可防止用指针修改指向的值
	- 2.将指针本身声明为常量，以防止改变指针指向的位置

```C++
int age = 39;  
const int * pt = &age;  // 
// pt的声明并不意味着它指向的值实际上就是一个常量，而只是意味着对pt而言，这个值是常量。
// pt指向age，而age不是const。可以直接通过age变量来修改age的值，但不能使用pt指针来修改它
```

- 还有两种可能：
	- 1.将const变量的地址赋给指向const的指针（可行）
	- 2.const的地址赋给常规指针（不可行） 
```C++
const float g_earth = 9.80;
const float * pe = &g_earth;  // VALID

const float g_moon = 1.63;
float * pm = &g_moon;  // INVALID
```
- C++禁止第二种情况的原因很简单——如果将g_moon的地址赋给pm，则可以使用pm来修改g_moon的值，这使得g_moon的const状态很荒谬，因此C++禁止将const的地址赋给非const指针。
- 如果读者非要这样做，可以使用强制类型转换来突破这种限制，第15章讨论对运算符const_cast

#### 多重指针
- 如果将指针指向指针，情况更复杂。假如涉及的是一级间接关系，则将非const指针赋给const指针是可以的
```C++
int age = 39;  // age++ is a valid operation
int * pd = &age;  // *pd = 41 is a valid operation
const int* pt = pd;  // *pt = 42 is an invalid operation
```
- 然而，进入两级间接关系时，与一级间接关系一样将const和非const混合的指针赋值方式将不再安全。如果允许这样做，则可以编写这样的代码：
```C++
const int **pp2;
int *p1;
const int n = 13;
pp2 = &p1;  // not allowed, but suppose it were
*pp2 = &n;  // valid, both const, but sets p1 to point at n
*p1 = 10;  // valid, but chages const n
```
- [ ] 上述代码将非const地址（&p1）赋给了const指针（pp2）,因此可以使用p1来修改const数据。仅当只有一层间接关系（如指针指向基本数据类型）时，才可以将非const地址或指针赋给const指针。

```C++
const int months[12] = {31, 29, 31, 30...}

int sum(int arr[], int n};  // should have been const int arr[]
...
int j = sum(months, 12};  // not allowed
```
- 不能把常量数组的地址赋给非常量指针，也不能把数组名作为参数传递给非常量形参的函数;
- 将指针参数声明为指向常量数据的指针有两条理由：
	- 避免无意间修改数据而造成编程错误
	- 使用const能使函数处理const和非const实参，否则将只能接受非const数据
```C++
int age = 39;
const int * pt = &age;
// 能防止修改pt指向的值，
```
- [ ] 这个部分先略过，之后看视频再细看，看了几遍都觉得懵

## 7.4 函数和二维数组
```C++
int data[3][4] = {{1, 2, 3, 4}, {4, 8, 7, 6}, {2, 6, 5, 4}};
int total = sum(data, 3);  // 它的函数原型是怎样的？

// 不能省略括号, int *ar2[4];是4个指向int的指针组成的数组。而不是由一个指向4个int组成的数组的指针。
int sum(int (*ar2)[4], int size);  
int sum(int ar2[][4], int size);  // 另一种形式，但是可读性更强
```
- 在定义时已经指明了指针的列数，因此size来指明行数。使用时将ar2看做数组名使用即可
```C++
ar2[r][c] == *(*(ar2 +r) + c)
ar2 + r  // 第r行的首地址
*(ar2 + r)  // 第r行第一个元素的值
*(ar2 + r) + c  // 第r行第c个元素的地址
*(*(ar2 + r) + c)  // 第r行第c个元素的值
```
- sum()代码在声明参数ar2时，没有用const。因为这种技术智能用于指向基本类型的指针，而不能用于指向指针的指针(ar2)
- [ ] 也有点不太懂，结合C语言那本书看看

## 7.5 函数和C-风格字符串
- 前面介绍的大部分有关设计数组函数的知识也适用于字符串函数。如，将字符串作为参数时，意味着传递的是地址，但可以使用const类禁止对字符串参数进行修改。

### 7.5.1 将C-风格字符串作为参数的函数
- 将字符串作为参数传递给函数，表示字符串的方式有三种：
	- char 数组
	- 用引号括起的字符串常量（也称字符串字面值）
	- 被设置为字符串的地址的char指针
- 上述3种类型都是char指针(char * )，因此可以将其作为字符串处理函数的参数。

```C++
// 7.9 strfun.cpp -- function with a string argument
#include <iostream>
unsigned int c_in_str(const char* str, char ch);

int main()
{
	using namespace std;
	char mmm[15] = "minmum";
	const char* wail = "ululate"; 
	
	unsigned int ms = c_in_str(mmm, 'm');
	unsigned int us = c_in_str(wail, 'u');
	cout << ms << " m characters in " << mmm << endl;
	cout << us << " u characters in " << wail << endl;
	return 0;
}

unsigned int c_in_str(const char* str, char ch)
{
	unsigned int count = 0;

	while (*str)
	{
		if (*str == ch)
			count++;
		str++;
	}
	return count;
}
```
### 7.5.2 返回C-风格字符串的函数
```C++
// 7.10 strgback.cpp -- a function that returns a pointer to char
#include <iostream>
char* buildstr(char c, int n);

int main()
{
	using namespace std;
	int times;
	char ch;

	cout << "Enter a character: ";
	cin >> ch;
	cout << "Enter a interger: ";
	cin >> times;

	char* ps = buildstr(ch, times);
	cout << ps << endl;
	delete[] ps;

	ps = buildstr('+', 20);
	cout << ps << "-DONE-" << ps << endl;
	delete[] ps;
	return 0;
}

char* buildstr(char c, int n)
{
	char* pstr = new char[n + 1];
	pstr[n] = '\0';
	while (n-- > 0)
		pstr[n] = c;
	return pstr;
}
```
- buildstr中是为了额外的变量
```C++
// 从前向后填充
int i = 0;
while (i < n)
	pstr[i++] = c;
```
- 这种设计（让函数返回一个指针，该指针指向new分配的内存）的**缺点**是，程序员必须记住使用delete。

## 7.6 函数和结构
- 在涉及到函数时，结构变量的行为更接近于基本的单值变量。也就是说，与数组不同，结构将其数据组合成单个实体或数据对象，该实体被视为一个整体。
- 前面讲过，可以将一个结构赋给另外一个结构。同样，也可以按值传递结构，就像普通变量那样。
- 另外，函数也可以返回结构。与数组名就是数组第一个元素的地址不同的是，结构名只是结构的名称，要获得结构的地址，必须使用地址运算符&。C++还使用该运算符表示引用，这将在第8章讨论。
- 按值传递结构有个**缺点**：如果结构非常大，则复制结构将增加内存要求，降低系统运行的速度。
	- C语言倾向传递结构的地址。
	- C++提供第三种方式——按引用传递（第8章介绍）
	- 下面介绍前两种


### 7.6.1 传递和返回结构
-当结构比较小时，按值传递最合理。
```C++
// 7.11 travel.cpp -- using structures with functions
#include <iostream>
struct travel_time
{
	int hours;
	int minutes;
};
const int Mins_per_hr = 60;

travel_time sum(travel_time t1, travel_time t2);
void show_time(travel_time t);

int main()
{
	using namespace std;
	travel_time day1 = { 5, 45 };
	travel_time day2 = { 4, 55 };

	travel_time trip = sum(day1, day2);
	cout << "Two-day total: ";
	show_time(trip);

	travel_time day3 = { 4, 22 };
	cout << "Three-day total: ";
	show_time(sum(trip, day3));

	return 0;
}

travel_time sum(travel_time t1, travel_time t2)
{
	travel_time total;
	
	total.minutes = (t1.minutes + t2.minutes) % Mins_per_hr;
	total.hours = t1.hours + t2.hours +
				 (t1.minutes + t2.minutes) / Mins_per_hr;
	return total;
}

void show_time(travel_time t)
{
	using namespace std;
	cout << t.hours << " hours, "
		<< t.minutes << " minutes\n";
}
```
- 这行很妙：``` show_time(sum(trip, day3)); ```

### 7.6.2 另一个处理结构函数的实例
```C++

```


















