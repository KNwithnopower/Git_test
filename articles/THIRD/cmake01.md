# 01 基本介绍
1. 在main.cpp同级目录下创建CMakeLists.txt文件,写入
    ```
    # 指定CMake的最小版本号
    cmake_minimum_required(VERSION 3.16)
    
    #创建项目
    project(
        cmake_01
    )
    
    #创建可执行文件
    add_executable(
        cmake_01
        main.cpp
    )
    ```
2. 然后创建build文件，打开后在终端里
    ```
    cmake ..
    ```
# 02 CMake 文件在项目中的分布
1. 项目大不时，可以仅适用一个```CMakeLists.txt```来管理所有属性配置，但项目越大，其可读性、可维护性也会降低，所以要对特定的模块编写```CMakeLists.txt```,可参考opencv的CMakeLists.txt布局
   ```
    .
    ├── cmake
    ├── CMakeLists.txt
    ├── include
    │   └── CMakeLists.txt(用于全局包含的opencv.hpp的导出)
    ├── modules
    │   ├── CMakeLists.txt
    │   ├── core
    │   ├── include
    │   ├── src
    │   └── test
    ├── README.md
    └── samples
    ```
    > 每级模块的文件夹下都会存在一个```CMaistske.txt```
    * 最底层的```CMaistske.txt```为了管理该模块功能的编译、测试文件编译，以及提供指定的接口（可以是库，也可以是若干CMake变量）给外部。opencv中使用```ocv_add_module()```来添加模块，例如：
        ```
        #定义<opencv-path>/modules/core/CMakeLists.txt的内容
        ocv_add_module(core
                        OPTIONAL opencv_cudev
                        WRAP java objc python js
        )
        ```
    * Opencv4.x系列版本设置两级目录的CMakeLists.txt
2. 其余CMake文件
   > CMake 中除了解析CMakeLists.txt文件，还允许解析拓展名为cmake的文件，以下两种操作可对这类文件进行访问
      1. ```find_package()```
      2. ```include()```
   
   2.1. ```*.cmake```文件 
   
   2.2.``` *.in```文件

# 03 变量的设置与引用
> Cmake变量划分：

     1. 普通变量
     2. 缓存变量
     3. 环境变量
1. 普通变量
   具有自动存储期效果
   * 局部作用域
   * CMakelists.txt可以继承】父目录文件中定义的变量
   * 定义方法：
    ```
    set(val1 "aaa") # val1内容为1个值：aaa
    set(val2 "bb" bbb) # val2内容为2个值：bb，bbb
    set(val3 aa;bb cc)# val3内容为3个值，aa，bb，cc
    ```    
    > 1. 即使```bbb```不加```“”```，也能表示一个字符号串，因为CMake的变量始终为字符串类型
    > 2. 要么都加```“”```，要么都不加
    > 3. 变量内容可通过空格``` ```,或者```;```进行分隔，也可以混用  
    * 引用
    * 可以理解为取值操作```${xxx}```
    ```
    set(AA xy mn)
    set(BB ${AA} cc)# BB即xy mn cc
    set(CC "${BB}") # CC即xy mn cc
    ```    
    * 解除
    unset()即解除变量定义的功能，写在CMakelists.txt
    * 作用域提升
    > 在子目录CMaistske.txt里设置的变量能够在父目录CMakeLists.txt里生效，需要提升作用域
    ```
    set(xxx PARENT_SCOPE)
    ```
    例如，在项目根目录下的CMakeLists.txt里写下内容
    ```
    message(STATUS ""var1 = ${var1}")
    message(STATUS ""var2 = ${var3}")
    add_subdirectory(module)
    message(STATUS ""var1 = ${var1}")
    message(STATUS ""var2 = ${var3}") 
    ```
    在module模块文件对应的CMakeLists.txt里写入
    ```
    set(var1 "abc")
    set(var "def" PARENT_SCOPE)
    ```
    运行结果为：
    ```
    -- var1 =
    -- var2 =
    -- var1 =
    -- var2 = def
    ```
2. CMake变量
   1. 种类
      * 提供信息的变量，```CMAKE_BINARY_DIR```,```PROJECT_NAME```
      * 改变行为的变量,```CMAKE_BUILD_TYPE```
      * 描述系统的变量,```CMAKE_SYSTEM_PROCESSOR```
      * 控制构建的变量,```LIBRARY_OUTPUT_PATH```
      * 语言的变量,```CMAKE_CXX_STANDARD```
      * CTest与CPack的变量
   2. 定义 
       ```
        set(CMAKE_BUILD_TYPE Debug)
        ```
        在底层CMAKELISTS.txt里被设置，但在之后的CMakelists.txt里也能继续生效
3. 缓存变量
   相当于C/C++里的动态存储期变量
    1. 定义
        上文说到变量都是字符串类型，但在缓存变量中，可以解释成其他类型供cmake-gui工具显示
        1. option
            使用option是创建bool型缓存变量的一种方式
            ```
            option(ORT_WITH_CUDA "the library compile CUDA" OFF)
            ```
            只有两种状态：TRUE或者FALSE，或另一在表达：ON、OFF，在使用cmake-gui时还可以打勾
        2. 自定义缓存变量
            ```set(xxx CACHE)```是一般设置缓存变量方式
            ```
            set(OPENCV_MODULES_BUILD "" CACHE INTERNAL "Lists of OpenCV modules included into the build")
            ```
            INTERNAL效果与STRING
            效果大致相同
            但INTERNAL无法在CMake工具中显示，但STRING可以
    2. 引用
        ${XXX}会先搜索有无普通变量，若找不到才会继续搜索缓存变量，强制搜缓存则使用``` $CACHE{xxx}```
        ```
        set(val2 "bbb" CACHE STRING "Test String")
        
        message(STATUS $CACHE{val2}) #若val2不是缓存变量，则无值，若是则显示bbb
        ```
    3. 解除
        ```
        unset(OPENCV_WORLD_MODULES CACHE) 
        ```
  
4. 环境变量
   >与系统中的环境变量是一个概念，比如```LD_LIBRARY_PATH```,用```$ENV{xxx}```引用
5. 变量规范
   1. 设置规范
    OpenCV中红```ocv_add_module```内部一个条件控制语句的写法：
    ```
    if(...)
        #...
        set(__ocv_argn__ ${ADD_MODULE_ARGN})
        #...
        unset(__ocv_argn__)
        #...
    endif()
    ```
   2. 命名规范
      1. 普通变量：小写+下划线
      2. 缓存变量：大写+下划线
# 04 运算符和条件、循环语句
1. if
    ```
    if(aaa)
    #...
    elseif(bbb)
    #...
    else()
    #...
    endif()
    ```
    * **可嵌套**
   
2. 运算符
    BOOL类型变量的运算符
    1. 一元
       1. EXISTS
          >判断文件或目录是否存在，需要绝对路径
       2. IS_DIRECTORY
          >判断内容是否为文件夹，需绝对路径
       3. COMMAND
          >判断是否是命令、宏或者函数这类可被调用的对象
       4. DEFINED
          判断给定的变量是否存在，系统变量前要加ENV
       5. TARGET
           判断给定的名称是否是目标
    2. 二元
       1. EQUAL
        判断左右两个字符串或者变量是否相等
       2. STREQUAL
        判断左右字典需是否相等
       3. MATCHES按照正则表达式匹配，左为待匹配值，右为正则表达式
            ``` 
            if(NOT m MATCHES "^opencv_")
            ```
    3. 逻辑运算符
        ``` AND OR NOT```
        结合优先级：NOT>AND>OR

3. foreach
    1. 用法：    
        ```
        foreach(每个元素 ${列表1} ${列表2} …)
            循环体
        endforeach()
        ```
        ```
        foreach(subdir ${subdirs})
            if(EXISTS "${path}/${subdir}/CMakeLists.txt")
                list(APPEND paths "${path}/${subdir}")
        #...
        ```
        * ```${subdir}```不可带```""```
        * subdir诗歌普通变量，所以访问时也需要```${}```的写法
        ```
        foreach(mod ${} ${} ${} ${})
        ```
        * 四个待遍历列表，会将所有内容一次访问
        ```
        unset(OPENCV_MODULE_${mod}_DEPS CACHE)
        ```
        当${mod}为其他变量时，就等效于将该变量替换进去的表达式
        > 注意：endif()和endeach()括号内内容可以不写，一旦写了，就必须和if()和foreach（）括号内容一致
    2. 其他写法
        ```
        foreach(m IN LIST members)
        #...
        endforeach()
        ```

4. 示例
   1. 执行子进程：```execute_process```
       * ```COMMAND```
       * ```OUTPUT_VARIABLE```
       * ```ERROR_VARABLE```
       * 列出当前目录下所有文件夹：
            ```
            executa_process(COMMAND ls ${CMAKE_CURRENT_LIST_DIR} OUTPUT_VARIABLE subs)
            ```
   2. 使用file操作
    > file可以实现同样效果，且更加通用
        ```
        set(cur_path "${CMAKE_CURRENT_LIST_DIR}")
        file(FLOB subs RELATIVE "${cur_path}""${cur_path}/*")
        ```
        输出变量为subs
   3. 示例
    >列出当前目录下所有文件夹，并添加到dirs中
    ```
    set(dirs)
    set(cur_path "${CMAKE_CURRENT_LIST_DIR}")    file(FLOB subs RELATIVE "${cur_path}""${cur_path}/*")
    
    foreach(_sub ${subs})
        if(IS_DIRECTORY ${_sub})
            list(APPEND dirs ${_sub})
        endif()
    foreach()
    ```


     
# 05 目标构建
## 1. 前期准备
    1. 编译的四个步骤
        > 预编译、编译、汇编、链接
    2. 动态库、静态库
        * ```.dll```的文件，被称为是动态链接库```Dynamic-Link Library```，同样在Ubuntu/usr/local/lib目录下也能看到一系列名为*.so的文件，称为共享对象```Shared Object```,即动态库
        * windows下.lib的文件，Ubuntu下*.a文件，都属于静态库
    3. 区别
        1. 静态库
            在编译时就会**直接整合到目标程序**中，所以利用静态库编译的文件会较大，优点是**编译成功的可执行文件可以独立运行**；但从升级难易度看没有明显优势，**如果函数库更新，需要重新编译**
        
        2.动态库 
            动态函数库在编译时，在程序中只有一个符号，也就是说，只有当可执行文件需要用到函数库的机制时，才会去解析这个符号并完成动态地址的重定位，也即是说可执行文件无法单独运行，对产品功能升级十分方便，只要替换对应动态库即可完成升级，不必重新编译整个可执行文件


## 2. 目标构建！
### 2.1 可执行文件的构建
* 文件架构：
.
├── CMakeLists.txt
└── main.cpp
* CMakeLists.txt:
```
cmake_minimum_required(VERSION 3.16) #指定CMake最小版本号，小于此版本的无法通过
project(MyProject)                   #定义项目
add_executable(main main.cpp)        #使用main.cpp生成可执行文件main
```
> main是目标名，在CMake朱红具有TARGET属性的变量，即目标，同时也是可执行文件的名称。如果main目标依赖了其他目标，例如main.cpp中使用到了来自其他.cpp文件定义的函数，也要在CMakeLists.txt中输入：
> ``` 
    target_link_libraries(
        main
        PRIVATE xxx
    )
### 2.2普通库目标构建
* ```add_library()```将功能模块隔离抽象出来，为该模块构建一个库，最终将这些库链接至可执行文件的目标上,只能是源文件
* ```add_subdirectory(MyLib)```
* 每个库都需要有一个单独的CMakeLists管理
* SHARED无法做到内存上的服用，在多个进程使用到该动态库时，仍然会开辟一块内存存储该动态库内容，为了实现内存上的复用，需要使用地址五官代码机制（position-independent code）
  ```set()```
* ```target_include_directories```添加头文件搜索路径
* ```target_link_libraries```添加本项目外其他目标和项目外第三方目标
### 2.2接口库目标构建
* 对只有头文件的库生成目标，并且生成链接，需要创建接口库，因为没有源文件，没有实质性参与构建编译，但提供了与普通目标一样的操作方式，因此接口库为伪目标
* add_library、target_include_directories、target_link_libraries和普通库类似，只不过少了源文件的添加步骤，并且要设置属性为INTERFACE
### 2.4导入目标构建
* 若仅提供了若干头文件和若干库文件，这种情况下无法通过add_library从源文件构建目标，需要引入导入目标
    ```
    add_library(camera SHARED IMPORTED)
    set_target_porperties(
        camera PROPERTIES
        INTERFACE_INCLUDE_DIRECTORIES "${CMAKE_CURRENT_LIST_DIR}/include"
        IMPORTED_LOCATION "${CMAKE_CURRENT_LIST_DIR}/lib/libMVSDK.so"    
    )
    ```
* IMPORTED_LOCATION需要制定绝对路径
* 存在多个动态库时，需找到真正的动态库
* INTERFACE_INCLUDE_DIRECTORIES属性，也可以用常规写法，即
    ```
    target_include_directories(
        camera
        INTERFACE include
    )
    ```
## 3.补充内容
### 3.1其余目标库
* 目标库的划分：二进制目标和伪目标
#### 3.1.1对象库
* 多个源文件设置成一个单独的目标，而不生成可执行文件或普通库，可以被其他目标链接和使用
* 创建对象库，同样可以使用add_library命令，并将OBJECT关键字与源文件列表一起使用
#### 3.1.2别名目标
* 即引用，使用ALIAS关键字
* 但别名目标不可作为target_link_libraries,target_include_directories的目标名
### 3.2设置目标属性
1. 补充内容
