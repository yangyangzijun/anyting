## 容器(containers)

## 空间配置器（allocator）

为容器分配空间

对于  new T();

allocator 将其分为两阶段完成

- allocator deallocator  内存的配置与释放
- construct  destory  对象的构造与析构

考虑到小型区块可能造成的内存破碎问题，设计了双层配置器

* 第一级配置器使用malloc 和 free
* 第二级配置器采用不同的策略： 当配置区超过128B ,调用 第一级配置器





