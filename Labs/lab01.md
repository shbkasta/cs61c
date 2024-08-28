# lab01

## Exercise 1
掌握宏的基本用法，编译器会先将宏定义的值进行替换
题目要求我们更改宏定义的值，使得eccentrics.c程序输出如下结果
```
Berkeley eccentrics:
====================
Happy Happy Happy
Yoshua
Go BEARS!
```
分析程序，第一行和第二行进入程序就会打印。
接下来要打印3和 Happy，可以看到其循环次数由v0控制，因此将v0的宏改为3
接下来要打印Yoshua，可以看到当v1=3的时候，会打印该值,因此将v1的宏设为1。当v1=0的时候，还会打印case1的值才会break。
接下来要打印Go,v3==3成立的时候会打印该值，因此v3的宏设为3
最后要打印BEARS,当if(v4)为真的时候打印，在C里面，非零代表真，因此v4可以取任何一个非零值，此处取1。
```c
#define V0 3
#define V1 3 
#define V2 1
#define V3 3
```

## Exercise 2
入门命令
```
gcc -g 编译的时候加上调试信息
gdb filename 调试filename这个可执行文件
	b 在某处打断点
	s 下一步
```
问题解答
```
1. run arglist --start your program with arglist
2. b function_name --set breakpoint at function_name
	后面还可以跟多少行，可以跟偏移量等等
3. s --execute until another line reached
4. next --execute next line, including any function
calls
5. c --continue running;
6. p --show value of expr
7. display --show value of expr each time program
stops
8. info locals --local variables of selected frame
9. quit
```

## Exercise 3
题意大致是要求我们不使用命令行输入，使用文件进行输入，可以使用重定向操作
```
./int_hello <int_hello_ans.txt

int_hello_ans.txt是自己创建的文件，里面的内容就是自己要输入的内容
```

## Exercise 4
1. Valgrind可以帮助我们追踪cpu和内存访问信息，检测一些深度bug
2. Valgrind ./segfault_ex
3. 在main函数的第4行有写入错误，地址0x1fff001000不在栈或者堆上
```
==31736== Invalid write of size 4
==31736==    at 0x10914F: main (segfault_ex.c:4)
==31736==  Address 0x1fff001000 is not stack'd, malloc'd or (recently) free'd
```
4. 因为未初始化的值可能存在任何值，比如
```
int \*p;
*p=10;
```
p这个地址可能是任何地址，比如是0地址，这样就会将0地址上的内容改成10，这是不可以的。
5. a是一个指向有5个int元素的指针，因此sizeof(a)=20,循环的时候会越界，导致segfault。但是可能在第6~20个元素的值比较小，因此我们访问的时候没有发生segfault
6. 因为6~20次循环的时候，值未知
7. 用sizeof(a)/sizeof(int)代替

## Exercise 5
很经典的一个判断链表有没有环的题目，不理解可以力扣搜索一下讲解
```
int ll_has_cycle(node *head) {
    node *p=head,*q=head;
    while(p!=NULL && p->next!=NULL)
    {
        p=p->next->next;
        q=q->next;
        if(p==q) return 1;
    }
    return 0;
}
```





