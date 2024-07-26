# 一、linux介绍
## 区别
* windows:但用户、多任务
* linux：多用户、多任务，一切皆文件
## 目录结构
* bin:binary,即存放二进制文件
* dev：存放外接设备
* etc：配置文件
* home：除了root用户以外的用户的家目录
* proc：进程
* root：root管理员用户的家目录
* sbin：super权限的二进制
* user：用户安装的软件目录
* tmp：临时文件存放目录
* var：程序/系统日志文件的目录
* mnt：外界设备需要挂载到的目录
## 指令与选项
* 指令格式：
  * 命令（空格）【选项】（空格）【操作对象】
  * 【】即可选的意思
  
  eg: ``` ls -l -a -h /home ./ ```

  等同：``` ls -lah /home ./ ```
* 重要指令：
  * pwd：打印当前目录
  * ls：列出当前目录下文件/夹的名称
    * ls [路径]：指出该路径下的所有文件

        绝对路径

        ``` ls /home```

        相对路径

        ``` ls ./ //当前目录下```
        
        ``` ls ../ //上一级目录下 ```


    * ls [选项]：以制定格式进行显示
        
        -l：以详细列表形式进行展示
        
        * “-“表示类型是文件
        * “d”表示类型是文件夹
        * 隐藏文档一般以“.”开头

        -a：表示现实***所有***的文件/文件夹（包含隐藏文件/文件夹）

        -h：表示可读性较高的形式展示

        ``` ls -lah /home```
* cd：切换当前工作目录
  
  * cd等价于cd ~：即直接进入当前用户的家目录下

  * cd 【相对路径】
    
    * cd ..:进入到上级目录下
    * cd ../local:进入到上级目录的local目录下
    * cd [绝对路径]：``` cd /usr/local/ ```
* mkdir:创建目录（文件夹）
  
  * mkdir 路径
  * mkdir -p 路径：``` mkdir -p ~/a/b/c```一次性创造多层不存在目录（纵向）
  * mkdir 路径1 路径2 路径3（横向）
* touch：创建新文件
  * touch [路径]
  
    * 在当前目录下创建文件``` touch linux.txt```
    * 在上级目录下创建文件``` touch ../linux```
    * 在绝对路进下创建文件``` touch /home/bing/myfile```
  * touch 路径1 路径2
    * 在当前目录下创建两个文件``` touch file file.txt```dotnetcli
* rm:删除文件/目录
  * rm [选项] 需要移除的文件路径
    * ``` rm myfile```
    * ``` rm /usr/myfile```
  * rm [选项] 需要移除的目录
    * ``` rm -rf myfolder```
    * ``` rm -rf /usr/myfolder```
* cp:复制文件/文件夹到指定位置
  * cp 被复制的文件路径``` cp /home/bing/myfile ./```
  * cp -r [被复制的文件夹路径] [文件夹被复制到的路径]
    * -r表示递归复制，复制文件夹的时候需要加-r
    * ``` cp -r /home/bing/myfolder /```
* mv:移动到新位置或者重命名
  * mv 需要移动的文件路径
    * ``` mv myfile /myfile```
    * ``` mv myfolder /myfolder```
    * ``` mv myfile myfile007```
* cat: 显示、创建或连接文件
  * 参数 
    * -A	显示不可打印字符,并在行尾显示$
    * -b	显示行号,行号添加在非空行前面
    * -E	显示行结束字符$
    * -n	显示包含行号的输出
    * -s	压缩连续的空行输出
    * -T	显示制表符为^I
    * -v	显示不可打印字符
    * 文件	要显示或连接的文件
  * 例子
    * ``` cat demo.txt```输出结果是会打印出该文件内容，也可显示多个文件
    * ``` cat > demo2.txt```创建了一个新文件，然后可以紧接输入文件内容，最后按Ctrl+D结束内容输入
    * ``` cat demo.txt test.txt demo2.txt > combined.txt```将三个文件连接成了一个文件
    * ``` cat -n demo.txt```会带上行号显示内容
* chomod：用于控制用户对文件的权限的命令
* man:包含linux全部命令手册
  * man [命令]
    * ``` man ls```
    * ``` man cd```
    * ``` man man```
* reboot: 立刻重启
* shutdown： 关机
  * ``` shutdown -h now```
## 文件编辑
* vim
  
  *既能文本编辑又能写代码

  * ``` vim linux ```进入到linux文件里，然后按``` i ```可以进入到输入模式

  * 退出时先按Esc，再输入一个```:w```即可完成写入

  * 再输入``` :q```即可退出编辑

# 二、环境搭建
* sudo：提升用户权限到管理员权限
# 三、GCC编译器
![](https://free.wzznft.com/i/2024/07/13/exoe1l.png)
 1. 预处理//.i文件
   
  ``` g++ -E test.cpp -o test.i```
 
 2. 编译//.s文件
  
  ``` g++ -S test.i -o test.s```

 3. 汇编//.o文件

  ``` g++ -c test.s -o test.o```

 4. 链接//bin文件

  ``` g++ test.o -o test```

## g++重要的编译参数
1. **-g**编译带调试信息的可执行文件
    
    ``` g++ -g test.cpp```
2. **-O[n]**优化源代码
  
    ``` g++ -o2 test.cpp```
    
3. **-l和-L**制定库文件|指定库文件路径
    
     * 在/lib和/usr/lib和/usr/local/lib里的库直接用-l参数就能链接
     
          链接glog库 
      ``` g++ -lglog test.cpp```

      * 如果库文件没在上面三个目录里，需要使用-L参数指定库文件所在目录
        
          链接mytest库，在/home/bing/mytestlibfolder的目录下  
      ``` g++ -L/home/bing/mytestlibfolder -lmytest test.cpp```
  
  4. -I指定头文件搜索目录 
  5. -Wall 打印警告信息
  6. -w关闭警告信息
  7. -std=c++11设置编译标准
  8. -o 指定输出文件名
  9. -D定义宏
  ![](https://free.wzznft.com/i/2024/07/13/exoo6o.png)
## g++命令行编译


# 六、CMake
## 重要指令
* **cmake_minimum_required**:指定CMake的最小版本要求
  
  ``` cmake_minimum_required(VERSION 2.8.3)```
* **project**：定义工程名称，并指定工程支持的语言
    * project(projectname[CXX][C][Java])
  ``` project(HELLOWOORLD)```
* **set**显式地定义变量
  ```set(SRC sayhello.cpp hello.cpp)```

* **include_directories**向工程添加多个特定的头文件搜索路径
  
  ``` include_directories(/usr/include/myincludefolder ./include)``` 
* **link_library**项工程添加多个特定的库文件搜索路径
  
  ``` link_directories(/usr/lib/mylibfolder ./lib)``` 
* **add_library**生成库文件
  * add_library(libname \[SHARED|STATIC|MQDULE][EXCLUDE_FROM_ALL]source1 source2…souceN)
  * SHARED是动态库，STATIC是静态库
  
  ``` addlibrary(hello SHARED ${SRC})``` 
* **add_compile_options** 添加编译参数
  ``` add_compile_options(-wall -std=c++11 -o2)```    
* **add_executable** 生成可执行文件
    *  add_library(exename source1 source2…)
  ``` add_executable(main main.cpp)``` 
* **targe_link_libraries**为target添加需要链接的共享库
* **add_subdirectory**向当前工程添加存放元文件的子目录
* **aux_source_directory**发现一个目录下所有的源代码文件并将列表存储在一个变量里，这个指令被饮食用来自动构建源文件列表
## CMake常用变量
* CMAKE_C_FLAGS gcc编译选项
* CMAKE_CXX_FLAGS g++编译选项

``` set( CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")```

* CMAKE_BUILD_TYPE 编译类型(Debug, Release)
    
    * ``` 设定编译类型为debug，调试时需要选择debug   set(CMAKE_BUILD_TYPE Debug) ```

    * ``` 设定编译类型为release，发布时需要选择release  set(CMAKE_BUILD_TYPE Release)```
* - **CMAKE_BINARY_DIR**
    
    **PROJECT_BINARY_DIR**
    
    **<projectname>_BINARY_DIR**
    > 这三个变量指代的内容是一致的。如果是 in source build，指的就是工程顶层目录。如果是 out-of-source 编译,指的是工程编译发生的目录。PROJECT_BINARY_DIR 跟其他指令稍有区别，不过现在，你可以理解为他们是一致的。

* - **CMAKE_SOURCE_DIR**
    
    **PROJECT_SOURCE_DIR<projectname>_SOURCE_DIR**

* CMAKE_C_COMPILER：指定C编译器
* CMAKE_CXX_COMPILER：指定C++编译器
* EXECUTABLE_OUTPUT_PATH：可执行文件输出的存放路径
* LIBRARY_OUTPUT_PATH：库文件输出的存放路径

## CMake 编译工程
CMake目录结构：项目主目录存在一个CMakeLists.txt文件
两种方式设置编译规则：
1. 包含源文件的子文件夹包含CMakeLists.txt文件，主目录的CMakeLists.txt通过add_subdirectory添加子目录即可；
2. 包含源文件的子文件夹未包含CMakeLists.txt文件，子目录编译规则体现在主目录的CMakeLists.txt中；
### 编译流程
在linux平台下使用CMake构建C/C++工程流程如下：
* 手动编写 CMakeLists.txt。
* 执行命令 cmake PATH生成 Makefile ( PATH 是顶层CMakeLists.txt 所在的目录 )。
* 执行命令make 进行编译。
```
# important tips
.  		# 表示当前目录
./ 		# 表示当前目录

..  	# 表示上级目录
../ 	# 表示上级目录 
```
### 两种构建方式
* **内部构建(in-source build)**：不推荐使用
    
    内部构建会在同级目录下产生一大堆中间文件，这些中间文件并不是我们最终所需要的，和工程源文件放在一起会显得杂乱无章。
```
## 内部构建

# 在当前目录下，编译本目录的CMakeLists.txt，生成Makefile和其他文件
cmake .
# 执行make命令，生成target
make
```
* **外部构建(out-of-source build)**：推荐使用
   
    将编译输出文件与源文件放到不同目录中
```
## 外部构建

# 1. 在当前目录下，创建build文件夹
mkdir build
# 2. 进入到build文件夹
cd build
# 3. 编译上级目录的CMakeLists.txt，生成Makefile和其他文件
cmake ..
# 4. 执行make命令，生成target
make
```
即
```
mkdir build
cd build
cmake ..
make
```
### 实战项目
![](https://free.wzznft.com/i/2024/07/13/exowz5.png)
![](https://free.wzznft.com/i/2024/07/13/exp25y.png)
![](https://free.wzznft.com/i/2024/07/13/expekf.png)
![](https://free.wzznft.com/i/2024/07/13/expssa.png)
红框这两个非常重要
* program是可调试文件的绝对路径
* prelaunchTask即在调试前的task
![](https://free.wzznft.com/i/2024/07/13/exq7xj.png)
* tasks需要完成的任务
* prelaunchtask的标签就是task.json里的对应标签，如果是build，好处就算在程序运行前就不用手动编译了，随便改代码后，不用手动cmake 和 make编译，就可以直接调试了
![](https://free.wzznft.com/i/2024/07/13/exqd2e.png)
![](https://free.wzznft.com/i/2024/07/13/exqu0i.png)