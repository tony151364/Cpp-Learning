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
// 8.6
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
	free_throws one = { "Ifelsa Branch", 13, 14 };
	free_throws two = { "Andor Knott", 13, 14 };
	free_throws three = { "Minnie Max", 7, 9 };
	free_throws four = { "Whily Looper", 5, 9 };
	free_throws five = { "Long Long", 6, 14 };
	free_throws team = { "Throwgoods", 0, 0 };

	// no initialization
	free_throws dup;

	set_pc(one);
	display(one);
	accumulate(team, one);
	display(team);

	// use return value as argument
	display(accumulate(team, two));
	accumulate(accumulate(team, three), four);
	display(team);

	// use return value in assignment
	dup = accumulate(team, five);
	cout << "Displaying team:\n";
	display(dup);
	set_pc(four);

	// ill-advised assignment
	accumulate(dup, five) = four;
	cout << "Displaying dup after ill-advised assignment:\n";
	display(dup);
	return 0;
}

void display(const free_throws& ft)
{
	cout << "Name: " << ft.name << '\n';
	cout << " Made: " << ft.made << '\t';
	cout << "Attempts: " << ft.attempts << '\t';
	cout << "Percent: " << ft.percent << '\n';
}

void set_pc(free_throws& ft)
{
	if (ft.attempts != 0)
		ft.percent = 100.0f * float(ft.made) / float(ft.attempts);
	else
		ft.percent = 0;
}

free_throws& accumulate(free_throws& target, const free_throws& source)
{
	target.attempts += source.attempts;
	target.made += source.made;
	set_pc(target);
	return target;
}
```

#### 1.程序说明
- 由于display()显示结构的内容，而不修改它，因此这个函数使用了一个const引用参数。就这个函数而言，也可以按值传递结构，但与复制原始结构的拷贝相比，使用引用可以节省时间和内存。
- [ ] 使用常量指针指向常量数据应该也可以
```C++
// 因为函数的返回值是一个引用（这里是变量名），所以这是可行的。
accumulate(dup, five) = four;  // 修改后的dup被four所覆盖
 // 等价于下面两条语句
accumulate(dup, five);
dup = four;
```

#### 2.为何要返回引用
```C++
dup = accumulate(team, five);
```
- 如果accumulate()返回一个结构，而不是指向结构的引用，将把整个结构复制到一个临时位置，再将这个拷贝复制给dup。但在返回值为引用是，直接把team复制到dup，其效率更高。
- [ ] 注意：**返回引用的函数实际上是被引用的变量的别名**（函数是变量的别名）

#### 3.返回指针时需要注意的问题
- 返回引用是最重要的一点是，应避免返回函数终止时不再存在的内存单元，避免使用这样的代码
```C++
const free_throws & clone2(free_throws & ft)
{
	free_throws newguy;  // first step to big error
	newguy = ft;  // copy info
	return newguy;  // return reference to copy
}
```
- 一种方法是：返回作为参数传递给函数的引用，accumulate()正是这样做的
- 另一种方法：用new来分配新的存储空间。

```C++
const free_throws & clone(free_throws & ft)
{
	free_throws *pt;
	*pt = ft;  // copy info
	return *pt;  // return reference to copy
}  // 函数声明表明了，该函数实际上将返回这个结构的引用
// 因此，可以这样使用该函数
free_throws & jolly = clone(three);  // 这使jolly成为新结构的引用
```
- 该方法存在的问题，调用clone()隐藏了对new的调用，这使得以后很容易忘记使用delete来释放内存。第16章会讨论自动释放的问题。

#### 4.为何将const用于引用返回类型
```C++
accumulate(dup, five) = four;
```
- 为何这条语句能够通过编译？在赋值语句中，左值必须是可修改的左值。也就是说，**在赋值表达式中，左边的子表达式必须标识一个可修改的内存块**。在这里，函数返回指向dup的引用，它确实标识的是一个这样的内存块，因此这条语句是合法的。
- 另一方面，常规（非引用）返回类型是右值——不能通过地址访问的值。这种表达式可出现在赋值语句的右边，但不能出现在左边。显然，获取字面值（如 10.0）的地址没有意义，但为何常规函数返回值是右值呢？因为这种返回值位于类是内存单元中，运行到下一条语句时，它们可能不再存在。


- 不想被修改，可以在函数返回值前加const

### 8.2.5 将引用用于类对象
- 将类对象传递给函数时，C++通常的做法是使用引用。可以通过引用，让函数将类string、ostream、istream、ofstream和ifstream等类的对象作为参数。
```C++
// 8.7 
#include <iostream>
#include <string>
using namespace std;
string version1(const string& s1, const string& s2);
const string& version2(string& s1, const string& s2);  // has side effect
const string& version3(string& s1, const string& s2);  // bad design

int main()
{
	string input;
	string copy;
	string result;

	cout << "Enter a string: ";
	getline(cin, input);
	copy = input;
	cout << "Your string as entered: " << input << endl;
	result = version1(input, "***");
	cout << "Your string enhenced: " << result << endl;
	cout << "Your orginal string: " << input << endl;

	result = version2(input, "###");
	cout << "Your string enhenced: " << result << endl;
	cout << "Your orginal string: " << input << endl;

	cout << "Resetting orginal string.\n";
	input = copy;
	result = version3(input, "@@@");
	cout << "Your string enhenced: " << result << endl;
	cout << "Your orginal string: " << input << endl;

	return 0;
}

string version1(const string& s1, const string& s2)
{
	string temp;

	temp = s2 + s1 + s2;
	return temp;
}

const string& version2(string& s1, const string& s2)
{
	s1 = s2 + s1 + s2;
	return s1;
}

const string& version3(string& s1, const string& s2)  // bad design
{
	string temp;

	temp = s2 + s1 + s2;
	return temp;
}
```
- 对于version1：为何程序能将char* 指针赋给string引用呢？
	- 首先，string类定义了一种char* 到string的转换功能，这使得可以使用C-风格字符串来初始化string对象。
	- 其次，前面讨论过的一个const引用形参的一个属性——const会自动做类型转换（把数据合理化） 
	- 因此，如果形参类型为const string &，在函数调用时，使用的实参可以是string对象或C-风格字符串，如用引号括起的字符串常量、以空字符结尾的char数组或指向char的指针变量


- version3指出了什么不能做。它存在一个致命缺陷：返回一个指向version3()中声明的变量的引用。这个函数能通过编译（编译器可能会发出警告），当程序执行时可能崩溃。具体地说，问题由下面的赋值语句引发：
```C++
result = version3(input, "@@@");
```

### 8.2.6 对象、继承和引用
- 派生类继承基类，派生类可以使用基类的某些特性
- 继承的另一个特征：基类引用可以指向派生类对象，而无需进行强制类型转换。这种特征的一个实际结果是，可以定义一个接受基类引用作为参数的函数，调用该函数时，可以将基类对象作为参数，也可以将派生类对象作为参数。
```C++
// 8.8
#include <iostream>
#include <fstream>
#include <cstdlib>
using namespace std;

void file_it(ostream& os, double fo, const double fe[], int n);
const int LIMIT = 5;
int main()
{
	ofstream fout;
	const char* fn = "D:/Desktop/ep-data.txt";
	
	fout.open(fn);
	if (!fout.is_open())
	{
		cout << "Cant't open " << fn << ".Bye\n";

		// 参数不填写行不行？不行。里面写的是退出状态。函数在cstdlib文件里。
		// C使用整型，0正常退出，1异常退出。C++使用常量。
		exit(EXIT_FAILURE);  // EXIT_FALLURE == 1; EXIT_SUCCESS = 0
	}

	double objective;
	cout << "Enter the foacl length of your "
		"telescope objective in mm: ";
	cin >> objective;

	double eps[LIMIT];
	cout << "Enter the focal lengths, in mm, of" << LIMIT
		<< " eyepieces:\n";
	for (int i = 0; i < LIMIT; i++)
	{
		cout << "Eyepiece #" << i + 1 << ": ";
		cin >> eps[i];
	}

	file_it(fout, objective, eps, LIMIT);  // 将目镜数据写入到文件
	file_it(cout, objective, eps, LIMIT);  // 将目镜数据显示到屏幕
	cout << "Done\n";
	return 0;
}

void file_it(ostream& os, double fo, const double fe[], int n)
{
	ios_base::fmtflags initial;
	initial = os.setf(ios_base::fixed);  // save initial formatting state
	os.precision(0);
	os << "Focal length of objective: " << fo << " mm\n";
	os.setf(ios::showpoint);
	os.precision(1);
	os.width(12);
	os << "f.l eyepiece";
	os.width(15);
	os << "magnification" << endl;
	for (int i = 0; i < n; i++)
	{
		os.width(12);
		os << fe[i];
		os.width(15);
		os << int(fo / fe[i] + 0.5) << endl;
	}
	os.setf(initial);  // restore initial formatting state
}
```
- ``` os.setf(ios_base::fixed) ```将对象置于**使用定点表示法**的模式；``` os.setf(ios_base::showpoint) ```将对象置于**显示小数点**的模式；``` persion() ```指示显示多少位小数（假定对象处于定点模式下）。所有这些设置都将一直保持不变，直到再次调用相应的方法重置它们。
- 方法``` width() ``` 设置下一次输出操作使用的字段宽度，这种设置只在显示下一个值时有效，然后将恢复默认状态（字段宽度为0）

```C++
ios_base:: fmtflags initial;
initial = os.setf(ios_base::fixed);   // save initial formatting state
...
os.setf(initial);  / restore initial formatting state
```
- 方法setf()返回调用它之前的有效的格式化设置。 ↑是个恢复的过程

### 8.2.7 何时使用参数
- 引用参数实际上是基于指针的代码的另一个接口
- 使用参数的主要原因：
	- 程序员能够修改调用函数中的对象
	- 通过传递引用而不是整个数据对象，可以提高程序的运行速度。
- **只传递值，而不修改**的函数 ：
	-  数据对象很小，则按值传递
	-  数据对象是数组，只能用指针，需要将指针声明为const
	-  数据很大，使用const引用或const指针，将提高效率
	-  数据是类对象，使用const引用
-  **修改数值**的函数：
	- 内置数据类型使用指针（有时也用引用，如cin >> n; 不是 cin >> &n;）
	- 数组，用指针
	- 结构，引用或指针
	- 类对象，引用  

## 8.3 默认参数
- 由于编译器通过查看原型类了解函数所使用的参数数目，因此函数原型也必须将可能的默认参数告知程序。
- 对于带参数列表的函数，必须从右向左添加默认值。也就是说，要为某个参数设置为默认值，则必须为它右边的所有参数提供默认值：
```C++
int harpo(int n, int m = 4, int j = 5);  // VALID
int chico(int n, int m = 6, in j);  // INVALID
int groucho(int k = 1; int m = 2; int n = 3);  // VALID

// 实参从左到右，不能跳过任何参数。下面是不允许的：
beeps = harpo(3, , 8);  // invalid, doesn't set m to 4
```
- 默认参数是一种便捷操作。在设计类时，通过默认参数，可以减少要定义的折构函数、方法以及方法重载的数量。
```C++
// 8.9
#include <iostream>
const int ArSize = 80;
char* left(const char* str, int n = 1);
int main()
{
	using namespace std;
	char sample[ArSize];
	cout << "Enter a string:\n";
	cin.get(sample, ArSize);
	char* ps = left(sample, 4);
	cout << ps << endl;
	delete[] ps;

	ps = left(sample);
	cout << ps << endl;
	delete[] ps;
	return 0;
}

char* left(const char* str, int n)
{
	if (n < 0)
		n = 0;
	char* p = new char[n + 1];
	int i;
	for (i = 0; i < n && str[i]; i++)
		p[i] = str[i];  // copy characters
	while (i <= n)
		p[i++] = '\0';
	return p;
}
```

#### 程序说明


















