- C++自带了一个包含函数的大型库（标准ANSI库加上多个C++类），但真正的编程乐趣在于编写自己的函数

## 7.1 复习函数的基本知识
- 要使用C++函数，必须：
  - 提供函数定义；
  - 提供函数原型；
  - 调用函数； 

### 7.1.1 定义函数
- 可以将函数分为两类：
```C++
// 1.没有返回值的函数(void)
void funtionName(parameterList)  // parameter指定了传递给函数的参数类型和数量
{
    statement(s);
    return;  // 可选的,返回语句标记了函数的结尾
}
// 2.有返回值的函数
typeName funtionName(parameterList)
{
    statement(s);
    return value;  // 必须有返回语句
}
```

