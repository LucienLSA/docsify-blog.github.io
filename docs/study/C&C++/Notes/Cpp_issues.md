

# mingw64的版本问题
[https://blog.csdn.net/weixin_43455581/article/details/135692389](windows下编译报‘mutex‘ in namespace ‘std‘ does not name a type的解决方案)
```shell
error: ‘mutex’ in namespace ‘std’ does not name a type

error: ‘mutex’ is not a member of ‘std’

error: ‘mutex’ was not declared in this scope
```

mutex跟多线程挂钩，经检查，本地MinGW版本为x86_64-8.1.0-release-win32-seh-rt_v6-rev0，而win32版本是没有 C++11 多线程特性的，要想支持C++11多线程特性，可改用posix版本

## 下载不同posix版本的mingw

## 另外seh和sjlj
[https://blog.csdn.net/xxzhaoming/article/details/139998535](MinGW SEH 和 MinGW SJLJ 的区别)

建议使用 SJLJ，除非你明确知道需要使用 SEH。然而在现代系统上，通常更建议使用 SEH，因为它现在更为常见。实际上，你可以轻松地在两者之间切换，所以影响不大

[https://sourceforge.net/projects/mingw/files/MinGW/](MinGW - Minimalist GNU for Windows Files)

## 无法识别thread和mutex相关库
[https://blog.csdn.net/neverever01/article/details/107155542](Windows下C++使用thread时无法识别thread和mutex相关库的解决)

### 修改c++配置文件launch.json、tasks.json和c_cpp_properities.json
注意配置的步骤，选择对应的mingw版本，路径


# Path问题

## c++文件调试运行 Unable to start debugging

![Unable to start debugging](https://s2.loli.net/2024/04/03/6oKmCeawpBSIZ2A.png)

不需要打开 `launch.json` 查看文件的路径是否有中文，将其改为英文即可

# 提前建立或者已存在的txt文本，输出为乱码

原txt文本的编码可能为其它，如UTF-8

![1](https://s2.loli.net/2024/04/03/3dMYGs7xXkKBQEA.png)

![2](https://s2.loli.net/2024/04/03/IE8W7qtTuHiZvD1.png)

![3](https://s2.loli.net/2024/04/03/6E94idm2ZhbVARQ.png)

## 解决方式
将保存的编码格式修改为ANSI

# vscode配置
转载链接

[配置C++开发环境](https://gitbookcpp.llfc.club/sections/cpp/section1/cpp02.html)

### 注意配置launch.json和task.json。可自定义launch.json，在调试时选择相应的调试配置。

在args中添加了src文件目录，这样就可以编译多个cpp文件 同时设置label为"task g++",这个和lauch.json中preLaunchTask 对应 options 设置cwd为自己的mingw路径 再次运行，可以看到弹出对话框显示程序执行结果。

### 可将inc文件夹下为.h文件 src文件夹下为.cpp文件

故将多个文件放在.cpp中，在main.cpp中执行主函数，多个cpp文件编译

# 万能头文件
```cpp
#include <bits/stdc++.h>
```
1. 在算法竞赛中，它可以省去大量时间，不必编写所有必需的头文件。
2. 减少了繁琐的头文件引入工作。

然而，它也存在一些不足之处：
1. 并非GNU C++库的标准头文件，可能在某些情况下会导致编译失败。
2. 包含了很多不必要的内容，可能显著增加编译时间。


# 终端运行C++程序时,出现中文乱码
[https://blog.csdn.net/helloword233/article/details/112610841](解决使用VS Code终端运行C++程序时,出现中文乱码现象)


# expected declaration or statement at end of input
报错信息：expected declaration or statement at end of input

可能问题是：
    1）括号未对应起来；
    2）缺少头文件；
