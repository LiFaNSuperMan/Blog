# 流式加密与MMAP
- 流式加密
	-	流式加密的主要概念
		-	其实流式加密的主要是利用异或的自反性，如：A^B^B = A ^ 0 = A,这样，通过对明文和生成的密钥流异或得到密文，对密文和密钥流再次异或即可得到明文。所以流式加密的核心是如何生成一个合理、安全的密钥流。流式加密的主要算法现在主要是分为RC4和ChaCha20。
	-	流式加密的性能
		-	待测试    
	- 	流式加密的主要算法之RC4
		-	RC4(Rivest cipher 4)对称加密技术，和DES算法一样的，RC4算法是对称加密算法，即使用同一个密钥来加解密。和DES不同的是RC4不是对明文进行分组处理，而是以字节流的形式依次加密明文中的每一个字节，解密的时候也是密文中的每一个字节进行解密，这种加密方式又称为流式加密。
		- RC4优缺点
			-	优点：它的最大亮点是算法的简单性和快速处理，因此它可以很容易多种语言上实现。设有一个256字节的数组，用它来加密明文 plaintext，每使用一次，数组的就要交换其中两个字节。被交换的两个字节通过变量 i j 来指定，它们初始值为 0。计算 i 的新值时，直接加一，计算 j 的新值时，将 i 数值对应的数组字节值和密钥字节值相加得到。要得到密文 ciphertext，将明文和 i j 求和后指示的字节相异或 XOR，加密 encrypt 和解密 decrypt 的过程一样。然后交换 i j 指示的数组字节，所有操作都对256求模，数组使用前经过初始化，值依次为 0-255。密钥长度在 1-256字节 
			- 	缺点：因为RC4算法具有实现简单，加密速度快，对硬件资源耗费低等优点，于轻量级加密算法的行列中确属优秀的算法。但是另一方面，其简单的算法结构也容易遭到破解攻击，RC4算法的加密强度完全取决于密钥，即伪随机序列生成，而真正的随机序列是不可能实现，只能实现伪随机。这就不可避免出现密钥的重复，而加密解密过程都进行了异或运算，这就意味着，一旦子密钥序列出现了重复，密文就极有可能被破解。
		-  注意点：程序实现时，须要注意的是，状态向量数组S和暂时向量数组T的类型应设为unsigned char，而不是char。由于在一些机器下，将char默认做为signed char看待，在算法中计算下标i，j的时候，会涉及char转int。假设是signed的char。那么将char的8位复制到int的低8位后，还会依据char的符号为，在int的高位补0或1。由于密钥是随机产生的，假设遇到密钥的某个字节的高位为1的话，那么计算得到的数组下标为负数，就会越界。
		- RC4主要实现代码[github]() 
	-	流式加密的主要算法之ChaCha20(sa)
		-	ChaCha20是一种流加密算法，其原理和实现都较为简单，大致可以分成如下两个步骤：
			-	基于输入的对称秘钥生成足够长度的keystream
			-	将上述keystream和明文进行按位异或，得到密文
		-	重点在keystream的生成
			- 	quater-round(x,y,z,w)
				-	 ChaCha20算法中的基本操作叫做“quarter round”，一个quarter round定义如下：		
					-	核心公式
						-	a ⊞= b; d ⊕= a; d <<<= 16;
						-	c ⊞= d; b ⊕= c; b <<<= 12;
						-	a ⊞= b; d ⊕= a; d <<<= 8;
						-	c ⊞= d; b ⊕= c; b <<<= 7;  
				-	 这样得到一组新的a、b、c、d,quater-round(x,y,z,w)实际操作的就是下文生成的state
				-	 round有如下操作
					-	1.  QUARTERROUND ( 0, 4, 8,12)
						2.  QUARTERROUND ( 1, 5, 9,13)
						3.  QUARTERROUND ( 2, 6,10,14)
						4.  QUARTERROUND ( 3, 7,11,15)
						5.  QUARTERROUND ( 0, 5,10,15)
						6.  QUARTERROUND ( 1, 6,11,12)
						7.  QUARTERROUND ( 2, 7, 8,13)
						8.  QUARTERROUND ( 3, 4, 9,14)
					-	如上有两个round，将上面8个操作执行10次，既执行20次round，这就是chacha20中的20	 	  
			-	keystream的生成
				- 在state上反复应用确定的好的quater-round(x, y, z, w)组合，得到一个新的64字节（即512位）的随机数据，此数据即为一个keystream block。state的内容不是随便定义的，ChaCha20算法存在如下规定：
					-	cccccccc  cccccccc  cccccccc  cccccccc
					-	kkkkkkkk  kkkkkkkk  kkkkkkkk  kkkkkkkk
					-	kkkkkkkk  kkkkkkkk  kkkkkkkk  kkkkkkkk
					-	bbbbbbbb  nnnnnnnn  nnnnnnnn  nnnnnnnn
					-	其中：	
	c：4个32位数字，内容固定为：0x61707865, 0x3320646e, 0x79622d32, 0x6b206574。
	k：256位的对称密钥，即32字节
	b：count，按明文的block数递增，可以从0或者1开始
	n：nouce，其组成根据ChaCha20在不同协议中的使用而有所区别，下文将介绍TLS中的nouce构成
				-	最终生成的keystream为512位（64字节），然后将明文按照512位（64字节）分块与keystream按位异或即可，
			-	chacha20的优缺点
				-	待填写  
			-	注意点：
				-	key需要为32字节
				- 	nonce的选取
				-  注意分块
			-	- Chacha20主要实现代码[github]() 
- MMAP
	- MMAP的主要原理
		-	通过 mmap 内存映射文件，提供一段可供随时写入的内存块，App 只管往里面写数据，由 iOS 负责将内存回写到文件，不必担心 crash 导致数据丢失。 
      -	其实core就是通过本地文件和内存地址进行映射，这样就可以完成直接对内存地址就行操作完成对文件的操作。主要是mmap有一个脏页面回写。
	- MMAP的优缺点
	- MMAP的主要实现代码[github](https://github.com/LiFaNSuperMan/LFMmap)
