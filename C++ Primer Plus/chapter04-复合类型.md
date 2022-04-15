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
 double earnings[4] {1.2e4, 1.6e4, 1.1e4, 1.7e4};  // 首先；数组初始化可以省略等号
 unsigned int counts[10] = {};  // 其次可不在大括号内包含任何东西，这把所有元素都设置为零。
 ```






















