# shell
shell是一个命令行解释器，它接收应用程序/用户命令，然后调用操作系统内核；还是一个功能强大的编程语言，易编写、易调试、灵活性强。
![](/shell/image/shell.png)

## shell脚本入门
脚本以 **#!/bin/bash** 开头

```shell
#!/bin/bash

echo "hello world"
```
执行脚本命令
第一种执行方法是 bash 解析器帮你执行脚本，所以脚本不需要执行权限；第二种方法本质是脚本需要自己执行，所以需要执行权限。
```linux
相对路径或绝对路径都可以
1   sh heloworld.sh
    bash heloworld.sh
    ssh heloworld.sh
2   增加可执行权限后可以 ./helloworld.ssh
``` 

## shell中的变量
#### 系统变量
\$HOME   用户的家目录  
\$PWD    当前的目录
\$SHELL  默认使用的解析器
\$USER   当前用户

#### 自定义变量

##### 1 定义变量
```shell
变量=值  等号两侧不能有空格
A=2     #A = 2错误
```
##### 2 撤销变量
+ unset 变量

##### 3 声明静态变量
+ readonly 变量
注意：该类型变量不能 unset, 也不可更改，只能重新启动才能取消

##### 4 变量定义规则
1. 变量名有数字、字母及下划线组成，但不能以数字开头，**环境变量名建议用大写**；
2. **等号两侧不能有空格**
3. 在bash中，变量默认类型都是字符串类型，无法直接进行数值计算
   ```shell
   c=1+1
   echo $c
   -->1+1
   ```
4. 变量的值如果有空格，需要使用双引号或单引号括起来
5. 变量提升为全局变量后，可供其他shell使用
   export 变量名

#### 特殊变量
#### 1. $n
n为数字，\$0 表示该脚本名称，\$1~\$9代表第一到第九个参数，10以上的参数需要用大括号包含，如 \${10}。
```shell
#!/bin/bash
echo "$0 $1 $2 $3 $4"

执行脚本 ssh parament.sh duan de yu 95
--->parament.sh duan de yu 95
```

#### 2. $#
获取所有输入参数的个数，常用于循环

#### 3. $*, $@
\$*  这个变量代表命令行中所有的参数，\$* 把所有的参数看成一个整体
\$@  这个变量也代表命令行中所有的参数，不过 \$@ 把每个参数区别对待
```shell
ssh parament.sh duan de yu 95
$* --->duan de yu 95
$@ --->duan de yu 95
虽然看起来一样，但前者是一个整体"duan de yu 95"，而后者是独立的参数"duan" "de" "yu" "95"
```

#### 4. $?
最后一次执行命令的返回状态。如果这个变量的值为0，证明上一个命令正确执行；如果这个变量的值为非0（具体哪个数，由命令自己来决定），则证明上个命令执行不正确。

## 运算符
#### 基本语法
1. \$((运算式)) 或 \$[运算式]
2. expr +, -, \\*, /, %     加，减，乘，除，取余

注：expr 运算符间要有空格
```shell
必须每个运算符间都有空格
expr 3 + 2
expr `expr 2 + 3` \* 4
s=$[(2+3)*4]
```

## 条件判断
#### 1.基本语法
[ condition ] **condition前后都要有空格**
注：
+ 条件判断中不能用>=表达式
+ 条件非空即真

#### 2.常用判断条件
= 字符串比较
**整数比较**
-lt 小于（less than）
-le 小于等于（less equal）
-eq 等于（equal）
-gt 大于（greater than）
-ge 大于等于（greater equal）
-ne 不等于（no equal）
**按照文件权限进行判断**
-r 有读权限（read）
-w 有写权限（write）
-x 有可执行权限（execute）
**按照文件类型进行判断**
-f 文件存在并且是一个常规文件（file）
-e 文件存在（existence）
-d 文件存在并是一个目录（directory）
**多条件判断 &&  ||**
```shell
[ 23 -ge 22 ]
echo $?
[ -w parament.sh ]
```
## 流程控制
#### if判断
```shell
if [ 条件判断式 ];then
程序
fi

if [ 条件判断式 ]
then
程序
fi

if [ 条件判断式 ]
then
程序
elif [条件表达式]
程序
fi
```
#### case语句
```shell
case $变量名 in
“值1”)
程序1
;;
“值2”)
程序2
;;
*）
默认执行此句
;;
esac
```
#### for循环
for循环中可以用>=表达式、++表达式
```shell
for((初始值;循环控制变量;变量变化))
do
程序
done
```
```shell
#!/bin/bash
s=0
for((i=0;i<=100;i++))
do
s=$[$s+$i]
done
```
```shell
for 变量 in 值1 值2 值3
do
程序
done
```
```shell
打印所有输入的参数
for i in $*
do
echo “$i”
done
```
#### while循环
```shell
while [ 条件表达式 ]
do
程序
done
```
```shell
s=0
i=1
while [ $i -le 100 ]
do
s=$[$s+$i]
i=$[$i+1]
done
echo $s
```
#### read读取控制台的输入
read 选项 参数
选项：
   -p 指定读取时的提示符
   -t 指定读取时等待的时间（秒）
变量：指定读取值的变量名
```shell
#!/bin/bash
read -t 7 -p “Enter your name in 7 seconds” NAME
echo $NAME
```
## 函数
#### 系统函数
1. basename 文件路径
从文件路径中删除路径，返回文件名
basename 文件路径 文件名后缀
从文件路径中删除路径，并将文件名后缀去掉，返回不加后缀的文件名
2. dirname 文件的绝对路径
从绝对路径中删除文件名，返回剩下的路径

#### 自定义函数
这几个中括号中的内容可写可不写，一般写上可读性较好
```shell
[function]funname[()]
{
Action;
}
funname
```
+ 注意：
  必须在调用函数的地方之前，先声明函数，shell脚本是逐行运行的，不会像其他语言一样先编译
  function和()可以省略，但是不能同时省略function关键字和()小括号
  函数体不能为空

```shell
#!/bin/bash

function sum()
{
s=0;
s=$[$1+$2];
echo $s;
}
read -p “input interger1” p1
read -p “input interger1” p2
sum $p1 $p2
```
