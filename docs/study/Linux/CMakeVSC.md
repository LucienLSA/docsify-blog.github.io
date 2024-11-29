
## 资料来源

https://xbing.notion.site/VSCode-CMake-C-C-Linux-c330a94669a84c2480a59ba708fd4ece

## linux系统的常用指令和选项

见文档指令

## 开发环境搭建

安装编译器和调试器，安装CMake

安装之前先执行，将安装升级进行更新

```bash
sudo apt update
```

## GCC编译器

VSCode通过调用GCC编译器实现C/C++的编译
gcc指令编译C，g++指令编译C++

### 设置编译标准

使用c++11标准编译test.cpp

```bash
g++ -std=c++11 test.cpp
```

## GDB调试器

以下主要为命令行调试

VSCode通过调用GDB调试器实现C/C++的调试

主要功能：

- 设置断点
- 指定代码行暂停
- 单步执行程序
- 查看变量值的变化
- 分析崩溃程序产生的core文件

```bash
g++ sum.cpp -o a_no_g
g++ -g sum.cpp -o a_yes_g
```

执行gdb调试

```bash
gdb a_yes_g

# Type "apropos word" to search for commands related to "word"...
# Reading symbols from a_yes_g...
```

之后可执行gdb调试，查看调试的命令，如list continue break run

## VSCode执行Cpp文件

"ctrl `"  打开终端

### 多文件编译

不同文件级别下cpp文件

```bash
g++ main.cpp src/swap.cpp -Iinclude -o main
```

文件夹文件信息查询。x表示文件可执行，linux下可执行文件与windows下的不同。windows需要以后缀.exe结尾

```bash
ls -h
```

固定打开选项卡，直接双击，不会将打开的文件覆盖掉。单击时，打开下一个文件则会覆盖掉窗口

## CMake

Cmake是跨平台的安装编译工具，成为大部分开源项目标配

使用Cmake增加CMakeLists.txt，能对不同平台进行整合编译

1. 基础语法：指令(参数1 参数2)
2. 指令是大小写无关，而参数和变量大小写有关

注意：变量使用${}方式取值，但在IF语句中是直接使用变量名

### 重要指令和常用变量

1. 重要指令

- cmake_minimum_required 指定CMake的最小版本要求
- project 定义工程名称，可指定工程支持得语言
- set 显式的定义变量，如定义SRC变量，值为main.cpp hello.cpp
- include_directories 向工程添加多个特定的头文件搜索路径 *相当于指定g++编译器的-I参数*
- link_direcetories 向工程添加多个特定的库文件搜索路径 *相当于指定g++编译器的-L参数*
- add_library 生成库文件，如通过变量SRC生成libhello.so共享库
- add_compile_options 添加编译参数，如添加-wall -std=c++11 -O2
- add_executable 生成可执行文件，如编译main.cpp 生成可执行文件main
- target_link_libraries 为target添加需要链接的共享库 *相当于指定g++编译器-l参数*，将hello动态库文件链接到可执行文件main
- add_subdirectory 向当前工程添加存放源文件的子目录，并可指定中间二进制和目标二进制存放的位置
- aux_source_directory 发现一个目录下所有的源代码文件并将列表存储在一个变量中，临时被用来自动构建源文件列表

2. 常用变量

- CMAKE_CXX_FLAGS g++编译选项，如在编译选项后追加-std=c++11
- CMAKE_BUILD_TYPE 编译类型(Debug Release)

### CMake编译工程

目录结构：主目录存在一个CMakeList.txt 文件

### 设置编译规则

1.包含源文件的子文件夹包含CMakeLists.txt文件，主目录的CMakeLists.txt通过add_subdirectory添加子目录即可;
2.包含源文件的子文件夹未包含CMakeLists.txt文件，子目录编译规则体现在主目录的CMakeLists.txt中;

### 编译流程

1. 手动编写CMakeList.txt
2. 执行命令cmake PATH 生成Makefile（PATH是顶层CMakeList.txt所在目录）
3. 执行命令make

### 外部构建

编译输出文件与源文件放在不同目录

```bash
# 1. 当前目录，创建build文件夹
mkdir build
# 2. 进入build 文件夹
cd build 
# 3. 编译上级目录的CMakeList.txt 生成Makefile和其它文件
cmake ..
# 4. 执行make命令，生成target
make
```

## 实践案例

修改完CMakeLists.txt需要保存，再编译

### 1 helloworld

CMakeLists.txt文件

```cmake
cmake_minimum_required(VERSION 3.0) 

project(HELLOWORLD)

add_executable(helloWorld_cmake helloworld.cpp)
```

执行可执行文件

```cmake
./ helloWorld_cmake
```

### 2 swap

CMakeLists.txt文件

```cmake
cmake_minimum_required(VERSION 3.0)

project(SWAP)

include_directories(include)

add_executable(main_cmake main.cpp src/swap.cpp)
```

执行可执行文件

```cmake
./main_cmake
```

## 完整实战C++

### 编写源文件

### 编译CMake项目

完成外部构建后，进行编译运行。如果还需要修改代码，则修改代码文件后，需要保存文件，再执行make，再可执行文件

```cmake
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
set(CMAKE_BUILD_TYPE Debug)
```

### 配置json并调试项目

1. 创建项目Solider的launch.json文件
```json
{
    "version": "0.2.0",
    "configurations": [

        {
            "name": "g++ - 生成和调试活动文件",
            "type": "cppdbg", 
            "request": "launch",
            "program": "${workspaceFolder}/build/my_cmake_exe", // 调试的可执行文件
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                },
                {
                    "description": "将反汇编风格设置为 Intel",
                    "text": "-gdb-set disassembly-flavor intel",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "Build",
            "miDebuggerPath": "/usr/bin/gdb"
        }

    ]
}
```

2. 创建task.json文件
```json
{   
    "version": "2.0.0",
    "options": {
        "cwd": "${workspaceFolder}/build"
    },
    "tasks": [
        {
            "type": "shell",
            "label": "cmake",
            "command": "cmake",
            "args": [
                ".."
            ]
        },
        {
            "label": "make",
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "command": "make",
            "args": [

            ]
        },
        {
            "label": "Build",
			"dependsOrder": "sequence", // 按列出的顺序执行任务依赖项
            "dependsOn":[
                "cmake",
                "make"
            ]
        }
    ]
}
```

launch.json中的"preLaunchTask":"Build"  与 task.json中的"label":"Build"  是一一对应的

理解：在launch之前完成，执行"cmake"和"make"，即对应的命令

如果不执行"preLaunchTask":"Build" ，则调试异常，直接输出运行结果
