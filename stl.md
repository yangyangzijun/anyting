## 容器(containers)

## 空间配置器（allocator）

为容器分配空间

对于  new T();

allocator 将其分为两阶段完成

- allocator deallocator  内存的配置与释放
- construct  destory  对象的构造与析构

考虑到小型区块可能造成的内存破碎问题，设计了双层配置器

* 第一级配置器使用malloc 和 free

  > 第一级配置器以malloc ,free ,realloc 等c 函数实现内存的配置，释放，重配置操作，并实现类似c++ new_handler 的机制
  >
  >  c++ new_handler ：系统在内存配置需求无法被满足时，调用一个指定的函数。换句话说是，一旦 ：：operator new 无法完成任务，在出现：：bad_alloc ，会先调用由客户端指定的处理程序

* 第二级配置器采用不同的策略： 当配置区超过128B ,调用 第一级配置器  当配置器区块小于128B ，为了降低负担采用 memory pool 

  > 内存池 详见stl源码剖析 p6

  空间配置函数 allocate（）

>先判断区块大小，大于128B 调用第一级配置器，小于128B 检查相应的  free list ,如果free list 又可用的区块
>
>





