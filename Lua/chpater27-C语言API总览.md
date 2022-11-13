- Lua语言的两种定位*嵌入式语言*和*可扩展语言*分别对应C语言和Lua语言之间的两种交互形式。
    - 第一种：C语言拥有控制权，而Lua语言被用作库，这种交互形式中的C代码被称为*应用代码（application code）*
    - 第二种：Lua语言拥有控制权，而C被用作库，此时的C代码被称为*库代码（library code）*
    - 应用代码和库代码都使用相同的API与Lua语言通信，这些API被称为CAPI

```C
#include <stdio.h>
#include <string.h>
#include "lua.h"
#include "lauxlib.h"
#include "lualib.h"

int main(void)
{
	char buff[256];
	int error;
	lua_State* L = luaL_newstate();  // 打开Lua
	luaL_openlibs(L);  // 打开标准库

	while (fgets(buff, sizeof(buff), stdin) != NULL)
	{
		error = luaL_loadstring(L, buff) || lua_pcall(L, 0, 0, 0);
		if (error)
		{
			fprintf(stderr, "%s\n", lua_tostring(L, -1));
			lua_pop(L, 1);  // 从栈中弹出错误信息
		}
	}

	lua_close(L);
	return 0;
}
```
- 头文件lua.h：包含Lua提供的基础函数，包括创建新Lua环境函数、调用Lua函数的函数、读写环境中的全局变量的函数...;
 - 所有的内容都有一个前缀lua_（例如：lua_pcall)
- 头文件lauxlib.h：声明了*辅助库（auxiliary library, auxlib）*所提供的函数
 - 前缀LuaL_
 - 辅助库使用lua.h提供的基础API来提供更高级的抽象，特别是对标准库用到的相关机制进行抽象。
 - 基础API追求经济性和正交性（orthogonality），而辅助库则追求对常见任务的实用性
 - 请记住，辅助库不能访问Lua的内部元素，而只能通过lua.h中盛名的官方基础API完成所有工作。
 - 辅助库能实现什么，你的程序就能实现什么
 
- Lua标准库没有定义任何C全局变量，它将其所有的状态都保存在动态的结构体lua_State中，Lua中的所有函数都接受一个指向该结构的指针作为参数。这种设计使得Lua是可重入的，并且可以直接用于编写多线程代码

- 函数luaL_newstate用于创建一个新的Lua状态。当它创建一个新状态时，新环境没有包含预定义的函数，甚至连print也没有。为了保持Lua语言的精炼，所有的标准都被组成不同的包，这样我们在不需要使用某些包时可以忽略它们。
 - 头文件lualib.h中声明了用于打开这些库的函数。函数luaL_openlibs用于打开所有的标准库

- 创建一个好的状态 + 加载其中的标准库 -> 就可以处理用户输入。程序会首先调用函数luaL_loadstring来编译用户输入的每一行内容。
- 如果将Lua作为C代码编译出来后又要在C++中使用，那么可以引入lua.cpp来替代lua.h
```C++
extern "C" {
#include "lua.hpp"
}
```
## 27.2 栈
- Lua与C所有的操作和数据交换都是通过*虚拟栈（stack）*来完成
- 当我们想在Lua和C之间交换数据时，会面对两个问题：
 - 1.动态类型和静态类型体系之间不匹配
 - 2.自动内存管理和手动内存管理之间不匹配
 
- 栈是Lua状态的一部分，因此垃圾收集器知道C语言正在使用那些值。
- 几乎C API中所有的函数都会用到栈
- 正如第一个示例
 - 函数luaL_loadstring将其结果**留在栈中（入栈）**（不管是编译好的代码段还是一条错误消息）；
 - 函数lua_pcall从**栈中取出（出栈）**要调用的函数，并且也会将错误消息留在栈中
 
- Lua严格按照LIFO的规则操纵栈，只有栈顶部的部分会发生变化；
- C语言可以检视栈中的任何一个元素，甚至可以在栈的任意位置插入和删除元素。

### 27.2.1 压入元素
- 针对每一种能用C语言直接表示的Lua数据类型，C API中都有一个对应的压栈函数
- Lua语言中的字符串不是以\0结尾的，它们可以包含任意的二进制数据。
- 各种类型提供了各种函数

### 27.2.2 查询元素
- C API使用*索引（index）*来引用栈中的元素。第一个被压入栈的元素索引为1，第二个为2……。
- 也可以以栈顶为参照，使用负数来索引栈中的元素。-1表示栈顶元素。调用lua_tostring(L, 1)会将栈顶的值作为字符串返回。
- 查各种类型提供了各种函数。如：lua_isnil、lua_isnumber、lua_isstring和lua_istable等
- lua_isnumber不会检查某个值是否为特定类型，而是检查该值是否能被转换为此特定类型。函数lua_isstring与之类似，特别之处在于，它接受数字。
