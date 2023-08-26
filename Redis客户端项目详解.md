# Redis客户端项目详解

## 1、typedef.h文件

这是一个预编译指令，用于防止重复包含头文件。如果之前没有定义过XG_HEAD_TYPEDEF，就定义它，否则跳过。

```C++
#ifndef XG_HEAD_TYPEDEF
#define XG_HEAD_TYPEDEF
```

------

判断代码是否在Microsoft Visual C++编译器下编译。

```C++
#ifdef _MSC_VER
```

------

在Microsoft Visual C++编译器下定义一个宏，用于禁用一些安全检查，使得可以使用一些不可移植的函数。这里的宏`_CRT_SECURE_NO_WARNINGS`用于禁用`scanf`、`sscanf`和`gets`等函数的使用警告。

```c++
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
```

------

如果之前没有定义过XG_MINGW，就定义XG_LINUX，表示代码在Linux平台下编译。

```C++
#ifndef XG_MINGW
#define XG_LINUX
```

------

这是一段C语言代码，用于包含标准库和其他头文件：

- time.h：包含了一些时间处理的函数和结构体。
- ctype.h：包含了一些字符处理的函数。
- stdio.h：包含了输入输出相关的函数和定义。
- stdlib.h：包含了一些常用的函数，如动态内存分配、字符串转换、随机数生成等。
- string.h：包含了一些字符串处理的函数。
- stdarg.h：包含了可变参数的函数处理宏定义。
- assert.h：包含了一个宏定义assert，用于调试时检查程序的正确性。

```c++
#include <time.h>
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h> 
#include <stdarg.h>
#include <assert.h>
```

------

### Linux系统下

这是一段C语言代码，用于包含一些Linux系统相关的头文件：

- grp.h: 包含了一些**操作组信息**的函数和结构体。
- pwd.h: 包含了一些**操作用户信息**的函数和结构体。
- errno.h: 定义了一些错误码，用于表示函数调用失败的原因。
- netdb.h: 包含了一些**网络相关**的函数和结构体。
- fcntl.h: 包含了一些**文件相关**的函数和常量定义。
- signal.h: 包含了一些**信号处理相关**的函数和常量定义。
- unistd.h: 包含了一些**系统调用相关**的函数和常量定义。
- dirent.h: 定义了一些**操作目录**的函数和结构体。
- termios.h: 包含了一些**操作终端**的函数和常量定义。
- pthread.h: 包含了一些**多线程相关**的函数和结构体。
- sys/time.h: 定义了一些**时间相关**的函数和结构体。
- sys/wait.h: 包含了一些**进程相关**的函数和常量定义。
- sys/stat.h: 包含了一些**文件状态**相关的函数和结构体。
- sys/file.h: 包含了一些**文件操作**相关的函数和常量定义。
- sys/types.h: 包含了一些系统类型的定义。
- netinet/in.h: 包含了一些**Internet地址族**相关的函数和结构体。
- sys/socket.h: 包含了一些**套接字相关**的函数和结构体。
- sys/sysinfo.h: 包含了一些**系统信息相关**的函数和结构体。

```
#include <grp.h>
#include <pwd.h>
#include <errno.h>
#include <netdb.h>
#include <fcntl.h>
#include <signal.h>
#include <unistd.h>
#include <dirent.h>
#include <termios.h>
#include <pthread.h>
#include <sys/time.h>
#include <sys/wait.h>
#include <sys/stat.h>
#include <sys/file.h>
#include <sys/types.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include <sys/sysinfo.h>
```

------



```C++
#ifndef MAX_PATH
#define MAX_PATH	256
#endif

#define TRUE 		1
#define FALSE 		0
#define Sleep(ms)	usleep((ms)*1000)

typedef int SOCKET;
```

- MAX_PATH: 如果之前没有定义过MAX_PATH，就定义为256，表示文件路径的最大长度。
- TRUE: 定义为1，表示真。
- FALSE: 定义为0，表示假。
- Sleep(ms): 定义了一个宏，用于将线程挂起指定的毫秒数，实现延时的效果。
- SOCKET: 定义了一个类型别名，表示一个套接字的文件描述符。

------



```c++
typedef enum
{
	eRED = 31,		//红
	eBLUE = 34,		//蓝
	eGREEN = 32,	//绿
	eWHITE = 37,	//白
	eYELLOW = 33	//黄
} E_CONSOLE_COLOR;
```

定义一个枚举类型E_CONSOLE_COLOR，用于表示控制台输出的颜色：

- eRED: 枚举成员，表示红色。
- eBLUE: 枚举成员，表示蓝色。
- eGREEN: 枚举成员，表示绿色。
- eWHITE: 枚举成员，表示白色。
- eYELLOW: 枚举成员，表示黄色。

------



```C++
inline static int getch()
{
	struct termios tm;
	struct termios old;
    
	int ch;
	int fd = STDIN_FILENO;

	if (tcgetattr(fd, &tm) < 0) return 0;
		
    old = tm;
	cfmakeraw(&tm);

	if (tcsetattr(fd, TCSANOW, &tm) < 0) return 0;
    
	ch = fgetc(stdin);

	if (tcsetattr(fd, TCSANOW, &old) < 0) return 0;

	return ch;
}
```

定义一个静态内联函数getch()，用于从控制台读取一个字符，不需要用户按下回车键：

- inline: 用于定义内联函数，可以提高函数调用的效率。

- static: 用于定义静态函数，可以避免函数名冲突。

- int: 函数返回值类型为整型。

- getch(): 函数名为getch。

- struct termios: 是一个结构体类型，包含了终端设备的属性，在头文件<termios.h>中。

  - 原型如下：

  - ```C++
    struct termios {
        tcflag_t c_iflag;  // 输入模式标志
        tcflag_t c_oflag;  // 输出模式标志
        tcflag_t c_cflag;  // 控制模式标志
        tcflag_t c_lflag;  // 本地模式标志
        cc_t c_cc[NCCS];   // 控制字符
    };
    ```

  - c_iflag: 输入模式标志，用于控制输入方式和控制字符的处理方式。

  - c_oflag: 输出模式标志，用于控制输出方式和控制字符的处理方式。

  - c_cflag: 控制模式标志，用于控制终端的控制模式，如波特率、数据位、停止位、校验位等。

  - c_lflag: 本地模式标志，用于控制终端的本地模式，如是否启用回显、是否启用信号处理等。

  - c_cc[NCCS]: 控制字符数组，用于存储控制字符的值，如终端的删除字符、退格字符、中断字符等。

- STDIN_FILENO: 标准输入文件描述符，表示控制台输入。

- tcgetattr(fd, &tm): 用于获取终端设备的属性，将其保存到tm结构体中，fd表示文件描述符。

- cfmakeraw(&tm): 用于将终端设备的属性设置为原始模式，即不进行任何处理。

- TCSANOW: 用于表示设置立即生效。

- tcsetattr(fd, TCSANOW, &tm): 用于设置终端设备的属性，将其设置为原始模式。

- fgetc(stdin): 从标准输入中读取一个字符，即从控制台读取一个字符。

- tcsetattr(fd, TCSANOW, &old): 用于恢复终端设备的属性，将其设置为之前保存的属性。

------

```C++
inline static void SetConsoleTextColor(E_CONSOLE_COLOR color)
{
    printf("\e[%dm", (int)(color));
}
```

定义一个静态内联函数SetConsoleTextColor()，用于设置控制台输出的文本颜色：

- inline: 用于定义内联函数，可以提高函数调用的效率。
- static: 用于定义静态函数，可以避免函数名冲突。
- void: 函数返回值类型为空。
- SetConsoleTextColor(E_CONSOLE_COLOR color): 函数名为SetConsoleTextColor，参数为E_CONSOLE_COLOR类型的变量color，表示要设置的颜色。
- printf("\e[%dm", (int)(color)): 使用printf函数输出控制台命令，将颜色设置为参数color表示的颜色。其中\e表示转义字符，[表示开始控制台命令，%d表示将color强制转换为整数并输出，m表示结束控制台命令。



### Windows系统下

```c++
#ifndef _UNICODE
#define _UNICODE
#endif
```

用于定义UNICODE宏。如果该宏未定义，则将其定义为1，表示使用Unicode字符集编码。Unicode是一种国际标准，用于字符编码，它包含了世界上几乎所有的字符，可以表示多种语言、符号等。通过定义UNICODE宏，程序可以使用Unicode字符集编写，并支持Unicode字符集的输入输出。

------

```C++
#include <windows.h>
#include <conio.h>
```

这是C语言中的头文件，用于在Windows操作系统下编写控制台应用程序。

- windows.h：Windows操作系统的头文件，其中包含了Windows API的各种函数、宏定义和数据类型等。
- conio.h：控制台输入输出头文件，其中包含了一些用于在控制台窗口中进行输入输出的函数，如getch()、putch()、cprintf()等。这些函数可以实现在控制台窗口中的光标移动、字符输出和键盘输入等操作。

------

```C++
#ifdef _MSC_VER
#define strcasecmp stricmp
#pragma warning(disable:4996)
#pragma comment(lib, "WS2_32.lib")
#endif
```

根据定义了_MSC_VER宏的情况下进行编译，该宏表示使用Microsoft Visual C++编译器。根据宏定义，进行了如下操作：

- 定义strcasecmp宏为stricmp：strcasecmp函数是Linux/Unix系统中的==**字符串比较函数**==，而Windows操作系统中没有该函数，使用stricmp函数可以实现相同的功能，因此将strcasecmp宏定义为stricmp。
- 禁用警告4996：4996警告表示使用了不安全的函数或者已经被删除的函数，禁用该警告可以避免编译时的警告信息。
- 添加WS2_32库：**==WS2_32是Windows Sockets的库文件==**，包含了在Windows操作系统中使用网络编程所需的函数和结构体等，通过添加该库文件可以使用网络编程相关的函数。

------

```C++
#define getch				_getch
#define snprintf			_snprintf
#define sleep(s)			Sleep((s)*1000)
#define localtime_r(a,b)	localtime_s(b,a)
```

这是一些C语言的宏定义，用于将函数名或者功能进行重定义，以便于在特定的编译环境中使用。

- \#define getch _ getch：将getch函数重定义为_ getch函数，_ getch是Windows系统中的一个函数，**==用于从控制台读取字符==**，与getch函数功能相同。
- \#define snprintf _ snprintf：将snprintf函数重定义为_ snprintf函数，_snprintf是Windows系统中的一个函数，**==用于将字符串格式化输出==**，与snprintf函数功能相同。
- \#define sleep(s) Sleep((s)*1000)：将sleep函数重定义为Sleep函数，Sleep是Windows系统中的一个函数，**==用于将当前线程挂起一段时间==**，与sleep函数功能相同。**==注意，这里将参数从秒改为毫秒。==**
- \#define localtime_r(a,b) localtime_s(b,a)：将localtime_r函数重定义为localtime_s函数，localtime_s是Windows系统中的一个函数，**==用于将时间戳转换为当地时间==**，与localtime_r函数功能相同。

------

```C++
typedef enum
{
	eRED = FOREGROUND_RED,
	eBLUE = FOREGROUND_BLUE,
	eGREEN = FOREGROUND_GREEN,
	eYELLOW = FOREGROUND_RED | FOREGROUND_GREEN,
	eWHITE = FOREGROUND_RED | FOREGROUND_BLUE | FOREGROUND_GREEN
} E_CONSOLE_COLOR;
```

这是一个C语言中的枚举类型定义，用于定义控制台文本颜色。

- typedef enum：定义一个枚举类型。
- eRED = FOREGROUND_RED：eRED表示红色，其值为FOREGROUND_RED，FOREGROUND_RED是Windows系统中的一个宏定义，表示红色前景色。
- eBLUE = FOREGROUND_BLUE：eBLUE表示蓝色，其值为FOREGROUND_BLUE，FOREGROUND_BLUE是Windows系统中的一个宏定义，表示蓝色前景色。
- eGREEN = FOREGROUND_GREEN：eGREEN表示绿色，其值为FOREGROUND_GREEN，FOREGROUND_GREEN是Windows系统中的一个宏定义，表示绿色前景色。
- eYELLOW = FOREGROUND_RED | FOREGROUND_GREEN：eYELLOW表示黄色，其值为FOREGROUND_RED | FOREGROUND_GREEN，表示红色和绿色前景色的组合。
- eWHITE = FOREGROUND_RED | FOREGROUND_BLUE | FOREGROUND_GREEN：eWHITE表示白色，其值为FOREGROUND_RED | FOREGROUND_BLUE | FOREGROUND_GREEN，表示红色、蓝色和绿色前景色的组合。

------

```c++
inline static void SetConsoleTextColor(E_CONSOLE_COLOR color)
{
	static HANDLE console = NULL;
	if (console == NULL) console = GetStdHandle(STD_OUTPUT_HANDLE);
	SetConsoleTextAttribute(console, color);
}
```

这是一个C语言中的函数定义，用于设置控制台文本颜色。

- inline static：表示该函数是一个内联函数，可以提高函数调用的效率。
- void：表示该函数没有返回值。
- SetConsoleTextColor(E_CONSOLE_COLOR color)：函数名为SetConsoleTextColor，参数为一个枚举类型的变量color，用于指定设置的文本颜色。
- static HANDLE console = NULL：定义一个静态变量console，用于保存控制台的句柄，初始值为NULL。
- if (console == NULL) console = GetStdHandle(STD_OUTPUT_HANDLE)：如果console变量还没有被赋值，则调用GetStdHandle函数获取控制台的句柄，并将其赋值给console变量。
- SetConsoleTextAttribute(console, color)：调用Windows API函数SetConsoleTextAttribute设置控制台文本颜色，参数为控制台句柄和指定的颜色值。

------

```C++
#ifndef XG_AIX
typedef char 				int8;
typedef long long			int64;
typedef unsigned long long	u_int64;
```

这是一个C语言中的条件编译指令，用于根据不同的操作系统定义不同的数据类型。

- \#ifndef XG_AIX：如果未定义XG_AIX宏，则执行以下语句。ifndef是if not defined的缩写，意思是如果宏没有被定义，则执行以下语句。
- typedef char int8：定义一个类型为int8的char型变量。
- typedef long long int64：定义一个类型为int64的long long型变量。
- typedef unsigned long long u_int64：定义一个类型为u_int64的unsigned long long型变量。这些类型的定义可能是为了保证在不同的操作系统上运行时，数据类型的大小和范围都是一致的。

------

```C++
typedef int					int32;
typedef short				int16;

typedef unsigned int		u_int;
typedef unsigned char		u_int8;
typedef unsigned char		u_char;
typedef unsigned long		u_long;
typedef unsigned short		u_short;

typedef unsigned int		u_int32;
typedef unsigned short		u_int16;
```

这是一个C语言中的数据类型定义，用于定义不同位数和有符号/无符号的整型变量。

- typedef int int32：定义一个类型为int32的int型变量。
- typedef short int16：定义一个类型为int16的short型变量。
- typedef unsigned int u_int：定义一个类型为u_int的unsigned int型变量。
- typedef unsigned char u_int8：定义一个类型为u_int8的unsigned char型变量。
- typedef unsigned char u_char：定义一个类型为u_char的unsigned char型变量。
- typedef unsigned long u_long：定义一个类型为u_long的unsigned long型变量。
- typedef unsigned short u_short：定义一个类型为u_short的unsigned short型变量。
- typedef unsigned int u_int32：定义一个类型为u_int32的unsigned int型变量。
- typedef unsigned short u_int16：定义一个类型为u_int16的unsigned short型变量。 这些类型的定义可能是为了在不同的平台和编译器下保证数据类型的大小和范围的一致性，方便程序的移植性。

------

```
#ifndef ARR_LEN
#define ARR_LEN(arr)	(sizeof(arr)/sizeof(arr[0]))
#endif

#define CHECK_FALSE_RETURN(FUNC) if (FUNC){} else return false
```

这是一个C语言中的宏定义，用于定义一些常用的宏命令，方便代码编写和阅读。

- \#ifndef ARR_LEN：如果未定义ARR_LEN宏，则执行以下语句。ifndef是if not defined的缩写，意思是如果宏没有被定义，则执行以下语句。
- \#define ARR_LEN(arr) (sizeof(arr)/sizeof(arr[0]))：定义ARR_LEN宏，用于计算数组arr的长度。sizeof是一个运算符，用于计算其操作数的字节数，所以sizeof(arr)表示数组arr的总字节数，sizeof(arr[0])表示数组arr中一个元素的字节数，两者相除即可得到数组的长度。
- \#endif：结束条件编译指令。
- \#define CHECK_FALSE_RETURN(FUNC) if (FUNC){} else return false：定义CHECK_FALSE_RETURN宏，用于检查一个函数调用的返回值是否为false，如果是，则直接返回false。这个宏可以简化代码的写法，避免重复书写相似的代码块。
  - \#define CHECK_FALSE_RETURN(FUNC)：定义一个名为CHECK_FALSE_RETURN的宏，其参数为一个函数调用表达式FUNC。
  - if (FUNC){} else return false：使用if-else语句判断函数调用的返回值是否为false，如果是，则直接返回false。这个宏可以简化代码的写法，避免重复书写相似的代码块。
  - 宏的使用示例：假设有一个名为func的函数，其返回值类型为bool，可以使用CHECK_FALSE_RETURN(func())来检查函数调用的返回值是否为false，如果是，则直接返回false。

------

## 2、ResPool.h文件

```C++
#ifndef XG_RESPOOL_H
#define XG_RESPOOL_H
```

这是一个C语言中的条件编译指令，用于防止头文件的重复包含。

- \#ifndef XG_RESPOOL_H：如果未定义XG_RESPOOL_H宏，则执行以下语句。ifndef是if not defined的缩写，意思是如果宏没有被定义，则执行以下语句。
- \#define XG_RESPOOL_H：定义XG_RESPOOL_H宏，防止头文件的重复包含。
- \#endif：结束条件编译指令。

------

```C++
#include <ctime>
#include <mutex>
#include <vector>
#include <string>
#include <memory>
#include <thread>
#include <sstream>
#include <iostream>
#include <iterator>
#include <typeinfo>
#include <algorithm>
#include <functional>
```

- \#include <ctime>：定义了时间处理相关函数和类型。
- #include <mutex>：定义了**==互斥量相关函数和类型，用于线程同步==**。
- #include <vector>：定义了**==动态数组==**相关函数和类型。
- #include <string>：定义了字符串相关函数和类型。
- #include <memory>：定义了**==内存管理==**相关函数和类型，如智能指针等。
- #include <thread>：定义了**==线程相关==**函数和类型。
- #include <sstream>：定义了**==字符串流==**相关函数和类型。
- #include <iostream>：定义了输入输出流相关函数和类型。
- #include <iterator>：定义了迭代器相关函数和类型。
- #include <typeinfo>：定义了**==类型信息==**相关函数和类型。
- #include <algorithm>：定义了各种算法相关函数和类型。
- #include <functional>：定义了**==函数对象和函数适配器==**相关函数和类型。

------

这是一个名为 `ResPool` 的类模板，代表资源池。它包含以下成员：

1. 内部类 Data ，代表数据项，包含以下成员：
   - `num`：数据项被获取的次数。
   - `utime`：数据项的最近一次被获取的时间。
   - `data`：存储数据项的智能指针。
   - `get()`：获取数据项的智能指针，并更新 `num` 和 `utime` 的值。
2. `mutex mtx`：一个互斥量，用于保证线程安全。
3. 成员变量 `maxlen`：缓存中存储数据项的最大数量。
4. 成员变量 `timeout`：缓存中存储数据项的超时时间。
5. 成员变量 `vec`：用于存储数据项的容器，类型为 `vector<Data>`。
6. 成员变量 `func`：缓存的创建器，类型为 `function<shared_ptr<T>()>`。
7. `shared_ptr<T> get()`：从缓存中获取一个数据项。如果缓存中没有可用的数据项，则调用创建器创建一个新的数据项。如果缓存已满，则删除一个 `use_count` 为 1 的数据项，并将新的数据项添加到缓存中。如果创建器返回空指针，则返回空指针。如果获取到的数据项不可用，则重新获取数据项。
8. `void clear()`：清空缓存中的所有数据项。
9. `int getLength() const`：获取缓存的最大长度。
10. `int getTimeout() const`：获取缓存的超时时间。
11. `void disable(shared_ptr<T> data)`：禁用一个数据项。
12. `void setLength(int maxlen)`：设置缓存的最大长度。
13. `void setTimeout(int timeout)`：设置缓存的超时时间。
14. `void setCreator(function<shared_ptr<T>()> func)`：设置缓存的创建器。
15. 构造函数：
    - `ResPool(int maxlen = 8, int timeout = 60)`：使用默认的创建器创建一个资源池。
    - `ResPool(function<shared_ptr<T>()> func, int maxlen = 8, int timeout = 60)`：使用指定的创建器创建一个资源池。

------

### 智能指针（可能是一个亮点）

```C++
class Data
{
public:
	int num;// 表示数据被获取的次数
	time_t utime;// 表示数据最后一次被获取的时间
    
	// 表示数据的智能指针，T是一个类型参数，可以是任何类型
    shared_ptr<T> data;
	
    // 成员函数，返回数据的智能指针，并更新utime和num
	shared_ptr<T> get()
	{
		utime = time(NULL);
		num++;
		return data;
	}
    
    // 构造函数，接受一个智能指针作为参数，并调用update函数更新成员变量
	Data(shared_ptr<T> data)
	{
		update(data);
	}
    
    // 成员函数，接受一个智能指针作为参数，更新成员变量num、data和utime
	void update(shared_ptr<T> data)
	{
		this->num = 0;
		this->data = data;
		this->utime = time(NULL);
	}
};
```

> 智能指针是一个类，其行为类似于指针，但提供了自动内存管理的功能。它可以自动地分配和释放内存，避免了手动管理内存的问题，如内存泄漏、悬挂指针等。

在C++中，智能指针主要有以下几种类型：

1. auto_ptr：是 C++98 标准中定义的一种智能指针，其主要功能是在对象生命周期结束时自动释放对象。它的特点是只能有一个 `auto_ptr` 指向一个对象（即不能进行拷贝构造和赋值操作，因为它们会导致原有的指针被置空），所以它的使用范围比较有限，一般用于局部变量的管理和资源所有权的转移。由于其存在多种缺陷，如无法处理数组、无法与标准库容器一起使用等，因此在C++11中已经被弃用，推荐使用`unique_ptr`、`shared_ptr`等智能指针。
2. shared_ptr：多个指针可以共享同一个对象，并在最后一个引用被销毁时自动释放内存。
3. unique_ptr：独占指针，只能有一个指针指向一个对象，当指针被销毁时，它所指向的对象也会被释放。
4. weak_ptr：弱引用指针，不会增加对象的引用计数，只能用于shared_ptr的管理下，用于解决shared_ptr的循环引用问题。

------

```C++
protected:
	mutex mtx;
	int maxlen;
	int timeout;
	vector<Data> vec;
	function<shared_ptr<T>()> func;
```

这是一个C++类定义中的成员变量，包括：

- mutex mtx：互斥量，用于线程同步。
- int maxlen：缓存中最大存储的数据条数。
- int timeout：缓存中数据的超时时间，单位为秒。
- vector vec：存储数据的容器，类型为Data，即前面提到的数据类。
- function<shared_ptr()> func：一个函数对象，返回类型为shared_ptr，用于提供新数据的获取方式。

------

```C++
int len = 0;
int idx = -1;
shared_ptr<T> tmp;
time_t now = time(NULL);
```

1. `len`：缓存中数据项的数量，初始化为0。
2. `idx`：第一个空闲数据项的下标，初始化为-1。
3. `tmp`：临时变量，用于保存可复用的数据项。
4. `now`：当前时间，用于判断数据项是否已经过期。

------

```C++
mtx.lock();
	len = vec.size();
	for (int i = 0; i < len; i++)
	{
		Data& item = vec[i];
		if (item.data.get() == NULL || item.data.use_count() == 1)
		{
			if (tmp = item.data)
			{
				if (item.num < 100 && item.utime + timeout > now)
				{
					shared_ptr<T> data = item.get();
					mtx.unlock();
					return data;
				}
				item.data = NULL;
			}
			idx = i;
		}
	}
mtx.unlock();
```

这是 `get()` 函数中的一部分，在使用互斥锁 `mtx` 保护缓存访问的情况下，查找缓存中是否有可用的数据。具体流程如下：

1. 加锁，保证在遍历缓存时其他线程不会修改缓存。
2. 获取缓存的大小 `len`。
3. 遍历缓存中的所有数据，使用引用方式获取每个数据项。
4. 如果该数据项的数据指针为空或者引用计数为1，说明该数据项是空闲状态或者已经失效，可以重新使用。
5. 如果该数据项的数据指针不为空，则先将其保存到 `tmp` 变量中。
6. 如果该数据项的 `num` 值小于100且距离上一次更新时间不超过超时时间，则说明该数据项是可用的，直接调用 `item.get()` 获取数据并返回。
7. 如果该数据项不可用，则将其数据指针设置为空，表示该数据项已经失效。
8. 如果遍历过程中找不到可用的数据项，则记录下第一个空闲的数据项的下标 `idx`，用于后续新数据的存储。
9. 解锁，允许其他线程访问缓存。

------

```C++
if (idx < 0)
{
	if (len >= maxlen) return shared_ptr<T>();
	shared_ptr<T> data = func();
	if (data.get() == NULL) return data;
	mtx.lock();
	if (vec.size() < maxlen) vec.push_back(data);
	mtx.unlock();
	return data;
}
```

这是 `get()` 函数中的一部分，用于创建新的数据项并将其存储到缓存中。具体流程如下：

1. 如果找不到空闲数据项，则需要创建新的数据项。
2. 如果缓存中已经存储的数据项数量已经达到了最大值 `maxlen`，则无法再创建新的数据项，直接返回空的共享指针。
3. 调用 `func()` 函数创建新的数据项 `data`。
4. 如果创建的数据项为空，则说明创建失败，直接返回空的共享指针。
5. 加锁，保证在修改缓存时其他线程不会修改缓存。
6. 如果缓存中数据项的数量还没有达到最大值 `maxlen`，则将新创建的数据项存储到缓存中。
7. 解锁，允许其他线程访问缓存。
8. 返回新创建的数据项的共享指针。

------

```C++
shared_ptr<T> data = func();
if (data.get() == NULL) return data;	
mtx.lock();
vec[idx].update(data);
mtx.unlock();
return data;
```

这是 `get()` 函数中的一部分，用于将新获取到的数据存储到缓存中。具体流程如下：

1. 调用 `func()` 函数创建新的数据项 `data`。
2. 如果创建的数据项为空，则说明创建失败，直接返回空的共享指针。
3. 加锁，保证在修改缓存时其他线程不会修改缓存。
4. 将新创建的数据项存储到缓存中的空闲数据项 `idx` 中，即调用 `vec[idx].update(data)`。
5. 解锁，允许其他线程访问缓存。
6. 返回新创建的数据项的共享指针。

------

```C++
auto grasp = [&](){
	int len = 0;
	int idx = -1;
	shared_ptr<T> tmp;
	time_t now = time(NULL);

	mtx.lock();
	len = vec.size();
	for (int i = 0; i < len; i++)
	{
		Data& item = vec[i];
		if (item.data.get() == NULL || item.data.use_count() == 1)
		{
			if (tmp = item.data)
			{
				if (item.num < 100 && item.utime + timeout > now)
				{
					shared_ptr<T> data = item.get();
					mtx.unlock();
					return data;
				}
				item.data = NULL;
			}
			idx = i;
		}
	}
	mtx.unlock();

	if (idx < 0)
	{
		if (len >= maxlen) return shared_ptr<T>();
		shared_ptr<T> data = func();
		if (data.get() == NULL) return data;
		mtx.lock();
		if (vec.size() < maxlen) vec.push_back(data);
		mtx.unlock();
		return data;
	}

	shared_ptr<T> data = func();
	if (data.get() == NULL) return data;
			
	mtx.lock();
	vec[idx].update(data);
	mtx.unlock();
	return data;
};
```

这是一个 lambda 表达式，定义了一个名称为 `grasp` 的函数对象。该函数对象主要用于从缓存中获取可用的数据项，并在必要时创建新的数据项。具体流程如下：

1. 定义局部变量 `len`、`idx`、`tmp` 和 `now`，分别用于记录缓存中数据项的数量、空闲数据项的下标、可复用的数据项、当前时间。
2. 加锁，保证在读取和修改缓存时其他线程不会修改缓存。
3. 遍历缓存中的所有数据项，找到第一个空闲数据项的下标 `idx`，并且记录可复用的数据项 `tmp`。
4. 如果找到了可复用的数据项 `tmp`，则检查其使用次数是否小于100且未过期，如果满足条件，则返回这个数据项，否则将其标记为空。
5. 解锁，允许其他线程访问缓存。
6. 如果找不到可复用的数据项，则需要创建新的数据项。
7. 如果缓存中已经存储的数据项数量已经达到了最大值 `maxlen`，则无法再创建新的数据项，直接返回空的共享指针。
8. 调用 `func()` 函数创建新的数据项 `data`。
9. 如果创建的数据项为空，则说明创建失败，直接返回空的共享指针。
10. 加锁，保证在修改缓存时其他线程不会修改缓存。
11. 如果缓存中数据项的数量还没有达到最大值 `maxlen`，则将新创建的数据项存储到缓存中。
12. 如果找到了空闲数据项 `idx`，则将新创建的数据项存储到这个位置上。
13. 解锁，允许其他线程访问缓存。
14. 返回新创建的数据项的共享指针。

------

```C++
void clear()
{
	lock_guard<mutex> lk(mtx);
	vec.clear();
}
```

这是一个名为 `clear` 的函数，用于清空缓存中的所有数据项。具体流程如下：

1. 使用 `lock_guard` 对 `mtx` 进行加锁，保证在清空缓存时其他线程不会修改缓存。
2. 调用 `vec` 的 `clear()` 函数，将缓存中的所有数据项清空。
3. `lock_guard` 在函数执行完后会自动析构，释放对 `mtx` 的锁定。

------

```C++
int getLength() const
{
	return maxlen;
}
```

这是一个名为 `getLength` 的函数，用于获取缓存中存储数据项的最大数量。具体流程如下：

1. 函数声明了 `const` 限定符，表示该函数不会修改对象的状态。
2. 返回成员变量 `maxlen`，即缓存中存储数据项的最大数量。

------

```C++
int getTimeout() const
{
	return timeout;
}
```

这是一个名为 `getTimeout` 的函数，用于获取缓存中存储数据项的超时时间。具体流程如下：

1. 函数声明了 `const` 限定符，表示该函数不会修改对象的状态。
2. 返回成员变量 `timeout`，即缓存中存储数据项的超时时间。

------

```C++
void disable(shared_ptr<T> data)
{
	lock_guard<mutex> lk(mtx);
	for (Data& item : vec)
	{
		if (data == item.data)
		{
			item.data = NULL;
			break;
		}
	}
}
```

这是一个名为 `disable` 的函数，用于禁用缓存中的某个数据项。具体流程如下：

1. 使用 `lock_guard` 对 `mtx` 进行加锁，保证在禁用数据项时其他线程不会修改缓存。
2. 遍历缓存中的所有数据项，找到与参数 `data` 相同的数据项。
3. 如果找到了相同的数据项，则将其标记为空。
4. 使用 `break` 语句跳出循环，因为禁用的数据项只需要找到一次即可。
5. `lock_guard` 在函数执行完后会自动析构，释放对 `mtx` 的锁定。

------

```C++
void setLength(int maxlen)
{
	lock_guard<mutex> lk(mtx);
	this->maxlen = maxlen;
	if (vec.size() > maxlen) vec.clear();
}
```

这是一个名为 `setLength` 的函数，用于设置缓存中存储数据项的最大数量。具体流程如下：

1. 使用 `lock_guard` 对 `mtx` 进行加锁，保证在设置最大数量时其他线程不会修改缓存。
2. 将参数 `maxlen` 的值赋值给成员变量 `maxlen`，即缓存中存储数据项的最大数量。
3. 如果缓存中存储的数据项数量已经超过了最大数量 `maxlen`，则调用 `vec` 的 `clear()` 函数，将缓存中的所有数据项清空。
4. `lock_guard` 在函数执行完后会自动析构，释放对 `mtx` 的锁定。

------

```C++
void setTimeout(int timeout)
{
	lock_guard<mutex> lk(mtx);
	this->timeout = timeout;
	if (timeout <= 0) vec.clear();
}
```

这是一个名为 `setTimeout` 的函数，用于设置缓存中存储数据项的超时时间。具体流程如下：

1. 使用 `lock_guard` 对 `mtx` 进行加锁，保证在设置超时时间时其他线程不会修改缓存。
2. 将参数 `timeout` 的值赋值给成员变量 `timeout`，即缓存中存储数据项的超时时间。
3. 如果超时时间小于等于0，则调用 `vec` 的 `clear()` 函数，将缓存中的所有数据项清空。
4. `lock_guard` 在函数执行完后会自动析构，释放对 `mtx` 的锁定。

------

```C++
void setCreator(function<shared_ptr<T>()> func)
{
	lock_guard<mutex> lk(mtx);
	this->func = func;
	this->vec.clear();
}
```

这是一个名为 `setCreator` 的函数，用于设置缓存的创建器。具体流程如下：

1. 使用 `lock_guard` 对 `mtx` 进行加锁，保证在设置创建器时其他线程不会修改缓存。
2. 将参数 `func` 的值赋值给成员变量 `func`，即缓存的创建器。
3. 调用 `vec` 的 `clear()` 函数，将缓存中的所有数据项清空，因为创建器可能已经发生变化，之前的数据项可能已经无效。
4. `lock_guard` 在函数执行完后会自动析构，释放对 `mtx` 的锁定。

------

```C++
ResPool(int maxlen = 8, int timeout = 60)
{
	this->timeout = timeout;
	this->maxlen = maxlen;
}
```

这是一个名为 `ResPool` 的构造函数，用于初始化缓存池的最大数量和超时时间。具体流程如下：

1. 将参数 `timeout` 的值赋值给成员变量 `timeout`，即缓存中存储数据项的超时时间。
2. 将参数 `maxlen` 的值赋值给成员变量 `maxlen`，即缓存中存储数据项的最大数量。

------

```C++
ResPool(function<shared_ptr<T>()> func, int maxlen = 8, int timeout = 60)
{
	this->timeout = timeout;
	this->maxlen = maxlen;
	this->func = func;
}
```

这是一个名为 `ResPool` 的构造函数，用于初始化缓存池的最大数量、超时时间和创建器。具体流程如下：

1. 将参数 `timeout` 的值赋值给成员变量 `timeout`，即缓存中存储数据项的超时时间。
2. 将参数 `maxlen` 的值赋值给成员变量 `maxlen`，即缓存中存储数据项的最大数量。
3. 将参数 `func` 的值赋值给成员变量 `func`，即缓存的创建器。

------

## 3、RedisConnect.h文件

```C++
#include <errno.h>
#include <netdb.h>
#include <fcntl.h>
#include <signal.h>
#include <sys/time.h>
#include <sys/wait.h>
#include <sys/stat.h>
#include <sys/file.h>
#include <sys/types.h>
#include <sys/ioctl.h>
#include <arpa/inet.h>
#include <sys/epoll.h>
#include <sys/statfs.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <sys/syscall.h>
```

这是一个包含多个头文件的代码段，这些头文件定义了一些系统调用、函数和常量，用于进行网络编程、文件操作等。具体来说，这些头文件包含以下内容：

- `errno.h`：定义了错误码，用于标识系统调用返回的错误。
- `netdb.h`：定义了与网络相关的数据结构和函数，如 `getaddrinfo()`、`getnameinfo()` 等。
- `fcntl.h`：定义了文件控制操作，**==如设置文件状态标志、锁定文件、修改文件描述符==**等。
- `signal.h`：定义了信号处理相关的函数和常量，如 `signal()`、`SIGINT` 等。
- `sys/time.h`：定义了时间相关的数据结构和函数，如 `struct timeval`、`gettimeofday()` 等。
- `sys/wait.h`：定义了进程等待相关的函数和常量，如 `waitpid()`、`WNOHANG` 等。
- `sys/stat.h`：定义了文件状态相关的数据结构和函数，如 `struct stat`、`stat()` 等。
- `sys/file.h`：定义了文件锁定相关的函数和常量，如 `flock()`、`LOCK_EX` 等。
- `sys/types.h`：定义了一些基本数据类型，如 `pid_t`、`off_t` 等。
- `sys/ioctl.h`：定义了**输入输出控制相关**的函数和常量，如 `ioctl()`、`FIONREAD` 等。
- `arpa/inet.h`：定义了一些与**互联网地址相关**的函数，如 `inet_addr()`、`inet_ntoa()` 等。
- `sys/epoll.h`：定义了一些与 I/O 多路复用相关的函数和常量，如 `epoll_create()`、`EPOLLIN` 等。
- `sys/statfs.h`：定义了一些与**文件系统状态相关**的函数，如 `statfs()`、`struct statfs` 等。
- `sys/socket.h`：定义了一些与套接字相关的数据结构和函数，如 `struct sockaddr`、`socket()`、`bind()`、`listen()`、`accept()` 等。
- `netinet/in.h`：定义了一些与 Internet 协议族相关的数据结构和函数，如 `struct sockaddr_in`、`htons()`、`inet_pton()` 等。
- `sys/syscall.h`：定义了一些**系统调用的编号**，如 `SYS_gettid`。

------

```C++
#define ioctlsocket ioctl
#define INVALID_SOCKET (SOCKET)(-1)
typedef int SOCKET;
```

这是一个定义了 `SOCKET` 类型和宏的代码段，用于网络编程中代表套接字。具体来说，这段代码定义了以下内容：

- `SOCKET`：代表套接字的数据类型，是一个整型。
- `INVALID_SOCKET`：表示**无效的套接字**，它的值为 `-1`。
- `ioctlsocket`：将 `ioctl()` 函数重命名为 `ioctlsocket`，方便在 Windows 平台上使用。 这段代码可能是用于将 Linux 平台的网络编程代码移植到 Windows 平台上使用，因为在 Windows 平台上，套接字的类型是 `SOCKET`，而不是 Linux 平台上的整型。因此，通过定义 `SOCKET` 和 `INVALID_SOCKET`，可以在代码中统一使用 `SOCKET` 类型，并且在 Windows 平台上使用 `ioctlsocket()` 函数时，可以直接将其替换为 `ioctl()` 函数。

------

### static bool IsSocketTimeout()

```C++
static bool IsSocketTimeout()
{
#ifdef XG_LINUX
	return errno == 0 || errno == EAGAIN || errno == EWOULDBLOCK || errno == EINTR;
#else
	return WSAGetLastError() == WSAETIMEDOUT;
#endif
}
```

这是一个静态函数，用于判断套接字是否超时。具体来说，该函数的实现包括以下内容：

- `IsSocketTimeout()`：函数名，用于判断套接字是否超时。
- `#ifdef XG_LINUX`：条件编译指令，用于判断是否为 Linux 平台。如果是 Linux 平台，则执行下面的代码块。
- `errno`：代表系统调用返回的错误码。
- `EAGAIN`、`EWOULDBLOCK`、`EINTR`：代表不同的错误码，分别表示操作暂时无法完成、操作被阻塞、操作被中断。
- `WSAGetLastError()`、`WSAETIMEDOUT`：Windows 平台下的函数和错误码，分别表示**获取最近一次操作的错误码**和**超时错误码**。
- `return`：返回一个布尔值，判断套接字是否超时。如果是 Linux 平台，判断 `errno` 是否为 0、`EAGAIN`、`EWOULDBLOCK`、`EINTR` 中的一种；如果是 Windows 平台，判断 `WSAGetLastError()` 是否等于 `WSAETIMEDOUT`。 该函数的作用是判断套接字是否超时，主要用于网络编程中的超时处理。**如果套接字超时，可以根据返回值来进行相应的处理，如重新连接、重新发送数据**等。

------

### static void SocketClose()

```C++
static void SocketClose(SOCKET sock)
{
	if (IsSocketClosed(sock)) return;
#ifdef XG_LINUX
	::close(sock);
#else
	::closesocket(sock);
#endif
}
```

这是一个静态函数，用于关闭套接字。具体来说，该函数的实现包括以下内容：

- `SocketClose()`：函数名，用于关闭套接字。
- `sock`：代表要关闭的套接字。
- `IsSocketClosed(sock)`：判断套接字是否已经关闭，如果已经关闭则直接返回，不进行关闭操作。
- `#ifdef XG_LINUX`：条件编译指令，用于判断是否为 Linux 平台。如果是 Linux 平台，则执行下面的代码块。
- `::close(sock)`：关闭套接字，使用了 Linux 平台下的系统调用 `close()`。
- `::closesocket(sock)`：关闭套接字，使用了 Windows 平台下的函数 `closesocket()`。 该函数的作用是关闭套接字，主要用于网络编程中的资源释放。如果不及时关闭套接字，可能会导致资源占用过多，影响程序的性能和稳定性。因此，在网络编程中，及时关闭套接字是非常重要的。

------

### static bool IsSocketClosed()

```C++
static bool IsSocketClosed(SOCKET sock)
{
	return (sock == INVALID_SOCKET) || (sock < 0);
}
```

这是一个静态函数，用于判断套接字是否已经关闭。具体来说，该函数的实现包括以下内容：

- `IsSocketClosed()`：函数名，用于判断套接字是否已经关闭。
- `sock`：代表要判断的套接字。
- `INVALID_SOCKET`：代表无效的套接字，其值为 -1。
- `return`：返回一个布尔值，判断套接字是否已经关闭。如果套接字等于 `INVALID_SOCKET` 或套接字小于 0，则认为套接字已经关闭。 该函数的作用是判断套接字是否已经关闭，主要用于网络编程中的错误处理。如果套接字已经关闭，则可能需要重新建立连接或者进行其他的处理。

------

### static bool SocketSetSendTimeout()

```C++
static bool SocketSetSendTimeout(SOCKET sock, int timeout)
{
#ifdef XG_LINUX
	struct timeval tv;
	tv.tv_sec = timeout / 1000;			// 将毫秒级别的超时时间转换为秒级别的超时时间
	tv.tv_usec = timeout % 1000 * 1000;	// 将毫秒级别的超时时间转换为微秒级别的超时时间
	return setsockopt(sock, SOL_SOCKET, SO_SNDTIMEO, (char*)(&tv), sizeof(tv)) == 0;
#else
	return setsockopt(sock, SOL_SOCKET, SO_SNDTIMEO, (char*)(&timeout), sizeof(timeout)) == 0;
#endif
}
```

这是一个静态函数，用于设置套接字的发送超时时间。具体来说，该函数的实现包括以下内容：

- `SocketSetSendTimeout()`：函数名，用于设置套接字的发送超时时间。

- `sock`：代表要设置的套接字。

- `timeout`：代表发送超时时间，单位为毫秒。

- `#ifdef XG_LINUX`：条件编译指令，用于判断是否为 Linux 平台。如果是 Linux 平台，则执行下面的代码块。

- `struct timeval`：Linux 平台下用于设置超时时间的结构体。

  - 结构体原型如下：

  - ```C++
    struct timeval {
        time_t tv_sec;  // 秒数部分
        suseconds_t tv_usec;  // 微秒数部分
    };
    ```

  - 类型为 `time_t`，通常是一个带符号的长整型数值

  - 类型为 `suseconds_t`，通常是一个带符号的整型数值。

- `tv.tv_sec`：代表秒数部分，是一个长整型数值。

- `tv.tv_usec`：代表微秒数部分，是一个长整型数值。

- `setsockopt()`：Linux 平台下用于设置套接字选项的函数。

- `SOL_SOCKET`：代表套接字选项的层级。

- `SO_SNDTIMEO`：代表发送超时时间的套接字选项。

- `(char*)(&tv)`：代表指向超时时间结构体的指针。

- `sizeof(tv)`：代表超时时间结构体的大小。

- `return`：返回一个布尔值，判断设置超时时间是否成功。如果设置成功，返回 true；否则返回 false。

- `setsockopt()`：Windows 平台下用于设置套接字选项的函数。

- `SO_SNDTIMEO`：代表发送超时时间的套接字选项。

- `(char*)(&timeout)`：代表指向超时时间的指针。

- `sizeof(timeout)`：代表超时时间的大小。 该函数的作用是设置套接字的发送超时时间，主要用于网络编程中的超时处理。如果发送数据的时候超时，可以根据超时时间来进行相应的处理，如重新发送数据等。

------

### static bool SocketSetRecvTimeout()

```C++
static bool SocketSetRecvTimeout(SOCKET sock, int timeout)
{
#ifdef XG_LINUX
	struct timeval tv;
	tv.tv_sec = timeout / 1000;
	tv.tv_usec = timeout % 1000 * 1000;
	return setsockopt(sock, SOL_SOCKET, SO_RCVTIMEO, (char*)(&tv), sizeof(tv)) == 0;
#else
	return setsockopt(sock, SOL_SOCKET, SO_RCVTIMEO, (char*)(&timeout), sizeof(timeout)) == 0;
#endif
}
```

这是一个用于设置套接字接收超时时间的函数。在 Linux 等类 Unix 系统中，该函数将 `timeout` 的值转换为 `struct timeval` 结构体类型的值，并使用 `setsockopt()` 函数设置套接字的 `SO_RCVTIMEO` 选项，以便于在接收数据时能够自动超时。在 Windows 系统中，该函数直接使用 `setsockopt()` 函数设置套接字的 `SO_RCVTIMEO` 选项，将 `timeout` 的值作为超时时间传递给该选项即可。 具体来说，该函数的实现过程如下：

1. 在 Linux 等类 Unix 系统中，首先创建一个 `struct timeval` 结构体类型的变量 `tv`；
2. 将 `timeout` 的值除以 1000，得到秒数部分，赋值给 `tv.tv_sec` 字段；
3. 将 `timeout` 的值对 1000 取模，得到毫秒数部分，将毫秒数部分乘以 1000，得到微秒数部分，赋值给 `tv.tv_usec` 字段；
4. 调用 `setsockopt()` 函数设置套接字的 `SO_RCVTIMEO` 选项，将 `tv` 变量的地址作为参数传递给该选项；
5. 在 Windows 系统中，直接调用 `setsockopt()` 函数设置套接字的 `SO_RCVTIMEO` 选项，将 `timeout` 的值作为超时时间传递给该选项；
6. 如果设置成功，则返回 `true`，否则返回 `false`。 总之，该函数的作用是为套接字设置接收数据的超时时间，以便于在接收数据时能够自动超时，避免阻塞。

------

```C++
SOCKET SocketConnectTimeout(const char* ip, int port, int timeout)
```

这是一个用于在指定时间内连接指定 IP 地址和端口的函数。该函数的实现过程如下：

1. 首先创建一个 `u_long` 类型的变量 `mode`，并将其赋值为 1；
2. 创建一个 `struct sockaddr_in` 类型的变量 `addr`，并将其初始化，设置其地址族为 `AF_INET`，端口号为指定的 `port`，IP 地址为指定的 `ip`；
3. 调用 `socket()` 函数创建一个套接字，如果创建失败则返回 `INVALID_SOCKET`；
4. 使用 `ioctlsocket()` 函数将该套接字设置为非阻塞模式；
5. 调用 `connect()` 函数尝试连接指定的 IP 地址和端口，如果连接成功则将该套接字设置回阻塞模式，并返回该套接字句柄；
6. 如果连接失败，则根据操作系统的不同进行不同的处理：
   - 在 Linux 系统中，首先创建一个 `struct epoll_event` 类型的变量 `ev`，并将其初始化，设置其关注事件为 `EPOLLOUT`、`EPOLLERR` 和 `EPOLLHUP`，然后调用 `epoll_ctl()` 函数将该套接字加入到 epoll 中；
   - 然后调用 `epoll_wait()` 函数等待事件发生，如果等待超时则关闭 epoll，关闭套接字，并返回 `INVALID_SOCKET`；如果等待成功，则根据事件类型进行处理：如果事件类型包含 `EPOLLOUT`，则说明连接成功，此时需要使用 `getsockopt()` 函数获取套接字的错误码，如果错误码为 0，则说明连接成功，将套接字设置回阻塞模式，关闭 epoll，返回该套接字句柄，否则关闭 epoll，关闭套接字，返回 `INVALID_SOCKET`；
   - 在 Windows 系统中，首先创建一个 `struct timeval` 类型的变量 `tv`，并设置其秒数和微秒数，然后创建一个 `fd_set` 类型的变量 `ws`，并将该套接字加入到 `ws` 中，最后调用 `select()` 函数等待事件发生，如果等待超时则关闭套接字，返回 `INVALID_SOCKET`；如果等待成功，则使用 `getsockopt()` 函数获取套接字的错误码，如果错误码为 0，则说明连接成功，将套接字设置回阻塞模式，返回该套接字句柄，否则关闭套接字，返回 `INVALID_SOCKET`；
7. 最后关闭套接字，返回 `INVALID_SOCKET`。 总之，该函数的作用是在指定时间内连接指定 IP 地址和端口，如果连接成功则返回该套接字句柄，否则返回 `INVALID_SOCKET`。

------

```C++
u_long mode = 1;

struct sockaddr_in addr{};
SOCKET sock = socket(AF_INET, SOCK_STREAM, 0);

if (IsSocketClosed(sock)) return INVALID_SOCKET;

addr.sin_family = AF_INET;
addr.sin_port = htons(port);
addr.sin_addr.s_addr = inet_addr(ip);

ioctlsocket(sock, FIONBIO, &mode); mode = 0;
if (::connect(sock, (struct sockaddr*)(&addr), sizeof(addr)) == 0)
{
	ioctlsocket(sock, FIONBIO, &mode);
	return sock;
}
```

以上代码用于**创建一个套接字，并尝试连接指定的 IP 地址和端口**。具体实现过程如下：

1. 创建一个 `u_long` 类型的变量 `mode`，并将其初始化为 1，用于设置套接字为非阻塞模式；
2. 创建一个 `struct sockaddr_in` 类型的变量 `addr`，并将其初始化，设置其地址族为 `AF_INET`，端口号为指定的 `port`，IP 地址为指定的 `ip`；
3. 调用 `socket()` 函数创建一个套接字，如果创建失败则返回 `INVALID_SOCKET`；
4. 判断该套接字是否已经关闭，如果已经关闭则返回 `INVALID_SOCKET`；
5. 将 `addr` 中的端口和 IP 地址信息绑定到该套接字上，用于下一步尝试连接；
6. 使用 `ioctlsocket()` 函数将该套接字设置为非阻塞模式，并将 `mode` 的值改为 0；
   - 第一个参数是要进行设置的套接字
   - 第二个参数是要设置的选项，这里是 `FIONBIO`，表示将套接字设置为非阻塞模式。
   - 第三个参数是一个指向选项值的指针，这里是 `&mode`，表示将选项值设置为 `mode` 变量的值。
7. 调用 `connect()` 函数尝试连接指定的 IP 地址和端口，如果连接成功则将该套接字设置回阻塞模式，并返回该套接字句柄；
8. 如果连接失败，则继续执行后续代码。 总之，以上代码的作用是创建一个套接字，并尝试连接指定的 IP 地址和端口，如果连接成功则返回该套接字句柄，否则继续执行后续代码。

------

### Linux 平台下的 epoll 实现

```C++
#ifdef XG_LINUX						// Linux 平台下的 epoll 实现
	struct epoll_event ev;// 定义 epoll 事件结构体 ev,变量ev用于描述要监听的事件，
	struct epoll_event evs;// 定义 epoll 事件结构体 evs,变量evs用于保存发生的事件
	int handle = epoll_create(1);// 创建 epoll 实例，返回 epoll 文件描述符，或者 -1 表示失败

	if (handle < 0)// 如果创建 epoll 实例失败，则关闭套接字并返回错误码
	{
		SocketClose(sock);// 关闭之前创建的套接字sock
		return INVALID_SOCKET;
	}
	memset(&ev, 0, sizeof(ev));// 将事件结构体 ev 的内存空间清零
	ev.events = EPOLLOUT | EPOLLERR | EPOLLHUP;	// 设置事件类型为可写、错误、挂起事件
	epoll_ctl(handle, EPOLL_CTL_ADD, sock, &ev);// 将套接字 sock 添加到 epoll 实例中，监听事件 ev
	if (epoll_wait(handle, &evs, 1, timeout) > 0)// 等待事件，timeout 为等待的超时时间
	{
		if (evs.events & EPOLLOUT)// 如果事件是可写事件
		{
			int res = FAIL;// 用于保存 getsockopt 函数返回的错误码
			socklen_t len = sizeof(res);
			getsockopt(sock, SOL_SOCKET, SO_ERROR, (char*)(&res), &len);// 获取套接字的选项值，判断连接是否成功
			ioctlsocket(sock, FIONBIO, &mode);	// 将套接字设置为非阻塞模式
			if (res == 0)// 如果连接成功
			{
				::close(handle);// 关闭 epoll 实例
				return sock;// 返回套接字，表示连接成功
			}
		}
	}
	::close(handle);// 关闭 epoll 实例
```

这段代码使用 epoll 实现了非阻塞连接操作。 代码首先将事件结构体 `ev` 的内存空间清零，并设置事件类型为可写、错误、挂起事件。然后，将套接字 `sock` 添加到 epoll 实例中，监听事件 `ev`。接下来，调用 `epoll_wait()` 函数等待事件发生，指定等待的超时时间为 `timeout`。如果事件发生，检查事件类型是否为可写事件，如果是，则调用 `getsockopt()` 函数获取套接字的选项值，判断连接是否成功。如果连接成功，则将套接字设置为非阻塞模式，并关闭 epoll 实例，返回套接字，表示连接成功。如果连接失败，则继续执行后面的代码，关闭 epoll 实例，并返回错误码。

------

### Windows 平台下的 select 实现

```C++
#else
    // Windows 平台下的 select 实现
    struct timeval tv {};  // 定义时间结构体 tv
    fd_set ws{};  // 定义文件描述符集合 ws
    FD_ZERO(&ws);  // 将文件描述符集合 ws 清零
    FD_SET(sock, &ws);  // 将套接字 sock 添加到文件描述符集合 ws 中
    tv.tv_sec = timeout / 1000;  // 将等待超时时间转换成秒数
    tv.tv_usec = timeout % 1000 * 1000;  // 将等待超时时间转换成微秒数
    if (select(sock + 1, NULL, &ws, NULL, &tv) > 0)  // 调用 select 函数等待可写事件
    {
        int res = ERROR;  // 用于保存 getsockopt 函数返回的错误码
        int len = sizeof(res);  // 用于保存 getsockopt 函数返回的错误码的长度
        getsockopt(sock, SOL_SOCKET, SO_ERROR, (char*)(&res), &len);  // 获取套接字的选项值，判断连接是否成功
        ioctlsocket(sock, FIONBIO, &mode);  // 将套接字设置为非阻塞模式
        if (res == 0)  // 如果连接成功
        {
            return sock;  // 返回套接字，表示连接成功
        }
    }
#endif
```

这段代码是对于 Windows 平台的兼容处理，使用 `select()` 函数实现非阻塞连接操作。 代码首先定义时间结构体 `tv` 和文件描述符集合 `ws`，并将文件描述符集合 `ws` 清零，并将套接字 `sock` 添加到文件描述符集合 `ws` 中。然后，将等待超时时间转换成秒数和微秒数，并调用 `select()` 函数等待可写事件。如果事件发生，调用 `getsockopt()` 函数获取套接字的选项值，判断连接是否成功。如果连接成功，则将套接字设置为非阻塞模式，并返回套接字，表示连接成功。

------

### void close()

```C++
void close()
{
	SocketClose(sock);
	sock = INVALID_SOCKET;
}
```

这段代码是一个函数，用于关闭套接字。具体解释如下：

- `SocketClose(sock)`：调用 `SocketClose()` 函数关闭套接字，释放套接字所占用的资源。
- `sock = INVALID_SOCKET`：将套接字变量 `sock` 赋值为 `INVALID_SOCKET`，表示套接字已关闭。

------

### bool isClosed() const

```C++
bool isClosed() const
{
	return IsSocketClosed(sock);
}
```

这段代码是一个函数，用于判断套接字是否已关闭。具体解释如下：

- `IsSocketClosed(sock)`：调用 `IsSocketClosed()` 函数判断套接字是否已关闭，如果套接字已关闭则返回 `true`，否则返回 `false`。函数的参数是套接字变量 `sock`。 
- `return`：将函数返回值返回给调用者。

------

### bool setSendTimeout()

```C++
bool setSendTimeout(int timeout)
{
	return SocketSetSendTimeout(sock, timeout);
}
```

这段代码是一个函数，用于设置发送超时时间。具体解释如下：

- `SocketSetSendTimeout(sock, timeout)`：调用 `SocketSetSendTimeout()` 函数设置套接字的发送超时时间
  - 函数的第一个参数是套接字变量 `sock`
  - 第二个参数是发送超时时间 `timeout`。
  - 函数返回布尔值，表示设置是否成功。
- `return`：将函数返回值返回给调用者。

------

### bool setRecvTimeout()

```C++
bool setRecvTimeout(int timeout)
{
	return SocketSetRecvTimeout(sock, timeout);
}
```

这段代码是一个函数，用于设置接收超时时间。具体解释如下：

- `SocketSetRecvTimeout(sock, timeout)`：调用 `SocketSetRecvTimeout()` 函数设置套接字的接收超时时间
  - 函数的第一个参数是套接字变量 `sock`
  - 第二个参数是接收超时时间 `timeout`
  - 函数返回布尔值，表示设置是否成功。
- `return`：将函数返回值返回给调用者。

------

### bool connect()

```C++
bool connect(const string& ip, int port, int timeout)
{
	close();
	sock = SocketConnectTimeout(ip.c_str(), port, timeout);
	return IsSocketClosed(sock) ? false : true;
}
```

这段代码是一个函数，用于连接服务器。具体解释如下：

- `close()`：调用 `close()` 函数关闭套接字，释放套接字所占用的资源。
- `sock = SocketConnectTimeout(ip.c_str(), port, timeout)`：调用 `SocketConnectTimeout()` 函数连接服务器
  - 第一个参数是服务器的 IP 地址
  - 第二个参数是服务器的端口号
  - 第三个参数是连接超时时间。
  - 函数返回连接成功后的套接字变量 `sock`。
- `IsSocketClosed(sock) ? false : true`：判断套接字是否已关闭，如果套接字已关闭则返回 `false`，否则返回 `true`。

------

### int write()

```C++
int write(const void* data, int count)
{
	const char* str = (const char*)(data);
	int num = 0;// 记录本次发送的字节数
	int times = 0;// 记录发送次数
	int writed = 0;// 记录已发送的字节数
	while (writed < count)
	{// 如果已发送的字节数小于需要发送的字节数，则继续发送
		if ((num = send(sock, str + writed, count - writed, 0)) > 0)
		{
			if (num > 8)
			{
				times = 0;
			}
			else
			{
				if (++times > 100) return TIMEOUT;
			}
			writed += num;// 累加已发送的字节数
		}
		else
		{
			if (IsSocketTimeout())
			{
				if (++times > 100) return TIMEOUT;
				continue;
			}
			return NETERR;// 网络错误
		}
	}
	return writed;// 返回已发送的字节数
}
```

这段代码是一个函数，**用于向服务器写入数据**。具体解释如下：

- `if ((num = send(sock, str + writed, count - writed, 0)) > 0)`：调用 `send()` 函数向服务器发送数据，函数的第一个参数是套接字变量 `sock`，第二个参数是需要发送的数据的指针，第三个参数是需要发送的字节数。如果发送成功，则返回发送的字节数，否则返回 -1。

- `if (num > 8)`：如果本次发送的字节数大于 8，则将 `times` 变量赋值为 0，表示发送成功，发送次数清零。

- 第一个`else`：否则，将 `times` 变量加 1，如果发送次数超过 100 次，则返回超时错误 `TIMEOUT`。

- 第二个`else`：如果发送失败，判断是否为超时错误，如果是，则将 `times` 变量加 1，如果发送次数超过 100 次，则返回超时错误 `TIMEOUT`；否则返回网络错误 `NETERR`。

------

### int read()

```c++
int read(void* data, int count, bool completed)
{
	char* str = (char*)(data);
	if (completed)
	{// 如果需要读取完整数据，则执行以下操作
		int num = 0;// 记录本次接收的字节数
		int times = 0;// 记录接收次数
		int readed = 0;// 记录已接收的字节数
		while (readed < count)
		{// 已接收的字节数小于需要接收的字节数，则继续接收
			if ((num = recv(sock, str + readed, count - readed, 0)) > 0)
			{
				if (num > 8)
				{// 本次接收的字节数大于 8，则将 `times` 变量赋值为 0
					times = 0;// 表示接收成功，接收次数清零
				}
				else
				{// 累加已接收的字节数
					if (++times > 100) return TIMEOUT;// 超时错误
				}
				readed += num;
			}
			else if (num == 0)
			{// 接收到的字节数为 0，则表示服务器已关闭连接，返回网络关闭错误
				return NETCLOSE;
			}
			else
			{
				if (IsSocketTimeout())
				{// 如果接收失败，判断是否为超时错误
                    // 如果是，则将 times 变量加 1，如果接收次数超过 100 次，则返回超时错误 TIMEOUT
					if (++times > 100) return TIMEOUT;
					continue;
				}
				return NETERR;// 则返回网络错误NETERR
			}
		}
		return readed;// 返回已接收的字节数
	}
	else
	{// 如果不需要读取完整数据，则执行以下操作
		int val = recv(sock, str, count, 0);// 函数从服务器接收数据
		if (val > 0) return val;// 返回已接收的字节数或错误码
		if (val == 0) return NETCLOSE;// 如果接收到的字节数为 0，则表示服务器已关闭连接，返回网络关闭错误
		if (IsSocketTimeout()) return 0;// 如果接收超时，则返回 0
		return NETERR;// 如果接收失败且不是超时错误，则返回网络错误
	}
}
```

这段代码是一个函数，用于从服务器读取数据。具体解释如下：

- `if ((num = recv(sock, str + readed, count - readed, 0)) > 0)`：调用 `recv()` 函数从服务器接收数据，函数的第一个参数是套接字变量 `sock`，第二个参数是接收数据的缓冲区，第三个参数是需要接收的字节数。如果接收成功，则返回接收的字节数，否则返回 -1。

- `int val = recv(sock, str, count, 0)`：调用 `recv()` 函数从服务器接收数据，函数的第一个参数是套接字变量 `sock`，第二个参数是接收数据的缓冲区，第三个参数是需要接收的字节数。如果接收成功，则返回接收的字节数；如果接收到的字节数为 0，则表示服务器已关闭连接，返回网络关闭错误 `NETCLOSE`；如果接收失败且不是超时错误，则返回网络错误 `NETERR`；如果接收超时，则返回 0。

------

### class Command{}

> 用于生成和解析Redis协议的命令的类

```c++
friend RedisConnect;
```

这是一个C++中的friend关键字，表示将RedisConnect类声明为Command类的友元类。这样，在RedisConnect类中就可以直接访问Command类的私有成员变量和函数。

```c++
protected:
	int status;// 命令执行的状态，0表示执行成功，其他数值表示执行失败
	string msg;// 命令执行的消息，通常为错误信息
	vector<string> res;// 命令执行的结果，以字符串数组的形式存储
	vector<string> vec;// 命令需要执行的参数，以字符串数组的形式存储
```

这是Command类的protected访问权限下的成员变量：

这些成员变量在Command类中被使用，用于存储和传递命令的执行状态、消息和结果。由于它们被声明为protected，因此它们可以被Command类的子类访问。同时，它们也不能被类外部直接访问，只能通过Command类中的成员函数进行访问。

------

### int parse()

```C++
protected:
	int parse(const char* msg, int len)
	{// 解析Redis协议的命令结果
		if (*msg == '$')
		{
            // 调用parseNode函数解析字符串节点，并将解析结果存储到end指针中
			const char* end = parseNode(msg, len);
			if (end == NULL) return DATAERR;// 如果解析结果为NULL，则返回数据解析错误
			switch (end - msg)
			{// 根据解析结果计算节点长度
			case 0: return TIMEOUT;// 如果长度为0，则返回超时
			case -1: return NOTFOUND;// 如果长度为-1，则返回未找到指定数据
			}
			return OK;// 执行成功
		}
		const char* str = msg + 1;// 将msg指针向后移动一位，指向协议字符串的第二个字符
        // 在协议字符串中查找第一个出现"\r\n"的位置，并将位置指针存储到end变量中
		const char* end = strstr(str, "\r\n");
		if (end == NULL) return TIMEOUT;// 如果未找到"\r\n"，则返回执行超时
		if (*msg == '+' || *msg == '-' || *msg == ':')
		{// 根据协议字符串的开头字符，设置Command对象的status、msg成员变量
			this->status = OK;
			this->msg = string(str, end);
			if (*msg == '+') return OK;
			if (*msg == '-') return FAIL;
			this->status = atoi(str);// 调用atoi函数将字符串转换成整数
			return OK;
		}
		if (*msg == '*')
		{
			int cnt = atoi(str);// 解析协议字符串中的节点个数，并将解析结果存储到cnt变量中
			const char* tail = msg + len;// 计算协议字符串的末尾位置，并将位置指针存储到tail变量中
			vec.clear();// 清空Command对象的vec成员变量
			str = end + 2;// 将str指针指向当前节点的下一个节点的起始位置
			while (cnt > 0)
			{
				if (*str == '*') return parse(str, tail - str);
				end = parseNode(str, tail - str);
				if (end == NULL) return DATAERR;
				if (end == str) return TIMEOUT;
				str = end;
				cnt--;
			}
			return res.size();// 返回执行结果的长度，即Command对象的res成员变量的大小
		}
		return DATAERR;// 如果协议字符串不符合Redis协议的格式，则返回数据解析错误
	}
```

这是Command类的protected访问权限下的成员函数parse的实现。它用于解析Redis协议的命令结果，并返回执行状态。参数msg表示待解析的协议字符串，len表示协议字符串的长度。该函数的实现逻辑如下：

- 如果协议字符串以$开头，则调用parseNode函数解析字符串节点，并根据解析结果返回执行状态。
- 如果协议字符串以+、-或:开头，则根据开头字符不同，设置Command对象的status、msg成员变量，并返回执行状态。
- 如果协议字符串以*开头，则解析多个节点，并将节点解析结果存储到Command对象的vec、res成员变量中，并返回执行结果的长度。
- 如果协议字符串不符合以上格式，则返回数据解析错误。 在函数执行过程中，会调用parseNode函数解析字符串节点。函数返回的状态码有以下几种：
	- OK：命令执行成功。
	- FAIL：命令执行失败。
	- TIMEOUT：命令执行超时。
	- NOTFOUND：未找到指定数据。
	- DATAERR：数据解析错误。

------

### const char* parseNode()

```C++
const char* parseNode(const char* msg, int len)
{
	const char* str = msg + 1;
	const char* end = strstr(str, "\r\n");
	if (end == NULL) return msg;
	int sz = atoi(str);
	if (sz < 0) return msg + sz;
	str = end + 2;
	end = str + sz + 2;
	if (msg + len < end) return msg;
	res.push_back(string(str, str + sz));
	return end;
}
```

这段代码定义了一个名为parseNode的函数，其作用是解析协议字符串中的一个节点，并将解析结果存储到Command对象的res成员变量中。该函数的输入参数msg是一个指向协议字符串的指针，len是协议字符串的长度。函数的返回值是一个指向协议字符串下一个节点的指针。 该函数的具体流程如下：

- 将msg指针向后移动一位，指向协议字符串的第二个字符。
- 在协议字符串中查找第一个出现"\r\n"的位置，并将位置指针存储到end变量中。如果未找到"\r\n"，则返回msg指针。
- 解析协议字符串中的节点长度，并将解析结果存储到sz变量中。如果节点长度小于0，则返回当前节点的结束位置。
- 将str指针指向当前节点的数据起始位置，将end指针指向当前节点的数据结束位置。
- 如果协议字符串的长度小于当前节点的结束位置，则返回msg指针。
- 将当前节点的数据存储到Command对象的res成员变量中。
- 返回当前节点的结束位置。

------

### Command(const string& cmd)

```
Command(const string& cmd)
{
	vec.push_back(cmd);
	this->status = 0;
}
```

这段代码定义了一个Command类的构造函数，其作用是初始化Command对象，并将传入的字符串cmd存储到Command对象的vec成员变量中，并将status成员变量初始化为0。这样，当创建一个Command对象时，就会自动调用该构造函数，将传入的字符串存储到vec成员变量中，并将status成员变量初始化为0。

------

### void add(const char* val)

```c++
void add(const char* val)
{
	vec.push_back(val);
}
```

这段代码定义了一个名为add的函数，其作用是在Command对象的vec成员变量中添加一个新元素。该函数的输入参数val是一个指向字符串的指针。

------

### void add(const string& val)

```c++
void add(const string& val)
{
	vec.push_back(val);
}
```

这段代码定义了一个名为add的函数，其作用是在Command对象的vec成员变量中添加一个新元素。该函数的输入参数val是一个string类型的引用。

------

### template<class DATA_TYPE> void add(DATA_TYPE val)

```c++
template<class DATA_TYPE> void add(DATA_TYPE val)
{
	add(to_string(val));// 将输入参数val转换成字符串形式
}
```

这段代码定义了一个名为add的函数模板，其作用是在Command对象的vec成员变量中添加一个新元素。该函数模板的输入参数val可以是任意类型的数据。该函数模板的实现如下：

- 调用to_string函数将输入参数val转换成字符串形式。
- 调用另一个名为add的函数，将转换后的字符串添加到Command对象的vec成员变量中。

------

### template<class DATA_TYPE, class ...ARGS> void add(DATA_TYPE val, ARGS ...args)

```c++
template<class DATA_TYPE, class ...ARGS> void add(DATA_TYPE val, ARGS ...args)
{
	add(val);
	add(args...);
}
```

这段代码定义了一个名为add的函数模板，其作用是在Command对象的vec成员变量中添加多个新元素。该函数模板的输入参数val和args可以是任意类型的数据。该函数模板使用了可变参数模板，**可以接受不定数量的输入参数**。该函数模板的实现如下：

- 调用另一个名为add的函数，将第一个输入参数val添加到Command对象的vec成员变量中。
- **使用递归调用方式**，调用自身的add函数，并将剩余的输入参数args传递进去。递归调用的终止条件为没有剩余的输入参数。

------

### string toString() const

```c++
string toString() const
{
	ostringstream out;
	out << "*" << vec.size() << "\r\n";
	for (const string& item : vec)
	{
		out << "$" << item.length() << "\r\n" << item << "\r\n";
	}
	return out.str();
}
```

这段代码定义了一个名为toString的函数，其作用是**将Command对象转换成字符串形式**。该函数没有输入参数，返回一个string类型的结果。该函数的实现如下：

- 创建一个ostringstream对象out，用于将Command对象转换成字符串形式。
- 将Command对象vec成员变量的大小添加到字符串形式中，并在前面加上"*"作为标识符。
- 遍历Command对象的vec成员变量，并将每个元素添加到字符串形式中。对于每个元素，需要先添加其长度信息，再添加该元素本身
- 返回字符串形式。

------

### string get(int idx) const

```c++
string get(int idx) const
{
	return res.at(idx);
}
```

这段代码定义了一个名为get的函数，其作用是获取Command对象vec成员变量中指定位置的元素。该函数的输入参数idx是一个整数，表示要获取的元素的位置。该函数返回一个string类型的结果。该函数的实现如下：

- 调用vector容器的at函数，获取vec成员变量中指定位置的元素，存储在一个string类型的变量中。
- 返回获取到的元素。

------

### const vector<string>& getDataList() const

```c++
const vector<string>& getDataList() const
{
	return res;
}
```

这段代码定义了一个名为getDataList的函数，其作用是返回Command对象的res成员变量。该函数没有输入参数，返回一个常引用类型的vector对象。该函数的实现如下：

- 返回Command对象的res成员变量。

------

### int getResult()

```c++
int getResult(RedisConnect* redis, int timeout)
{
	auto doWork = [&]() {
		string msg = toString();// 调用toString函数获取待发送的命令字符串
		Socket& sock = redis->sock;// 获取Redis连接对象中的socket对象
        // 向Redis服务器发送命令，如果发送失败则返回网络错误码NETERR
		if (sock.write(msg.c_str(), msg.length()) < 0) return NETERR;

		int len = 0;
		int delay = 0;
		int readed = 0;
		char* dest = redis->buffer;
		const int maxsz = redis->memsz;// 获取Redis连接对象中的buffer大小
		while (readed < maxsz)
		{// 进入while循环，用于接收Redis服务器返回的数据
            // 调用socket对象的read函数，从Redis服务器接收数据。如果接收失败，则返回相应的错误码。
			if ((len = sock.read(dest + readed, maxsz - readed, false)) < 0) return len;
			if (len == 0)
			{// 如果接收到的数据长度为0，则说明Redis服务器还未返回执行结果，需要等待一段时间再次尝试接收数据。
				delay += SOCKET_TIMEOUT;
                // 如果等待时间超过了设定的超时时间timeout，则返回超时错误码TIMEOUT。
				if (delay > timeout) return TIMEOUT;
			}
			else
			{// 如果接收到的数据长度不为0，则更新readed变量的值，并将接收到的数据末尾处添加一个空字符'\0'。
				dest[readed += len] = 0;
				if ((len = parse(dest, readed)) == TIMEOUT)
				{
					// 调用parse函数，解析接收到的数据。
                    // 如果解析结果为超时错误码TIMEOUT，则将delay变量的值重置为0，继续等待接收数据
					delay = 0;
				}
				else
				{
					return len;// 否则返回解析结果。
				}
			}
		}
		return PARAMERR;// 如果接收的数据长度已经达到了buffer的最大值，则返回参数错误码PARAMERR。
	};
    
    // 调用doWork函数获取Redis命令的执行结果码，并将其存储在Redis连接对象的code成员变量中。
    // 同时，将Redis连接对象的状态和错误信息进行了清空，以便存储新的状态和错误信息。
	status = 0;// 将Redis连接对象的状态status设置为0，表示没有错误
	msg.clear();// 清空Redis连接对象的错误信息msg
    // 调用doWork函数，获取Redis命令的执行结果码，并将其存储在Redis连接对象的code成员变量中
	redis->code = doWork();
    
    // 这段代码用于根据Redis连接对象的code成员变量的值，设置Redis连接对象的错误信息msg。
    // 如果msg已经有了值，则不进行操作。
	if (redis->code < 0 && msg.empty())
	{
		switch (redis->code)
		{
		case SYSERR:
			msg = "system error";
			break;
		case NETERR:
			msg = "network error";
			break;
		case DATAERR:
			msg = "protocol error";
			break;
		case TIMEOUT:
			msg = "response timeout";
			break;
		case NOTFOUND:
			msg = "element not found";
			break;
		default:
			msg = "unknown error";
			break;
		}
	}
    // 更新Redis连接对象的状态、错误信息，并返回Redis命令的执行结果码
	redis->status = status;
	redis->msg = msg;
	return redis->code;
}
```

这段代码定义了一个名为getResult的函数，其**作用是向Redis服务器发送命令**，并获取命令执行结果。该函数的输入参数redis是一个RedisConnect类型的指针，表示要发送命令的Redis连接对象；timeout是一个整数，表示等待命令执行结果的超时时间。该函数返回一个整数类型的结果，表示命令执行的结果码。该函数的实现如下：

1. 定义一个名为doWork的lambda函数，用于实际执行向Redis服务器发送命令和获取命令执行结果的操作。**该函数内部实现了命令的发送和结果的获取，返回一个整数类型的结果码**。
2. 初始化status和msg变量，用于存储命令执行状态和错误信息。
3. 调用doWork函数，获取命令执行结果码，并将其存储在redis连接对象的code成员变量中。
4. 检查命令执行结果码是否小于0，如果小于0且msg变量为空，则设置msg变量为相应的错误信息。
5. 将命令执行状态和错误信息存储在redis连接对象的status和msg成员变量中，返回命令执行结果码。

------

```c++
protected:
	int code = 0;// Redis命令的执行结果码，默认值为0
	int port = 0;// Redis服务器的端口号，默认值为0
	int memsz = 0;// 缓存区的大小，默认值为0
	int status = 0;// Redis连接对象的状态，默认值为0
	int timeout = 0;// 网络超时时间，默认值为0
	char* buffer = NULL;// 缓存区指针，默认值为NULL

	string msg;// Redis连接对象的错误信息，默认为空字符串
	string host;// Redis服务器的主机名或IP地址，默认为空字符串
	Socket sock;// 网络套接字对象
	string passwd;// Redis服务器的密码，默认为空字符串
```

这段代码定义了一个Redis连接对象的私有成员变量

------

### ~RedisConnect()

```c++
~RedisConnect()
{
	close();
}
```

这段代码定义了Redis连接对象的析构函数，用于释放Redis连接对象的资源。具体实现如下：

1. 调用close函数，关闭Redis连接对象。
2. 在close函数中，会释放Redis连接对象的缓存区和网络套接字资源。

------

### int getStatus() const、int getErrorCode() const、string getErrorString() const

```c++
public:
	int getStatus() const
	{
		return status;
	}
	int getErrorCode() const
	{
		if (sock.isClosed()) return FAIL;
		return code < 0 ? code : 0;
	}
	string getErrorString() const
	{
		return msg;
	}
```

这段代码定义了Redis连接对象的公共成员函数，用于获取Redis连接对象的**状态、错误码和错误信息**。具体实现如下：

1. getStatus函数，用于获取Redis连接对象的状态，返回值为整型。
2. getErrorCode函数，用于获取Redis连接对象的错误码，返回值为整型。
	- 如果网络套接字已关闭，则返回FAIL（-1）
	- 否则返回Redis命令的执行结果码
		- 如果执行结果码为负数，则认为发生了错误，返回结果码本身
		- 否则返回0。
3. getErrorString函数，用于获取Redis连接对象的错误信息，返回值为字符串类型。

------

### void close()

```c++
void close()
{
	if (buffer)
	{
		delete[] buffer;
		buffer = NULL;
	}
	sock.close();
}
```

这段代码定义了Redis连接对象的close函数，用于关闭Redis连接对象，释放Redis连接对象的资源。具体实现如下：

1. 判断Redis连接对象的缓存区指针是否为空，如果不为空，则释放缓存区内存，将缓存区指针置为空。
2. 调用网络套接字对象的close函数，关闭网络套接字对象，释放网络套接字资源。

------

### bool reconnect()

```c++
bool reconnect()
{
	if (host.empty()) return false;
	return connect(host, port, timeout, memsz) && auth(passwd) > 0;
}
```

这段代码定义了Redis连接对象的reconnect函数，用于重新连接Redis服务器。具体实现如下：

1. 判断Redis连接对象的主机名或IP地址是否为空，如果为空，则返回false，表示重新连接失败。
2. 调用connect函数，连接Redis服务器，如果连接成功，则调用auth函数，进行身份验证。
	- 如果身份验证成功，则返回true，表示重新连接成功
	- 否则返回false，表示重新连接失败。

------

### int execute(Command& cmd)

```c++
int execute(Command& cmd)
{
	return cmd.getResult(this, timeout);
}
```

这段代码定义了Redis连接对象的execute函数，用于执行Redis命令对象的操作。具体实现如下：

1. 调用Redis命令对象的getResult函数，执行Redis命令，并返回执行结果码。

------

### int execute(DATA_TYPE val, ARGS ...args)

```c++
template<class DATA_TYPE, class ...ARGS>
int execute(DATA_TYPE val, ARGS ...args)
{
	Command cmd;
	cmd.add(val, args...);
	return cmd.getResult(this, timeout);
}
```

这段代码定义了Redis连接对象的execute函数模板，用于执行Redis命令。可以传入多个参数，用于构造Redis命令。具体实现如下：

1. 创建Redis命令对象cmd。
2. 调用Redis命令对象的add函数，将传入的参数添加到Redis命令中。
3. 调用Redis命令对象的getResult函数，执行Redis命令，并返回执行结果码。

------

### int execute(vector<string>& vec, DATA_TYPE val, ARGS ...args)

```c++
template<class DATA_TYPE, class ...ARGS>
int execute(vector<string>& vec, DATA_TYPE val, ARGS ...args)
{
	Command cmd;
	cmd.add(val, args...);
	cmd.getResult(this, timeout);
	if (code > 0) std::swap(vec, cmd.res);
	return code;
}
```

这段代码定义了Redis连接对象的execute函数模板，用于执行Redis命令，并将结果存储在vector容器中。可以传入多个参数，用于构造Redis命令。具体实现如下：

1. 创建Redis命令对象cmd。

2. 调用Redis命令对象的add函数，将传入的参数添加到Redis命令中。

3. 调用Redis命令对象的getResult函数，执行Redis命令，并将执行结果保存在Redis命令对象的res成员中。

4. 判断执行结果码是否大于0，如果大于0，则将Redis命令对象的res成员与传入的vector容器进行交换，将执行结果存储在vector容器中。

	> 将Redis命令对象的res成员与传入的vector容器进行交换，是为了避免在函数返回时，再次将Redis命令对象的res成员复制给vector容器，从而提高了程序的效率。
	>
	>  在Redis命令对象的getResult函数中，执行Redis命令后，将执行结果存储在Redis命令对象的res成员中。如果直接将Redis命令对象的res成员返回给调用者，程序会复制一份结果集，增加了不必要的开销。因此，可以通过将Redis命令对象的res成员与传入的vector容器进行交换，将执行结果直接存储在传入的vector容器中，避免了复制操作，提高了程序的效率。

5. 返回执行结果码。

------

### bool connect(const string& host, int port, int timeout = 3000, int memsz = 2 * 1024 * 1024)

```c++
bool connect(const string& host, int port, int timeout = 3000, int memsz = 2 * 1024 * 1024)
{
	close();
	if (sock.connect(host, port, timeout))
	{
		sock.setSendTimeout(SOCKET_TIMEOUT);
		sock.setRecvTimeout(SOCKET_TIMEOUT);
		this->host = host;
		this->port = port;
		this->memsz = memsz;
		this->timeout = timeout;
		this->buffer = new char[memsz + 1];
	}
	return buffer ? true : false;
}
```

这段代码定义了Redis连接对象的connect函数，用于连接Redis服务器。具体实现如下：

1. 首先调用close函数，关闭当前Redis连接。
2. 调用Socket对象的connect函数，连接Redis服务器。如果连接成功，则设置Socket对象的发送和接收超时时间，保存Redis服务器的主机名或IP地址、端口号、缓冲区大小和超时时间，并分配缓冲区空间。
3. 返回连接结果，如果缓冲区分配成功，则返回true，否则返回false。

------

### Redis命令函数，执行常用的Redis操作

> 这段代码定义了Redis连接对象的一系列Redis命令函数，用于执行常用的Redis操作。这些Redis命令函数都调用了Redis连接对象的execute函数模板，传入不同的Redis命令和参数，执行Redis操作，返回执行结果码。

```c++
int ping()
{
	return execute("ping");
}
int del(const string& key)
{// 删除指定的键值对
	return execute("del", key);
}
int ttl(const string& key)
{// 获取指定键的过期时间，并返回执行结果码或过期时间
	return execute("ttl", key) == OK ? status : code;
}
int hlen(const string& key)
{// 获取指定哈希表的长度，并返回执行结果码或哈希表长度
	return execute("hlen", key) == OK ? status : code;
}
int auth(const string& passwd)
{// 进行身份验证
	this->passwd = passwd;
	if (passwd.empty()) return OK;
	return execute("auth", passwd);
}
int get(const string& key, string& val)
{// 获取指定键的值，并将值存储在传入的字符串val中
	vector<string> vec;
	if (execute(vec, "get", key) <= 0) return code;
	val = vec[0];
	return code;
}
int decr(const string& key, int val = 1)
{// 将指定键的值减去给定的整数val
	return execute("decrby", key, val);
}
int incr(const string& key, int val = 1)
{// 将指定键的值加上给定的整数val
	return execute("incrby", key, val);
}
int expire(const string& key, int timeout)
{// 设置指定键的过期时间
	return execute("expire", key, timeout);
}
int keys(vector<string>& vec, const string& key)
{// 获取符合给定模式的键，并将键存储在传入的vector容器中
	return execute(vec, "keys", key);
}
int hdel(const string& key, const string& filed)
{// 删除指定哈希表中的指定字段
	return execute("hdel", key, filed);
}
int hget(const string& key, const string& filed, string& val)
{// 获取指定哈希表中指定字段的值，并将值存储在传入的字符串val中
	vector<string> vec;
	if (execute(vec, "hget", key, filed) <= 0) return code;
	val = vec[0];
	return code;
}
int set(const string& key, const string& val, int timeout = 0)
{// 执行Redis的set命令或setex命令,设置指定键的值
	return timeout > 0 ? execute("setex", key, timeout, val) : execute("set", key, val);
}
int hset(const string& key, const string& filed, const string& val)
{// 设置指定哈希表中指定字段的值
	return execute("hset", key, filed, val);
}
```

------

### 操作Redis列表类型的函数

> 这段代码定义了一系列操作Redis列表类型的函数，包括获取列表元素、添加元素、删除元素等操作。

```c++
int pop(const string& key, string& val)
{// 从左侧弹出列表中的一个元素，并将元素存储在传入的字符串val中
    return lpop(key, val);
}
int lpop(const string& key, string& val)
{// 从左侧弹出列表中的一个元素，并将元素存储在传入的字符串val中
    vector<string> vec;
    if (execute(vec, "lpop", key) <= 0) return code;
    val = vec[0];
    return code;
}
int rpop(const string& key, string& val)
{// 从右侧弹出列表中的一个元素，并将元素存储在传入的字符串val中
    vector<string> vec;
    if (execute(vec, "rpop", key) <= 0) return code;
    val = vec[0];
    return code;
}
int push(const string& key, const string& val)
{// 将指定元素从右侧添加到列表中
    return rpush(key, val);
}
int lpush(const string& key, const string& val)
{// 将指定元素从左侧添加到列表中
    return execute("lpush", key, val);
}
int rpush(const string& key, const string& val)
{// 将指定元素从右侧添加到列表中
    return execute("rpush", key, val);
}
int range(vector<string>& vec, const string& key, int start, int end)
{// 执行Redis的lrange命令，获取列表中指定范围内的元素，并将元素存储在传入的vector容器中
    return execute(vec, "lrange", key, start, end);
}
int lrange(vector<string>& vec, const string& key, int start, int end)
{// 执行Redis的lrange命令，获取列表中指定范围内的元素，并将元素存储在传入的vector容器中
    return execute(vec, "lrange", key, start, end);
}
```

------

### 操作Redis有序集合类型的函数

> 这段代码定义了一系列操作Redis有序集合类型的函数，包括添加元素、删除元素、获取元素等操作。

```c++
int zrem(const string& key, const string& filed)
{// 从有序集合中删除指定的元素
    return execute("zrem", key, filed);
}
int zadd(const string& key, const string& filed, int score)
{// 将指定元素（具有指定的分值）添加到有序集合中
    return execute("zadd", key, score, filed);
}
int zrange(vector<string>& vec, const string& key, int start, int end, bool withscore = false)
{// 获取有序集合中指定范围内的元素，并将元素存储在传入的vector容器中
    // withscore参数表示是否获取元素的分值，默认为不获取
    return withscore ? execute(vec, "zrange", key, start, end, "withscores") : execute(vec, "zrange", key, start, end);
}
```

------

### int eval(const string& lua)

```c++
template<class ...ARGS>
int eval(const string& lua)
{
	vector<string> vec;
	return eval(lua, vec);
}
```

这段代码定义了一个可变参数模板函数eval，**用于执行Redis的EVAL命令，执行Lua脚本**。具体实现如下：

1. eval函数：接收一个字符串类型的参数lua，表示要执行的Lua脚本，同时**创建一个vector容器用于存储执行结果**。该函数调用另一个名为eval的函数，并将vector容器传入，返回eval函数的执行结果。

	其中，...ARGS表示可变参数模板，允许函数接收任意数量的参数。 

2. eval函数：接收两个参数，一个字符串类型的参数lua，表示要执行的Lua脚本，另一个是vector类型的参数vec，**用于存储执行结果**。该函数构造一个vector容器args，将可变参数转换为字符串类型后存入args中，然后调用execute函数执行EVAL命令，将args和lua作为参数传入，将执行结果存储在vec中，最终返回执行结果码。

------

### int eval(const string& lua, const string& key, ARGS ...args)

```c++
template<class ...ARGS>
int eval(const string& lua, const string& key, ARGS ...args)
{
	vector<string> vec;
	vec.push_back(key);
	return eval(lua, vec, args...);
}
```

这段代码定义了一个可变参数模板函数eval，用于执行Redis的EVAL命令，执行Lua脚本。**与前面的eval函数不同之处在于，它还接收一个字符串类型的参数key，表示要操作的键值**。具体实现如下：

1. eval函数：接收三个参数，一个字符串类型的参数lua，表示要执行的Lua脚本，一个字符串类型的参数key，表示要操作的键值，以及可变数量的参数args。该函数创建一个vector容器用于存储执行结果，并将key添加到该容器中。然后调用另一个名为eval的函数，并将vector容器和可变参数args传入，返回eval函数的执行结果。

	其中，...ARGS表示可变参数模板，允许函数接收任意数量的参数。 

2.  eval函数：接收三个参数，一个字符串类型的参数lua，表示要执行的Lua脚本，一个vector类型的参数vec，用于存储执行结果，以及可变数量的参数args。该函数构造一个vector容器args，将可变参数转换为字符串类型后存入args中，然后调用execute函数执行EVAL命令，将args、lua和vec[0]作为参数传入，因为vec[0]存储了要操作的键值，将执行结果存储在vec中，最终返回执行结果码。

------

### int eval(const string& lua, const vector<string>& keys, ARGS ...args)

```c++
template<class ...ARGS>
int eval(const string& lua, const vector<string>& keys, ARGS ...args)
{
	vector<string> vec;
	return eval(vec, lua, keys, args...);
}
```

这段代码定义了一个可变参数模板函数eval，用于执行Redis的EVAL命令，执行Lua脚本。**与前面的eval函数不同之处在于，它还接收一个vector类型的参数keys，表示要操作的键值列表**。具体实现如下：

1. eval函数：接收三个参数，一个字符串类型的参数lua，表示要执行的Lua脚本，一个vector类型的参数keys，表示要操作的键值列表，以及可变数量的参数args。该函数创建一个vector容器用于存储执行结果，然后调用另一个名为eval的函数，并将该容器、lua、keys和可变参数args传入，返回eval函数的执行结果。

	其中，...ARGS表示可变参数模板，允许函数接收任意数量的参数。 

2.  eval函数：接收四个参数，一个vector类型的参数vec，**用于存储执行结果**，一个字符串类型的参数lua，**表示要执行的Lua脚本**，一个vector类型的参数keys，**表示要操作的键值列表**，以及可变数量的参数args。该函数构造一个vector容器args，将可变参数转换为字符串类型后存入args中，然后调用execute函数执行EVAL命令，将args、lua、keys和vec作为参数传入，将执行结果存储在vec中，最终返回执行结果码。

------



```c++
template<class ...ARGS>
int eval(vector<string>& vec, const string& lua, const vector<string>& keys, ARGS ...args)
{
	int len = 0;// 记录keys的长度
	Command cmd("eval");// 构造函数接收一个字符串"eval"，表示要执行的Redis命令是EVAL
	cmd.add(lua);// 将要执行的Lua脚本代码添加到cmd对象中
	cmd.add(len = keys.size());// 将keys的长度添加到cmd对象中，并将len的值更新为keys的长度
	if (len-- > 0)
	{// 判断keys的长度是否大于0，如果是，就依次将keys中的元素添加到cmd对象中
		for (int i = 0; i < len; i++) cmd.add(keys[i]);
		cmd.add(keys.back(), args...);
	}
    // 调用getResult函数执行EVAL命令，并将执行结果存储在cmd对象的res成员中，
    // getResult函数的第一个参数是this，表示该函数是Command类的成员函数，timeout是一个可选的超时参数
	cmd.getResult(this, timeout);
	if (code > 0) std::swap(vec, cmd.res);// 如果成功，就将cmd对象的res和vec交换，将执行结果存储在vec中
	return code;
}
```

这段代码定义了一个可变参数模板函数eval，用于执行Redis的EVAL命令，执行Lua脚本。与前面的两个eval函数不同之处在于，它直接调用execute函数执行EVAL命令，并将执行结果存储在参数vec中。具体实现如下：

1. eval函数：接收四个参数，一个vector类型的参数vec，用于存储执行结果，一个字符串类型的参数lua，表示要执行的Lua脚本，一个vector类型的参数keys，表示要操作的键值列表，以及可变数量的参数args。该函数构造一个Command类型的对象cmd，设置EVAL命令的参数，包括lua代码和参数个数，然后将键值列表和可变参数args添加到cmd中。接着调用getResult函数执行EVAL命令，将结果存储在cmd的res成员中，如果执行成功，就将cmd的res和vec交换，最终返回执行结果码。

	其中，...ARGS表示可变参数模板，允许函数接收任意数量的参数。Command是一个封装了Redis命令的类，add函数用于添加命令的参数，getResult函数用于执行Redis命令并获取执行结果。

------

### string get(const string& key)

```c++
string get(const string& key)
{
	string res;
	get(key, res);
	return res;
}
```

这段代码定义了一个名为get的函数，用于从Redis中获取指定键的值。 具体实现过程如下：

1. 定义一个字符串变量res，用于存储获取到的键值。
2. 调用get函数的重载版本，传入需要获取的键key和结果存储变量res。
3. 返回获取到的键值。

------

### string hget(const string& key, const string& filed)

```c++
string hget(const string& key, const string& filed)
{
	string res;
	hget(key, filed, res);
	return res;
}
```

这段代码定义了一个名为hget的函数，用于从Redis中**获取指定哈希表的指定字段的值**。 具体实现过程如下：

1. 定义一个字符串变量res，用于存储获取到的字段值。
2. 调用hget函数的重载版本，传入需要获取的哈希表名key、需要获取的字段filed和结果存储变量res。
3. 返回获取到的字段值。

------

### const char* getLockId()

```c++
const char* getLockId()
{
	thread_local char id[0xFF] = {0};
	auto GetHost = [](){// 定义一个lambda表达式GetHost，用于获取主机名
		char hostname[0xFF];
		if (gethostname(hostname, sizeof(hostname)) < 0) return "unknow host";
		struct hostent* data = gethostbyname(hostname);
		return (const char*)inet_ntoa(*(struct in_addr*)(data->h_addr_list[0]));
	};
	if (*id == 0)
	{
#ifdef XG_LINUX
		snprintf(id, sizeof(id) - 1, "%s:%ld:%ld", GetHost(), (long)getpid(), (long)syscall(SYS_gettid));
#else
		snprintf(id, sizeof(id) - 1, "%s:%ld:%ld", GetHost(), (long)GetCurrentProcessId(), (long)GetCurrentThreadId());
#endif
	}
	return id;
}
```

这段代码定义了一个名为getLockId的函数，用于获取当前线程的锁ID。 具体实现过程如下：

1. 定义了一个线程局部变量char类型的数组id，数组大小为0xFF，**用于存储获取到的锁ID**。

2. 定义一个lambda表达式GetHost，**用于获取主机名**。

3. 判断锁ID是否为空，如果是

	（Linux系统）通过snprintf函数将当前线程的锁ID存储到id数组中，使用GetHost获取主机名，使用getpid获取进程ID，使用syscall(SYS_gettid)**获取线程ID**;

	（Windows系统）使用GetCurrentProcessId和GetCurrentThreadId获取进程ID和线程ID，拼接成格式为"主机名:进程ID:线程ID"的字符串，存储到id数组中。

4. 返回id数组的首地址，即锁ID。

------

### bool unlock()

```c++
bool unlock(const string& key)
{
	const char* lua = "if redis.call('get',KEYS[1])==ARGV[1] then return redis.call('del',KEYS[1]) else return 0 end";
	return eval(lua, key, getLockId()) > 0 && status == OK;
}
```

这段代码定义了一个名为unlock的函数，**用于释放Redis中指定key的锁**。 具体实现过程如下：

1. 定义一个字符串类型的lua常量，用于在Redis中执行lua脚本，判断当前key的值是否等于当前线程的锁ID，如果相等，则删除该key并返回1，否则返回0。

	lua脚本的内容如下：

	> ```lua
	> if redis.call('get',KEYS[1]) == ARGV[1] then  -- 判断当前key的值是否等于当前线程的锁ID
	>     return redis.call('del',KEYS[1])         -- 如果相等，则删除该key并返回1
	> else
	>     return 0                                 -- 否则，返回0
	> end
	> ```
	>
	> 1. 脚本中使用Redis提供的get、del、call函数对Redis中指定key的值进行操作。
	>
	> - get函数用于获取指定key的值。
	> - del函数用于删除指定key。
	> - call函数用于调用Redis命令，第一个参数是命令名称，后面的参数是命令所需参数。
	> - KEYS[1]表示lua脚本中的第一个参数即Redis中指定的key
	> - ARGV[1]表示lua脚本中的第二个参数即当前线程的锁ID
	> 	- 这两个参数通过eval函数的后面两个参数进行传递，即key和getLockId()。
	> - 如果当前key的值等于当前线程的锁ID，则删除该key并返回1，否则返回0。

2. 调用eval函数执行lua脚本，传入lua脚本、key和当前线程的锁ID作为参数，如果执行结果大于0且状态为OK，则说明释放锁成功，并返回true；否则，释放锁失败，返回false。

------

### bool lock()

```c++
bool lock(const string& key, int timeout = 30)
{
	int delay = timeout * 1000;
	for (int i = 0; i < delay; i += 10)
	{
		if (execute("set", key, getLockId(), "nx", "ex", timeout) >= 0) return true;
		Sleep(10);
	}
	return false;
}
```

这段代码定义了一个名为lock的函数，用于在Redis中对指定key进行加锁操作。 具体实现过程如下：

1. 将传入的timeout参数转换为毫秒级别的delay。

2. 使用for循环，每次循环等待10毫秒，尝试设置指定key的值为当前线程的锁ID。如果返回值大于等于0，则说明设置成功，即加锁成功，返回true；否则继续等待。

3. 在每次循环中，使用execute函数调用Redis的set命令，尝试设置指定key的值为当前线程的锁ID，同时设置参数nx和ex，表示只有当前key不存在时才能设置，并且设置后的过期时间为timeout秒。如果设置成功，则返回1，否则返回0。

	> 这段代码调用execute函数，使用Redis的set命令尝试对指定key加锁。如果设置成功，则返回true，表示加锁成功。
	>
	> 具体实现过程如下：
	>
	> 1. 调用execute函数，传入参数"set"、key、当前线程的锁ID、"nx"和"ex"，表示调用Redis的set命令，设置指定key的值为当前线程的锁ID，只有当key不存在时才能设置，设置后的过期时间为timeout秒。
	> 2. set命令的返回值有以下三种情况：
	> 	- 如果设置成功，则返回字符串"OK"，并且execute函数返回值大于等于0。
	> 	- 如果key已经存在，则返回nil，表示设置失败，execute函数返回值为-1。
	> 	- 如果发生错误，则返回nil，并且execute函数返回值为-2.
	> 3. 如果execute函数返回值大于等于0，则说明设置成功，即加锁成功，返回true；否则设置失败，返回false。

4. 如果循环完毕，仍然无法成功加锁，则返回false，表示加锁失败。

------

### virtual shared_ptr<RedisConnect> grasp() const

```c++
virtual shared_ptr<RedisConnect> grasp() const
{
	static ResPool<RedisConnect> pool([&]() {
		shared_ptr<RedisConnect> redis = make_shared<RedisConnect>();
		if (redis && redis->connect(host, port, timeout, memsz))
		{
			if (redis->auth(passwd)) return redis;
		}
		return redis = NULL;
	}, POOL_MAXLEN);
	shared_ptr<RedisConnect> redis = pool.get();
	if (redis && redis->getErrorCode())
	{
		pool.disable(redis);
		return grasp();
	}
	return redis;
}
```

这段代码定义了一个名为grasp的函数，**用于从Redis连接池中获取一个可用的Redis连接**，并**返回该连接的shared_ptr智能指针**。 具体实现过程如下：

1. 定义一个静态的ResPool对象pool，并使用lambda表达式初始化，lambda表达式中返回一个shared_ptr对象，该对象首先通过make_shared函数创建一个RedisConnect对象，并调用其connect函数连接Redis服务器，如果连接成功并授权成功，则返回该RedisConnect对象，否则返回NULL。
2. 调用pool的get函数，从连接池中获取一个可用的Redis连接。如果获取成功，则返回该连接的shared_ptr智能指针；否则返回NULL。
3. 如果获取到的Redis连接存在，并且连接状态异常，则调用pool的disable函数禁用该连接，并递归调用grasp函数尝试获取另一个可用的Redis连接。
4. 如果获取到的Redis连接存在，并且连接状态正常，则直接返回该连接的shared_ptr智能指针。

------

### static bool CanUse()

```c++
static bool CanUse()
{
	return GetTemplate()->port > 0;
}
```

这段代码定义了一个名为CanUse的静态函数，**用于判断当前Redis连接是否可用**。具体实现过程如下：

1. 调用GetTemplate()函数获取Redis连接的配置信息，包括host、port等信息。
2. 判断获取到的port是否大于0，如果是则认为当前Redis连接可用，返回true；否则返回false。

------

### static RedisConnect* GetTemplate()

```C++
static RedisConnect* GetTemplate()
{
	static RedisConnect redis;
	return &redis;
}
```

这段代码定义了一个名为GetTemplate的静态函数，用于获取Redis连接的模板对象指针。 具体实现过程如下：

1. 定义一个静态的RedisConnect对象redis。
2. 返回该静态对象的指针。

------

### static void SetMaxConnCount()

```c++
static void SetMaxConnCount(int maxlen)
{
	if (maxlen > 0) POOL_MAXLEN = maxlen;
}
```

这段代码定义了一个名为SetMaxConnCount的静态函数，**用于设置Redis连接池的最大连接数**。 具体实现过程如下：

1. 判断传入的maxlen参数是否大于0，如果是则更新POOL_MAXLEN值为maxlen。

------

### static shared_ptr<RedisConnect> Instance()

```c++
static shared_ptr<RedisConnect> Instance()
{
	return GetTemplate()->grasp();
}
```

这段代码定义了一个名为Instance的静态函数，用于获取一个可用的Redis连接的shared_ptr智能指针。 具体实现过程如下：

1. 调用GetTemplate()函数获取Redis连接的模板对象指针。
2. 调用该模板对象的grasp()函数获取一个可用的Redis连接的shared_ptr智能指针，并返回该指针。

------

### static void Setup()

```c++
static void Setup(const string& host, int port, const string& passwd = "", int timeout = 3000, int memsz = 2 * 1024 * 1024)
{
#ifdef XG_LINUX
	signal(SIGPIPE, SIG_IGN);// 忽略SIGPIPE信号
#else
	WSADATA data;
    WSAStartup(MAKEWORD(2, 2), &data);
#endif
	RedisConnect* redis = GetTemplate();
	redis->host = host;
	redis->port = port;
	redis->memsz = memsz;
	redis->passwd = passwd;
	redis->timeout = timeout;
}
```

这段代码定义了一个名为Setup的静态函数，**用于设置Redis连接的配置信息**。 具体实现过程如下：

1. 在Linux系统中，忽略SIGPIPE信号。

2. 在Windows系统中，初始化Winsock库。

	> Winsock库是Windows下的网络编程库，用于实现网络通信功能。
	>
	> 在使用Winsock库之前，需要调用WSAStartup函数进行初始化。
	>
	> 该函数的第一个参数表示要使用的Winsock版本，第二个参数是一个指向WSADATA结构的指针，用于接收Winsock库初始化的结果信息。
	>
	> 在使用完Winsock库之后，需要调用WSACleanup函数释放Winsock资源。

3. 调用GetTemplate()函数获取Redis连接的模板对象指针。

4. 更新模板对象的配置信息，包括host、port、passwd、timeout、memsz等信息。

------

## 4、RedisCommand.cpp文件

```c++
#define ColorPrint(__COLOR__, __FMT__, ...)		\
SetConsoleTextColor(__COLOR__);					\
printf(__FMT__, __VA_ARGS__);					\
SetConsoleTextColor(eWHITE);					\
```

这段代码定义了一个宏，用于在控制台输出带颜色的文本。 具体实现过程如下：

1. 调用SetConsoleTextColor函数设置控制台文本颜色为指定的颜色。
2. 使用printf函数输出带格式的文本。
3. 调用SetConsoleTextColor函数将控制台文本颜色恢复为白色。

> **上一段代码每一行后面为什么要加 / ?**
>
> 这是一种在宏定义中使用的注释方式，称为反斜线注释。这种注释方式可以将一行的注释内容延续到下一行，使得注释内容更加清晰明了。在宏定义中，由于宏定义需要在一行内完成，因此使用反斜线注释可以让注释内容与宏定义内容分开，不会影响宏定义的正确性。

------

### bool CheckCommand(const char* fmt, ...)

```
bool CheckCommand(const char* fmt, ...)
{
    // 定义可变参数列表和缓冲区，并将格式化字符串和可变参数转换为字符串
    va_list args;  // 定义可变参数列表
    char buffer[64 * 1024] = {0};  // 定义缓冲区
    va_start(args, fmt);  // 初始化可变参数列表
    vsnprintf(buffer, sizeof(buffer) - 1, fmt, args);  // 将格式化字符串和可变参数转换为字符串
    va_end(args);  // 结束可变参数列表
    // 输出字符串，并等待用户输入Y或N
    printf("%s", buffer);
    while (true)
    {
        int n = getch();  // 获取用户输入的字符
        if (n == 'Y' || n == 'y')  // 如果用户输入Y或y，则输出(YES)并返回true
        {
            SetConsoleTextColor(eGREEN);  // 设置控制台文本颜色为绿色
            printf(" (YES)\n");  // 输出(YES)
            SetConsoleTextColor(eWHITE);  // 恢复控制台文本颜色为白色
            return true;  // 返回true
        }
        else if (n == 'N' || n == 'n')  // 如果用户输入N或n，则输出(NO)并返回false
        {
            SetConsoleTextColor(eRED);  // 设置控制台文本颜色为红色
            printf(" (NO)\n");  // 输出(NO)
            SetConsoleTextColor(eWHITE);  // 恢复控制台文本颜色为白色
            return false;  // 返回false
        }
    }
    // 如果函数一直未返回，则返回false
    return false;
}
```

这段代码定义了一个名为CheckCommand的函数，**用于在控制台上输出指定格式的文本**，并等待用户输入Y或N来确认是否继续执行程序。 具体实现过程如下：

1. 使用可变参数列表va_list和vsnprintf函数将格式化字符串fmt和后面的可变参数转换为字符串buffer。
2. 在控制台上输出字符串buffer，并等待用户输入Y或N。
3. 如果用户输入了Y或N，则分别输出(YES)或(NO)，并返回对应的布尔值；否则，函数一直等待用户输入。
4. 如果函数始终未返回，则返回false。

------

```c++
int main(int argc, char** argv) // 程序主函数，接收命令行参数
{
    auto GetCmdParam = [&](int idx){ // 定义一个lambda表达式，用于获取命令行参数
        return idx < argc ? argv[idx] : NULL;
    };
    string val; // 定义一个字符串变量val
    RedisConnect redis; // 创建RedisConnect对象
    const char* ptr = NULL; // 定义一个空指针变量ptr
    const char* cmd = GetCmdParam(1); // 获取命令行参数列表中的第2个参数，赋值给变量cmd
    const char* key = GetCmdParam(2); // 获取命令行参数列表中的第3个参数，赋值给变量key
    const char* field = GetCmdParam(3); // 获取命令行参数列表中的第4个参数，赋值给变量field
    int port = 6379; // 定义一个整型变量port，赋值为6379
    const char* host = getenv("REDIS_HOST"); // 获取环境变量REDIS_HOST中的值，赋值给变量host
    const char* passwd = getenv("REDIS_PASSWORD"); // 获取环境变量REDIS_PASSWORD中的值，赋值给变量passwd
    if (host) // 如果host不为空
    {
        if (ptr = strchr(host, ':')) // 如果在host中找到冒号
        {
            static string shost(host, ptr); // 将host中冒号前面的部分赋值给变量shost
            port = atoi(ptr + 1); // 将冒号后面的部分转换成整型，赋值给变量port
            host = shost.c_str(); // 将变量shost转换成C风格字符串，赋值给变量host
        }
    }
    if (host == NULL || *host == 0) host = "127.0.0.1"; // 如果host为空或者host的第一个字符为0，将host赋值为"127.0.0.1"
    if (redis.connect(host, port)) // 尝试连接Redis服务器
    {
        if (passwd && *passwd) // 如果passwd不为空
        {
            if (redis.auth(passwd) < 0) // 尝试使用passwd进行身份验证
            {
                ColorPrint(eRED, "REDIS[%s][%d]验证失败\n", host, port); // 打印提示信息
                return -1; // 返回-1
            }
        }
        if (cmd == NULL) // 如果cmd为空
        {
            ColorPrint(eRED, "%s\n", "请输入要执行的命令"); // 打印提示信息
            return -1; // 返回-1
        }
        int res = 0; // 定义一个整型变量res，赋值为0
        string tmp = cmd; // 创建一个字符串变量tmp，赋值为cmd
        std::transform(tmp.begin(), tmp.end(), tmp.begin(), ::toupper); // 将tmp中的所有字符转换成大写字母形式
        if (tmp == "DELS" && key && *key) // 如果tmp等于"DELS"并且key不为空
        {
            vector<string> vec; // 创建一个字符串向量vec
            if (redis.keys(vec, key) > 0) // 获取所有匹配key的键名，返回值大于0表示获取成功
            {
                if (CheckCommand("确认要删除键值[%s]？", key)) // 弹出一个确认对话框，确认是否要删除键值
                {
                    ColorPrint(eWHITE, "%s\n", "--------------------------------------"); // 打印分隔线
                    for (const string& item : vec) // 遍历向量vec中的所有键名
                    {
                        if (redis.del(item)) // 删除键名为item的键值，返回值大于0表示删除成功
                        {
                            ColorPrint(eGREEN, "删除键值[%s]成功\n", item.c_str()); // 打印提示信息
                        }
                        else
                        {
                            ColorPrint(eRED, "删除键值[%s]失败\n", item.c_str()); // 打印提示信息
                        }
                    }
                    ColorPrint(eWHITE, "%s\n\n", "--------------------------------------"); // 打印分隔线
                }
            }
            else
            {
                ColorPrint(eRED, "删除键值[%s]失败\n", key); // 打印提示信息
            }
        }
        else // 如果不是删除命令
        {
            int idx = 1; // 定义一个整型变量idx，赋值为1
            RedisConnect::Command request; // 创建一个RedisConnect::Command对象request
            while (true) // 循环处理命令行参数
            {
                const char* data = GetCmdParam(idx++); // 获取第idx个命令行参数，赋值给变量data
                if (data == NULL) break; // 如果data为空，跳出循环
                request.add(data); // 将data添加到request中
            }
            if ((res = redis.execute(request)) >= 0) // 执行Redis命令，返回值大于等于0表示执行成功
            {
                ColorPrint(eWHITE, "执行命令[%s]成功[%d][%d]\n", cmd, res, redis.getStatus()); // 打印提示信息
                const vector<string>& vec = request.getDataList(); // 获取执行结果列表
                if (vec.size() > 0) // 如果执行结果列表不为空
                {
                    ColorPrint(eWHITE, "%s\n", "--------------------------------------"); // 打印分隔线
                    for (const string& msg : vec) // 遍历执行结果列表中的所有元素
                    {
                        ColorPrint(eGREEN, "%s\n", msg.c_str()); // 打印提示信息
                    }
                    ColorPrint(eWHITE, "%s\n", "--------------------------------------"); // 打印分隔线
                    ColorPrint(eWHITE, "共返回%ld条记录\n\n", vec.size()); // 打印提示信息
                }
            }
            else // 如果执行失败
            {
                ColorPrint(eRED, "执行命令[%s]失败[%d][%s]\n", cmd, res, redis.getErrorString().c_str()); // 打印提示信息
            }
        }
    }
    else // 如果连接失败
    {
        ColorPrint(eRED, "REDIS[%s][%d]连接失败\n", host, port); // 打印提示信息
    }
    return 0; // 返回0
}
```

这段代码是一个使用Redis的命令行工具，通过命令行参数传入Redis的命令和参数，然后执行相应的命令。其中，程序首先获取Redis服务器的连接信息，包括主机名、端口、密码等，然后根据命令行参数执行相应的Redis命令。 具体的执行过程如下：

1. 定义了一个lambda函数GetCmdParam，用于获取命令行参数，方便后续的代码使用。
2. 获取Redis服务器的连接信息，包括主机名、端口、密码等。如果环境变量中定义了主机名和端口号，则从环境变量中获取；否则使用默认的值127.0.0.1和6379。
3. 如果Redis服务器连接成功，则根据命令行参数执行相应的Redis命令。如果命令是DELS，则删除指定的键值；否则执行Redis命令，并输出执行结果。
4. 如果Redis服务器连接失败，则输出连接失败的信息。 需要注意的是，这段代码使用了C++11的lambda表达式和auto关键字，以及STL中的vector和string等容器类，代码风格比较现代化。

------

### auto GetCmdParam = [&](int idx)

```c++
auto GetCmdParam = [&](int idx){
	return idx < argc ? argv[idx] : NULL;
};
```

这段代码定义了一个lambda表达式，用于获取命令行参数。

- lambda表达式的定义以方括号[]开始，其中的&表示以引用的方式捕获外部变量，这里的外部变量是argc和argv。
- lambda表达式的返回值类型由参数idx的类型推导得出，即如果idx小于argc，则返回argv[idx]，否则返回NULL。
	- 该表达式的作用是避免访问不存在的参数，防止程序崩溃。
- 这段代码的作用是通过lambda表达式**封装了获取命令行参数的逻辑**，使得后续的代码可以更加简洁地使用命令行参数。

------

## makefile文件

```makefile
target: app

app: RedisConnect.h RedisCommand.cpp
ifdef WINDIR
	g++ -std=c++11 -pthread -DXG_MINGW -o redis RedisCommand.cpp -lws2_32 -lpsapi -lm
else
	g++ -std=c++11 -pthread -o redis RedisCommand.cpp -lutil -ldl -lm
endif
	
clean:
	@rm redis
```

- 该Makefile文件中定义了一个名为"app"的目标，该目标依赖于RedisConnect.h和RedisCommand.cpp两个文件。
- 在定义"app"目标的同时，使用了一个条件编译指令，即ifdef WINDIR。
	- 当WINDIR被定义时，表示编译系统为Windows系统，此时使用g++编译RedisCommand.cpp文件生成名为redis.exe的可执行文件，并链接-lws2_32、-lpsapi和-lm库文件。
	- 否则，表示编译系统为Unix/Linux系统，此时使用g++编译RedisCommand.cpp文件生成名为redis的可执行文件，并链接-lutil、-ldl和-lm库文件。
- 在make命令执行时，如果没有指定目标，默认会执行第一个目标，即"app"目标。同时，该Makefile文件还定义了一个名为"clean"的伪目标，用于清除生成的redis或redis.exe可执行文件。