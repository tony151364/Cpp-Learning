
## 6.1 if 语句
- 和循环测试条件一样，if测试条件也将被强制转换为bool值，因此0将被转换为false，非零为true。整个if语句被视为一条语句。

```C++
// 6.1 if.cpp -- using the if statement
#include <iostream>
int main()
{
	using std::cin;  // using declarations
	using std::cout;
	char ch;
	int spaces = 0;
	int total = 0;
	cin.get(ch);
	while (ch != '.')
	{
		if (ch == ' ')
			++spaces;
		++total;
		cin.get(ch);
	}
	cout << spaces << " spaces, " << total;
	cout << " characters total in sentence\n";
	return 0;
}
```
- 注意字符总数中包括按回车键生成的换行符

### 6.1.1 if else 语句
```C++
// 6.2 ifelse.cpp -- using the if else statement
#include <iostream>
int main()
{
	char ch;

	std::cout << "Type, and I shall repeat.\n";
	std::cin.get(ch);
	while (ch != '.')
	{
		if (ch == '\n')
			std::cout << ch;
		else
			std::cout << ++ch;
		std::cin.get(ch);
	}
	// try ch + 1 instead of ++ch for interrsting effect
	std::cout << "\nPlease excuse the slight confusion.\n";
		// std::cin.get();
		// std::cin.get();
	return 0;
}
```
- cin.get()的一些说明（参考：4.2.4节）：
  - cin.get(); 读取一个字符，包括换行符;**用户按完回车后才开始执行**，下同。
  - cin.get(ch); 把读取的字符存储到ch变量所在的内存中
  - cin.get(name, ArSize); 读取一行字符，最大为ArSize-1个字符，最后一个位置为'\0'.且不主动读取换行符。也就是说换行符还在输入流中，会被下一个cin.get()读取（如果有这个操作的话）。
  - cin >> name; 只读取一个单词，遇到空白符就停止了。（name是个字符数组名，上同）
### 6.1.2 格式化if else语句
- 第一种格式：强调的是语句的块结构
```C++
if (ch == 'z')
{
	zorro++;
	cout << "Another zorro candidate\n";
}
else
{
	dull++;
	cout << "Not a Zorro condidate\n";
}
```
- 第二种格式：将语句块与关键字if和else更紧密地结合在一起。
```C++
if (ch == 'z') {
	zorro++;
	cout << "Another zorro candidate\n";
	}
else {
	dull++;
	cout << "Not a Zorro condidate\n";
	}
```
### 6.1.2 if else if else 结构
```C++
// 6.3 ifelseif.cpp -- using if else if else
#include <iostream>
const int Fave = 27;
int main()
{
	using namespace std;
	int n;

	cout << "Enter a number in the range 1-100 to find ";
	cout << "my favorite number: ";
	do
	{
		cin >> n;
		if (n < Fave)
			cout << "Too low -- guess again: ";
		else if (n > Fave)
			cout << "Too high -- guess again: ";
		else
			cout << Fave << " is right!\n";
	} while (n !=  Fave);
	return 0;
}
```
- 编写让编译器更容易发现错误的代码：
```C++
if ( 3 == myNumber)   // 可以正常工作
if ( 3 = myNumber)   // 错误，但编译器很容易发现，因为程序员尝试将一个值赋给一个字面值
if ( myNumber = 3)  // 错误，但是编译器发现不了，因为这符合语法。
```

## 6.2 逻辑表达式
- 三种逻辑运算符：逻辑OR(||)、逻辑AND(&&)、逻辑NOT(!)

### 6.2.1 逻辑OR运算符：||
- 两边只要有一个为true，表达式的值就为true。否则为false，即两边都为false，结果为false。
- ``` 5 == 5 || 5 == 9; ``` 对于←来说，||的优先级比关系运算符低，因此不需要在表达式中使用括号
- C++规定，||运算符是个顺序点(sequence pint)。也就是先修改左侧的值，再运算右侧的表达式。当左侧为true，右侧就不运算了（这种现象也称为“**短路**”）

```C++
// 6.4 or.cpp
#include <iostream>
int main()
{
	using namespace std;
	cout << "This program may format your hard disk\n"
		"and destroy all your data.\n"
		"Do you wish to continue? <y/n> ";
	char ch;
	cin >> ch;
	if (ch == 'y' || ch == 'Y')
		cout << "You were warned!\a\a\n";
	else if (ch == 'n' || ch == 'N')
		cout << "A wise choice ... bye\n";
	else
		cout << "That wasn't a y or n! Apparently you "
			"can't follow\ninstructions, so "
			"I'll trash your disk anyway.\a\a\a\n";
	return 0;
}
```
### 6.2.2 逻辑AND运算符：&&
- 仅当两侧都为true时，结果才为true
- 和||运算符一样，&&也是顺序点，先左后右。左侧为false是，右侧不判断了（**短路**）
```C++
// 6.5 and.cpp -- using the logical AND opeartor
#include <iostream>
const int ArSize = 6;
int main()
{
	using namespace std;
	float naaq[ArSize];
	cout << "Enter the NAAQs {New Age Awareness Quotients} "
		<< "of\nyour neighbors. Program terminates "
		<< "when you make\n" << ArSize << " entries "
		<< "or enter a negative value.\n";
	
	int i = 0;
	float temp;
	cout << "First value: ";
	cin >> temp;
	while (i < ArSize && temp >= 0)
	{
		naaq[i] = temp;
		++i;
		if (i < ArSize)
		{
			cout << "Next value: ";
			cin >> temp;  // so get next value
		}
	}
	if (i == 0)
		cout << "No data--bye\n";
	else
	{
		cout << "Enter your NAAQ: ";
		float you;
		cin >> you;
		int count = 0;
		for (int j = 0; j < i; j++)
			if (naaq[j] > you)
				++count;
		cout << count;
		cout << " of your neighbors have greater awareness of\n"
			<< "the New Age than you do.\n";
	}
	return 0;
}
```
### 6.2.3 用&&来设置取值范围
```C++
// 6.6 more_and.cpp -- using the logical AND operator
#include <iostream>
const char* qualify[4] =
{
	"10,000-memter race.\n",
	"mud tug-of-war.\n",
	"masters canoe jousting.\n",
	"pie-throwing festival.\n"
};
int main()
{
	using namespace std;
	int age;
	cout << "Enter your age in years: ";
	cin >> age;
	int index;

	if (age > 17 && age < 35)
		index = 0;
	else if (age >= 35 && age < 50)
		index = 1;
	else if (age >= 50 && age < 65)
		index = 2;
	else
		index = 3;

	cout << "You qualify for the " << qualify[index];
	return 0;
}
```
- **取值范围测试**：
```C++
if ( 17 < age < 35)  // 不要使用数学符号将表示为这样，编译器不会捕获这种错误，因为它仍然是有效的语法
if ( (17 < age) < 35)  // 根据<运算符从左向右结合，上述表达式含义如←
// 17 < age的值要么为true(1)，要么为false(0)。不管那种都小于35，结果总是true
```
### 6.2.4 逻辑NOT运算符：!
- !运算符将它后面的表达式的真值取反

```C++
// 6.7 not.cpp -- using the not operator
#include <iostream>
#include <climits>
bool is_int(double);
int main()
{
	using namespace std;
	double num;

	cout << "Yo, dude!Enter an integer value: ";
	cin >> num;
	while (!is_int(num))  // continue while num is not int-able
	{
		cout << "Out of range -- please try again: ";
		cin >> num;
	}
	int val = int(num);  // type cast
	cout << "You've enterd the integer " << val << "\nBye\n";
	return 0;
}
bool is_int(double x)
{
	if (x <= INT_MAX && x >= INT_MIN)
		return true;
	else
		return false;
}
```
- 程序先是用double存储的，因为double比int的范围大的多，当输入的值超出int范围时也能打印出来。用long long其实也可以。

### 6.2.5 逻辑运算符细节
- C++逻辑OR和逻辑AND运算符的优先级都低于关系运算符。``` x > 5 && x < 10``` -> ``` (x > 5) && (x < 10)```
- !运算符的优先级高于所有的关系运算符和算术运算符。因此要对表达式求反必须用括号括起来``` !(x > 5)```
- 逻辑AND运算符的优先级高于逻辑OR运算符。因此，表达式:
```C++
age > 30 && age < 45 || weight > 300  // 被解释为：
(age > 30 && age < 45) || weight > 300
(age > 50 || weight > 300) && donation > 100   // 这种情况就必须要用括号括起来了
```
- 一般来说，还是用括号括一下，即便是第一行那种也是。这样也便于阅读，避免忘了优先级而出错或理解错误。
- C++确保程序从左向右进行计算逻辑表达式，并在知道答案后立刻停止。

### 6.2.6 其他表示方式
- 当键盘不提供逻辑符号时，可以用C++标准提供的而另一种表示方式——标识符and、or和not来替代。
- [x] 标识符and、or和not都是C++保留字，这意味着不能将它们用作变量名等。它们不是关键字，因为它们都是已有语言特性的另一种表示方式。它们不是C语言的保留字，若要使用则需要包含头文件iso646.h。C++不要求使用头文件
- 一些区分：
	- 保留字：语言中已经定义过的字，使用者不能再将这些字作为变量名或过程名使用。在一些语言中，一些保留字可能并没有应用于当前的语法中。，这就成了保留字与关键字的区别
	- 关键字：在语言中有特定含义，称为语法中的一部分的那些字。关键字一定是保留字，反过来也差不多。（在Java中，goto是个保留字不是关键字，因此你不能使用它）
	- 标识符：关键字和变量名都算标识符。只不过程序员在遵循一定规则下，可以自己定义变量名。
	- 变量名：是标识符的实例
	- 我觉得：最终还是变量名和关键字这俩，那两个的概念解释实在太少，暂时就这么理解吧。
```C++
#include <iostream>
#include <ciso646>

int main()
{
	using namespace std;

	cout << (0 and 1) << "\t" << (0 && 1)<< endl;
	cout << (1 or 0) << "\t" << (0 || 1) << endl;
	cout << not 0 <<"\t" << !0 << endl;

	return 0;
}
```
## 6.3 字符函数库cctype
- C++从C语言继承了一个与字符相关的、非常方便的函数软件包——cctype
- isalpha(ch); ch是一个字符返回一个非零值，否则返回0
- ispunct(ch); ch是标点符号函数返回true
- isspace()来测试字符是否为空白
```C++
// 6.8 cctypes.cpp -- using the ctype.h library
#include <iostream>
#include <cctype>  // prototypes for character functions
int main()
{
	using namespace std;
	cout << "Enter text for analysis, and type @"
		"to terminate input.\n";
	char ch;
	int whitespace = 0;
	int digits = 0;
	int chars = 0;
	int punct = 0;
	int others = 0;

	cin.get(ch);  // get first character
	while (ch != '@')
	{
		if (isalpha(ch))
			chars++;
		else if (isspace(ch))
			whitespace++;
		else if (isdigit(ch))
			digits++;
		else if (ispunct(ch))
			punct++;
		else
			others++;
		cin.get(ch);
	}
	cout << chars << " letters, "
		<< whitespace << " whitespace, "
		<< digits << " digits, "
		<< punct << " punctuations, "
		<< others << " others.\n";
	return 0;
}
```
## 6.4 ?:运算符
- 三元运算符，常用来替代if else
```C++
// 6.9 condit.cpp -- using the conditional operator
#include <iostream>
int main()
{
	using namespace std;
	int a, b;
	cout << "Enter two integers: ";
	cin >> a >> b;  // 可以啊，一次读两个
	cout << "The larger of " << a << " and " << b;
	int c = a > b ? a : b;
	cout << " is " << c << endl;
	return 0;
}
```
- 与if else相比，条件运算符生成一个表达式，因此是一个值，可以将其赋给变量或将其放到一个更大的表达式中。
- 它也可以嵌套，当代码变复杂是，使用if else可能更清晰。

## 6.5 switch语句
- switch语句能够更容易地从大型列表中进行选择；switch就像指路牌，告诉计算机接下来应该执行哪行代码。
- 每个标签都必须是整数常量表达式，常见的是int或char常量，也可以是枚举量
```C++
// 6.10 switch.cpp -- using the switch statement
#include <iostream>
using namespace std;
void showmenu();  // function prototypes
void report();
void comfort();
int main()
{
	showmenu();
	int choice;
	cin >> choice;
	while (choice != 5)
	{
		switch (choice)
		{
		case 1: cout << "\a\n";
			break;
		case 2: report();
			break;
		case 3: cout << "The boss was in all day.\n";
			break;
		case 4: comfort();
			break;
		default: cout << "That's not a choice.\n";
		}
		showmenu();
		cin >> choice;
	}
	cout << "Bye!\n";
	return 0;
}

void showmenu()
{
	cout << "Please enter 1, 2, 3, 4, or 5:\n"
		"1) alarm			2) report\n"
		"3) ablibi			4) comfort\n"
		"5) quit\n";
}

void report()
{
	cout << "It's been an excellent week for business. \n"
		"Sales are up 120%. Expenses are down 35%.\n";
}

void comfort()
{
	cout << "Your employees think you are the finest CEO\n" 
		"in the industry. The board of directors think\n" 
		"you are the finest CEO in the industry.\n";
}
```
### 6.5.1 将枚举量用作标签
```C++
// 6.11 enum.cpp -- using enum
#include <iostream>
enum {red, orange, yellow, green, blue, violet, indigo};
int main()
{
	using namespace std;
	cout << "Enter color code (0-6): ";
	int code;
	cin >> code;
	while (code >= red && code <= indigo)
	{
		switch (code)
		{
			case red: cout << "Her lips were red.\n"; break;
			case orange: cout << "Her hair were orange.\n"; break;
			case yellow: cout << "Her shoes were yellow.\n"; break;
			case green: cout << "Her nails were green.\n"; break;
			case blue: cout << "Her sweatsuit were blue.\n"; break;
			case violet: cout << "Her eyes were violet.\n"; break;
			case indigo: cout << "Her mood were indigo.\n"; break;
		}
		cout << "Enter color code (0-6): ";
		cin >> code;
	}
	cout << "Bye\n";
	return 0;
}
```
- 通常，cin无法识别枚举类型（它不知道程序员是如何定义它们的），因此该程序要求用户选择选项时输入一个整数。当switch语句将int值和枚举量标签进行比较时，将枚举量提升为int。
- while循环测试条件中，也会将枚举量提升为int类型

### 6.5.2 switch 和 if else
- switch并不是为处理取值范围而设计的。switch语句中的每一个case标签都必须是一个单独的值。另外，这个值必须是整数（包括char）。另外case标签值还必须是常量。相比之下，if else 更通用。
- **提示**：如果既可以用 if else if语句，也可用switch语句，则当选项不少于3个时，应使用switch语句。

### 6.6 break 和 continue语句
```C++
// 6.12 jump.cpp -- using continue and break
#include <iostream>
const int ArSize = 80;
int main()
{
	using namespace std;
	char line[ArSize];

	int spaces = 0;

	cout << "Enter a line of text:\n";
	cin.get(line, ArSize);
	cout << "Complete line:\n" << line << endl;
	cout << "Line through first period:\n";
	for (int i = 0; line[i] != '\0'; i++)
	{
		cout << line[i];
		if (line[i] == '.')
			break;
		if (line[i] != ' ')
			continue;
		spaces++;
	}
	cout << "\n" << spaces << " spaces\n";
	cout << "Done.\n";
	return 0;
}
```
- 在for循环中，continue语句使程序直接跳到更新表达式处，然后跳到测试表达式处。
- 对于while来说，continue将使程序直接跳到测试表达式处
## 6.7 读取数字的循环
```C++
int n;
cin >> n;
```
- 如果用户输入一个单词，而不是数字，将发生4种情况：
	- n的值保持不变；
	- 不匹配的输入将被留在输入队列中
	- cin对象将设置一个错误标记
	- cin方法返回false（如果被转换为bool类型） 
- 方法返回false意味着可以用非数字输入来结束读取数字的循环

```C++
// 6.13 cinfish.cpp -- non-numeric input terminates loop
#include <iostream>
const int Max = 5;
int main()
{
	using namespace std;
	double fish[Max];
	cout << "You may enter up to " << Max
		<< " fish <q to terminate>.\n";
	cout << "fish #1: ";
	int i = 0;
	while (i < Max && cin >> fish[i])  // 需要注意
	{
		if (++i < Max)  // 需要注意
			cout << "fish #" << i + 1 << ": ";
	}
	double total = 0.0;
	for (int j = 0; j < i; j++)
		total += fish[j];
	if (i == 0)
		cout << "No fish\n";
	else
		cout << total / i << " = average weight of "
		<< i << " fish\n";
	cout << "Done.\n";
	return 0;
}
```
- i < Max放到&&左边是有讲究的，运用&&的“短路”现象以防止数组越界
- 6.13中,的表达式cin >> fish[i] 实际上是一个cin方法调用，该函数返回cin。如果cin位于测试条件中，则将被转换为bool类型。如果输入成功，则转换后的值为true，否则为false。
```C++
// 6.14 cingolf.cpp -- non-numeric input skipped
#include <iostream>
const int Max = 5;
int main()
{
	using namespace std;
	// get data
	int golf[Max];
	cout << "Please enter your golf scores.\n";
	cout << "You must enter " << Max << " rounds.\n";
	int i;
	for (i = 0; i < Max; i++)
	{
		cout << "round #" << i + 1 << ": ";
		while (!(cin >> golf[i])) {  // 若输入的不满足golf[i]的要求，返回false
			cin.clear();  // 1.重置cin以接受新的输入
			while (cin.get() != '\n')  // 2.删除错误输入
				continue; // get rid of bad input
			cout << "Please enter a number: "; // 3.提示用户再输入
		}
	}
	// calculate average
	double total = 0.0; 
	for (i = 0; i < Max; i++)
		total += golf[i];
	// report results
	cout << total / Max << " = average score "
		<< Max << " rounds\n";
	return 0;
}
```
- 注意：程序必须先重置cin，才能删除错误输入

## 6.8 简单文件输入/输出
- 控制台输入/输出；文件输入/输出

### 6.8.1 文本I/O和文本文件
- [ ] 学完一遍后，阅读cin的原函数是如何编写的。
- 本章讨论的文件I/O相当于控制台I/O，因此仅适用于文本文件(txt等)
### 6.8.2 写入到文本文件
- cout用于控制台的一些内容：
	- 必须包含头文件iostream
	- 头文件iostream定义了一个用于处理输出的ostream类
	- 头文件iostream声明了一个名为cout的ostream变量（对象）
	- 必须指明名称空间std；例如，为引用元素cout和endl，必须使用编译指令using或前缀std::。
	- 可以结合使用cout和运算符<<来显示各种类型的数据
- 文件输出与其极其相似：
	- 必须包含头文件fstream
	- 头文件fstream定一个了一个用于处理输出的ofstram类
	- 需要声明一个或多个ofstream变量（对象），并以自己喜欢的方式对其进行命名，条件是遵守常用的命名规则。
	- 必须指明名称空间std;例如，为引用元素ofstream，必须使用编译指令using或前缀std::。
	- 需要将ofstream对象与文件关联起来。为此，方法之一就是使用open()方法
	- 使用完文件后，应使用方法close()将其关闭
	- 可结合使用ofstream对象和运算符来输出各种类型的数据

- 注意：虽然头文件iostream提供了一个预先定义好的名为cout的ostream对象，但您必须声明自己的ofstream对象，为其命名，并将其同文件关联起来。
- 总之，文件输出的主要步骤如下。
	- 1.包含头文件fstream
	- 2.创建一个ofstream对象
	- 3.将该ofstream对象同一个文件关联起来
	- 4.就像使用cout那样使用该ofstream对象
```C++
// 6.15 -- outline.cpp
#include <iostream>  // ostream->cout		istream->cin
#include <fstream>   // ofsteam		  	ifstream
int main()
{
	using namespace std;

	char automobile[50];
	int year;
	double a_price;
	double d_price;

	ofstream outFile;  // create object for output
	outFile.open("carinfo.txt");  // associate with file

	cout << "Enter the make and model of automobile: ";
	cin.getline(automobile, 50);
	cout << "Enter the model year: ";
	cin >> year;
	cout << "Enter the original asking price: ";
	cin >> a_price;
	d_price = 0.913 * a_price;

	// display information on screen with cout
	cout << fixed;  // 用一般的方式输出浮点数，而不是科学计数法
	cout.precision(2);  // 设置精确度为2，并返回上一次的设置
	cout.setf(ios_base::showpoint);  // 显示浮点数小数点后的0
	cout << "Make and model: " << automobile << endl;
	cout << "Year: " << year << endl;
	cout << "Was asking $" << a_price << endl;
	cout << "Now asking $" << d_price << endl;

	// now do exact same things using outFile instead of cout
	outFile << fixed;  // outFile的用法和cout一模一样，只不过cout输出的屏幕，outFile输出到文件中
	outFile.precision(2);
	outFile.setf(ios_base::showpoint);
	outFile << "Make and model: " << automobile << endl;
	outFile << "Year: " << year << endl;
	outFile << "Was asking $" << a_price << endl;
	outFile << "Now asking $" << d_price << endl;

	outFile.close();   // done with file
	return 0;
}
```
- close(): 如果你忘记关文件，程序正常终止时将自动关闭它
- 如果文件已经存在，open()将首先截断该文件，即将其长度截短到零——丢其原有的内容，然后将新的输入加入到该文件中。（第17章介绍如何修改这种行为）

### 6.8.3 读取文本文件
- 控制台输入：
	- 必须包含头文件iostream
	- 头文件iostream定义了一个用处理输入的istream类
	- 头文件iostream声明了一个名为cin的ostream变量（对象）
	- 必须指明名称空间std；例如，为引用元素cin，必须使用编译指令using或前缀std::。
	- 可以结合使用cin和运算符>>来显示各种类型的数据
	- 可以使用cin和eof()、fail()方法来判断输入是否成功
	- 对象cin本身被用作测试条件时，如果最后一个读取操作成功，它将被转换为布尔值true，否则转换为false
- 文件输入与其极其相似：
	- 必须包含头文件fstream
	- 头文件fstream定一个了一个用于处理输入的ifstram类
	- 需要声明一个或多个ifstream变量（对象），并以自己喜欢的方式对其进行命名，条件是遵守常用的命名规则。
	- 必须指明名称空间std;例如，为引用元素ifstream，必须使用编译指令using或前缀std::。
	- 需要将ifstream对象与文件关联起来。为此，方法之一就是使用open()方法
	- 使用完文件后，应使用方法close()将其关闭
	- 可结合使用ifstream对象和运算符>>来读取各种类型的数据
	- 可以使用ifstream对象和get()方法来读取一个字符，使用ifstream对象和getline()来读取一行字符。
	- 可以结合使用ifstream和eof()、fail()等方法来判断输入是否成功
	- ifstream对象本身用作测试条件时，如果最后一个读取操作成功，它将转换为布尔值true，否则转换为false。

```C++
// 6.16 sumafile.cpp -- functions with an array argument
#include <iostream>
#include <fstream>
#include <cstdlib>  // support for exit()
const int SIZE = 60;
int main()
{
	using namespace std;
	char filename[SIZE];
	ifstream inFile;  // object for handling file input
	
	cout << "Enter name of data file: ";
	cin.getline(filename, SIZE);
	inFile.open(filename);  // associate inFile with a file
	if (!inFile.is_open())  // failed to open file
	{
		cout << "Could not open the file " << filename << endl;
		cout << "Program terminating.\n";
		exit(EXIT_FAILURE);  // EXIT_FAILURE:用于同操作系统通信的参数值
	}
	
	double value;
	double sum = 0.0;
	int count = 0;  // number of items read

	inFile >> value;  // get first value?
	// 效果类似与inFile.is_open()，是比较老的方法，没is_open()那么广泛
	while (inFile.good())
	{
		cout << "Values: " << value << endl;
		++count;	// one more item read
		sum += value;  // calculate running total
		inFile >> value;  // get next value
	}
	if (inFile.eof())  // enf of file
		cout << "End of file reached.\n";
	else if (inFile.fail())
		cout << "Input terminated by data mismatch.\n";  // 数据不匹配
	else
		cout << "Input terminated for unknown reason.\n";
	if (count == 0)
		cout << "No data processed.\n";
	else
	{
		cout << "Items read: " << count << endl;
		cout << "Sum: " << sum << endl;
		cout << "Average: " << sum / count << endl;
	}
	inFile.close();
	return 0;
}
```
- 声明一个ifstream对象并将其同文件关联起来后，便可以像使用cin那样使用它。所有可用于cin的操作和方法都可用于ifstream对象（前面的inFile和fin也是）
- 注意：有些文本编译器（MS的记事本）不会自动在最后一行末尾加上换行符。需要在文本最后按下回车键，再保存文件，才能保证最后一行数据被读入。
- 读取文件时：有几点需要检查：
	- 首先，程序读取文件时不应超过EOF。如果最后一次读取数据时遇到EOF，方法eof()将返回true
	- 其次，程序可能遇到类型不匹配的情况。这时方法fail()将返回true
	- 最后，如果发生了文件受损或硬件故障这样的问题，方法bad()将返回true。
	- 不需要分别检查这些情况，方法good()在没有任何错误发生时返回true()
```C++
// 方式1：循环终止
while (inFile.good())
{
	...
}

// 方式2：循环终止
if (inFile.eof())  // enf of file
	cout << "End of file reached.\n";
else if (inFile.fail())
	cout << "Input terminated by data mismatch.\n";  // 数据不匹配
else
	cout << "Input terminated for unknown reason.\n";

// 方式3。最后一次读取输入的操作是否成功很重要，所以...
inFile >> value;  // get first value
while (inFile.good())  // while input good and not at EOF
{
	// loop body goes here
	inFile >> value;  // get next value
}
// 方式4：表达式inFile >> value的结果为inFile，而在需要一个bool值的情况下，inFile的结果为inFIle.good()，即true或false。遵循了在测试之前进行读取的规则
// omit pre-loop input
while (inFile >> value)  // read and test for success
{
	// loop body goes here
	// omit end-of-loop input
}
```
## 6.9 总结
- cctype字符函数库提供了一组方便的、功能强大的工具，可用于分析字符输入。
## 6.10 复习题
- [X] 第3题
- [X] 第5题
- [X] 第8题
```C++
// 1.减少运算量，因为' '和'\n'不可能同时出现
// 2.ch将会由字符型提升为int型，输出结果也为int，需要char(ch+1)，或者char(++ch)
// 3.输出：ct1 = 3, ct2 = 0 （错误） ct1 = 9， ct2 = 9.
// 4. 
//  a: weight >= 115 && weight < 125 
//  b: ch == 'q' || ch == 'Q'
//  c: x != 26 && x % 2 == 0
//  d: x % 26 != 0 && x % 2 == 0
//  e: guest == 1 || (donation >= 1000  && donation << 2000)
//  f: (ch >= 'a' && ch <= 'z') || (ch >= 'A' && ch <= 'Z')
// 5.是相同的 （错误）。如果x为10, 则!x 为 0, !!x = 1。如果x为bool变量，则!!x 为 x
// 6.
(x >= 0) ? x : -x;
// 7.
switch (ch)
{
	case 'A': a_grade++; break;
	case 'B': b_grade++; break;
	case 'C': c_grade++; break;
	case 'D': d_grade++; break;
	default: f_grade++;
}
// 8.原来的程序：输入非数字cin会缓存这个字符，程序陷入死循环。但是如果是字符型，输错成其他的数字或者输错字符，程序都可以正常运行
// 9.
int line = 0;
char ch;

// while ( cin.get(ch) && ch != 'Q')
while ((ch = cin.get()) != 'Q')
{
	if ( ch == '\n')
		line++;
}
```
## 6.11 编程练习
- 1
```C++
#include <iostream>
#include <cctype>

int main()
{	
	using namespace std;
	char ch;
	
	while ((ch = cin.get()) != '@')
	{
		if (islower(ch))  // islower
			cout << char(toupper(ch));
		else if (isupper(ch))  // isUpper
			cout << char(tolower(ch));
		else if (ch == '\n')
			cout << endl;
		else
			continue;
	}

	cout << "Program terminated!" << endl;;
	return 0;
}
```
```C++
// 修改后
#include <iostream>
#include <cctype>

int main()
{
	using namespace std;
	char ch;

	while (cin >> ch && ch != '@')
	{
		if (islower(ch))  // islower
			ch = toupper(ch);
		else if (isupper(ch))  // isUpper
			ch = tolower(ch);
		else if (isdigit(ch))
			continue;
			
		cout << ch << endl;
	}

	cout << "Program terminated!" << endl;;
	return 0;
}

// 再修改后
#include <iostream>
#include <cctype>

int main()
{
	using namespace std;
	char ch;

	while ((ch = cin.get()) != '@')
	{
		if (isdigit(ch))
			continue;
		else if (islower(ch))
			ch = toupper(ch);
		else  // upper and other
			ch = tolower(ch);

		cout << ch;
	}

	cout << "Program terminated!" << endl;;
}
```
- 2
```C++
#include <iostream>
#include <array>
using namespace std;

int main()
{
	array<double, 10> donations;
	int i = 0;
	double sum = 0.0;

	cout << "Please Enter donation less than 10:\n";
	while ((i < 10) && (cin >> donations[i]))  // i < 10要放在左边，防止数组越界
	{
		sum += donations[i];
		i++;
	}

	if (i == 0)
		return 0;
	else
	{
		cout << "i = " << i << endl;
		cout << "sum = " << sum << endl;
	}
		

	double average = sum / i;
	int count = 0;

	for (int j = 0; j < i; j++)
	{
		if (donations[j] > average)
			count++;
	}
	cout << fixed;
	cout << "\nAverage: " << average << endl;
	cout << count << " numbers that are greater than the average" << endl;
	return 0;

}// 0.1 0.2 0.3 0.4 0.5 0.6 0.7 0.8 0.9 1.0

// 修改后
#include <iostream>
#include <array>
using namespace std;

int main()
{
	array<double, 10> donations;
	int i = 0;
	double sum = 0.0;

	cout << "Please Enter donation less than 10:\n";
	while ((i < 10) && (cin >> donations[i]))  // i < 10要放在左边，防止数组越界
	{
		sum += donations[i];
		i++;
	}

	if (i == 0)
		return 0;
	else
	{
		cout << "i = " << i << endl;
		cout << "sum = " << sum << endl;
	}


	double average = sum / i;
	int count = 0;

	while(i - 1 >= 0)
	{
		if (donations[--i] > average)
			count++;
	}

	cout << fixed;
	cout << "\nAverage: " << average << endl;
	cout << count << " numbers that are greater than the average" << endl;
	return 0;

}// 0.1 0.2 0.3 0.4 0.5 0.6 0.7 0.8 0.9 1.0
```
```C++
#include <iostream>
#include <cctype>
#include <array>
using namespace std;
const int ArSize = 10;

int main()
{	
	array<double, ArSize> nums;
	double donation, sum = 0;
	int i = 0;
	cout << "Enter a maximum of 10 numbers: ";
	while (cin >> donation)
	{
		cout << "donation: " << donation << endl;
		nums[i++] = donation;
		sum += donation;
	}

	double average = 0;
	int count = 0;
	if (i > 0)
	{
		average = sum / i;
		for (int j = 0; j < i; ++j)
			if (nums[i] > average)
				++count;
	}
	cout << "Average: " << average << ", nums: " << i << endl;
	return 0;
}
```

- 3
```C++
#include <iostream>
using namespace std;

int main()
{
	char ch;
	cout << "Please enter one of the following choices:" << endl;
	cout << "a) apple \t\t c) carnivore\n";
	cout << "p) pianist \t\t t) tree\n";
	cout << "g) game\n";
	while (true)
	{
		cin >> ch;
		if (ch != 'a' && ch != 'c' && ch != 'p' && ch != 't' && ch != 'g')
		{
			cin.clear();
			while (cin.get() != '\n')
				continue;
			cout << "Please enter a a, c, p, t, or g: ";
		}
		else
			break;
	}

	switch (ch)
	{
	case 'a': cout << "give your a apple." << endl; break;
	case 'c': cout << "You describe yourself as a born-again carnivore." << endl; break;
	case 'p': cout << "You are a talented pianist." << endl; break;
	case 't': cout << "a maple is a tree." << endl; break;
	default: cout << "玩局《暖雪》吧！" << endl;
	}

	return 0;
}
```
```C++
// 修改后
#include <iostream>
using namespace std;

void show(void);

int main()
{
	char ch;
	
	show();
	cin >> ch;
	while (ch != 'a' && ch != 'c' && ch != 'p' && ch != 't' && ch != 'g')
	{
		cin.clear();
		while (cin.get() != '\n')
			continue;
		cout << "Please enter a, c, p, t, or g: ";
		cin >> ch;
	}

	switch (ch)
	{
	case 'a': cout << "give your a apple." << endl; break;
	case 'c': cout << "You describe yourself as a born-again carnivore." << endl; break;
	case 'p': cout << "You are a talented pianist." << endl; break;
	case 't': cout << "a maple is a tree." << endl; break;
	default: cout << "玩局《暖雪》吧！" << endl;
	}

	return 0;
}

void show(void)
{
	cout << "Please enter one of the following choices:" << endl;
	cout << "a) apple \t\t c) carnivore\n";
	cout << "p) pianist \t\t t) tree\n";
	cout << "g) game\n";
}
```

- 4.这道题有点花里胡哨，到时候看视频跟着敲得了。
```C++
#include<iostream>

const int strsize = 30;
const int memberSize = 4;

struct bop {
	char fullname[strsize];  // real name
	char title[strsize];  // job title
	char bopname[strsize];  // secret BOP name
	int preference;  // 0 = fullname, 1 = title, 2 = bopname
};

bop members[memberSize] =
{
	{"Rick", "level_A", "RR", 0},
	{"Jack", "level_B", "JJ", 1},
	{"Micheal", "Level_C", "MM", 2},
	{"Rose", "Levle_B", "RR", 0}
};

void ShowMenu();
void ShowFullName();
void ShowBopName();
void ShowTitle();
void ShowPerference();

int main()
{
	using std::cout;
	using std::cin;

	ShowMenu();
	
	char ch;
	cout << "Enter your choice: ";
	cin >> ch;
	while (ch != 'q')
	{
		switch (ch)
		{
		case 'a': ShowFullName(); break;
		case 'b': ShowTitle(); break;
		case 'c': ShowBopName(); break;
		case 'd': ShowPerference(); break;
		default: std::cout << "Wrong input!" << std::endl;
		}

		cin.clear();
		while (cin.get() != '\n')
			continue;
		
		ShowMenu();
		std::cout << "Next choice: ";
		cin >> ch;
	}

	std::cout << "Bye!" << std::endl;
	return 0;
}

void ShowMenu()
{
	using std::cout;

	cout << "--------------------------------------------------------\n";
	cout << "Benevolent Order of Programmer Report\n";
	cout << "a. display by name \t\t b. display the title\n";
	cout << "c. display by bopname \t\t d.display by preference\n";
	cout << "q.quit\n";
	cout << "--------------------------------------------------------\n";
}

void ShowFullName()
{
	for(int i = 0; i < memberSize; ++i)
	{
		std::cout << members[i].fullname << std::endl;
	}
}

void ShowBopName()
{
	for (int i = 0; i < memberSize; ++i)
	{
		std::cout << members[i].bopname << std::endl;
	}
}

void ShowTitle()
{
	for (int i = 0; i < memberSize; ++i)
	{
		std::cout << members[i].title << std::endl;
	}
}

void ShowPerference()
{
	for (int i = 0; i < memberSize; ++i)
	{
		switch (members[i].preference)
		{
		case 0: std::cout << members[i].fullname << std::endl; break;
		case 1: std::cout << members[i].title << std::endl; break;
		case 2: std::cout << members[i].bopname << std::endl; break;
		}
	}
}
```

- 5
```C++
#include <iostream>
double tax(double);

int main()
{
	using namespace std;
	double money;
	while (true)
	{
		cout << "Enter your money: ";
		if (!(cin >> money) || money < 0)
			break;
		else
			cout << "Tax: " << tax(money) << endl;
	};

	cout << "Done.\n";
}

double tax(double n)
{
	if (n <= 5000)
		return 0;
	else if (n >= 5001 && n <= 15000)
		return (n - 5000) * 0.1;
	else if (n >= 15001 && n <= 35000)
		return 10000 * 0.1 + (n - 15000) * 0.15;
	else
		return 10000 * 0.1 + 20000 * 0.15 + (n - 35000) * 0.2;
}
```

```C++
// 修改后
#include <iostream>
double tax(double);

int main()
{
	using namespace std;
	double money;

	cout << "Enter your money: ";
	while ((cin >> money) || money >= 0)
	{
		cout << "Tax: " << tax(money) << endl;
		cout << "Enter your money: ";
	};

	cout << "Done.\n";
}

double tax(double n)
{
	if (n <= 5000)
		return 0;
	else if (n >= 5001 && n <= 15000)
		return (n - 5000) * 0.1;
	else if (n >= 15001 && n <= 35000)
		return 10000 * 0.1 + (n - 15000) * 0.15;
	else
		return 10000 * 0.1 + 20000 * 0.15 + (n - 35000) * 0.2;
}
```
- 6
```C++
#include <iostream>
#include <string>
using namespace std;
struct Patron {
	string name;
	double money = 0.0;
};

int main()
{
	int size;
	cout << "Enter the number of patrons: ";
	cin >> size;
	Patron* patrons = new Patron[size];
	for (int i = 0; i < size; ++i)
	{
		cout << "Enter " << i + 1 << "th name:";
		cin.get();
		getline(cin, patrons[i].name); // ?为啥不是->
		cout << "Enter " << i + 1 << "th money:";
		cin >> patrons[i].money;
	}

	cout.setf(ios_base::fixed);
	cout.precision(0);
	cout << "Grand Patrons:\n";
	for (int i = 0; i < size; ++i)
		if (patrons[i].money > 10000)
			cout << "\t" << patrons[i].name
				 << "\t" << patrons[i].money << endl;
	cout << "Patrons:\n";
	for (int i = 0; i < size; ++i)
		if (patrons[i].money <= 10000)
			cout << "\t" << patrons[i].name
			<< "\t" << patrons[i].money << endl;

	delete[] patrons;
	return 0;
}
```
```C++
// 修改后，这题有点模棱两可，代码可能有差别
#include <iostream>
#include <string>
using namespace std;
struct Patron {
	string name;
	double money = 0.0;
};

int main()
{
	int size;
	cout << "Enter the number of patrons: ";
	cin >> size;
	Patron* patrons = new Patron[size];

	for (int i = 0; i < size; ++i)
	{
		cin.get();

		cout << "Enter " << i + 1 << "th name:";
		getline(cin, patrons[i].name); // 为啥不是->？因为数组的元素不是指针，是结构体
		
		cout << "Enter " << i + 1 << "th money:";
		cin >> patrons[i].money;
	}

	cout.setf(ios_base::fixed);
	cout.precision(0);

	cout << "-----Grand Patrons:-----\n";
	for (int i = 0; i < size; ++i)
	{
		if (patrons[i].money > 10000)
		{
			cout << patrons[i].name
				<< ": " << patrons[i].money << endl;
		}	
	}
		
	cout << "-----Patrons:-----\n";
	for (int i = 0; i < size; ++i)
	{
		if (patrons[i].money <= 10000)
		{
			cout <<  patrons[i].name << ": " << patrons[i].money << endl;
		}
		else
		{
			cout << "none\n";
		}
	}
		
	delete[] patrons;

	return 0;
}
```
- 7
```C++
#include<iostream>
#include<string>
#include<cctype>

using namespace std;

int main(void)
{
	string words;
	int vowels = 0, consonants = 0, others = 0;

	cout << "Enter words(q to quit):" << endl;
	while ((cin >> words) && (words != "q"))
	{
		if (isalpha(words[0]))
		{
			switch (words[0])
			{
			case 'a':
			case 'e':
			case 'i':
			case 'o':
			case 'u':
			case 'A':
			case 'E':
			case 'I':
			case 'O':
			case 'U':  vowels++; break;
			default:  consonants++;
			}
		}
		else
		{
			others++;
		}
	}

	cout << vowels << " words begining with vowels\n";
	cout << consonants << " words begining with consonants\n";
	cout << others << " others" << endl;
	return 0;
}
```

- 8
- [x] 每行末尾都会多读一个字符，是不是换行没忽略？
```C++
#include <iostream>
#include <fstream>
#include <cstdlib>
using namespace std;
const int SIZE = 60;

int main()
{
	// 1.打开文件
	char filename[SIZE];
	ifstream inFile;  // object for handling file input
	cout << "Enter name of data file: ";
	cin.getline(filename, SIZE);
	inFile.open(filename);  // associate inFile with a file
	if (!inFile.is_open())
	{
		cout << "Could not open the file" << filename << endl;
		cout << "Program terminating.\n";
		exit(EXIT_FAILURE);
	}

	// 2.处理文件+显示结果
	char value;
	int count = 0;

	while (inFile.good())
	{
		inFile >> value;
		if (value == '\n' || inFile.eof())
			continue;  // 跳过换行符和EOF
		else
		{
			++count;
			cout << "count: " << count
				<< "\tValues: " << value << endl;
		}

	}
	if (inFile.eof())
		cout << "End of file reached.\n" << count << " characters";
	else if (inFile.fail())
		cout << "Input terminated by data mismatch.\n";
	else
		cout << "Input terminated for unknown reason.\n";

	// 3.关闭文件
	inFile.close();
	return 0;
}
```
```C++
// 修改后
#include <iostream>
#include <fstream>
#include <cstdlib>
using namespace std;
const int SIZE = 60;

int main()
{
	// 1.打开文件
	char filename[SIZE];
	ifstream inFile;  // object for handling file input
	cout << "Enter name of data file: ";
	cin.getline(filename, SIZE);  // string: getline(cin, filename);
	inFile.open(filename);  // associate inFile with a file
	if (!inFile.is_open())
	{
		cout << "Could not open the file" << filename << endl;
		cout << "Program terminating.\n";
		exit(EXIT_FAILURE);
	}

	// 2.处理文件+显示结果
	char value;
	int count = 0;

	while (inFile.good())  // or inFile.eof()  
	{
		if (inFile >> value)
		{
			++count;
			cout << "count: " << count
				<< "\tValues: " << value << endl;
		}
	}

	if (inFile.eof())
		cout << "End of file reached.\n" << count << " characters";
	else if (inFile.fail())
		cout << "Input terminated by data mismatch.\n";
	else
		cout << "Input terminated for unknown reason.\n";

	// 3.关闭文件
	inFile.close();
	return 0;
}
```

- 9
```C++
#include <iostream>
#include <fstream>
#include <string>
using namespace std;
const int SIZE = 60;

struct Patron {
	string name;
	double money = 0.0;
};
int main()
{
	// 1.打开文件
	char filename[SIZE];
	ifstream inFile;  // object for handling file input
	cout << "Enter name of data file: ";
	cin.getline(filename, SIZE);
	inFile.open(filename);  // associate inFile with a file
	if (!inFile.is_open())
	{
		cout << "Could not open the file" << filename << endl;
		cout << "Program terminating.\n";
		exit(EXIT_FAILURE);
	}

	// 2.处理文件+显示结果
	int size, i = 0;
	inFile >> size;  // 捐款人数
	Patron* patrons = new Patron[size];
	cout << "inFile.isopen(): " << inFile.is_open() << endl;
	cout << "size: " << size << endl;
	while (inFile.good() && i < size)
	{
		inFile.get();  // 吃掉文件末尾换行符
		getline(inFile, patrons[i].name);
		cout << "Read name: " << patrons[i].name << endl;

		inFile >> patrons[i].money;
		cout << "Read money: " << patrons[i].money << endl;
		i++;
	}
	if (inFile.eof())
		cout << "End of file reached.\n";
	else if (inFile.fail())
		cout << "Input terminated by data mismatch.\n";
	else
		cout << "Input terminated for unknown reason.\n";
	
	
	// 3.显示结果 + 关闭文件
	cout.setf(ios_base::fixed);
	cout.precision(0);
	cout << "Grand Patrons:\n";
	for (int i = 0; i < size; ++i)
		if (patrons[i].money > 10000)
			cout << "\t" << patrons[i].name
			<< "\t" << patrons[i].money << endl;
	cout << "Patrons:\n";
	for (int i = 0; i < size; ++i)
		if (patrons[i].money <= 10000)
			cout << "\t" << patrons[i].name
			<< "\t" << patrons[i].money << endl;
	
	inFile.close();
	delete[] patrons;
	return 0;
}   // D:/Desktop/ep-data.txt
```







