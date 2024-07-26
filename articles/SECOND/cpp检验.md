
# 1. **指针是什么？引用是什么？值传参与引用传参有什么不同？**

答：
* 指针是存储地址的变量
* 引用就是给变量取别名，和对应的变量直接用的同一个地址
* 值传参只是把值传给了形参，形参和实参不是同一个地址，它在函数内部就是局部变量，当函数调用结束时，内部局部变量也会被销毁，实参也不会被影响而改变；而引用传递是用形参给实参的地址起了一个别名，那么函数调用结束后，形参的值改变了，实参也会跟着改变。

# 2. **在C语言中类和结构体有什么不同？在C++中类和结构体有什么不同？**

答：
* C语言是面向过程的语言，应该是没有类的，所以C的结构体不能有成员函数，只能封装一些属性变量
* 而C++是面向对象的语言，类和结构体主要区别是前者默认是private，后者默认是public

# 3. **C++有哪几种类型转换函数？分别有什么用？**
答：
* 隐式地转换：
    ```
    int a=5;
    double b=a;
    ```
* C语言风格的转换：
    ```
    double a=(int)value+5.4;
    ```
* static_cast
  >是最常用的C++风格的强制类型转换，主要是为了执行那些较为合理的强制类型转换，使用格式如下：```static_cast<type_id>(expression);```
  * 场景一：用于基本内置数据类型之间的转换
    >基本内置数据类型比如char变float，double变long，可隐式转换
    ```
     char type_char = 'A';
    float type_float = type_char; // 隐式转换也可以
    float type_float_cast = static_cast<float>(type_char); // 显式地使用static_cast进行强制类型转换
    ```
  * 场景二：用于指针之间的转换
    >static_cast可以用于指针之间的转换，这种转换类型检查非常严格，不同类型的指针是直接不给转的，除非使用void*作为中间参数，我们知道隐式转换下void*类型是无法直接转换为其它类型指针的，这时候就需要借助static_cast来转换了。
    ```
    int type_int = 10;

    void* void_ptr = &type_int; // 任何指针都可以隐式转换为void*

    float* float_ptr3 = void_ptr; // void* -> float* 隐式转换无效

    float* float_ptr4 = static_cast<float*>(void_ptr); // void* -> float* 使用static_cast转换成功

    char* char_ptr3 = void_ptr; // void* -> char* 隐式转换无效

    char* char_ptr4 = static_cast<char*>(void_ptr); // void* -> char* 使用static_cast转换成功
    ```

  * 不能转换掉expression的const或volitate属性
  
  * 用于类实例之间转换
    * 派生类可以转到基类（上行转换），但基类转到派生类（下行转换）会认为是两个无关联的类进行转换
    * 也可以通过复制构造函数去转换 
  * 用于没有多态的类实例指针或引用之间的转换
    *  上行转换安全，下行转换不安全，如果两个类无继承关系，则会失败，但是这种情况下会显性地展示出错误信息，是安全的
  * 用于具有多态的类实例指针或引用之间的转换
    * 进行上行转换（派生类指针->基类指针、派生类引用->基类引用）是完全安全的，没有任何问题，当然这个过程由隐式转换来完成也是合法的
    
    * 进行下行转换（基类指针->派生类指针、基类引用->派生类引用）由于缺乏安全检查，所以是有问题的，并且因为具有多态的类往往具有特殊的用法，所以在这种情况下产生的后果比前面没有多态情况下的要更严重，要尽量避免这种用法

* reinterpret_cast
  >它让程序员能够将一种对象类型转换为另一种，不管它们是否相关

    ```reinterpret_cast<type_id>(expression);```

    * type-id和expression中必须有一个是指针或引用类型
    * 第一种用途是改变指针或引用的类型
    * 第二种用途是将指针或引用转换为一个整型，这个整型必须与当前系统指针占的字节数一致
    * 第三种用途是将一个整型转换为指针或引用类型
    * 可以先把一个指针转换成一个整数，再把该整数转换成原类型的指针，还可以得到原先的指针值（由于这个过程中type-id和expression始终有一个参数是整形，所以另一个必须是指针或引用，并且整型所占字节数必须与当前系统环境下指针占的字节数一致）
    * 使用reinterpret_cast强制转换过程仅仅只是比特位的拷贝，和C风格极其相似（但是reinterpret_cast不是全能转换，详见第1点），实际上reinterpret_cast的出现就是为了让编译器强制接受static_cast不允许的类型转换，因此使用的时候要谨而慎之
    * reinterpret_cast同样也不能转换掉expression的const或volitale属性。
* const_cast
  
  > 作用是去掉const或volitale属性，且只能去除指向常量对象的指针或者引用的常量性，变量是不可以的，而且不同类型的指针或引用也不可以，只能是同类型的指针或引用
    ```
    float type_float = const_cast<float>(type_int);        // 错误，const_cast只能转换引用或者指针

    float* type_float_ptr = const_cast<float*>(&type_int); // 错误，从int* -> float* 无效

    float& type_float_ref = const_cast<float&>(type_int);  // 错误，从int& -> float& 无效
    ```
    > 一个变量本来就不具备const属性，但是在传递过程中被附加了const属性，这时候使用const_cast就能完美清除掉后面附加的那个const属性了。
    ```
    #include <iostream>

    void fun(const int& v)
    {
        int& type_int_ref = const_cast<int&>(v);
        type_int_ref = 10;
    }

    int main(int argc, char* argv[])
    {
        int type_int = 100;

        fun(type_int);
        std::cout << type_int << std::endl; // 输出10，改变了
    }
    ```
* dynamic_cast
  > 前面三种都是编译时完成的，而dynamic_cast是运行时处理的

  * dynamic_cast是运行时处理的，运行时会进行类型检查（这点和static_cast差异较大）
  * dynamic_cast不能用于内置基本数据类型的强制转换，并且dynamic_cast只能对指针或引用进行强制转换
  * dynamic_cast如果转换成功的话返回的是指向类的指针或引用，转换失败的话则会返回nullptr
  * 使用dynamic_cast进行上行转换时，与static_cast的效果是完全一样的
  * 使用dynamic_cast进行下行转换时，dynamic_cast具有类型检查的功能，比static_cast更安全。并且这种情况下dynamic_cast会要求进行转换的类必须具有多态性（即具有虚表，直白来说就是有虚函数或虚继承的类），否则编译不通过
  * 需要有虚表的原因：类中存在虚表，就说明它有想要让基类指针或引用指向派生类对象的情况，dynamic_cast认为此时转换才有意义（事实也确实如此）。而且dynamic_cast运行时的类型检查需要有运行时类型信息，这个信息是存储在类的虚表中的
  * 在C++中，编译期的类型转换有可能会在运行时出现错误，特别是涉及到类对象的指针或引用操作时，更容易产生错误。dynamic_cast则可以在运行期对可能产生问题的类型转换进行测试
  
# 4. auto是什么？有什么用？
    > 到了 C++11 时代，auto 关键字可以用于变量类型的推断，我们可以在声明变量的时候根据变量初始值的类型自动为该变量选择合适的类型，而不需要我们显式指定类型。
    * 普通类型的推断
    ```
    auto x = 127;               // auto 被推断为 int 类型

    auto y = 3.14;              // auto 被推断为 double 类型

    auto z;                         // 错误，必须有初始值才能进行推断
    ```
    * 引用类型推断
    ```
    int x = 0, &rx = x; 

    auto a1 = rx;               // 使用引用其实是使用引用的对象，此时 auto 以引用对象的类型作为 auto 的类型，所以 auto 这里被推断为 int 

    auto &a2 = rx;            // 此时 auto 被推断为 int 类型，a2 对象本身就是 int & 类型

    const auto &a3 = rx;  // auto 被推断为 int 类型，a3 对象本身是 const int & 类型，不能通过 a3 去修改 rx 引用的对象值
    ```
    * const类型的推断
    ```
     const int c = 10;

    auto a1 = c;            // auto 此时是 int，顶层 const 属性被丢弃

    auto &a2 = c;         // auto 此时是 const int，底层 const 属性保留   
    ```
    * 针对数组和函数的推断
    ```
    const char arr[] = "I Love China";

    auto r1 = arr;                                        // 如果将数组名赋值给 auto 变量，那么 auto 推断的结果是指针类型，如果有 const 属性

                                                             // 会被保留，auto 推断的结果是 const char *，r1 为 const char *

    auto &r2 = arr;                                      // 如果将数组名赋值给 auto & 变量，auto & 变量的类型是一个数组引用类型，即为

                                                             // const char (&) [14] 

    int add(int a,int b);                                // 函数声明

    auto r3 = add;                                      // r3 为 int(*)(int, int)

    auto &r4 = add;                                    // r4 为 int(&)(int, int)
    ```
    * 应用场景
        * 一些类型长度书写很长的，可以使用 auto 来简化，如 for(std::vector<int>::iterator it = v.begin();it != v.end();++it)，如果使用 auto 可以直接写为 for(auto it = v.begin();it != v.end();++it)
        * 当函数返回的值不确定时，可以使用 auto 做返回值类型，更加方便，编译器会根据返回值的类型推断 auto 的类型，这种语法是在 C++14 才出现的。
    * 不适用的场景
        * 函数参数类型不能是 auto 类型，比如 int add(auto a, auto b) { return a + b; } 是不允许的
        * 类的成员变量不可以是 auto 类型。类的静态成员变量可以是 auto 类型的，但是需要使用 const 修饰，而且该变量的值在类内初始化
# 5. 基于范围的for循环是什么？有什么用？
    > 格式：for(迭代的变量:数组){}
    ```
    for(const auto& e: arr)
	{
		std::cout << e << " ";
	}
	std::cout << std::endl;
    ```
    >条件是循环迭代的范围必须是可确定的，

    > 用auto声明迭代器的变量，遍历STL的容器，导出来的类型是迭代器解引用后的对象
      ```
        std::vector<int> vec{ 1,5,6,9,4 };
    
    	for (const auto iter = vec.begin(); iter != vec.end(); ++iter)
    	{
    		std::cout << *iter << " ";
    	}
    	std::cout << std::endl;
    
    	for(const auto& elem : vec)
    	{
    		std::cout << elem << " ";
    	}
    	std::cout << std::endl;
      ```
    
# 6. 为什么使用#include <xxx.h>就能访问到xxx.h中定义的函数，即#include的原理是什么？
   > #include就是将<xxx.h>的内容展开复制粘贴过来，然后再编译
   * 为了避免重定义函数，头文件只能声明函数，函数的定义要放到对应的cpp文件中，只能#include该头文件，而不能#include其cpp源文件，因为声明可以声明多次，但定义只能定义一次。 
    
# 7. 为什么不建议在头文件中使用using namespace？
    > 因为using namespace所引用的命名空间内的内容会全部引入到目前的命名空间里，而在大型项目中，头文件可能会被多个cpp文件引用，而一旦引用，那么和using namespace所引用的空间内容重名的内容就会引起混乱甚至报错，追踪查错起来十分麻烦。即使不会报错，没有引起混乱，虽然省去了一部分前缀，但是阅读会更不清晰，不清楚该函数来自哪个头文件
    * 所以解决方法一：只引用特定的不会出现重名的内容，例如``` using std::string;  ```
    * 解决方法二：把using namespace的作用域限制到一个函数或在局部作用域里
  
# 8. 智能指针与普通指针有什么不同？有哪几种智能指针？分别有什么特点？使用new int与使用make_shared<int>为shared_ptr<int>初始化有什么不同？
  
      答： 
      * 普通指针需要new在堆上分配内存，delete来删除内存，释放内存，而智能指针可以实现这一过程自动化
      * C++11有三种智能指针：
        * unique_ptr是栈上作用域指针，超出作用域时，它会被销毁，自动调用delete，且不可被复制，不然会有两个指针指向同一个内存，若其中一个被销毁，那么共同指向的那个内存也会被销毁。所以unique_ptr的开销是十分低的
        * shared_ptr，其工作方式是通过引用计数，是一种方法，可以追踪指针有多少个引用，一旦计数为零，则被销毁，内存释放
        * weak_ptr,设计的目的是为配合 shared_ptr 而引入的一种智能指针来协助 shared_ptr 工作，它只可以从一个 shared_ptr 或另一个weak_ptr 对象构造, 它的构造和析构不会引起引用记数的增加或减少。
      * 使用make_shared 初始化对象，分配内存效率更高，因为通过new初始化，涉及到两次内存分配，比如```shared_ptr<int>sp1=new int(10)```,第一次是通过new为数据对象分配内存，即上方的new int（10），第二次是构造一个shared_ptr的管理对象，而make_shared构造只分配一次内存，既包括管理对象也包括数据对象

# 9.  this有什么作用？static有几种用法？类内静态函数的存储位置跟类内普通函数的存储位置一样吗？类内静态函数的实现中能使用this吗？为什么？
    
    答： this指针即指向对象本身，在成员函数内部，

# 10.  什么是继承？什么是多态？什么是虚函数？什么是纯虚函数？

# 11.  为什么父类指针能指向子类对象但是子类指针不能指向父类对象？如果能指向父类对象的话会发生什么问题？

# 12.  什么是虚析构函数？为什么需要有虚析构函数？什么是虚函数表？结合虚函数表猜测虚析构函数的实现方法；

# 13. 什么是左值？什么是右值？