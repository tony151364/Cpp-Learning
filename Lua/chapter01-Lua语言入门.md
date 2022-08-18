# 1 Lua语言入门

```lua
print("Hello World")
```

```lua
-- 定义一个计算阶乘的函数
function fact (n)
    if n==0 then
        return 1
    else
        return n * fact(n - 1)
    end
end

print("enter a number:")
a = io.read("*n")  -- 读取一个数字
print(fact(a))
```

## 1.1 程序段
- 事实上，由于Lua语言也可以被用作数据定义语言，所以几MB的程序段也很常见。
- Lua语言的解释器可以支持非常大的程序段

```lua
lua -i prog
-- 上述的命令行会在执行完文件prog中的呈现出断后进入交互模式，这对调试和手工测试都很有用。
```

```lua
-- 另一种运行函数的方式：调用函数dofile()
function norm (x, y)
    return math.sqrt(x^2 + y^2)
end

function twice(x)
    return 2.0 * x
end

-- 在交互模式下
> dofile("D:\\learning\\Lua\\hello.lua")  -- 加载文件
> n = norm(3.4, 1.0)
> twice(n)
-- 开两个窗口时很方便
```

## 1.2 一些语法规范
- 哑变量(Dummy variable)

```lua
--[[
print(10)
--]]

---[[
print(10)
--]]
```
- 分隔符不是必须的，一般一行有多条语句是采用
```lua
-- 下述语句等价
a = 1 b = 2
a = 1; b = 2
```

## 1.3 全局变量
 - Lua不区分未初始化的变量和nil变量

## 1.4 类型和值
- Lua是动态类型语言(Dynamically-typed language),在这种语言中没有类型定义(type definition)
- type()可以获取一个值对应的类型，返回值类型为string
- userdata类型允许把任意的C语言数据保存在Lua变量中。在Lua语言中，用户数据类型除了赋值和相等性测试外， 没有其他预定义的操作。
- 用户数据常被用来表示由应用或者C语言编写的库所创建的类型。

### 1.4.1 nil
