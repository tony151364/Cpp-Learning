### 难点
- [ ] [函数指针、函数指针数组](https://www.bilibili.com/video/BV1Yv411t7qe/?p=69)

## 开头
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
- [x] **在原型中提供与定义中相同的信息似乎有些多余**，但这样做确实有些道理。要让信差从办公室的办公桌取走一些物品，则向信差和办公室中的同事交代自己的意图，将提高信差顺利完成这样工作的概率。(不然邮差和同事都会不知所措，降低了办事效率)（原型给出简单的信息，方便编译器去纠错。就像去饭店吃饭，原型就是提前告诉饭店有几个人去，具体这几个人叫什么名字饭店并不关心）

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
	cout << fixed;
	cout.precision(0);

	while ((cin >> total >> choices) && choices <= total)
	{	
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
	- **对数组名使用sizeof将得到整个数组的长度（以字节为单位）**
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

#### 尽可能使用const
- 将指针参数声明为指向常量数据的指针有两条理由：
	- 这样可以避免由于无意间修改数据而导致的编译错误
	- 使用const使得函数能够处理const和非const实参，否则只能接受非const数据 
```C++
int age = 39;
const int * pt = &age;  // *pt不能变，pt可以变。age也可以修改
int* const pt = &age;  // pt不能变,*pt可以变
const int* const pt = &age;  // 都不能改，非常安全
```
- 图7-5很清晰
- [x] 这个部分先略过，之后看视频再细看，看了几遍都觉得懵

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
*(ar2 + r)  // == ar2
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
- 当结构比较小时，按值传递最合理。
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
// 7.12 strctfun.cpp -- function with a structure argument
#include <iostream>
#include <cmath>
using namespace std;
struct polar
{
	double distance;
	double angle;
};
struct rect
{
	double x;
	double y;
};

polar rect_to_polar(rect xypos);
void show_polar(polar dapos);

int main()
{
	rect rplace;
	polar pplace;

	cout << "Enter the x and y values: ";
	while (cin >> rplace.x >> rplace.y)
	{
		show_polar(rect_to_polar(rplace));
		cout << "Next two numbers (q to qiut): ";
	}
	cout << "Done.\n";
	return 0;
}

// convert rectangular to polar coordinates
polar rect_to_polar(rect xypos)
{
	polar answer;

	answer.distance =
		sqrt(xypos.x * xypos.x + xypos.y * xypos.y);

	answer.angle = atan2(xypos.y, xypos.x);  // 根据x,y的值计算角度
	return answer;
}

void show_polar(polar dapos)
{
	const double Rad_to_deg = 57.29577951;  // 180/π,用于弧度转角度

	cout << "distance = " << dapos.distance;
	cout << ", angle = " << dapos.angle * Rad_to_deg;
	cout << " degrees\n";
}
```

- 如果程序在输入循环后还需要进行输入，则必须使用cin.clear()重置输入，然后还需要读取不合法的输入来丢弃它们。程序7.7演示了这种技术。

```C++
for (i = 0; i < limit; i++)
{
	cout << "Enter value #" << i + 1 << ": ";
	cin >> temp;
	if (!cin)  // cin is false
	{
		cin.clear();
		while (cin.get() != '\n')
			continue;
		cout << "Bad input; input process terminated.\n";
		break;
	}
	else if (temp < 0)  // signal to terminate
		break;
}
```

### 7.6.3 传递结构的地址
- 将前面函数进行修改
	- 将形参声明为指向poplar的指针，即polar* 型。
	- 由于函数不应该修改结构，因此使用const修饰符。
	- 使用间接成员运算符(->)

```C++
// 7.13 trctptrcpp -- function with pointer to structure arguments
#include <iostream>
#include <cmath>
using namespace std;
struct polar
{
	double distance;
	double angle;
};
struct rect
{
	double x;
	double y;
};

void rect_to_polar(const rect* pxy, polar* pda);
void show_polar(const polar* dapos);

int main()
{
	rect rplace;
	polar pplace;

	cout << "Enter the x and y values: ";
	while (cin >> rplace.x >> rplace.y)
	{
		rect_to_polar(&rplace, &pplace);
		show_polar(&pplace);
		cout << "Next two numbers (q to qiut): ";
	}
	cout << "Done.\n";
	return 0;
}

// convert rectangular to polar coordinates
void rect_to_polar(const rect* pxy, polar* pda)
{
	pda->distance =
		sqrt(pxy->x * pxy->x + pxy->y * pxy->y);

	pda->angle = atan2(pxy->y, pxy->x);  // 根据x,y的值计算角度
}

void show_polar(const polar* dapos)
{
	const double Rad_to_deg = 57.29577951;  // 180/π,用于弧度转角度

	cout << "distance = " << dapos->distance;
	cout << ", angle = " << dapos->angle * Rad_to_deg;
	cout << " degrees\n";
}
```

## 7.7 函数和string对象
- 与char数组相比，string对象与结构更相似。如，可以将一个结构赋给另一个结构，也可以将一个对象赋给另一个对象。可以将结构作为完整的实体传递给函数，也可以将对象作为完整的实体进行传递。
- 如果需要多个字符串，可以声明一个string对象数组，而不是二维char数组。
```C++
// 7.14 topfive.cpp -- handing an array of string objects
#include <iostream>
#include <string>
using namespace std;
const int SIZE = 5;
void display(const string sa[], int n);
int main()
{
	string list[SIZE];
	
	cout << "Enter your " << SIZE << " favorite astronimical sights:\n";
	for (int i = 0; i < SIZE; ++i)
	{
		cout << i + 1 << ": ";
		getline(cin, list[i]);
	}

	cout << "Your list:\n";
	display(list, SIZE);

	return 0;
}

void display(const string sa[], int n)
{
	for (int i = 0; i < n; ++i)
		cout << i + 1 << ": " << sa[i] << endl;
}
```
- 除了getline()外，该程序像对待内置类型（如int）一样对待string，如声明、赋值、打印。

## 7.8 函数与array对象
- 在C++中，类对象是基于结构的，因此结构编程方面的有些考虑因素也适用于类。例如，可按值将对象传递给函数，这时函数处理的是原始对象的副本。另外，也可传递指向对象的指针，这让函数能直接操作原始对象。
- 使用array需要包含头文件array，而名词array位于名称空间std中。
- 注意：模板array并非只能存储基本数据类型，它还可存储类对象。

```C++
// 7.15 arrobj.cpp -- functions with array objects (C++11)
#include <iostream>
#include <array>
#include <string>
// constant data
const int Seasons = 4;
// const arrya对象，包含四个string对象
const std::array<std::string, Seasons> Snames =
{ "Spring", "Summer", "Fall", "Winter" };

void fill(std::array<double, Seasons>* pa);
void show(std::array<double, Seasons> da);

int main()
{
	std::array<double, Seasons> expenses;
	fill(&expenses);
	show(expenses);
	return 0;
}

void fill(std::array<double, Seasons>* pa)
{
	using namespace std;
	for (int i = 0; i < Seasons; ++i)
	{
		cout << "Enter " << Snames[i] << " expenses: ";
		cin >> (*pa)[i];  // pa是array对象的指针，pa不是array对象，(*pa)才是array对象，所以用的时候需要先把pa转换为array对象
	}
}

void show(std::array<double, Seasons> da)
{
	using namespace std;
	double total = 0.0;
	cout << "\nEXPENSES\n";
	for (int i = 0; i < Seasons; i++)
	{
		cout << Snames[i] << ": $" << da[i] << endl;
		total += da[i];
	}
	cout << "Total Expenses: $" << total << endl;
}
```
- 对于``` cin >> (*pa)[i];  ```，pa是一个指向array<double, 4>对象的指针，因此\*pa为该对象，而(\*pa)[i]是该对象的第i+1个元素。由于优先级的影响，括号必不可少。这种方式逻辑简单，但增加了犯错的机会。

## 7.9 递归
- 与C语言不同，C++不允许main()调用自己

### 7.9.1 包含一个递归调用的递归
```C++
// 7.16 recur.cpp
#include <iostream>
void countdown(int n);
using namespace std;

int main()
{
	countdown(4);
	return 0;
}

void countdown(int n)
{
	cout << "Counting down ..." << n << " (n at " << &n << ")" << endl;
	if (n > 0)
		countdown(n - 1);  // function calls itself
	cout << n << ": Kaboom!" << "\t  " << " (n at " << &n << ")" << endl;
}
```
- ↑太妙了！几行代码把递归说的清清楚楚！
- 注意：每个n都是一个独立的变量，它们有不同的地址
### 7.9.2 包含多个递归调用的递归
```C++
// 7.17 ruler.cpp -- using recusion to subdivide a ruler
#include <iostream>
using namespace std;
const int Len = 66;
const int Divs = 6;
void subdivide(char ar[], int low, int high, int level);

int main()
{
	char ruler[Len];
	int i;
	for (i = 1; i < Len - 2; ++i)
		ruler[i] = ' ';
	ruler[Len - 1] = '\0';

	int max = Len - 2;
	int min = 0;
	ruler[min] = ruler[max] = '|';
	cout << ruler << endl;
	for (i = 1; i <= Divs; i++)
	{
		subdivide(ruler, min, max, i);
		cout << ruler << endl;
		for (int j = 1; j < Len - 2; j++)
			ruler[j] = ' ';  // reset to blank ruler
	}
	return 0;
}

void subdivide(char ar[], int low, int high, int level)
{
	if (level == 0)
		return;
	
	int mid = (high + low) / 2;
	ar[mid] = '|';
	subdivide(ar, low, mid, level - 1);
	subdivide(ar, mid, high, level - 1);
}
```

## 7.10 函数指针
- 函数也有地址。可以编写将另一个函数的地址作为参数的函数。这样第一个函数能够找到第二个函数，并运行它。

### 7.10.1 函数指针的基础知识
#### 1.获取函数的地址
- 函数名就是地址。也就是说，如果think()是一个函数，则think就是该函数的地址
#### 2.声明函数指针
- 声明指向某种数据类型的指针时，必须指定指针指向的类型。同样，声明指向函数的指针是，也必须指定指针指向的函数类型。
- 说白了，就是需要声明：**返回值** + **参数列表**

```C++
double pam(int)  // prototype

// 没括号返回的就是double类型的指针
double (*pf)(int);  // pf points to a function that takes
		    // ont int argument and that returns type double
pf = pam;   // pf now points to the pam() function
```
- 可以看出与pam声明类似，只是将pam替换为(* pf)。由于pam是函数，因此(* pf)也是函数。如果(* pf)是函数，则pf就是函数指针
- 注意：声明的时候，可以先编写这种函数的原型，然后用(* pf)替换函数名。
- 参数类型和返回值类型必须相同
#### 3.使用指针来调用函数
- (* pf)扮演的角色与函数名相同，因此使用(* pf)时，只需将它看做函数名即可。
```C++
double pam(int);
double (*pf)(int);
pf = pam;
double x = pam(4);  // call pam() using the function name
double y = (*pf)(5);  // call pam() using the function name
double y = pf(5);  // C++也允许像使用函数名那样使用pf
```
- 虽然第一种格式不太好看，但是它给出了强有力的提示——代码正在使用函数指针
#### 历史与逻辑
- 两种观点：
	- 一种学派认为，由于pf是函数指针，而* pf是函数，因此应将(* pf)用作函数调用
	- 另一种认为，由于函数名是指向该函数的指针，指向函数的指针的行为应于函数名相似，因此应将pf()用作函数调用使用
	- 容忍逻辑上无法自圆其说的观点正是人类思维活动的特点

### 7.10.2 函数指针示例
```C++
// 7.18 fun_ptr.cpp -- pointers to functions
#include <iostream>
using namespace std;

double betsy(int);
double pam(int);
void estimate(int lines, double(*pf)(int));


int main()
{
	int code;
	cout << "How many lines of code do you need? ";
	cin >> code;
	cout << "Here's Betsy's estimate:\n";
	estimate(code, betsy);
	cout << "Here's Pam's estimate:\n";
	estimate(code, pam);
	return 0;
}

double betsy(int lns)
{
	return 0.05 * lns;
}

double pam(int lns)
{
	return 0.03 * lns + 0.0004 * lns * lns;
}

void estimate(int lines, double(*pf)(int))
{
	cout << lines << " lines will take ";
	cout << (*pf)(lines) << " hour(s)\n";
}
```
### 7.10.3 深入探讨函数指针
```C++
// 三种函数原型实际上是相同的。
const double * f1(const double ar[], int n);
const double * f2(const double [], int);
const double * f3(const double *, int);

// 声明一个指针，指向三函数之一。假设该指针名为pa，则只需将目标函数原型中的函数名替换为(*pa)
const double * (*p1)(const double *, int);
 // 声明的同时进行初始化
const double * (*p1)(const double *, int) = f1; 
// 使用C++11的自动类型推断功能时，代码要简单得多
auto p2 = f2;  // C++11 automitic type deduction
// 看看下面语句
cout << (*p1)(av,3) << ": " << *(*p1)(av, 3) << endl;
cout << p2(av, 3) << ": " << *p2(av, 3) << endl;
// ↑不带*是地址，带了*是具体的值（函数返回值）
```

#### 函数指针数组
```C++
// 声明包含3个函数指针的数组
const double * (*pa[3])(const double *, int) = {f1, f2, f3};

// 不能用auto，因为auto只能用于单值初始化，而不能用于列表初始化。
// 但声明数组pa后，声明通用类型的数组就简单了
auto pb = pa;

// 调用
const double * px = pa[0](av, 3);
const double * py = (*pb)(av, 3);
// 要获得指向的double值，可以使用运算符*
double x = *pa[0](av, 3);
double y = *(*pb[1])（av, 3）;

// 指向指针的指针，也可用单值对其进行初始化。
auto pc = &pa;  // const double *(*(*pa)[3]) (const double*, int) pc = &pa;

const double *(*(*pd)[3])(const double *, int ) = &pa;  // 指向pa的指针
*pd[3]  // an array of 3 pointers（包含3个指针的数组）
(*pd)[3]  // a pointer to an array of 3 elements（一个指向数组的指针，这数组有3个元素）

// (*pa[2])(av, 3) == f3(av, 3); 第三个元素的指针
// *pa[2](av, 3) == *f3(av, 3);  第三个元素的值 （括号的结合性优于*）
```
```
要调用函数，需要认识这样一点：既然pd指向数组，那么 *pd 就是数组，而 (*pd)[i] 是数组中的元素，即函数指针。
因此，较简单的函数调用是 (*pd)[i](av, 3) 是返回的指针指向的值。也可以使用第二种指针调用的语法：
使用(*(*pd)[i](av, 3))来调用函数，而*(*(*pd)[i](av, 3)是指向的double值
```
- [x] 这一部分也没听太懂，越来越懵了。

#### 注意pa与&pa的差别
- pa是数组名，表示地址。大多数情况下pa是数组第一个元素的地址，即&pa[0]。因此，它是单个元素的地址
- &pa是整个数组（3个元素）的地址。
- 从数字上说，pa与&pa值相同。但他们类型不同
	- 差别1：pa+1为数组中下一个元素，而&pa + 1为pa后面一个12字节内存块的地址（假定3个元素，每个4字节）
	- 差别2：要得到一个元素的值，对pa解除引用1次，而&pa需要2次。``` **&pa == *pa == pa[0] ```
```C++
// 7.19 arfupt.cpp -- an array of function pointers
#include <iostream>
using namespace std;
const double* f1(const double ar[], int n);
const double* f2(const double[], int);
const double* f3(const double*, int);

int main()
{
	double av[3] = { 1112.3, 1542.6, 2227.9 };

	// 1.函数指针及其使用
	const double* (*p1)(const double*, int) = f1;
	auto p2 = f2;  // C++11 automatic type deduction
	// pre-C++11 can use the following code instead
	// const double *(*p2)(const double *, int) = f2;
	cout << "Using pointers to functions:\n";
	cout << "Address Value\n";
	cout << (*p1)(av, 3) << ": " << *(*p1)(av, 3) << endl;
	cout << p2(av, 3) << ": " << *p2(av, 3) << endl;

	// 2.函数指针数组及其使用
	const double* (*pa[3])(const double*, int) = { f1, f2, f3 };
	auto pb = pa;
	// pre-C++11 can use the following code instead
	// const double *(**pb)(const double *, int) = pa;
	cout << "\nUsing an array of pointers to functions:\n";
	cout << " Address Value\n";
	for (int i = 0; i < 3; i++)
		cout << pa[i](av, 3) << ": " << *pa[i](av, 3) << endl;

	// 3.指向函数指针的指针
	cout << "\nUsing a pointer to a pointer to a function:\n";
	cout << " Address Value\n";
	for (int i = 0; i < 3; i++)
		cout << pb[i](av, 3) << ": " << *pb[i](av, 3) << endl;
	
	// 4.指向函数指针数组的指针
	cout << "\nUsing a pointer to an array to a function pointers:\n";
	cout << " Address Value\n";
	auto pc = &pa;
	// pre-C++11 can use the following code instead
	// const double *(*(*pc)[3])(const double *, int) = &pa;
	cout << (*pc)[0](av, 3) << ": " << *(*pc)[0](av, 3) << endl;
	const double* (*(*pd)[3])(const double*, int) = &pa;
	// store return value in pdb
	const double* pdb = (*pd)[1](av, 3);
	cout << pdb << ": " << *pdb << endl;
	// alternative notation
	cout << (*(*pd)[2])(av, 3) << ": " << *(*(*pd)[2])(av, 3) << endl;


	return 0;
}

const double* f1(const double * ar, int n)
{
	return ar;
}

const double* f2(const double ar[], int n)
{
	return ar + 1;
}

const double* f3(const double ar[], int n)
{
	return ar + 2;
}

```
- 指向函数的指针并不少见。实际上，类的虚方法实现通常都采用了这种技术（参见第13章）。所幸的是，这些细节由编译器处理。
#### 感谢auto
- auto有一个潜在缺点。自动类型推断确保变量的类型与赋给它的初值的类型一致，但您提供的初值的类型可能不对：
```C++
 auto pc = *pa;  // oops! used *pa instead of &pa
```
- 上述声明导致pc的类型与\*pa一致，在程序7.19中，后面使用它是假定其类型与&pa相同，将会导致编译错误
- [x] 真TM头疼，难理解

### 7.10.4 使用typedef进行简化
- typedef能够让你创建类型的别名。减少输入量，让您编写代码时不容易犯错，让程序更容易理解。
```C++
typedef double real;  // make real another name for double

// 用p_fun声明为程序7.19使用的函数指针类型起别名
typedef const double *(*p_fun)(const double *, int);  // p_fun now a type name
p_fun p1 = f1;  // p1 points to the f1() function
// 然后使用这个别名来简化代码
p_fun pa[3] = {f1, f2, f3};  // 包含3个元素的函数指针数组
p_fun (*pd)[3] = &pa;  /// 指向上面这个函数指针数组
```
- 跟引用有点像，都是起了个别名，只不过引用是对变量起别名，typedef是对声明变量的类型起别名

## 7.11 总结
- 定义、原型、调用
	- 函数原型描述了函数的接口：传递给函数的数目以及函数返回值类型
	- 函数调用使得程序将参数传递给函数，并执行函数的代码
- C++函数通过使用拷贝，保护了原始数据的完整性

## 7.12 复习题
- [ ] 12
```C++
// 1.声明原型、进行编写(定义函数）、最后调用

// 2.	
a. void igor(void);
b. float tofu(int);
c. double mpg(double, double);
d. long summation(long[], int);
e. double doctor(const char*);
f. void ofcourse(boss);
g. char* plot(map*);

// 3.	
void func1(int arr[], int ArSize, int n)
{
	for (int i = 0; i < ArSize; i++)
		arr[i] = n;
}

// 4.
void func2(int* begin, int* end, int n)
{
	for (; begin < end; ++first, --end)
		*begin = *end = n;
		
	for(int *pt = begin; pt != end; ++pt)
		*pt = n;
}

// 5.
double func3(const double arr[], int ArSize)
{
	double max = arr[0];
	for (int i = 1; i < ArSize; i++)
		if (arr[i] > max)
			max = arr[i];
	return max;
}
// 6. 因为对于基本类型，函数会复制一份进行使用，函数调用结束后就会将复制的这一份删除，不会影响原始的数值

// 7. 1)string 2)char数组 3)字符串常量 X
char str[] = "Hello world"; // 1)字符数组
"Hello world";  // 2）字符串常量
char *pt = "Hello world"; // 3）指向字符串首字符地址的字符指针

// 8.
int replace(char* str, char c1, char c2)
{
	int count = 0;
	
	while (*str != '\0') // while (*str)
	{
		if (*str == c1)
		{
			*str = c2;
			count++;
		}
		
		str++;
	}

	return count;
}

// 9. "pizza"是个字符串常量，含义是其首地址即字符'p'的地址，*"pizza"就是首个字符的值，即'p'

// 10. 
struct GLITZ
{
	int n;
};
void fun4(GLITZ glitz);  // 按值传递
void fun5(GLITZ*);  // 按地址传递
// 按值传递：函数会复制一份，不会对原始数据产生影响，但若结构较大，会占用较多内存。
// 按地址传递：可以直接对原始数据进行操作，节省内存，但容易破坏原始数据。

// 11.
int judge(const char* ); X
int judge(int (*pa)(const char*)); √
```
- 12
```C++
// 错误，未考虑整型数组
struct applicant {
	char name[30];
	int credit_ratings[3];
};
void show(applicant app)  // 按值传递
{
	std::cout << app.name << std::endl;
	std::cout << app.credit_ratings << std::endl;
}

void show2(applicant *app)  // 按地址传递
{
	std::cout << app->name << std::endl;
	std::cout << app->credit_ratings << std::endl;
}
```

```C++
#include<iostream>

struct applicant {
	char name[30];
	int credit_ratings[3];
};

void show(applicant app)  // 按值传递
{
	std::cout << app.name << std::endl;

	for (int i = 0; i < 3; i++)
	{
		std::cout << app.credit_ratings[i] << std::endl;
	}
}

void show(applicant* app)  // 按地址传递
{
	std::cout << app->name << std::endl;

	for (int i = 0; i < 3; i++)
	{
		std::cout << app->credit_ratings[i] << std::endl;
	}
	
}

int main()
{
	applicant app =
	{
		"a apple",
		{1, 2, 3}
	};

	show(app);
	show(&app);
}
```

- 13
```C++
// 错误
typedef void (*p1_fun)(applicant);
p1_fun p1 = f1;
typedef const char* (*p2_fun)(const applicant, const applicant);
p2_fun p2 = f2;
void (*ap[5])(applicant);
const char* (*(*p2)[10])(const applicant, const applicant);
```

```C++
// 正确
typedef void (*p1_fun)(applicant*);
typedef const char* (*p2_fun)(const applicant*, const applicant*);

p1_fun p1 = f1;  // auto p1 = f1;
p2_fun p2 = f2;  // auto p2 = f2;

p1_fun ap[5];
p2_fun (*pa)[10]; // const char* (*(*pa)[10])(const applicant, const applicant);
```

## 7.13 编程练习
- [ ] 第七题：为什么加了const begin依然可以自增
- 1
```C++
#include <iostream>
double harmonic_average(double x, double y);

int main()
{
	using namespace std;
	double x, y;
	while (true)
	{
		cout << "Please input two numbers:";
		cin >> x >> y;
		if (x == 0 || y == 0)
			break;
		else
			cout << "answer: " << harmonic_average(x, y) << endl;
	}
	cout << "Done." << endl;
	return 0;
}

double harmonic_average(double x, double y)
{
	return 2.0 * x * y / (x + y);
}
```
```C++
int main()
{
	using namespace std;
	double x, y;
	
	cout << "Please input two numbers, untill one of them is zero: ";
	cin >> x >> y;
	
	whille (x != 0 && y != 0)
	{
		cout << "result: " << harmonic_average(x, y) << endl;
		cout << "Please input two numbers, untill one of them is zero: ";
		cin >> x >> y;
	}
}
```

- 2
```C++
#include <iostream>
using namespace std;
const int ArSize = 10;
int input(double*);
void show(const double*, int);
double average(const double*, int);

int main()
{
	double grades[ArSize];
	int size = input(grades);  // fill_golf(grades);这个名字更清楚一点
	show(grades, size);
	cout << "Average: " << average(grades, size) << endl;
	
	return 0;
}

int input(double* arr)
{
	int i = 0;

	cout << "input scores and press 'q' to quit: ";
	while (i < ArSize)
	{
		if (!(cin >> arr[i++]))
		{
			i--;
			break;
		}
			
	}

	return i;
}

void show(const double* arr, int size)
{
	cout << "All scores: ";
	
	for (int i = 0; i < size; i++)
		cout << arr[i] << " ";
	
	cout << endl;
}

double average(const double* arr, int size)
{
	double sum = 0.0;

	for (int i = 0; i < size; i++)
		sum += arr[i];

	return sum / size;
}
```

```C++
int fill_golf(double* arr)
{
	cout << "input scores and press 'q' to quit: ";
	int i = 0;
	
	for (; i < ArSize; i++)
	{
		cin >> temp;
		
		if (!cin)
		{
			cin.clear();
			while(cin.get !='\n);
			cout << "Invaild input, terminate." << endl;
			break;
		}
		else if (temp < 0)
		{
			break;
		}
		else
		{
			arr[i] = temp;
		}
	}

	return i;
}
```

- 3
```C++
#include <iostream>
struct box
{
	char maker[40];
	float height;
	float width;
	float length;
	float volume;
};
using namespace std;
void ShowMembers(box B);
void SetVolume(box& B);

int main()
{
	using namespace std;
	box B = { "Zhangyh", 1.0f, 2.0f, 3.0f, 0.0f };

	SetVolume(B);
	ShowMembers(B);
	return 0;
}

void ShowMembers(box B)
{
	cout << "maker:" << B.maker << endl;
	cout << "height:" << B.height << endl;
	cout << "width:" << B.width << endl;
	cout << "length:" << B.length << endl;
	cout << "volume:" << B.volume << endl;
}

void SetVolume(box& B)
{
	B.volume = B.height * B.width * B.length;
}
```

- 4
```C++
// 7.4 lotto.cpp -- probability of winning
#include <iostream>
long double probability(unsigned numbers, unsigned picks);

int main()
{
	using namespace std;

	cout << probability(47, 5) * probability(27, 1) << endl;
	cout << "Bye\n";
	return 0;
}

long double probability(unsigned numbers, unsigned picks)
{
	long double result = 1.0;
	long double n;
	unsigned p; // = unsigned int

	for (n = numbers, p = picks; p > 0; n--, p--)
		result = result * (n / p);

	return result;
}
```

- 5
```C++
#include <iostream>

long double factorial(unsigned n);

int main()
{
	using std::cin;
	using std::cout;
	using std::fixed;
	unsigned n;

	cout << fixed;
	cout.precision(0);

	cout << "Enter  a factor: ";

	while (cin >> n)
	{
		cout << n << "! = " << factorial(n) << "\n";
		cout << "Enter  a factor: ";
	}

	return 0;
}

long double factorial(unsigned n)
{
	if (n <= 1)
	{
		return 1;
	}
	else
	{
		return n * factorial(n - 1);
	}
}
```

- 6
```C++
// vect.cpp : Defines the entry point for the console application.

#include "stdafx.h"
#include <iostream>

void FillArray(double* dNums, int& nSize);
void ShowArray(const double* dNums, const int& nSize);
void ReverseArray(double* dNums, const int& nSize);

using namespace std;

int main()
{
	int nSize;

	cout << "Enter size: ";
	cin >> nSize;
	cin.get();

	double* dNums = new double[nSize];
	FillArray(dNums, nSize);
	ShowArray(dNums, nSize);

	ReverseArray(dNums, nSize);
	cout << "after reverse: " << endl;
	ShowArray(dNums, nSize);
	
	cout << "after reverse: " << endl;
	ShowArray(dNums+1, nSize-1);
}

void FillArray(double* dNums, int& nSize)
{
	if (nSize <= 0)
	{
		return;
	}
	
	int i;
	for(i = 0; i < nSize; ++i)
	{
		cout << "Enter a double number: ";
		if (!(cin >> dNums[i]))
		{
			break;
		}
	}

	cout << "there have " << i << " nums.\n";
	nSize = i;
}

void ShowArray(const double* dNums, const int& nSize)
{
	for (int i = 0; i < nSize; ++i)
	{
		cout << dNums[i] << " ";
	}

	cout << endl;
}

void ReverseArray(double* dNums, const int& nSize)
{
	double temp;

	for (int i = 0, j = nSize-1; i < j; ++i, --j)
	{
		temp = dNums[i];
		dNums[i] = dNums[j];
		dNums[j] = temp;
	}
}
```

- 7
```C++
// 7.7 arrfun3.cpp -- array functions and const
#include <iostream>

const int Max = 5;

double* fill_array(double* begin, const double* end);
void show_array(const double* begin, const double* end);
void revalue(double r, double* begin, const double* end);

using namespace std;

int main()
{
	double properties[Max];
	double* currentEnd = fill_array(properties, properties + Max);
	show_array(properties, currentEnd);

	if ((currentEnd - properties) > 0)
	{
		double factor;
		cout << "Enter revaluation factor: ";

		while (!(cin >> factor))  // bad input
		{
			cin.clear();

			while (cin.get() != '\n');

			cout << "Bad input; Please enter a number: ";
		}

		revalue(factor, properties, currentEnd);
		show_array(properties, currentEnd);
	}
	cout << "Done.\n";
	return 0;
}

double* fill_array(double* begin, const double* end)
{
	double temp;
	int i;

	for (i = 0; (begin + i) != end; ++i)
	{
		cout << "Enter value #" << i + 1 << ": ";
		cin >> temp;

		if (!cin)
		{
			cin.clear();

			while (cin.get() != '\n');

			cout << "Bad input; input process terminated.\n";
			break;
		}
		else if (temp < 0)  // signal to terminate
		{
			break;
		}

		*(begin + i) = temp;
	}

	return (begin + i);
}

void show_array(const double* begin, const double* end)
{
	for (int i = 0; begin != end; ++i, ++begin)
	{
		cout << "Property #" << i + 1 << ": $";
		cout << *begin << endl;
	}
}

// multiplies each element of ar[] by r
void revalue(double r, double* begin, const double* end)
{
	double* ptr = begin;

	while (ptr != end)
	{
		*ptr *= r;

		++ptr;
	}
}
```

- 8
```C++
#include "stdafx.h"

// 7.15 arrobj.cpp -- functions with array objects (C++11)
#include <iostream>
#include <array>
#include <string>

// constant data
const int Seasons = 4;
// const arrya对象，包含四个string对象
const char* Snames[Seasons] = 
{ "Spring", "Summer", "Fall", "Winter" };

struct Expenses
{
	double  spend[Seasons];
} expenses;

void fill(Expenses* pa);
void show(const Expenses& da);

int main()
{
	fill(&expenses);
	show(expenses);
	return 0;
}

void fill(Expenses* pa)
{
	using namespace std;
	for (int i = 0; i < Seasons; ++i)
	{
		cout << "Enter " << Snames[i] << " expenses: ";
		cin >> pa->spend[i];
	}
}

void show(const Expenses& da)
{
	using namespace std;
	double total = 0.0;
	cout << "\nEXPENSES\n";
	for (int i = 0; i < Seasons; i++)
	{
		cout << Snames[i] << ": $" << da.spend[i] << endl;
		total += da.spend[i];
	}
	cout << "Total Expenses: $" << total << endl;
}
```

- 9
```C++
#include <iostream>
using namespace std;
const int SLEN = 30;
struct student
{
	char fullname[SLEN];
	char hobby[SLEN];
	int ooplevel;
};

int getinfo(student pa[], int n);
void display1(student st);
void display2(student* st);
void display3(const student pa[], int n);

int main(void)
{
	cout << "Enter class size: ";
	int class_size;
	cin >> class_size;
	while (cin.get() != '\n')
		continue;

	student* ptr_stu = new student[class_size];
	int extered = getinfo(ptr_stu, class_size);
	for (int i = 0; i < extered; i++)
	{
		display1(ptr_stu[i]);
		display2(&ptr_stu[i]);
	}
	display3(ptr_stu, extered);
	delete[] ptr_stu ;
	cout << "Done\n";
	return 0;
}

int getinfo(student pa[], int n)
{
	int i = 0;
	for (; i < n; ++i)
	{
		cout << "Input fullname: ";
		cin.getline(pa[i].fullname, SLEN);
		if (strcmp(pa[i].fullname, "") == 0)
		{
			break;
		}
		
		cout << "Input Hobby: ";
		cin.getline(pa[i].hobby, SLEN);
		
		cout << "Input ooplevel: ";
		cin >> pa[i].ooplevel;
		cin.get();
	}

	return i;
}

void display1(student st)
{
	cout << "display1:\n";
	cout << st.fullname << endl;
	cout << st.hobby << endl;
	cout << st.ooplevel << endl;
}

void display2(student* st)
{
	cout << "display2:\n";
	cout << st->fullname << endl;
	cout << st->hobby << endl;
	cout << st->ooplevel << endl;
}

void display3(const student pa[], int n)
{
	cout << "display3:\n";
	for (int i = 0; i < n; i++)
	{
		cout << pa[i].fullname << endl;
		cout << pa[i].hobby << endl;
		cout << pa[i].ooplevel << endl;
	}
}
```

- 10
```C++
#include<iostream>

double add(double x, double y);
double sub(double x, double y);
double multiply(double x, double y);
double divide(double x, double y);
double calculate(double x, double y, double(*func)(double, double));

int main()
{
	using namespace std;

	double x, y;

	cout << "Enter tow numbers: ";
	while (cin >> x >> y)
	{
		cout << "add: " << calculate(x, y, add) << endl;
		cout << "sub: " << calculate(x, y, sub) << endl;
		cout << "multiply: " << calculate(x, y, multiply) << endl;
		cout << "divide: " << calculate(x, y, divide) << endl;
		cout << "Enter tow numbers: ";
	}

	cout << endl;

	double (*pf[4]) (double, double) = { add, sub, multiply, divide };

	for (int i = 0; i < 4; i++)
	{
		cout << "result: " << calculate(4, 5, pf[i]) << endl;
		cout << "result: " << calculate(4, 5, *(pf + i)) << endl;
	}
}

double add(double x, double y)
{
	return x + y;
}

double sub(double x, double y)
{
	return x - y;
}

double multiply(double x, double y)
{
	return x * y;
}

double divide(double x, double y)
{
	if (x == 0 || y == 0)
	{
		return 0;
	}
	else
	{
		return x / y;
	}
}

double calculate(double x, double y, double (*func)(double, double))
{
	return func(x, y);
}
```





