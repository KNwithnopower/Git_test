# namespace
> 其目的即为避免命名冲突而存在
* 若不同namespace定义了同一个函数，可以```apple::print()```和```orange::print```作区分
* namespace还可以嵌套
```
namespace apple {namespace functions{
    void print()
    {
    
    }}
    void printf()
    {
    
    }}
```
* 也可以在函数或者某个局部作用域内部```using namespace apple```
* 还可以在函数里用其他字符去代替某一个长串难写的namespace``` namespace a= apple```
# 智能指针
> new在堆上分配内存，delete来删除内存，释放内存，而智能指针就是实现这一过程自动化
* unique_ptr是栈上作用域指针，超出作用域时，它会被销毁，自动调用delete，且不可被复制，不然会有两个指针指向同一个内存，若其中一个被销毁，那么共同指向的那个内存也会被销毁。所以unique_ptr的开销是十分低的
* shared_ptr，其工作方式是通过引用计数，是一种方法，可以追踪指针有多少个引用，一旦计数为零，则被销毁，内存释放
  * 使用make_shared 初始化对象，分配内存效率更高，因为通过new初始化，涉及到两次内存分配，比如```shared_ptr<int>sp1=new int(10)```,第一次是通过new为数据对象分配内存，即上方的new int（10），第二次是构造一个shared_ptr的管理对象，而make_shared构造只分配一次内存，既包括管理对象也包括数据对象
* weak_ptr,设计的目的是为配合 shared_ptr 而引入的一种智能指针来协助 shared_ptr 工作，它只可以从一个 shared_ptr 或另一个weak_ptr 对象构造, 它的构造和析构**不会引起引用记数的增加或减少**。 同时weak_ptr 没有重载*和->但可以使用 lock 获得一个可用的 shared_ptr 对象。
# this
  * 在构造函数内部，形参与成员变量名称相同时，就可以显式使用```this->x=x```,
  * 在一个const函数里，this指针也会是const，而在非const函数里，this也就非const