
# 第9章 内存模型和名称空间
- C++为在内存中存储数据方面提供了多种选择。可以选择保留在内存中的时间长度（存储连续性）以及程序的那一部分可以访问数据（作用域和链接）等。
- C++名称空间是另一种控制访问权的方式。通常，大型程序都有多个源文件代码组成，这些文件可能共享一些数据。这样的程序涉及到程序文件的单独编译。


## 9.1 单独编译
- 和C语言一样，C++也允许甚至鼓励程序员将组件函数放在独立的文件中，这样管理更加方便。当一个文件修改时，只需重新编译该文件即可，链接器再将其重新链接。
- 将多个文件所共用的内容，如结构体放置再头文件中，要修改结构声明时，只需要修改头文件即可。
- 另外，也可以将函数原型放在头文件中。因此，可以将原来的程序分为三部分：
    - 头文件：包含结构声明和使用这些结构的函数的原型。
    - 源代码文件：包含与结构有关的函数的代码。
    - 源代码文件：包含调用与结构相关的函数的代码。（main）
- 这种组织方式也与OOP方法一致
    - 一个文件（头文件）包含了用户定义类型的定义。
    - 另一个文件包含操纵用户定义类型的函数的代码。
    - 这两个文件组成了一个软件包，可用于各种程序中。
- [ ] 不要将函数定义或变量声明放在头文件中。例如，如果在头文件包含一个函数定义，两个文件若都包含该函数，最后形成的程序将包含同一函数的两个定义，除非这函数是内联的，否则就会出错。
- 头文件常包含的内容：
    - 函数原型
    - 使用#define或const定义的符号常量。const有特殊的链接属性
    - 结构声明。这是可以的，因为它们不创建变量，在源代码文件声明时才创建
    - 类声明。
    - 模板声明。不是将被编译的代码，它们指示编译器如何生成与源代码中的函数调用相匹配的函数定义。
    - 内联函数 。有特殊链接属性

- [ ] 注意：只需将源代码文件加入到项目中，而不用加入头文件。这是因为#include指令管理头文件。
- [ ] 注意：不要使用#include来包含源代码文件，这样做将导致多重声明。
- [ ] 警告：在IDE中，不要将头文件加入到项目列表中，也不要在源代码文件使用#include来包含其他源代码文件
```C++
// 9.1coordin.h -- structure templates and function prototypes
// structure templates
#ifndef COORDIN_H_
#define COORDIN_H_

struct polar
{
	double distance;  // distance from origin
	double angle;  // direction from origin
};

struct rect
{
	double x;  // horizontal distance from origin
	double y;  // vertical distance from origin
};

// prototypes
polar rect_to_polar(rect xypos);
void show_polar(polar dapos);

#endif
```

```C++
// 9.2 file1.cpp -- example of a three-file program
#include <iostream>
#include "coordin.h"  // structure templates, function protorypes
using namesapce std;
int main()
{
	rect rplace;
	polar pplace;
	
	cout << "Enter the x and y values: ";
	while (cin >> rplace.x >> rplace.y)  // slick use of cin
	{
		pplace = rect_to_polar(rplace);
		show_polar(pplace);
		cout << "Next two numbers (q to qiut): ";
	}
	cout << "Bye!\n";
	return 0;
}
```
```C++
// 9.3 file2.cpp -- contains functions called in file1.cpp
#include <iostream>
#include <cmath>
#include "coordin.h"  // structure templates, function protorypes

// convert rectangular to polar coordinates
polar rect_to_polar(rect xypos)
{
	using namespace std;
	polar answer;
	
	answer.distance = sqrt( xypos.x * xypos.x + xypos.y * xypos.y);
	answer.angle = atan2(xypos.y, xypos.x);
	return answer;  // returns a polar structure
}

// show polar coordinates, converting angle to degrees
void show_polar (polar dapos)
{
	using namespace std;
	const double Rad_to_deg = 57.29577951;
	
	cout << "distance = " << dapos.distance;
	cout << ", angle = " << dapos.angle * Rad_to_deg;
	cout << " degrees\n";
}
```

- 虽然我们讨论的是根据文件进行单独编译，但为保持通用性，C++标准使用了术语翻译单元(translation unit)，而不是文件；文件并不是计算机组织信息时的唯一方式。出于简化的目的，本书使用术语文件，你可将其解释为翻译单元。

- 多个库链接时，可能因为函数生成的修饰名称不同而引发错误，使得链接器无法将一个编译器生成的函数调用与另一个编译器生成的函数定义匹配。

## 9.2 存储持续性、作用域和链接性
- 存储类别如何影响信息在文件间的共享？
- C++11中的四种存储方案：
	- 自动存储持续性：函数定义中声明的变量（包括函数参数）的在程序开始执行时被创建，结束时所占内存被释放。C++有两种存储持续性为自动的变量。
	- 静态存储持续性：在函数定义外定义的变量和使用关键字static定义的变量的存储持续性都为静态。它们在程序整个运行过程中都存在，C++有3种存储持续性为静态的变量。
	- 线程存储持续性(C++11)：当前，多核处理器很常见，不同线程。如果变量使用关键字thread_local声明的，则其生命周期与所属的线程一样长。
	- 动态存储持续性：用new运算符分配的内存将一直存在，直到使用delete运算符将其释放或程序结束为止。

### 9.2.1 作用域和链接
- 作用域：略
- 链接性(linkage)：描述了名称如何在不同单元间共享。链接性为外部的名称可以在文件间共享，链接性为内部的名称只能由一个文件中的函数共享。自动变量的名称没有链接性，因此它们不能共享。
- 作用域：作用域为全局（也叫文件作用域）的变量在定义位置到文件末尾之间都可用。自动变量的作用域为局部，静态变量的作用域是全局还是局部取决于它是如何被定义的。

- C++函数的作用域可以是整个类或整个名称空间（包括全局的），但不能是局部的（因此不能在代码块内定义函数，如果函数的作用域为局部，则只对它自己是可见的，因此不能被其他函数调用）
- 不同的C++存储方式是通过存储连续性、作用域和链接性来描述的。

### 9.2.2 自动存储持续性
- 在默认情况下，函数中声明的函数参数和变量的存储持续性为自动，作用域为局部，没有链接性
```C++
// 9.4 autoscp.cpp -- illustrating scope of automatic variables
#include <iostream>
using namespace std;

void oil(int x);

int main()
{
	int texas = 31;
	int year = 2011;
	
	cout << "In main(), texas = " << texas << ", &texas = ";
	cout << &texas << endl;
	cout << "In main(), year = " << year << ", &year = ";
	cout << &year << endl;
	
	oil(texas);
	
	cout << "In main(), texas = " << texas << ", &texas = ";
	cout << &texas << endl;
	cout << "In main(), year = " << year << ", &year = ";
	cout << &year << endl;
	return 0;
}

void oil(int x)
{
	int texas = 5;

	cout << "In oil(), texas = " << texas << ", &texas = ";
	cout << &texas << endl;
	cout << "In oil(), x = " << x << ", &x = ";
	cout << &x << endl;
	{
		int texas = 113;
		cout << "In block, texas = " << texas;
		cout << ", &texas = " << &texas << endl;
		cout << "In block, x = " << x << ", &x = ";
		cout << &x << endl;
	}  // end a block
	cout << "Post-block texas = " << texas;
	cout << ", &texas = " << &texas << endl;
}
```

#### 使用C++11中的auto
##### 1.自动变量的始化
- 可以使用任何在声明时其值为已知的表达式来初始化自动变量，下面的示例初始化变量x、y和z；
```C++
int w;  // value of w is indeterminate
int x = 5;  // initialized with a numeric literal
int big = INT_MAX - 1;  // initialized with a constant expression
int y = 2 * x;  // use previously determined value of x
cin >> w;
int z = 3 * w;
```
##### 2.自动变量和栈
- 栈的默认长度取决于实现，但编译器通常提供改变栈长度的选项。程序使用两个指针来追踪栈，一个指针指向栈底——栈开始的位置，另一个指针指向堆顶——下一个可用内存单元。

##### 3.寄存器变量
- 关键字register最初是由C语言引入的，它建议编译器使用CPU存储器来存储自动变量：``` register int count_fast;  // request for a register variable ``` 旨在提高变量的访问速度
- 在C++11中，它只是显式的指出变量是自动的。
- [ ] 自动变量到底什么意思？

### 9.2.3 静态持续变量
- C/C++为静态存储持续性变量提供了3种链接性：
	- 外部链接性（可在其他文件中访问）
	- 内部链接性（只能在当前文件中访问）
	- 无链接性（只能在当前函数或代码块中访问）
- 由于静态变量的数目在程序运行期间是不变的，因此程序不需要使用特殊的装置（如栈）来管理它们。编译器将分配固定的内存块来存储所有的静态变量，这些变量在整个程序执行期间一直存在。
- 另外，如果没有显式的初始化静态变量，编译器将把它设置为0。在默认情况下，静态数组和结构将每个元素或成员的所有位都设置为0.
```C++
int global = 1000;  // static duration, external linkage
static int one_file = 50;  // static duration, internal linkage
int main()
{
···
}

void funct1(int n)
{
	static int count = 0;  // static duration, no linkage
	int llama = 0;
···
}
void funct2(int q)
{
···
}
```
- 所有静态持续变量（上述示例中的global、one_file和count）在整个程序执行期间都存在。one_file的链接性为内部，因此只能在包含上述代码的文件中使用它；global的链接性为外部，因此可以在程序的其他文件中使用它。
- [ ] 关键字重载

#### 静态变量的初始化
- 除默认的零初始化外，还可对静态变量进行表达式初始化和动态初始化。
- 零初始化和常量表达式初始化被统称为**静态初始化**，这意味着编译器处理文件（翻译单元）时初始化变量。动态初始化意味着变量将在编译后初始化。
- 初始化形式的决定因素：首先，所有静态变量都被零初始化，而不管程序员是否显式的初始化了它。接下来，如果使用常量表达式初始化了变量，且编译器仅根据文件内容（包括被包含的头文件）就可计算表达式，编译器将执行常量表达式初始化。必要时，编译器将执行简单计算。如果没有足够的信息，变量将被动态初始化。
```C++
#include <cmath>
int x;  // zero-initialization
int y = 5;  // constant-expression initialization
long z = 13 * 13;  // constant-expression initialization
const double pi = 4.0 * atan(1.0);  // dynamic initialization
```

### 9.2.4 静态持续性、外部链接性
- 链接性为外部的变量通常称为外部变量，它们的存储持续性为静态，作用域为整个文件。外部变量也称为全局变量
#### 1.单定义规则
- 单定义规则(One Definition Rule, ODR)，该规则指出，变量只能有一次定义。为满足这种需求，C++提供了两种变量声明：
	- **定义声明**(defining declaration)或简称为定义(deffinition)，它给变量分配存储空间；
	- **引用声明**（reference declaration）或简称为声明(declaration)，它不给变量分配存储空间，因为它引用已有的变量。 

- 引用声明使用关键字extern，且不进行初始化；否则，声明为定义，导致分配存储空间
```C++
double up;    // definition, up is 0;
extern int blem;  // blem define elsewhere
extern char gr = 'z';  // definition because initialized
```
- 如果要在多个文件中使用外部变量，只需在一个文件中包含该变量的定义（单定义规则） ，但在使用该变量的其他所有文件中，都必须使用关键字extern声明它：
```C++
// file01.cpp
extern int cats = 20;  // definition because of initialization
int dogs = 22;  // also a definition
int fleas;  // alse a definition
...
// file02.cpp
// use cats and dogs from file01.cpp
extern int cats;  // not definitions because they use
extern int dogs;  // extern and have no initialization
···
// file98.cpp
// use cats, dogs, and fleas from file01.cpp
extern int cats;
extern int dogs;
extern int fleas;
```
- 请注意，单定义规则并非意味着不能有多个变量的名称相同。
- 如果在函数中声明了一个与外部变量同名的变量，结果将如何呢？这种声明被视为一个自动变量的定义，当程序执行自动变量所属的函数时，该变量将位于作用域内。

```C++
// 9.5 autoscp.cpp -- illustrating scope of automatic variables compile with support.cpp
#include <iostream>
#include "support.cpp"
using namespace std;
double warming = 0.3;  // warming defined

void update(double dt);
void local();

int main()  // uses global variable
{
	cout << "Global warming is " << warming << " degrees.\n";
	update(0.1);  // call function to change warming
	cout << "Global warming is " << warming << " degrees.\n";
	local();  // call function to change warming
	cout << "Global warming is " << warming << " degrees.\n";
	return 0;
}
```

```C++
// 9.6 support.cpp -- use external variable compile with external.cpp
#include <iostream>
extern double warming;  // use warming from another file

void update(double dt);
void local();

using std::cout;

void update(double dt)  // modifies global variable
{
	extern double warming;  // optional redeclaration
	warming += dt;  // uses global warming
	cout << "Updating global warming to " << warming;
	cout << " degress.\n";
}

void local()  // use local variable
{
	double warming = 0.8;  // new variable hides external one

	cout << "Local warming = " << warming << " degrees.\n";
	// Access global variable with the scope resolution operator
	cout << "But global warming = " << ::warming;  // ↓
	cout << " degrees.\n";
}
```
- C++比C语言更进了一步——它提供了作用域解析运算符(::)。放在变量前面时，该运算符表示使用变量的全局版本。因此，local()讲warming显式为0.8，但将::warming显式为0.4.

#### 全局变量和局部变量
```C++
const char* const months[12] = 
{
	"January", "February", "March", "April", "May",
	"June", "July", "August", "September", "October",
	"November", "December"
};
```
- 在上述示例中，第一个const防止字符串被修改，第二个const确保数组中每个指针始终指向它最初指向的字符串。

### 9.2.5 静态持续性、内部链接性

















