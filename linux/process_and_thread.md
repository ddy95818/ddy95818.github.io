# 进程和线程
## 进程

## 线程
代码里面涉及多线程的东西，gcc的时候要加 **-lpthread**  

[多线程讲解](https://www.bilibili.com/video/BV1kt411z7ND)

```C
example1.c
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>

void* myfunc(void* arg){    //固定格式
    int i;
    for(i = 1; i <= 50; i++)
    {
        printf("%d\n", i);
    }
    return NULL;
}

int main() {
    pthread_t th1;
    pthread_t th2;

    //第一个参数为创建的线程地址 第二个参数一般为NULL 
    //第三个参数为线程要运行的函数 第四个为函数的参数
    pthread_create(&th1, NULL, myfunc, NULL);
    pthread_create(&th2, NULL, myfunc, NULL);

    //等待线程结束，在执行return 0，否则主函数执行完毕，其他线程不管有没有执行都会结束
    pthread_join(th1, NULL);
    pthread_join(th2, NULL);

    return 0;
}
```
```C
example2.c
#include<stdio.h>
#include<stdlib.h>
#include<pthread.h>

typedef struct{
        int first;
        int last;
        int result;
}MY_ARGS;
int arr[5000];

void* myfunc(void* args){
    int i;
    int s = 0;
    MY_ARGS *my_args = args;
    int first = my_args->first;
    int last = my_args->last;
    for(i = first; i < last; i++)
    {
        s=s+arr[i];
    }
    my_args->result = s;
    return NULL;
}
int main() {
    for(int i = 0;i < 5000; i++){
        arr[i] = rand() % 50;
    }
    MY_ARGS arg1 = {0, 2500, 0};
    MY_ARGS arg2 = {2500, 5000, 0};
    pthread_t th1;
    pthread_t th2;

    pthread_create(&th1, NULL, myfunc, &arg1);
    pthread_create(&th2, NULL, myfunc, &arg2);

    pthread_join(th1, NULL);
    pthread_join(th2, NULL);

    int s1 = arg1.result;
    int s2 = arg2.result;
    printf("s1 = %d\n", s1);
    printf("s2 = %d\n", s2);
    printf("s1 + s2 = %d\n", s1+s2);

    return 0;
}
```
