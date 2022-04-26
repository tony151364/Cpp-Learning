# 第 4 章 复合类型
- 复合类型是使用其他类型来创建的

## 4.1 数组
- 数组（array）是一种数据格式，能够存储多个同类型的值。
- 数组声明``` short months[12] ```：
  - 存储在每个元素中的值的类型
  - 数组名
  - 数组中的元素数: 必须是整型常数或const值，也可以是常量表达式（如 8 * sizeof(int)，不能是变量。

```C++
// 4.1 arrayone.cpp -- small arrays of integers
#include <iostream>

int main()
{
	using namespace std;
	int yams[3];
	yams[0] = 7;
	yams[1] = 8;
	yams[2] = 6;

	int yamcosts[3] = { 20, 30, 5 };
	cout << "Total yams = ";
	cout << yams[0] + yams[1] + yams[2] << endl;
	cout << yamcosts[1] << " cents per yam.\n";
	
	int total = yams[0] * yamcosts[0] + yams[1] * yamcosts[1];
	total = total + yams[2] * yamcosts[2];
	cout << "The total yam expense is " << total << " cents.\n";

	cout << "\nSize of yams array = " << sizeof yams;
	cout << " bytes.\n";
	cout << "Size of one element = " << sizeof yams[0];
	cout << " bytes.\n";
	return 0;
}
```
### 4.1.2 数组的初始化规则
- 只有在定义数组时才能使用初始化，此后就不能使用了，也不能将一个数组赋给另一个数组。但是可以使用下标分别给数组中的元素赋值。
```C++
float hotelTips[5] = {5.0, 2.5};  // 只对前两个数值初始化
long total[500] = {0};  // 全部元素设置为0
long total2[500] = {1};  // 第一个元素设置为1，其他元素被设置为0

short things[] = {1, 5, 4, 3};  // 编译器自动计数
int num_elements = sizeof things / sizeof(short);  // 计算元素个数
```
### 4.1.3 C++数组初始化方法
- 第3章说过，C++11将使用大括号的初始化（列表初始化）作为一种通用初始化方式，可用于所有类型。
- C++新增一些功能
 ```C++
 // 首先；数组初始化可以省略等号
 double earnings[4] {1.2e4, 1.6e4, 1.1e4, 1.7e4};  
 
 // 其次可不在大括号内包含任何东西，这把所有元素都设置为零。
 unsigned int counts[10] = {};  
 
 // 第三，列表初始化禁止缩窄转换，这在第3章介绍过
 long plifs[] = {25, 92, 3.0};  // not allowed,因为浮点转化为整型是缩窄操作
 char slifs[4] {'h', 'i', 1122011, '\0');  // note allowed, 1122011超出了char的取值范围
 char tlifs[4] {'h', 'i', 112, '\0'};  // allowed,112在char取值范围之内
 ```
 
 ## 4.2 字符串
- C++处理字符串的两种方式：
	- C style string
	- 基于string类库的方法

- C 风格字符串存储在字符数组中，以'\0'结尾
```C++
char dog[8] = {'b', 'e', 'a', 'u', 'x', ' ', 'I', 'I'};  // not a string!
char dog[8] = {'b', 'e', 'a', 'u', 'x', ' ', 'I', 'I', '\0'};  // a string!
char bird[11] = "Mr.Cheeps";   // a string 
char fish[] = "Bubbles";  // let the compiler count
```
### 4.2.1 拼接字符串常量
```C++
// 下面所有输出语句都是等效的
cout << "I like you," " you like me.\n";
cout << "I like you, you like me.\n";
cout << "I like you, you"
" like me.\n";
// 第一个字符串中的\0字符将被第二个字符串的第一个字符取代
```
### 4.2.2 在数组中使用字符串
```C+++
// 4.2 string.cpp -- storing strings in an array
#include <iostream>
#include <cstring>  // for the strlen() function
int main()
{
	using namespace std;
	const int Size = 15;
	char name1[Size];  // empty array
	char name2[Size] = "C++owboy";  // initialized array

	cout << "Howdy! I`m " << name2;
	cout << "! What's your name?\n";
	cin >> name1;
	cout << "Well, " << name1 << ", your name has ";
	cout << strlen(name1) << " letters and is stored\n";
	cout << "in an array of " << sizeof(name1) << " bytes.\n";
	cout << "Your initial is " << name1[0] << ".\n";
	name2[3] = '\0';  // set to null character
	cout << "Here are the first 3 characters of my name: ";
	cout << name2 << endl;
	return 0;
}
```

#### 4.2.3 字符串输入
```C++
// 4.3 instr1.cpp -- reading more than one string
#include <iostream>

int main()
{
	using namespace std;
	const int ArSize = 15;
	char name[ArSize];
	char dessert[ArSize];

	cout << "Enter your name:\n";
	cin >> name;
	cout << "Enter your favorite dessert: \n";
	cin >> dessert;
	cout << "I have some delicious " << dessert;
	cout << " for you, " << name << ".\n";
	return 0;
}
```
- cin使用空白(空格、制表符和换行符)来确定字符串的结束位置.这意味着cin在获取字符数组时，只读取一个单词。

### 4.2.4 每次读入一行字符串输入
- getline()和get() 这两个函数都读取一行输入，直到达到换行符。
- 然而，随后getline()将丢弃换行符，而get()将换行符保留在输入序列中。

#### 1.面向行的输入：getline()
```C++
// 以回车为结尾。如果参数为20，则函数最多读取19个字符，自动末尾添加空字符
cin.getline(name, 20);
```
```C++
// 4.3 instr2.cpp -- reading more than one word with getline
#include <iostream>
int main()
{
	using namespace std;
	const int ArSize = 20;
	char name[ArSize];
	char dessert[ArSize];

	cout << "Enter your name:\n";
	cin.getline(name, ArSize);
	cout << "Enter your favorite dessert: \n";
	cin.getline(dessert, ArSize);
	cout << "I have some delicious " << dessert;
	cout << " for you, " << name << ".\n";
	return 0;
	// 换行符替换为空字符
}
```

##### 2.面向行的输入：get()
```C++
cin.get(name, ArSize);  // read first line
cin.get();  // read new line
cin.get(dessert, Arsize);  // read second line

cin.get(name, ArSize).get();  // concatenate member functions
```

#### 3.空行和其他问题
- [ ] 没太看懂

### 4.2.5 混合输入字符串和数字
```C++
// 4.6 numstr.cpp -- following number input with line input
#include <iostream>
int main()
{
	using namespace std;
	cout << "What year was your house built?\n";
	int year;
	(cin >> year).get();  // 因为多个换行符
	cout << "What is its street address?\n";
	char address[80];
	cin.getline(address, 80);
	cout << "Year built: " << year << endl;
	cout << "Address: " << address << endl;
	cout << "Done!\n";
	return 0;
}
```
- C++程序常使用指针来处理字符串

## 4.3 string类简介
```C++
// 4.7 strtype1.cpp --using the C++ string class
#include <iostream>
#include <string>  // make string class available
int main()
{
	using namespace std;
	char charr1[20];
	char charr2[20] = "jaguar";
	string str1;
	string str2 = "panther";

	cout << "Enter a kind of feline: ";
	cin >> charr1;
	cout << "Enter another kind of feline: ";
	cin >> str1;
	cout << "Here are some felines:\n";
	cout << charr1 << " " << charr2 << " "
		<< str1 << " " << str2 // use cout for output
		<< endl;
	cout << "The third letter in " << charr2 << " is "
		<< charr2[2] << endl;
	cout << "The third letter in " << str2 << " is "
		<< str2[2] << endl;  // use array notation

	return 0;
}
```
- 与字符数组的相同
	- 可使用C风格字符串来初始化string对象
	- 可以使用cin和cout输入输出string对象
	- 可以使用下标访问string对象中的字符

- 区别：
	- 可以将string对象声明为简单变量，而不是数组 

- 可以将char数组视为一组用于存储一个字符串的char存储单元，而string类变量是一个表示字符串的实体

### 4.3.1 C++11 字符串初始化
- 列表初始化可用于C-风格字符串和string对象
```C++
char first_data[] = {'Le chapon Dodu"};
char second_date[] {"The Elegant Plate"};
string third_date = {"The Bread Bowl"};
string fourth_date {"Hank`s Fine Eats"};
```

### 4.3.2 赋值、拼接和附加
- 不能将一个数组赋给另一个数组，但可以将一个string对象赋给另一个string对象
- string 类简化了字符串合并操作。可以使用运算符+将两个string对象合并起来，还可以使用运算符+=将字符串附加到string对象的末尾。
```C++
string str3;
str3 = str1 + str2;
str1 += str2;
```

```C++
// 4.8 strtype2.cpp -- assigning, adding, and appending
#include <iostream>
#include <string>
int main()
{
	using namespace std;
	string s1 = "penguin";
	string s2, s3;

	cout << "You can assign one strng object to another: s2 = s1\n";
	s2 = s1;
	cout << "s1: " << s1 << ", s2: " << s2 << endl;
	cout << "You can assign a C-style string to a string object.\n";
	cout << "s2 = \"buzzard\"\n";
	s2 = "buzzard";
	cout << "s2: " << s2 << endl;
	cout << "You can concatenate strings: s3 = s1 + s2\n";
	s3 = s1 + s2;
	cout << "s3: " << s3 << endl;
	cout << "You can append  strings.\n";
	s1 += s2;
	cout << "s1 += s2 yields s1 = " << s1 << endl;
	s2 += " for a day";
	cout << "s2 += \"for a day\" yields s2 = " << s2 << endl;

	return 0;
}
```

### 4.3.3 string类的其他操作
```C++
// 4.9 strtype3.cpp -- more string class features
#include <iostream>
#include <string>  // make string class available
#include <cstring>  // C-style string library

int main()
{
	using namespace std;
	char charr1[20];
	char charr2[20] = "jaguar";
	string str1;
	string str2 = "panther";

	// assignment for string objects and character arrays
	str1 = str2;  // copy str2 to str1;
	strcpy_s(charr1, charr2);  // copy charr2 to charr1

	// appending for string objrcts and character arrays
	str1 += " paste";  // add paste to end of str1
	strcat_s(charr1, " juice");  // add juice to end of charr1

	// finding the length of a string object and a C-style string
	int len1 = str1.size();  // obtain length of str1
	int len2 = strlen(charr1);  // obtain length of charr1

	cout << "The string " << str1 << " contains "
		<< len1 << " characters.\n";
	cout << "The string " << charr1 << " contains "
		<< len2 << " characters.\n";

	return 0;
}
```
- 对于字符数组，copy时，可能超出所给的内存范围。但是string类会自动调整大小，不用担心超出范围

```C++
//  确定字符数的两种方法
int len1 = str1.size();  // 表明str1是一个对象，而size是一个类方法。方法是一个函数，只能通过其所属类的对象进行调用。str1是一个string对象，而size()是string类的一个方法。
int len2 = strlen(charrr1);
```

### 4.3.4 string类I/O
```C++
// 4.10 strtype4.cpp
#include <iostream>
#include <string>
#include <cstring>

int main()
{
	using namespace std;
	char charr[20];
	string str;

	cout << "Length of string in charr before input: "
		<< strlen(charr) << endl;
	cout << "length of string in str before input: "
		<< str.size() << endl;
	cout << "Enter a line of text:\n";
	cin.getline(charr, 20);  // indicate maximum length
	cout << "You entered: " << charr << endl;
	
	cout << "Enter another line of text:\n";
	getline(cin, str);  // cin now an argument:no length specifier
	cout << "You entered: " << str << endl;
	
	cout << "Length of string in charr after input: "
		<< strlen(charr) << endl;
	cout << "Length of string in str after input: "
		<< str.size() << endl;
	return 0;
}
```
- 对于为被初始化的字符数组，第一个空字符的出现位置是随机的；
- 用户输入之前，str的字符串长度为0。这是因为string类对未被初始化的对象设置的
- ```C++ getline(cin, str); ```; 这个getline不是类方法，她没用句点表示法。它将cin作为参数，指出到哪里去查找输入，string对象根据字符串长度自动调整自己的大小。具体看第11章

### 4.3.5 其他形式的字符串字面值
- 对于wchar_t、char16_t、char32_t这三种类型，创建时分别使用不同的前缀
```C++
wchar_t title[] = L"Chief Astrogator";  // w_char string
char16_t name[] = u"Felonia Ripova";  // char_16 string
char32_t car[] = U"Humber Super Snipe";  // char_32 string
```
- C++ 11新增原始(raw)字符串。\n不表示换行符。而是两个常规字符——斜杠和n。用"(和)"做界定符并用前缀R来标识
- 输入原始字符串时，按回车不会移到下一行，还将在原始字符串中添加回车符
```C++
cout << R"(Jim "king" Tutt uses "\n" instead of endl.)" << '\n';
// print: Jim "king" Tutt uses "\n" instead of endl.
```
- 如果要输出"(和)"在"(之间添加其他字符,)"之间也是同样的。
- 在自定义定界符时，在默认定界符之间添加任意数量的基本字符，但空格、左括号、有括号、斜杠和控制字符（如制表符和换行符）除外
```C++
cout << R"+*("(Who wouldn't?)", she whispered.)+*" << endl;
// print: "(Who wouldn't?)", she whispered.
```

## 4.4 结构简介
- 结构也是C++OOP的基石，学习有关结构的知识将使我们离C++的核心OOP更近
- 关键字struct表明，这些代码定义的是一个结构布局。
- 标识符inflatable是这种数据格式的名称（新的类型名）
- 访问类成员函数（如cin.getline()）的方式是从访问结构成员变量（如hat.price）的方式衍生而来的

### 4.4.1 在程序中使用结构
- 两种方式的声明：
	- 内部声明：声明在函数中，只能被该函数使用
	- 外部声明：声明在函数前，所有的函数都可以使用（更常用）

- 变量和常量的声明：
	- 变量提倡定义在内部（第9章详细介绍）
	- 常量提倡定义在外部（外部意味着共享）

```C++
// 4.11 structur.cpp -- a simple structure
#include <iostream>
struct inflatable  // structure declaration
{
	char name[20];
	float volume;
	double price;
};

int main()
{
	using namespace std;
	inflatable guest =
	{
		"Glorious Gloria",  // name value
		1.88,  // volume value
		29.99
	};  // 第1种初始化方式

	inflatable pal = { "Audacious Arthur", 3.12, 32.99 };  // 第2种初始化方式

	cout << "Expand your guest list with " << guest.name;
	cout << " and " << pal.name << "!\n";
	cout << "You can have both for $";
	cout << guest.price + pal.price << "!\n";
	return 0;
}
```
### 4.4.2 C++11 结构初始化
- 1. C++11支持列表初始化用于结构，且等号（=）是可选的
- 2. 若大括号内为包含任何东西，各个成员都将设置为零
- 3. 不允许缩窄转换（大类型转小类型）
```C++
inflatable duck {"Daphne", 0.12, 9.98};   // can omit = in C++ 11
inflatable mayor {};  // initialize every element to zero
```
### 4.4.3 结构可以将string类作为成员吗
- 答案是肯定对。但是一定要让结构定义能够访问名称空间std。
	- 可以将编译指令using移到结构定义之前
	- 也可以想下面那样，将name的类型声明为std::string

```C++
#include <iostream>
struct inflable
{
	std::string name;
	float volume;
	double price;
};
```
### 4.4.4 其他结构属性
- 可以将结构作为参数传递，也可以让函数返回一个结构
- 可以用赋值运算符(=)将结构赋给另一个同类型的结构。每个成员都被设置为另一个成员的值，包括数组。这被称为**成员赋值**(memberwise assignment)（有道翻译：成员逐一赋值）

```C++
// 4.12 assgn_st.cpp -- assing structures
#include <iostream>
struct inflatable
{
	char name[20];
	float volum;
	double price;
};

int main()
{
	using namespace std;
	inflatable bouquet =
	{
		"sunflowers",
		0.20,
		12.49
	};
	inflatable choice;
	cout << "bouquet: " << bouquet.name << " for $";
	cout << bouquet.price << endl;

	choice = bouquet;  // assign one structure to another
	cout << "choice: " << choice.name << " for $";
	cout << choice.price << endl;
	return 0;
}
```
- 可以同时完成定义结构和创建变量的工作
```C++
struct perks
{
	int key_number;
	char car[12];
} mr_smith, ms_jones;  // two perks variables
```
- 甚至可以初始化以这种方式创建的变量。但是，将定义和变量声明分开可以使程序更易于阅读和理解。

```C++
struct perks
{
	int key_numbers;
	char car[12];
} mr_glitz = 
{
	7,
	"packard"
};
- ↓ 创建了一个名为position的结构变量，可以访问成员，但后续无法创建这种类型的变量
```C++
struct  // no tag
{
	int x;  // 2 members
	int y;
} position;  // a structure variable
```
- 与C不同的是C++结构除了成员变量之外，还是以有成员函数。这被应用于类，第10章讨论

### 4.4.5 结构数组
```C++
// 4.13 arrstruc.cpp
#include <iostream>
struct inflatable
{
	char name[20];
	float volume;
	double price;
};
int main()
{
	using namespace std;
	inflatable guests[2] =   // initializing an array of structs
	{
		{"Bambi", 0.5, 21.99},  // first structure in array
		{"Godzailla", 2000, 565.99}  // next structure in array
	};

	cout << "The guests " << guests[0].name << " and " << guests[1].name
		<< "\nhave a combined volume of "
		<< guests[0].volume + guests[1].volume << " cubic feet.\n";
	return 0;
}
```
### 4.4.6 结构中的位字段
- 与C一样，C++也可以指定占用特定位数的结构成员，这使得创建与某个硬件设备上的寄存器对应的数据结构非常方便。
- 字段的类型应为整型和枚举，接下来是冒号，冒号后是数字，它指定了使用的位数
- 可以使用没有名称的字段来提供间距。每个成员都被称为位字段(bit field)
```C++
struct torgle_register
{
	unsigned int SN : 4;  // 4 bits for SN value
	unsigned int : 4; // 4 bits unused
	bool goodIn : 1;  // valid input (1 bit)
	bool goodTorgle : 1;  // successful torgling
};
```
- 可以像通常那样初始化，还可以像通常那样访问
```C++
torgle_register tr = { 14, true, false };
...
if (tr.goodIn)  // if statement covered in Chapter 6
...
```
## 4.5 共同体
- 共同体(union)是一种数据格式，能存储不同的数据类型，但只能存储其中一种数据类型。句法与结构相似，但含义不同
- 每次只能存储一个值，所以共同体的长度为最大成员长度
- 共同体的用途之一是，当数据项使用两种或更多种格式(但不会同时使用)时,可节省空间。例如：假设管理一个小商品目录，其中有一些商品的ID为整数，而另一些的ID为字符串。这种情况下，可以这么做
```C++
struct widget
{
	char brand[20];
	int type;
	union id  // format depends on widget type
	{
		long id_num;  // type 1 widgets
		char id_char[20];  // other widgets
	} id_val;
};
...
if (prize.type == 1)
	cin >> prize.id_val.id_num;
else
	cin >> prize.id_val.id_char;
```
- 匿名共同体(anonymous union)没有名称，其成员将位于相同地址处的变量。显然，每次只有一个成员是当前成员：
```C++
struct widget
{
	char brand[20];
	int type;
	union  // anonymous union
	{
		long id_num;  // type 1 widgets
		char id_char[20];  // other widgets
	};
};
...
widget prize;
...
if (prize.type == 1)
	cin >> prize.id_num;
else
	cin >> prize.id_char;
```
- 由于共同体是匿名的，因此id_num 和 id_char 被视为prize的两个成员，它们的地址相同，所以U币需要中间标识符id_val.**程序员**负责确定当前那个成员是活动的。
- 共同体常用于（但并非只能用于）节省内存。对嵌入式系统来说，内存是很宝贵的，就很有用。另外，共同体常用于操作数据结构或硬件数据结构


## 4.6 枚举(enumeration)
- C++的enum工具提供另一种创建符号常量的方式，这种方式可代替const。
- 它还允许定义新类型，但必须按照严格的限制进行。
```C++
enum spectrum {red, orange, yellow, green, blue, voilet, indigo, ultraviolet};
```
- 这条语句完成两项工作:
	- 让spectrum称为类型名（新），spectrum被称为枚举，就像struct变量被称为结构一样；
	- 将red、orange、yellow等作为符号常量，他们对应整数值0~7。这些常量叫做枚举量
	
- 默认情况下、将整数值赋给枚举量、第一个0，第二个1，也可以覆盖，后面讲。
```C++
spectrum band;   // band是一个spectrum类型的变量

// 在不进行强制转换的情况下，只能将定义枚举时使用的枚举量赋给这种枚举的变量
band = blue;  // 可以，blue是个枚举量
band = 2000;  // 不行，2000不是；spectrum受到限制，只有8个可能的值，否则编译报错
```
- 枚举只定义了赋值运算符，没有算数运算符
```C++
band = orange;  // valid
++band;  // not valid
band = orange + red;  // not valid
```

- 枚举量是整型，可被提升为int类型，但int类型不能自动转换为枚举类型
- 算术表达式中同时使用枚举和常规整数，尽管没有为枚举本身定义算术运算
```C++
int color = blue;  // valid, spectrum type promoted to int
band = 3;  // invaild, int not converted to spectrum
color = 3 + red;  // valid, red converted to int
```
- 如果int值是有效的，可以通过强制类型转换，把它赋给枚举
	- ```C++ band = spectrum(3);``` 
- 如果对一个不适当的值进行强制类型转换，结果是不确定的。参阅后面“枚举的取值范围”一节
	- ``` band = spec(40009); ``` 
- 枚举的规则相当严格。实际上，枚举更常被用来定义相关的符号常量，而不是新类型。可以定义switch语句中的符号常量。
- 如果打算只使用常量，而不创建枚举类型的变量，则可以省略枚举类型的名称
	-```C++ enum{red, orange, yellow, green, blue, voilet, indigo, ultraviolet};

### 4.6.1 设置枚举的值
```C++
enum bits{one = 1, two = 2, four = 4, eight = 8};  // 显式赋值
enum bigstep{first, second = 100, third};  // 部分赋值；first为0，third为101
enum {zero, null = 0, one, numero_uno = 1};   // 可以创建多个值相同的枚举量;zero和null为0,其余为1
```
### 4.6.2 枚举的取值范围
- 早期版本，只能将int值（或提升为int的值）赋给枚举量，后来这种限制取消了，因此可以使用long甚至long long
- 每个枚举都有取值范围，通过强制类型转换可以将取值范围中的任何整数值赋给枚举变量。

```C++
enum bits{one = 1, two = 2, four = 4, eight = 8};
bits myflag;
myflag = bits(6);  // valid, because 6 is in bits range
```
- 取值范围的定义如下：
	- 首先，找上限。大于max(枚举量)的最小的2的幂-1。如max=101,则为128-1=127
	- 其次，找下限。若不小于0，则取0.否则，和上限一样，但加个符号。如min= -6，-(8-1) = -7
- C++11扩展了枚举。增加了作用域内枚举(scoped enumeration),第10章的“类作用域”一节将简要介绍

## 4.7 指针和自由存储空间
- 第3章开头提到：追踪计算机程序在存储数据时必须追踪的3种基本属性。
```C++
// address.cpp - using the & operator to find addresses
#include <iostream>
int main()
{
	using namespace std;
	unsigned int donuts = 6;
	double cups = 4.5;

	cout << "donuts value = " << donuts;
	cout << " and donuts address = " << &donuts << endl;
	cout << "cups value = " << cups;
	cout << " and cups address = " << &cups << endl;
	return 0;
}
```
- 旁注：面向对象编程与传统的面向过程编程的区别在于，OOP强调在运行阶段进行决策，而不是编译阶段。C++采用的方式是，使用关键字new请求正确数量的内存以及使用指针来跟踪新分配的内存的位置
- 使用常规变量时，值是指定的量，而地址是派生量。指针则是将地址视为指定的量，将值视为派生量。

- * 运算符被称为间接值(indirect value)或解除引用(dereferencing)运算符，应用于指针，可以得到该地址处存储的值。

```C++
// 4.15 pointer.cpp
#include <iostream>
int main()
{
	using namespace std;
	int updates = 6;
	int* p_updates;
	p_updates = &updates;

	// express values two ways
	cout << "Values: updates = " << updates;
	cout << ", *p_updates = " << *p_updates << endl;

	// express address two ways
	cout << "Addresses: &updates = " << &updates;
	cout << ", p_updates = " << p_updates << endl;

	// using pointer to chage value
	*p_updates = *p_updates + 1;
	cout << "Now updates = " << updates << endl;
	return 0;
}
```
- * 与 & 是两个相反方向的操作

### 4.7.1 声明和初始化指针
- 指针声明必须指定指针指向的数据的类型。便于指针去操作
- \* 运算符两边的空格是可选的。
	- C程序员使用这种格式：``` int *ptr;``` 这强调* ptr是一个int类型指针 
	- C++程序员用这种格式：``` int* ptr;``` 这强调int* 是一种类型——指向int的指针
	- 这样写也没啥事``` int*ptr ```
	- 每个指针变量前都要有个* ``` int* p1, p2; ``` 一个指针(p1)，一个int变量(p2)
- 注意：在C++中，int* 是一种复合类型，是指向int的指针 
- 指向不同类型的指针占用的内存是相同的，毕竟地址的长度都是一样的，2 or 4 bytes。
```C++
// 4.16 init_ptr.cpp -- initialize a pointer a pointer
#include <iostream>
int main()
{
	using namespace std;
	int higgens = 5;
	int* pt = &higgens;

	cout << "Value of higgens = " << higgens
		<< "; Address of higgens = " << &higgens << endl;
	cout << "Value of *pt = " << *pt
		<< "; Value of pt = " << pt
		<< " Address of &pt = " << &pt << endl;
	return 0;
}
```

### 4.7.2 指针的危险
- 一定要在对指针应用接触引用运算符(* )之前，将指针初始化为一个确定、适当的地址。这是关于使用指针的金科玉律。
```C++
long* fellow;  // create a pointer-to-long
*fellow = 2333333;  // place a value in never-never land
```
- fellow指向的地方很可能并不是要存储233333的地方，这种错误可能会导致一些最隐秘、最难以追踪的bug。

### 4.7.3 指针和数字
- 虽然计算机通常把地址当做整数来处理，但指针不是整型，将两个地址做运算是没有意义的
```C++
int * pt;
pt = (int *) 0xB8000000;  // 这样才能赋值。两边都是整数。pt是int型的地址，并不意味着pt本身的类型是int
```
### 4.7.4 使用new来分配内存
- 变量是在编译时分配的有名称的内存，而指针只是为可以通过名称直接访问的内存提供了一个别名。
- 指针的真正用武之地在于：**在运行阶段分配未命名的内存以存储值**（就是C中的malloc()）

- new:程序员告诉new，需要为那种数据类型分配内存；new将找到一个长度正确的内存块，并返回该内存块的地址。 程序员的责任是将该地址赋给指针。
```C++
int* pn = new int;   // 处理数据对象的指针

int higgens;  // 变量的地址赋给指针
int* pt = &higgens;
``` 
- 第二种情况可以通过名词higgens来访问int；第一章只能通过指针访问
- pn指向的内存没有名称，如何称呼它？我们说pn指向一个**数据对象**。术语“数据对象”比“变量”更通用，它指的是为数据项分配的内存块。因此变量也是数据对象。
- 乍一看，处理数据对象的指针方法可能不太好用，但它使程序在管理内存方面有更大的控制权

```C++
// 4.17 use_new.cpp -- using the new opearator
#include <iostream>
int main()
{
	using namespace std;
	int nights = 1001;
	int* pt = new int;  // allocate space for an int
	*pt = 1001;  // store a value there

	cout << "nights value = ";
	cout << nights << ": location " << &nights << endl;
	cout << "int value = ";
	cout << *pt << ": location = " << pt << endl;
	
	double* pd = new double;
	*pd = 10000001.0;

	cout << "double value = ";
	cout << *pd << ": location = " << pd << endl;
	cout << "location of pointer pd: " << &pd << endl;
	cout << "size of pt = " << sizeof(pt);
	cout << ": sizeof *pt = " << sizeof(*pt) << endl;
	cout << "size of pd = " << sizeof pd;
	cout << ":size of *pd = " << sizeof * pd << endl;
	
	delete pt;
	delete pd;
	return 0;
}
```
- 变量通常放在栈中；new的从堆或自由存储区(free store)分配内存。第9章细讲。

### 4.7.5 使用delete释放内存
- delete释放指针指向的内存，而不会删除指针本身（想C中的free()函数）
- 一定要配对地使用new和delete；否则将发生内存泄露(memory leak)，即被分配的内存再也无法使用了。如果内存泄露严重，程序会因为不断寻找新内存而终止。
- 不要尝试释放已经释放的内存块，C++标准指出，这样做的结果是不确定的，意味着什么情况都可能发生。不能用delete 来释放已经声明变量所获得的内存。
```C++
int * ps = new int;  // ok
delete ps;  // ok
delete ps;  // not ok now
int jugs = // ok
int *pi = &jugs;  // ok
delete pi  // not allowed, memory not allocated by new
```
- **警告：** 只能用delete来释放使用new分配的内存。栈里边的不用主动释放，程序运行完自动释放。对空指针用delete是安全的，无所谓直接摆烂。

- 使用delete到new分配的内存上。这不意味着，要使用用于new的指针，而是用于new的地址。
```C++
int *ps = new int;  // allocate memory
int * pq = ps;  // set second pointer to same block
delete pq;  // delete with second pointer（用第二个指针删除new分配的内存）
```
- 一般不要创两个指向同一内存块的地址，会增加错误删除同一个内存块两次的可能性。但对于返回指针的函数，使用另一个指针确实有点道理。

### 4.7.6 使用new来创建动态数组
- 在编译时给数组分配内存被称为**静态联编**(static bindling)，意味着数组是编译时加入到程序中的。
- 用new则在运行需要时才创建。还可以在程序运行时选择数组的长度。这被称为**动态联编**(dynamic binding)。数组也称动态数组(dynamic array)
- 关于动态数组的两个基本问题：
	- 1.如何用C++的new运算符创建数组
	- 2.如何使用指针访问数组
#### 1.使用new创建动态数组
```C++
int *psome = new int [10];  // new返回第一个元素的地址

delete [] psome;  // 方括号告诉程序，应释放整个数组，而不仅仅是指针指向的元素。
```
- 如果使用new不带方括号，使用delete时也不带。如果使用new时带方括号，则使用delete时也带。new与delete格式不匹配的后果是不确定的
```C++
int * pt = new int;
short * ps = new short [500];
delete [] pt; // effect is undefined, don't do it
delete ps;  // effect is undefined, don't do it
```
- 总之，使用new和delete是，遵循如下规则：
	- 不要用delete来释放不是new分配的内存
	- 不要使用delete释放同一个内存块两次
	- 如果使用new[] 为数组分配内存，则用delete[] 来释放
	- 如果使用new[] 为一个实体分配内存，则使用delete（没有方括号）来释放
	- 空指针用delete是安全的。无所谓摆烂。 
- 不能使用sizeof来确定动态分配的数组包含的字节数

#### 2.使用动态数组  
- 指针可以直接当数组名用，因为C和C++内部都使用指针处理数组
- 数组和指针基本等价是C和C++的优点之一（这在有时候也是个问题，但这是另一码事）
- **指针和真正的数组名之间的根本差别**:数组名是不能修改，但是指针名是变量可以修改。

```C++
// 4.18 arraynew.cpp -- using the new operator for arrays
#include <iostream>
int main()
{
	using namespace std;
	double* p3 = new double[3];

	p3[0] = 0.2, p3[1] = 0.5, p3[2] = 0.8;
	cout << "p3[1] is " << p3[1] << ".\n";
	p3 = p3 + 1;  // increment the pointer
	cout << "Now p3[0] is " << p3[0] << " and ";
	cout << "p3[1] is " << p3[1] << ".\n:";
	p3 = p3 - 1;  // point back to beginning
	delete[] p3;  // free the memory
	return 0;
}
```

## 4.8 指针、数组和指针算术
- 指针和数组基本等价的原因在于：指针算术(pointer arithmetic) 和 C++内部处理数组的方式。
- C++将数组名解释为地址

```C++
// 4.19 addpntrs.cpp
#include <iostream>
int main()
{
	using namespace std;
	double wages[3] = { 10000.0, 20000.0, 30000.0 };
	short stacks[3] = { 3, 2, 1 };

	// Here are two ways to get the address of an array
	double* pw = wages;  // name of an array = address
	short* ps = &stacks[0];  // or use address operator
	
	// with array element
	cout << "pw = " << pw << ", *pw = " << *pw << endl;
	pw = pw + 1;
	cout << "add 1 to the pw pointer:\n";
	cout << "pw = " << pw << ", *pw = " << *pw << "\n\n";
	cout << "ps = " << ps << ", *ps = " << *ps << endl;
	ps = ps + 1;
	cout << "add 1 to the ps pointer:\n";
	cout << "ps = " << ps << ", *ps = " << *ps << "\n\n";

	cout << "access two elements with array notation\n";
	cout << "stacks[0] = " << stacks[0]
		<< ", stacks[1] = " << stacks[1] << endl;
	cout << "access two elements with pointer notation\n";
	cout << "*stacks = " << *stacks
		<< ", *(stacks + 1) = " << *(stacks + 1) << endl;

	cout << sizeof(wages) << " = size of wages array\n";
	cout << sizeof(pw) << " = size of pw pointer\n";
	return 0;
}
```
- * (stacks + 1) 和 stacks[1]是等价的。通常C++内部都会把后者变为前者
- 对数组应用sizeof得到的是数组的长度，而对指针应用sizeof得到的是指针的长度。**在这种情况下，C++不会将数组名解释为地址**
- [ ] p109数组的地址这有点懵，之前那本C上应该有，再复习下
```C++
short (*pas) [20] = &tell;   // 包含20个元素的short数组
short* pas [20] = &tell;  // 指针数组，包含20个元素
```

### 4.8.3 指针和字符串

















































