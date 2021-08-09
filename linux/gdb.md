# gdb(The GNU Project Debugger)
linux下的程序调试工具
## gdb的安装
执行下列命令，如果没安装，则进行安装；如果安装过，则进行更新  
1、Centos下：
```c
sudo yum -y install gdb
```
2、ubuntu下：
```c
sudo apt-get install gdb
```

## gdb调试
用gcc编译源程序的时候，编译后的可执行文件不会包含源程序代码，如果您打算编译后的程序可以被调试，编译的时候要加 -g 的参数  
例如：
```c
gcc -g xxx.c -o xxx
gdb xxx
```

## 基本调试命令

命令 | 命令缩写 | 命令说明
:-: | :-: | :-:
break | b | 设置断点<br>b 20 表示在第20行设置断点<br>可以设置多个断点
run | r | 开始运行程序, 程序运行到断点的位置会停下来<br>如果没有遇到断点，程序一直运行下去
next | n | 执行当前行语句<br>如果该语句为函数调用，不会进入函数内部执行
print | p | 显示变量值<br>p name表示显示变量name的值<br>p 后面也可以接表达式
continue | c | 继续程序的运行，直到遇到下一个断点
list | l | 查看文件代码
quit | q | 退出gdb环境
set var name=value |  | 设置变量的值<br>假设程序有两个变量：int ii; char name[21];<br>set var ii=10 把ii的值设置为10；<br>set var name="西施" 把name的值设置为"西施"，注意，不是strcpy。
set args |  | 设置主程序的参数<br>例如：./xxx a b<br>(gdb) set args a b<br>p argc p argv[0] p argv[1]打印
step | s | 	执行当前行语句，如果该语句为函数调用，则进入函数，执行该函数的第一条语句<br>注意，如果函数是库函数或第三方提供的函数，用s也是进不去的，因为没有源代码<br>如果是您自定义的函数，只要有源码就可以进去。

如果参数里面包括特殊字符，则需要将参数用双引号 "" 括起来  
在gdb调试下，可以使用 shell 命令， shell command

## segment fault(core dump)段错误(核心已转储)

操作非法地址之后，系统会生成 core 文件  
segment fault产生的原因：  
1. 坏指针错误：在指针赋值之前就用它来引用内存
2. 改写错误：越过数组边界写入数据，在动态分配的内存空间以外写入数据，或改写一些堆管理数据结构
3. 指针释放引起的错误：释放同一块内存两次，或释放一块未曾使用 malloc 分类的内存，或释放一个无效的指针

程序挂掉的时，系统缺省不会生成 core 文件  
1) gcc -g xxx.c  
2) ./a.out  
![](image/段错误.png)

3) ulimit -a 查看系统参数；  
![](image/ulimit-a.png)

4) ulimit -c unlimit 把 core 文件的大小设为无限制；  
![](image/修改core文件大小.png)

5) 运行程序，生成 core 文件；  
![](image/core.png)

6) gdb 程序名 core文件名  
![](image/调试.png)

7) 输入 bt，可查看函数调用栈  
![](image/函数调用栈.png)

## 调试正在运行的程序
如果程序进入了死循环，一直运行，那么重新打开一个终端，先查看当前的进程pid
```c
ps -ef | grep a.out
gdb a.out -p pid
```
![](image/pid.png)

## 调试多进程

```C
fork()函数  
#由fork创建的新进程被称为子进程（child process）。该函数被调用一次，但返回两次。两次返回的区别是子进程的返回值是0，而父进程的返回值则是子进程的进程 id  

wait(*status)函数  
//进程一旦调用了wait，就会分析当前进程的某个子进程是否已经退出。如果让它找到了这样一个已经变成僵尸的子进程，wait就会收集这个子进程的信息，并把它彻底销毁后返回；如果没有找到这样一个子进程，wait就会一直阻塞在这里，直到有一个出现为止。
//参数status是一个指向int类型的指针。如果我们对这个子进程是如何死掉的毫不在意，只想把这个僵尸进程消灭掉，我们就可以设定这个参数为NULL，就象下面这样：
wait(NULL)
//如果成功，wait会返回被收集的子进程的进程ID；如果失败，此时wait返回-1。

exit()函数
//关闭所有文件，终止正在进行的进程
//exit(0)表示正常退出
//exit(1)表示异常退出
```

```C
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<sys/types.h>
#include<sys/wait.h>

int main()
{
   pid_t id = fork();//创建一个子进程
   if(id == 0)
   {
      //id==0,说明这是子进程
      printf("i am child, pid = %d, ppid = %d\n",getpid(),getppid());
      int i;
      for(i = 0;i < 10;i++)
      {
         printf("child i = %d\n",i);
      }
      exit(1);
   }else{
      //id>0 说明这是父进程
      int i;
      sleep(1);
      printf("i am parent, pid = %d, ppid = %d\n",getpid(),getppid());
      for(i = 0;i < 10;i++)
      {
         printf("parent i = %d\n",i);
      }
      if(wait(NULL) != -1)
      {
         printf("wait success\n");
      }
   }
   return 0;
}
```

```C
follow-fork-mode:
set follow-fork-mode [parent/child] //设置调试进程，默认父进程
show follow-fork-mode   //查看当前调试模式

detach-on-fork:
set detach-on-fork [on/off]   //调试当前进程时，其它进程是运行的还是挂起的，默认是 on 
//挂起表示不显示进程执行内容

info inferiors
//查询正在调试的进程，带 * 表示当前正在调试的进程

inferior <infer number>
//切换调试进程
```

## 调试多线程
```C
#include<stdio.h>
#include<stdlib.h>
#include<unistd.h>
#include<pthread.h>

void* pthread_run1(void* arg)
{
   for(int i = 0; i < 10; i++)
   {
      printf("I am thread1, ID: %ld, %d\n",pthread_self(), i);
      sleep(1);
   }
}
void* pthread_run2(void* arg)
{
   for(int i = 0; i < 10; i++)
   {
      printf("I am thread2, ID: %ld, %d\n",pthread_self(), i);
   sleep(1);
   }
}
int main()
{
   pthread_t tid1;
   pthread_t tid2;
   //创建两个进程
   pthread_create(&tid1, NULL, pthread_run1, NULL);
   pthread_create(&tid2, NULL, pthread_run2, NULL);

   printf("I am main thread\n");
   //等待进程的结束
   pthread_join(tid1, NULL);
   pthread_join(tid2, NULL);
   return 0;
}
```
```C
gcc -g test_thread.c -lthread
//线程是第三方提供的线程库，编译时要链接第三方库
//-l 表示链接哪个库

info threads //查看线程
thread <ID> //切换线程
set scheduler-locking off|on
thread apply ID1 ID2 command //让一个或多个线程执行GDB命令command
thread apply all command //让若有被调试线程执行GDB命令command
```