## 第3章 处理数据
- 面向对象编程(OOP)的本质是设计并扩展自己的数据类型。设计自己的数据类型就是让类型与数据匹配。如果正确做到了这一点，就会发现以后使用数据类型时会容易很多。但是，待先了解内置类型。

### 3.1 简单变量
#### 3.1.1 变量名
- 命名规则中的一个：以两个下划线或下划线和大写字母打头的名称被保留给实现(编译器技巧使用的资源)使用。以一个下划线开头的名称用作全局标识符。

#### 3.1.3 整型short、int、long和 long long
- 每种类型都有有符号版本和无符号版本
- C++提供了一种灵活的标准，它确保了最小长度（从C语言借鉴而来的），如下所示：
	- short至少16位
	- int至少与short一样长
	- long至少32位，且至少与int一样长
	- long long至少64位，且至少与long一样长

#### 3.1.8 char类型：字符和小整数
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

##### 3.转义序列
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
##### 4.通用字符名
- ASCII是Unicode的子集

##### 5.signed char 和 unsigned char
- char在默认情况下既不是没有符号，也不是有符号。是否有符号由C++实现决定
```C++
// 可以显式的将类型设置为signed char 或 unsigned char
char fodo;  // may be signed, may be unsigned
unsigned char bar;  // definitely unsigned
signed char snark;  // definitely signed
```
- 若将char用作数值类型，则unsigned char 和 signed char 之间的差异将非常重要。

##### 6.wcha_t 
- 8位char可以表示基本字符集，另一种类型wchar_t(宽字符类型)可以表示扩展字符集。wchar_t类型是一种整数类型，它有足够的空间，可以表示系统使用的最大扩展字符集。
- 可以通过加上前缀L来只是宽字符常量和宽字符串。
```C++
// 将字母P的wchar_t版本存储到变量bob中，并显示单词tall的wchart版本
wchart_t bob = L'p';
wcout << L"tall" << endl;
```
##### 7.C++11新增的类型：char16_t 和 char32_t
- C++11使用前缀u表示char16_t，使用U表示char32_t
```C++
char16_t ch1 = u'q';  // basic character in 16-bit form
char32_t = ch2 = U'\U0000222B';  // universal character name in 32-bit form
```
#### 3.1.9 bool类型
- 字面量true 和 false 都可以转换为int类型，true被转换为1， 而false被转换为0
- 任何数字值或指针值都可以被隐式转换（即不用显式强制转换）为bool值。任何非零值转换为true，零转换为false。


### 3.2 const限定符
- 常量被初始化后，其值就被固定了，编译器将不允许再修改常量的值。
```C++
// 创建常量的通用格式
const type name = value
```
### 3.3 浮点数

#### 3.3.1 书写浮点数
- 第一种定点表示法
- 第二种表示浮点值的方式叫做E表示法。E6指的是10的6次方。E~n则小数点向左移n位
- E表示法最适合于非常大或非常小的数
#### 3.3.2 浮点类型
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

#### 3.3.4 浮点数的优缺点
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
### 3.4 C++算术运算符
#### 3.4.1 运算符优先级和结合性
- 乘除结合性从左到右
- 运算符重载(operator overloading)：使用相同的符号进行多种操作叫做运算符重载。比如int类型执行int除法，long类型执行long除法，double类型执行double除法，float类型执行float除法。

#### 3.4.4 类型转换
- 小类型转大类型，取值不会出现问题。自动类型转换
- 大类型转小类型，会损失精度。强制类型转换。

##### 1.初始化和赋值进行的转换
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
##### 2.以{}方式进行初始化时进行的转换（C++11）
- C++11 将使用大括号的初始化称为列表初始化（list-initialization）,因为这种初始化常用于给复杂的数据类型提供值列表。
- 大类型转小类型不行

##### 3.表达式中的转换
- 当一个表达式中包含两种不同的算术类型时，会自动进行类型转换。如true被转换为1，false被转换为0。这些转换被称为整型提升
```C++
short chickens = 20;
short ducks = 35;
short fowl = chickens + ducks;  // line 3
```
- 在执行第三行时，程序取得chickens和fowl的值，并将它们转换为int，然后，程序将结果转换为short类型。**通常选择int类型选择为计算机最自然的类型，这意味着计算机使用这种类型时，运算速度可能最快**
- 一般都是小类型转换为大类型
- 类型优先级略
##### 4.传递参数时的转换
- 在取消原型的情况下，C++将char和short类型(signed和unsigned)应用整型提升。float提升为double 
##### 5.强制类型转换
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
#### 3.4.5 C++11中的auto声明
- auto：在初始化的时候如果使用auto关键字，则不指定变量的类型。编译器将把变量的类型设置成与初始值相同。
```C++
auto n = 100;  // n is int
auto x = 1.5;  // x is double
auto y = 1.3e12L;  // y is long double

auto x = 0.0;  // ok, x is double because 0.0 is double
double y = 0;  // ok, automatically converted to 0.0
auto z = 0;  // opps, z is int because 0 is int
```

### 3.5 总结
- C++的基本类型分为两组：一组由存储为整数的值组成，另一组有存储为格式的值组成。
- 整型之间通过存储值时使用的内存量及有无符号来区分。

### 3.6 复习题
