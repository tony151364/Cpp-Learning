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
