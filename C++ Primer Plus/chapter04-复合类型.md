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
 
 ### 4.2 字符串
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
#### 4.2.1 拼接字符串常量
```C++
// 下面所有输出语句都是等效的
cout << "I like you," " you like me.\n";
cout << "I like you, you like me.\n";
cout << "I like you, you"
" like me.\n";
// 第一个字符串中的\0字符将被第二个字符串的第一个字符取代
```
#### 4.2.2 在数组中使用字符串
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

#### 4.2.4 每次读入一行字符串输入
- getline()和get() 这两个函数都读取一行输入，直到达到换行符。
- 然而，随后getline()将丢弃换行符，而get()将换行符保留在输入序列中。

##### 1.面向行的输入：getline()
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

##### 3.空行和其他问题
- [ ] 没太看懂

#### 4.2.5 混合输入字符串和数字
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

### 4.3 string类简介
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









