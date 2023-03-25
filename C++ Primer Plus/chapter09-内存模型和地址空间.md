
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
- [x] 关键字重载，即关键字的含义取决于上下文

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
// 9.5 external.cpp -- illustrating scope of automatic variables compile with support.cpp
#include <iostream>
#include "support.h"

using namespace std;

double warming = 0.3;

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
// 9.6.5 support.h 
#pragma once

#ifndef _SUPPORT_H_
#define _SUPPORT_H_

extern double warming;

void update(double dt);
void local(void);

#endif

```

```C++
// 9.6 support.cpp -- use external variable compile with external.cpp

#include "support.h"
#include <iostream>

using namespace std;

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
- C++比C语言更进了一步——它提供了作用域解析运算符(::)。放在变量前面时，该运算符表示使用变量的全局版本。因此，local()中warming显式为0.8，但将::warming显式为0.4.

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

- **注意**： 在多个文件程序中，可以在一个文件（且只能在一个文件）中定义一个外部声明。使用该变量的其他文件必须使用关键字extern声明它
- 可使用外部变量在多文件程序的不同部分共享数据；可使用链接性为内部的静态变量在同一个文件中的多个函数之间共享数据（名称空间提供了另外一种共享数据的方法）。另外，如果将作用域为整个文件的变量设置为静态的，就不必担心其名称与其他文件中的作用域为整个文件的变量发生冲突。
```C++
// 9.7 twofile1.cpp -- variables with external and internal linkage
#include <iostream>
int tom = 3;
int dick = 30;
static int harry = 300;

void remote_acess();

int main()
{
	using namespace std;

	cout << "main() reports the following addresses:\n";
	cout << &tom << " = &tom, " << &dick << " = &dick, ";
	cout << &harry << " = &harry\n";
	remote_acess();
	return 0;
}
```

```C++
// 9.8 towfile2.cpp -- variable with internal and external linkage
#include<iostream>
extern int tom;
static int dick = 10;
int harry = 200;

void remote_acess()
{
	using namespace std;
	cout << "remote_acess() reports the following address:\n";
	cout << &tom << " = &tom, " << &dick << " = &dick, ";
	cout << &harry << " = &harry\n";
}
```

### 9.2.6 静态存储持续性、无链接性
- 静态变量适用于再生
- 如果初始化了静态局部变量，则程序只在启动时进行一次初始化。以后再调用函数时，将不会像自动变量那样再次被初始化。
```C++
// 9.9 static.cpp -- using a static local variable
#include <iostream>
const int ArSize = 10;

void strcount(const char* str);

int main()
{
	using namespace std;

	char input[ArSize];
	char next;

	cout << "Enter a line:\n";
	cin.get(input, ArSize);
	while (cin)  // 使用get(char*, int) 读取空行将导致cin为false
	{
		cin.get(next);
		while (next != '\n')
		{
			cin.get(next);
		}
		strcount(input);
		cout << "Enter next line (empty line to quit):\n";
		cin.get(input, ArSize);
	}
	cout << "Bye\n";
	return 0;
}

void strcount(const char* str)
{
	using namespace std;
	static int total = 0;
	int count = 0;

	cout << "\"" << str << "\" contains ";
	while (*str++)
	{
		count++;
	}
	total += count;
	cout << count << " characters\n";
	cout << total << " characters total\n";
}
```

### 9.2.7 说明符和限定符
- 存储说明符
	- auto（在C++11中不再是说明符，而是自动类型推断）
	- register（C++11只是指出变量是自动的）
	- static
	- extern
	- thread_local（C++11新增的，指出变量的持续性与其所属线程的持续性相同。该变量之于线程，犹如常规静态变量之于整个程序）
	- mutable
- 在同一个声明中不能使用多个说明符，但thread_local除外，它可与static或extern结合使用

#### 1.cv-限定符
- const
- volatile（表明，即使程序代码没有对内存单元进行修改，其值也可能发生变化）

#### 2. mutable
- 可以用它来指出，即使结构（或类）变量为const，其某个成员也可以被修改，
```C++
struct data
{
	char name[30];
	mutable int access;
	...
}
```

#### 3. 再谈const
- 在默认情况下全局变量的链接性为外部的，但是const全局变量的链接性为内部的。也就是说，在C++看来，全局const就像使用了static说明符一样、
```C++
const int fingers = 10;  // same as static const int fingers = 10;
```
- [ ] 我加了const，每个文件都有一份自己的const，就可以在所用文件中使用相同的声明。否则，只有一个文件可以包含前面的声明，其他文件需要extern，就很麻烦。？

- 如果希望某个常量的链接性为外部的，可以使用关键字extern来覆盖默认的内部链接性。
```C++
extern const int fingers;   // can't be initialized
extern const int states = 50;  // definition with extern linkage
```

### 9.2.8 函数和链接性
- C/C++ 不允许在一个函数中定义另外一个函数，因此所有的函数的存储持续性都自动为静态的。
- 默认情况下，函数的链接性为外部的，即可以在文件间共享。实际上，可以在函数原型中使用关键字extern来指出函数时在另一个文件中定义的。
- 还可以使用关键字static将函数的链接性设置为内部的，使之只能在一个文件中使用。
```C++
// 必须在原型和函数定义中使用该关键字
static int private(double x);
...
static int private(double x)
{
	...
}
```
- 这还意味着可以在其他文件中定义同名的函数。和变量一样在定义静态函数的文件中，静态函数将覆盖外部定义，因此即使在外部定义了同名的函数，该文件仍将使用静态函数。
- 单定义规则也适用于飞内联函数，因此对于每个非内联函数，程序只能包含一个定义。对于链接性为外部的函数来说，这意味着在多文件程序中，只能有一个文件（该文件可能是库文件）包含该函数的定义，但使用该函数的每个文件都应包含其函数原型。
- [ ] 内联函数不受这项规则的约束，这允许程序员能够将内联函数的定义放在头文件中。这样，包含了头文件的每个文件都有内联函数的定义。然而，C++要求同一个函数的所有内联定义都必须相同
#### C++在那里查找函数
- 如果定义了一个与库函数同名的函数，编译器将使用程序员定义的版本。

### 9.2.9 语言的链接性
- 语言链接性（language linking）也对函数有影响。

```C++
extern "C" void spiff(int);  // use C protocol for name look-up
extern void spiff(int);  // use C++ protocol for name look-up
extern "C++" void spiff(int);  // use C++ protocol for name look-up
```

### 9.2.10 存储方案和动态分配
- 动态内存由运算符new和delete控制，而不是由作用域和链接性规则控制。因此，可以在一个函数中分配动态内存，而在另一个函数中将其释放
- 虽然存储方案概念不适用于动态内存，但适用于用来追踪动态内存的自动和静态指针变量。
```C++
float* p_fees = new float [20];  // 语句块用完指针可能消失了，但内存中还有，别的函数想用待有地址
```
- 如果将p_fees的链接性声明为外部的，则文件中位于该声明后面的所有函数都可以使用它。
```C++
// 在另一个文件做个声明，就能用该指针
extern float* p_fees;
```

#### 1.使用new运算符初始化
```C++
struct whrere
{
	double x;
	double y;
	double z;
}

where* ont = new where {2.5, 5.3, 7.2};  // C++11

int * ar = new int [4] {2, 4, 6, 7};  // C++11

int* pin = new int {6};  // *pi set to 6
double * pdo = new double {99.99};  // *pdo set to 99.99
```

#### 2. new失败时
- new可能找不到请求的内存量。在最初的10年中，C++在这种情况下让new返回空指针，但现在将引发异常 std::bad_alloc。

#### 3.new：运算符、函数和替换函数
- 分配函数(allocation function)与释放函数(deallocation function),它们位于全局名称空间中。
```C++
// 运算符new和new [] 分别调用如下函数
void * operator new(std::size_t);  // 运算符重载
void * operator new[](std::size_t);

void * operator delete(void *);  // 运算符重载
void * operator delete[](void *);
```
- std::size_t 是一个typedef，对应于合适的类型
```C++
int* pi = new int;  // 被转换为：
int* pi = new(sizeof(int));    // new包含初始值时就不仅仅调用new()了。

int* pa = new int [40];  // 被转换为
int* pa = new(40 * sizeof(int));

delete pi;  // 被转换为
delete (pi);
```
- C++将这些函数称为可替换的（replaceable）。意味着你可以为new和delete提供自己定制的函数。（牛逼！）

#### 4.定位new运算符

- 定位(placement)； new的4种用法：
```C++
// 9.9
#include <new>

struct chaff
{
	char dross[20];
	int slag;
};

char buffer1[50];
char buffer2[500];

int main()
{
	chaff *p1, *p2;
	int *p3, *p4;
	
	// first, the regular forms of new
	p1 = new chaff;
	p3 = new int[20];

	// now, the two forms of placement new
	p2 = new (buffer1) chaff;  // place structure in buffer1
	p2 = new (buffer2) int [20];  // place int array in buffer2
}
```

```C++
// 9.10 newplace.cpp -- using placement new
#include <iostream>
#include <new>

const int BUF = 512;
const int N = 5;
char buffer[BUF];
int main()
{
	using namespace std;
	double* pd1, * pd2;
	int i;

	cout << "Calling new and placement new:\n";
	pd1 = new double[N];  // use heap
	pd2 = new (buffer) double[N];  // use buffer array
	for (i = 0; i < N; ++i)
	{
		pd2[i] = pd1[i] = 1000 + 20.0 * i;
	}

	cout << "Memory address:\n" 
		<< " heap: " << pd1
		<< " stack: " << (void*)buffer << endl;
	cout << "<Memory contents:\n";
	for (i = 0; i < N; i++)
	{
		cout << pd1[i] << " at " << &pd1[i] << "; ";
		cout << pd2[i] << " at " << &pd2[i] << endl;
	}

	cout << "\nCalling new and placement new a second time:\n";
	double* pd3, * pd4;
	pd3 = new double[N];  
	pd4 = new (buffer) double[N];  // overwrite old data
	for (i = 0; i < N; ++i)
	{
		pd3[i] = pd4[i] = 1000 + 40.0 * i;
	}

	cout << "<Memory contents:\n";
	for (i = 0; i < N; i++)
	{
		cout << pd3[i] << " at " << &pd3[i] << "; ";
		cout << pd4[i] << " at " << &pd4[i] << endl;
	}

	cout << "\nCalling new and placement new a third time:\n";
	delete[] pd1; 
	pd1 = new double[N];
	pd2 = new (buffer + N * sizeof(double)) double[N];
	for (i = 0; i < N; ++i)
	{
		pd1[i] = pd2[i] = 1000 + 60.0 * i;
	}

	cout << "<Memory contents:\n";
	for (i = 0; i < N; i++)
	{
		cout << pd1[i] << " at " << &pd1[i] << "; ";
		cout << pd2[i] << " at " << &pd2[i] << endl;
	}
	
	delete[] pd1;
	delete[] pd3;
	return 0;
}
```
##### 5.程序说明
- 1.p1 是double指针，而buffer是char指针（顺便说一句，这也是程序使用(void*) 对buffer进行强制转换的原因，如果不这样做，cout将显示一个字符串。（因为cout对于字符型地址的处理就是显示这个字符串）
- 2.定位new运算符使用传递给他的地址，她不追踪那些内存单元已被使用，也不查找未使用的内存块。这将一些内存管理的负担交给了程序员。
- 3.buffer位于delete的管辖区域以外。如果buffer使用常规new运算符创建，便可以使用常规delete运算符来释放整个内存。

##### 6.定位new的其他形式
- 就像常规new调用一个接收一个参数的new()函数一样，标准定位new调用一个接受两个参数的new()函数
```C++
int * p1 = new int;  // invokes new(sizeof(int))
int * p2 = new(buffer) int;  // invokes new(sizeof(int), buffer)
int * p3 = new(buffer) int [40];  // invokes new(40*sizeof(int), buffer) 
```
- 定位new函数不可替换，但可重载。它至少需要接收两个参数，其中第一个总是std::size_t，指定了请求的字节数。这样的函数都被称为定义new，即使额外的参数没有指定位置。

## 9.3 名称空间
- 在C++中，名称可以是变量、函数、结构、枚举、类以及类和结构的成员

### 9.3.1 传统的C++名称空间
- 潜在作用域(potential scope)比声明区域(declaration region)

### 9.3.2 新的名称空间特性
- 名称空间可以是全局的，也可以位于另一个名称空间中，但不能位于代码块中。因此，在默认情况下，在名称空间中声明的名称的链接性为外部的（除非它引用了常量）
- 全局名称空间(global namespace)
- 名称空间是开发的，即可以把名称加入到已有的名称空间中

#### 1.using声明和using编译指令
- using声明使特定的标识符可用，using编译指令使整个名称空间可用。
- 全局的前面会加作用域解析运算符(::)
- 编译声明后，不能再重命名
```C++
namespace Jill {
	double bucket (double n) { ... }
	double fetch;
	struct Hill {...};
}

char fetch;
int main()
{
	using Jill::fetch;  // put fetch into local namespace
	double fetch;  // Error! Already have a local fetch
	cin >> fetch;  // read a value into Jill::fetch
	cin >> ::fetch // read a value into global fetch  
}
```
- 在函数的外面使用using声明时，将把名称添加到全局名称空间中。
- 编译器不允许您同时使用下述两个using声明，因为这会导致二义性
```C++
using jack::pal;
using jill::pal;
pal = 4;  // which one? now have a conflict
```

#### 2.using编译指令和using声明之比较
- 在下面的示例中，名称空间为全局的。如果使用using编译指令导入一个已经在函数中声明的名称，则局部名称将隐藏命名空间名称
```C++
namespace Jill {
	double bucket (double n) { ... }
	double fetch;
	struct Hill {...};
}

char fetch;
int main()
{
	using namespace Jill;  // import all namespace names
	Hill Thrill;  // create a type Jill::Hill structure
	double water = bucket(2);  // use Jill::bucket();
	double fetch;  // not an errorl hides Jill::fetch
	cin >> fetch;  // read a value into the local fetch
	cin >> ::fetch // read a value into global fetch
	cin >> Jill::fetch;  // read a value into Jill::fetch
	...  
}

int foom()
{
	Hill top;  // ERROR
	Jill::Hill crest;  // valid
}
```

- **注意：** 假设名称空间和声明区域定义了相同的名称。如果试图使用using声明将名称空间的名称导入该声明区域，则这两个名称会发生冲突，从而出错。如果使用using编译指令将该名称空间的名称导入该声明区域，则局部版本将隐藏名称空间版本。
- 一般来说，使用using声明比使用using编译指令更安全，这是由于它只导入特定的名称。而且重名时，编译器会报错。

- 下面的实例：首先，#include语句将头文件iostream放到名称空间std中。然后，using编译指令是该名称空间在main()函数中可用
```C++
#include <iostream>
int main()
{
	using namespace std;
}
```

#### 3.名称空间的其他特性
- （1）可以将名称空间进行嵌套
- （2）也可以在名称空间中使用using编译指令和using声明

```C++
namespace myth
{
	using Jill::fetch;
	using namespace elements;
	using std::cout;
	using std::cin;
}

std::cin  >> myth::fetch;
std::cout << Jill::fecth;  // display value read into myth::fetch

// 如果没有与之冲突的变量，则也可以这样做：
using namespace myth;
cin >> fetch;  // really std::cin and Jill::fetch

// using编译指令是可传递的
using namespace myth;
// 这条编译指令与下面两条编译指令等价
using namespace myth;
using namespace elements;

// 可以给命名空间创建别名
namespace my_very_favorite_things
{
	...
}
// 可以使用下面的语句使mvft称为my_very_favorite_thsings的别名
namespace mvft = my_very_favorite_things;
// 可以使用这种技术来简化嵌套命名空间的使用
namespace MEF = myth::elements::fire;
using MEF::flame; 
```

#### 4.未命名的名称空间
- 可以使用省略名称空间的名称来创建未命名的名称空间
```C++
namespace
{
	int ice;
	int bandycoot;
};
```
- 这就像后面跟着using编译指令一样，也就是说，该命名空间中声明的名称的潜在作用域为：从声明点到该声明区域末尾。从这个方面看与全局变量相似
- 因为其没有名称，别的位置也不能用。就是说，不能在未命名名称空间之外的其他文件中，使用该名称空间中的名称。这提供了链接性为内部的静态变量的替代品

- 也就相当于一个全局变量，只是这个全局变量不能在外部使用。
```C++
// 假设有这样的代码
// static storage, internal linkage

// 采用名称空间的方法
```

### 9.3.3 名称空间实例
- 头文件包含：常量、结构定义和函数原型

```C++
#pragma once

// 9.11 namesp.h
#include <string>

namespace pers
{
	struct Person
	{
		std::string fname;
		std::string lname;
	};
	void getPerson(Person&);
	void showPerson(const Person&);
}

namespace debts
{
	using namespace pers;
	struct Debt
	{
		Person name;
		double amount;
	};
	void getDebt(Debt&);
	void showDebt(const Debt&);
	double sumDebts(const Debt ar[], int n);
}
```

```C++
// 9.12 namesp.cpp -- namespaces
#include <iostream>
#include "namesp.h"

// 在名称空间中声明的函数名的作用域为整个名称空间，因此定义和声明必须位于同一个名称空间中。
namespace pers
{
	using std::cout;
	using std::cin;

	void getPerson(Person& rp)
	{
		cout << "Enter first name: ";
		cin >> rp.fname;
		cout << "Enter last name: ";
		cin >> rp.lname;
	}

	void showPerson(const Person& rp)
	{
		cout << rp.lname << ", " << rp.fname;
	}
}

namespace debts
{
	void getDebt(Debt& rd)
	{
		getPerson(rd.name);
		std::cout << "Enter debt: ";
		std::cin >> rd.amount;
	}

	void showDebt(const Debt& rd)
	{
		showPerson(rd.name);
		std::cout << ": $" << rd.amount << std::endl;
	}

	double sumDebts(const Debt ar[], int n)
	{
		double total = 0;

		for (int i = 0; i < n; ++i)
		{
			total += ar[i].amount;
		}

		return total;
	}
}
```

```C++
// 9.13 namessp.cpp

#include <iostream>
#include "namesp.h"

void other(void);
void another(void);

int main(void)
{
	using debts::Debt;
	using debts::showDebt;

	Debt golf = { {"Benny", "Goatsniff"},  120.0 };
	showDebt(golf);
	other();
	another();
	return 0;
};

void other(void)
{
	using std::cout;
	using std::endl;
	using namespace debts;

	Person dg = { "Doodles", "Glister" };
	showPerson(dg);
	cout << endl;

	Debt zippy[3];
	int i;
	for (i = 0; i < 3; ++i)
	{
		getDebt(zippy[i]);
	}

	for (i = 0; i < 3; ++i)
	{
		showDebt(zippy[i]);
	}

	cout << "Total debt: $" << sumDebts(zippy, 3) << endl;
	return;
}

void another(void)
{
	using pers::Person;
	
	Person collector = { "Milo", "Rightshift" };
	pers::showPerson(collector);
	std::cout << std::endl;
}
```
- using声明没有描述showDebt的返回类型和函数特征标，而只给出了名称；因此，如果函数被重载，则一个using声明将导入所有的版本。
- 另外，虽然Debt和showDebt都是用了Person类型，但不必导入任何Person名称，因为debt名称空间有一条包含pers名称空间的using编译指令。
- 由于debts中的using编译指令导入了pers名称空间，因此other()函数可以使用Person类型和showPerson()函数

## 9.4 总结
- 程序多文件
- auto、extern

## 9.5 复习题
- 1
```C++
// a. 自动存储持续性
// b.静态存储持续性，外部链接性
// c.静态存储持续性，内部链接性
// d.静态存储持续性、内部链接性

// 答案
// a.
// b.应该在一个文件中将secret定义为外部变量，并在第二个文件中使用extern来声明它
// c.可以在外部定义前加上关键字static，将topsecret定义为一个有内部链接的静态变量。也可以在一个未命名的名称空间中定义（跟第五题很像）
// d.应该在函数中的声明前加上关键字static，将beencalled定义为一个本地静态变量。
```
- 2.using声明只导入单个变量，与内部有冲突时会报错。using编译指令全部导入，还可能被内部同名变量隐藏，容易出错
	- using声明使得名称空间中的单个名称可用，其作用域与using所在的声明区域相同。using编译指令使名称空间中的所有名称可用。使用using编译指令是，就像在一个包含using声明和名称空间本身的最小声明区域中声明了这些名称一样 
- 3.加个std::就行了
- 4
```C++
using std::cout;
using std::cin;
using std::endl;
```
- 5 那肯定是用命名控件来实现啦，一个返回int，一个返回double
- 实际上只说对一半。还有一种方式是定义静态函数，而且还应该使用未命名的名称空间
```C++
// file1
namespace
{
	int average(int a, int b)
	{
		return int((a + b) / 2);
	}
}

namespace
{
	double average(int a, int b)
	{
		return (a + b) / 2;
	}
}

int main()
{
	file1::average(1, 2);
	file2::average(2, 3);
}
```
- 6
```C++
/*
10
4
0
10, 1
10, error(-4)
也就相当于一个全局变量，只是这个全局变量不能在外部使用。
*/
```
- 7
```C++
/*
1
4, 1, 2
2
2
4, 1, 2
2
*/
```

## 9.6 编程练习
- 1
```C++
#pragma once

const int Len = 40;

struct golf
{
	char fullname[Len];
	int handicap;
};

void setgolf(golf& g, const char* name, int hc);

int setgolf(golf& g);

void handicap(golf& g, int hc);

void showgolf(const golf& g);
```

```C++
#include "golf.h"
#include <iostream>
// #include <cstring>

void setgolf(golf& g, const char* name, int hc)
{
	strcpy_s(g.fullname, Len, name);
	g.handicap = hc;
}

int setgolf(golf& g)
{

	std::cout << "Enter fulll name:";
	std::cin.getline(g.fullname, Len);
	
	if (0 == strcmp(g.fullname, ""))
	{
		return 0;
	}

	std::cout << "Enter Handicap:";
	std::cin >> g.handicap;
	std::cin.get();
	return 1;
}

void handicap(golf& g, int hc)
{
	g.handicap = hc;
}

void showgolf(const golf& g)
{
	std::cout << "fullname: " << g.fullname << std::endl;
	std::cout << "Handicap: " << g.handicap << std::endl;
}
```

```C++
#include "golf.h"
#include <iostream>

int main()
{
	golf g;

	while (0 != setgolf(g))
	{
		std::cout << "The results are as follows: \n" << std::endl;
		showgolf(g);
	}
	return 0;
}
```
- 2
```C++
// 9.9-plus static.cpp -- using a static local variable
#include <iostream>
#include <string>

void strcount(const std::string& str);

int main()
{
	using namespace std;

	std::string input;
	char next;

	cout << "Enter a line:\n";
	while (getline(cin, input) && !input.empty())  // 在while条件中判断是否为空行
	{
		strcount(input);

		cout << "Enter next line (empty line to quit):\n";
	}
	cout << "Bye\n";
	return 0;
}

void strcount(const std::string& str)
{
	using namespace std;
	static int total = 0;

	cout << "\"" << str << "\" contains ";

	total += (int)str.size();
	cout << str.size() << " characters\n";
	cout << total << " characters total\n";
}
```

- 3
```C++

```
- 4
```C++

```
