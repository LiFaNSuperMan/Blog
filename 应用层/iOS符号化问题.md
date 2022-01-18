# iOS符号化问题

### 什么是符号化

### 符号化的原理是什么

1.  第一步 - 从内存地址回溯到文件
    -   指的是将运行时随机的内存地址转换为磁盘上的二进制文件中稳定可用的文件信息
    -   磁盘地址空间的地址是编译时`linker`链接器赋予二进制文件的地址
    -   ASLR ( Address space layout randomization ) 地址空间布局随机化技术
        -   ASLR Slide 内存空间随机分布偏移量
            -   ASLR slide = Load Address - Linker Address
            -   S = L - A
    -   所以可以通过崩溃信息得到崩溃位置的Link Address
2.  第二步 - 还原运行时调试信息
    -   Function starts 符号表
        -   只提供方法地址 不提供方法名称
        -   内置于Mach-O文件中的`__LINKEDIT`段，并以`LC_FUNCTION_STARTS`标记
    -   Nlist Symbols List - Nlist 符号表
        -   提供方法名称和方法地址
        -   内置于Mach-O文件__LINKEDIT段
        -   n_type 为直接符号时
            -   直接符号表的问题是只限于在链接时被直接链接的部分，动态库等运行时加载的二进制文件不被包括在内，未能符号化的方法就是跨模块从动态库中调用的方法
            -   可以在编译时主动干预直接符号表的生成
        -   n_type为间接符号时
    -   DWARF
        -   编译单元、子程序、DWARF关系树
            -   编译单元表示了在项目中会被编译的单个源码文件
            -   子程序
            -   DWARF关系树
        -   几乎记录了函数的所有上下文信息
        -   一般存储于单独的dSYM Bundle中
        -   注意内联函数、内联子程序