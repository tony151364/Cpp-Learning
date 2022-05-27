- Lua语言是一门十分简洁的脚本语言，C语言20行的程序可以实现的功能，Lua语言可能就1行，所以说Lua语言十分简洁。
- Lua由C语言构造并开源得来，所以Lua还被称为“胶水语言”，他可以在C语言与C++之间粘合使用。
- Lua的体积非常小，就算很长的代码也仅仅只有100多kb，这在程序当中是不可想象的。
- Lua语言应用场景：游戏开发、脚本开发、Web脚本、应用插件、嵌入式、移动设备、Web服务
- Lua语言特点: Lua可以运行在大多数的平台上：ARM处理器，Windows，Unix，高通处理器，XBOX等平台上，可以说是Lua真的是无所不能。Lua十分简洁易懂，就连它的C API也非常的简单易懂，但是每个事物都是具有两面性的，Lua语言过度简易导致了它不能开发中大型的程序应用。

- [ ] lua语言能干什么？与游戏引擎之间的关系
## 1.Lua语言介绍（再看一遍）
- 用C语言开发，运行效率高。设计初衷是为了快捷高效，能嵌入C/C++程序中。也具有良好的跨平台性。
- 由于过于小巧，所以没什么具体的功能，需要C/C++来扩展
- 实践中用C/C++来实现功能，用Lua来实现逻辑会比较省心。

## 2.Lua语言基本语法
- 定义变量
```lua
num = 100  --全局变量
local num2 = 200  --局部变量
print(num)
print(num2)
```
- 定义方法
```lua
function sayhello()
  print("Hello Lua")
end

function max()
  if a > b then
    reurn a
  else
    reurn b
end

sayhello()
print((max(2, 3))
```
- 循环
```lua
for var=1, 100 do
  print(var)  -- print 1 to 100
end
```

## 3.lua语言基础之表
```lua
config = {hello = "Hello Lua", world="World"}
config.words = "Hello"
config.num = 100
config["name"] = "ZhangSan"  -- 键值对

print(config.words)
print(config.num)
print(config.name)

for key, var in pairs(config) do
  print(key, var)  -- 输出所以键值对
end
```

## 4.lua基础之数组
```lua
-- lua并没有数组的概念，数组也是用表来实现的
arr = {1, 2, 3, 4, "word"}

for key, var in pairs(arr) do
  print(key, var)  -- key是数组索引，从1开始
end
```

```lua
arr = {}

for var=1, 100 do
  table.insert(arr, 1, var)   -- 头插法，所以是逆序
end

for key, var in pairs(arr) do
  print(key, var)
end

function table_maxn(t)
  local mn=nil;
  for k, v in pairs(t) do
    if(mn==nil) then
      mn=v
    end
    if mn < v then
      mn = v
    end
  end
  return mn
end
-- 指定table中所有正数key值中最大的key值. 如果不存在key值为正数的元素, 则返回0

print(table_maxn(arr))   -- maxn在但是lua5.2后已经不存在了，这里自定义的函数
```

## 5.lua面向对象之复制表的方式面向对象
```lua
People = {}

-- 定义方式1
function People.sayBey()
  print("People say bye")
end

-- 定义方式2
People.sayHi = function()
  print("People say hi"..self.name)  -- ..表示字符串连接
  
end

-- 通过复制People来创建实例
function clone(tab)
  local ins = {}
  for key, var in pairs(tab) do
    ins[key] = var
  end
  return ins
end

-- 写一个构造方法
People.new = function(name)
  local self = clone(People)
  self.name = name
  return self
end


-- 调用方式1
local p1 = clone(People)
p1.sayHi()
p1.sayBey()

-- 调用方式2 通过构造方法
local p2 = People.new("ZhangSan")
p2:sayHi() -- 等价于 p2.sayHi(p2)

```

## 6.函数闭包的方式面向对象
```lua

```

## 7.使用Lua语言开发Cocos2d-x游戏
```lua

```

## 8.Lua语言卡片记忆游戏实例
```lua

```




