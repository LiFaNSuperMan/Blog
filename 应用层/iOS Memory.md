# iOS Memory
## iOS基于BSD
## 传统的系统机制
-	冯·诺依曼结构
	-	 1945年提出将存储器和运算器分离，导致了以存储器为核心的现代计算机的诞生
	-	 冯·诺依曼瓶颈：在目前的科技水平之下，CPU与存储器之间的读写速度远远小于CPU的工作效率，简单的说就是CPU太快了，存储器读写读取不够快，造成了CPU性能的浪费
	-	 所以现在的解决方式是多级存储，平衡存储器的读写速度
-	存储器的层次结构
	-	随机访问存储器RAM
		-	SRAM	高速缓存
		- 	DRAM  	主存
	-	ROM
	-	了解存储器的层次结构   
		-	l0 寄存器
		- 	l1 高速缓存 sram 
		-  l2 高速缓存 sram
		-  l3 高速缓存 sram
		-  l4 主存		dram
		-  l5 本地磁盘	
		-  l6 远端二级存储 分布式数据库以及web服务
	-	CPU寻址方式
		-	现代处理器采用虚拟寻址的方式，通过CPU内存管理单元（memory management unit）翻译得到物理地址
	-	虚拟内存
		-	直接使用物理寻址，会有地址空间缺乏保护的严重问题。那么如何解决呢？实际上在使用了虚拟寻址之后，由于每次都会进行一个翻译过程，所以可以在翻译中增加一些额外的权限判定，对地址空间进行保护。所以，对于每个进程来说，操作系统为其提供一个独立的、私有的、连续的地址空间，这就是所谓的虚拟内存<br>
		![图](https://user-gold-cdn.xitu.io/2019/7/29/16c3db80c9adaa91?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)   
		-	虚拟内存的最大意义是保护的进程的地址空间，使得进程之间不能越权进行互相的干扰。 由于进程是一个连续的内存空间，也方便了程序员进行管理。
		- 	Swap内存交换机制
			-	由于硬盘的读写速度不如内存快，所以操作系统会优先使用内存空间，但是当物理内存不足的时候，就会将部分内存数据到硬盘上去存储。这样虚拟内存机制利用了硬盘空间扩展了内存空间。
	-	内存分页
		-	物理内存的最小单位是帧（Frame）,虚拟内存的最小单位是页（Page）
			-	内存分页最大的意义是，支持了物理内存的离散使用。同时利用了一些页面调度算法，利用翻译过程中的局部性原理，将大概率被使用的帧地址加入到TLB或者是页表中，提高翻译的效率。
			
##	iOS的内存机制
-	使用了虚拟内存机制
- 	内存有限，但是单应用可用内存大
-  没有使用Swap内存交换机制
-  OOM(out of memory carsh)崩溃
-  iOS内存分类
	-	Clean memory
		-	对于iOS来说，这部分指的是能被重新创建的内存
			-	app的二进制文件
			- 	framework中的_DATA_CONST段
			-  	文件映射的内存
			- 	未写入数据的内存  
	- 	Dirty memory
		-	值得是不能被清理的物理内存，直到物理内存不够用之后，系统便会开始清理  
	-  Compressed memory      
		-	当物理内存不足的时候，iOS会将部分内存压缩，在需要读写的时候在解压，已达到内存的目的。这种方式和Swap内存交换机制的区别是，这种内存压缩技术消耗的时间更少，但占比CPU更高。
-	OOM崩溃
	-	Jetasm机制
	- 	现有的检测OOM机制
		-	OOM分为Foreground OOM 、 Background OOM
		- 	Facebook [FBAllocationTracker](https://github.com/facebook/FBAllocationTracker)
		-  Tencent [OOMDetector](https://github.com/Tencent/OOMDetector)      
-	参考文章
	-	[iOS Memory 内存详解 (长文)](https://juejin.im/post/5d3ee77ef265da039f1290b2?utm_source=gold_browser_extension)   