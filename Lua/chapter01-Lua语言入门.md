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
- Lua标识符：由任意字母、数字和下划线组成的字符串（注意不能以数字开头）

- 作者通常将“下划线+小写字母”用作哑变量(Dummy variable)
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
- nil是一种类型，该类型也只有一个值nil
- nil的主要作用就是与其他值进行区分
- Lua用你nil来表示无效值

### 1.4.2 Boolean
- 在Lua语言中，Boolean值并非是用于条件测试的唯一方式，任何值都可以表示条件。
- Lua将false和nil以外的值视为真，0和空字符也为真。
- and：如果第一个操作数为false，则返回第一个操作数，否则返回第二个操作数。
- or：第一个操作数不为false，则返回第一个操作数，否则返回第二个操作数
- 因为and的优先级大于or的优先级，所以((a and b) or c) 与(a and b or c)等价。它们还与C语言的三目运算符a?b:c等价
- x = x or v 等价于 if not x then x = v end
- not永远返回Boolean类型的值

## 1.5 独立解释器
- lua \[options] \[script\[args]]
- e 参数允许我们在命令行输入代码：如``` %lua -e "print(math.sin(12))" ```
- -l用于加载库
- -i用于在运行完其他命令行参数后进入交互模式。
```lua
-- 下面的命令行会加载lib库，然后执行x = 10的赋值语句，并最终进入交互模式
% lua -i -llib -e "x = 10"
-- 如果不想输出结果，可以在末尾加一个分号
> io.flush()
```

- LUA_INIT使得我们可以灵活地配置独立解释器。例如，我们可以预先加载程序包(Package)、修改路径、定义自定义函数、对函数进行重命名或删除函数，等等；

## 1.6 练习
```lua
-- 1.1 如果输入负数会发生栈溢出(stack overflow)，处理方法为：输入负数返回负数本身
function fact (n)
	if n < 0 then
		return n
	elseif n == 0 then
		return 1
	else
		return n * fact(n - 1)
	end
end

print("enter a number:")
a = io.read("*n")  -- 读取一个数字
print(fact(a))

-- 1.2
-- lua.exe -i D:\learning\Lua\hello.lua
-- dofile方式有点麻烦

-- 1.3 AppleScript

-- 1.4 只要不是Lua保留字，且不以数字开头，都是有效的标识符
until?  -- invaild

end?  -- invaild
-- 1.5 因为type(nil)的返回值类型为string

--1.6
local function isBool(value)
    return value == true or value == false
end

print(isBool(false))
print(isBool(true))
print(isBool(1))
print(isBool(nil))
print(isBool("true"))
print(isBool("false"))

-- 1.7
--不是必需的。 推荐还是用括号，增强可读性
local x,y,z = true,true,false
if x and y and (not z) or (( not y) and x) then
    print("success1")
end
if x and y and not z or not y and x then
    print("success2")
end

x,y,z = true,false,false
if x and y and (not z) or (( not y) and x) then
    print("success3")
end
if x and y and not z or  not y and x then
    print("success4")
end


--1.8
if arg and arg[0] then
    print(arg[0])
end
```
