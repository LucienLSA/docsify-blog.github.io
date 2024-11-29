---
title: c++11及以上新特性      # 文章标题
author: Lucien
katex: true
categories: 
- cpp
tags:
- cpp
---
# new

## 局部变量和堆变量

```cpp
Tesk t1 = Tesk(1,2,3); // (1)
Test* t2 = new Test(1,2,3); // (2)  
```

(1) 是在栈上创建了一个对象，属于局部变量（自动释放）。

(2) 是在堆上创建了一个对象，属于堆变量（手动释放）。

## 栈变量(局部变量)

```cpp
void Temp();
int main()
{
    int a = 1; // (3)
    Temp();
    return 0;
}
void Temp()
{
    int* b = NULL; // (4)
}
```

普通创建的变量被分配在栈（后进先出）上，称为栈变量

栈变量的生命周期是临时的。从它被创建的时候产生，作用域结束的时候自动释放。

由于栈变量是在栈上创建的，因此在非特殊情况下，最晚创建的变量将会最先被消灭。

## 堆变量

```cpp
int c = 2; // (5)
int main()
{
    int* d = (int*)malloc(sizeof(int)); // (6)
    int* e = new int; // (7)
    int* f = new int(1); // (8)
    return 0;
}
```

堆变量就是全局、静态、用 malloc 、new 等类似的关键字申请堆空间的变量（如 (5)、(6)、(7) 、(8) ）（与关键字有关的一般返回的都是指针 ）。

堆变量分配的空间不会自动释放 ，若在程序中没有手动释放堆变量，它将一直占用系统内存。

## new详细

当使用关键字 new 在堆上动态创建一个对象时，它实际上做了三件事（ 只考虑申请空间成功的情况 ）：

1）申请一块新的、对应类型大小的内存空间（不会自动释放），创建该类型的对象。

2）调用该类的构造函数，对对象进行初始化。

3）返回指向该对象的指针。

如果我们创建的是简单类型的变量（无构造函数的类型的变量），那么第二步会被省略。

想要释放该空间需要手动删除，即书写 delete(指针变量名); ，否则会造成内存的泄露。

### 用法

类型名* 指针变量名 = new 该对象的构造函数名( parameter1, ...)；

类型名* 指针变量名 = new 类型名( parameter1, ...)；

```cpp
int* e = new int; // (7)
int* f = new int(1); // (8)
 
delete(e); // 不用的时候记得要手动释放内存
delete(f); // 不用的时候记得要手动释放内存
```

#### 7

使用关键字 new 在堆上申请一段 int 类型大的空间（不会自动释放），创建一个 int 类型的对象 。

~~调用 int 的无参构造函数（相当于什么也没有做）。~~

返回该 对象（int 类型） 的指针给指针变量 e 。

#### 8

使用关键字 new 在 堆 上申请一段 int 类型大的空间（不会自动释放），创建一个 int 类型的对象

# Lambda表达式

[深入浅出 C++ Lambda表达式：语法、特点和应用](https://blog.csdn.net/m0_60134435/article/details/136151698)

## 定义

Lambda表达式是一种在被调用的位置或作为参数传递给函数的位置定义匿名函数对象（闭包）的简便方法。

1. capture list 是捕获列表，用于指定 Lambda表达式可以访问的外部变量，以及是按值还是按引用的方式访问。捕获列表可以为空，表示不访问任何外部变量，也可以使用默认捕获模式 & 或 = 来表示按引用或按值捕获所有外部变量，还可以混合使用具体的变量名和默认捕获模式来指定不同的捕获方式。
2. parameter list 是参数列表，用于表示 Lambda表达式的参数，可以为空，表示没有参数，也可以和普通函数一样指定参数的类型和名称，还可以在 c++14 中使用 auto 关键字来实现泛型参数。
3. return type 是返回值类型，用于指定 Lambda表达式的返回值类型，可以省略，表示由编译器根据函数体推导，也可以使用 -> 符号显式指定，还可以在 c++14 中使用 auto 关键字来实现泛型返回值。
4. function body 是函数体，用于表示 Lambda表达式的具体逻辑，可以是一条语句，也可以是多条语句，还可以在 c++14 中使用 constexpr 来实现编译期计算

## 捕获方式

1. 值捕获
   在捕获列表中使用变量名，表示将该变量的值拷贝到 Lambda 表达式中，作为一个数据成员。值捕获的变量在 Lambda 表达式定义时就已经确定，不会随着外部变量的变化而变化。值捕获的变量默认不能在 Lambda 表达式中修改，除非使用 mutable 关键字
2. 引用捕获
   在捕获列表中使用 & 加变量名，表示将该变量的引用传递到 Lambda 表达式中，作为一个数据成员。引用捕获的变量在 Lambda 表达式调用时才确定，会随着外部变量的变化而变化。引用捕获的变量可以在 Lambda 表达式中修改，但要注意生命周期的问题，避免悬空引用的出现。
3. 隐式捕获
   在捕获列表中使用 = 或 &，表示按值或按引用捕获 Lambda 表达式中使用的所有外部变量。这种方式可以简化捕获列表的书写，避免过长或遗漏。隐式捕获可以和显式捕获混合使用，但不能和同类型的显式捕获一起使用。
4. 初始化捕获
   允许在捕获列表中使用初始化表达式，从而在捕获列表中创建并初始化一个新的变量，而不是捕获一个已存在的变量。这种方式可以使用 auto 关键字来推导类型，也可以显式指定类型。这种方式可以用来捕获只移动的变量，或者捕获 this 指针的值。

# 左值、右值和std::move

## 左值和右值

### 左值

左值是一个表达式，它指向内存中的一个固定地址，并且可以出现在赋值操作的左侧。左值通常指的是变量的名字，它们在程序的整个运行期间都存在。左值的判断标准：可以取地址、有名字的就是左值。

### 右值

右值是一个临时的、不可重复使用的表达式，它不能出现在赋值操作的左侧。右值通常包括字面量、临时生成的对象以及即将被销毁的对象。右值的判断标准：不可以取地址、没有名字的就是右值。

## std::move

强制转换为右值，将一个对象的左值引用转换为右值引用，这个操作实质上是一个静态类型转换

### 作用

1. 触发移动构造函数或移动赋值操作符，从而避免对象的复制构造，特别是在涉及到大量资源（如动态内存、文件句柄等）时。
2. 与标准库中的容器和算法一起使用，以优化临时对象的处理。

## 完美转发forward

完美转发主要解决了在模板编程中参数转发时保留参数类型和值类别的问题。在C++11之前，模板函数在转发参数时可能会无意中改变参数的类型或值类别，尤其是当参数是左值或右值时。

引入完美转发后，开发者可以确保转发的参数保持其原始的类型和值类别

### 作用

1. 保持参数的左值和右值属性：在C++11中引入了右值引用，用以区分左值和右值。完美转发确保了在模板函数中可以正确地保持参数的左值或右值属性。
2. 避免不必要的复制：通过完美转发，可以避免在转发过程中对某些参数进行不必要的复制，尤其是在转发临时对象时，可以利用移动语义提高效率。
3. 提高模板函数的通用性：完美转发允许模板函数接受任意类型的参数，并将它们按原样转发给其他函数，从而提高了模板函数的通用性和灵活性。

```c++
#include <iostream>
#include <utility> // For std::forward
 
// 模拟一个可以接收左值引用和右值引用的函数
void process(int& x) {
    std::cout << "process(int&) - Lvalue reference" << std::endl;
}
 
void process(int&& x) {
    std::cout << "process(int&&) - Rvalue reference" << std::endl;
}
 
// 模板函数，使用完美转发将参数转发给process函数
template<typename T>
void wrapper(T&& arg) {
    // 使用std::forward来转发参数，保持其原始的类型和值类别
    process(std::forward<T>(arg));
}
 
int main() {
    int a = 10;
    wrapper(a);  // 编译器将调用 process(int&)，因为a是左值
    wrapper(10); // 编译器将调用 process(int&&)，因为10是临时对象（右值）
}
```

# 新特性emplace

emplace、emplace_back、emplace_front，分别对应insert、push_back、push_front

## emplace

1. 调用push或者insert时，将元素类型的对象传递出去，这些对象被拷贝到容器当中，或者创建一个局部临时对象，并将其压入容器
2. 调用emplace时，则是将参数传递给元素类型的构造函数，emplace成员使用这些参数在容器管理的内存空间中直接构造元素，没有拷贝的操作

reserve操作为容器预留足够的空间，避免不必要的重复分配

vector的size超过capacity时会重新进行内存分配（一般是double），并把原有的数据拷贝到新申请的地址。这是vector的实现细节

### emplace和push的对象是右值

1. emplace函数在容器中直接构造，push不支持直接构造
2. push则是先构造一个临时对象，再把该对象拷贝到容器中，临时对象还需要析构
3. 针对构造好的右值，emplace和push没有区别
4. 针对右值，emplace的效率更高

### emplace和push的对象是左值

1. emplace是直接把构造好的左值对象拷贝到容器当中
2. push也是直接把构造好的左值对象拷贝到容器当中
3. 针对左值，emplace和push的效率是一样的

# 模板

## 函数模板

1. 隐式调用，不需要明确传入一个类型参数
2. 显式调用，传入一个类型参数

```c++
#include <iostream>
#include <string>

// 函数模板
template <typename _Ty>
_Ty Max(_Ty a, _Ty b) 
{
    return a > b ? a : b; // 不能直接比较，因为自定义数据类型，需要重载运算符
}

// 自定义数据类型
class MM
{
public: 
    MM(std::string name, int age) : name(name), age(age){} // 初始化列表
    friend std::ostream& operator<<(std::ostream& out, const MM& object);
    bool operator>(MM object) // 重载>
    {
        return this->age > object.age;
    }
protected:
    std::string name;
    int age;
};

std::ostream& operator<< (std::ostream& out, const MM& object) // 重载左移运算符
{
    out << object.name << "\t" << object.age << std::endl;
    return out;
}

// 类中成员函数是函数模板
class Test
{
public:
    template <typename _Ty> 
    void print(_Ty data) // 模板函数
    {
        std::cout << data << std::endl;
    }
protected:  
};

int main() {
    std::cout << Max("张三","李四") << std::endl;
    // 操作自定义数据类型
    MM mm("mm", 14);
    MM girl("girl",19);
    std::cout << Max(mm, girl) << std::endl; // 重载运算符<<
    // 类中成员函数是模板函数
    Test test;
    test.print(1);
    test.print("string");
    return 0;
}
```

## 类模板

1. 类中用到未知类型

- 必须显式调用
- 所有用到类名：类名<未知类型>

```c++
// 类模板 
template <class _Ty1, class _Ty2> class MM1
{
public:
    MM1(_Ty1 first, _Ty2 second):first(first), second(second)
    { 

    } // 构造函数
    void print2()
    {
        std::cout << first << "\t" << second << std::endl;
    }
protected:
    static int num;
    _Ty1 first;
    _Ty2 second;   
};
struct Score
{
    int math;
    int english;
    int chinese;
    Score(int m, int e, int c) : math(m), english(e), chinese(c) {}
};
std::ostream& operator<<(std::ostream& out, const Score& object) 
{
    out << object.math << "\t" << object.english << "\t" << object.chinese << std::endl;
    return out;
}
void testMM1()
{
    MM1<std::string, int> test(std::string("test"), 18);
    test.print2();
    MM1<std::string, std::string>* pstr = new MM1<std::string,std::string>("小明", "小红");
    pstr->print2();
    delete pstr;
    MM1<std::string, Score>* pScore = new MM1<std::string, Score>("小明", Score(99,78,89));
    pScore->print2();
    delete pScore;
}

// 类外实现静态成员变量
template <class _Ty1, class _Ty2> int MM1<_Ty1, _Ty2>::num = 0;
// 类外实现成员函数
// template <class _Ty1, class _Ty2> void MM1<_Ty1, _Ty2>::print2()
// {
//     std::cout << "类模板" << std::endl;
// }
// 类模板的继承
template <class _Ty1, class _Ty2> class Girl : public MM1<_Ty1, _Ty2>
{
public:
};
    // 类模板
    testMM1();
```

## 模板嵌套

```c++
// 模板嵌套
template <class _Ty>
struct A {
    _Ty data;
    A() : data() {}
    A(_Ty data) : data(data){} 
};
template <class _Ty, class _T> 
struct B {
    _T b;
    A<_Ty> aObject;
};

// 模板类嵌套
template <class T>
class Data {
public:
    Data(T data) : data(data) {}
    // 类中函数不需要加template<class T>
    void print(T data) {
        std::cout << "Data is " << data << std::endl;
    }
    // 友元类外函数，需要手动加template<class T>
    // 在友元函数的声明中，模板参数 T 与类模板 Data<T> 中的模板参数 T 冲突。使用不同的名称来替代 T
    template <class U> friend std::ostream& operator<<(std::ostream& out, const Data<U>& obj);
protected:
    T data;
};
// 类外声明左移运算符
template <class U> std::ostream& operator<<(std::ostream& out, const Data<U>& obj)
{
    out <<  obj.data;
    return out;
}

template <class _Ty>
class MM 
{
public:
    MM(_Ty data) :data(data) {}
    void print() {
        std::cout << "Data is " << data << std::endl;
    }
protected:
    _Ty data; // Data<std::string>类型
};
int main() {
    // 普通嵌套
    B<std::string, int> bObject;
    bObject.b = 1;
    bObject.aObject = A<std::string>("Love");

    MM<Data<std::string>> girl(Data<std::string>("Hello"));
    girl.print();
    return 0;
}
```

## 可变参数模板

```c++
int print (const char * format, ...);
int scanf (const char * format, ...);
```

... 就是可变参数列表，使用了可变参数列表，但是scanf 和 printf 的可变参数是函数参数的可变参数，并不是模板的可变参数

### 定义

```c++
// Args是一个模板参数包，args是一个函数形参参数包
// 声明一个参数包Args...args，这个参数包中可以包含0到任意个模板参数。
template <class ...Args>
void ShowList(Args... args)
{}
```

带省略号的参数称为 “参数包”，它里面包含了0到N（N>=0）个模版参数。Args是一个模板参数包，args是一个函数形参参数包

无法直接获取参数包 args 中的每个参数的，只能通过展开参数包的方式来获取参数包中的每个参数

### 折叠参数

1. 递归的方式展开参数包

```c++
template<class ...Args>
void ShowList(Args... args)
{
	cout << sizeof...(args) << endl; //获取参数包中参数的个数
}
int main()
{
	ShowList(1);
	ShowList(1, 2);
	ShowList(11, 22, 'a');
	ShowList(11, 22, 'a', "BBB");
	ShowList();
	return 0;
}
```

2. 列表的方式展开参数包

```c++
// 列表的方式展开
template <class _Ty>
void print(_Ty data)
{
    std::cout << data << std::endl;
}
template <class ...Args>
void printData(Args... args) {
    int array[] = {(print(args), 0)...};
}
printData(1, 2, "love", "s", 5);
```

### tuple 标准库的元组，可变参数模板类

所有数据都可以是一个元组

#### 模板特化+继承的方式展开参数

```c++
// 模板特化+递归的方式展开函数包
template<class ...Args>
class Tup;
// 特化
template<>
class Tup<> {};
// 递归
template<class _Ty, class... Args>
class Tup<_Ty, Args...> {
public:
    Tup(_Ty data, Args... args):data(data), args(args...) {}
    _Ty& head()
    {
        return data;
    }
    Tup<Args...>& next()
    {
        return args;
    }
protected:
    _Ty data;
    Tup<Args...> args;
};
testTup();
```

#### 模板特化+递归的方式展开参数

# c++11可调用对象

```c++
int func1(int x, int y) {return x+y;}
// 普通函数可调用对象
using FUNC1 = int (*) (int, int);

// 类成员函数
class MyMath{
public:
    int add(int x, int y) {
        return x + y;
    }
    // 类静态函数
    static int add1(int x, int y) { return x + y;}
    // 仿函数
    int operator()(int x, int y) {
        return x + y;
    }
};
using FUNC2 = int (MyMath::*)(int, int);

using FUNC3 = int (*)(int, int);

using FUNC4 = int (MyMath::*)(int, int);

// 可被转换为函数指针的类对象
using FUNC = int (*)(int, int);
class MyClass{
public:
    operator FUNC() {
        return add;
    }
    static int add(int x, int y) {
        return x + y;
    }
};

// lambda表达式作为可调用对象
using LAMBDA = int (*)(int, int);

int main() {
    FUNC1 f1 = func1; // 获取普通函数指针
    std::cout << f1(2,2) << std::endl;

    MyMath m;
    FUNC2 f2 = &MyMath::add; // 获取成员函数指针
    auto x = (m.*f2)(10, 20);  // 调用成员函数
    std::cout << x << std::endl;

    FUNC3 f3 = MyMath::add1; // 获取静态函数指针
    std::cout << f3(3, 4) << std::endl;  // 调用静态函数

    MyMath m1;
    // std::cout << m1(1, 2) << std::endl; 
    FUNC4 f4 = &MyMath::operator(); // 获取仿函数指针
    std::cout << (m1.*f4)(3, 5) << std::endl;  // 调用仿函数

    MyClass mm;
    FUNC f5 = mm; // 获取可被转换为函数指针的类对象
    std::cout << f5(4, 6) << std::endl;  // 调用类对象作为函数指针
  
    LAMBDA l = [](int x, int y) { return x + y; }; // lambda表达式作为可调用对象
    std::cout << l(7, 8) << std::endl;  // 调用lambda表达式
    return 0;
}
```

# function 和 bind

## function

std::function是函数包装器模板，能包装任何类型的可调用元素

std::function类型对象实例可包装成：函数、函数指针、类成员函数指针或者任意类型的函数对象

[理解C++ std::function灵活性与可调用对象的妙用](https://zhuanlan.zhihu.com/p/686240649)

### 作用实现接口统一

```c++
int add(int a, int b) {
    return a + b;
}
int sub(int a, int b) {
    return a - b;
}

void func(std::function<int(int, int)> f, int a, int b)
{
    f(a, b);
}

int main() {
    // std::function<int(int, int)> f = add;
    // std::cout << f(2, 3) << std::endl; // Output: 5

    // f = sub;
    // std::cout << f(2, 3) << std::endl; // Output: -1

    func(add, 2, 3); // Output: 5
    func(sub, 2, 3); // Output: -1

    std::map<std::string, std::function<int(int, int)>> map{
        {"+", add}, {"-", sub},
        {"*",[](int x, int y){return x*y;}}
    };
    std::cout << map["+"](2, 3) << std::endl; // Output: 5
    return 0;
}
```

## bind

bind函数看作是一个通用的函数适配器，接受一个可调用对象，生成新的可调用对象来适应原对象的参数列表

### bind绑定普通函数

```c++
void print(const std::string& s1, const std::string& s2)
{
    std::cout << s1 << " " << s2 << std::endl;
}

void func(std::function<void(const std::string&)>) {}

int main() {
    std::string s1 = "Hello";
    std::string s2 = "World";
    std::string s3 = "!";
    std::function<void()> f = std::bind(print, s1, s2);
    f(); // Output: Hello World
  
    // 占位符形式
    std::function<void(const std::string&)> f1 = std::bind(print, std::placeholders::_1, s2);
    f1(s3);

    auto f2 = bind(print, std::placeholders::_1, s2);
    func(f2); // Output: Hello World
    return 0;
}
```

### bind绑定成员函数

```c++
class MyMath{
public:
    MyMath()
    {
        std::cout << "默认构造函数" << std::endl;
    }
    MyMath(const MyMath& math) {
        std::cout << "默认拷贝函数" << std::endl;
    }
    int add(int a, int b) {
        return a + b;
    }
};
    // bind可调用对象的类型必须与函数签名匹配
    MyMath myMath; // 作引用，否则为拷贝对象
    int a = 10;
    int b = 20;
    std::function<int(int)> f3 = std::bind(&MyMath::add, myMath, std::placeholders::_1, b); // 占位符形式
    std::cout << f3(2) << std::endl; // Output: 30
    std::function<int()> f4 = std::bind(&MyMath::add, &myMath, a, b);
    std::cout << f4() << std::endl; // Output: 30
```

### bind绑定类静态成员函数

```c++
class MyMath{
public:
    // 类静态成员函数
    static int sub(int a, int b) {
        return a - b;
    }
};
using FUNC2 = int(*)(int, int);

    FUNC2 f5 = MyMath::sub;
    std::cout << f5(a, b) << std::endl; // Output: -10

    std::function<int(int)> f6 = std::bind(MyMath::sub, std::placeholders::_1, b);
    std::cout << f6(a) << std::endl; // Output: -10
```

bind预先绑定的参数需要传具体的变量或值，值传递，对事先不绑定的参数，需要传std::placeholders::_1，其为引用传递

## std::ref

bind对参数直接拷贝，无法传入引用，std::ref在模板传参时传入引用

```c++
void func(int& a) {
    a++;
    std::cout << "a = " << a << std::endl;
    std::cout << "address of a = " << &a << std::endl;
}
int main() {
    int a = 10;
    auto f = std::bind(func, a);
    // auto f = std::bind(func, std::ref(a));
    f();
    std::cout << "a = " << a << std::endl;
    std::cout << "address of a = " << &a << std::endl;
    return 0;
}
```

# 函数指针和std::function
