1. 字符串化操作符 `#`：
   - `#` 运算符可以将宏参数转换为字符串常量。
   - 示例：`#define STRINGIFY(x) #x`，`STRINGIFY(hello)` 将被替换为 `"hello"`。

2. 符号连接操作符 `##`：
   - `##` 运算符可以用于将两个符号连接在一起。
   - 示例：`#define CONCAT(x, y) x##y`，`CONCAT(name, 1)` 将被替换为 `name1`。

3. 宏与函数结合：
   - 可以使用宏来模拟简单的函数，例如实现最小值或最大值的宏。
   - 示例：`#define MIN(x, y) ((x) < (y) ? (x) : (y))`

4. 多行宏：
   - 使用 `\` 将宏定义延续到下一行。
   - 示例：
     ```c
     #define MAX(a, b) \
        ((a) > (b) ? (a) : (b))
     ```

5. 条件编译：
   - 使用宏来进行条件编译，根据不同的条件定义不同的代码。
   - 示例：
     ```c
     #ifdef DEBUG
     // debug 模式下的代码
     #endif
     ```

6. 宏的嵌套：
   - 可以在宏中调用其他宏，实现更复杂的功能。
   - 示例：`#define LOG(msg) printf("Log: %s\n", msg)`

7. 可变参数宏：
   - C99 引入了可变参数宏，可以使用 `__VA_ARGS__` 处理可变数量的参数。
   - 示例：
     ```c
     #define DEBUG_PRINT(format, ...) \
        fprintf(stderr, format, __VA_ARGS__)
     ```

8. 预定义宏：
   - C/C++ 中有一些预定义的宏，如 `__FILE__`、`__LINE__`、`__DATE__`、`__TIME__`，它们可以在宏中使用，用于生成调试信息或日志。

9. 使用宏进行代码生成：
   - 宏可以用于生成重复的代码结构，例如生成一系列相似的函数声明或定义。
   - 示例：
     ```c
     #define DECLARE_GETTER_SETTER(type, name) \
        type get##name() const; \
        void set##name(type value);
     ```

10. 防御性编程：
   - 可以使用宏来实现一些防御性编程技巧，例如断言宏。
   - 示例：
     ```c
     #define ASSERT(condition, message) \
        if (!(condition)) { \
            fprintf(stderr, "Assertion failed: %s\n", message); \
            exit(EXIT_FAILURE); \
        }
     ```
