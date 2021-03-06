# 第3章 处理数据
- 面向对象编程(OOP)的本质是设计并扩展自己的数据类型。设计自己的数据类型就是让类型与数据匹配。如果正确做到了这一点，就会发现以后使用数据类型时会容易很多。但是，待先了解内置类型。
- C++内置的类型分两种：
	- 基本类型：整型和浮点型
	- 符合类型：指针、数组、字符串、结构 

## 3.1 简单变量
### 3.1.1 变量名
- 命名规则中的一个：
	- __ 以两个下划线或下划线和大写字母打头的名称被保留给实现(编译器技巧使用的资源)使用。
	- _ 以一个下划线开头的名称用作全局标识符。

### 3.1.3 整型short、int、long和 long long
- 每种类型都有有符号版本和无符号版本
- C++提供了一种灵活的标准，它确保了最小长度（从C语言借鉴而来的），如下所示：
	- short至少16位
	- int至少与short一样长
	- long至少32位，且至少与int一样长
	- long long至少64位，且至少与long一样长
- **sizeof是一个运算符**，因为你可以这样使用： 
```C++
// 3.1 
#include <iostream>
#include <climits>

int main()
{
	using namespace std;

	int n = 0;  // n_int(0);  n_int{0};  n_int = {0};
	int n_int = INT_MAX;
	unsigned u_int = UINT32_MAX;
	short n_short = SHRT_MAX;
	long n_long = LONG_MAX;
	long long n_llong = LLONG_MAX;

	cout << "n = " << n << endl;

	cout << "int is " << sizeof(int) << " bytes" << endl;  // 类型必须加括号
	cout << "int is " << sizeof n_int << " bytes" << endl;  // 变量加不加都无所谓
	cout << "short is " << sizeof(short) << " bytes" << endl;
	cout << "long is " << sizeof(long) << " bytes" << endl;
	cout << "long is " << sizeof(long long) << " bytes" << endl;
	
	cout << "short max: " << n_short << endl;
	cout << "int max: " << n_int << endl;
	cout << "unsigned int max: " << u_int << endl;
	cout << "long max: " << n_long << endl;
	cout << "long long max: " << n_llong << endl;
	return 0;
}
/*
int is 4 bytes
short is 2 bytes
long is 4 bytes
long is 8 bytes
short max: 32767
int max: 2147483647
unsigned int max: 4294967295
long max: 2147483647
long long max: 9223372036854775807
*/
```
### 3.1.4 无符号类型
- 可以使用头文件climits来确定限制情况

### 3.1.5 选择数据类型
- 通常，int被设置为对目标计算机而言最“自然”的长度。自然长度(natural size)指的是计算机处理起来效率最高的长度。

### 3.1.6 整型字面量
```C++
// 3.3 hexoct1.cpp -- shows hex and octal literals
#include <iostream>
using namespace std;
int main()
{
	int chest = 42;
	int waist = 0x42;
	int inseam = 042;

	cout << "chest = " << chest << " (42 in decimal)" << endl;
	cout << "waist = " << waist << " (0x42 in hexadecimal)" << endl;
	cout << "inseam = " << inseam << " (042 in octal)" << endl;
	return 0;
}
```

```C++
// 3.4 hexoct2.cpp -- display values in hex and octal
#include <iostream>
using namespace std;
int main()
{
	int chest = 42;
	int waist = 42;
	int inseam = 42;

	cout << "chest = " << chest << " (decimal for 42)" << endl;
	// 修改cout显示帧数的方式。hex位于名称空间std中，使用了using namespace std;后不能当做变量。不使用名称空间时可以用作变量。
	cout << hex;  
	cout << "waist = " << waist << " (hexadecimal for 42)" << endl;
	cout << oct;
	cout << "inseam = " << inseam << " (octal for 42)" << endl;
	return 0;
}
```
### 3.1.7 C++如何确定常量的类型
- 除非有理由存储为其他类型（如使用了特殊的后缀来表示特定的类型，或者值太大，不能存储为int），一般都存储为int类型。20000存储为int，40000存储为long类型

```
unsigned long 后缀：LU, UL
unsigned long long 后缀：ull, uLL, Ull, ULL
```

### 3.1.8 char类型：字符和小整数
- char也是一种整型，专门为存储字符而设计，任何一个字符都会对应一个整数。在内存中，也是用整数存储的，只是读取(cin)和打印(cout)的时候会做转换.
```C++
// 3.6 morechar.cpp -- the char type and int type contrasted
#include <iostream>
int main()
{
	using namespace std;
	char ch = 'M';  // assign ASCII code for M to ch
	int i = ch;  // store same code in an int
	cout << "The ASCII code for  " << ch << " is " << i << endl;

	cout << "Add one to the character code:" << endl;
	ch = ch + 1;  // change character code in ch
	i = ch;
	cout << "The ASCII code for  " << ch << " is " << i << endl;

	cout << "Displaying char ch using cout.put(ch): ";
	cout.put(ch);
	cout.put('!');
	cout << endl << "Done" << endl;
	return 0;
}
```
- cout.put()：类定义了如何表示和控制数据。成员函数归类所有，描述了操纵类数据的方法。

#### 3.转义序列
```C++
// 3.7 bondini.cpp -- using escape sequences
#include <iostream>
int main()
{
	using namespace std;
	cout << "Operation \" HyperHype\" is now activateed! \n";
	cout << "Enter your agent code:________\b\b\b\b\b\b\b\b";
	long code;
	cin >> code;
	cout << "You entered " << code << "...\n";
	cout << "Code verified! Proceed with Plan Z3!\n";
	return 0;
}
```
#### 4.通用字符名
- ASCII是Unicode的子集

#### 5.signed char 和 unsigned char
- char在默认情况下既不是没有符号，也不是有符号。是否有符号由C++实现决定
```C++
// 可以显式的将类型设置为signed char 或 unsigned char
char fodo;  // may be signed, may be unsigned
unsigned char bar;  // definitely unsigned
signed char snark;  // definitely signed
```
- 若将char用作数值类型，则unsigned char 和 signed char 之间的差异将非常重要。

#### 6.wcha_t 
- 8位char可以表示基本字符集，另一种类型wchar_t(宽字符类型)可以表示扩展字符集。wchar_t类型是一种整数类型，它有足够的空间，可以表示系统使用的最大扩展字符集。
- 可以通过加上前缀L来只是宽字符常量和宽字符串。
```C++
// 将字母P的wchar_t版本存储到变量bob中，并显示单词tall的wchart版本
wchart_t bob = L'p';
wcout << L"tall" << endl;
```
#### 7.C++11新增的类型：char16_t 和 char32_t
- C++11使用前缀u表示char16_t，使用U表示char32_t
```C++
char16_t ch1 = u'q';  // basic character in 16-bit form
char32_t = ch2 = U'\U0000222B';  // universal character name in 32-bit form
```
### 3.1.9 bool类型
- 字面量true 和 false 都可以转换为int类型，true被转换为1， 而false被转换为0
- 任何数字值或指针值都可以被隐式转换（即不用显式强制转换）为bool值。任何非零值转换为true，零转换为false。


## 3.2 const限定符
- 常量被初始化后，其值就被固定了，编译器将不允许再修改常量的值。
```C++
// 创建常量的通用格式
const type name = value
```
## 3.3 浮点数

### 3.3.1 书写浮点数
- 第一种定点表示法
- 第二种表示浮点值的方式叫做E表示法。E6指的是10的6次方。E~n则小数点向左移n位
- E表示法最适合于非常大或非常小的数
### 3.3.2 浮点类型
- float至少32位；double至少48位且不少于float；long double至少和double一样多
```C++
// 3.8 floatnum.cpp  -- floating-point types
#include <iostream>
int main()
{
	using namespace std;
	// 强制使用点数表示法，为了防止程序把较大的值切换到E表示法，并使程序显示到小数点后6位
	cout.setf(ios_base::fixed, ios_base::floatfield);  // fixed-point
	float tub = 10.0 / 3.0;
	double mint = 10.0 / 3.0;
	const float millon = 1.0e6;

	cout << "tub = " << tub;
	cout << ", a millon tubs = " << millon * tub;
	cout << ".\nand ten million tubs = ";
	cout << 10 * millon * tub << endl;

	cout << "mint = " << mint << " and a millon mints = ";
	cout << millon * mint << endl;
	return 0;
}
```
- 当程序将每个数乘以一百万，tub 在第7个3之后就与正确的值有了误差。tub在7位上精确，但double在13位上精确

### 3.3.4 浮点数的优缺点
- 优点：表示的范围大
- 缺点：浮点运算通常比整数运算慢，且精度将降低。
```C++
// 3.9 fltadd.cpp -- precision probelm with float
#include <iostream>

int main()
{
	using namespace std;
	float a = 2.34E+22f;
	float b = a + 1.0f;
	
	cout << "a = " << a << endl;
	cout << "b - a = " << b - a << endl;  // 0
	return 0;
}
```
- ↑结果本来是1的，但是是0，因为float智能表示数字的前6位或7位，修改第23个值，没啥影响
## 3.4 C++算术运算符
### 3.4.1 运算符优先级和结合性
- 乘除结合性从左到右
- 运算符重载(operator overloading)：使用相同的符号进行多种操作叫做运算符重载。比如int类型执行int除法，long类型执行long除法，double类型执行double除法，float类型执行float除法。

### 3.4.4 类型转换
- 小类型转大类型，取值不会出现问题。自动类型转换
- 大类型转小类型，会损失精度。强制类型转换。

#### 1.初始化和赋值进行的转换
```C++
// 3.13 init.cpp -- type chages on initialization
#include <iostream>

int main()
{
	using namespace std;
	cout.setf(ios_base::fixed, ios_base::floatfield);
	float tree = 3;  // int converted to float
	int guess(3.9832);  // double converted to int
	int debt = 7.2E12;  // result not defined in C++

	cout << "tree = " << tree << endl;
	cout << "guess = " << guess << endl;
	cout << "debt = " << debt << endl;
	return 0;
}
```
#### 2.以{}方式进行初始化时进行的转换（C++11）
- C++11 将使用大括号的初始化称为列表初始化（list-initialization）,因为这种初始化常用于给复杂的数据类型提供值列表。
- 大类型转小类型不行

#### 3.表达式中的转换
- 当一个表达式中包含两种不同的算术类型时，会自动进行类型转换。如true被转换为1，false被转换为0。这些转换被称为整型提升
```C++
short chickens = 20;
short ducks = 35;
short fowl = chickens + ducks;  // line 3
```
- 在执行第三行时，程序取得chickens和fowl的值，并将它们转换为int，然后，程序将结果转换为short类型。**通常选择int类型选择为计算机最自然的类型，这意味着计算机使用这种类型时，运算速度可能最快**
- 一般都是小类型转换为大类型
- 类型优先级略
#### 4.传递参数时的转换
- 在取消原型的情况下，C++将char和short类型(signed和unsigned)应用整型提升。float提升为double 
#### 5.强制类型转换
```C++
(typeName) value;  // C
typeName value;  // C++

static_cast<long> (thorn)  // 将thorn转换为long
```
- static_cast<> 可用于将值从一种数值类型转换为另一种数值类型。
```C++
// 3.14 typecast.cpp -- forcing type changes
#include <iostream>
int main()
{
	using namespace std;
	int auks, bats, coots;

	// the following statement adds the values as double,
	// then convert the result to int
	auks = 19.99 + 11.99;

	// these statements add values as int
	bats = (int)19.99 + (int)11.99;  // old C syntax
	coots = int(19.99) + int(11.99); // new C++ syntax
	cout << "auks = " << auks << ", bats = " << bats;
	cout << ", coots = " << coots << endl;

	char ch = 'Z';
	cout << "The code for " << ch << " is ";  // print as char
	cout << int(ch) << endl;  // print as int
	cout << "Yes, the code is ";
	cout << static_cast<int>(ch) << endl;  // using static_cast
	return 0;
}
```
### 3.4.5 C++11中的auto声明
- auto：在初始化的时候如果使用auto关键字，则不指定变量的类型。编译器将把变量的类型设置成与初始值相同。
```C++
auto n = 100;  // n is int
auto x = 1.5;  // x is double
auto y = 1.3e12L;  // y is long double

auto x = 0.0;  // ok, x is double because 0.0 is double
double y = 0;  // ok, automatically converted to 0.0
auto z = 0;  // opps, z is int because 0 is int
```

## 3.5 总结
- C++的基本类型分为两组：一组由存储为整数的值组成，另一组有存储为格式的值组成。
- 整型之间通过存储值时使用的内存量及有无符号来区分。

## 3.6 复习题
```C++
// 1.相应的数据类型配合相应的数据运算
/* 2. 
int is 4 bytes
short is 2 bytes
long is 4 bytes
long long is 8 bytes
short max: 32767
int max: 2147483647
unsigned int max: 4294967295
long max: 2147483647 （有点系统4字节，有的系统8字节）
long long max: 9223372036854775807
c：先明确int 、long、long long的最大值是多少； unsigned int c = 3000000000; unsigned long c = 3000000000;
*/
// 3.C++没有提供自动防止超出整数类型范围的功能，需要程序员自己预估数据大小并选择合适的数据类型
// 4.C++在不超出int类型的范围情况下，默认优先使用int类型
	33：int类型
	33L：以long类型来存储整数常量
// 5.在ASCII下，两者等价。但是 char grade = 65; 先将65存储为int类型，然后再将int转换为char。char grade = 'A'则不需要这样的转换
// 6. 
	char ch = 88; 
	cout << ch << endl;
	cout << (char)88 << endl;  // cout << char(88) << endl;
// 7. 如果long是4字节，则存放入double不会出现舍入误差
      long long 若为8字节，存入double会出现舍入误差
// 8. 
	6 * 3 / 4 = 4
	3 / 4 * 6  = 0
	6.0 * 3 / 4 = 4.5
// 9. 
int sum = (int)x1 + (int)x2;  // int sum2 = int(x1) + int(x2);
int sum = int(x1 + x2);
// 10. fract -> double
```
##   3.7 编程练习
- 3
```C++
// 用户以度、分、秒的方式输入一个纬度，然后以度为单位显示该纬度
// 
#include <iostream>
using namespace std;
int main()
{
	cout << "Enter a latitude in degree, minutes, and seconds:" << endl;
	double degree, minute, second, result;

	cout << "First, enter the degrees: " ;
	cin >> degree;
	cout << "Next, enter the minuetes of arc: ";
	cin >> minute;
	cout << "Finally, enter the seconds of arc: ";
	cin >> second;
	
	result = degree + minute / 60 + second / (60 * 60);
	cout << degree << " degrees, " << minute << " minutes, "
	     << second << " seconds = " << result << " degrees." 
		 << endl;
	return 0;
}	
```
- 4
```C++
#include <iostream>
using namespace std;
const int DAY_HOURS = 24;
const int HOUR_MINUTES = 60;
const int MINUTE_SECONDS = 60;

int main()
{
	unsigned long long seconds;
	unsigned int days, hours, minutes, new_seconds;
	cout << "Enter the number of seconds:";
	cin >> seconds;

	days = seconds / (DAY_HOURS * HOUR_MINUTES * MINUTE_SECONDS);
	hours = (seconds / (HOUR_MINUTES * MINUTE_SECONDS)) % DAY_HOURS;
	minutes = (seconds / MINUTE_SECONDS) % HOUR_MINUTES;
	new_seconds = seconds % MINUTE_SECONDS;
	/*另一种思路，类似循环，seconds的值会改变。
	days = seconds / (DAY_HOURS * HOUR_MINUTES * MINUTE_SECONDS);
	seconds = seconds % (DAY_HOURS * HOUR_MINUTES * MINUTE_SECONDS);

	hours = seconds / (HOUR_MINUTES * MINUTE_SECONDS);
	seconds = seconds % (HOUR_MINUTES * MINUTE_SECONDS);

	minutes = seconds / MINUTE_SECONDS
	seconds = seconds % MINUTE_SECONDS;
	*/

	cout << seconds << " seconds = " << days<< " days, "
		<< hours << " hours, "<< minutes << " minutes, "
		<< new_seconds << " seconds" << endl;
	return 0;
}
```

- 5
```C++
#include <iostream>
using namespace std;

int main()
{
	long long world, us;
	cout << "Enter the world's population: ";
	cin >> world;
	cout << "Enter the population of the US:";
	cin >> us;
	double result = us / (world * 1.0);
	cout << "The population of the US is " << result * 100
		<< "% of the world population." << endl;;
	return 0;
}
// 6898758899   310783781
```
