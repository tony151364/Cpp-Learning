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
- 处于标准化的目的，C++标准使用参数(argument)来表示实参，使用参量(parameter)来表示形参，因此参数传递，将parameter赋给argument。
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

### 7.3.1 将数组作为参数意味着什么



