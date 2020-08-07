# 1 内存分区模型

C++程序在执行时，将内存大方向划分为4个区域：

* 代码区：存放函数体的二进制代码，由操作系统进行管理
* 全局区：存放全局变量和静态变量以及常量
* 栈区：由编码器自行分配释放、存放函数的参数值、局部变量等
* 堆区：由程序员分配和释放，若程序员不释放，程序结束时由操作系统回收

内存四区意义：

不同区域存放的数据，赋予不同的生命周期，给我们更大的灵活编程

## 1.1 程序运行前

在程序编译后，生成了exe可执行程序，未执行该程序前分为两个区域

代码区：

* 存放CPU执行的机器指令，即二进制的0或1
* 代码区是共享的，共享的目的是对于频繁被执行的程序，只需要在内存中有一份代码即可
* 代码区是只读的，使其只读的原因是防止程序意外地修改了它的指令

全局区：

* 全局变量和静态变量存放在此
* 全局区还包括了常量区，字符串常量和其他常量也存放在此
* 该区域的数据在程序结束后由操作系统释放

```C++
#include <iostream>
using namespace std;

//全局变量
int g_a = 10;
int g_b = 10;
//const修饰的全局变量,全局常量
const int c_g_a = 10;
const int c_g_b = 10;

int main()
{
	//全局区
	//全局变量、静态变量、常量
	//创建普通局部变量
	int a = 10;//函数体内的变量，即局部变量
	int b = 10;
	cout << "局部变量a的地址：" << (int)&a << endl;
	cout << "局部变量b的地址：" << (int)&b << endl;

	cout << "全局变量g_a的地址：" << (int)&g_a << endl;
	cout << "全局变量g_b的地址：" << (int)&g_b << endl;

	//静态变量   在普通变量前面加static，属于静态变量
	static int s_a = 10;
	static int s_b = 10;
	cout << "静态变量s_a的地址：" << (int)&s_a << endl;
	cout << "静态变量s_b的地址：" << (int)&s_b << endl;

	//常量
	//字符串常量
	cout << "字符串常量的地址：" << (int)&"hello world" << endl;
	//const修饰的变量
	//const修饰的全局变量，const修饰的局部变量
	cout << "全局常量c_g_a的地址：" << (int)&c_g_a << endl;
	cout << "全局常量c_g_b的地址：" << (int)&c_g_b << endl;

	const int l_g_a = 10;   //const-c   global-g   local-l
	const int l_g_b = 10;
	cout << "局部常量l_g_a的地址：" << (int)&l_g_a << endl;
	cout << "局部常量l_g_b的地址：" << (int)&l_g_b << endl;
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片1.png)

<font color=red>**总结：**</font>

* 不在全局区：局部变量、const修饰的局部变量（局部常量）地址相近。

* 在全局区：全局变量、静态变量（static关键字）、常量：const修饰的全局变量（全局常量）、字符串常量地址相近。

* C++中在程序运行前分为全局区和代码区

 ##  1.1 程序运行后

栈区：由编译器自行分配释放，存放函数的参数值、局部变量等。

注意事项：不要返回局部变量的地址，栈区开辟的数据由编译器自动释放。

int 整数型变量类型

*表示指针

int* 定义一个指向整数型的指针变量。

```C++
#include <iostream>
using namespace std;

//栈区数据注意事项---不要返回局部变量的地址
//栈区的数据由编译器管理开辟和释放
int * func()//表示一个内存空间，这个空间用来存放一个指针，这个指针指向一个存放整数的空间
{
	int a = 10;//局部变量  存放在栈区，栈区的数据在函数执行完自动释放
	return &a;  //返回局部变量的地址  &是一个取地址符, &a表示变量a的地址*/
}
int main()
{
	//接受func函数的返回值
	int* p = func();
	cout << *p << endl;//第一次可以打印正确的数字，是因为编译器做了保留
	cout << *p << endl;//第二次这个数据就不再保留了
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片2.png)

堆区：

* 由程序员分配释放，若程序员不释放，程序结束时由操作系统回收
* 在C++中主要利用new在堆区开辟内存

```C++
#include <iostream>
using namespace std;

int * func()
{
	//利用new关键字，可以将数据开辟到堆区
	//指针 本质也是局部变量，放在栈上，指针保存的数据是放在堆区
	int *p=new int(10);
	return p;  
}
int main()
{
	//在堆区开辟数据
	int* p = func();
	cout << *p << endl;
	cout << *p << endl;
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片3.png)

## 1.1 new操作符

C++中利用new操作符在堆区开辟数据

堆区开辟的数据，由程序员手动开辟，手动释放，释放利用操作符delete

语法：`new 数据类型`

利用new创建的数据，会返回该数据对应的类型的指针

```C++
#include <iostream>
using namespace std;

//1.new的基本用法
int * func()
{
	//在堆区创建数据
	//new返回的是该数据类型的指针
	int *p=new int(10);
	return p;
}
void test01()
{
	int* p = func();
	cout << *p << endl;
	cout << *p << endl;
	cout << *p << endl;
	//堆区的数据由程序员管理开辟，程序员管理释放
	//如果想释放，可利用delete
	delete p;
	//cout << *p << endl;//内存已经被释放，再次访问就是非法操作，会报错
}
//2.在堆区利用new开辟数组
void test02()
{
	//创建10整型数据的数组，在堆区
	int *arr=new int[10];//10代表数组有10个元素
	for (int i = 0; i < 10; i++)
	{
		arr[i] = i + 100;//给10 个元素赋值100-109
		cout << arr[i] << endl;
	}
	//释放堆区数组
	//释放数组要在delete后加[]
	delete[] arr;
}

int main()
{
	test01();
	test02();
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片4.png)

# 2 引用

## 2.1 引用的基本使用

作用：给变量起别名

语法：`数据类型 &别名=原名`

```C++
#include <iostream>
using namespace std;

int main()
{
	//引用基本语法  数据类型 &别名=原名
	int a = 10;
	int& b = a;
	cout << "a = " << a << endl;
	cout << "b = " << b << endl;
	b = 100;
	cout << "a = " << a << endl;
	cout << "b = " << b << endl;
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片5.png)

## 2.2 引用注意事项

* 引用必须初始化

* 引用在初始化后，不可以改变，即b不能既做a的别名，又做b的别名

```C++
#include <iostream>
using namespace std;

int main()
{
	int a = 10;
	//1.引用必须初始化
	//int& b;//错误，必须要初始化 
	int& b = a;

	//2.引用在初始化后，不可以改变
	int c = 20;
	int& b = c;//错误

	system("pause");
	return 0;
}
```

## 2.3 引用做函数参数

作用：函数传递参数时，可以利用引用的技术让形参修饰实参

优点：可以简化指针修改实参

```C++
#include <iostream>
using namespace std;

//交换函数
//1.值传递
void mySwap01(int a, int b)
{
	int temp = a;
	a = b;
	b = temp;
	cout << "mySwap01 a = " << a << endl;
	cout << "mySwap01 b = " << b << endl;
}
//2.地址传递
void mySwap02(int* a, int* b)
{
	int temp = *a;
	*a = *b;
	*b = temp;
	cout << "mySwap02 a = " << *a << endl;
	cout << "mySwap02 b = " << *b << endl;
}
//3.引用传递
void mySwap03(int &a,int &b)
{
	int temp = a;
	a = b;
	b = temp;
	cout << "mySwap03 a = " << a << endl;
	cout << "mySwap03 b = " << b << endl;
}
int main()
{
	int a = 10;
	int b = 20;
	//mySwap01(a, b);//值传递，形参发生改变，实参不变
	//mySwap02(&a, &b);//地址传递，形参会修饰实参
	mySwap03(a, b);//引用传递，形参会修饰实参
	cout << "a = " << a << endl;
	cout << "b = " << b << endl;
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片6.png)

## 2.4 引用做函数返回值

作用：引用是可以作为函数的返回值存在的

注意：不要返回局部变量引用

用法：函数调用作为左值

```C++
#include <iostream>
using namespace std;

//引用做函数的返回值
//1.不要返回局部变量的引用
int & test01()//int & 表示引用
{
	int a = 10;//局部变量存放在四区中的栈区
	return a;
}
	//2.函数的调用(如test01（）)可以作为左值
int& test02()//int & 表示引用
{
	static int a = 10;//静态变量，存放在全局区，该区域的数据在程序结束后由操作系统释放
	return a;
}
int main()
{
	//int& ref = test01();
	//cout << "ref= " << ref << endl;//输出：ref= 10，第一次结果正确，因为编译器做了保留
	//cout << "ref= " << ref << endl;//输出：ref= 2020190608，第二次结果错误，因为a的内存已经释放
	int& ref2 = test02();
	cout << "ref2= " << ref2 << endl;//10
	cout << "ref2= " << ref2 << endl;//10
	test02() = 1000;//如果函数的返回值是引用，这个函数调用可以作为左值
	cout << "ref2= " << ref2 << endl;//1000
	cout << "ref2= " << ref2 << endl;//1000
	system("pause");
	return 0;
}
```

## 2.5 引用的本质

本质：引用的本质在C++内部实现的是一个指针常量

引用在初始化后，不可以改变，即b不能既做a的别名，又做b的别名

```C++
#include <iostream>
using namespace std;

//发现是引用，转换为 int * const ref=&a;
void func(int& ref)
{
	ref = 100;//ref是引用，转换为*ref = 100
}
int main()
{
	int a = 10;

	//自动转换为int * const ref=&a;指针常量是指向不可改，也说明为什么引用不可更改
	int& ref = a;
	ref = 20;//内部发现ref是引用，自动帮我们转换为：*ref=20;

	cout << "a :" << a << endl;//20
	cout << "ref :" << ref << endl;//20
	system("pause");
	return 0;
}
```

<font color=red>**总结：**</font>C++推荐用引用技术，因为语法方便，引用本质是指针常量，但是所有的指针操作编译器都帮我们做了

## 2.6 常量引用

作用：常量引用主要用来修饰形参，防止误操作

在函数形参列表中，可以加const修饰形参，防止形参改变实参

```C++
#include <iostream>
using namespace std;

//打印数据的函数
void showValue(const int& val)//用引用的方式接收,const int& val表示常量引用 
{
	//val = 1000;
	cout << "val = " << val << endl;
}
int main()
{
	//常量引用
	//使用场景：用来修饰形参，防止误操作
	//int a = 10;
	//int& ref = a;//int& ref = 10;是错误的，引用必须引一块合法的内存空间
	//const int& ref = 10;//加上const之后，编译器将代码修改，int temp = 10;const int & ref = temp;
	//ref =20;加入const之后变为只读，不可以修改

	int a = 100;
	showValue(a);//别名改了，原名也改
	cout << "a = " << a << endl;

	system("pause");
	return 0;
}
```

输出：不加const，val=1000,a=1000；加const后，val=1000,a=100。

# 3 函数提高

## 3.1 函数默认参数

在C++中，函数的参数列表中的形参是可以有默认值的

语法：`返回值类型 函数名 （参数=默认值）{}`

```C++
#include <iostream>
using namespace std;

//函数默认参数
//如果我们自己传入数据，就用自己的数据，如果没有，那么用默认值
//语法：返回值类型 函数名 （参数=默认值）{}
int func(int a, int b=20, int c=30)
{
	return a + b + c;
}
//注意事项
//1.如果某个位置已经有了默认参数，那么从这个位置往后，从左到右都必须有默认值
//2.如果函数声明有默认参数，函数实现就不能有默认参数，声明和实现只能有一个有默认参数
//int func2(int a=10, int b=10);
//int func2(int a=10, int b=10)//函数实现
//{
//	return a + b;
//}
int main()
{
	cout << func(10) << endl;
	system("pause");
	return 0;
}
```

## 3.2 函数占位参数

C++中函数的形参列表里可以有占位参数，用来做占位，调用函数时必须填补该位置

语法：`返回值类型 函数名 （数据类型）{}`

```C++
#include <iostream>
using namespace std;

//占位参数
//语法：返回值类型 函数名 （数据类型）{}
//占位参数，还可以有默认参数
void func(int a,int=10)
{
	cout << "this is func" << endl;
}
int main()
{
	func(10,10);//占位参数必须填补
	system("pause");
	return 0;
}
```

## 3.3 函数重载

### 3.3.1 函数重载概述

作用：函数名可以相同，提高复用性

函数重载满足条件：

* 同一个作用域下（全局作用域）
* 函数名称相同
* 函数参数**类型**不同或者**个数**不同或者**顺序**不同

注意：函数的返回值不可以作为函数重载的条件

```C++
#include <iostream>
using namespace std;

//函数重载，可以让函数名相同，提高复用性
//函数重载的满足条件
//1.在同一个作用域下
//2.函数名称相同
//3.函数参数类型不同或者个数不同或者顺序不同
void func()
{
	cout << "func 的调用" << endl;
}
void func(int a)//个数不同
{
	cout << "func(int a ) 的调用" << endl;
}
void func(double a)//类型不同
{
	cout << "func(double a ) 的调用" << endl;
}
void func(int a,double b)
{
	cout << "func(int a,double b ) 的调用" << endl;
}
void func(double a,int b)//和上式顺序不同
{
	cout << "func(double a,int b ) 的调用" << endl;
}
//注意事项：函数的返回值不可以作为函数重载的条件
//int func(double a, int b)
//{
//	cout << "func(double a,int b ) 的调用" << endl;
//}是错误的

int main()
{
	//func();调用第一个函数
	func(10);//调用第二个函数
	func(3.14);//调用第三个函数
	func(10, 3.14);//调用第四个函数
	func(3.14,10);//调用第五个函数
	system("pause");
	return 0;
}
```

### 3.3.2 函数重载注意事项

* 引用作为重载条件
* 函数重载碰到函数默认参数

```C++
#include <iostream>
using namespace std;

//函数重载注意事项
//1.引用作为重载的条件
void func(int& a)//int & a=10;是不合法的
{
	cout << "func(int& a) 调用" << endl;
}
void func(const int& a)//const int &a=10; 合法
{
	cout << "func(const int& a) 调用" << endl;//重载：类型不同
}

//2.函数重载碰到默认参数
void func2(int a,int b=10)
{
	cout << "func2的调用" << endl;
}
void func2(int a)
{
	cout << "func2(int a)的调用" << endl;
}

int main()
{
	//int a = 10;
	//func(a);//调用第一个
	//func(10);//调用第二个
	//func2(10);错误，当函数重载碰到默认参数，出现二义性，报错，尽量避免
	system("pause");
	return 0;
}
```

# 4 类和对象

C++面向对象的三大特性为：<font color=red>**封装、继承、多态**</font>

C++认为**万事万物皆为对象**，对象上有其属性和行为

例如：

人可以作为对象，属性有姓名、年龄、身高、体重……，行为有走、跑、跳、吃饭、唱歌……

车也可以作为对象，属性有轮胎、方向盘、车灯……，行为有载人、放音乐、开空调……

具有相同性质的**对象**，我们可以抽象称为**类**，人属于人类，车属于车类

## 4.1 封装

### 4.1.1 封装的意义

封装是C++面向对象三大特性之一

封装的意义：

* 将属性和行为作为一个整体，表现生活中的事物
* 将属性和行为加以权限控制

封装意义一：

在设计类的时候，属性和行为写在一起，表现事物

语法：`class 类名{   访问权限 ：属性  /行为  };`

例：设计一个圆类，求圆的周长

```C++
#include <iostream>
using namespace std;

const double PI = 3.14;//圆周率
//设计一个圆类，求圆的周长
//class代表类，语法：class 类名{   访问权限 ：属性  /行为  };
class Circle
{
	//访问权限
public://公共权限
	//属性
	int m_r;//半径
	//行为
	double calculateZC()//获取圆的周长
	{
		return 2 * PI * m_r;
	}
};
int main()
{
	//通过圆类创建具体的圆（对象）
	Circle c1;//实例化，即通过一个类，创建一个对象的过程
	c1.m_r = 10;//给圆对象的属性赋值
	cout << "圆的周长：" << c1.calculateZC() << endl;//c1.calculateZC()为函数调用
	system("pause");
	return 0;
}
```

例2：设计一个学生类，属性有姓名、学号，可以给姓名和学号赋值，可以显示学生的姓名和学号

```C++
#include <iostream>
using namespace std;

//设计一个学生类，属性有姓名、学号，可以给姓名和学号赋值，可以显示学生的姓名和学号
class Student
{
public:
	//类中的属性和行为统一称为成员
	//属性   成员属性  成员变量
	//行为   成员函数  成员方法
	//属性
	string m_Name;
	int m_Id;
	//行为
	void showStudent()
	{
		cout << "姓名：" << m_Name << "学号：" << m_Id << endl;
	}
	//给姓名赋值
	void setName(string name)
	{
		m_Name = name;
	}
	//给学号赋值
	void setId(int id)
	{
		m_Id = id;
	}
};
int main()
{
	//创建一个具体的学生，实例化对象
	Student s1;
	//s1.m_Name = "张三";用下式
	s1.setName("张三");
	//s1.m_Id = 1;
	s1.setId(1);
	s1.showStudent();

	Student s2;
	s2.m_Name = "李四";
	s2.m_Id = 2;
	s2.showStudent();
	system("pause");
	return 0;
}
```

封装意义二：

类在设计时，可以把属性和行为放在不同的权限下，加以控制

访问权限有三种：

1.public  公共权限

2.protected   保护权限

3.private   私有权限

```C++
#include <iostream>
using namespace std;

//访问权限，三种：
//公共权限 public		成员  类内可以访问，类外可以访问
//保护权限 protected		成员  类内可以访问，类外不可以访问   儿子可以访问父亲中的保护内容
//私有权限 private		成员  类内可以访问，类外不可以访问	儿子不可以访问父亲的私有内容
class Person
{
public:
	//公共权限
	string m_Name;
protected:
	//保护权限
	string m_Car;
private:
	//私有权限
	int m_Password;//银行卡密码
public:
	void func()
	{
		//类内访问
		m_Name = "张三";
		m_Car = "拖拉机";
		m_Password = 123456;
	}
};
int main()
{
	Person p1;
	p1.m_Name = "李四";
	//p1.m_Car = "奔驰";//保护权限内容，在类外访问不到
	//p1.m_Password = 123;//私有权限内容，在类外访问不到
	system("pause");
	return 0;
}
```

### 4.1.2 struct和class区别

在C++中struct和class唯一区别就在于**默认的访问权限不同**

区别：

* struct默认权限为公共
* class默认权限为私有

```C++
#include <iostream>
using namespace std;

class C1
{
	int m_A;//默认权限   私有
};
struct C2
{
	int m_A;//默认权限   公共
};
int main()
{
	//struct和class区别
	//struct默认权限是公共 public
	//class默认权限是私有  private
	C1 c1;
	//c1.m_A = 100;错误，不可访问
	C2 c2;
	c2.m_A = 100;
	system("pause");
	return 0;
}
```

### 4.1.3 成员属性设置为私有

优点1：将所有成员属性设置为私有，自己可以控制读写权限

优点2：对于写权限，我们可以检测数据的有效性

```C++
#include <iostream>
using namespace std;

//成员属性设置为私有
//1.自己可以控制读写权限
class Person
{
public:
	//设置姓名
	void setName(string name)
	{
		m_Name = name;
	}
	//获取姓名
	string getName()
	{
		return m_Name;
	}
	//获取年龄
	int getAge()
	{
		m_Age = 0;//初始化为0岁
		return m_Age;
	}
	//设置情人
	void setLover(string lover)
	{
		m_Lover = lover;
	}
private:
	string m_Name;//姓名  可读可写
	int m_Age;//年龄   只读
	string m_Lover;//情人   只写
};
int main()
{
	Person p;
	p.setName("张三");
	cout << "姓名：" << p.getName() << endl;

	cout << "年龄：" << p.getAge() << endl;

	p.setLover("李四");
	//cout << "情人：" << p.getLover() << endl;是不可以访问的
	system("pause");
	return 0;
}
```

```C++
//2.对于写权限，我们可以检测数据的有效性
//获取年龄  可读可写  如果想修改（年龄的范围必须是0~150之间）
int getAge()
{
    //m_Age=0;
    return m_Age;
}
//设置年龄
void setAge(int age)
{
    if(age<0||age>150)
    {
        cout<<"年龄有误！"<<endl;
        return;
    }
    m_Age=age;
}
int main()
{
    p.setAge();
    cout << "年龄：" << p.getAge() << endl;
}
```

练习案例：设计立方体类

设计立方体类（Cube）

求出立方体的面积和体积

分别用全局函数和成员函数判断两个立方体是否相等

高：m_H	长：m_L	宽：m_W

```C++
#include <iostream>
using namespace std;

//设计立方体类（Cube）
//1.创建立方体类
//2.设计属性
//3.设计行为 获取立方体的面积和体积
//4.分别用全局函数和成员函数判断两个立方体是否相等
class Cube
{
public:
	//设置长
	void setL(int l)
	{
		m_L = l;
	}
	//获取长
	int getL()
	{
		return m_L;
	}
	//设置宽
	void setW(int w)
	{
		m_W = w;
	}
	//获取宽
	int getW()
	{
		return m_W;
	}
	//设置高
	void setH(int h)
	{
		m_H = h;
	}
	//获取高
	int getH()
	{
		return m_H;
	}
	//获取立方体面积
	int calculateS()
	{
		return 2 * m_L * m_W + 2 * m_W * m_H + 2 * m_L * m_H;
	}
	//获取立方体体积
	int calculateV()
	{
		return m_L * m_W  * m_H ;
	}
	//利用成员函数判断两个立方体是否相等
	bool isSameByClass(Cube& c)
	{
		if (m_L == c.getL() && m_W == c.getW() && m_H == c.getH())//用m_L，类内可以访问private
		{
			return true;//return完不会执行if后面的语句
		}
		return false;
	}
private:
	int m_L;
	int m_W;
	int m_H;
};
//利用全局函数判断  两个立方体是否相等
bool isSame(Cube& c1, Cube& c2)//引用传递数据，不会另外再复制一份数据
{
	if (c1.getL() == c2.getL() && c1.getW() == c2.getW() && c1.getH() == c2.getH())
	{
		return true;//return完不会执行if后面的语句
	}
	return false;
}
int main()
{
	//创建立方体的对象
	Cube c1;
	c1.setL(10);
	c1.setW(10);
	c1.setH(10);

	cout << "c1的面积：" << c1.calculateS() << endl;
	cout << "c1的体积：" << c1.calculateV() << endl;

	//创建第二个立方体
	Cube c2;
	c2.setL(10);
	c2.setW(10);
	c2.setH(15);
	//利用全局函数判断
	bool ret=isSame(c1, c2);
	if (ret)
	{
		cout << "c1和c2是相等的" << endl;
	}
	else
	{
		cout << "c1和c2是不相等的" << endl;
	}
	//利用成员函数判断
	ret = c1.isSameByClass(c2);//c1已知，传入c2
	if (ret)
	{
		cout << "成员函数：c1和c2是相等的" << endl;
	}
	else
	{
		cout << "成员函数：c1和c2是不相等的" << endl;
	}
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片7.png)

练习案例2：点和圆的关系

设计一个圆形类（Circle）和一个点类（Point），计算点和圆的关系

快捷键：shift+tab  整体缩进

```C++
#include <iostream>
using namespace std;

//点和圆关系案例
//点类
class Point
{
public:
	//设置X
	void setX(int x)
	{
		m_X = x;
	}
	//获取X
	int getX()
	{
		return m_X;
	}
	//设置Y
	void setY(int y)
	{
		m_Y = y;
	}
	//获取Y
	int getY()
	{
		return m_Y;
	}
private:
	int m_X;
	int m_Y;
};
//圆类
class Circle
{
public:
	//设置半径
	void setR(int r)
	{
		m_R = r;
	}
	//获取半径
	int getR()
	{
		return m_R;
	}
	//设置圆心
	void setCenter(Point center)
	{
		m_Center = center;
	}
	//获取圆心
	Point getCenter()
	{
		return m_Center;
	}
private:
	int m_R;//半径
	//在类中可以让另一个类作为本类中的成员
	Point m_Center;//圆心

};
//判断点和圆的关系
void isInCircle(Circle& c, Point& p)
{
	//计算两点之间距离的平方
	int distance =
		(c.getCenter().getX() - p.getX()) * (c.getCenter().getX() - p.getX()) +
		(c.getCenter().getY() - p.getY()) * (c.getCenter().getY() - p.getY());
	//计算半径的平方
	int rDistance = c.getR() * c.getR();
	//判断关系
	if (distance == rDistance)
	{
		cout << "点在圆上" << endl;
	}
	else if (distance > rDistance)
	{
		cout << "点在圆外" << endl;
	}
	else
	{
		cout << "点在圆内" << endl;
	}
}
int main()
{
	//创建圆
	Circle c;
	c.setR(10);
	Point center;
	center.setX(10);
	center.setY(10);
	c.setCenter(center);
	//创建点
	Point p;
	p.setX(10);
	p.setY(10);

	//判断关系
	isInCircle(c, p);
	system("pause");
	return 0;
}
```

拓展：类的份文件编写 .h和.cpp

## 4.2 对象的初始化和清理

* 生活中我们买的电子产品都会有出厂设置，在某一天我们不用时候也会删除一些自己信息数据保证安全
* C++中的面向对象来源于生活，每个对象也都会有初始化设置以及对象销毁前的清理数据的设置

### 4.2.1 构造函数和析构函数

对象的初始化和清理也是两个非常重要的安全问题

一个对象或者变量没有初始状态，对其使用后果是未知

同样的使用完一个对象或者变量，没有及时清理，也会造成一定的安全问题



C++利用了构造函数和析构函数解决上述问题，这两个函数将会被编译器自动调用，完成对象初始化和清理工作

对象的初始化和清理工作是编译器强制要我们做的事情，因此**如果我们不提供构造和析构，编译器会提供**

**编译器提供的构造函数和析构函数是空实现**

* 构造函数：主要作用在于创建对象时为对象的成员属性赋值，构造函数由编译器自动调用，无需手动调用
* 析构函数：主要作用在于对象销毁前系统自动调用，执行一些清理工作

**构造函数语法：**`类名(){}`

1.构造函数，没有返回值也不写void

2.函数名称与类名相同

3.构造函数可以有参数，因此可以发生重载

4.程序在调用对象时会自动调用构造，无需手动调用，而且只会调用一次 

**析构函数语法：**`~类名(){}`

1.析构函数，没有返回值也不写void

2.函数名称与类名相同，在名称前加上符号"~"

3.构造函数不可以有参数，因此不可以发生重载

4.程序在对象销毁前会自动调用析构，无需手动调用，而且只会调用一次 

```C++
#include <iostream>
using namespace std;

//对象的初始化和清理
class Person
{
public:
	//1 构造函数	进行初始化操作
	//构造函数，没有返回值也不写void；函数名称与类名相同；构造函数可以有参数，因此可以发生重载；
	//程序在调用对象时会自动调用构造，无需手动调用，而且只会调用一次
	Person()
	{
		cout << "Person 构造函数的调用" << endl;
	}
	//2 析构函数 进行清理工作
	//析构函数，没有返回值也不写void；函数名称与类名相同，在名称前加上符号"~"；构造函数不可以有参数，因此不可以发生重载；
	//程序在对象销毁前会自动调用析构，无需手动调用，而且只会调用一次
	~Person()
	{
		cout << "Person 析构函数的调用" << endl;
	}
};
//构造和析构都是必须有的实现，如果我们自己不提供，编译器会提供一个空实现的构造和析构
void test01()
{
	Person p;//在栈上的数据，test01执行完毕后，释放这个对象
}
int main()
{
	test01();
	//Person p;
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片8.png)

如果：

```C++
int main()
{
	//test01();
	Person p;
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片9.png)

注意：后者在system后才会被释放

### 4.2.2 构造函数的分类及调用

两种分类方式：

按参数分为：有参构造和无参构造

按类型分为：普通构造和拷贝构造

三种调用方式：

括号法

显示法

隐式转换法

```C++
#include <iostream>
using namespace std;

//构造函数的分类和调用
//分类
class Person
{
public:
	//构造函数  按参数分为：有参构造和无参构造（默认构造）
	Person()
	{
		cout << "Person 无参构造函数的调用" << endl;
	}
	Person(int a)
	{
		age = a;
		cout << "Person 有参构造函数的调用" << endl;
	}
	//按类型分为：普通构造和拷贝构造
	//拷贝构造函数
	Person(const Person& p)
	{
		//将传入的人身上的所有属性，拷贝到我身上
		age = p.age;
		cout << "Person 拷贝构造函数的调用" << endl;
	}
	~Person()
	{
		cout << "Person 析构函数的调用" << endl;
	}
	int age;
};
//调用
void test01()
{
	//1.括号法
	//Person p1;//默认构造函数调用
	//Person p2(10);//有参构造函数调用
	//Person p3(p2);//拷贝构造函数
	//注意事项1：
	//调用默认构造函数的时候，不要加（）
	//因为下面这行代码，编译器会认为是一个函数的声明，不会认为在创建对象
	//Person p1();//与函数声明形似void func();

	/*cout << "p2的年龄：" << p2.age << endl;
	cout << "p3的年龄：" << p3.age << endl;*/
	//2.显示法
	Person p1;
	Person p2 = Person(10);//有参构造	
	Person p3 = Person(p2);//拷贝构造

	//Person(10);//匿名对象 特点：当前行执行结束后，系统会立即回收掉匿名对象
	//cout << "匿名对象" << endl;
	//注意事项2  不要利用拷贝构造函数 初始化匿名对象,编译器会认为Person(p3)==Person p3;对象声明
	//Person(p3);
	//3.隐式转换法
	Person p4 = 10;//相当于写了Person p4=Person(10);有参构造
	Person p5 = p4;//拷贝构造
}
int main()
{
	test01();
	system("pause");
	return 0;
}
```

### 4.2.3 拷贝构造函数调用时机

C++中拷贝构造函数调用时机通常有三种情况：

* 使用一个已经创建完毕的对象来初始化一个新对象
* 值传递的方式给函数参数传值
* 以值方式返回局部对象

```C++
#include <iostream>
using namespace std;

//拷贝构造函数调用时机通常有三种情况：


class Person
{
public:
	Person()
	{
		cout << "Person默认构造函数调用" << endl;
	}
	Person(int age)
	{
		cout << "Person有参函数调用" << endl;
		m_Age = age;
	}
	Person(const Person& p)
	{
		cout << "Person拷贝构造函数调用" << endl;
		m_Age = p.m_Age;
	}
	~Person()
	{
		cout << "Person析构函数调用" << endl;
	}
	int m_Age;
};
//1.使用一个已经创建完毕的对象来初始化一个新对象
void test01()
{
	Person p1(20);
	Person p2(p1);
	cout << "p2的年龄：" <<p2.m_Age <<endl;
}
	
//2.值传递的方式给函数参数传值
void doWork(Person p)//Person p 是形参
{

}
void test02()
{
	Person p;
	doWork(p);//p是实参
}
//3.以值方式返回局部对象
Person doWork2()
{
	Person p1;
	cout << (int*)&p1 << endl;
	return p1;
}
void test03()
{
	Person p = doWork2();
	cout << (int*)&p << endl;
}

int main()
{
	//test01();
	//test02();
	test03();
	system("pause");
	return 0;
}
```

### 4.2.4 构造函数调用规则

默认情况下，C++编译器至少给一个类添加3个函数

1.默认构造函数（无参，函数体为空）

2.默认析构函数（无参，函数体为空）

3.默认拷贝构造函数，对属性进行值拷贝

构造函数调用规则如下：

* 如果用户定义有参构造函数，C++不再提供默认无参构造，但是会提供默认拷贝构造
* 如果用户定义拷贝构造函数，C++不再提供其他构造函数

```C++
#include <iostream>
using namespace std;

//构造函数的调用规则
//1.创建一个类，C++编译器会给每个类都添加至少3个函数
//默认构造函数（无参，函数体为空）
//默认析构函数（无参，函数体为空）
//默认拷贝构造函数，对属性进行值拷贝


class Person
{
public:
	Person()
	{
		cout << "Person默认构造函数调用" << endl;
	}
	Person(int age)
	{
		cout << "Person有参函数调用" << endl;
		m_Age = age;
	}
	/*Person(const Person& p)
	{
		cout << "Person拷贝构造函数调用" << endl;
		m_Age = p.m_Age;
	}*/
	~Person()
	{
		cout << "Person析构函数调用" << endl;
	}
	int m_Age;
};
void test01()
{
	Person p;
	p.m_Age = 18;

	Person p2(p);
	cout << "p2的年龄：" <<p2.m_Age <<endl;
}

int main()
{
	test01();
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片10.jpg)

```C++
#include <iostream>
using namespace std;

//构造函数调用规则如下：
//如果用户定义有参构造函数，C++不再提供默认无参构造，但是会提供默认拷贝构造
//如果用户定义拷贝构造函数，C++不再提供其他构造函数
class Person
{
public:
	/*Person()
	{
		cout << "Person默认构造函数调用" << endl;
	}*/
	Person(int age)
	{
		cout << "Person有参函数调用" << endl;
		m_Age = age;
	}
	/*Person(const Person& p)
	{
		cout << "Person拷贝构造函数调用" << endl;
		m_Age = p.m_Age;
	}*/
	~Person()
	{
		cout << "Person析构函数调用" << endl;
	}
	int m_Age;
};
//void test01()
//{
//	Person p;//是错误的，如果用户定义有参构造函数，C++不再提供默认无参构造，但是会提供默认拷贝构造
//	p.m_Age = 18;
//
//	Person p2(p);
//	cout << "p2的年龄：" <<p2.m_Age <<endl;
//}
void test02()
{
	Person p(28);
	Person p2(p);
	cout << "p2的年龄：" << p2.m_Age << endl;
}
int main()
{
	//test01();
	test02();
	system("pause");
	return 0;
}
```

![](C:\Users\10755\Desktop\C++核心编程\图片11.jpg)

### 4.2.5 深拷贝与浅拷贝

浅拷贝：简单的赋值拷贝操作

深拷贝：在堆区重新申请空间，进行拷贝工作

```C++
#include <iostream>
using namespace std;

//深拷贝与浅拷贝
class Person
{
public:
	Person()
	{
		cout << "Person默认构造函数调用" << endl;
	}
	Person(int age,int height)
	{
		m_Age = age;
		m_Height=new int(height);//指针接收堆区的数据
		cout << "Person有参函数调用" << endl;
	}
	/*Person(const Person& p)
	{
		cout << "Person拷贝构造函数调用" << endl;
		m_Age = p.m_Age;
	}*/
	~Person()
	{
		//析构代码，将堆区开辟数据做释放操作
		if (m_Height != NULL)
		{
			delete m_Height;
			m_Height = NULL;
		}
		cout << "Person析构函数调用" << endl;
	}
	int m_Age;
	int* m_Height;//身高
};
void test01()
{
	Person p1(18,160);
	cout << "p1的年龄：" << p1.m_Age <<"身高:"<<*p1.m_Height<< endl;

	Person p2(p1);
	cout << "p2的年龄：" << p2.m_Age << "身高:"<< *p2.m_Height << endl;
}
int main()
{
	test01();
	system("pause");
	return 0;
}
```



![](C:\Users\10755\Desktop\C++核心编程\图片12.jpg)

析构释放遵循先进后出

先p2对堆区数据释放，后又p1释放，所以是非法操作

**浅拷贝带来的问题就是堆区的内存重复释放**

**浅拷贝的问题要利用深拷贝进行解决**

```C++
//自己实现拷贝构造函数，解决浅拷贝带来的问题
	Person(const Person& p)
	{
		cout << "Person拷贝构造函数调用" << endl;
		m_Age = p.m_Age;
		//m_Height = p.m_Height;编译器默认实现就是这行代码
		//深拷贝操作
		m_Height=new int(*p.m_Height);
	}
```

![](C:\Users\10755\Desktop\C++核心编程\图片13.jpg)

<font color=red>**总结：**</font>如果属性有在堆区开辟的，一定要自己提供拷贝构造函数，防止浅拷贝带来的问题

### 4.2.6 初始化列表

作用：C++提供了初始化列表语法，用来初始化属性

语法：`构造函数（）：属性1（值1），属性2（值2）……{}`

```C++
#include <iostream>
using namespace std;

//初始化列表
class Person
{
public:

	//传统初始化操作
	/*Person(int a,int b,int c)
	{
		m_A = a;
		m_B = b;
		m_C = c;
	}*/
	//初始化列表初始化属性：构造函数（）：属性1（值1），属性2（值2）……{}
	/*Person() : m_A(10), m_B(20), m_C(30)
	{

	}*/
	//改进：
	Person(int a,int b,int c) : m_A(a), m_B(b), m_C(c)
	{

	}
	int m_A;
	int m_B;
	int m_C;
};
void test01()
{
	//Person p(10, 20, 30);
	Person p(30,20,10);
	cout << "m_A = " << p.m_A << endl;
	cout << "m_B = " << p.m_B << endl;
	cout << "m_C = " << p.m_C << endl;
}
int main()
{
	test01();
	system("pause");
	return 0;
}
```

### 4.2.7 类对象作为类成员

C++类中的成员可以是另一个类的对象，我们称该成员为对象成员

例如：

```C++
class A{}
class B
{
    A a;
}
```

B类中有对象A作为成员，A为对象成员

那么当创建B对象时，A与B的构造和析构的顺序时谁先谁后？

```C++
#include <iostream>
using namespace std;

//类对象作为类成员
//手机类
class Phone
{
public:
	Phone(string pName)
	{
		cout << "Phone的构造函数调用" << endl;
		m_PName = pName;
	}
	~Phone()
	{
		cout << "Phone的析构函数调用" << endl;
	}
	string m_PName;//手机品牌名称
};
class Person
{
public:
	//Phone m_Phone=pName; 隐式转换法
	Person(string name, string pName):m_Name(name), m_Phone(pName)
	{
		cout << "Person的构造函数调用" << endl;
	}
	~Person()
	{
		cout << "Person的析构函数调用" << endl;
	}
	string m_Name;
	Phone m_Phone;
};
//当其它类对象作为本类成员，构造时候先构造类对象，再构造自身
//析构的顺序与构造相反
void test01()
{
	Person p("张三","苹果");
	cout << p.m_Name << "拿着：" << p.m_Phone.m_PName << endl;
}
int main()
{
	test01();
	system("pause");
	return 0;
}
```

### 4.2.8 静态成员

静态成员就是在成员变量和成员函数前加上关键字static，称之为静态成员

静态成员分为：

* 静态成员变量
  * 所有对象共享同一份数据
  * 在编译阶段分配内存
  * 类内声明，类外初始化

* 静态成员函数
  * 所有对象共享同一个函数
  * 静态成员函数只能访问静态成员变量

## 4.3 C++对象模型和this指针

### 4.3.1 成员变量和成员函数分开存储

在C++中，类内的成员变量和成员函数分开存储

只有非静态成员变量才属于类的对象上

```C++
#include <iostream>
using namespace std;

//成员变量和成员函数分开存储

class Person
{
	int m_A;//非静态成员变量 属于类的对象上的
	static int m_B;//静态成员变量  不属于类的对象上的
	void func(){}//非静态成员函数  不属于类的对象上的
	static void func2() {}//静态成员函数  不属于类的对象上的
};
int Person::m_B = 0;
void test01()
{
	Person p;
	//空对象占用内存空间为：1
	//C++编译器会给每个空对象也分配一个字节空间，是为了区分空对象占内存的位置
	//每个空对象也应该有一个独一无二的内存地址
	cout << "size of p =" << sizeof(p) << endl;
}
void test02()
{
	Person p;
	cout << "size of p =" << sizeof(p) << endl;
}
int main()
{
	//test01();
	test02();
	system("pause");
	return 0;
}
```

### 4.3.2 this指针概念

通过4.3.1我们知道在C++中成员变量和成员函数时分开存储的

每一个非静态成员函数只会诞生一份函数实例，也就是说多个同类型的对象会共用一块代码

那么问题是：这一块代码是如何区分哪个对象调用自己的呢？



C++通过提供特殊的对象指针，this指针，解决上面问题。**this指针指向被调用的成员函数所属的对象**

this指针是隐含每一个非静态成员函数内的一种指针

this指针不需要定义，直接使用即可



this指针的用途：

* 当形参和成员变量同名时，可用this指针来区分
* 在类的非静态成员函数中返回对象本身，可使用return *this