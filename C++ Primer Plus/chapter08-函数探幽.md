# 第8章 函数探幽
- C++相比于C语言的新特性：
  - 内联函数
  - 按引用传递变量
  - 默认的参数值
  - 函数重载（多态）
  - 函数模板 
## 8.1 C++内联函数
- 常规函数与内联函数的区别不在于编写方式，而在于C++编译器如何将它们组合到程序中
- 内联函数的编译代码与其他程序代码“内联”起来了。也就是说，编译器将使用相应的函数代码替换函数调用。
- 调用时间与所占内存的取舍，时间与空间。
- 通常的做法是省略原型，将整个定义（即函数头和所有函数代码）放在本应提供原型的地方
  - 在函数声明前 && 函数定义前加关键字inline 
- 程序员请求将函数作为内联函数时，编译器并不一定会满足这种要求。它可能认为该函数过大或注意到函数调用了自己（**内联函数不能递归**），因此不能将其作为内联函数

```C++
// 8.1 inline.cpp -- using an inline function
#include <iostream>

// an inline function definition
inline double square(double x) { return x * x; }

int main()
{
	using namespace std;

	double a, b;
	double c = 13.0;

	a = square(5.0);
	b = square(4.5 + 7.5);  // can pass expressions
	cout << "a = " << a << ", b = " << b << endl;
	cout << "c = " << c;
	cout << ", c squared = " << square(c++) << endl;
	cout << "Now c = " << c << endl;

	return 0;
}
```
- 如果函数定义占用多行（假设没有使用冗长的标识符），将其作为内联函数就不太合适。（占内存太多）
### 内联与宏
- 宏是通过替换文本来实现的，内联是通过传递参数来实现的。宏的缺点就是不能按值传递，计算时容易出现一些问题。C++内联函数远远胜过C语言的宏定义，如果C语言的宏执行了类似函数的功能，应考虑将它们转换为C++内联函数

## 8.2 引用变量
- C++新增了一种复合类型——引用变量。引用是已定义的变量的别名（另一个名称）。
- 引用变量的主要用途是用作函数的形参。通过将引用变量用作参数，函数将使用原始数据，而不是其副本。（有点类似指针）
- 这样除了指针之外，引用也为函数处理大型结构提供了一种非常方便的途径，同时对于设计类来说，引用也是必不可少的。

### 8.2.1 创建引用变量
- 前面讲过，C和C++使用&符号来指示变量的地址。C++给&符号赋予了另一个含义，将其用来声明引用。
- 其中，&不是地址运算符，而是类型标识符的一部分。就像声明char* 指的是指向char的指针一样，int&指的是指向int的引用。
- 上述允许将rats和rodents互换，因为它们指向相同的内存单元
```C++
// 8.2 firstref.cpp -- defining and using a reference
#include <iostream>
int main()
{
	using namespace std;
	int rats = 101;
	int& rodents = rats;  // 将rodents作为rats变量的别名

	cout << "rats = " << rats;
	cout << ", rodents = " << rodents << endl;
	rodents++;
	cout << "rats = " << rats;
	cout << ", rodents = " << rodents << endl;

	cout << "rats address    = " << &rats << endl;
	cout << "rodents address = " << &rodents << endl;
	return 0;
}
```
#### 引用和指针的区别
- 表达式rodents和* prats都可以同rats互换，而表达式&rodents和prats都可以同&rats互换。
```C++
int rats = 101;
int & rodents = rats;  // rodents a reference
int * prats = &rats;
```
- 看上去引用像是伪装的指针。但是还有有差别的，其中之一就是，必须在声明引用时将其初始化，而不能像指针那样，先声明，再赋值。
- 引用更接近const指针，必须在创建时进行初始化，一旦与某个变量关联起来，就一直效忠于它。
```C++
int rat;
int & rodent;
rodent = rat;  // No, you can't do this.

int & rodents = rats;
// 实际是下述代码的伪装
int * const pr = &rats;
```

```C++
// 8.3 sceref.cpp -- defining and using a reference
#include <iostream>
int main()
{
	using namespace std;
	int rats = 101;
	int& rodents = rats;  

	cout << "rats = " << rats;
	cout << ", rodents = " << rodents << endl;

	cout << "rats address = " << &rats;
	cout << ", rodents address = " << &rodents << endl;

	int bunnies = 50;
	rodents = bunnies;  // can we change the reference?
	cout << "bunies = " << bunnies;
	cout << ", rats = " << rats;
	cout << ", rodents = " << rodents << endl;

	cout << "bunnies address = " << &bunnies;
	cout << ", rodents address = " << &rodents << endl;
	return 0;
}
```
- ``` rodents = bunnies; ``` 等价于 ``` rats = bunnies; ```也就是说，可以通过初始化声明来设置引用，但不能通过赋值来设置。
- 假如程序员这样做：
```C++
int rats = 101;
int * pt = &rats;
int & rodents = *pt;  // pt必须经过初始化，否则编译器报错，程序无法正常运行
int bunnies = 50;
pt = &bunnies;
```
- 将rodents初始化为* pt使得rodents指向rats，接下来pt指向bunnies。但是rodents引用的还是rats。
### 8.2.2 将引用用作函数参数
- 引用经常用作函数参数，使得函数中的变量名成为调用程序中的变量别名。这种传递参数的方法称为按**引用传递**。
- 这是对C语言的超越，C语言只能按值传递。按值传递导致被调用函数使用调用程序的值的拷贝。当然，C语言也允许避开按值传递的限制，采用按指针传递的方式。
```C++
// 交换两个变量，比较按引用传递、按指针传递和按值传递：
// 8.4 swaps.cpp -- swapping with references and with pointers
#include <iostream>
void swapr(int& a, int& b);  // a, b are aliases for ints
void swapp(int* p, int* q);  // p, q are addresses of ints
void swapv(int a, int b);  // a, b are new variables

int main()
{
	using namespace std;
	int wallet1 = 300;
	int wallet2 = 350;
	cout << "wallet1 = $" << wallet1;
	cout << " wallet2 = $" << wallet2 << endl;

	cout << "Using reference to swap contents:\n";
	swapr(wallet1, wallet2);
	cout << "wallet1 = $" << wallet1;
	cout << " wallet2 = $" << wallet2 << endl;

	cout << "Using pointers to swap contents again:\n";
	swapp(&wallet1, &wallet2);
	cout << "wallet1 = $" << wallet1;
	cout << " wallet2 = $" << wallet2 << endl;

	cout << "Tyring to use passing by value:\n";
	swapv(wallet1, wallet2);
	cout << "wallet1 = $" << wallet1;
	cout << " wallet2 = $" << wallet2 << endl;
	return 0;
}

void swapr(int& a, int& b)
{
	int temp;

	temp = a;
	a = b;
	b = temp;
}

void swapp(int* p, int* q)
{
	int temp;

	temp = *p;
	*p = *q;
	*q = temp;
}

void swapv(int a, int b)
{
	int temp;

	temp = a;
	a = b;
	b = temp;
}

```
- 前面说过，应在定义引用变量时对其进行初始化。函数调用使用实参初始化形参，因此函数的引用参数被初始化为函数调用传递的实参。

### 8.2.3 引用的属性和特别之处
```C++
// 8.5 cubes.cpp -- regular and reference arguments
#include <iostream>
inline double cube(double a)
{
	a *= a * a;
	return a;
}
inline double refcube(double& ra)
{
	ra *= ra * ra;
	return ra;
}
int main()
{
	using namespace std;
	double x = 3.0;

	cout << cube(x);
	cout << " = cube of " << x << endl;
	cout << refcube(x);
	cout << " = refcube of " << x << endl;
	return 0;
}
```
- 如果想使用引用，又不想对信息进行修改，应该在函数原型和函数头中使用const：``` double refcube(const double &ra); ```。这时候，如果编译器发现代码修改了ra的值，将生成错误消息。
- 其实上面这个函数，最好使用按值传递
- 对于引用来说，传递表达式是不行的``` double z = refcube(x + 3.0);  // should not compile```，**因为表达式不是变量**，就像``` x + 3.0 = 5.0 // nonsensical ```一样不合理。
- 早期的C++允许将表达式传递给引用变量。有些情况下，仍然是这样做的。这样做的结果如下：由于x + 3.0不是double类型的变量，因此程序将创建一个临时的无名变量，并将其初始化为表达式x+3.0的值。然后，ra将成为该临时变量的引用。（有点像i++与++i的区别）下面详细讨论。
#### 临时变量、引用参数和const
- 什么时候创建临时变量？如果引用参数是const，则编译器将在下面两种情况下生成临时变量：
	- 实参的类型正确，但不是左值；
	- 实参的类型不正确，但可以转换为正确的类型。

- 什么是左值？
	- 左值参数是可被引用的数据对象，例如：变量、数组元素、结构成员、引用和接触引用的指针都是左值。
	- 非左值：字面量（用引号括起的字符串除外，它们由其地址表示）和包含多项的表达式。
	- 以前：在C语言中，左值最初指的是可出现在赋值语句左边的实体，但这是引入关键字const之前的情况。
	- 现在：常规变量和const变量都可视为左值，因为可通过地址访问它们。但常规变量属于可修改的左值，而const属于不可修改的左值。
```C++
double refcube(const double& ra)
{
	return ra * ra * ra;
}

double side = 3.0;
double* pd = &side;
double& rd = side;
long edge = 5L;
double lens[4] = { 2.0, 5.0, 10.0, 12.0 };
double c1 = refcube(side);  // ra is side
double c2 = refcube(lens[2]);  // ra is lens[2]
double c3 = refcube(rd);  // ra is rd is side
double c4 = refcube(*pd);  // ra is *pd is side
double c5 = refcube(edge);  // ra is temporary variable
double c6 = refcube(7.0);  // ra is temporary variable
double c7 = refcube(side + 10.0); // ra is temporary variable
```
- 参数side、lens[2]、rd和\*pd都是有名称的、double类型的数据对象，因此可以为其创建引用，而不需要临时变量。但是，edge虽然是变量，类型却不正确，double引用不能指向long。另一方面，参数7.0和side+10.0的类型都正确，但是没有名称，在这些情况下，编译器都将生成一个临时匿名变量，并让ra指向它。这些临时变量只在函数调用期间存在，此后编译器便可以随意将其删除。

- 为什么对于常量引用，这种行为是可行的，但是其他情况下却不行呢？对于程序8.4中的函数swapr()
```C++
void swapr(int & a, int & b)  // use references
{
	int temp;
	
	temp = a;
	a = b;
	b = temp;
}

long a = 3, b = 5;
swapr(a, b);
```
- ↑ 这里类型不匹配，编译器创建两个临时int类型变量，将它们交换。（太傻b了）而a, b保持不变。
- 意思就是，你不能直接修改原始变量。你修改的只是临时变量。等于没引用，跟按值传递差不多。
#### 应尽可能使用const
- 将引用参数声明为常量数据的引用的理由有三个：
	- 使用const可以避免无意中修改数据的编程错误
	- 使用const使函数能够处理const和非const实参，否则将只能接受非const数据
	- 使用const引用函数能够正确生成并使用临时变量

- C++新增了**右值引用**(rvalue reference)。这种引用可指向右值，是使用&&声明的：
```C++
double && rref = std::sqrt(36.0);  // not allowed for double &
double j = 15.0;
double && jref = 2.0 * j + 18.5;  // not allowed for double &
std::cout << rref << '\n';  // display 6.0
std::cout << jref << '\n';  // display 48.5
```
- 第18章讨论使用右值引用来实现移动语义（move semantics）

### 8.2.4 将引用用于结构
- 引用非常适合用于结构和类（C++用户定义类型），引用主要也用于这些类型。

```C++
#include <iostream>
#include <string>
using namespace std;

struct free_throws
{
	string name;
	int made;
	int attempts;
	float percent;
};
void display(const free_throws& ft);
void set_pc(free_throws& ft);
free_throws& accumulate(free_throws& target, const free_throws& source);

int main()
{

	return 0;
}

void display(const free_throws& ft);
void set_pc(free_throws& ft);
free_throws& accumulate(free_throws& target, const free_throws& source);
```















