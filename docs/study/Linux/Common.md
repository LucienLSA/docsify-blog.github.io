
## vim卡死，或者不能输入的原因

有些时候使用vim的时候莫名其妙的会卡死，导致输入不了内容。在使用vim的工程中带入了许多Windows上的使用习惯，比如“Ctrl-s”保存等，这可能会与Linux平台的有些快捷键冲突。ctrl-s在windows上的编辑器是默认保存，但是在Linux，却是禁止在命令行（控制台）输入内容，所以这时候就表现为卡死（没有任何提示），解决的方案也好办“ctrl-q”

再着就是在配置vim的时候不要配置"no swapfile",有人说：" 以前计算机经常崩溃，vim 会自动创建一个 .swp 结尾的文件， 崩溃重启后可以从 .swap 文件恢复 ， 现在计算机鲜少崩溃了，可以禁用此功能“ 但是Linux可能是通过ssh，或者其他手段远程连接上去的，不确定性因素很多，不要配置"no swapfile"，即使中途连接断掉，编辑的文件还可以找回来。

## 静态库与动态库的区别与优缺点

一、什么是库
库是写好的，现有的，成熟的，可以复用的代码。本质上来说，库是一种可执行代码的二进制形式，可以被操作系统载入内存执行。库有两种：静态库（.a、.lib）和动态库（.so、.dll）。所谓静态、动态是指链接。

库文件是事先编译好的方法的合集。

二、静态库与动态库的区别
1、静态库的扩展名一般为“.a”或“.lib”；动态库的扩展名一般为“.so”或“.dll”。

2、静态库在编译时会直接整合到目标程序中，编译成功的可执行文件可独立运行；动态库在编译时不会放到连接的目标程序中，即可执行文件无法单独运行。

静态库和动态库最本质的区别就是：该库是否被编译进目标（程序）内部。

静态（函数）库
一般扩展名为（.a或.lib）,这类的函数库通常扩展名为libxxx.a或xxx.lib 。

这类库在编译的时候会直接整合到目标程序中，所以利用静态函数库编译成的文件会比较大，这类函数库最大的优点就是编译成功的可执行文件可以独立运行，而不再需要向外部要求读取函数库的内容；但是从升级难易度来看明显没有优势，如果函数库更新，需要重新编译。

动态（函数）库
动态函数库的扩展名一般为（.so或.dll），这类函数库通常名为libxxx.so或xxx.dll 。

与静态函数库被整个捕捉到程序中不同，动态函数库在编译的时候，在程序里只有一个“指向”的位置而已，也就是说当可执行文件需要使用到函数库的机制时，程序才会去读取函数库来使用；也就是说可执行文件无法单独运行。这样从产品功能升级角度方便升级，只要替换对应动态库即可，不必重新编译整个可执行文件。

三、静态库与动态库优缺点
1、静态库
优点：

①静态库被打包到应用程序中加载速度快
②发布程序无需提供静态库，移植方便

缺点：

①相同的库文件数据可能在内存中被加载多份，消耗系统资源，浪费内存
②库文件更新需要重新编译项目文件，生成新的可执行程序，浪费时间。

2、动态库
优点：

①可实现不同进程间的资源共享
②动态库升级简单，只需要替换库文件，无需重新编译应用程序
③可以控制何时加载动态库，不调用库函数动态库不会被加载

缺点：

①加载速度比静态库慢
②发布程序需要提供依赖的动态库

## LF和CRLF的区别

* `LF`："\n"，Linux的换行符；
* `CRLF`："\r\n"，Windows的换行符

## rm-rf 强制永久删除

## ps -ef 查询进程

查看wget的后台进程，pid为5543

```bash
ps-ef|grep
```

## 检查上一条命令执行是否可行

特殊变量 $?

上次命令执行失败，$?就是非0的错误状态码，上次命令正确执行，则输出为0。

如检测一个不存在的网址

## 解压和压缩命令

(linux tar 解压命令总结)[https://blog.csdn.net/imyang2007/article/details/7634470]

### 报错 tar (child): bzip2：

(tar (child): bzip2：无法 exec: 没有那个文件或目录)[https://blog.csdn.net/qq_35344821/article/details/89302416]

使用gcc编译出现的报错

1. 动态库

   (Linux下修改可执行程序或者库的动态链接库的路径)[https://blog.csdn.net/Youning_Yim/article/details/126582927]

   (Linux动态库查询路径设置(LD_LIBRARY_PATH))[https://blog.csdn.net/flysnow010/article/details/140101682?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-3-140101682-blog-126582927.235%5Ev43%5Epc_blog_bottom_relevance_base8&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EYuanLiJiHua%7EPosition-3-140101682-blog-126582927.235%5Ev43%5Epc_blog_bottom_relevance_base8&utm_relevant_index=6]
2. gdb提示Missing separate debuginfos, use: debuginfo-install glibc-2.17-325.el7_9.x86_64...

   (gdb提示Missing separate debuginfos, use: debuginfo-install glibc-2.17-325.el7_9.x86_64...)[https://blog.csdn.net/hope18578/article/details/122384730]
3. 反斜杠和换行为空格所分隔

   (删除换行符后的空白字符)[https://blog.csdn.net/u012258978/article/details/73136245]
4. 段错误调试

   (段错误以及调试方法)[https://blog.csdn.net/qq_42519524/article/details/119608102#:~:text=%E6%AE%B5%E9%94%99%E8%AF%AF%E5%B0%B1%E6%98%AF%E8%AE%BF%E9%97%AE%E4%BA%86%E4%B8%8D%E5%8F%AF%E8%AE%BF%E9%97%AE%E7%9A%84%E5%86%85%E5%AD%98%EF%BC%8C%E5%87%BA%E7%8E%B0%E4%BA%86%E8%BF%90%E8%A1%8C%E6%97%B6%E5%87%BA%E7%8E%B0%E4%BA%86%20segmentation%20fault,%E7%9A%84%E6%8A%A5%E9%94%99%20%E4%BA%A7%E7%94%9F%E7%9A%84%E5%8E%9F%E5%9B%A0%EF%BC%9A%E8%AE%BF%E9%97%AE%E4%B8%8D%E5%AD%98%E5%9C%A8%E7%9A%84%E5%86%85%E5%AD%98%E5%9C%B0%E5%9D%80%E3%80%81%E8%AE%BF%E9%97%AE%E7%B3%BB%E7%BB%9F%E4%BF%9D%E6%8A%A4%E7%9A%84%E5%86%85%E5%AD%98%E5%9C%B0%E5%9D%80%20%E3%80%81%E8%AE%BF%E9%97%AE%E5%8F%AA%E8%AF%BB%E7%9A%84%E5%86%85%E5%AD%98%E5%9C%B0%E5%9D%80%E3%80%81%E7%A9%BA%E6%8C%87%E9%92%88%E5%BA%9F%E5%BC%83%EF%BC%88eg%3Amalloc%E4%B8%8Efree%E9%87%8A%E6%94%BE%E5%90%8E%EF%BC%8C%E7%BB%A7%E7%BB%AD%E4%BD%BF%E7%94%A8%EF%BC%89%E3%80%81%E5%A0%86%E6%A0%88%E6%BA%A2%E5%87%BA%E3%80%81%E5%86%85%E5%AD%98%E8%B6%8A%E7%95%8C%EF%BC%88%E6%95%B0%E7%BB%84%E8%B6%8A%E7%95%8C%EF%BC%8C%E5%8F%98%E9%87%8F%E7%B1%BB%E5%9E%8B%E4%B8%8D%E4%B8%80%E8%87%B4%E7%AD%89%EF%BC%89]

(Linux上段错误(SegFault)的9种实用调试方法)[https://blog.csdn.net/xzh203/article/details/135548102?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-1-135548102-blog-119608102.235^v43^pc_blog_bottom_relevance_base6&spm=1001.2101.3001.4242.2&utm_relevant_index=4]

5. gdb调试

```

0x00007ffff7874a4a in __GI__IO_str_overflow () from /lib64/libc.so.6

(gdb) n

Single stepping until exit from function __GI__IO_str_overflow,

which has no line number information.


Program terminated with signal SIGSEGV, Segmentation fault.


```
