# 侯捷--C++内存管理机制

# 第一讲、primitives

## C++ memory primitives(内存原理)

> 所有内存管理的最终动作都要跑到`malloc`去，所以`malloc`的效率至关重要。
>
> 由于电脑初期，内存技术不行，所以设计者对内存这一块锱铢必较，几k都要争取。

C++内存使用途径：

<img src="https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262044819.png" alt="image-20230519160935242" style="zoom: 80%;" />

> 即，最高阶是使用STL中的内存分配器，而STL的内存分配器实现是通过new，new[]，new()等较为低阶的函数，但终归会到malloc和free这两种C函数上来，最低阶的是操作系统的内存分配函数。

对内存分配的工具概览如下：

![image-20230519161101733](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262045996.png)

上图4个primitives的基本调用：

```c++
void* p1 = malloc(512);// 512 bytes
free(p1);

complex<int>* p2 = new complex<int>;
delete p2;

void* p3 = ::operator new(512);// 512 bytes
// ::operator new 里面实际就是调用的malloc
::operator delete(p3);
// ::operator delete 里面实际就是调用的free

// 以下使用C++标准库提供的allocators
// 其接口虽有标准规格，但是先厂商并未完全遵守：下面三者形式略异
#ifdef _MSC_VER
// 以下量函数都是non-static,定要通过object调用，以下分配3个ints
	int* p4 = allocator<int>().allocate(3,(int*)0);
	allocator<int>().deallocate(p4,3);
#endif

#ifdef __BORLANDC__
// 以下两函数都是non-static，定要通过object调用，以下分配5个ints
	int* p4 = allocator<int>().allocate(5);
	allocator<int>().deallocate(p4,5);
#endif

#ifdef __GNUC__
// GNUC 2.9
// 以下两函数都是static，可通过全名调用之，以下分配512 bytes
	void* p4 = alloc::allocate(512);
	alloc::deallocate(p4, 512);
#endif

#ifdef __GNUC__
// GNUC 4.9
// 以下两函数都是non-static，定要通过object调用，以下分配7个ints
	void* p4 = allocator<int>().allocate(7);
	allocator<int>().deallocate((int*)p4, 7);
	
// 以下两函数都是non-static，定要通过object调用，以下分配9个ints
	void* p5 = __gnu_cxx::__pool_alloc<int>().allocate(9);
	__gnu_cxx::__pool_alloc<int>().deallocate((int*)p5,9);
#endif
```

**详解**

```C++
#ifdef _MSC_VER
	// 以下量函数都是non-static,定要通过object调用，以下分配3个ints
	int* p4 = allocator<int>().allocate(3,(int*)0);
	allocator<int>().deallocate(p4,3);
#endif
```

> 首先，代码使用allocator()创建了一个int类型的分配器对象，然后调用该对象的allocate函数分配了3个int类型的存储空间，并将其地址赋值给指针p4。
>
> 这里注意，allocate函数的第二个参数指定了分配的存储空间的首地址，这里指定为0，表示从当前位置开始分配。
>
> 因此分配的总的存储空间大小是sizeof(int) * 3 = 12字节。
>
> 接着，代码又使用allocator()创建了另一个int类型的分配器对象，并调用该对象的deallocate函数释放了指针p4所指向的存储空间。
>
> 这里注意，deallocate函数的第二个参数需要指定要释放的存储空间的大小，这里应该指定为3，表示需要释放3个int类型的存储空间。



```c++
#ifdef __BORLANDC__
	// 以下两函数都是non-static，定要通过object调用，以下分配5个ints
	int* p4 = allocator<int>().allocate(5);
	allocator<int>().deallocate(p4,5);
#endif
```

> 首先，代码使用allocator()创建了一个int类型的分配器对象，然后调用该对象的allocate函数分配了5个int类型的存储空间，并将其地址赋值给指针p4。
>
> 这里注意，allocate函数的参数指定了需要分配的存储空间的大小，而不是需要分配的元素个数，因此分配的总的存储空间大小是sizeof(int) * 5 = 20字节。
>
> 接着，代码又使用allocator()创建了另一个int类型的分配器对象，并调用该对象的deallocate函数释放了指针p4所指向的存储空间。
>
> 这里注意，deallocate函数的第二个参数需要指定要释放的存储空间的大小，这里应该指定为5，表示需要释放5个int类型的存储空间。

```C++
#ifdef __GNUC__
// GNUC 2.9
	// 以下两函数都是static，可通过全名调用之，以下分配512 bytes
	void* p4 = alloc::allocate(512);
	alloc::deallocate(p4, 512);
#endif
```

> 首先，代码调用allocate函数分配了一块大小为512字节的存储空间，并将其地址赋值给指针p4。
>
> 由于这里的allocate函数返回的是void*类型的指针，因此需要根据具体的需要进行类型转换，例如可以将其转换为int*类型的指针。
>
> 接着，代码又调用deallocate函数释放了指针p4所指向的存储空间。这里需要注意的是，deallocat函数的第二个参数需要指定要释放的存储空间的大小，这里应该指定为512字节。



## 表达式new、delete

基本用法

### new expression

![image-20230519182324543](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262045579.png)

> `_callnewh` 即 call new handler 
>
> 在operator new的源码中，有个`std::nothrow_t& _THROW0()`参数，表示这个函数不抛异常，取而代之的是返回一个空指针，用户通过判断是否为空指针来判断是否分配成功。
>
> `pc = static_cast<Complex*>(mem);`// 使用static_cast将void*类型的mem强制转换为Complex*类型的指针pc

new的步骤：

1. 申请一段指定类大小的空间：new —> operator new —> malloc
2. 转化为对应类类型
3. 调用类构造函数

### delete expression

![image-20230519182449042](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262045942.png)

delete步骤：

1. 先调用析构函数
2. 释放内存：delete —> operator delete —> free

### 测试用指针直接调用构造函数

<img src="https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262045481.png" alt="image-20230519201521834"  />

### array new、array delete

![image-20230519182623338](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262046173.png)

array new是分配一个对象数组，通常容易犯得一个错误是在delete的时候忘记在delete后面加[]导致内存泄漏的问题。

**但是正如上图所说的，==对于类中没有指针的类==，不加[]可能问题不大，==因为没有指针的类析构函数本来也就没有什么大的作用==**；但是，如果有指针，忘记写[]，那么delete只会触发一次析构函数，delete掉一个指针指向的内存，==**其他指针指向的内存就会泄露**==。如上图的psa析构，str2和str3指向的地址会发生内存泄漏（析构的顺序依编译器而定）。

![image-20230519204913992](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262046483.png)

真的得贴图才能理解得深刻一些：下面两张图分别表示有指针和没指针的对象数组分配内存的区别：
![image-20230519210245923](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262046373.png)

![image-20230519211806359](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262046683.png)

对于分配一个对象数组，他会把数组的大小也放到存放对象数组的内存块的开头，如果在delete内存的时候不加[]，编译器会把他当成一个无指针的对象来析构，那么，他碰到存放数组大小的那块内存的时候，不就傻眼了吗？

### placement new

用array new调用的是类的默认构造函数，还需要对数组中的对象进行真正的构造，这就需要placement new。允许我们在已经分配好的内存上构造对象，所以叫placement new。

他不会进行内存分配，而是调用重载的operator new，用于返回已经分配好的内存，转型、调用构造函数。

![image-20230519182842501](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures/img/202308262046756.png)

### C++引用程序，分配内存的途径

![image-20230519213349316](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/image-20230519213349316.png)

![1693054370740](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054370740.webp)

## 重载::operator new / ::operator delete

这是全局重载：

```C++
inline void* operator new(size_t size){
	cout << "global new() " << endl;
	return malloc(size);
}

inline void* operator new[](size_t size){
	cout << "global new[]() " << endl;
	return malloc(size);
}
```

![1693054370785](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054370785.webp)

### 重载operator new / operator delete

在类中重载::operator new / ::operator delete更有用（array new / array delete重载也是一样的方法）：

![1693054370751](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054370751.webp)

![1693054370761](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054370761.webp)

我们重载这两个函数，是为了接管内存分配的工作，接管它了有什么用呢？以下是一个接口示例：

![1693054370795](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054370795.webp)

![1693054370804](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054370804.webp)

![1693054935255](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054935255.webp)



我们重载这两个函数，是为了接管内存分配的工作，接管它了有什么用呢？很有用，比如说可以做一个内存池（这个就是之前讲STL的时候的__pool_allocator）：

![1693055125452](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055125452.webp)

只有在上述的重载replacement new抛出异常的时候，才会调用相应的operator delete（这个需要自己去实现），因为在重载replacement new抛出异常，那么说明内存分配不成功，但是可能已经申请好内存，那么我们应该去处理申请好的这个内存。

### 重载new() / delete()

![1693055223016](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055223016.webp)

![image-20230519220900851](侯捷--C++内存管理机制.assets/image-20230519220900851.png)

![1693055223126](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055223126.webp)

### basic_string使用new(extra)扩充内存申请量

![1693055223079](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055223079.webp)

#### ①版本的例子(有缺点，多一个指针)

![1693055223109](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055223109.webp)

![1693055223094](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055223094.webp)

#### ②版本的例子(缺点:没有把内存还给系统)

![1693055288800](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055288800.webp)

![1693055288824](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055288824.webp)



#### ③static allocator(把分割内存的动作抽取出来增强代码复用性)

![1693055288848](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055288848.webp)

![1693055288752](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055288752.webp)

![1693055288786](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055288786.webp)

#### ④macro for static allocator(设计一个宏macro)

![1693055288836](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055288836.webp)

![1693055288813](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055288813.webp)



### 补充：new handler

![1693055376638](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055376638.webp)

当operator new没有能力为你分配出你所申请的memory，会抛出一个`bad_alloc`的异常。抛出异常之前会先（不止一次）调用一个==可由用户指定的handler==：

```c++
typedef void(*new_handler)();
new_handler set_new_handler(new_handler p) throw();
```

这个new handler一般做两件事情：

1. 让更多的内存可用
2. 调用abort()或者exit()

![1693055376661](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055376661.webp)









# 第二讲、分配器std::allocator

分配器分配途径：allocator—>allocate—>::operator new—>malloc

我们想要把operator new / delete抽取出来形成单独的一个类allocator，是的这个类和内存分配的分配细节剥离开，这样，需要内存管理的类，就调用allocator。这就是STL中分配器的实现思路。
![1693055435476](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055435476.webp)

![1693055452648](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055452648.webp)

还有一种实现方式，就是把上图黄色的这些东西定义成宏！具体的实现就不贴了，因为技术思想上没有改进，只是一个偷懒工作。

![1693055376617](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055376617.webp)

### VC6的标准分配器的实现

这个编译器的allocator没有任何特殊设计，单纯是调用malloc，分配是以对象元素为单位。

![1693055376684](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055376684.webp)

### BC5的标准分配器

和VC6相同，没有任何特殊设计。

![1693055376672](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055376672.webp)



### GNU4.9的标准分配器

同样，没有任何特殊设计，和VC6，BC5一样。这个文件中已经说明，这个分配器没有用，容器的分配器不使用它！

![1693055498233](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055498233.webp)

![1693055522994](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055522994.webp)



### GNU2.9的标准分配器

![1693055376695](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055376695.webp)

![1693055376650](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055376650.webp)

#### G2.9 std::alloc运行模式

为什么要把他放在最后讲？因为侯先生认为它设计得最好。它和GNU4.9中的__pool_allocator相同。

下面是他的设计总览图：

![1693055559744](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055559744.webp)

设计是内存池的思想。16个指针指向16条链表，每条链表分配不同大小的内存空间，从左往右每一条链表间隔8个字节。理论上是这样的，但是在代码实现的时候，是首先申请一个大的内存块，然后在大的内存块上进行切分，所以不同链表之间会有连线。

每个链表会申请nx8x20x2字节的内存空间，n代表第n条链表，20表示将内存最多切分成20个子内存块，2表示会申请2倍大小的内存用于战备。这些内存空间是cookie free的。

如果申请的内存大于128字节，那么就有malloc来管理，就不归这个pool管理了。

源码中deallocate没有free操作（源于先天设计缺陷），即只要被pool申请空间之后，就不会再还回操作系统了。对于一个程序来说，这个也没有什么大不了的，但是对于一台机器来说，不止运行一个程序，这是有弊端的。这可能就是为什么GNU4.9把它替换回去的原因？

> **==if语句中的判断，把常量写在左边==**！！！！！！！！！！！这样，如果把两个写成一个等号了编译就不会通过！！！！！！！！血泪教训！！！！！！！！！

![1693055574231](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055574231.webp)

![1693055591908](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055591908.webp)

![1693055631088](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055631088.webp)

![1693055631066](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055631066.webp)

![1693055631100](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055631100.webp)

![1693055685994](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055685994.webp)

![1693055685938](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055685938.webp)

![1693055685950](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055685950.webp)

![1693055686006](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055686006.webp)

![1693055685969](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055685969.webp)

![1693055685982](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055685982.webp)

![1693055778286](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055778286.webp)

![1693055778273](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055778273.webp)

![1693055778238](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055778238.webp)

![1693055778253](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693055778253.webp)



# 第三讲、C函数malloc、free

### VC6内存分配

![1693054636384](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636384.webp)

![1693054636470](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636470.webp)

![1693054636488](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636488.webp)

![1693054636506](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636506.webp)

![1693054636539](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636539.webp)

![1693054636523](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636523.webp)

![1693054636556](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636556.webp)

![1693054636573](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636573.webp)

![1693054636589](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636589.webp)

![1693054636605](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636605.webp)





### VC10内存分配(SBH深埋到OS底层去了)

![1693054636452](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636452.webp)



### malloc分配出的内存块

![1693056010361](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056010361.webp)

组成：

1. 头尾两个cookie存放当前内存块大小，是否分配出的信息
2. debug header用于debug
3. 真实分配的内存块

分配出的内存块大小必须是16字节的倍数，也就是16字节内存对齐。

### VC6和VC10的malloc比较

CRT ：C RunTime
SBH ：Small Block Heap

VC6：如果申请的内存小于1016，则是SBH服务，否则，用操作系统的HeapAlloc；
VC10：全部由操作系统的HeapAlloc分配内存，但是小内存的分配工作也被整合到HeapAlloc中了。

### VC6内存分配细节

也不是很细节，觉得记个大概工作流程就好了，太细的过久了也会忘记。



### SBH之始—`_heap_init()`和`__sbh_heap_init()`

![1693056028681](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056028681.webp)

![1693054636401](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636401.webp)

### SBH首次分配内存详细步骤

![1693054636573](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636573.webp)

总体的malloc如上图。**==总的来说，就是为了添加debug信息，申请具体的内存，方面内存管理。==**

1. `_heap_init()`做的事是分配16个头，16个头的数据结构如下，使用这个数据结构使得每个header能够管理1M的内存，1M内存能够快速分配，快速回收。一个header包括两个指针，一个指针指向自己管理的内存，一个指针指向管理内存中心。详细见步骤6。这些bit用于存放链表的状态，是否挂载上page。

	![1693054636401](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636401.webp)

2. `_ioinit()`负责第一次分配内存：根据是不是debug的模式，调用不同的内存分配策略。debug直接调用malloc，非debug则分配256字节的内存：

	![1693056146025](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056146025.webp)

3. `heap_alloc_dbg()`负责在debug模式下，加上一些调试信息（debug header），这些调试信息就是下面的数据结构CrtMemBlockHeader。这些调试信息包括：调试文件名，行号，数据大小，间隔符。绿色的地方就是填充我们最后要申请分配的内存。被分配的内存会被指针串起来形成一条链表。

	![1693056159710](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056159710.webp)

4. `_heap-alloc_base()`根据申请的大小来判断（是否大于1016，为什么是1016？因为要留给cookie八字节的内存）是要自己提供服务还是交由操作体统来服务。

5. `__sbh_alloc_block()`用于字节对齐，加首尾cookie。处理完之后的内存块如下图右上角的图。cookie利用最后的一个bit来代表这个内存块是否被分配出去，1表示已分配，0表示未分配。

	![1693056173096](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056173096.webp)

6. `__sbh_alloc_new_region()`：内存管理中心数据结构就是tagRegion，管理区块是不是被分配，在不在内存分配链表上。为了管理好1M的内存空间，花了16K的内存（tagRegion数据结构）。

	![1693056185124](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056185124.webp)

7. `__sbh_alloc_new_group()`：4K一个page。一个header管理1M内存，这个1M内存会被分成32块（组，每个组由32对指针组成，即双向链表），32块分别由内存管理中心的数据结构来管理，每一个块又会被分成8个page，一个page占4K，使用链表将这些page连起来，连起来之后首尾接到绿箭头指向的双向链表中。完成这些工作之后，申请一块内存，得到内存，初始化，内存申请就成功了。

	![1693056198978](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056198978.webp)

8. 最后将__sbh_alloc_new_region组好的内存填充到组中去。层层return会让用户拿到指向绿色开头的指针。

	![1693056209876](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056209876.webp)

### SBH第二（n）次分配内存

在一个page上继续进行切分，当前page不够用，去其他page找。header负责维护的状态应该发生变化。

![1693056221856](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056221856.webp)

### 内存释放

将一个内存块的cookie中的状态置为未分配，将内存变为两个指针，指向负责管理内存的链表，header中的bit也发生变化，表示未分配。

回收相邻的内存。应该有个合并策略（**==这就是为什么要有下cookie的原因==**），要不然会出现内存碎片的问题。

![1693056236547](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056236547.webp)

### free( p )

![1693054636687](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636687.webp)

p属于哪个header？
p属于哪个group？
p属于哪个free-list？

### 内存分配总结

上述的分配策略，总的思想是一个分段管理。至于为什么有16个头，32个组，1个头管理1M内存，这些都是经验值，有利于操作系统。

全回收的动作会被延缓，并不会只要归还所有内存之后就把这么多段的内存整合还给操作体统（defer）。当第二个全回收出现的时候才会把内存归还操作系统。



# 第四讲、Loki::allocator

Loki是一个C++库，但是不太成熟，现在不太维护了。

侯先生觉得它棒，其中一点是因为pool allocator那个版本的分配器，只要从操作系统中申请了内存，就不归还，而Loki就能解决这个问题。

主要有三个类，用户使用的是SmallObjAllocator。

和pool allocator思想相似，但是用数组代替链表，用索引代替指针。（有点像用数组来实现链表的思想）

![1693054636620](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636620.webp)

那Loki是怎么解决pool allocator的无法归还内存给操作系统的问题呢？



## Loki allocator，Chunk

![1693054636670](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636670.webp)

## Chunk::Allocate()--最低阶

![1693054636637](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636637.webp)





## Chunk::Deallocate()

![1693054636703](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636703.webp)

Chunk中的deallocate用索引记录链表头，如果要释放，就把要释放的块变成链表头，并改变记录链表头的索引，如下图：

![1693054636774](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636774.webp)

**==当一个chunk全回收的时候，就能把当前chunk还给操作系统！==**



## Fixed Allocator::Allocate()--第二层

![1693054636758](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693054636758.webp)





## GNU4.9分配器

分配器各有各的优势。

### new_allocator

实现出简谱的operator new和operator delete，这个好处就是能够重载。

### malloc_allocator

直接调用malloc和free，没有经过operator new / delete，所以没有重载的机会。

------

下面两种是两种智能型的分配器。

### array_allocator

允许**==分配一个已知且固定大小的内存块==**，静态分配，内存来自array object。用上这个分配器，大小固定的容器就无需在调用operator new / delete。

![1693056338303](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056338303.webp)

### debug_allocator

相当于一个warpper，可以包裹在任何分配器之上。它把用户的申请量添加了一些，然后由分配器回应，并以额外的内存存放size信息。

![1693056347592](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056347592.webp)

下面的三种分配器实现都是内存池的思想，追求的是不要分配过多的cookie。

### __mt_allocator

多线程的内存分配器。

### __pool_allocator

就是GNU2.9中的标准分配器，说了好多遍了，不再赘述。

### bitmap_allocator

一个高效能的分配器，使用bit-map追踪被使用和未被使用的内存块。

![1693056360789](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056360789.webp)

上图是一个super block
bitmap用于存放后面的block是否被分配出去
use-count表示已经分配了多少个
__mini_vector用来管理后面的block，指向block的头尾

bitmap的变化方向和_M_start变化方向相反。
如果一个super block用光了，就会新起一个super block，而且能够分配的内存会翻倍，如下图：
![1693056373199](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056373199.webp)

如果发生全回收，则可分配的内存减半。

全回收，使用另外一个__min_vector来登记（free list）全回收的super block，发生全回收，则分配内存减半，如下图是三次全回收的情况：

![1693056386788](https://cdn.jsdelivr.net/gh/xiaose-code/Pictures@main/img/1693056386788.webp)

如果free list中已经有64个super block，下一次再free的的super block，直接delete。