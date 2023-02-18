
## 0 lua_slate简介
- lua_State 可以理解为Lua的运行环境，也可以理解为Lua的虚拟机
```C++
extern "C"的主要作用就是为了能够正确实现C++代码调用其他C语言代码。
    加上extern "C"后，会指示编译器这部分代码按C语言的进行编译，而不是C++的。由于C++支持函数重载，
    因此编译器编译函数的过程中会将函数的参数类型也加到编译后的代码中，而不仅仅是函数名；
    而C语言并不支持函数重载，因此编译C语言代码的函数时不会带上函数的参数类型，一般只包括函数名。
```

```C++
#include <cstdio>
extern "C" {
#include "lua.h"
#include "lauxlib.h"
#include "lualib.h"
    }

int main()
{
    lua_State* state = luaL_newstate();
    luaL_openlibs(state);
    {
        // some code
    }
    lua_close(state);
    return 0;
}
```

## 01 加载一个简单脚本并初始化
- 遇到不会的函数，阅读官方文档往往更快
```C++
#include <cstdio>
#include <cstring>

extern "C" {
#include "lua.h"
#include "lauxlib.h"
#include "lualib.h"
}

char const* script = R"(
function helloworld()
    print("hello,world!")
end
helloworld()
)";

int main()
{
    lua_State* state = luaL_newstate();

    luaL_openlibs(state);
    {
        auto rst = luaL_loadbuffer(state, script, strlen(script), "hellowrold");

        if (rst != 0)
        {
            if (lua_isstring(state, -1))
            {
                auto msg = lua_tostring(state, -1);
                printf("load script failed: %s\n", msg);
                lua_pop(state, 1);
            }

            return -1;
        }

        if (lua_pcall(state, 0, 0, 0))
        {
            if (lua_isstring(state, -1))
            {
                auto msg = lua_tostring(state, -1);
                printf("load script failed: %s\n", msg);
                lua_pop(state, 1);
            }
        }
    }

    lua_close(state);
    return 0;
}
```

## 02 函数调用与返回值
- 学习目标：调用一个已加载的脚本函数

```C++
#include <cstdio>
#include <cstring>

extern "C" {
#include "lua.h"
#include "lauxlib.h"
#include "lualib.h"
}

char const* script = R"(
function helloworld()
    print("hello,world!")
end

function my_pow(x, y)
    return x^y
end
)";

int main()
{
    lua_State* state = luaL_newstate();

    luaL_openlibs(state);

    {
        auto rst = luaL_loadbuffer(state, script, strlen(script), "hellowrold");

        if (rst != 0)
        {
            if (lua_isstring(state, -1))
            {
                auto msg = lua_tostring(state, -1);
                printf("load script failed: %s\n", msg);
                lua_pop(state, 1);
            }

            return -1;
        }

        // lua_call, lua_pcall always removes the function and its arguments from the stack.
        if (lua_pcall(state, 0, 0, 0))
        {
            if (lua_isstring(state, -1))
            {
                auto msg = lua_tostring(state, -1);
                printf("load script failed: %s\n", msg);
                lua_pop(state, 1);
            }
        }

        // call my_pow
        lua_getglobal(state, "my_pow");

        if (!lua_isfunction(state, -1))
        {
            printf("function named 'my_pow' not found!\n");
            return -1;
        }

        lua_pushnumber(state, 2);
        lua_pushnumber(state, 8);

        rst = lua_pcall(state, 2, 1, 0);

        if (rst != 0)
        {
            if (lua_isstring(state, -1))
            {
                auto msg = lua_tostring(state, -1);
                printf("call function chunck failed: %s\n", msg);
                lua_pop(state, 1);
            }
        }

        if (lua_isnumber(state, -1))
        {
            lua_Number val = lua_tonumber(state, -1);
            printf("my_pow ret: %.0lf", val);
        }
    }

    lua_close(state);
    return 0;
}
```
## 03 加载脚本到包
- up_value是一个变量也可以认为是一个环境。给函数传递up_value，函数可以从up_value去取值，使用了元表的特性
```C++

```
