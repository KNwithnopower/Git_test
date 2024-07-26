# 检验
1. **解释下列命令的含义：cd，cat，mkdir，touch，mv，cp，rm，chmod，在表示地址时，.和..分别是什么意思？sudo是什么意思？**
* cd：切换当前工作目录
  
  * cd等价于cd ~：即直接进入当前用户的家目录下

  * cd 【相对路径】
    
    * cd ..:进入到上级目录下
    * cd ../local:进入到上级目录的local目录下
    * cd [绝对路径]：``` cd /usr/local/ ```
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
* mv:移动到新位置或者重命名
  * mv 需要移动的文件路径
    * ``` mv myfile /myfile```
    * ``` mv myfolder /myfolder```
    * ``` mv myfile myfile007```
* cp:复制文件/文件夹到指定位置
  * cp 被复制的文件路径``` cp /home/bing/myfile ./```
  * cp -r [被复制的文件夹路径] [文件夹被复制到的路径]
    * -r表示递归复制，复制文件夹的时候需要加-r
    * ``` cp -r /home/bing/myfolder /```
* rm:删除文件/目录
  * rm [选项] 需要移除的文件路径
    * ``` rm myfile```
    * ``` rm /usr/myfile```
  * rm [选项] 需要移除的目录
    * ``` rm -rf myfolder```
    * ``` rm -rf /usr/myfolder```
* chmod:用于控制用户对文件的权限的命令
  * 
* 表示地址：
  * .当前路径
  * ..上一级路径
* sudo:管理员权限

1. **build文件夹有什么作用？怎么编译一份C++代码？**
   
2. **自行安装Ubuntu系统，安装VSCode，配置C++，CMake，OpenCV，跟着教程成功用OpenCV输出一张红色图片。**
   
3. **~/.bashrc是什么文件，有什么作用？**