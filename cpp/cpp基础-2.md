# C++
### 2.5 const 链接属性
C 语言下const 修饰的全局变量默认是外部链接属性，但在其他文件中引用时，要用 extern 声明，告诉编译器这是一个外部变量；

C++下，const 修饰的全局变量默认是内部链接属性，可以加 extern 来提高它的作用域；和C一样，在其他文件中引用时，都要加 extern 来声明。

### 2.6 const 分配内存情况
1、对const变量取地址，会分配临时内存；  
2、使用普通变量初始化const变量；  
3、对于自定义的数据类型；  
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;
//1、对const变量取地址，会分配临时内存
void test01()
{
	const int a = 10;
	int* p = (int*)&a;
}
//2、使用普通变量初始化const变量
void test02()
{
	int a = 10;
	const int b = a;
	int* p = (int*)&b;
	*p = 20;
	cout << b << endl;
}
//3、对于自定义的数据类型
struct Person {
	string m_Name;
	int m_Age;
};
void test03()
{
	const Person p;
	//p.m_Age = 10; //直接修改会失败
	Person* pp = (Person*)&p;
	(*pp).m_Name = "Tom";
	pp->m_Age = 10;
	cout << "姓名： " << p.m_Name << "年龄： " << p.m_Age << endl;
}
int main()
{
	test02();
	test03();
	return 0;
}
```
### 2.7 尽量用 const 代替 #define
```cpp
#define MAX 1024
//我们定义的宏MAX从未被编译器看到过，因为他在预处理阶段，所有的MAX已经被替换为1024了，于是MAX并没有被加入到符号表中；
//如果出现编译错误，且这个宏定义不在你写得头文件中，那么就根本不知道1024代表什么；
//解决办法是用一个常量替换宏定义
const int MAX = 1024;
```
**const 和 #define 的区别：**  
1、const 有类型，可进行编译器类型安全检查；#define 无类型，不可进行类型检查。
2、const 有作用域，而#define 不重视作用域，**默认定义处到文件结尾**，如果定义在指定作用域下有效的常量，那么#define就不能用。

### 2.8 引用
#### 2.8.1 引用的基本用法
目的：起别名  
语法：类型（与原名类型必须一致）& 别名 = 原名；  
1、引用的基本用法：类型& 别名 = 原名；  
2、引用必须初始化；  
3、引用一旦初始化后，就不可以引向其他变量；  
4、建立对数组的引用  
+ 直接建立引用；int arr[10];int(&pArr)[10] = arr;
+ 先定义出数组类型，在通过类型定义引用；typedef int(ARRAY_YPE)[10];ARRAY_YPE& pArr2 = arr;  

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;

//引用的基本用法； 类型& 别名 = 原名
void test01()
{
	int a = 10;
	int& b = a;
	b = 100;
	cout << "a = " << a << endl;
	cout << "b = " << b << endl;
}

void test02()
{
	int a = 10;
	int& b = a;
	int c = 100;
	b = c;	//这个是赋值操作
	cout << "a = " << a << endl;
	cout << "b = " << b << endl;
	cout << "c = " << c << endl;
}

//对数组建立引用
void test03()
{
	//1、直接建立引用
	int arr[10];
	int(&pArr)[10] = arr;
	for (int i = 0; i < 10; i++)
		arr[i] = 100 + i;
	for (int i = 0; i < 10; i++)
		cout << pArr[i] << endl;

	//2、先定义出数组类型，在通过类型定义引用
	typedef int(ARRAY_YPE)[10];
	//类型& 别名 = 原名
	ARRAY_YPE& pArr2 = arr;
	for (int i = 0; i < 10; i++)
		cout << pArr2[i] << endl;
}
int main()
{
	test01();
	test02();
	test03();
	return 0;
}
```
#### 2.8.2 参数的传递方式
1. 值传递  
2. 地址传递  
3. 引用传递，引用时直接建立原变量的别名，相当于指向的是同一块内存，所以可以直接修改  

>注意事项：  
>>1、引用必须引一块合法的内存空间，不能是常量；  
>>2、不要返回局部变量的引用；
>>>当引用返回的是引用的时候，那么函数的调用可以作为左值进行运算  

```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;

int& func()
{
	int a = 10;
	return a;
}

void test01()
{
	//引用必须引一块合法的内存空间
	//int& a = 10;

	//不要返回局部变量的引用
	int& ref = func();
	cout << "ref = " << ref << endl;
	cout << "ref = " << ref << endl;
}

int& func2()
{
	static int a = 10;
	return a;
}

void test02()
{
	int& ref = func2();
	cout << "ref = " << ref << endl;
	cout << "ref = " << ref << endl;
	cout << "ref = " << ref << endl;
	cout << "ref = " << ref << endl;

	//当函数返回值为引用的时候，函数的调用可以作为左值
	func2() = 100;
	cout << "ref = " << ref << endl;
}

int main()
{
	test01();
	test02();
	return 0;
}
```
#### 2.8.3 引用的本质
引用的本质在 C++ 内部实现是一个指针常量  
C++ 编译器在编译过程中使用常指针作为引用的内部实现，因此，引用占用的空间大小与指针相同，只是这个过程是编译器内部实现，用户不可见。  
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;

void testFunc(int& ref)	//发现是引用，转换为int* const ref = &value
{
	ref = 100;	//ref 是引用，转换为*ref = 100
}
int main()
{
	int a = 10;
	int& aRef = a;	//自动转换为 int* const aref = &a,这也能说明引用为什么必须初始化
	aRef = 20;	//内部发现aRef是引用，自动帮我们转换为: *aRef = 20
	cout << "a: " << a << endl;
	cout << "aRef: " << aRef << endl;
	testFunc(a);
	cout << "a: " << a << endl;
	return 0;
}
```

#### 2.8.4 指针的引用
1、利用引用可以简化指针；  
2、可以直接用同级指针的引用，给同级指针分配空间  
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;

struct Person {
	int age;
};
void allocateSpace(Person** p)
{
	*p = (Person*)malloc(sizeof(Person));
	(*p)->age = 10;
}
void test01()
{
	Person* p = NULL;
	allocateSpace(&p);
	cout << "p->age: " << p->age << endl;
}

//Person** const pp = &p
void allocateSpace2(Person*& pp)	//Person* &pp = p
{
	pp = (Person*)malloc(sizeof(Person));
	pp->age = 30;
}
void test02()
{
	Person* p = NULL;
	allocateSpace2(p);
	cout << "p->age: " << p->age << endl;
}
int main()
{
	test01();
	test02();
	return 0;
}
```
#### 2.8.5 常量的引用
1、常量是不可以直接引用的，但加上const之后可以引用，相当于写成了
```cpp
int& ref = 10;
//加入const之后，相当于写成了 int temp=10; const int& ref = temp;
const int& ref = 10;
```
2、常量引用的使用场景：修饰函数中的形参，防止误操作  
```cpp
#define _CRT_SECURE_NO_WARNINGS
#include<iostream>
#include<string>
using namespace std;

void test01()
{
	//int& ref = 10;
	const int& ref = 10;	//加入const之后，相当于写成了 int temp=10; const int& ref = temp;
	int* p = (int*)&ref;
	*p = 1000;
	cout << ref << endl;
}
//常量引用的使用场景 修饰函数中的形参，防止误操作
void showValue(const int& a)
{
	//a = 10000;
	cout << "a = " << a << endl;
}
void test02()
{
	int a = 10;
	showValue(a);
	cout << "a = " << a << endl;
}
int main()
{
	test01();
	test02();
	return 0;
}
```
```cpp
ouyput:
1000
a = 10
a = 10
```
