## 未解决
- [文件相关问题](https://www.nowcoder.com/test/question/done?tid=55835812&qid=157342#summary)
- [函数指针问题](https://www.nowcoder.com/test/question/done?tid=55835812&qid=304674#summary)
- [原码等1](https://www.nowcoder.com/test/question/done?tid=55846227&qid=247568#summary)
- [原码等2](https://www.nowcoder.com/test/question/done?tid=55846227&qid=111131#summary)

## 1.内存
### 1分区
- 程序代码区
- 常量区存放常量。程序结束时由OS回收。
- 全局区（静态区）存放全局变量和静态变量。初始化的全局变量和静态变量在一块区域，未初始化的全局变量和未初始化的静态变量在相邻的另一块区域。 程序结束时由OS回收。
- 堆区存放的变量（用new，malloc，calloc，realloc等分配内存函数得到的变量）由程序员分配释放。
- 栈区存放的变量（局部变量、函数参数等）由编译器自动分配释放。
