
## c++11 Thead线程库的基本使用

### 什么是进程

运行中的程序

### 什么是线程

进程中的进程 ，一个进程里有多个线程

## 程序报错

VScode 在使用多线程类#include`<thread>`的时候出现"htread is not a member of 'std' "

这是因为MinGW没有thread类，对于跨平台的线程实现，GCC标准库似乎依赖于gthreads/pthreads库。如果这个库不可用，就像MinGW一样，std::thread、std::mutex、std::condition_variable类不会被定义。然而，各种可用的helper类仍然定义在系统头文件中。因此，这个实现没有重新定义它们，而是包含了这些头文件

原文链接：[“thread is not a member of ‘std‘ “](https://blog.csdn.net/m0_46434334/article/details/128977254)

### 创建进程

#### join()

主线程等待子线程执行完毕

检查线程有没有执行return，如果没结束，则主线程不结束。等待子线程结束后，再执行主线程，不是等待所有线程

join是阻塞的

线程函数传递参数，在函数的后面紧接着传递函数

```cpp
std::thread thread1(printHelloWorld, "Hello Thread");
```

#### detach()

主线程和子线程分离

线程仍是靠主进程的栈分配的空间，detach()分离，是提前给内核标记，等到主进程结束时同时结束线程，后台运行子线程，main()结束后，会被销毁

#### joinable()

判断线程是否使用join()和detach()

```c++
void printHelloWorld(std::string msg) {
    // std::cout << msg << std::endl;         
    return;
}

int main() {
    // 1. 创建线程
    std::thread thread1(printHelloWorld, "Hello Thread");
    bool isJoin = thread1.joinable(); 
    if (isJoin) {
        thread1.join(); // join是阻塞的
    }
    // system("pause");
    return 0;
}
```

## 线程函数中数据未定义错误

### std::ref

报错：note:   substitution of deduced template arguments resulted in errors seen above

右值传递给左值引用，报错

```
通过使用引用来替代指针，会使 C++ 程序更容易阅读和维护。C++ 函数可以返回一个引用，方式与返回一个指针类似。

当函数返回一个引用时，则返回一个指向返回值的隐式指针。这样，函数就可以放在赋值语句的左边。
```

c++线程函数的传参方式为值传递，&传引用类型，线程函数拿到的是赋值该引用指向的数据值类型

```c++
void foo(int& x) {
    x += 1;
}
int main() {
    int a = 1;
    std:: thread t(foo, std::ref(a));
    t.join();

    return 0;
}
```

### 传递指针或引用指向局部变量

如果没有报错，先进入线程后释放局部变量，随机的

```c++
std::thread t1;
void test() {
    int b = 1; // 局部变量被释放掉
    t1 = std::thread(foo, std::ref(b));
    std::cout << b << std::endl;
}
int main() {
    test();  
    t1.join();
    return 0;
}
```

解决方案：设置全局变量

### 访问已经销毁的指针 或引用指向局部变量

申请的内存传递给指针，delete将地址释放

```c++
std::thread t3
void foo1(int *x) {
    std::cout << *x << std::endl;
    // *x += 1;
}
int main() {
    int* ptr = new int(1);
    std::thread t3(foo1, ptr);
    delete ptr;
    ptr = NULL;
    t3.join();
    return 0;
}
```

解决：将delete放在join()函数之后，将其变量销毁

总之就是在线程结束之前，线程中的变量都不能被销毁！！

### 类成员函数作为入口函数，类对象被提前释放

解决方案：引入智能指针shared_ptr，智能指针的类不再被需要，则会调用析构函数自动将内存释放掉。指向的地址一直是有效的

在C++中，当使用std::thread创建线程并传递类成员函数时，需要使用&来获取成员函数的地址，同时还需要传递对象的指针（或引用）作为第一个参数。

```c++
class A {
public:
    void foo() {
        std::cout << "Hello" << std::endl;
    }
};

int main() {
    std::shared_ptr<A> a = std::make_shared<A>();
    std::thread t1 (&A::foo, a);

    t1.join();
    return 0;
}
```

### 入口函数为类的私有成员函数

使用友元，构造线程的友元函数，进行访问私有成员

```c++
class A {
private:
friend void thread_foo();
    void foo() {
        std::cout << "Hello" << std::endl;
    }
};

void thread_foo() {
    std::shared_ptr<A> a = std::make_shared<A>();
    std::thread t1 (&A::foo, a);
    t1.join();
}

int main() {
    thread_foo();
    return 0;
}
```

## 互斥量解决多线程数据共享问题

多个线程共享数据，线程安全问题。如果多个线程同时访问同一个变量，并且其中至少有一个线程对该变量进行写操作，出现数据竞争问题。数据竞争可能会导致程序崩溃，产生未定义结果，或得到错误的结果

为避免数据竞争问题，需要同步机制确保多个线程之间对共享数据的访问是安全的，常见同步机制包括互斥量、条件变量、原子操作

### 互斥量

lock()加锁

锁住是互斥量mtx，访问a前，先确定mtx有没有解释，没解锁就等待。lock本质是先判断是否上锁，如果上锁就阻塞，没有上则进行锁后面的操作，从而保证互斥访问共享变量

lock和unlock的原理是主动创建阻塞区别，防止多个线程访问同一个变量

多线程程序每一次的运行结果和单线程运行的结果始终是一样的，那就是线程是安全

```c++
int a = 0;
std::mutex mtx;

void func() {
    mtx.lock();
    for(int i=0; i<10000; i++) {  
        a += 1; 
    }
    mtx.unlock();
}

int main() {
    std::thread t1 (func);
    std::thread t2 (func);

    t1.join();
    t2.join();

    std::cout << a << std::endl;
    return 0;
}
```

## 互斥量死锁

假设两个线程T1和T2，需要对两个互斥量mtx1和mtx2进行访问，按顺序获取互斥量的所有权

- T1先获取mtx1的所有权，再获取mtx2的所有权
- T2先获取mtx2的所有权，再获取mtx1的所有权
  两个线程同时进行，出现死锁，T1获取mtx1的所有权，但无法获取mtx2的所有权，两个线程互相等待对方释放互斥量，导致死锁

```c++
std::mutex m1, m2;

void func1() {
    Sleep(10000); // 加入延迟函数
    m1.lock();
    m2.lock();
    m1.unlock();
    m2.unlock();
}

void func2() {
    Sleep(10000);
    m2.lock();
    m1.lock();
    m1.unlock();
    m2.unlock();
}

int main() {
    std::thread t1(func1);
    std::thread t2(func2);

    t1.join();
    t2.join();

    std::cout << "over" << std::endl;
    return 0;
}
```

### 解决死锁问题

对互斥量的顺序锁，即T1和T2同时对获取mtx1的所有权，再对获取mtx2的所有权，相当于同一时刻只有一个线程

## lock_guard与unique_lock

### std::lock_guard互斥量封装类，用于保护共享数据，防止多个线程同时访问同一资源而导致的数据竞争问题

1. 构造函数被调用，互斥量会被自动锁定
2. 析构函数被调用，互斥量会被自动解锁
   std::lock_guard对象不能复制或移动，只能在局部作用域中使用

```c++
int shared_data = 0;
std::mutex mtx;
void func() {
    std::lock_guard<std::mutex> lg(mtx);
    for(int i=0; i<10000; i++) {  
        shared_data++; 
    }
}
int main() {
    std::thread t1 (func);
    std::thread t2 (func);

    t1.join();
    t2.join();

    // std::cout << "over" << std::endl;
    std::cout << shared_data << std::endl;
    return 0;
}
```

### std::unique_lock 互斥量封装类，多线程程序中对互斥量进行加锁和解锁操作。对互斥量灵活管理，包括延迟加锁、条件变量、超时等

#### 延迟加锁

timed_mutex时间锁

正常情况下，获取不到锁的所有权，阻塞一直等待，try_lock_for只等待一段时间，超时则不等，继续执行

```c++
int shared_data = 0;
std::timed_mutex mtx;

void func() {

    for(int i=0; i<2; i++) {  
        // std::lock_guard<std::mutex> lg(mtx);
        std::unique_lock<std::timed_mutex> lg(mtx,std::defer_lock);
        if (lg.try_lock_for(std::chrono::seconds(2))) { // 加锁只等待2秒
            std::this_thread::sleep_for(std::chrono::seconds(4)); // 线程休眠4秒
            shared_data++; 
        }
    }
}
int main() {
    std::thread t1 (func);
    std::thread t2 (func);

    t1.join();
    t2.join();

    std::cout << shared_data << std::endl;
    return 0;
}
```

一个锁只有一个对象所有权，移动可把其转化为右值，锁的所有权转移到别的地方，节约了构造对象的资源

## call_once和单例模式

### 单例模式

确保某个类只能创建一个实例，单例全局唯一。

1. 构造函数是private
2. instance成员是static的原因是该成员就表示该类的实例，保证该成员是属于这个类而不是属于类的各个对象，始终只存在这一个；

饿汉模式：不管用不用得到，都构造出来。本身就是线程安全的。饿汉式即一种静态初始化的方式，它是类一加载就实例化对象，所以要提前占用系统资源

懒汉模式：只有用到了才实例化对象并返回（调用了对外的接口才会实例化对象）

```c++
class Log {
public:
    static Log& GetInstance() { // 静态方法
        // static Log log; // 全局只有一个 饿汉模式
        static Log *log = nullptr; // 懒汉模式
        if (!log) log = new Log; 
        return *log; 
    }

    void PringLog(std::string msg) {
        std::cout << __TIME__ << msg << std::endl;
    }
private:
    Log(){};
};
int main() {
    Log::GetInstance().PringLog(" error ");
    Log1(const Log1& log1) = delete; // 拷贝构造禁用
    Log1 &operator=(const Log1 &log1) = delete; // 禁用等号运算符重载
}
```

### call_once

静态局部变量只会被初始化依次，确保单例实例只会被创建一次，但是并不是线程安全的，如果多个线程同时调用getInstance()函数，导致多个对象被创建，违反单例模式要求，使用call_once实现一次性初始化，确保单例实例只被创建一次

本质：封装了加锁的过程，once_flag就是一个全局变量标识符，call_once的时候会先判断这个标志符是不是为true，如果为true就加锁执行，同时修改标识符为false，结束后释放锁mutex。等到其他线程看到标志符为false就不再执行call_once。

只能在线程函数中调用

```c++
class Log1;
static Log1* log1 = nullptr;
static std::once_flag once;

class Log1 {
public:
    static Log1& GetInstance() {
        std::call_once(once, init);
        return *log1;
    }
  
    static void init() {
        if (!log1) log1 = new Log1;
    }

    void PringLog(std::string msg) {
        std::cout << __TIME__ << msg << std::endl;
    }
private:
    Log1(){};
    Log1(const Log1& log1) = delete; // 拷贝构造禁用
    Log1 &operator=(const Log1 &log1) = delete; // 禁用等号运算符重载
};

void print_error() {
    Log1::GetInstance().PringLog(" error ");
}

int main() {
    std::thread t1(print_error);
    std::thread t2(print_error);
    t1.join();
    t2.join();
    return 0;
}
```

### condition_variable

#### 生产者与消费者模型

1. 生产者生产内容。
2. 消费者消费内容。
3. （阻塞）缓冲区的大小有限制（超过限制，生产者暂停生产），反之就一直等待。
4. （阻塞）只有缓冲区有内容的时候，消费者才可以进行消费，反之就一直等待。
5. 缓冲区需要加锁，才能保证线程安全。
6. 有结束条件 —— 生产n份产品并消费n份产品之后就停止。

```c++
std::queue<int> g_queue; // 创建队列
std::condition_variable g_cv; // 条件变量
std::mutex mtx;

void Producer() {
    for (int i = 0 ; i < 10 ; i++) {
        { // 大括号局部作用域，使得unique_lock析构释放掉，不加大括号会导致全部生产完才会加锁
            std::unique_lock<std::mutex> lock(mtx);
            g_queue.push(i); // 加任务
            // 通知消费者取任务
            g_cv.notify_one(); // 一个任务可取
            std::cout << "Produce: " << i << std::endl;
        }
        std::this_thread::sleep_for(std::chrono::microseconds(1000));
    }
}

void Consumer() {
    while (1) 
    {
        // 共享变量取和放，需要锁
        std::unique_lock<std::mutex> lock(mtx);
        // bool isempty = g_queue.empty();
        // // 如果队列为空，就要等待
        // g_cv.wait(lock, !isempty);
        g_cv.wait(lock, [](){return !g_queue.empty();});
        int value = g_queue.front(); // 从任务队列中取任务
        g_queue.pop(); //完成后弹出
        std::cout << "Consumer: " << value << std::endl;
    }  
}
    // std::thread t1(Producer);
    // std::thread t2(Consumer);
    // t1.join();
    // t2.join();
```

生产者for循环的大括号，对于sleep_for()函数与队列无关，不需要上锁；而上锁是为了生产者和消费者互斥使用队列

#### c++11的std::ref用法

[https://murphypei.github.io/blog/2019/04/cpp-std-ref](std::ref)

C++11 中引入 std::ref 用于取某个变量的引用，考虑函数式编程（如 std::bind）在使用时，是对参数直接拷贝，而不是引用。

说明 std::bind 使用的是参数的拷贝而不是引用，因此必须显示利用 std::ref 来进行引用绑定。具体为什么 std::bind 不使用引用，可能确实有一些需求，使得 C++11 的设计者认为默认应该采用拷贝，如果使用者有需求，加上 std::ref 即可。

### c++跨平台线程池

线程开辟和销毁非常销毁时间，提前开辟好一堆线程专门等着任务进来，执行任务，可提高效率。维护一堆线程数组和任务队列

见文件thread_pool.cpp

### 异步并发-async future packaged_task promise

#### async、future

异步执行一个函数，并返回一个std::future对象，异步操作。std::async异步编程，避免手动创建线程和管理线程

```c++
int func() {
    int i = 0;
    for (i = 0; i < 1000; i++)
    {
        i++;
    }
    return i;
}
int main() {
    std::future<int> future_result = std::async(std::launch::async, func);
    std::cout << func() << std::endl;
    std::cout << future_result.get() << std::endl;
    return 0;
}
```

计算在另一个线程中执行，不阻塞主线程，通过get()函数获取结果。

#### packaged_task

类模板，将一个可调用对象（函数、函数对象、lambda表达式）封装成一个可调用的对象，并提供异步执行的接口，并返回一个future对象。

```c++
int func() {
    int i = 0;
    for (i = 0; i < 1000; i++)
    {
        i++;
    }
    return i;
}

int main() {
    std::packaged_task<int()> task(func);
    auto future_result = task.get_future();
    std::thread t1(std::move(task)); // 可移动对象，使用move转移 手动开启线程
    std::cout << func() << std::endl;
    t1.join(); // 等待线程结束
    std::cout << future_result.get() << std::endl;

    return 0;
}
```

将一个函数calculate封装成一个异步操作，在其他线程中执行

#### promise

类模板，在线程中产生一个值，另一个线程中获取这个值，与future和async配合使用。

```c++
void func2(std::promise<int> &f) {
    f.set_value(1000);
}
int main() {
    std::promise<int> f;
    auto future_result2 = f.get_future();
    std::thread t2(func2, std::ref(f)); // 引用传递
    t2.join();
    std::cout << future_result2.get() << std::endl;
    return 0;
}
```

future对象的get()方法获取promise对象设置的值，并输出到控制台

#### 原子操作

提供线程安全的方式访问和修改共享变量，避免线程环境中的数据竞争问题，

```c++
// 原子操作
std::atomic<int> shared_data;
void func(std::atomic<int>& shared_data) {
    for (int i = 0; i < 1000000; ++i) {
        shared_data++;
    }
}
int main() {
    std::thread t1(func, std::ref(shared_data));
    std::thread t2(func, std::ref(shared_data));
    t1.join();
    t2.join();
    std::cout << "shared_data = " << shared_data << std::endl;
    return 0;
}
```
