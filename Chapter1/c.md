# C 指针

通过指针，可以简化一些 C 编程任务的执行，还有一些任务，如动态内存分配，没有指针是无法执行的。每一个变量都有一个内存位置，每一个内存位置都定义了可使用 **&** 运算符访问的地址，它表示了在内存中的一个地址。

```c
#include <stdio.h>
 
int main ()
{
    int var_runoob = 10;
    int *p;              // 定义指针变量
    p = &var_runoob;
 
   printf("var_runoob 变量的地址： %p\n", p);
   return 0;
}
/*运行结果为
var_runoob 变量的地址： 0x7ffeeaae08d8（输出结果不一定回一摸一样，因为每个系统的不一样）
*/
```

![img](https://www.runoob.com/wp-content/uploads/2014/09/c-pointer.png)
