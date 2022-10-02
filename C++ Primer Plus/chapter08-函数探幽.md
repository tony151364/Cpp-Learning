#### 重点
- [ ] 左值和右值
- [ ] 函数模板


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
- 意思就是，如果类型不匹配，你不能直接修改原始变量。你修改的只是临时变量。等于没引用，跟按值传递差不多。
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
- [x] 使用常量指针指向常量数据应该也可以
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
- [x] 注意：**返回引用的函数实际上是被引用的变量的别名**（函数是变量的别名）
- 就是说用引用效率更高

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
- [ ] 该方法存在的问题，调用clone()隐藏了对new的调用，这使得以后很容易忘记使用delete来释放内存。第16章会讨论自动释放的问题。

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
		os << int(fo / fe[i] + 0.5) << endl;  // +0.5：四舍五入，向上取整
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
		p[i++] = '\0';  // 其它的空间都设置为空字符
	return p;
}
```

#### 程序说明
- ``` i < n && str[i] ``` 防止用户输入的数字超过字符串的字符数
```C++
// 另一种设置新字符串长度的方法是，将n设置为传递的值和字符串长度中较小的一个：
int len = strlen(str);
n = (n < len) ? n : len;  // the lesser of n and len
char * p = new char[n+1];
// 这样确保new分配的空间不会多于存储字符串所需的空间
```

## 8.4 函数重载
- 函数多态（函数重载）让您能够使用多个同名的函数，他们实现不同的但相似的功能。
- “多态”和“函数重载”两个术语指的是同一回事。有点像一词多意。
- 函数重载的关键是函数的参数列表——也称为函数特征标（function signture）
```C++
void print (const char * str, int width);  // #1
void print (double d, int width);  // #2
void print (long l, int width);  // #3
void print(int i, int width);  // #4
void print (const char *str);// #5

unsigned int year = 3210;
print(year, 6);  // 不与任何类型匹配
// 因为有3种转换方式，C++将拒绝这种函数调用
```
- 对于重载，返回值**可以相同**，但是参数列表**必须不同**
- 若参数列表**相同**，返回值**不同**则编译不能通过

- 匹配函数时，**区分**const和非const变量（书上印错了）。
#### 重载引用参数
- [x] 这个左值和右值的例子还不太理解,右值只能读，左值读、写都可以
- 表达式与右值引用匹配，变量与左值引用匹配，常量需要const类型左值引用，也可以右值引用

### 8.4.1 重载示例
```C++
// 8.10 leftover.cpp -- overloading the left() function
#include <iostream>
unsigned long left(unsigned long num, unsigned ct);
char* left(const char* str, int n = 1);

int main()
{
	using namespace std;
	const char* trip = "Hawwii!!";  // test value
	unsigned long n = 12345678;  // test value
	int i;
	char* temp;

	for (i = 1; i < 10; i++)
	{
		cout << left(n, i) << endl;
		
		temp = left(trip, i);  // left()函数里面用new了
		cout << temp << endl;
		delete temp;  // point to temorary storage
	}
	return 0;
}

// This function returns the first ct digits of the number num.
unsigned long left(unsigned long num, unsigned ct)
{
	unsigned digits = 1;
	unsigned long n = num;

	if (ct == 0 || num == 0)
		return 0;			// return 0 if no digits

	while (n /= 10)
		digits++;

	if (digits > ct)
	{
		ct = digits - ct;
		while (ct--)
			num /= 10;
		return num;			// return left ct digits
	}
	else					// if ct >= of digits
		return num;			// return the whole number
}

// This function returns a pointer to a new string
// consisting of the first n characters in the str string
char* left(const char* str, int n)
{
	if (n < 0)
		n = 0;

	char* p = new char[n + 1];
	int i;

	for (i = 0; i < n && str[i]; i++)
		p[i] = str[i];  // copy characters
	while (i <= n)
		p[i++] = '\0';  // set rest of string to '\0'
	return p;
}
```

### 8.4.2 何时使用重载函数
- 仅当函数基本上执行相同任务，但使用不同形式的数据时，才应采用函数重载。

#### 默认参数与函数重载
- 使用一个带默认参数的函数要简单些。只需编写一个函数（而不是两个函数），程序也只需为一个函数（而不是两个）请求内存；需要修改函数时，只修改一个。然而，如果需要使用不同类型的参数，则默认参数便不管用了，这种情况下，应该使用函数重载

#### 名称修饰
- 名称修饰(name decoration)或名称矫正(name mangling)，它根据函数原型中指定的参数类型对每个函数名进行加密，以便于C++追踪每个函数

## 8.5 函数模板
- 函数模板是通用的函数描述，也就是说，它们使用泛型来定义函数，其中的泛型可用具体的类型（如int 或 double）替换。通过将类型作为参数传递给模板，可使编译器生成该类型模板。
- 由于模板允许以泛型（而不是具体类型）的方式编写程序，因此有时也被称为通用编程。由于类型是用参数表示的，因此模板特性有时也称为参数化类型(parameterized types)
```C++
// 8.11 funtemp.cpp -- using a function template
#include <iostream>
template <typename T>  // 第一行指出，要建立一个模板，将类型命名为AnyType。
void Swap(T& a, T& b);

int main()
{
	using namespace std;
	int i = 10;
	int j = 20;
	cout << "i, j = " << i << ", " << j << endl;
	cout << "Using compiler-generated int swapper:\n";
	Swap(i, j);
	cout << "Now i, j = " << i << ", " << j << ".\n";

	double x = 24.5;
	double y = 81.7;
	cout << "x, y = " << x << ", " << y << ".\n";
	cout << "Using compiler-generated double swapper:\n";
	Swap(x, y);
	cout << "Now x, y = " << x << ", " << y << ".\n";
	return 0;
}

template <typename T>
void Swap(T& a, T& b)
{
	T temp;
	temp = a;
	a = b;
	b = temp;
}
```
- template 和 typename是必须的除非用关键字class代替typename。因为在C++98添加关键字typename之前，C++使用关键字class来创建模板。
- 模板不创建任何函数，而只是告诉编译器如何定义函数。
- **提示**：如果需要多个将同一种算法用于不同类型的函数，请使用模板。如果不考虑向后兼容的问题，并愿意键入较长的单词，则声明类型参数时，应使用关键字typename而不使用class
- **注意：** 函数模板不能缩短可执行程序。对于该程序而言，最终仍由两个独立的函数定义，就像以手工方式定义了这些函数一样。最终的代码不包含任何模板，而只包含了为程序生成的实际函数。使用模板的好处是，它使生成多个函数定义更简单、更可靠。
- 更常见的情形是，将模板放在头文件中，并在需要使用模板的文件中包含头文件。头文件将在第9章讨论。

### 8.5.1 重载的模板
- 需要多个对不同类型使用同一种算法时，可以使用模板。然而，并非所有的类型都使用相同的算法，这时候就需要重载模板。
```C++
// 8.12 twotemps.cpp -- using overloaded template functions
#include <iostream>
template <typename T>
void Swap(T& a, T& b);

template <typename T>
void Swap(T* a, T* b, int n);

void Show(int a[]);
const int Lim = 8;

int main()
{
	using namespace std;
	int i = 10;
	int j = 20;
	cout << "i, j = " << i << ", " << j << endl;
	cout << "Using compiler-generated int swapper:\n";
	Swap(i, j);
	cout << "Now i, j = " << i << ", " << j << ".\n";

	int d1[Lim] = { 0, 7, 0, 4, 1, 7, 7, 6 };
	int d2[Lim] = { 0, 7, 2, 0, 1, 9, 6, 9 };
	cout << "Original arrays:\n";
	Show(d1);
	Show(d2);
	Swap(d1, d2, Lim);
	cout << "Swapped arrays:\n";
	Show(d1);
	Show(d2);
	return 0;
}

template <typename T>
void Swap(T& a, T& b)
{
	T temp;
	temp = a;
	a = b;
	b = temp;
}

template <typename T>
void Swap(T* a, T* b, int n)
{
	T temp;
	for (int i = 0; i < n; i++)
	{
		temp = a[i];
		a[i] = b[i];
		b[i] = temp;
	}
}

void Show(int a[])
{
	using namespace std;
	cout << a[0] << a[1] << "/";
	cout << a[2] << a[3] << "/";
	for (int i = 4; i < Lim; i++)
		cout << a[i];
	cout << endl;
}
```

### 8.5.2 模板的局限性
- 如果模板函数中使用了赋值( = )，T是数组将不成立。如果使用了<运算符，T为结构也不成立。
- 总之，模板无法处理某些函数。
	- 一种解决办法是，C++允许您重载运算符+，以便能够将其用于特定的结构或类（第11章）
	- 另一种是，为特定类型提供具体化的模板定义，下面介绍这种方案。 

### 8.5.3 显式具体化(explicit specialization)
- 当编译器找到与函数调用匹配的具体化定义时，将使用该定义，而不再寻找模板。
- 当参数不变，只想更改函数的内容时，可以用显示具体化。

#### 1.第三代具体化(ISO/ANSI C++模板)
- 试验其他具体化方法后，C++98标准选择了下面方法
	- 对于给定的函数名，可以有非模板函数、模板函数和显式具体化模板函数以及它们的重载版本。
	- 显式具体化的原型和定义以template<>开头，并通过名称来指出类型。
	- 具体化优先于常规模板，而非模板函数优先于具体化和常规模板
```C++
// 下面是用于交换job结构的非模板函数、模板函数和具体化的原型

// non template function prototype
void Swap(job &, job &);

// template prototype
template <typename T>
void Swap(T &, T &);

// explicit specialization for the job type
template <> void Swap<job>(job &, job &);
```
- 编译器在选择原型时的优先级：非模板函数 > 显示具体化模板函数 > 常规模板函数
- 例如，在下面的代码中，第一次调用Swap()时使用通用版本，而第二次调用使用基于job类型的显式具体化版本。
```C++
template <class T>  // template
void Swap(T &, T &);

// explicit specialization for the job type
template <> void Swap<job> (job &, job &);
int main()
{
	double u, v;
	...
	Swap(u, v);  // use template
	job a, b;
	...
	Swap(a, b);  // use void Swap<job>(job &, job &)
}
```

- Swap<job>中的<job>是可选的，因为函数的参数类型表明，这是job的一个具体化。因此，也可以这样编写：
```C++ 
template <> void Swap(job &, job &);   // simpler form 
```
	
#### 2.显式具体化示例
```C++
// 8.13 twoswap.cpp -- specialization overrides a template
#include <iostream>
template <typename T>
void Swap(T& a, T& b);

struct job
{
	char name[40];
	double salary;
	int floor;
};

// explicit specialization
template <> void Swap<job>(job& j1, job& j2);
void Show(job& j);

int main()
{
	using namespace std;
	cout.precision(2);
	cout.setf(ios::fixed, ios::floatfield);
	int i = 10, j = 20;
	cout << "i, j = " << i << ", " << j << ".\n";
	cout << "Using compoler-generated int swapper:\n";
	Swap(i, j);  // genetates void Swap(int &, int &)
	cout << "Now, i, j = " << ", " << j << ".\n";

	job sue = { "Susan Yaffee", 73000.60, 7 };
	job sidney = { "Sidney Taffee", 78060.72, 9 };
	Show(sue);
	Show(sidney);
	Swap(sue, sidney);  // use void Swap(job &, job &)
	cout << "After job swapping:\n";
	Show(sue);
	Show(sidney);
	return 0;
}

template <typename T>
void Swap(T& a, T& b)  // generalversion
{
	T temp;
	temp = a;
	a = b;
	b = temp;
}

// swaps just the salary and floor fields of a job structure
template <> void Swap<job>(job& j1, job& j2)  // specialization
{
	double t1;
	int t2;
	
	t1 = j1.salary;
	j1.salary = j2.salary;
	j2.salary = t1;
	
	t2 = j1.floor;
	j1.floor = j2.floor;
	j2.floor = t2;
}

void Show(job& j)
{
	using namespace std;
	cout << j.name << ": $" << j.salary
		<< " on floor " << j.floor << endl;
}
	
```
	
### 8.5.4 实例化和具体化
- 为进一步了解模板，必须理解术语实例化的具体化。	
- 在代码中包含函数模板本身并不会生成函数定义，它只是一个用于生成函数定义的方案。编译器使用模板为特定类型生成函数定义时，得到的是模板实例(instantiation)。模板并非函数定义，但使用int的模板实例时函数定义。这种实例化方式被称为隐式实例化(implicit instantiation)
-  最初只能通过隐式实例化来生成函数定义，但现在C++还允许显示实例化(explicit instantiation)。
```C++
// 显式实例化
// 含义：使用Swap()模板生成int类型的函数定义
template void Swap<int>(int, int);  // explict instantiation	

// 显式具体化
// 含义：不要使用Swap()模板来生成函数定义，而应使用专门为int类型显式地定义的函数定义
template <> void Swap<int>(int &, int &);  // explicit specialization
template <> void Swap(int &, int &);  // explicit sepcializaiton
```

- 显式具体化在关键字template后包含<>，而显式实例化没有。
- **警告**: 试图在同一个文件（或转换单元）中使用同一种类型的显式实例或显示具体化将出错。

- 隐式实例化、显式实例化和显式具体化统称为**具体化**(specialization)。它们的相同之处在于，它们表示的都是使用具体类型的函数定义，而不是通用描述。
- 引入显式实例化后，必须使用新的语法——在声明中使用前缀template和template<>，以区分显式实例化和显式具体化。通常功能越多，语法规则越多。下面的代码总结了这些概念：
```C++
···
template <class T>
void Swap(T&, T&);  // template prototype

template <> void Swap<job>(job&, job&);  // explicit specialization for job

int mian(void)
{
	template void Swap<char>(char&, char&);  // explicit instantiation for char
	short a, b;
	···
	Swap(a, b);  // implicit template instantiation for short
	job n, m;
	···
	Swap(n, m);  // use explicit specilaization for job
	char g, h;
	···
	Swap(g, h);  // use explicit template instantiation for char
	···
}
```
- 编译器看到char的显示实例化后，将使用模板定义来生成Swap()的char版本。对于其他Swap()调用，编译器根据函数调用中实际使用的参数，生成相应的版本。例如，当编译器看到函数调用Swap(a, b)后，将生成Swap()的short版本，因为两个参数的类型都是short。当编译器看到Swap(n, m)后，将使用job类型提供的独立定义（显式具体化）。当编译器看到Swap(g, h)后，将使用处理显示实例化时生成的模板具体化
	

### 8.5.5 编译器选择使用哪个函数版本
- 对于函数重载、函数模板和函数模板重载，C++需要（且有一个定义良好的策略，来决定为函数调用使用哪一个函数的定义，尤其是有多个参数时。这个过程称为重载解析（overloading resolution)。详细解释这个策略将近一章的篇幅，因此我们先大致了解一下这个过程是如何进行的。
	- 第1步：创建候选函数列表。其中包含于被调用函数的名称相同的函数和
	- 第2步：使用候选函数列表创建可行函数列表。
	- 第3步：确定是否有最佳的可行函数
- 最佳到最差的顺序：
	- 1.完全匹配，但常规函数优于模板
	- 2.提升转换（例如，char和shorts自动转换为int，float自动转换为double）
	- 3.标准转换（例如，int转换为char，long转换为double）
	- 4.用户定义的转换，如类声明中定义的转换
- [ ] 标准转换
- [ ] 提升转换
	
#### 1.完全匹配和最佳匹配
	- Type(argument-list) 意味着用作实参的函数名与用作形参的函数指针只要返回类型和参数列表相同，就是匹配的
	- 如果有多个匹配的原型，则编译器将无法完成重载解析过程；如果没有最佳的可行函数，编译器将生成一条错误消息。
	- 有时候，及时两个函数都完全匹配，扔可完成重载解析。首先，指向非const数据的指针和引用优先与非const指针和引用参数匹配。但const与非const只适用于指针和引用
- [ ] 不太理解
	
```C++
// tempover.cpp  -- template overloading
#include <iostream>

template <typename T>  // template A
void ShowArray(T arr[], int n);

template <typename T>  // template B
void ShowArray(T* arr[], int n);

struct debts
{
	char name[50];
	double amount;
};

int main()
{
	using namespace std;
	int things[6] = { 13, 31, 103, 301, 310, 130 };
	struct debts mr_E[3] =
	{
		{"Ima Wolfe", 2400.0},
		{"Ura Foxe", 1300.0},
		{"Iby Stout", 1800.0}
	};
	double* pd[3];

	// set pointers to the amount members of the structures in mr_E
	for (int i = 0; i < 3; i++)
	{
		pd[i] = &mr_E[i].amount;
	}

	cout << "Listing Mr.E's counts of things:\n";
	// things is an array of int
	ShowArray(things, 6);  // use template A

	cout << "Listing Mr.E's debts:\n";
	// pd is an array of pointers to double
	ShowArray(pd, 3);  // use template B (more specialized)
	return 0;
}

template <typename T>
void ShowArray(T arr[], int n)
{
	using namespace std;
	cout << "template A\n";
	for (int i = 0; i < n; i++)
	{
		cout << arr[i] << ' ';
	}
	cout << endl;
}

template <typename T>
void ShowArray(T* arr[], int n)
{
	using namespace std;
	cout << "template B\n";
	for (int i = 0; i < n; i++)
	{
		cout << *arr[i] << ' ';
	}
	cout << endl;
}	
```
	
#### 3.自己选择
- 有些情况下，可通过编写合适的函数调用，引导编译器做出您希望的选择
```C++
// 8.15 choices.cpp
#include <iostream>

template<class T>  // or template <typename T>
T lesser(T a, T b)  // #1
{
	return a < b ? a : b;
}

int lesser(int a, int b)  // #2
{
	a = a < 0 ? -a : a;  // Take the absolute value of a, b
	b = b < 0 ? -b : b;
	return a < b ? a : b;
}

int main()
{
	using namespace std;
	int m = 20;
	int n = -30;
	double x = 15.5;
	double y = 25.9;

	cout << lesser(m, n) << endl;  // use # 2
	cout << lesser(x, y) << endl; // use #1 with double
	cout << lesser<>(m, n) << endl;  // use #1 with int
	cout << lesser<int>(x, y) << endl;  // use #1 with int

	return 0;
}
```
	
#### 4.多个参数的函数
- 将有多个参数的函数调用与有多个参数的原型进行匹配时，情况将非常复杂。编译器必须考虑所有参数的匹配情况。	

### 8.5.6 模板函数的发展

#### 1.什么是类型
- T1可能是short，而T2可能是char，在这种情况下，加法运算导致自动整型提升，因此结果类型为int。
- 结构和类可能导致重载运算符+，这导致问题更加复杂。因此，在C++98中，没有办法声明xpy的类型
```C++
template<class T1, T2>
void ft(T1 x, T2 y)
{
	
}
```

#### 2.关键字decltype（C++11）
- decltype提供的参数可以是表达式，因此在前面的模板函数ft()中，可使用下面代码
```C++
int x;
decltype(x + y) xpy;  // make xpy the same type as x + y;
xpy = x + y;
	
// 两条语句可以合二为一
decltype(x + y) xpy = x + y;
```
- 对于``` decltype(expression) var ``` ,为确定类型，编译器必须遍历一个核对表。核对表的简化版如下：
	- 第一步：如果expression是一个没有用括号括起的标识符，则var的类型与该标识符的类型相同，包括const等限定符
	- 第二步：如果expression是一个函数调用，则var的类型与函数的返回类型相同
	- 第三步：如果expression是一个左值，则var为指向其类型的引用。（不太懂）
	- 第四步：如果前面的条件都不满足，则var的类型与expression的类型相同
- [ ] 如果需要多次声明，可结合使用typedef和decltype
```C++
template<class T1, class T2>
void ft(T1 x, T2 y)
{
	···
	typedef decltype(x + y) xytype;
	xytype xpy = x + y;
	xytype arr[10];
	xytype& rxy = arr[2];  // rxy a reference
	···
}	
```

#### 3.另一种函数声明语法(C++11 后置返回类型)
- C++新增了一种声明和定义函数的语法。因为x, y还没声明，所以需要后置返回类型
```C++
double h(int x, float y);
// 写成
auto h(int x, float y) -> double;
```
- 这将返回类型转移到参数声明的后面。->double被称为后置返回类型(trailing return type)。其中auto是一个占位符，表示后置返回类型提供的类型，这是C++11给auto新增的一种角色。这种语法也可用于函数定义：
- 通过结合使用这种语法和decltype，便可给gt()指定返回类型：

```C++
template<class T1, class T2>
auto ft(T1 x, T2 y) -> decltype(x + y)
{
	···
	return x + y;
	
}	
```

## 8.6 总结
- 如果一个类（如 ofstream）是从另一个类型（如ostream）派生出来的，则基类引用可以指向派生类对象。
- 只能在参数列表中从右到左提供默认参数。因此，如果为某个参数提供了默认值，则必须为该参数右边所有的参数提供默认值。
- 函数模板自动完成重载函数的过程。

## 8.7 复习题
- 1.短小而简洁的函数，不包含循环等复杂运算。因为内联函数本质还是代码的替换。
- 2
```C++
// a.
void song(const char * name, int times = 1);
// b???
// c.不能吧，分配和释放感觉都有问题，要是提供了，又传进来一个，那么原来的就找不到了。但是覆盖了。（不懂）
```
- 3
```C++
#include <iostream>
#include <string>
using namespace std;
void iquote(int x)
{
	cout << " \" " << x << " \" " << endl;
}

void iquote(double y)
{
	cout << " \" " << y << " \" " << endl;	
}

void iquote(string s)
{
	cout << " \" " << s << " \" " << endl;	
}
```
- 4
```C++
struct box
{
    char maker[40];
    float height;
    float width;
    float length;
    float volume;
};

// a.
void ShowBox(box& B)
{
	cout << B.maker << endl;
	cout << B.height << endl;
	cout << B.width << endl;
	cout << B.length << endl;
	cout << B.volumn << endl;
}
	
// b.
void ChangeBoxVolume(box& B)
{
	B.volumn = B.height * B.width * B.length;
}
```
	
- 5.这是修改后的，对比下以前的，看看差别，尤其是指针的使用
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

void fill(std::array<double, Seasons>& pa);
void show(std::array<double, Seasons>& da);

int main()
{
	std::array<double, Seasons> expenses;
	fill(expenses);
	show(expenses);
	return 0;
}

void fill(std::array<double, Seasons>& pa)
{
	using namespace std;
	for (int i = 0; i < Seasons; ++i)
	{
		cout << "Enter " << Snames[i] << " expenses: ";
		cin >> pa[i];  
	}
}

void show(std::array<double, Seasons>& da)
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
- 6
```C++
#include <iostream>
// a.
double mass(double density, double volume)
{
	return density * volume;  // m = ρv
}
double mass(double density)
{
	return density * 1;
}

// b.
void repeat(int n, char* a)
{
	for (int i = 0; i < n; i++)
		std::cout << a << std::endl;
}
void repeat(char* a)
{
	for (int i = 0; i < 5; i++)
		std::cout << a << std::endl;
}

// c.
int average(int x, int y)
{
	int z = (int)(x + y) / 2;
	return z;
}

int average(double x, double y)
{
	return (x + y) / 2;
}

// d.
char mangle(char ch)
{
	return ch;
}
char* mangle(char* ch)
{
	return ch;
}
						    
```						    
- 7.
```C++
template<class T>
T max(T x, T y)
{
    return (x > y) ? x : y;
}
```
- 8. 
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

template<class T>
T max(T x, T y);

int main()
{
    box box1, box2;

    box1.volume = 33.4f;
    box2.volume = 33.4f;
    std::cout << max(box1.volume, box1.volume);
}

template<class T>
T max(T x, T y)
{
    return (x > y) ? x : y;
}
```
- 9. 看了视频再写

## 8.8 编程练习
- 1
```C++
#include<iostream>
using std::cout;
using std::endl;
void PrintString(char* str);
void PrintString(char* str, int n);

int main()
{
	char str[20] = "adsffasd";
	
	PrintString(str);
	PrintString(str, 3);
}

void PrintString(char* str)
{
	cout << str << endl;
}

void PrintString(char* str, int n)
{
	for (int i = 0; i < n; ++i)
	{
		cout << "times: " << i + 1 << endl;
		PrintString(str);
	}
}

```

- 2
```C++

```
- 3
```C++

```

- 4
```C++

```

- 5
```C++

```

- 6
```C++

```

- 7
```C++

```

- 8
```C++

```

- 9
```C++

```

- 10
```C++

```
						    
						    
						    
						    
						    
						    
						    
						    
						    

