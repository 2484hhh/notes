# C

## 一、注意小点

```cpp
1.不要返回局部变量的地址，局部变量在函数执行之后就被释放了，释放的内存就没有权限操作。

2.堆区通过malloc和free申请和释放内存。

3.给指针进行开辟内存时，传入的函数中不要用同等级的指针，需要传入指针的地址，函数中接受这个地址后在进行开辟内存。

4.static和extern区别：
	static特点：在程序运行前分配内存，程序运行结束后，生命周期结束，在本文件内都可以使用extern可以扩展变量作用域

5.在C语言中，const修饰的局部变量  称为伪常量，不可以初始化数组。
```

## 二、函数相关

### 1.宏函数

```c
	1、宏函数需要加小括号修饰，保证运算的完整性，如：
		#define MYADD(x,y) ((x)+(y))
	2、通常会将频繁、短小的函数写成宏函数
	3、宏函数 会比普通函数在一定程度上效率高，省去普通函数入栈、出栈时间上的开销
```



### 2.函数调用流程

```cpp
	1、函数形参入栈时，参数传递顺序是从左向右还是从右向左？
		答：函数调用方管理出栈：从右至左参数入栈，
	2、a和b是由谁管理释放？是main函数（主调函数）管理还是func函数（被调函数）管理？
		答：主调函数  main函数
	调用惯例 出栈管理方	  参数传递	       名字修饰
	cdecl	函数调用方	从右至左参数入栈	下划线+函数名 
	stdcall	函数本身	从右至左参数入栈	下划线+函数名+@+参数字节数
	fastcall函数本身	前两个参数由寄存器传递，其余参数通过堆栈传递。	@+函数名+@+参数的字节数
	pascal	函数本身	从左至右参数入栈	较为复杂，参见相关文档

	int func(int a,int b)
	{
		int t_a = a;
		int t_b = b;
		return t_a + t_b;
		}

	int main()
	{
		int ret = 0;
		ret = func(10, 20);
		return EXIT_SUCCESS;
	}
```



### 3.调用惯例

```cpp
	1.主调函数和被调函数必须要有一致约定，才能正确的调用函数，这个约定我们称为调用惯例。
	2.调用惯例 包含内容：出栈管理方、参数传递顺序、函数名称修饰。
	3.C/C++默认调用惯例：cdecl 从右到左，主调函数管理出栈。

PS:堆区数据只要没释放，都可以使用！
```

## 三、指针相关

```cpp
1.栈的生长方向和内存存放方向
	1.栈底---高地址；栈顶---低地址
	2.内存存放方向：
		高位字节数据---高地址，低位字节数据---低地址
										————小端对齐方式
2.指针不允许向NULL和非法地址拷贝内存：
	void test()
	{
		char *p = NULL;
		//给p指向的内存区域拷贝内容
		strcpy(p, "1111"); //err
	
		char *q = 0x1122;
		//给q指向的内存区域拷贝内容
		strcpy(q, "2222"); //err		
	}

3.野指针的导致情况：
	1.指针变量未初始化: int * p;
	2.指针释放后未制空
	3.指针操作超越变量作用域
        
4.指针的步长：
	1.代表指针+1之后跳跃的字节数
	2.解引用的时候，解出的字节数量
	3.#include <stddef.h>  offsetof(结构体名称,属性)找到对应的偏移量
        
5.指针做函数参数的输入输出特性
	1.输入特性：在主调函数中分配内存，被调函数中使用
	2.输出特性：在被调函数中分配内存，主调函数使用
      
6.指针的易错点
	1.越界
	2.指针叠加会不断改变指针指向
	3.返回局部变量地址
	4.同一块内存释放多次（不可以释放野指针）

7.const的使用场景
	const使用 修饰形参  防止误操作

8.二级指针做函数参数的输入输出特性
	1.输入特性：创建在堆区和栈上，其释放方式不同，且数组在做函数参数时，会退化成指针，指向第一个元素的地址，如果是指针数组，则此时类似二级指针。
	2.输出特性：被调函数分配内存，主调函数使用
9.二级指针练习--文件操作
	1.读取配置文件信息，并且将信息存放到 数组中
	2.释放堆区，关闭文件
```



## 四、字符串相关

```cpp
1.字符串强化训练
	1.字符串结束标志 \0
	2.sizeof 和 strlen
	3.拷贝字符串：[],指针
    
2.sprintf使用
	sprintf(目标字符串，格式化内容，占位参数)，返回值为有效字符串长度
    
3.calloc 和 realloc（都在堆区分配内存）
	1.calloc
		#include <stdlib.h>
		void *calloc(size_t nmemb, size_t size);
		功能：
		在内存动态存储区中分配nmemb块长度为size字节的连续区域。calloc自动将分配的内存置0。
		参数：
		nmemb：所需内存单元数量
		size：每个内存单元的大小（单位：字节）
		返回值：
			成功：分配空间的起始地址
			失败：NULL
	2.realloc
		#include <stdlib.h>
		void *realloc(void *ptr, size_t size);
		功能：
		重新分配用malloc或者calloc函数在堆中分配内存空间的大小。
		realloc不会自动清理增加的内存，需要手动清理，如果指定的地址后面有连续的空间，那么就会在已有地址基础上增加内存，如果指定的地址后面没有空间，那么realloc会重新分配新的连续内存，把旧内存的值拷贝到新内存，同时释放旧内存。
		参数：
		ptr：为之前用malloc或者calloc分配的内存地址，如果此参数等于NULL，那么和realloc与malloc功能一致
		size：为重新分配内存的大小, 单位：字节
		返回值：
		成功：新分配的堆内存地址
		失败：NULL
		注意：如果重新分配的内存比原来大，那么不会初始化新空间为0，如果比原来小，那么释放后续空间，只有权限操作申请空间
            
4.sscanf使用

 	1. int sscanf(const char *str, const char *format, ...);
    	功能：
    	    从str指定的字符串读取数据，并根据参数format字符串来转换并格式化数据。
    	参数：
    		str：指定的字符串首地址
    		format：字符串格式，用法和scanf()一样
    	返回值：
    		成功：成功则返回参数数目，失败则返回-1
    		失败： - 1

    2.忽略：
    	格式	       作用
    	%*s或%*d	  跳过数据
    	%[width]s	读指定宽度的数据
    	%[a-z]	  匹配a到z中任意字符(尽可能多的匹配)
    	%[aBc]	  匹配a、B、c中一员，贪婪性
    	%[^a] 	  匹配非a的任意字符，贪婪性
    	%[^a-z]	  表示读取除a-z以外的所有字符
    	
```



## 五、位和位移运算

```cpp
1.位运算
	1.按位取反 ~ 0变1  1变0
	2.按位与&  全1为1，一0为0
	3.按位或 |  全0为0，一1为1 //可以将数字中指定位置变为1
	4.按位异或 ^  相同为0，不同为1  可用来交换两数

2.位移运算
	1.左移：左移x位代表乘以2^x次方
	2.右移：右移x位代表除以2^x次方，有些机器用0填充高位，有些机器用1填充高位，如果是无符号类型，都是用0填充
```



## 六、数组

```cpp
1.一维数组名称
	1.除了两种特殊情况外，都是指向数组第一个元素的指针：
		1.sizeof统计数组长度
		2.对数组名取地址，得到数组指针，步长是整个数组
	2.数组名是指针常量，指针的指向不可以修改，而指针指向的值可以
	3.传参数时候，int arr[] 可读性更高
	4.数组索引下标可以为负数

2.数组指针的定义方式
	1.先定义出数组类型，再通过类型定义数组指针变量：
    	typedef int(array_type)[5];array_type * arrp = &arr;
	2.先定义数组指针类型，再通过类型定义数组指针变量：
        typedef int(*array_type)[5];array_type arrp = &arr;
	3.直接定义数组指针变量:int(*p)[] = &arr;

3.二维数组名
	1.除了两种特殊情况外，二维数组名称是指向第一个一维数组 的数组指针
		//特殊情况1  sizeof 统计二维数组大小
		//特殊情况2 对数组名取地址 int (*p)[3][3]=&arr  获取的是二维数组的 数组指针
	2.二维数组做函数参数
		void printArray(int array[3][3],int row,int col)
		void printArray(int array[][3],int row,int col)
		int array[3][3] 等价于一维数组指针 int  (*array)[3]
	3.数组指针 和 指针数组
		数组指针：指向数组的指针
		指针数组: 由指针组成的数组
    4.int strcmp(const char* str1, const char* str2);
        其中str1和str2可以是字符串常量或者字符串变量，返回值为整形。返回结果如下规定：
        ① str1小于str2，返回负值或者-1（VC返回-1）;
        ② str1等于str2，返回0;
        ③ str1大于str2，返回正值或者1（VC返回1）;
```

## 七、结构体和内存对齐

```cpp
1.结构体基本概念：
    1.加typedef 可以给结构体起别名
    2.不加typedef，可以直接创建一个结构体变量
    3.结构体声明  可以是匿名
    4.在栈上和堆区创建结构体
    5.在栈上和堆区创建结构体变量数组
    
2.结构体深浅拷贝：
    1.系统提供给的赋值操作是  浅拷贝--简单值拷贝，逐字节拷贝
    2.如果结构体中有属性 创建在堆区，就会出现问题，在释放期间，一段内存重复释放，一段内存泄露
    	解决方案：自己手动去做赋值操作，提供深拷贝
    
3.结构体偏移量
    1.获取属性偏移
    	1.1  offsetof(struct Person,结构体内容)
    	1.2  （int）&(p->b)-(int)p //结构体内某元素地址 - 结构体初始地址
    2.通过偏移量操作内存
    3.结构体嵌套结构体时，将子结构体展开对待即可
4.内存对齐
    1.查看对其模数：#pragma pack(show) ，默认对齐模数为8，修改show即可修改
    2.对齐规则：
    	//从第一个属性开始 从0开始偏移
		//第二个属性开始 要放在该类型的整数倍与 对齐模数 比 取小的值 的整数倍
		//所有的属性都计算完后，在整体做二次偏移，将整体计算的结果，要放在 结构体最大类型 与对齐模数  比取			小的值的 整数倍上
    3.结构体嵌套结构体时，子结构体放在该结构体中最大最大类型 和对齐模数比 的整数倍上即可。
```

## 八、文件读写

```cpp
1.文件读写
    1.缓冲区优势：
    	1.1 提高硬件寿命
    	1.2 提高读写效率 //直接从内存进行读写
    2.文件读写：
        	按照字符读写文件： fgetc(), fputc()
        	按照行读写文件： fputs(), fgets()
        	按照块读写文件   fread(), fwirte() //参数1：数据地址  参数2：块的大小  参数3：块个数   参数4：文件指针
        	按照格式化读写文件： fprintf(), fscanf()
        	按照随机位置读写文件： fseek(), ftell(), rewind()（光标置首）	
    	2.1  while((ch = fgetc(f_read))!=EOF) //判断是否到文件尾
    3.int fseek(FILE *stream, long offset, int whence);
        功能：移动文件流（文件光标）的读写位置。
        参数：
            stream：已经打开的文件指针
            offset：根据whence来移动的位移数（偏移量），可以是正数，也可以负数，如果正数，则相对于						whence往右移动，如果是负数，则相对于whence往左移动。如果向前移动的字节数超过了文件						开头则出错返回，如果向后移动的字节数超过了 文件末尾，再次写入时将增大文件尺寸。
            whence：其取值如下：
                    SEEK_SET：从文件开头移动offset个字节
                    SEEK_CUR：从当前位置移动offset个字节
                    SEEK_END：从文件末尾移动offset个字节
                    返回值：
                        成功：0
                        失败：-1
	4.error 宏 利用perror 打印错误提示信息；  rewind（）将光标置首
    5.注意事项
          5.1  不要用feof按照字符方式读文件，原因有滞后性，会读出EOF
          5.2  如果属性开辟到堆区，不要存指针到文件中，要将指针指向的内容存放到文件中   
```

## 九、函数指针

```cpp
1.链表
	1.1 由节点组成，节点由数据域和指针域组成
	1.2 静态链表：在栈上分配内存； 动态链表：在堆上分配内存	
     
2. 函数指针
	2.1 函数名本质就是一个函数指针
	2.2 可以利用函数指针 调用函数 ()
	2.3 定义方式
		2.3.1	//1、先定义出函数类型，再通过类型定义函数指针
				typedef void(FUNC_TYPE)(int, char);
		2.3.2  //2、定义出函数指针类型，通过类型定义函数指针变量
				typedef void(* FUNC_TYPE2)(int, char);
		2.3.3  //3、直接定义函数指针变量
				void(* pFunc3)(int, char) = func;
	2.4  函数指针和指针函数的区别
		//函数指针：指向了函数的指针
		//指针函数：函数返回值是指针的函数
    2.5 函数指针的数组定义方式：void(*pArray[3])();

3.函数指针做函数参数（回调函数）

4.动态链接定义  __declspec(dllexport)
    疑问一：__declspec(dllexport)是什么意思？
    	动态链接库中定义有两种函数：导出函数(export  function)和内部函数(internal  	function)。 导出函数可以被其它模块调用，内部函数在定义它们的DLL程序内部使用。

    疑问二：动态库的lib文件和静态库的lib文件的区别？
        在使用动态库的时候，往往提供两个文件：一个引入库（.lib）文件（也称“导入库文件”）和一个DLL（.dll）文件。虽然引入库的后缀名也是“lib”，但是，动态库的引入库文件和静态库文件有着本质的区别，对一个DLL文件来说，其引入库文件（.lib）包含该DLL导出的函数和变量的符号名，而.dll文件包含该DLL实际的函数和数据。在使用动态库的情况下，在编译链接可执行文件时，只需要链接该DLL的引入库文件，该DLL中的函数代码和数据并不复制到可执行文件，直到可执行程序运行时，才去加载所需的DLL，将该DLL映射到进程的地址空间中，然后访问DLL中导出的函数。

5.#pragma comment(lib，"路径") 自动查找lib并应用

```



# C++

## 一、基础知识

```cpp
  1.C++概述： 
		1.1 c++两大编程思想： 面向对象和泛型编程。
        1.2 ANSI 在1998年制定出第一套标准。
    
    2.双冒号'::'
        ::代表作用域，如之前不填，则表示为全局作用域

	3.命名空间
        3.1  命名空间用途：解决名称冲突  KingGlory::goAtk();   LOL::goAtk()
        3.2  命名空间下可以放  变量，函数，结构体，类······
        3.3  命名空间 必须要申明在全局作用域下
        3.4  命名空间可以嵌套命名空间  // B::A::m 
        3.5  命名空间是开放的，可以随时给命名空间添加新成员
        3.6  命名空间可以是匿名的  //此时相当于在变量前加了关键字 static
        3.7  命名空间可以起别名  namespace A= LongName;

	4.using声明以及编译指令
        4.1 //using声明
			//using KingGlory::sunwukongId;
        	//当using声明与就近原则同时出现，出错，尽量避免
        4.2  //using编译指令
            using namespace KingGlory; //打开一个命名空间
            //当using编译指令 与 就近原则 同时出现，优先使用就近原则  
			//当using编译指令有多个，需要加作用域区分

    5.尽量用const代替define 
        5.1 const有类型，可进行编译器类型安全检查。#define无类型，不可进行类型检查.
        5.2 const有作用域，而#define不重视作用域，默认定义处到文件结尾.如果定义在指定作用域下有效的常量，那么#define就不能用。
```

## 二、C和C++的不同

```cpp
C++对C语言的增强和扩展（一）
        	1.全局变量检测增强 //int a; int a=10;在C中可行，C++会报错重定义；
        	2.函数检测增强 //返回值、形参类型、函数调用参数个数都没有检测
        	3.类型转换检测增强 //等号左右类型必须相同才能赋值
        	4.struct增强  //C语言下结构体不可以有函数，且创建结构体变量的时候必须加关键字struct
        	5.bool类型扩展 //C语言下没有这个类型  代表真和假（True和False） 占1个字节
        	6.三目运算符（？：）增强 //a > b ? a:b 返回的是a或b变量，c语言里下是返回值
        	7.const增强  //C++下const修饰的变量  称为常量，C中为伪常量（不可初始化），且C下，const修饰的全局变量默认是外部链接属性，C++下const修饰的全局变量默认是内部链接属性，可以加extern提高作用域
        	8.const分配内存
        		8.1 对const变量取地址，会分配一个临时内存 
        			const int a=10;int*p=(int *)&a
                8.2 使用普通变量来初始化const变量
                8.3 对于自定义数据类型也会分配内存
```

## 三、引用

```cpp
1.引用(int &b =a;)
        1.1  int &b;//引用必须要初始化,
        1.2  引用一旦初始化后，就不可以引向其他变量
        1.3  对数组建立引用

               1.	直接建立引用
                  int arr[10];
                  int(&pArr)[10] = arr;
                  		2.先定义出数组类型，再通过类型定义引用
                  typedef int(ARRAT_TYPE)[10];
                  //类型 &别名=原名
                  ARRAT_TYPE & pArr2 = arr;
2.参数传递方式
    8.1 值传递 
    8.2 地址传递
    8.2 引用传递  void mySwap(int &a,int &b)//int &a=a;  int &b=b;

3.引用注意事项
    3.1 引用必须引一块合法内存空间
        int &a = 10;//错误
    3.2 不要返回局部变量
        int &ref = func();//错误，函数会释放
    3.3当函数返回值是引用，那么函数的调用可以作为左值去运算
        func2() = 1000;
    3.4 引用的本质在C++内部实现是一个指针常量
4.常量的引用
    4.1 const int &ref=10; 
//加了const之后，相当于写成 int temp=10;const int &ref=temp
10.2 常量引用的使用场景  修饰函数中的形参，防止误操作修改其值
    void show(const int &a)
```

## 四、类相关

### 	1.内联函数

​      

```cpp
  //函数的声明和实现必须同时加关键字 inline 才算内联函数
        //优点：解决宏缺陷，本身是一个函数，带来宏优势，以空间换时间，在适当的时候做展开
        inline void func(int a);
        inline void func(int a){}
        //任何在类内部定义的函数自动成为内联函数
        c++内联编译会有一些限制，以下情况编译器可能考虑不会将函数进行内联编译：
        	1.不能存在任何形式的循环语句
        	2.不能存在过多的条件判断语句
        	3.函数体不能过于庞大
        	4.不能对函数进行取址操作
    2.函数的默认参数和占位参数
        2.1 默认参数 语法 形参 类型 变量 =默认值
            int func(int a=10, int b=10,int c=10)
        2.2 注意事项：
            1. 如果有一个位置有了默认参数，则该位置之后参数都必须有默认值
            2. 函数的声明和实现，只能有一个提供默认参数，不可以同时加默认参数
```

### 2.函数重载

```cpp
实现函数重载的条件：
        	同一个作用域
        	函数名相同
        	参数个数不同
        	参数类型不同
        	参数顺序不同
        ps://返回值类型不同不可以做为函数重载的条件；
		   //加const和不加const的引用也可以作为重载条件
                void myFunc(int &a)
                void myFunc(const int &a)
```

### 3.extern浅析   

```cpp
 在C++中调用C语言文件方法：             
		4.1  extern "C" void fun() //告诉编译器，fun函数用C语言方式做链接
		4.2  函数很多时一般在.h文件中处理
             #ifdef __cplusplus  
             extern "C"
             {
             #endif      
             #include<stdio.h>
              void fun();
              void fun();
			 #ifdef __cpulspuls
			}
			#endif
```

### 4.构造函数和析构函数

```cpp
1. 构造函数 （对象创建时调用）
    //没有返回值，不用写void
    //函数名 与类名相同
    //可以有参数，可以发生重载
    //构造函数 由编译器自动调用一次，无需手动调用
2. 析构函数 （对象释放时调用）
    //没有返回值  不用写void
    //函数名与类名相同   函数名前加 ~（波浪号）
    //不可以有参数，不可以发生重载
    //析构函数也是由编译器自动调用一次，无需手动调用   

3.构造函数的分类和调用
        3.1 分类
        	1.按照参数分类： 无参构造（默认构造函数) 和 有参构造
			2.按照类型分类： 普通构造函数  和 拷贝构造函数	
        		拷贝构造函数定义：Person（const Person& p）//const防止误操作，引用传递
        3.2 调用
            1、括号法
            Person p1(10);//调用Person(int age)
            Person p2(p);

            2、显示法
            Person p3 = Person(10);//有参构造
            Person p4 = Person(p3);//拷贝构造

            Person(10);//匿名对象  特点：当前行执行完后，立马释放
            cout << "aaa" << endl;

            3、隐式法
            Person p5 = 10;//Person p5 = Person(10)
            Person p6 = p5;

            注意事项 1
                不要用括号法 调用无参构造函数 Person p(),编译器认为代码是函数的声明
            注意事项 2
                不要用拷贝构造函数  初始化 匿名对象 Person (p3); 编译器认为Person p3对象实例化，如果已经有p3   则p3重定义。   Person(p3);

	4.拷贝构造函数的调用时机
        1、用已经创建好的对象来初始化新的对象。
        2、用值传递的方式  给函数参数传值。 
        3、以值方式 返回局部对象。
        
    5.构造函数的调用规则
        5.1 编译器会给一个类 至少添加4个函数， 默认构造函数（空实现）， 析构函数（空实现），拷贝构造（值拷贝） operator=(值拷贝)。
        5.2 如果我们自己提供了有参构造函数，编译器就不会提供默认构造函数，但是依然会提供拷贝构造函数。
        5.3 如果我们自己提供了拷贝构造函数，编译器就不会提供其他构造函数。
```

### 5.静态成员变量    

```cpp
 1.静态成员变量： 编译阶段就分配了内存； 类内声明，类外初始化； 所有对象都共享一份数据
        1.1 两种访问方式
        	1、通过对象进行访问。
                Person p1;
                cout << p1.m_A << endl;
            2、通过类名进行访问
                cout << Person::m_A << endl;
		1.2 静态成员变量也是有访问权限的，私有权限类外访问不到. 
```

​            

### 6.静态成员函数

```cpp
	7.1 所有对象都共享一个函数。
    7.2 静态成员函数可以访问静态成员变量 静态成员函数不可以访问非静态成员变量。
    7.3 静态成员函数也是有访问权限，私有权限类外访问不到。
    7.4 两种访问方式：通过对象进行访问、通过类名进行访问。
```



### 7.单例模式--主席类案例

```cpp
7.1 通过一个类 只能实例化唯一的一个对象
7.2 私有化
    7.2.1 默认构造
    7.2.2 拷贝构造
	7.2.3 唯一实例指针
7.3 对外提供 getInstance接口，将指针返回
```

​       

### 8.C++对象模型初探

```cpp
8.1 类对象中的变量和函数是分开存储。
8.2 只有非静态数据成员直接内含在类对象中，其他都不在类对象中.
8.3 空类的sizeof结果为1。
```

​            

### 9.this指针

```cpp
9.1 this指针 指向 被调用的成员函数 所属的对象。
9.2 解决名称冲突
9.3 this指针  隐式加在每个成员函数中
9.4 *this 就是本体
9.5 p1.personADD(p2).personADD(p2).personADD(p2); //链式编程思想
```



### 10.空指针访问成员函数

​        如果成员函数中没用到this指针，可以用空指针调用成员函数，如果用到this，则需要加判断，this是否为空，防止程序崩掉；
​        

### 11.常函数和常对象

```cpp
11.1 常函数
    11.1.1 成员函数声明后面加const //修饰this指针，让指针指向的值不可以修改
    void showPerson() const //常函数 相当于  const Person * const this
    //this 指针的本质：Person * const this
    11.1.2 有些属性比较特殊，依然在常函数或者常对象中可以修改，需要加入关键字 mutable
11.2 常对象   const Person p1(10);
	11.2.1 常对象也不允许修改成员属性
    11.2.2 常对象只能调用常函数
```

​        

### 12.全局函数作为友元函数

```cpp
//利用friend关键字让全局函数 goodGay作为本类好朋友，可以访问私有成员
friend void goodGay(Building * building);//在类中做声明
```



### 13.类作为友元类

```cpp
//让GoodGay类作为Building的好朋友，可以访问私有成员
friend class GoodGay;//在Building类中做声明
```



### 14.类中的成员函数作为友元函数

```cpp
//让类中的成员函数作为友元
friend void GoodGay::visit();
```



## 五、几点注意   

### 1.深拷贝与浅拷贝的问题以及解决

​        如果堆区有属性开辟到堆区，利用编译器提供拷贝构造函数会调用浅拷贝带来析构重复释放堆区				内存的问题 //利用深拷贝解决浅拷贝问题，自己提供拷贝构造函数，重新开辟一块内存，实现深拷贝。      

### 2.初始化列表

```cpp
  Person(int a,int b,int c):m_a(a),m_b(b),m_c(c){}
//构造函数的名称后加：属性（值）,不用每个属性都赋值。
```



### 3.析构顺序

​		类对象做类中成员：当其他类对象作为本类成员，先构造其它类对象，再构造自身，因栈的先进后出特性，析构的顺序和构造相反。  

### 4.explict

用途： 防止利用隐式类型转换方式,(隐式法)来构造对象
        

### 5.new 和 delete

#### 5.1 malloc 和 new 区别

```cpp
1. malloc 和 free 属于库函数，  new 和 delete 属于运算符

2. malloc不会调用构造函数，new 会调用构造函数

3. malloc返回void * C++下需要强转类型，  new返回创建的对象的指针 
```

#### 5.2 注意事项：

​	不要用void * 去接收new出来的对象，无法调用析构函数

#### 5.3 利用new开辟数组

```cpp
		Person *pPerson = new Person[10];//堆区开辟数组，一定会调用默认构造函数
		delete [] pPerson;   //释放数组时 需要加[]
		//栈上开辟数组时，可以没有默认构造函数,直接调用有参构造即可
```



## 六、运算符重载

### 1.运算符重载

```cpp
对于自定义的数据类型，利用运算符重载，可以让符号有新的含义 
    //关键字 operator
    利用加号重载  实现 p1+p2 两个Person类的数据相加
    1.1 利用成员函数实现加号运算符重载 
        Person operator +(Person &p)
        //Person p3 = p1.operator+(p2);//成员函数本质
    1.2 利用全局函数实现加号运算符重载
        Person operator+(Person &p1, Person &p2)
        //Person p3 = operator+(p1, p2);//全局函数本质
```

​        

### 2.左移运算符重载

```cpp
//利用全局函数实现<<重载
ostream& operator<<(ostream &cout, Person& p1)
{
    cout << "m_a=  " << p1.m_a << "m_b= " << p1.m_b ;
    return cout;
}
```

​	

### 3.前置递增运算符重载

​        

```cpp
//前置++ 重载
MyInter& operator++()
{
    this->m_Num++;
    return *this;
}

//后置++ 重载
MyInter operator++(int)//此时不能返回引用，而是返回值，因为temp是局部变量
{
    //先记录初始状态
    MyInter temp = *this;
    this->m_Num++;
    return temp;
}
PS：前置++ 效率高于后置++效率，因为后置++会调用拷贝构造来创建新的数据
```

​            

### 4.指针运算符重载

​     

```cpp
用途：托管new醋回来的对象的释放
    class SmartPoint
    {
        public:
        SmartPoint(Person * person)
        {
            this->m_person = person;
        }
        //重载 -> 运算符
        Person* operator->()
        {
            return this->m_person;
        }
        //重载 * 运算符
        Person& operator*()
        {
            return *m_person;
        }
        ~SmartPoint()
        {
            if (this->m_person)
            {
                cout << "SmartPoint析构函数调用" << endl;
                delete this->m_person;
                this->m_person = NULL;
            }
        }
        private:
        Person *m_person;
    };
```



### 5.关系运算符重载

```cpp
        重载==  bool operator==(Person &p)  
        重载!=  bool operator!=(Person &p) 
```

​    

### 6.函数调用运算符重载

```cpp
重载（）	//因使用时很像函数调用，因此称为仿函数
    void operator()(string text)
    int operator()(int a, int b)  //其写法不固定，看需求而定

    cout << MyAdd()(1, 1) << endl;  //匿名函数对象，特点：当前行执行完立即释放
```

### 	7.符号重载总结

```cpp
	=, [], () 和 -> 操作符只能通过成员函数进行重载 
    << 和 >>只能通过全局函数配合友元函数进行重载 
    不要重载 && 和 || 操作符，因为无法实现短路规则
```



## 七、继承

### 1.继承基本语法

class 子类 ： 继承方式 父类//子类又叫派生类，父类又叫基类
        

### 2.继承方式

​	2.1 公共继承：父类中公共权限，子类中变为公共权限；
​       	 父类中保护权限，子类中变为保护权限；
​       	 父类中私有权限，子类访问不到；

​	2.2保护继承： 父类中公共权限，子类中变为保护权限；
​       	 父类中保护权限，子类中变为保护权限；
​        	父类中私有权限，子类访问不到；
​        

```cpp
2.3私有继承： 父类中公共权限，子类中变为私有权限；
    	父类中保护权限，子类中变为私有权限；
    	父类中私有权限，子类访问不到；
```


### 3.继承中的对象模型

```cpp
	3.1 父类中的私有属性，子类继承了但是访问不到，是由编译器给隐藏了
    3.2 利用开发工具查看对象模型 进入VS开发人员工具：
    跳转盘符->跳转路径->输入代码：
        cl /d1 reportSingleClassLayout类名 test.cpp
```

​        

### 4.继承中的构造和析构

​        4.1 先调用父类构造，再调用其他自身构造，析构的顺序与构造相反
​        4.2 Son2(int a=1000):Base2(a) //利用初始化列表语法，显式调用父类中的其他构造函数
​        4.3 父类中  默认构造、析构、拷贝构造、operator=  是不会被子类继承下去
​       

### 5.继承中的同名成员及同名静态成员处理

​      

```cpp
5.1 利用作用域访问父类中的同名成员
5.2 当子类重新定义了父类中的同名成员函数，子类的成员函数会隐藏掉父类中所有重载版本的同名成员，可以利用作用域显示指定调用
5.3 同名静态成员
    1、通过对象访问
    Son s;
    cout <<"m_a="<< s.m_a << endl;
    cout <<"Base中m_a="<< s.Base::m_a << endl;

    2、通过类名访问
    cout << "m_a=" << Son::m_a << endl;
    cout << "m_a=" << Base::m_a << endl;

    //通过类名的方式 访问 

    cout << "m_a=" << Son::Base::m_a << endl;
```



### 6.多继承

```cpp
class A:public B1,public B2      
```

### 7.菱形继承

​       

```cpp
7.1 两个类有共同的父类 和共同的子类，发生菱形继承
7.2 菱形继承导致数据有两份，浪费资源
7.3 解决方案：利用虚继承可以解决问题
    class Sheep :virtual public Animal//虚继承语法，此时Animal类称为 虚基类
    //当发生虚继承后，Sheep和Tuo类中 继承了一个 vbptr指针（虚基类指针） 指向的是一个虚基类表vbtable
    //虚基类表中记录了偏移量，通过偏移量可以找到唯一的一个m_age变量    
```

### 	8.虚继承内部工作原理

```cpp
      SheepTuo st;
                st.m_age = 10;
                //通过Sheep找到偏移量
                //  *(int *)&st  此时解引用到了虚基类表中
```



## 八、多态

### 1.静态联编和动态联编

```cpp
1.1 静态多态和动态多态
    1.1.1 静态多态： 函数重载，运算符重载
    1.1.2 动态多态产生条件： 
			/*1.先有继承关系	
              2.父类中有虚函数，且子类重写父类中的虚函数,函数返回值，函数名字，函数参数，必须和父类中的虚函数一致
              3.父类的指针或引用 指向子类的对象 */
            void doSpeak(Animal &animal)  //Animal & animal =cat
    1.1.3 静态多态地址在编译时就绑定地址，地址早绑定，叫静态联编
    1.1.4 动态多态在运行阶段绑定地址，地址晚绑定，叫动态联编
```

###     2.虚函数原理剖析

```cpp
	当父类写了虚函数后，类内部结构发生改变，多了一个vfptr（虚函数表指针），指向选函数表，虚函数表内部记录着 虚函数的入口地址，当父类指针或引用子类对象时，发生多态，调用时候从虚函数中找函数入口。
    //函数指针 void(*)()
    PS:修改调用惯例  
        typedef void(__stdcall *FUNPOINT)(int) (FUNPOINT(*((int*)*(int *)animal+1)))(10)
```

​            

### 3.多态计算器案例

#### 3.1纯虚函数

​            如果一个类中包含纯虚函数，那么这个类就无法实例化对象了,这个类通常我们称为抽象类
​            抽象类的子类必须要重写父类中的纯虚函数，否则也属于抽象类，即不可实例化对象

#### 3.2.纯虚函数和抽象类

```cpp
语法: virtual int getResult() = 0;
	如果一个类中包含纯虚函数，那么这个类就无法实例化对象了,这个类通常我们称为抽象类,抽象类的子类必须要重写父类中的纯虚函数，否则也属于抽象类，即不可实例化对象。
```

### 4.虚析构和纯虚析构

#### 4.1 虚析构

```cpp
virtual~Animal() //虚析构
如果子类中有指向堆区的属性，那么要利用虚析构技术，在delete的时候，调用子类的析构函数,在父类的析构函数前加virtual关键字
```

#### 4.2 纯虚析构   

```cpp
virtual~Animal() = 0;//纯虚析构
	如果一个类中有了纯虚析构函数，这个类也属于抽象类，无法实例化对象。
Animal::~Animal(){}//纯虚析构 需要有声明 也需要有实现，类内声明，类外实现
```

### 	5.向上向下类型转换

```cpp
5.1 向下类型转换
    Animal *animal = new Animal;  Cat * cat = (Cat *)animal
//父类指针转换类型成子类指针，向下类型转换 ，不安全
    
5.2 向上类型转换
    Cat * cat = new Cat; Animal *animal = (Animal *)cat
//子类指针转换类型成父类指针，向上类型转换 ，安全
PS：如果发生了多态，那么转换永远都是安全的！！
```



### 7.重写 重载 重定义

```cpp
7.1重载，同一作用域的同名函数
    1.	同一个作用域
    2.	参数个数，参数顺序，参数类型不同
    3.	和函数返回值，没有关系
    4.	const也可以作为重载条件  //do(const Teacher& t){}  do(Teacher& t)

7.2重定义（隐藏基类）
    1.	有继承
    2.	子类（派生类）重新定义父类（基类）的同名成员（非virtual函数）

7.3重写（覆盖基类）
    1.	有继承
    2.	子类（派生类）重写父类（基类）的virtual函数
    3.	函数返回值，函数名字，函数参数，必须和基类中的虚函数一致
```



## 九、模板类

### 1.函数模板

```cpp
1.1 泛型编程 - 模板技术  特点：类型参数化
    
1.2 template <typename T>  //T代表一个通用的数据类型，告诉编译器，如果下面紧跟着的函数或者类中出现T不要报错
    
1.3 使用：
    1.3.1 自动类型推导，必须推导出一致的T数据类型，才可以正常的使用模板
    1.3.2 显式指定类型  mySwap<int>(a, b);//必须告诉编译器T类型才可以调用
```

### 2.函数模板和普通函数的区别以及调用规则

#### 2.1 区别  

​		函数模板：如果使用自动类型推导,是不可以发生隐式类型转换的
​		普通函数;可以发生隐式类型转换

#### 2.2 调用规则

```cpp
	1、如果函数模板和普通函数都可以调用，那么优先调用普通函数
    2、如果想强制调用函数模板，可以使用空模板参数列表//myPrint<>(a, b);
    3、函数模板也可以发生函数重载
    4、如果函数模板能产生更好的匹配（不用做隐式类型转换），那么优先使用函数模板
```

### 3.模板的实现机制

​		3.1 编译器并不是把函数模板处理成能够处理任何类型的函数
​       	3.2 函数模板通过具体类型产生不同的函数 --通过函数模板产生的函数称为模板函数
​        3.2 编译器会对函数模板进行/*两次编译*/，在声明的地方对模板代码本身进行编译，在调用的地方对参数替换后的代码进行编译。   

### 4.模板的局限性

```cpp
	4.1 模板并不是真正的通用，对于自定义数据类型，可以使用具体化技术，实现对自定义数据类型特殊使用。（先写出模板函数，再用具体化技术）
    4.2利用具体化技术  实现对自定义数据类型  提供特殊模板
        template<class T>
        bool myCompare(T &a, T &b){}
    template<>bool myCompare(Person&p1, Person &p2){}
```

### 5.类模板

```cpp
5.1 类模板和函数模板区别
     5.1.1、类模板不可以使用自动类型推导，只能用显示指定类型方式
     5.1.2、类模板中可以有默认参数，函数模板不行
         template<class NAMETYPE,class AGETYPE=int>
```

### 6.类模板中的成员函数创建时机

​	类模板中的成员函数，并不是一开始创建的，而是在运行阶段确定出T的数据类型才去创建

### 7.类模板做函数参数 

```cpp
7.1 指定传入类型 
    	void doWork(Person<string, int> &p)
7.2 参数模板化
    	template <class T1,class T2>
        void doWork2(Person<T1, T2> &p)
7.3 整个类 模板化 
        template<class T>
        void doWork3(T &p)
7.4 查看数据类型 
		typeid(T).name()

```

### 8.类模板碰到继承的问题以及解决 

```c++
	1.class Son :public Base<int> //必须要指定出父类中的数据类型，才能给子类分配内存
    2.可由用户自己定义数据类型：  
    template<class T1, class T2>
        class Son2 :public Base2<T2>
```

### 9.类模板中的成员函数在类外实现

```cpp
1.普通成员函数
    template<class T1, class T2>
    void Person<T1,T2>::showPerson()

2. 有参构造函数
    template<class T1, class T2>
    Person<T1,T2>::Person(T1 name, T2 age)
```

### 10.类模板的分文件编写问题及解决

​     	10.1 类模板中的成员函数，不会一开始创建，因此导致分文件编写时连接不到函数的实现，出现无法解析的外部命令错误
​        10.2 解决方式 ： 将类的声明和实现写到同一个文件中，将文件后缀名改为.hpp即可

### 11.类模板碰到友元函数问题以及解决

```cpp
template<class T1,class T2>
class Person
{
    1、友元函数 类内实现
    friend void showPerson(Person<T1, T2> &p)
    {
    	cout << "姓名：" << p.m_name << " 年龄：" << p.m_age << endl;
	} 
	2、友元函数 类外实现
	template<class T1, class T2>
	friend void showPerson2(Person<T1, T2> &p);

public:
	Person(T1 name, T2 age)
    {
        this->m_name = name;
        this->m_age = age;
    }

private:
    T1 m_name;
    T2 m_age;
};

template<class T1, class T2>
void showPerson2(Person<T1, T2> &p)
{
cout << "姓名：" << p.m_name << " 年龄：" << p.m_age << endl;
}
```



## 八、类型转换

```cpp
1.类型转换        
	1.1 静态类型转换  static_cast
        1.1 允许内置数据类型之间转换
        1.2 允许父子之间的指针或引用的转换
            语法：  static_cast<目标类型>(原对象或原变量)
            //将base转为Son *  父转子  向下类型转换 不安全
             Son * son2 = static_cast<Son *>(base);
			//son 转为Base * 子转父 向上类型转换 安全
             Base * base2 = static_cast<Base *>(son);

    1.2  动态类型转换 dynamic_cast
        1.2.1 不允许内置数据类型之间转换
        1.2.2 允许父子之间的指针或引用的转换
           1. 父转子 不安全  转换失败
  
           2. 如果发生多态，那么转换总是安全的，可以成功。
              cast<目标类型>(原对象或原变量)

    1.3  常量转换 const_cast
        1.3.1 只允许指针或者引用之间转换，不可以将非指针或非引用做const_cast转换
           语法： const_cast<目标类型>(原对象或原变量)
        
    1.4  重新解释转换 ： reinterpret_cast  最不安全的一种转换，不建议使用
```

## 九、异常

### 1.异常的基本语法

```cpp
C++异常处理关键字：  try 、 throw 、 catch

1. 可以出现异常的代码放到 try 块，利用 throw抛出异常，利用 catch 捕获异常；
    catch(类型)， 如果想捕获其他类型  catch(...)；
2. 如果捕获到的异常不想处理，而继续向上抛出，利用throw；
异常必须有函数进行处理，如果没有任何处理，程序自动调用 terminate 函数，让程序中断。
3. 异常可以是自定义数据类型
	throw MyException();//抛出MyException匿名对象
```

### 2.栈解旋 

   从 try 代码开始，到 throw 抛出异常之前，所有栈上的数据都会被释放掉，释放顺序和创建顺序相反，这个过程我们称为栈解旋。

### 3.异常的接口声明

```c++
在函数中，如果限定抛出异常的类型，，可以用异常的接口声明：
    void TestFunction01(){}  //可抛出所有类型异常
	void TestFunction02() throw(int, char, char*){} d
    //只能抛出int char char*类型异常
    void TestFunction03() throw(){} //不能抛出任何类型异常 
```

### 4.异常变量的生命周期

```cpp
  	4.1 抛出的是  throw MyException();  catch (MyException e)调用拷贝构造函数，效率低
	4.2 抛出的是  throw MyException();  catch (MyException &e)只调用默认构造函数，效率高 推荐，此时相当于给匿名对象起名，所以不会立马释放  //推荐使用
	4.3 抛出的是  throw &MyException();  catch (MyException *e) 匿名对象会提前释放掉，不能再非法操作
	4.4 抛出的是  throw new MyException(); catch (MyException *e) 只调用默认构造函数，自己要管理释放
```

### 5.异常的多态使用

```cpp
5.1 提供基类异常
    5.1.1  class BaseException
    5.1.2  纯虚函数 virtual void printError() = 0;
5.2 子类 空指针异常 和越界异常
    class NullPointerException : public BaseException
    class OutofRangeException :public BaseException
5.3 重写 virtual void printError()
5.4 利用父类引用接收子类对象  发生多态 catch (BaseException &e)
```

### 7.系统的标准异常

```cpp
7.1  引入头文件   #include<stdexcept>
7.2  抛出越界异常 throw out_of_range("...")
7.3  获取错误信息 	catch (exception &e)/*利用多态*/   		e.what()获取错误信息
```

### 8.编写自己的异常类

```cpp
8.1	编写myOutofRange 继承 Exception类
8.2	重写 virtual const char *  what() const
8.3	将sting 转为 const char * 
8.3.	 .c_str()
8.4	const char * 可以隐式类型转换为 string  反之不可以
8.5	测试，利用多态打印出错误提示信息
```

###     9.标准输入流

​      

```cpp
  9.1 cin.get() //一次只能读取一个字符
      
  9.2 cin.get(两个参数)
  char buf[1024] = { 0 };
  cin.get(buf,1024) //利用 cin.get 获取字符串时，换行符遗留在缓冲区中
      
  9.3 cin.getline   //获取字符串时，换行符不会被取走，也不再缓冲区中，而是之间扔掉
      
  9.4 cin.ignore() //忽略 默认忽略一个字符，如果填入参数X 代表忽略X个字符
      
  9.5 cin.peek  //偷窥 查看第一个字符
  9.6 cin.putback() //放回 放回原位置
```

### 10.标志位 

```cpp
cin .fail()  
// 0代表正常  1代表异常
//清空缓冲区 重置标志位 
cin.clear();
cin.sync();
//cin.ignore();//vs2013以上版本加入 
//如果标志位为 0  代表缓冲区正常    如果标志位为1   缓冲区异常
```

### 11.标准输出流

```cpp
cout.put() //向缓冲区写字符
cout.write() //从buffer中写num个字节到当前输出流中。

通过流成员函数
void test02() 
{
    int number = 99;
    cout.width(20);//指定宽度为20
    cout.fill('*');//填充
    cout.setf(ios::left);//左对齐
    cout.unsetf(ios::dec); //卸载十进制
    cout.setf(ios::hex); //安装十六进制
    cout.setf(ios::showbase);//显示基数
    cout.unsetf(ios::hex); //卸载十六进制
    cout.setf(ios::oct);//安装八进制
	cout << number << endl;
}

使用控制符 //引入头文件 #include<iomanip>
void test03() 
{
    int number = 99;
    cout << setw(20)//设置宽度
    << setfill('~')//设置填充
    << setiosflags(ios::showbase)//显示基数
    << setiosflags(ios::left)//设置左对齐
    << hex //显示十六进制
    << number
    << endl;
}
```

### 11.文件读写

```cpp
12.1 头文件 #include <fstream>
12.2 写文件 
    12.2.1 ofstream ofs("文件路径",打开方式 ios::out|ios::trunc)
    //ios::trunc 判断文件是否存在，是则删除文件内容，否则窜创新
    12.2.2 判断文件是否打开成功 ofs.isopen()
    12.2.3 ofs<<"..." //写入内容
    12.2.4 ifs.close() //关闭文件
12.3 读文件
    12.3.1 ifstream ifs("文件路径",打开方式 ios::in)
    12.3.2 判断文件是否打开成功 ifs.isopen()
    12.3.3  char buf[1024] = { 0 };ifs>>buf  //  用>>读取文件内容
    12.3.4 ifs.close() //关闭文件
```



## 十、STL

### 1.STL概论

```cpp
1.1	STL六大组件
    1.1.1	容器、算法、迭代器、仿函数、适配器、空间配置器
1.2	STL优点
    1.2.1	内建在C++编译器中，不需要安装额外内容
    1.2.2	不需要了解具体实现内容，只要熟练运用即可
    1.2.3	高复用，可移植，高性能
1.3	容器划分
    1.3.1	序列式容器 
    1.3.2	关联式容器   有个key起到索引作用
1.4	算法
    1.4.1	质变算法
    1.4.2	非质变算法
```

###  2.STL初识

```cpp
2.1	vector容器  vector<int>v
    
2.2	vector<int>::iterator  itBegin = v.begin(); //v.begin() 起始迭代器，指向容器中第一个数据

2.3		vector<int>::iterator  itEnd = v.end(); //v.end()；结束迭代器，指向的是容器中最后一个元素的下一个位置

2.4	利用算法遍历容器
	2.4.1	for_each(v.begin(),v.end() , myPrint )  头文件    algorithm
    
2.5	容器中存放自定义数据类型
2.6	容器中存放自定义数据类型指针
2.7	容器嵌套容器
```

### 3.string容器

```cpp
3.1	构造、赋值
3.2	[]、at 字符存取
    3.2.1	[] 和 at区别？ []访问越界 直接挂掉，at抛出out_of_range异常
    
3.3	字符串拼接   +=  append
3.4	查找    find   rfind(从右往左查找)
3.5	替换    replace
3.6	比较    compare
3.7	子串    substr  
3.8	案例 
    3.8.1	求email中的用户名
    3.8.2	string str = "www.itcast.com.cn";
    3.8.3	将 www  itcast  com  cn 单词截取到 vector容器中
        
3.9	插入 insert
3.10	删除 erase    
3.11	string和const  char* 转换
    3.11.1	string 转 const char *     .c_str();
    3.11.2	const char * 转string    利用string 有参构造
    3.11.3	const char *可以隐式转换为 string 反之不可以
        
3.12	小写转大写  toupper
3.13	大写转小写  tolower
```

### 4.vector容器

​      //PS：动态内存扩展，并不是在原空间之后续接新空间(因为无法保证原空间之后尚有可配置的空间)，而是一块更大的内存空间，然后将原数据拷贝新空间，并释放原空间。因此，对vector的任何操作，一旦引起空间的重新配置，指向原vector的所有迭代器就都失效了。

#### 4.1 vector构造函数

```cpp
	vector<T> v; //采用模板实现类实现，默认构造函数
    vector(v.begin(), v.end());//将v[begin(), end())区间中的元素拷贝给本身。
    vector(n, elem);//构造函数将n个elem拷贝给本身。
    vector(const vector &vec);//拷贝构造函数。

    //例子 使用第二个构造函数 我们可以...
    int arr[] = {2,3,4,1,9};
    vector<int> v1(arr, arr + sizeof(arr) / sizeof(int));
```

#### 4.2 vector常用赋值操作

```cpp
assign(beg, end);//将[beg, end)区间中的数据拷贝赋值给本身。
assign(n, elem);//将n个elem拷贝赋值给本身。
vector& operator=(const vector  &vec);//重载等号操作符
swap(vec);// 将vec与本身的元素互换。
```

#### 4.3 vector大小操作

​           

```cpp
size();//返回容器中元素的个数
empty();//判断容器是否为空
resize(int num);//重新指定容器的长度为num，若容器变长，则以默认值填充新位置。如果容器变短，则末尾超出容器长度的元素被删除。

resize(int num, elem);//重新指定容器的长度为num，若容器变长，则以elem值填充新位置。如果容器变短，则末尾超出容器长>度的元素被删除。

capacity();//容器的容量
reserve(int len);//容器预留len个元素长度，预留位置不初始化，元素不可访问。
```

#### 4.4 vector数据存取操作

```cpp
at(int idx); //返回索引idx所指的数据，如果idx越界，抛出out_of_range异常。
operator[];//返回索引idx所指的数据，越界时，运行直接报错
front();//返回容器中第一个数据元素
back();//返回容器中最后一个数据元素
```

#### 4.5 vector插入和删除操作//（参数为迭代器）

```cpp
insert(const_iterator pos, int count,ele);//迭代器指向位置pos插入count个元素ele.
push_back(ele); //尾部插入元素ele
pop_back();//删除最后一个元素
erase(const_iterator start, const_iterator end);//删除迭代器从start到end之间的元素
erase(const_iterator pos);//删除迭代器指向的元素
clear();//删除容器中所有元素
```

#### 4.6 巧用swap收缩内存

```cpp
vector<int>(v).swap(v);//用v初始化匿名对象，然后与v交换
```

#### 4.7 巧用 reserve预留空间

```cpp
vector<int>v;
v.reserve(100000);
```

#### 4.8 逆序遍历   

```cpp
for (vector<int>::reverse_iterator it = v.rbegin();it!=v.rend();it++) 			
    //此时的迭代器用 reverse_iterator
```

#### 4.9 判断容器迭代器是否支持随机访问

```cpp
vector<int>::iterator itbegin = v.begin();
itbegin = itbegin + 3;
```

### 5.deque 容器  

//双端数组   内部有中控器控制数据

```cpp
5.1 与vector大部分类似，可参照
5.2 deque双端插入和删除操作
    push_front(elem);//在容器头部插入一个数据
    pop_front();//删除容器头部第一个数据

    push_back(elem);//在容器尾部添加一个数据
    pop_back();//删除容器尾部最后一个数据


5.3 deque数据存取
    at(idx);//返回索引idx所指的数据，如果idx越界，抛出out_of_range。
    operator[];//返回索引idx所指的数据，如果idx越界，不抛出异常，直接出错。
    front();//返回第一个数据。
    back();//返回最后一个数据
```

### 6. stack容器（栈）

```cpp
6.1 stack数据存取操作
    push(elem);//入栈
    pop();//出栈
    top();//返回栈顶元素
6.2 stack大小操作
    empty();//判断堆栈是否为空
	size();//返回堆栈的大小
```

### 7.queue容器（队列）

​      

```cpp
7.1 queue存取、插入和删除操作
    push(elem);//往队尾添加元素
    pop();//从队头移除第一个元素
    back();//返回最后一个元素
    front();//返回第一个元素
7.2 queue赋值操作
	queue& operator=(const queue &que);//重载等号操作符
7.3 queue大小操作
    empty();//判断队列是否为空
    size();//返回队列的大小
```

### 8.list容器（双向循环链表）

```cpp
8.1 remove(elem);//删除容器中所有与elem值匹配的元素。
8.2 list反转排序
    reverse();//反转链表，比如lst包含1,3,5元素，运行此方法后，lst就包含5,3,1元素。
    sort(); //list排序 
    //排序  如果容器的迭代器支持随机访问， 那么可以使用系统提供的而全局函数（sort(L)）
    //不支持随机访问的迭代器的容器，内部会提供对应的算法接口(L.sort())
    //L.sort(myCompare);//放入回调函数或者仿函数就可以设置排序方式
    //对于自定义数据类型，必须要指定排序规则
回调函数：
    bool myComparePerson(const Person &p1, const Person &p2)
    {
        if (p1.m_age == p2.m_age)
        {
            return p1.m_height < p2.m_height;
        }
            return p1.m_age > p2.m_age;
    }
8.3 如果利用remove删除自定义数据类型，需要重载==号，解决remove源码中if判断不了的问题
```

### 9. set容器

​       

```cpp
9.1 关联式容器  key值和val值相同，且值不允许重复
9.2 插入数据时默认排好序（从小到大）
9.3 插入数据 insert，插入之后不可修改
9.4 set查找操作
    find(key);//查找键key是否存在,若存在，返回该键的元素的迭代器；若不存在，返回set.end();
count(key);//统计键key的元素个数，结果要么0，要么1，因为set不允许有重复的键值
lower_bound(keyElem);//返回第一个key>=keyElem元素的迭代器。
upper_bound(keyElem);//返回第一个key>keyElem元素的迭代器。
equal_range(keyElem);//返回容器中key与keyElem相等的上下限的两个迭代器。

9.5 set.insert的返回值是个队组 pair<iterator,bool> bool代表插入是否成功
9.6 multiset 可以插入重复的值
9.7 更改set容器的排序规则需要在构造时利用仿函数
    class myCompareInt
    {
        public:
        bool operator()(int v1,int v2)
        {
            return v1 > v2;
        }

    };
set<int,myCompareInt>s; 

9.8 对于自定义数据类型，set通常都会制定出排序规则
    class myCompareInt2
    {
        public:
        bool operator()(const Person &p1, const Person &p2)
        {
            //按照年龄升序
            return p1.m_age<p2.m_age;
        }
        set<Person,myCompareInt2>s;
    }
```

### 10.pair队组

```cpp
10.1 创建方式
pair<string, int>p("Tom",10);
cout << p.first <<"  "<< p.second << endl;

pair<string, int>p2 = make_pair("Tony", 18);
cout << p2.first << "  " << p2.second << endl;
```

### 11.map容器

```cpp
11.1 关联式容器
11.2 默认按照key从小到
11.3 插入方法
    map<int, int>m;
    //第一种插入方式
    m.insert(pair<int, int>(1, 10));
    //第二种插入方式
    m.insert(make_pair(2, 20));
    //第三种插入方式
    m.insert(map<int, int>::value_type(3, 30));
    //第四种插入方式  不推荐
    m[4] = 40;

11.4 map查找操作	
    find(key);//查找键key是否存在,若存在，返回该键的元素的迭代器；/若不存在，返回map.end();
    count(keyElem);//返回容器中key为keyElem的对组个数。对map来说，要么是0，要么是1。对multimap来说，值可能大于1。
    lower_bound(keyElem);//返回第一个key>=keyElem元素的迭代器。
    upper_bound(keyElem);//返回第一个key>keyElem元素的迭代器。
    equal_range(keyElem);//返回容器中key与keyElem相等的上下限的两个迭代器。

11.5 利用仿函数实现指定排序规则
```

### 12.各容器比较

```cpp
        vector   deque	 list	  set	multiset  	map		  multimap
内存结构  单端数组 双端数组	 双向链表   二叉树  二叉树	  二叉树       二叉树
可随机存取  是       是	  否	      否      否	 对key而言：不是	   否
搜寻速度   慢	   慢	非常慢	    快	   快	 对key而言：快    对key而言：快
安插移除 尾端	头尾两端  任何位置	-	-	-	-
```

## 十一、STL其他概念

```cpp
	1.函数对象（仿函数）
        1.1	本质是一个类的对象，因此称为函数对象，也叫仿函数
        	MyPrint myPrint;//MyPrint 为类
			myPrint(100); 
		1.2 函数对象超出了普通函数的概念，可以拥有自己的状态，即可以在类中加入其他属性；
        1.3 函数对象可以做函数参数
            void doPrint(MyPrint myPrint, int num) 
            {
                myPrint(num);
            }
	
	2.谓词
        2.1 谓词是指普通函数或重载的operator()（仿函数）返回值是bool类型的函数对象。
        2.2 一元谓词 operator接受一个参数 
                class GreaterThan20
                {
                public:
                    bool operator()(int val)
                    {
                        return val > 20;
                    }
                };
   //find_if(v.begin(), v.end(), GreaterThan20()) 返回值是迭代器
        2.3 二元谓词 operator接受两个参数 
                class myCompare
                {
                public:
                    bool operator()(int v1,int v2)
                    {
                        return v1 > v2;
                    }
                };
        	2.3.1 使用回调函数时不用加小括号，函数名即可，仿函数需要加小括号
        		sort(v.begin(), v.end(), myCompare());
		2.4 lambda表达式[](){} 本质时匿名函数，[]代表lambda表达式标志
			//[](形参列表){函数实现体}    

    3.内建函数对象
        3.1 引入头文件 #include<functional>
        3.2 取反 negate    negate<int>n;  cout << n(10) << endl;//-10
        3.3 加法 plus   plus<int>p;  cout << p(10, 10) << endl //20
        3.4 大于greater  sort(v.begin(), v.end(),greater<int>());//从大到小排序

	4.适配器
        4.1 函数对象适配器
            4.1.1 利用bind2nd(Myprint(),num) 进行绑定
        		for_each(v.begin(),v.end(),bind2nd(Myprint(),num));
            4.1.2 继承binary_function<参数1 类型 ,参数2 类型 , 返回值类型>
            4.1.3 函数对象后加const //void operator()( int val,int start)const
        4.2 取反适配器
            4.2.1、利用not1进行取反
				vector<int>::iterator ret = find_if(v.begin(), v.end(), not1 ( bind2nd(greater<int>(),5)));
            2、继承 public unary_function<int,bool>
            3、加const
            4 二元取反
        4.3 函数适配器
            4.3.1 将普通函数指针适配成函数对象  ptr_fun
			for_each(v.begin(), v.end(),  bind2nd(ptr_fun(myPrint3),100));
		4.4 成员函数适配器
            4.4.1 如果存放的是对象，实体利用 mem_fun_ref适配 且注意，需要加&
            for_each(v.begin(), v.end(), mem_fun_ref(&Person::showPerson))
            4.4.2 如果存放的是对象指针，利用 mem_fun适配
    
    5.常用遍历算法
        5.1 for_each  用于遍历，有返回值，可以绑定参数进行输出
        5.2 transform  搬运，要注意：目标容器要有容量，v2.resize(v.size())
        
    6.常用查找算法     
        6.1	find  查找
        6.2	find_if  按条件查找
        6.3	adjacent_find算法 查找相邻重复元素
        6.4	binary_search算法 二分查找法
        6.4.1	注意: 在无序序列中不可用
        6.5	count算法 统计元素出现次数
        6.6	count_if 按条件进行统计
    7	常用排序算法
        7.1	merge 合并  
        7.1.1	将两个容器合并到 目标容器中 
        7.1.2	注意： 两个容器必须是有序序列
        7.1.3	目标容器必须有容量
        7.2	sort 排序
        7.3	random_shuffle 洗牌
        7.4	reverse 反转
    8	常用的拷贝和替换算法
        8.1	copy 拷贝
        8.1.1	实现打印  copy(v.begin(),v.end() , ostream_iterator<int>(cout , “ ”));
        8.2	replace 替换
        8.3	replace_if 按条件替换
        8.4	swap 交换
    9	常用的算数生成算法
        9.1	头文件  #include <numeric>
        9.2	accumulate算法 计算容器元素累计总和
        9.3	fill算法 向容器中添加元素
    10	常用集合算法
        10.1	set_intersection算法 求两个set集合的交集
        10.2	set_union算法 求两个set集合的并集
        10.3	set_difference算法 求两个set集合的差集
        10.4	注意:两个集合必须是有序序列

```

