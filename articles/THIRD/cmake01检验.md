检验
1. 解释用OpenCV输出一张红色图片的代码中的CMakeLists.txt每一行语句的含义；
```
#指定CMake最小版本号，小于此版本的无法通过
cmake_minimum_required(VERSION 3.10)

#定义项目名
project(test)

#查找名为 OpenCV 的包，找不到就报错（并终止 cmake 进程，不再继续往下执行）。
find_package(OpenCV REQUIRED)

#生成可执行文件test
add_executable(test test.cpp)

#给目标添加头文件的搜索路径，PUBILC的作用是，在其他目标链接当前目标test时，能访问到这些指定的头文件内容
target_include_directories(test PUBLIC ${OpenCV_INCLUDE_DIRS})

#绑定test依赖的项目外第三方目标库OpenCV_LIBS
target_link_libraries(test PUBLIC ${OpenCV_LIBS} )
```
2. 编译有哪几个步骤？分别在做什么事？
答：四个步骤，分别是预编译、编译、汇编、链接
    1. 预编译：展开宏定义和头文件
    2. 编译：将源代码翻译成汇编代码
    3. 汇编：将汇编代码再转换成机器代码的过程
    4. 链接：链接库文件，将生成的目标文件转化成可执行文件
1. 什么是静态库？什么是动态库？什么是接口库？
答：静态库是编译时就会直接整合到目标程序的函数库，动态库是编译时在程序里只有一个符号，而当可执行文件需要用到时才会被解析和重定位地址的函数库，接口库是只有头文件而没有源文件的库。
2. 结合#include的原理，说明*.h文件是怎么参与编译的；
答：在预编译阶段，编译器就会将#include的头文件.h替换成头文件.h里面的内容
3. target_link_library和link_library有什么不同？
答：target_link_libraries用来链接导入动态库，link_libraries用来链接静态库，所以前者在生成可执行文件之后，后者在生成可执行文件之前