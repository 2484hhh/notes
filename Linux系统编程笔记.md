#  一、Linux基础命令

## 1. shell相关

​	概念：shell就是命令解析器，shell将用户输入的命令解释成内核能够识别的质量。

​	查看当前系统使用的shell：echo $SHELL;

​	当前系统支持的shell：cat/etc/shells;

## 2 .linux目录结构

是一个倒立的树状结构;
  /bin: ls cp mv 
  /sbin: ifconfig halt shutdown
  /lib: 库目录
  /root: root用户的家目录
  /dev: 字符设备和块设备.  **在linux下, 一切皆是文件.**
  /home: 用户的家目录
  /usr: 相当于windows的program files
  /etc: 系统级别的配置文件
  /mnt:
  /media
  /tmp
  /opt

## 3.ls 命令

3.1  ls -la : 列出当前目录下所有文件的相信信息, 包括隐藏文件 

3.2  ls -ltr: 列出当前目录下的文件, 按照时间逆向排序

## 4.文件的创建和删除

touch file: 若file不存在,则创建一个空文件, 否则, 则更新文件的最后修改时间.

mkdir file:
mkdir dir:
mkdir -p aa/bb/cc  创建多级文件
rmdir: 只能删除空目录
rm file: 删除文件
rm -r dir: 删除目录

## 5.cp命令

**cp 源文件  目标文件   //拷贝文件**

**若有目录的拷贝需要使用-r参数**

cp 要拷贝的文件（file1） file（不存在）

创建file，将file1中的内容拷贝到file

cp file1 file（存在）

file1覆盖file

cp file dir（存在）

拷贝file到dir目录

cp -r dir（存在） dir1（存在） 

将dir目录拷贝到dir1目录中， 包括dir目录

cp -r dir（存在） dir1（不存在） 

创建dir1，将dir中的内容拷贝到dir1中, 不包括dir目录

cp 拷贝目录也可以用-a参数, 这样可以保留被拷贝的文件的一些属性信息

  pwd ---查看当前目录

## 6.mv命令 

**mv 源文件  目标文件**

改名或者移动文件 mv file1 file2

1. 改名

   mv file（存在） file1（不存在） 

   mv dir（存在） dir1（不存在）

   mv file（存在） file2（存在）

   file文件覆盖file2文件,file改名为file2

2. 移动(第二个参数一定是目录文件)

   mv file（文件） dir（存在目录）

   将file文件移动到dir中

   mv dir（目录存在） dir1（目录存在）

   将dir移动到dir1中, dir就会作为dir1的子目录而存在

## 7.查看文件内容

cat   more   less   head   tail 
tail -f test.log: 实时显示文件内容

一个比较重要的应用：显示日志 : tail -f test.log
一个终端tail -f test.log , 另一个终端: echo “hello world” >>test.log

## 8.软硬链接

1. 软连接：相当于windows下的快捷方式
     ln -s 源文件  连接文件
     注意点: 

   ​		**创建软连接源文件一定要使用绝对路径,** 否则, 当软连接文件移动位置之后, 就会找不到指向的文件了.

   

2. 硬链接

   ​	ln 源文件  目标文件
   ​	硬链接不能建立在目录上
   ​	创建一个硬链接文件, 硬链接计数+1, 删除一个则-1;
   ​	**ls -li: 可以查看文件的inode**

   ​	**stat 文件名：可以查看i节点信息**

   作用: 
   	1 可以起到同步文件的作用
   	2 可以起到保护文件的作用

## 9.修改用户权限、用户和用户组

1. 修改文件权限：

   1.1 文字设定法：

   ​	命令：chmod [who] [+|-|=] [mode] 文件名

   操作对象【who】

   ​	u -- 用户（user）

   ​	g -- 同组用户（group）

   ​	o -- 其他用户（other）

   ​	a -- 所用用户（all）【默认】

   操作符【+-=】

   ​	+ -- 添加权限

   ​	- -- 取消权限

   ​	= -- 赋予给定权限并取消其他权限

   权限【mode】

   ​	r -- 读

   ​	w -- 写

   ​	x -- 执行

   ​	示例：chmod ug+wr file.txt  给文件file.txt的所有者和所属组添加读写权限

   1.2 数字设定法：

   ​	命令：chmod  +|-|=mode文件名

   ​	操作符【+-=】

   ​		+-- 添加权限

   ​		--- 取消权限

   ​		= -- 赋予给定权限并取消其他权限 (默认为=)

   ​	数字表示的含义

   ​		0 -- 没有权限(-)

   ​		1 -- 执行权限（x）

   ​		2 -- 写权限（w）

   ​		4 -- 读权限（r)

   ​		例：给file.txt文件设置 rw-rw-r--

   ​				chmod 664 file.txt

    	 注意点: 使用数字设定法, 一定要使用3位的8进制数: 如:066

2. 修改文件所有者和所属组

   2.1 修改文件所有者chown

   ​	用法：chown 文件所有者 文件名

   ​	sudo chown mytest file.txt

   2.2 修改文件所有者和所属组chown

   ​	用法：

   ​		chown 文件所有者:文件所属组 文件名

   ​		sudo chown mytest:mytest file.txt

   ​		sudo chown mytest.mytest file.txt

   ​	注意:普通用户需要使用管理员用户权限执行该命令

   ​	注意: 若系统没有其他用户, 可以使用sudo adduser 用户名 创建一个新用户.

3. 修改文件所属组

   chgrp命令

   ​	使用方法：chgrp 用户组 文件或目录名

   ​	示例：修改文件所属组为mytest

   sudo chgrp mytest file.txt

   ​	普通用户需要使用管理员权限执行该命令。

## 10.find查找命令

find:

​	按名称：-name:
​	按类型：-type:
​	按大小：-size
​	按深度：-maxdepth/mindepth
​	按文件日期：-ctime/atime/mtime
​	高级查找：	

​					find path -name "*.c" -exec shell命令 {} \;  

​					find path -name "*.c" -ok shell命令 {} \; 

​					find path -name ".c" | xargs shell命令 

## 11.grep查找命令

​	-n:  可以显示查找内容所在的行号
​	-i:  可以忽略大小写进行查找
​	-v: 不显示含有某字符串
​	-r: 若是目录，则可以递归搜索
find和grep结合使用:
find . -name "*.sh" | xargs grep -n "while" 

## 12.CentOS 软件安装

![20170810101833044](C:\Users\24846\Desktop\C++\20170810101833044.png)

## 13.压缩和解压缩

### tar工具

相关参数说明

z：用gzip来压缩/解压缩文件

j：用bzip2来压缩/解压缩文件

c：create, 创建新的压缩文件, 与x互斥使用

x：从压缩文件中释放文件, 与c互斥使用

v：详细报告tar处理的文件信息

f：指定压缩文件的名字

t: 查看压缩包中有哪些文件

压缩：

tar -cvf 压缩包名字.tar 原材料[要打包压缩的文件或目录]

tar -zcvf 压缩包名字.tar.gz 原材料[要打包压缩的文件或目录]

tar -jcvf 压缩包名字.tar.bz2 原材料[要打包压缩的文件或目录]

解压缩：

tar -xvf  已有的压缩包（test.tar.gz）

tar -zxvf 已有的压缩包（test.tar.gz）

tar -jxvf 已有的压缩包（test.tar.bz2）

解压到指定目录：添加参数 -C（大写）

tar -zxvf test.tar.gz -C 解压目录（./mytest）

查看压缩包中有哪些文件

tar -tvf test.tar

### zip工具

zip: 压缩：zip -r 压缩包名 要压缩的文件(含文件或目录)
  	zip -r xxx file dir  //使用该命令不需要指定压缩包后缀
  unzip:解压缩到指定目录：添加参数 –d 解压目录
  	unzip xxx.zip -d /home/itcast/test/day1

## 14.tmux

```c
tmux——实现shell终端分屏
ubuntu下sudo apt-get install tmux安装


1. 修改tmux配置文件，设置自己的喜好
tmux默认按下 ctrl+b 加以下按键执行相关指令；
可以在 ~/.tmux.conf 配置文件中修改；
语句格式大致如下（也是本人自己所用的）：

将前缀命令 ctrl+b 修改为 ctrl+a

unbind C-b
set -g prefix C-a
1
2
用ctrl+方向键切换窗口

bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

每次修改完.tmux.conf文件，运行如下命令使之生效

tmux source-file .tmux.conf .tmux.conf配置文件如图：

2. tmux使用命令总结
最常用指令

    ctrl + b 松手按 "      #上下分屏
    ctrl + b 松手按 %      #左右分屏
    alt  + 方向键          #切换tmux窗口
    ctrl + b 松手按 {  	  #与上一个窗口交换位置  ‘}’与下一个
    ctrl + PageDown/PageUp #滚屏--查看上面或下面的命令行
    ctrl + b 松手按 &      #关闭所有窗口
    exit	    		  #关闭当前窗口

所有指令 （ctrl-b前提）

    " 上下分屏 (用到shift)
    % 左右分屏 (用到shift)
    x 关闭当前分屏
    ! 将当前面板置于新窗口,即新建一个窗口,其中仅包含当前面板
    ctrl+方向键 以1个单元格为单位移动边缘以调整当前面板大小
    alt+方向键 以5个单元格为单位移动边缘以调整当前面板大小
    q 显示面板编号
    o 选择当前窗口中下一个面板
    方向键 移动光标选择对应面板
    { 向前置换当前面板
    } 向后置换当前面板
    alt+o 逆时针旋转当前窗口的面板
    ctrl+o 顺时针旋转当前窗口的面板
    z 最大化当前所在面板
    page up 向上滚动屏幕，q 退出
    page down 向下滚动屏幕，q 退出
窗口指令

    c 创建新窗口
    & 关闭当前窗口
    [0-9] 数字键切换到指定窗口
    p 切换至上一窗口
    n 切换至下一窗口
    l 前后窗口间互相切换
    w 通过窗口列表切换窗口
    , 重命名当前窗口，便于识别
    . 修改当前窗口编号，相当于重新排序
    f 在所有窗口中查找关键词，便于窗口多了切换
    
基本指令

    ? 列出所有快捷键；按q返回
    d 脱离当前会话,可暂时返回Shell界面
    s 选择并切换会话；在同时开启了多个会话时使用
    : 进入命令行模式；此时可输入支持的命令，例如 kill-server 关闭所有tmux会话
    [ 复制模式，光标移动到复制内容位置，空格键开始，方向键选择复制，回车确认，q/Esc退出
    ] 进入粘贴模式，粘贴之前复制的内容，按q/Esc退出
    ~ 列出提示信息缓存；其中包含了之前tmux返回的各种提示信息
    ctrl + z 挂起当前会话
```

#### 系统指令

| 前缀   | 指令   | 描述                                   |
| :----- | :----- | :------------------------------------- |
| Ctrl+b | ?      | 显示快捷键帮助文档                     |
| Ctrl+b | d      | 断开当前会话                           |
| Ctrl+b | D      | 选择要断开的会话                       |
| Ctrl+b | Ctrl+z | 挂起当前会话                           |
| Ctrl+b | r      | 强制重载当前会话                       |
| Ctrl+b | s      | 显示会话列表用于选择并切换             |
| Ctrl+b | :      | 进入命令行模式，此时可直接输入ls等命令 |
| Ctrl+b | [      | 进入复制模式，按q退出                  |
| Ctrl+b | ]      | 粘贴复制模式中复制的文本               |
| Ctrl+b | ~      | 列出提示信息缓存                       |

#### 窗口（window）指令

| 前缀   | 指令 | 描述                                     |
| :----- | :--- | :--------------------------------------- |
| Ctrl+b | c    | 新建窗口                                 |
| Ctrl+b | &    | 关闭当前窗口                             |
| Ctrl+b | 0~9  | 切换到指定窗口                           |
| Ctrl+b | p    | 切换到上一窗口                           |
| Ctrl+b | n    | 切换到下一窗口                           |
| Ctrl+b | w    | 打开窗口列表，用于且切换窗口             |
| Ctrl+b | ,    | 重命名当前窗口                           |
| Ctrl+b | .    | 修改当前窗口编号（适用于窗口重新排序）   |
| Ctrl+b | f    | 快速定位到窗口（输入关键字匹配窗口名称） |

#### 面板（pane）指令

| 前缀   | 指令        | 描述                                                         |
| :----- | :---------- | :----------------------------------------------------------- |
| Ctrl+b | “           | 当前面板上下一分为二，下侧新建面板                           |
| Ctrl+b | %           | 当前面板左右一分为二，右侧新建面板                           |
| Ctrl+b | x           | 关闭当前面板（关闭前需输入y or n确认）                       |
| Ctrl+b | z           | 最大化当前面板，再重复一次按键后恢复正常（v1.8版本新增）     |
| Ctrl+b | !           | 将当前面板移动到新的窗口打开（原窗口中存在两个及以上面板有效） |
| Ctrl+b | ;           | 切换到最后一次使用的面板                                     |
| Ctrl+b | q           | 显示面板编号，在编号消失前输入对应的数字可切换到相应的面板   |
| Ctrl+b | {           | 向前置换当前面板                                             |
| Ctrl+b | }           | 向后置换当前面板                                             |
| Ctrl+b | Ctrl+o      | 顺时针旋转当前窗口中的所有面板                               |
| Ctrl+b | 方向键      | 移动光标切换面板                                             |
| Ctrl+b | o           | 选择下一面板                                                 |
| Ctrl+b | 空格键      | 在自带的面板布局中循环切换                                   |
| Ctrl+b | Alt+方向键  | 以5个单元格为单位调整当前面板边缘                            |
| Ctrl+b | Ctrl+方向键 | 以1个单元格为单位调整当前面板边缘（Mac下                     |
| Ctrl+b | t           | 显示时钟                                                     |

#### 设置快捷键

```shell
tmux
tmux set -g prefix C-x
tmux unbind C-b
tmux bind C-x send-prefix
```



# 二、Vim

## Ⅰ、命令模式下的操作

### 1.保存退出

| **快捷键** | **操作** |
| ---------- | -------- |
| **ZZ**     | 保存退出 |

### 2.代码格式化

| **快捷键** | **操作**     |
| ---------- | ------------ |
| **gg=G**   | 代码的格式化 |

### 3.光标移动

| **快捷键** | **操作**                  |
| ---------- | ------------------------- |
| **h**      | 光标左移                  |
| **j**      | 光标下移                  |
| **k**      | 光标上移                  |
| **l**      | 光标右移                  |
| **w**      | 移动一个单词              |
| **gg**     | 光标移动到文件开头        |
| **G**      | 光标移动到文件末尾        |
| **0**      | 光标移到到行首            |
| **$**      | 光标移到到行尾            |
| **nG**     | 行跳转, 例12G, 跳到12行处 |

### 4.删除命令

| **快捷键**   | **操作**                                                     |
| ------------ | ------------------------------------------------------------ |
| **x**        | 删除光标后一个字符,相当于 Del                                |
| **X**        | 删除光标前一个字符,相当于  Backspace                         |
| **dw**       | 删除光标开始位置的字,包含光标所在字符                        |
| **d0**       | 删除光标前本行所有内容,不包含光标所在字符                    |
| **D**        | 删除光标后本行所有内容,包含光标所在字符                      |
| **dd**       | 删除光标所在行(本质其实是剪切)                               |
| **ndd**      | 从光标当前行向下删除指定的行数, 如15dd                       |
| **v/ctrl+v** | 使用h、j、k、l移动选择内容,  然后按d删除其中ctrl+v是列模式,  v为非列模式 |

### 5.撤销和反撤销

| **快捷键** | **操作**                             |
| ---------- | ------------------------------------ |
| **u**      | 一步一步撤销, 相当于word文档的ctrl+z |
| **ctrl+r** | 反撤销,  相当于word文档的ctrl+y      |

### 6.复制粘贴

| **快捷键**         | **操作**                                                     |
| ------------------ | ------------------------------------------------------------ |
| **yy**             | 复制当前行                                                   |
| **nyy**            | 复制n行,  如10yy                                             |
| **p**              | 在光标所在位置向下新开辟一行, 粘贴                           |
| **P**              | 在光标所在位置向上新开辟一行,  粘贴                          |
| **剪切**  **操作** | 按dd或者ndd删除, 将删除的行保存到剪贴板中, 然后按p/P就可以粘贴了 |

### 7.可视模式

| **快捷键**   | **操作**                                                     |
| ------------ | ------------------------------------------------------------ |
| **v/ctrl+v** | 使用h、j、k、l移动选择内容;  使用d删除  使用y复制   使用p粘贴到光标的后面  使用P粘贴到光标的前面 |

### 8.替换

| **快捷键** | **操作**               |
| ---------- | ---------------------- |
| **r**      | 替换当前字符           |
| **R**      | 替换当前行光标后的字符 |

### 9.查找

| **快捷键**  | **操作**                                                  |
| ----------- | --------------------------------------------------------- |
| **/**       | /xxxx, 从光标所在的位置开始搜索, 按n向下搜索, 按N向上搜索 |
| **?**       | ?xxxx, 从光标所在的位置开始搜索, 按n向上搜索, 按N向下搜索 |
| **#**       | 将光标移动到待搜索的字符串上, 然后按n向上搜索,但N向下搜索 |
| **shift+k** | 在待搜索的字符串上按shift+k或者K, 可以查看相关的帮助文档  |

## Ⅱ、插入模式

### 1.基础命令

从命令模式切换到文本输入模式只需输入如下命令:

| **快捷键**       | **操作**                                                     |
| ---------------- | ------------------------------------------------------------ |
| **i**            | 在光标前插入                                                 |
| **a**            | 在光标后插入                                                 |
| **I**            | 在光标所在行的行首插入                                       |
| **A**            | 在光标所在行的行尾插入                                       |
| **o**            | 在光标所在的行的下面新创建一行, 行首插入                     |
| **O**            | 在光标所在的行的上面新创建一行, 行首插入                     |
| **s**            | 删除光标后边的字符, 从光标当前位置插入                       |
| **S**            | 删除光标所在当前行, 从行首插入                               |
| **按列模式插入** | 先按ctrl+v进入列模式, 按hjkl移动选定某列,按I或者shift+i向前插入, 然后插入字符, 最后按两次esc. |

## Ⅲ、末行模式

### 1.保存退出

| **快捷键** | **操作**                 |
| ---------- | ------------------------ |
| q          | 退出                     |
| q!         | 强制退出，不保存修改内容 |
| w          | 保存修改内容, 不退出     |
| wq         | 保存并退出               |
| x          | 相当于wq                 |

### 2.替换操作

表格中old表示原字符串, new表示新字符串

| **快捷键**         | **操作**                         |
| ------------------ | -------------------------------- |
| :s/old/new/        | 光标所在行的第一个old替换为new   |
| :s/old/new/**g**   | 光标所在行的所有old替换为new     |
| :m,ns/old/new/g    | 将第m行至第n行间的old全替换成new |
| :%s/old/new/g      | 当前文件的所有old替换为new       |
| :1,$s/old/new/g    | 当前文件的所有old替换为new       |
| :%s/old/new/g**c** | 同上，但是每次替换需要用户确认   |

### 3.快速翻屏

| **快捷键** | **操作**                       |
| ---------- | ------------------------------ |
| ctrl + u   | 向下翻半屏(up)--光标向上移动   |
| ctrl + d   | 向上翻半屏(down)--光标向下移动 |
| ctrl + f   | 向上翻一屏(front)              |
| ctrl + b   | 向后翻一屏(back)               |

### 4.分屏操作

在打开文件后分屏：

| **快捷键**                 | **操作**                                     |
| -------------------------- | -------------------------------------------- |
| sp                         | 当前文件水平分屏                             |
| vsp                        | 当前文件垂直分屏                             |
| sp 文件名                  | 当前文件和另一个文件水平分屏                 |
| vsp 文件名                 | 当前文件和另一个文件垂直分屏                 |
| ctrl-w-w                   | 在多个窗口切换光标                           |
| wall/wqall/xall/qall/qall! | 保存/保存退出/保存退出/退出/强制退出分屏窗口 |

在打开文件之前分屏:

分屏: vim -o file1 file2 

垂直分屏: vim -O file1 file2

### Ⅳ、vim的配置文件

#### 1.用户级别配置文件

~/.vimrc，修改用户级别的配置文件只会影响当前用户,，不会影响其他的用户。

例如: 在用户的家目录下的.vimrc文件中添加

set tabstop=4 ----设置缩进4个空格

set nu    ----设置行号

set shiftwidth=4 ---设置gg=G缩进4个空格, 默认是缩进8个空格

#### 2.系统级别配置文件

​		/etc/vim/vimrc, 修改了系统级别的配置文件将影响系统下的所有用户。说明: 由于linux是多用户操作系统, 建议只在用户级别的配置文件下进行修改, 不要影响其他用户.

# 三、gcc编译器

## 1.gcc的工作流程

1、预处理: cpp预处理器, 去掉注释, 展开头文件, 宏替换
	**gcc -E test.c -o test.i**
2、编译: gcc, 将源代码文件编译成汇编语言代码
	**gcc -S test.i -o test.s**
3、汇编: as, 将汇编语言代码编译成了二进制文件(目标代码)
	**gcc -c test.s -o test.o**
4、链接: ld, 链接test.c代码中调用的库函数
	**gcc -o test test.o**

//一步直接运行 ： gcc -o 可执行文件 源文件名

或者     				   gcc 源文件名 -o 可执行文件名

## 2.gcc常用函数

**-v  查看gcc版本号, --version也可以**

**-E  生成预处理文件**

**-S 生成汇编文件**

**-c  只编译, 生成.o文件, 通常称为目标文件**

**-I(大写i)  指定头文件所在的路径**

**-L  指定库文件所在的路径**

**-l(小写L)  指定库的名字**

**-o  指定生成的目标文件的名字**

**-g  包含调试信息, 使用gdb调试需要添加-g参数**

-On n=0∼3 编译优化,n越大优化得越多

# 四、静态库和共享(动态)库

## 1.静态库

#### 1.静态库的制作

​	1.1 将.c文件编译成.o文件
​		gcc -c fun1.c fun2.c
​	1.2 使用ar命令将.o文件打包成.a文件
​		ar rcs libtest1.a fun1.o fun2.o
​	

#### 2.静态库的使用

​	2.1 main.c与head.h和libtest1.a在同一级目录的情况
​			**gcc -o main1 main.c -I./ -L./ -ltest1**
​	2.2 main.c与head.h和libtest1.a在不同一级目录的情况
​			**gcc -o main1 main.c -I./include -L./lib -ltest1**

## 2.动态库

#### 1.动态库的制作:

​	1.1 将.c文件编译成.o文件
​			**gcc -fpic -c fun1.c fun2.c**
​	1.2 使用gcc将.o文件编译成库文件
​			**gcc -shared fun1.o fun2.o -o libtest2.so**
​	

#### 2.动态库的使用:

​	**gcc -o main2 main.c -I./include -L./lib -ltest2**
​	
动态库文件在编译的时候, 连接器需要使用参数-L找到库文件所在的路径;
在执行的时候, 是加载器ldd根据动态库的路径进行加载的, 与编译的时候用的-L指定的路径无关.

最常用的解决办法:将LD_LIBRARY_PATH环境变量加到用户级别的配置文件~/.bashrc中, 然后生效(. ~/.bashrc    source ~/.bashrc   退出终端然后再登录)

**把export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:库路径, 设置到∼/.bashrc文件中**

## 3.优缺点

####  3.1静态库的优点

​    1 执行速度快, 是因为静态库已经编译到可执行文件内部了

​    2 移植方便, 不依赖域其他的库文件

####  3.2缺点:

​    1 耗费内存, 是由于每一个静态库的可执行程序都会加载一次

​    2 部署更新麻烦, 因为静态库修改以后所有的调用到这个静态库的可执行文件都需要重新编译

 

####  3.3动态库的优点

​    1 节省内存

​    2 部署升级更新方便, 只需替换动态库即可, 然后再重启服务.

####  3.4缺点 

​    1 加载速度比静态库慢

​    2 移植性差, 需要把所有用到的动态库都移植.

**由于由静态库生成的可执行文件是把静态库加载到了其内部, 所以静态库生成的可执行文件一般会比动态库大.**



# 五、makefile

## 1.概念及作用

​		makefile文件是用来管理项目工程文件, 通过执行make命令, make就会解析并执行makefile文件.

​		makefile命名: makefile或者Makefile

## 2.基本规则

规则：生成目标: 依赖 （换行） 

​	(tab) 命令

## 3.不同版本

#### 1.第一个版本

```makefile
main:main.c fun1.c fun2.c sum.c
	gcc -o main main.c fun1.c fun2.c sum.c -I./
	
```

#### 2.第二个版本

```makefile
main:main.o fun1.o fun2.o sum.o
	gcc -o main main.o fun1.o fun2.o sum.o

main.o:main.c
	gcc -o main.o -c main.c -I./

fun1.o:fun1.c
	gcc -o fun1.o -c fun1.c

fun2.o:fun2.c
	gcc -o fun2.o -c fun2.c

sum.o:sum.c
	gcc -o sum.o -c sum.c

```



	要想生成目标文件, 先要检查依赖条件是否都存在:
		若都存在, 则比较目标时间和依赖的时间, 如果依赖的时候比目标的时间新,
		则重新生成目标; 否则不重新生成
		若不存在, 则往下找有没有生成依赖的规则, 有则生成, 如果没有则报错.
#### 3.第三个版本

```makefile
target=main
object=main.o fun1.o fun2.o sum.o
CC=gcc
CPPFLAGS=-I./

$(target):$(object)
	$(CC) -o $@ $^

%.o:%.c
	$(CC) -o $@ -c $< $(CPPFLAGS) 

#main.o:main.c
#	gcc -o main.o -c main.c -I./
#
#fun1.o:fun1.c
#	gcc -o fun1.o -c fun1.c
#
#fun2.o:fun2.c
#	gcc -o fun2.o -c fun2.c
#
#sum.o:sum.c
#	gcc -o sum.o -c sum.c

```



```c
变量: 
	自定义变量: var = hello, 使用：$(var)
	自带变量: CC CPPFLAGS CFLAGS LDFLAGS
	自动变量: $@ $< $^
	模式规则:
		%.o:%.c------> 前后的%必须是相同 
自带变量：		
    CC = gcc #arm-linux-gcc
    CPPFLAGS : C预处理的选项 -I
    CFLAGS:   C编译器的选项 -Wall -g -c
    LDFLAGS :  链接器选项 -L  -l
自动变量：（只能用在命令当中）
	$@: 表示规则中的目标
	$<: 表示规则中的第一个条件
	$^: 表示规则中的所有条件, 组成一个列表, 以空格隔开, 如果这个列表中有重复的项则消除重复项。

```

#### 4.第四个版本

```makefile
src=$(wildcard ./*.c)
object=$(patsubst %.c, %.o, $(src))
target=main
CC=gcc
CPPFLAGS=-I./

$(target):$(object)
	$(CC) -o $@ $^

%.o:%.c
	$(CC) -o $@ -c $< $(CPPFLAGS) 

```



```c
makefile函数：
1.	wildcard – 查找指定目录下的指定类型的文件
src=$(wildcard *.c)  //找到当前目录下所有后缀为.c的文件,赋值给src
2.	patsubst – 匹配替换
obj=$(patsubst %.c,%.o, $(src)) //把src变量里所有后缀为.c的文件替换成.o

在makefile中所有的函数都是有返回值的。
当前目录下有main.c fun1.c fun2.c sum.c
src=$(wildcard *.c) 等价于src=main.c fun1.c fun2.c sum.c
obj=$(patsubst %.c,%.o, $(src))等价于obj=main.o fun1.o fun2.o sum.o
```

缺点: 每次重新编译都需要手工清理中间.o文件和最终目标文件

#### 5.第五个版本

```makefile
src=$(wildcard ./*.c)
object=$(patsubst %.c, %.o, $(src))
target=main
CC=gcc
CPPFLAGS=-I./

$(target):$(object)
	$(CC) -o $@ $^

%.o:%.c
	$(CC) -o $@ -c $< $(CPPFLAGS) 

.PHONY:clean
clean:
	-rm -f $(target) $(object)


```



```makefile
makefile的清理操作：
1. 用途: 清除编译生成的中间.o文件和最终目标文件
make clean 如果当前目录下有同名clean文件,则不执行clean对应的命令, 解决方案：
2. 伪目标声明: 
	.PHONY:clean  声明目标为伪目标之后, makefile将不会检查该目标是否存在或者该目标是否需要更新
        
3. clean命令中的特殊符号：
	“-”此条命令出错,make也会继续执行后续的命令。如:“-rm main.o”
    rm -f: 强制执行, 比如若要删除的文件不存在使用-f不会报错
“@”不显示命令本身, 只显示结果。如:“@echo clean done”
    
4.其它：
	– make 默认执行第一个出现的目标, 可通过make dest指定要执行的目标
	– make -f : -f执行一个makefile文件名称, 使用make执行指定的makefile: make -f mainmak 目标
    
```

# 六、gdb调试

## 1.概念

​		gdb是在程序运行的结果与预期不符合的时候, 可以使用gdb进行调试,
​		**特别注意的是: 使用gdb调试需要在编译的时候加-g参数.**

​		一般来说GDB主要调试的是C/C++的程序。要调试C/C++的程序, 首先在编译时, 我们必须要把调试信息加到可执行文件中。使用编译器（cc/gcc/g++）的 -g 参数可以做到这一点。如：

```c
gcc -g -c hello.c
gcc -o hello hello.o
```
## 2.启动gdb

```c
启动gdb：gdb program
	program 也就是你的执行文件, 一般在当前目录下。
设置运行参数
	set args 可指定运行时参数。（如：set args 10 20 30 40 50 ）
	show args 命令可以查看设置好的运行参数。
启动程序
	run：程序开始执行, 如果有断点, 停在第一个断点处
	start：程序向下执行一行。(在第一条语句处停止)

```

## 3.显示源代码

​		**可以用list命令来打印程序的源代码, 默认打印10行**

```c
	list linenum：打印第linenum行的上下文内容.
	list function：显示函数名为function的函数的源程序。
	list： 显示当前行后面的源程序。
	list -：显示当前文件开始处的源程序。
	list file:linenum: 显示file文件下第n行
	list file:function: 显示file文件的函数名为function的函数的源程序
 	一般是打印当前行的上5行和下5行, 如果显示函数是是上2行下8行, 默认是10行, 当然, 你也可以定制显示的范围, 使用下面命令可以设置一次显示源程序的行数。
	set listsize count：设置一次显示源代码的行数。         
	show listsize：   查看当前listsize的设置。

```

## 4.设置断点

```c
断点操作:(break 简写 b)
	b linenum //某行设置断点
	b func //设置函数内断点
	b file:linenum //设置其他文件内断点
	b file:func
info break //查看断点

disable m n | m-n	//使断点失效
enable  m n | m-n	//使断点生效

delete m n | m-n 	//断点删除
        
一般来说, 为断点设置一个条件, 我们使用if关键词, 后面跟其断点条件。设置一个条件断点：
     b test:8 if intValue == 5//条件断点

```
## 5.调试代码

```c
	run 运行程序, 可简写为r
	next 单步跟踪, 函数调用当作一条简单语句执行, 可简写为n
	step 单步跟踪, 函数调进入被调用函数体内, 可简写为s
	finish 退出进入的函数, 如果出不去, 看一下函数体中的循环中是否有断点，如果有删掉，或者设置无效
	until 在一个循环体内单步跟踪时, 这个命令可以运行程序直到退出循环体,可简写为u,
如果出不去, 看一下函数体中的循环中是否有断点，如果有删掉，或者设置无效
	continue 继续运行程序, 可简写为c(若有断点则跳到下一个断点处)
    
print 打印变量、字符串、表达式等的值, 可简写为p
p count -----打印count的值

```

## 6.自动显示 display

1.**自动显示变量的值**
你可以设置一些自动显示的变量, 当程序停住时, 或是在你单步跟踪时, 这些变量会自动显示。相关的GDB命令是display。

```c
	display 变量名
	info display -- 查看display设置的自动显示的信息。
	undisplay num（info display时显示的编号）
	delete display dnums… -- 删除自动显示, dnums意为所设置好了的自动显式的编号。如果要同时删除几个, 编号可以用空格分隔, 如果要删除一个范围内的编号, 可以用减号表示（如：2-5）
	删除某个自动显示: undisplay num 或者delete display num
	删除多个: delete display num1 num2
	删除一个范围: delete display m-n
	disable display dnums…
	使一个自动显示无效: disable display num
	使多个自动显示无效: delete display num1 num2
	使一个范围的自动显示无效: delete display m-n
	enable display dnums…
	使一个自动显示有效: enable display num
	使多个自动显示有效: enable display num1 num2
	使一个范围的自动显示有效: enable display m-n
	disable和enalbe不删除自动显示的设置, 而只是让其失效和恢复。
```

**查看修改变量的值**

```c
	ptype width --查看变量width的类型
          type = double
	p width --打印变量width 的值
          $4 = 13
你可以使用set var命令来告诉GDB, width不是你GDB的参数, 而是程序的变量名, 如：
          set var width=47  // 将变量var值设置为47
在你改变程序变量取值时, 最好都使用set var格式的GDB命令。
```

## 7.查看源码和汇编窗口和寄存器

layout split 查看源码和汇编窗口

layout source 查看源码窗口

layout asm 查看汇编窗口

layout reg 查看寄存器

ctrl-x a 退出layout

tui enable 打开窗口

tui disable 关闭窗口 

i frame 查看当前栈的内容

i args  查看函有关函数参数信息  

backtrace  (bt)

watch 变量  （当变量变化时会显现）

qemu 进入监控台  ctrl-a  c

​		info mem 查看当前页表

## 8.gdb手册

输入  apropos+想搜的命令

# 七、文件IO

```c
	使用fopen函数打开一个文件, 返回一个FILE* fp, 这个指针指向的结构体有三个重要的成员.
	文件描述符: 通过文件描述可以找到文件的inode, 通过inode可以找到对应的数据块
	文件指针: 读和写共享一个文件指针, 读或者写都会引起文件指针的变化
	文件缓冲区: 读或者写会先通过文件缓冲区, 主要目的是为了减少对磁盘的读写次数, 提高读写磁盘的效率.
    
虚拟地址空间:
	进程的虚拟地址空间分为用户区和内核区, 其中内核区是受保护的, 用户是不能够对其进行读写操作的;
	内核区中很重要的一个就是进程管理, 进程管理中有一个区域就是PCB(本质是一个结构体);
	PCB中有文件描述符表, 文件描述符表中存放着打开的文件描述符, 涉及到文件的IO操作都会用到这个文件描述符.
```

**man  1搜系统命令 2搜系统函数 3搜C语言库函数**

## 1.lseek函数

```c
#include <sys/types.h>c++
#include <unistd.h>
off_t lseek(int fd, off_t offset, int whence);
lseek函数常用操作
    文件指针移动到头部
    	lseek(fd, 0, SEEK_SET);
    获取文件指针当前位置
        int len = lseek(fd, 0, SEEK_CUR);
    获取文件长度
        int len = lseek(fd, 0, SEEK_END);
    lseek实现文件拓展
		off_t currpos;
        // 从文件尾部开始向后拓展1000个字节
        currpos = lseek(fd, 1000, SEEK_END); 
        // 额外执行一次写操作，否则文件无法完成拓展
        write(fd, “a”, 1);	// 数据随便写

```

## 2.perror和errno

```c
	errno是一个全局变量, 当系统调用后若出错会将errno进行设置, perror可以将errno对应的描述信息打印出来.
如:perror("open"); 如果报错的话打印: open:(空格)错误信息
	需要引入头文件#include<errno.h>
```

## 3.阻塞和非阻塞

```c
1. read函数读普通文件是非阻塞的；
2. read函数读设备文件是阻塞的；
    结论：阻塞和非阻塞不是read函数的属性，而是文件本身的属性！
3. socket pipe 这两种文件都是阻塞的
```

## 4.文件和目录

### 4.1 stat/lstat

```c
1.stat/lstat	
    函数描述: 获取文件属性
    函数原型: int stat(const char *pathname, struct stat *buf);
             int lstat(const char *pathname, struct stat *buf);
    函数返回值： 
        成功返回 0
        失败返回 -1
        
2.struct stat {
        dev_t          st_dev;        //文件的设备编号
        ino_t          st_ino;        //节点
        mode_t         st_mode; //文件的类型和存取的权限
        nlink_t        st_nlink;     //连到该文件的硬连接数目，刚建立的文件值为1
        uid_t          st_uid;       //用户ID
        gid_t          st_gid;       //组ID
        dev_t          st_rdev;      //(设备类型)若此文件为设备文件，则为其设备编号
        off_t          st_size;  //文件字节数(文件大小)
        blksize_t      st_blksize;   //块大小(文件系统的I/O 缓冲区大小)
        blkcnt_t       st_blocks;    //块数
        time_t         st_atime;     //最后一次访问时间
        time_t         st_mtime;     //最后一次修改时间
        time_t         st_ctime;     //最后一次改变时间(指属性)
	};

3.- st_mode -- 16位整数
		○ 0-2 bit -- 其他人权限
			S_IROTH      00004  读权限
			S_IWOTH      00002  写权限
			S_IXOTH      00001  执行权限
			S_IRWXO      00007  掩码, 过滤 st_mode中除其他人权限以外的信息
    
		○ 3-5 bit -- 所属组权限
			S_IRGRP     00040  读权限
			S_IWGRP     00020  写权限
		    S_IXGRP     00010   执行权限
			S_IRWXG     00070  掩码, 过滤 st_mode中除所属组权限以外的信息
    
		○ 6-8 bit -- 文件所有者权限
			S_IRUSR    00400    读权限
			S_IWUSR    00200    写权限
			S_IXUSR    00100     执行权限
			S_IRWXU    00700    掩码, 过滤 st_mode中除文件所有者权限以外的信息
            if (st_mode & S_IRUSR)   -----为真表明可读
            if (st_mode & S_IWUSR)  ------为真表明可写
            if (st_mode & S_IXUSR) ------为真表明可执行

		○ 12-15 bit -- 文件类型
			S_IFSOCK         0140000 套接字
			S_IFLNK          0120000 符号链接（软链接）
		    S_IFREG          0100000 普通文件
			S_IFBLK          0060000 块设备
			S_IFDIR          0040000 目录
		    S_IFCHR          0020000 字符设备
			S_IFIFO          0010000 管道
			S_IFMT  		 0170000 掩码,过滤 st_mode中除文件类型以外的信息
			if ((st_mode & S_IFMT)==S_IFREG) ----为真普通文件
            if(S_ISREG(st_mode))   ------为真表示普通文件
            if(S_ISDIR(st.st_mode))  ------为真表示目录文件
//掩码计算方法： 将读、写、执行权限进行与操作，如：100（4），010（2），001（1）三个进行与操作，得到111（7）即是掩码
```

```c
使用st_mode成员判断文件类型:
		    if((sb.st_mode & S_IFMT) ==S_IFLNK)
		    {   
		        printf("连接文件\n");
		    }   
		    

		    if (S_ISREG(sb.st_mode)) 
		    {   
		        printf("普通文件\n");
		    }
 	判断文件权限:
 		    if(sb.st_mode & S_IROTH)
		    {
		        printf("---R----");
		    }
```

```c
stat函数和lstat函数的区别
	对于普通文件, 这两个函数没有区别, 是一样的.
	对于连接文件,调用lstat函数获取的是链接文件本身的属性信息;
  		而stat函数获取的是链接文件指向的文件的属性信息.

```

## 5.目录操作相关函数

### 5.1 opendir函数

```c
opendir函数
    函数描述:打开一个目录 
    函数原型: DIR *opendir(const char *name);
    函数返回值: 指向目录的指针
    函数参数: 要遍历的目录(相对路径或者绝对路径)

```

### 5.2 readdir函数

```c
readdir函数
    函数描述: 读取目录内容--目录项
    函数原型: struct dirent *readdir(DIR *dirp);
    函数返回值: 读取的目录项指针
    函数参数: opendir函数的返回值

```

### 5.3 closedir函数

```c
closedir函数
    函数描述: 关闭目录
    函数原型: int closedir(DIR *dirp);
    函数返回值: 成功返回0, 失败返回-1
    函数参数: opendir函数的返回值

```

### 5.4 读取目录内容的一般步骤

```c
1. DIR *pDir = opendir(“dir”);   //打开目录
2. while((p=readdir(pDir))!=NULL){}  //循环读取文件
3. closedir(pDir);  //关闭目录
```



## 6.dup函数

```c
函数描述: 复制文件描述符
函数原型: int dup(int oldfd);
函数参数: oldfd -要复制的文件描述符
函数返回值:
    成功: 返回最小且没被占用的文件描述符
    失败: 返回-1, 设置errno值
// 复制之后，文件拥有两个文件描述符，关闭文件时需要关闭两个才行
```

## 7.dup2函数

```c
函数描述: 复制文件描述符
函数原型: int dup2(int oldfd, int newfd);
函数参数: 
    oldfd-原来的文件描述符
    newfd-复制成的新的文件描述符
函数返回值:
    成功: 将oldfd复制给newfd, 两个文件描述符指向同一个文件
    失败: 返回-1, 设置errno值
    假设newfd已经指向了一个文件，首先close原来打开的文件，然后newfd指向oldfd指向的文件.
	若newfd没有被占用，newfd指向oldfd指向的文件.

```

### 7.1 dup2函数实现文件重定向操作

```c
//使用dup2函数实现标准输出重定向操作
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>

int main(int argc, char *argv[])
{
	//打开文件
	int fd = open(argv[1], O_RDWR | O_CREAT, 0777);
	if(fd<0)
	{
		perror("open error");
		return -1;
	}

 	//调用dup2函数实现文件重定向操作
	dup2(fd, STDOUT_FILENO);
		
	printf("ni hao hello world");

	close(fd);
	close(STDOUT_FILENO);

	return 0;
}


```

![dup2文件重定向 ](C:\Users\24846\Desktop\C++\dup2文件重定向 .png)



## 8.fcntl函数

```c
fcntl函数
函数描述: 改变已经打开的文件的属性
函数原型: int fcntl(int fd, int cmd, ... /* arg */ );
    若cmd为F_DUPFD, 复制文件描述符, 与dup相同
    若cmd为F_GETFL, 获取文件描述符的flag属性值
    若cmd为 F_SETFL, 设置文件描述符的flag属性
函数返回值:返回值取决于cmd
    成功
        若cmd为F_DUPFD, 返回一个新的文件描述符
        若cmd为F_GETFL, 返回文件描述符的flags值
        若cmd为 F_SETFL, 返回0
    失败返回-1, 并设置errno值.

fcntl函数常用的操作:
    1 复制一个新的文件描述符:
    	int newfd = fcntl(fd, F_DUPFD, 0);
    2 获取文件的属性标志
    	int flag = fcntl(fd, F_GETFL, 0)
    3 设置文件状态标志
    	flag = flag | O_APPEND;
    	fcntl(fd, F_SETFL, flag)
    4 常用的属性标志
   	 	O_APPEND-----设置文件打开为末尾添加
    	O_NONBLOCK-----设置打开的文件描述符为非阻塞

```

```c
//修改文件描述符的flag属性
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>

int main(int argc, char *argv[])
{
	//打开文件
	int fd = open(argv[1], O_RDWR);
	if(fd<0)
	{
		perror("open error");
		return -1;
	}

	//获得和设置fd的flags属性
	int flags = fcntl(fd, F_GETFL, 0);
	flags = flags | O_APPEND;
	fcntl(fd, F_SETFL, flags);

	//写文件
	write(fd, "hello world", strlen("hello world"));

	//关闭文件
	close(fd);

	return 0;
}

```



# 八、进程控制

## 1.进程相关概念

### 1.1 程序和进程

​	程序: 是编译好的二进制文件, 存放在磁盘上, 占用的是磁盘空间, 是一个静态的概念.
​	进程: 一个启动的程序, 需要占用系统资源: 如 内存, cpu 终端 等
​	剧本--->程序
​	进程--->唱戏(舞台, 灯光, 道具, 人等资源)
​	同一个程序可以在多个终端执行, 类似与同一台戏可以在多个舞台演出.
​	每启动一个程序都会有一个进程PID, 即使是相同的程序多次启动也会有个不同的PID.

### 1.2 并行和并发的概念

​	并发: 在一个时间段内, 一个CPU上, 有多个程序在执行.
​	并行: 在一个时间片内, 有多个程序在执行(前提是有多个cpu)
​	cpu会将一个大的时间段分成多个小的时间片, 让进程轮流使用CPU的时间片.

### 1.3 PCB-进程控制块

```c
	进程id。系统中每个进程有唯一的id，在C语言中用pid_t类型表示，其实就是一个非负整数。
	进程的状态，有就绪、运行、挂起、停止等状态。
	进程切换时需要保存和恢复的一些CPU寄存器。
	描述虚拟地址空间的信息。
	描述控制终端的信息。
	当前工作目录：pwd
	umask掩码。
	文件描述符表，包含很多指向file结构体的指针。
	和信号相关的信息。
	用户id和组id。
	会话（Session）和进程组。
	进程可以使用的资源上限（Resource Limit）：ulimit -a

```

### 1.4 进程状态

​		进程基本的状态有5种。分别为**初始态，就绪态，**[**运行态**](http://baike.baidu.com/subview/1730379/1730379.htm)**，挂起态与终止态**。其中初始态为进程准备阶段，常与就绪态结合来看。

![image-20210616201230616](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210616201230616.png)

## 2.创建进程

### 2.1 fork函数

```c
函数作用：创建子进程
原型: pid_t fork(void);
函数参数：无
返回值：调用成功:父进程返回子进程的PID，子进程返回0；
        调用失败:返回-1，设置errno值。

```

![image-20210617192309059](E:\typroa_pic\image-20210617192309059.png)

```c
fork函数总结：
►fork函数的返回值？
     父进程返回子进程的PID，是一个大于0数;
     子进程返回0；
特别需要注意的是：不是fork函数在一个进程中返回2个值，而是在父子进程各自返回一个值。
►子进程创建成功后，代码的执行位置？
  	父进程执行到什么位置，子进程就从哪里执行
►如何区分父子进程
	通过fork函数的返回值
►父子进程的执行顺序
  不一定，哪个进程先抢到CPU，哪个进程就先执行

```

### 2.1循环创建n个子进程

```c
for(i=0; i<3; i++)
	{
		//创建子进程
		pid_t pid = fork();
		if(pid<0) //fork失败的情况
		{
			perror("fork error");
			return -1;
		}
		else if(pid>0)//父进程
		{
			printf("father: pid==[%d], fpid==[%d]\n", getpid(),getppid());
			//sleep(1);
		}
		else if(pid==0) //子进程
		{
			printf("child: pid==[%d], fpid==[%d]\n", getpid(), getppid());
			break;//阻止子进程再次创建子进程
		}
	}
```

![循环创建子进程](C:\Users\24846\Desktop\C++\循环创建子进程.jpg)

### 2.3父子进程对共享全局变量

**写时复制，读时共享**

​	父子进程不能共享全局变量；但是如果父子进程只是对全局变量做读操作，父子进程在内存中中有一份，属于共享，但是，如果父子进程中的任何一个对该变量进行修改，会在内存中拷贝一个副本，在副本上进行修改，并映射回虚拟内存。

![父子进程是否共享全局变量](C:\Users\24846\Desktop\C++\父子进程是否共享全局变量.jpg)

### 2.4 ps命令 和kill 命令

```c
	ps aux | grep "xxx"
	ps ajx | grep "xxx"
    ps  ef | grep "xxx"
	-a：（all）当前系统所有用户的进程
	-u：查看进程所有者及其他一些信息
	-x：显示没有控制终端的进程 -- 不能与用户进行交互的进程【输入、输出】
	-j: 列出与作业控制相关的信息
	kill -l 查看系统有哪些信号
	kill -9 pid 杀死某个线程

```

## 3.exec函数族

```c
如果想在一个进程内部执行系统命令或者是应用程序, 优先应该想到如下方式:
	先fork(), 然后在子进程里面执行execl拉起可执行程序或者命令.
    pid = fork();
    if(pid==0)
    {
        execl(...);
    }

```

exec族函数的实现原理图：

![execl函数原理](C:\Users\24846\Desktop\C++\execl函数原理.png)

### 3.1execl函数

```c
函数原型: int execl(const char *path, const char *arg, ... /* (char  *) NULL */);
参数介绍：
	path: 要执行的程序的绝对路径
	变参arg: 要执行的程序的需要的参数
	arg:占位，通常写应用程序的名字
	arg后面的: 命令的参数
	参数写完之后: NULL
返回值：若是成功，则不返回，不会再执行exec函数后面的代码；若是失败，会执行execl后面的代码，可以用perror打印错误原因。execl函数一般执行自己写的程序。
```

### 3.2 execlp函数

```c
函数原型: int execlp(const char *file, const char *arg, .../* (char  *) NULL */);
参数介绍：
	file: 执行命令的名字, 根据PATH环境变量来搜索该命令
	arg:占位
	arg后面的: 命令的参数
	参数写完之后: NULL
返回值：若是成功，则不返回，不会再执行exec函数后面的代码；若是失败，会执行exec后面的代码，可以用perror打印错误原因。
execlp函数一般是执行系统自带的程序或者是命令.
```

### 3.3 总结

​		exec函数是用一个新程序替换了当前进程的代码段、数据段、堆和栈；原有的进程空间没有发生变化，**并没有创建新的进程，进程PID没有发生变化。**

​		注意：**当execl和execlp函数执行成功后，不返回，并且不会执行execl后面的代码逻辑，原因是调用execl函数成功以后，exec函数指定的代码段已经将原有的代码段替换了。**

```c
//fork函数测试
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>

int main()
{
	//创建子进程
	pid_t pid = fork();
	if(pid<0) //fork失败的情况
	{
		perror("fork error");
		return -1;
	}
	else if(pid>0)//父进程
	{
		printf("father: [%d], pid==[%d], fpid==[%d]\n", pid, getpid(),getppid());
		//sleep(1);
	}
	else if(pid==0) //子进程
	{
		printf("child: pid==[%d], fpid==[%d]\n", getpid(), getppid());
		//execl("/bin/ls", "ls", "-l", NULL);
		//execl("./test", "test", "hello", "world", "ni", "hao", NULL);
		//execlp("ls", "ls", "-l", NULL);
		execlp("./test", "TESTING", "hello", "world", "ni", "hao", NULL);
		//execlp("./iitest", "test", "hello", "world", "ni", "hao", NULL);
		perror("execl error");
	}
	
	return 0;
}

```



## 4.进程回收

### 4.1 孤儿进程

孤儿进程: 父进程先退出，子进程就变成了孤儿进程，此时被init进程领养，当孤儿进程退出之后, 就会被init进程回收。



### 4.2 僵尸进程

**僵尸进程: 子进程先退出，父进程没有完成对子进程的回收，此时子进程就变成了僵尸进程。**

**如何解决僵尸进程:
	不能使用kill -9杀死僵尸进程,原因是僵尸进程是一个死掉的进程,无法进行通信;
	应该使用杀死僵尸进程父进程的方法来解决僵尸进程;
	原因是: 杀死其父进程可以让init进程领养僵尸进程,最后由init进程回收僵尸进程.**

### 4.3进程回收函数

#### 4.3.1 wait函数

```c
wait函数
函数原型：
	pid_t wait(int *status);
函数作用：
	阻塞并等待子进程退出 
	回收子进程残留资源
    获取子进程结束状态--- 退出原因
返回值：
	成功：清理掉的子进程ID；
	失败：-1 (没有子进程)
	status参数：子进程的退出状态 -- 传出参数
	WIFEXITED(status)：为非0        → 进程正常结束
	WEXITSTATUS(status)：获取进程退出状态 
	WIFSIGNALED(status)：为非0 → 进程异常终止
	WTERMSIG(status)：取得进程终止的信号编号。

```

```c
status: 子进程的退出状态
			if(WIFEXITED(status))
			{
				WEXITSTATUS(status)
			}
			else if(WIFSIGNALED(status))
			{
				WTERMSIG(status)
			}
```

#### 4.3.2 waitpid函数

```c
waitpid函数
函数原型：
pid_t waitpid(pid_t pid, int *status, in options);
函数作用同wait函数
函数参数：
    pid：
    pid = -1 //等待任一子进程。与wait等效。
    pid > 0 //等待其进程ID与pid相等的子进程。
    pid = 0 //等待进程组ID与目前进程相同的任何子进程，也就是说任何和调用
    waitpid()//函数的进程在同一个进程组的进程。
    pid < -1 //等待其组ID等于pid的绝对值的任一子进程。(适用于子进程在其他组的情况)
    status: 子进程的退出状态，用法同wait函数。
    options：设置为WNOHANG，函数非阻塞，设置为0，函数阻塞。
函数返回值
    >0：返回回收掉的子进程ID；
    -1：无子进程
    =0：参3为WNOHANG，且子进程正在运行
```

注意：调用一次waitpid或者wait函数只能回收一个子进程。

```c
//调用fork函数创建子进程, 并完成对子进程的回收
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/wait.h>

int main()
{
	int i = 0;
	int n = 3;

	for(i=0; i<n; i++)	
	{
		//fork子进程
		pid_t pid = fork();
		if(pid<0) //fork失败的情况
		{
			perror("fork error");
			return -1;
		}
		else if(pid>0) //父进程
		{
			printf("father: fpid==[%d], cpid==[%d]\n", getpid(), pid);
			sleep(1);
		}
		else if(pid==0) //子进程
		{
			printf("child: fpid==[%d], cpid==[%d]\n", getppid(), getpid());
			break;
		}
	}

	//父进程
	if(i==3)
	{
		printf("[%d]:father: fpid==[%d]\n", i, getpid());
		pid_t wpid;
		int status;

		while(1)
		{
			wpid = waitpid(-1, &status, WNOHANG);
			if(wpid==0) // 没有子进程退出
			{
				continue;
			}			
			else if(wpid==-1) //已经没有子进程了
			{
				printf("no child is living, wpid==[%d]\n", wpid);
				exit(0);
			}
			else if(wpid>0) //有子进程退出
			{
				if(WIFEXITED(status))
				{
					printf("normal exit, status==[%d]\n", WEXITSTATUS(status));
				}
				else if(WIFSIGNALED(status))
				{
					printf("killed by signo==[%d]\n", WTERMSIG(status));
				}
			}
		}
	}

	//第1个子进程
	if(i==0)
	{
		printf("[%d]:child: cpid==[%d]\n", i, getpid());
		execlp("ls", "ls", "-l", NULL);
		perror("execl error");
		exit(-1);
	}

	//第2个子进程
	if(i==1)
	{
		printf("[%d]:child: cpid==[%d]\n", i, getpid());
		execl("/home/itcast/test/course/day6/0527/zuoye/hello", "hello", "1111", "2222", NULL);
		perror("execl error");
		return -1;
	}

	//第3个子进程
	if(i==2)
	{
		printf("[%d]:child: cpid==[%d]\n", i, getpid());
		execl("/home/itcast/test/course/day6/0527/zuoye/test", "test", NULL);
		perror("execl error");
		return -1;
	}

	return 0;
}

```



# 九、IPC：进程间通信

## 1. 基本概念

​		两个进程要想完成数据交换，必须通过内核；一个进程将数据写到内核，然后另一个进程从内核读走数据。

## 2.管道-pipe

### 2.1 概念

管道是一种最基本的IPC机制，也称**匿名管道**，应用于有血缘关系的进程之间，完成数据传递。调用pipe函数即可创建一个管道。

![image-20210624100416280](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210624100416280.png)

```c
有如下特质：
	1.管道的本质是一块内核缓冲区 
	2.由两个文件描述符引用，一个表示读端，一个表示写端。
	3.规定数据从管道的写端流入管道，从读端流出。
	4.当两个进程都终结的时候，管道也自动消失。
	5.管道的读端和写端默认都是阻塞的。
```

### 2.2 原理

```c
1. 管道的实质是内核缓冲区，内部使用环形队列实现。
2. 默认缓冲区大小为4K，可以使用ulimit -a命令获取大小。
3. 实际操作过程中缓冲区会根据数据压力做适当调整。
```

### 2.3 管道的局限性

```c
1. 数据一旦被读走，便不在管道中存在，不可反复读取。
2. 数据只能在一个方向上流动，若要实现双向流动，必须使用两个管道
3. 只能在有血缘关系的进程间使用管道。
```

### 2.4 创建管道 -pipe函数

```c
函数作用: 创建一个管道
函数原型: int pipe(int fd[2]);
函数参数:
	若函数调用成功，fd[0]存放管道的读端，fd[1]存放管道的写端.
返回值:
	成功返回 0；
	失败返回 -1，并设置errno值。
	函数调用成功返回读端和写端的文件描述符，其中fd[0]是读端， fd[1]是写端，向管道读写数据是通过使用这两个文件描述符进行的，读写管道的实质是操作内核缓冲区。
	管道创建成功以后，创建该管道的进程（父进程）同时掌握着管道的读端和写端。
```

### 2.5 父子进程使用管道通信

**第一步：父进程创建管道**

**第二步：父进程fork出子进程**

**第三步：父进程关闭fd[0]，子进程关闭fd[1]**

```c
创建步骤总结：
	父进程调用pipe函数创建管道，得到两个文件描述符fd[0]和fd[1]，分别指向管道的读端和写端。
	父进程调用fork创建子进程，那么子进程也有两个文件描述符指向同一管。
	父进程关闭管道读端，子进程关闭管道写端。父进程可以向管道中写入数据，子进程将管道中的数据读出，这样就实现了父子进程间通信。
```

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/wait.h>

int main()
{
    //创建管道
    //int pipe(int pipefd[2]);
    int fd[2];
    int ret = pipe(fd);
    if(ret<0)
    {
        perror("pipe error");
        return -1;
    }
    //创建子进程
    pid_t pid = fork();
    if(pid<0)
    {
        perror("fork error");
        return -1;
    }
    else if(pid>0)
    {
        //关闭读端
        close(fd[0]);
        sleep(5);
        write(fd[1], "hello world", strlen("hello world"));

        wait(NULL);
    }
    else
    {
        //关闭写端
        close(fd[1]);

        char buf[64];
        memset(buf, 0x00, sizeof(buf));
        int n = read(fd[0], buf, sizeof(buf));
        printf("read over, n==[%d], buf==[%s]\n", n, buf);

    }
    return 0;
}

```

### 2.6 父子进程执行ps -aux | grep bash 思路分析

![ps -aux grep bash](C:\Users\24846\Desktop\C++\ps -aux grep bash.png)

```c
实现思路:
父进程:
1.创建管道--pipe
2.fork子进程
3.关闭读端:close(fd[0]);
4.将标准输出重定向到管道的写端dup2(fd[1], STDOUT_FILENO);
5.执行execlp函数执行ps aux命令
	execlp("ps","ps","axu",NULL);
子进程:
1.关闭写端:close(fd[1]);
2.将标准输入重定向到管道的读端dup2(fd[0],STDIN_FILENO)
3.执行execlp函数执行grep bash
    execlp("grep". "grep", "bash",NULL);

```

```c
//使用pipe完成ps aux | grep bash操作
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/wait.h>

int main()
{
    //创建管道
    //int pipe(int pipefd[2]);
    int fd[2];
    int ret = pipe(fd);
    if(ret<0)
    {
        perror("pipe error");
        return -1;
    }
    //创建子进程
    pid_t pid = fork();
    if(pid<0)
    {
        perror("fork error");
        return -1;
    }
    else if(pid>0)
    {
        //关闭读端
        close(fd[0]);
        //将标准输出重定向到管道的写端
        dup2(fd[1], STDOUT_FILENO);

        execlp("ps", "ps", "aux", NULL);

        perror("execlp error");
    }
    else
    {
        //关闭写端
        close(fd[1]);
        //将标准输入重定向到管道的读端
        dup2(fd[0], STDIN_FILENO);
        
        execlp("grep", "grep", "--color=auto", "bash", NULL);
        perror("execlp error");
    }
    return 0;
}
```

### 2.7 管道的读写行为

#### 2.7.1 读操作

​	有数据： read正常读，返回读出的字节数
​	无数据：  写端全部关闭: read解除阻塞，返回0, 相当于读文件读到了尾部。
​					  没有全部关闭： read阻塞

#### 2.7.2 写操作

​	读端全部关闭: 管道破裂，进程终止, 内核给当前进程发SIGPIPE信号
​	读端没全部关闭: 
​		缓冲区写满了,write阻塞
​		缓冲区没有满,继续write

### 2.8 如何设置管道为非阻塞

```c
	默认情况下，管道的读写两端都是阻塞的，若要设置读或者写端为非阻塞，则可参考下列三个步骤进行：
第1步： int flags = fcntl(fd[0], F_GETFL, 0); 
第2步： flag |= O_NONBLOCK;
第3步： fcntl(fd[0], F_SETFL, flags);
若是读端设置为非阻塞：
	写端没有关闭，管道中没有数据可读，则read返回-1；
	写端没有关闭，管道中有数据可读，则read返回实际读到的字节数
	写端已经关闭，管道中有数据可读，则read返回实际读到的字节数
	写端已经关闭，管道中没有数据可读，则read返回0
```

### 2.9获取管道缓冲区大小

```c
命令
	ulimit -a
函数 
    long fpathconf(int fd, int name);
    printf("pipe size==[%ld]\n", fpathconf(fd[0], _PC_PIPE_BUF));
    printf("pipe size==[%ld]\n", fpathconf(fd[1], _PC_PIPE_BUF));

```

## 3.FIFO

### 3.1 FIFO介绍

​	**FIFO**常被称为命名管道，以区分管道(pipe)。管道(pipe)只能用于“有血缘关系”的进程间通信。**但通过FIFO，不相关的进程也能交换数据。**

​     FIFO是Linux**基础文件类型**中的一种（**文件类型为p，可通过ls -l查看文件类型**）。但FIFO文件在磁盘上没有数据块，文件大小为0，仅仅用来标识内核中一条通道。进程可以打开这个文件进行read/write，实际上是在读写内核缓冲区，这样就实现了进程间通信。

### 3.2 创建管道

```c
方式1-使用命令 mkfifo
	命令格式： mkfifo 管道名
	例如：mkfifo myfifo
方式2-使用函数
	int mkfifo(const char *pathname, mode_t mode);
	参数说明和返回值可以查看man 3 mkfifo
	当创建了一个FIFO，就可以使用open函数打开它，常见的文件I/O函数都可用于FIFO。如：close、read、write、unlink等。
FIFO严格遵循先进先出，对FIFO的读总是从开始处返回数据，对它们的写则把数据添加到末尾。它们不支持诸如 lseek()等文件定位操作。
```

### 3.3 FIFO完成两个进程通信

```c
进程A：
	1.创建一个fifo文件：myfifo
	2.调用open函数打开myfifo文件
	3.调用write函数写入一个字符串如：“hello world”（其实是将数据写入到了内核缓冲区）
	4.调用close函数关闭myfifo文件
进程B：
	1.调用open函数打开myfifo文件
	2.调用read函数读取文件内容（其实就是从内核中读取数据）
	3.打印显示读取的内容
	4.调用close函数关闭myfifo文件

```

```c
//fifo完成两个进程间通信的测试
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>

int main()
{
	//创建fifo文件
	//int mkfifo(const char *pathname, mode_t mode);
	//判断myfofo文件是否存在,若不存在则创建
	int ret = access("./myfifo", F_OK);
	if(ret!=0)
	{
		ret = mkfifo("./myfifo", 0777);
		if(ret<0)
		{
			perror("mkfifo error");
			return -1;
		}
	}

	//打开文件
	int fd = open("./myfifo", O_RDWR);
	if(fd<0)
	{
		perror("open error");
		return -1;
	}

	//读fifo文件
	int n;
	char buf[64];
	while(1)
	{
		memset(buf, 0x00, sizeof(buf));
		n = read(fd, buf, sizeof(buf));
		printf("n==[%d], buf==[%s]\n", n, buf);
	}

	//关闭文件
	close(fd);

	//getchar();

	return 0;
}

```



## 4.内存映射区

### 4.1存储映射区介绍

​		**存储映射I/O (Memory-mapped I/O) 使一个磁盘文件与存储空间中的一个缓冲区相映射。从缓冲区中取数据，就相当于读文件中的相应字节；将数据写入缓冲区，则会将数据写入文件。这样，就可在不使用read和write函数的情况下，使用地址（指针）完成I/O操作。**

​		**使用存储映射这种方法，首先应通知内核，将一个指定文件映射到存储区域中。这个映射工作可以通过mmap函数来实现。**

### 4.2 mmap函数

```c
1. 函数作用:
	建立存储映射区
2. 函数原型
   void *mmap(void *addr, size_t length, int prot, int flags, int fd, off_t offset);
3. 函数返回值：
	成功： 返回创建的映射区首地址；
	失败： MAP_FAILED宏
4. 参数：	
	addr:  指定映射的起始地址, 通常设为NULL, 由系统指定
	length： 文件大小；//用lseek()获得，或者stat得到
	prot： 映射区的保护方式, 最常用的:
        读：PROT_READ
        写：PROT_WRITE
        读写：PROT_READ | PROT_WRITE
	flags： 映射区的特性, 可以是
		MAP_SHARED: 写入映射区的数据会写回文件, 且允许其他映射该文件的进程共享。
		MAP_PRIVATE: 对映射区的写入操作会产生一个映射区的复制(copy-on-write), 对此区域所做的修改不会写回原文件。
	fd： 由open返回的文件描述符, 代表要映射的文件。
	offset： 以文件开始处的偏移量, 必须是4k的整数倍, 通常为0, 表示从文件头开始映射。
```

#### 4.2.1 mmap函数注意事项

​		**创建映射区的过程中，隐含着一次对映射文件的读操作，将文件内容读取到映射区
​		当MAP_SHARED时，要求：映射区的权限应 <=文件打开的权限(出于对映射区的保护)。而MAP_PRIVATE则无所谓，因为mmap中的权限是对内存的限制。
​		映射区的释放与文件关闭无关，只要映射建立成功，文件可以立即关闭。
​		特别注意，当映射文件大小为0时，不能创建映射区。所以，用于映射的文件必须要有实际大小；mmap使用时常常会出现总线错误，通常是由于共享文件存储空间大小引起的。
​		munmap传入的地址一定是mmap的返回地址。坚决杜绝指针++操作。
​		文件偏移量必须为0或者4K的整数倍
​		mmap创建映射区出错概率非常高，一定要检查返回值，确保映射区建立成功再进行后续操作。**

#### 4.2.2 mmap函数使用总结

```c
	第一个参数写成NULL
	第二个参数要映射的文件大小 > 0
	第三个参数：PROT_READ 、PROT_WRITE
	第四个参数：MAP_SHARED 或者 MAP_PRIVATE
	第五个参数：打开的文件对应的文件描述符
	第六个参数：4k的整数倍
```

### 4.3 munmap函数

```c
函数作用:
	释放由mmap函数建立的存储映射区
函数原型:
	int munmap(void *addr, size_t length);
返回值：
	成功：返回0
	失败：返回-1，设置errno值
函数参数:
	addr：调用mmap函数成功返回的映射区首地址
	length：映射区大小（mmap函数的第二个参数）

```

### 4.4 匿名映射

```c
使用mmap函数建立匿名映射：//只能用于有血缘关系的进程通信
mmap(NULL, 4096, PROT_READ | PROT_WRITE, MAP_SHARED | MAP_ANONYMOUS, -1, 0);
//MAP_ANONYMOUS 必须和 MAP_SHARED 一起使用，而且df指定为-4，映射区大小也要自己指定
```

# 十、信号

## 1.信号机制

**每个进程收到的所有信号，都是由内核负责发送的。**

![image-20210713090610576](E:\typroa_pic\image-20210713090610576.png)

### 1.1 信号状态

​	信号有三种状态：产生、未决和递达。

1. 信号的产生
   			按键产生，如：Ctrl+c、Ctrl+z、Ctrl+\
        		系统调用产生，如：kill、raise、abort
        		软件条件产生，如：定时器alarm
        		硬件异常产生，如：非法访问内存(段错误)、除0(浮点数例外)、内存对齐出错(总线错误)
        		命令产生，如：kill命令

2. 未决：产生和递达之间的状态。主要由于阻塞(屏蔽)导致该状态。 

3. 递达：递送并且到达进程。

### 1.2 信号处理方式

1. 执行默认动作
2. 忽略信号（丢弃不处理）
3. 捕捉信号（调用用户的自定义的处理函数）

### 1.3 信号四要素

```c
通过man 7 sign  or ignored.
几个常用到的信号
SIGINT、SIGQUIT、SIGKILL、SIGSEGV、SIGUSR1、SIGUSR2、SIGPIPE、SIGALRM、SIGTERM、SIGCHLD、SIGSTOP、SIGCONT

```

**PS: 阻塞的函数接收到信号会立马中断**

## 2.信号相关函数

### 2.1 signal函数

```c
函数作用：注册信号捕捉函数
函数原型
 		typedef void (*sighandler_t)(int);
       	sighandler_t signal(int signum, sighandler_t handler);
函数参数
	signum：信号编号
	handler：信号处理函数（需要自己写，回调函数）
```

### 2.2 kill函数

```c
描述：给指定进程发送指定信号
	kill命令：kill -SIGKILL 进程PID
	kill函数原型：int kill(pid_t pid, int sig);	
函数返回值：
	成功：0；
	失败：-1，设置errno
函数参数：
	sig信号参数：不推荐直接使用数字，应使用宏名，因为不同操作系统信号编号可能不同，但名称一致。
	pid参数：
	pid > 0: 发送信号给指定的进程。
	pid = 0: 发送信号给与调用kill函数进程属于同一进程组的所有进程。
	pid < -1:  取|pid|发给对应进程组。
	pid = -1：发送给进程有权限发送的系统中所有进程。

```

​		进程组：每个进程都属于一个进程组，进程组是一个或多个进程集合，他们相互关联，共同完成一个实体任务，每个进程组都有一个进程组长，默认进程组ID与进程组长ID相同

### 2.3 raise函数和abort函数

#### 2.3.1 raise函数

```c
函数描述：给当前进程发送指定信号(自己给自己发) 

函数原型：int raise(int sig);

函数返回值：成功：0，失败非0值

函数拓展：raise(signo) == kill(getpid(), signo);
```

#### 2.3.2 abort函数

```c
函数描述：给自己发送异常终止信号 6) SIGABRT，并产生core文件

函数原型：void abort(void); 

函数拓展：abort() == kill(getpid(), SIGABRT);
```

#### 2.3.3 alarm函数

```c
函数原型：
	unsigned int alarm(unsigned int seconds); 
函数描述：
	设置定时器（闹钟）。在指定seconds后，内核会给当前进程发送14）SIGALRM信号。进程收到该信号，默认动作终止。每个进程都有且只有唯一的一个定时器。
函数返回值：
	返回0或剩余的秒数，无失败。例如
    
常用操作：
	取消定时器alarm（0），返回旧闹钟余下秒数。
	alarm使用的是自然定时法，与进程状态无关，就绪、运行、挂起（阻塞、暂停）、终止、僵尸...无论进程处于何种状态，alarm都计时。

```

####  2.3.4 setitimer 函数

```c
函数原型       
   int setitimer(int which, const struct itimerval *new_value, struct itimerval *old_value);
函数描述
	设置定时器(闹钟)，可代替alarm函数，精度微秒us，可以实现周期定时。
函数返回值
	成功：0；
	失败：-1，设置errno值
函数参数： 
	which：指定定时方式
	自然定时：ITIMER_REAL → 14）SIGALRM计算自然时间
	虚拟空间计时(用户空间)：ITIMER_VIRTUAL → 26）SIGVTALRM  只计算进程占用cpu的时间
	运行时计时(用户+内核)：ITIMER_PROF → 27）SIGPROF计算占用cpu及执行系统调用的时间
	new_value：struct itimerval, 负责设定timeout时间。
	itimerval.it_value: 设定第一次执行function所延迟的秒数 itimerval.it_interval: 设定以后每几秒执行function
struct itimerval { 
    struct timerval it_interval; // 闹钟触发周期
    struct timerval it_value; // 闹钟触发时间
  }; 
  struct timeval { 
    long tv_sec; 			// 秒
    long tv_usec; 			// 微秒
 }             
	old_value： 存放旧的timeout值，一般指定为NULL

```

```c
setitimer 实际调用

//SIGALRM 信号处理函数
  void sighandler(int signo)
  {   
      printf("signo=[%d]\n",signo);
  }
  int main()
  {
  
      //int setitimer(int which, const struct itimerval *new_value,
      //                     struct itimerval *old_value);
  
      //注册信号SIGALRM信号处理函数
  
      signal(SIGALRM,sighandler);
  
      struct itimerval tm;
      //周期性时间赋值
      tm.it_interval.tv_sec = 1;
      tm.it_interval.tv_usec = 0;
  
  
      //第一次出发的时间
      tm.it_value.tv_sec = 3;
      tm.it_value.tv_usec = 1;
  
      setitimer(ITIMER_REAL,&tm,NULL);
  
      while(1)
      {
          sleep(1);
      }
      return 0;
  }

```

## 3.信号集

​		阻塞信号集是当前进程要阻塞的信号的集合，未决信号集是当前进程中还处于未决状态的信号的集合，这两个集合存储在内核的PCB中。

```c
下面以SIGINT为例说明信号未决信号集和阻塞信号集的关系：
	当进程收到一个SIGINT信号（信号编号为2），首先这个信号会保存在未决信号集合中，此时对应的2号编号的这个位置上置为1，表示处于未决状态；在这个信号需要被处理之前首先要在阻塞信号集中的编号为2的位置上去检查该值是否为1：
	如果为1，表示SIGNIT信号被当前进程阻塞了，这个信号暂时不被处理，所以未决信号集上该位置上的值保持为1，表示该信号处于未决状态；
	如果为0，表示SIGINT信号没有被当前进程阻塞，这个信号需要被处理，内核会对SIGINT信号进行处理（执行默认动作，忽略或者执行用户自定义的信号处理函数），并将未决信号集中编号为2的位置上将1变为0，表示该信号已经处理了，这个时间非常短暂，用户感知不到。
当SIGINT信号从阻塞信号集中解除阻塞之后，该信号就会被处理。

```

![image-20210731155341514](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210731155341514.png)

## 4.信号集相关函数

```c
信号集相关函数
	int sigemptyset(sigset_t *set);
函数说明：将某个信号集清0		 	
函数返回值：成功：0；失败：-1，设置errno
    
	int sigfillset(sigset_t *set);
函数说明：将某个信号集置1		  		
函数返回值：成功：0；失败：-1，设置errno
    
	int sigaddset(sigset_t *set, int signum);	
函数说明：将某个信号加入信号集合中
函数返回值：成功：0；失败：-1，设置errno
    
	int sigdelset(sigset_t *set, int signum);	
函数说明：将某信号从信号清出信号集   	
函数返回值：成功：0；失败：-1，设置errno
    
	int sigismember(const sigset_t *set, int signum);
函数说明：判断某个信号是否在信号集中
函数返回值：在：1；不在：0；出错：-1，设置errno
    
	sigprocmask函数
函数说明：用来屏蔽信号、解除屏蔽也使用该函数。其本质，读
取或修改进程控制块中的信号屏蔽字（阻塞信号集）。
特别注意，屏蔽信号只是将信号处理延后执行(延至解除屏蔽)；而忽略表示将信号丢弃处理。
    
	函数原型：int sigprocmask(int how, const sigset_t *set, sigset_t *oldset);
	函数返回值：成功：0；失败：-1，设置errno
	函数参数：
	how参数取值：假设当前的信号屏蔽字为mask
    	SIG_BLOCK: 当how设置为此值，set表示需要屏蔽的信号。相当于 mask = mask|set
    	SIG_UNBLOCK: 当how设置为此，set表示需要解除屏蔽的信号。相当于 mask = mask & ~set
    	SIG_SETMASK: 当how设置为此，set表示用于替代原始屏蔽及的新屏蔽集。相当于mask = set若，调用sigprocmask解除了对当前若干个信号的阻塞，则在sigprocmask返回前，至少将其中一个信号递达。
	set：传入参数，是一个自定义信号集合。由参数how来指示如何修改当前信号屏蔽字。
	oldset：传出参数，保存旧的信号屏蔽字。
    
	sigpending函数
	函数原型：int sigpending(sigset_t *set);	   
	函数说明：读取当前进程的未决信号集
	函数参数：set传出参数
	函数返回值：成功：0；失败：-1，设置errno

```

## 5.sigaction函数

```c
sigaction函数
函数说明：
	注册一个信号处理函数
函数原型：
  int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);
函数参数：
	signum：捕捉的信号
	act：    传入参数，新的处理方式。
	oldact： 传出参数，旧的处理方式

struct sigaction {
       void  (*sa_handler)(int);	// 信号处理函数
       void  (*sa_sigaction)(int, siginfo_t *, void *); //信号处理函数
       sigset_t  sa_mask; //信号处理函数执行期间需要阻塞的信号
       int      sa_flags; //通常为0，表示使用默认标识
       void     (*sa_restorer)(void);
};

总结：
	sa_handler：指定信号捕捉后的处理函数名(即注册函数)。也可赋值为SIG_IGN表忽略 或 SIG_DFL表执行默认动作
	sa_mask: 用来指定在信号处理函数执行期间需要被屏蔽的信号，特别是当某个信号被处理时，它自身会被自动放入进程的信号掩码，因此在信号处理函数执行期间这个信号不会再度发生。注意：仅在处理函数被调用期间屏蔽生效，是临时性设置。
	sa_flags：通常设置为0，使用默认属性。
	sa_restorer：已不再使用	

```

**知识点: **

​	**信号处理不支持排队；**

​	**在XXX信号处理函数执行期间, XXX信号是被阻塞的, 如果该信号产生了多次, 在XXX信号处理函数结束之后, 该XXX信号只被处理一次；**

​	**在XXX信号处理函数执行期间(前提是sa_mask中阻塞了YYY信号),如果阻塞了YYY信号, 若YYY信号产生了多次, 当XXX信号处理函数结束后, YYY信号只会被处理一次.**

```c
//sigaction函数测试---注册信号处理函数
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
#include <signal.h>

//信号处理函数
void sighandler(int signo)
{
	printf("signo==[%d]\n", signo);
	sleep(4);
}

int main()
{
	//注册信号处理函数
	struct sigaction act;
	act.sa_handler = sighandler;
	sigemptyset(&act.sa_mask);  //在信号处理函数执行期间, 不阻塞任何信号
	sigaddset(&act.sa_mask, SIGQUIT);//在信号处理函数执行期间,阻塞SIGQUIT信号
	act.sa_flags = 0;
	sigaction(SIGINT, &act, NULL);

	
	signal(SIGQUIT, sighandler);	

	while(1)
	{
		sleep(10);
	}

	return 0;
}

```

## 6. SIGCHLD信号

### 6.1 产生SIGCHLD信号的条件

1. **子进程结束的时候**

2. **子进程收到SIGSTOP信号**

3. **当子进程停止时，收到SIGCONT信号**

### 6.2 SIGCHLD信号的作用

​    **子进程退出后，内核会给它的父进程发送SIGCHLD信号，父进程收到这个信号后可以对子进程进行回收。**

​    **使用SIGCHLD信号完成对子进程的回收可以避免父进程阻塞等待而不能执行其他操作，只有当父进程收到SIGCHLD信号之后才去调用信号捕捉函数完成对子进程的回收，未收到SIGCHLD信号之前可以处理其他操作。**

**注意点：**

​		**有可能还未完成信号处理函数的注册三个子进程都退出了。**

解决办法：可以在fork之前先将SIGCHLD信号阻塞，当完成信号处理函数的注册后在解除阻塞。

​		**当SIGCHLD信号函数处理期间, SIGCHLD信号若再次产生是被阻塞的,而且若产生了多次, 则该信号只会被处理一次, 这样可能会产生僵尸进程。**

 解决办法: 可以在信号处理函数里面使用while(1)循环回收, 这样就有可能出现捕获一次SIGCHLD信号但是回收了多个子进程的情况，从而可以避免产生僵尸进程。

```c
//父进程使用SICCHLD信号完成对子进程的回收
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>

void waitchild(int signo)
{
	pid_t wpid;

	//回收子进程
	while(1)
	{
		wpid = waitpid(-1, NULL, WNOHANG);
		if(wpid>0)
		{
			printf("child is quit, wpid==[%d]\n", wpid);
		}
		else if(wpid==0)
		{
			printf("child is living, wpid==[%d]\n", wpid);
			break;
		}
		else if(wpid==-1)
		{
			printf("no child is living, wpid==[%d]\n", wpid);
			break;
		}
	}
}

int main()
{
	int i = 0;
	int n = 3;

	//将SIGCHLD信号阻塞
	sigset_t mask;
	sigemptyset(&mask);
	sigaddset(&mask, SIGCHLD);
	sigprocmask(SIG_BLOCK, &mask, NULL);

	for(i=0; i<n; i++)	
	{
		//fork子进程
		pid_t pid = fork();
		if(pid<0) //fork失败的情况
		{
			perror("fork error");
			return -1;
		}
		else if(pid>0) //父进程
		{
			printf("father: fpid==[%d], cpid==[%d]\n", getpid(), pid);
			sleep(1);
		}
		else if(pid==0) //子进程
		{
			printf("child: fpid==[%d], cpid==[%d]\n", getppid(), getpid());
			break;
		}
	}

	//父进程
	if(i==3)
	{
		printf("[%d]:father: fpid==[%d]\n", i, getpid());
		//signal(SIGCHLD, waitchild);
		//注册信号处理函数
		struct sigaction act;
		act.sa_handler = waitchild;
		sigemptyset(&act.sa_mask);
		act.sa_flags = 0;
		sleep(5);
		sigaction(SIGCHLD, &act, NULL);

		//解除对SIGCHLD信号的阻塞
		sigprocmask(SIG_UNBLOCK, &mask, NULL);

		while(1)
		{
			sleep(1);
		}
	}

	//第1个子进程
	if(i==0)
	{
		printf("[%d]:child: cpid==[%d]\n", i, getpid());
		//sleep(1);
	}

	//第2个子进程
	if(i==1)
	{
		printf("[%d]:child: cpid==[%d]\n", i, getpid());
		sleep(1);
	}

	//第3个子进程
	if(i==2)
	{
		printf("[%d]:child: cpid==[%d]\n", i, getpid());
		sleep(1);
	}

	return 0;
}

```

# 十一、守护进程和线程

## 1.守护进程

### 1.1 守护进程介绍

Linux后台的一些系统服务进程，没有控制终端，不能直接和用户交互。不受用户登录、注销的影响，一直在运行着，他们都是守护进程。如：预读入缓输出机制的实现；ftp服务器；nfs服务器等

总结守护进程的特点：

​		**Linux后台服务进程**

​		**独立于控制终端**

​		**周期性的执行某种任务**

​		**不受用户登陆和注销的影响**

​		**一般采用以d结尾的名字**

### 1.2进程组和会话

![image-20210802215419552](E:\typroa_pic\image-20210802215419552.png)

#### 1.2.1 进程组

​		**进程组是一个或者多个进程的集合，每个进程都属于一个进程组，引入进程组是为了简化对进程的管理。当父进程创建子进程的时候，默认子进程与父进程属于同一个进程组。**

​		**进程组ID==第一个进程ID（组长进程）。如父进程创建了多个子进程，父进程和多个子进程同属于一个组，而由于父进程是进程组里的第一个进程，所以父进程就是这个组的组长, 组长ID==父进程ID。**

​		**可以使用kill -SIGKILL -进程组ID(负的)来将整个进程组内的进程全部杀死。**

​		**只要进程组中有一个进程存在，进程组就存在，与组长进程是否终止无关。**

​		**进程组生存期：从进程组创建到最后一个进程离开**

​		**ps -ef |wc -l   ---查看进程数**

#### 1.2.2 会话

​		**一个会话是一个或多个进程组的集合。**

​		**创建会话的进程不能是进程组组长**

​		**创建会话的进程成为一个进程组的组长进程，同时也成为会话的会长。**

​		**需要有root权限（ubuntu不需要）**

​		**新创建的会话丢弃原有的控制终端**

​		**建立新会话时，先调用fork, 父进程终止，子进程调用setsid函数**

​		**可以使用  ps ajx  来查看进程组ID和会话ID**

​		**可以fork出几个子进程，然后查看进程组ID和会话ID**

### 1.3. 创建守护进程的模型

```c
第1步：fork子进程，父进程退出
	子进程继承了父进程的进程组ID, 但具有一个新的进程ID,这样就保证了子进程不是一个进程组的组长ID,这对于下面要做的setsid函数的调用是必要的前提条件
    
第2步：子进程调用setsid函数创建新会话
	调用这个函数以后
	该进程成为新会话的首进程，是会话的会长
	成为一个新进程组的组长进程，是进程组组长
	不受控制终端的影响
    
第3步：改变当前工作目录chdir   ---非必须
	如：a.out在U盘上，启动这个程序，这个程序的当前的工作目录就是这个u盘，如果u盘拔掉后进程的当前工作目录将消失，a.out将不能正常工作。
    
第4步：重设文件掩码   mode & ~umask    ---非必须
	子进程会继承父进程的掩码
	增加子进程程序操作的灵活性
	umask(0000);

第5步：关闭文件描述符  ---非必须
	守护进程不受控制终端的影响所以可以关闭，以释放资源 
	close(STDIN_FILENO);
close(STDOUT_FILENO);
close(STDERR_FILENO);
第6步：执行核心工作
	守护进程的核心代码逻辑

```

```c
//创建守护进程
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <signal.h>
#include <time.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <sys/time.h>

void myfunc(int signo)
{
	//打开文件
	int fd = open("mydemon.log", O_RDWR | O_CREAT | O_APPEND, 0755);
	if(fd<0)
	{
		return;
	}

	//获取当前的系统时间
	time_t t;
	time(&t);
	char *p = ctime(&t);
	
	//将时间写入文件
	write(fd, p, strlen(p));

	close(fd);

	return;
}

int main()
{
	//父进程fork子进程, 然后父进程退出
	pid_t pid = fork();
	if(pid<0 || pid>0)
	{
		exit(1);
	}
	
	//子进程调用setsid函数创建会话
	setsid();

	//改变当前的工作目录
	chdir("/home/itcast/log");

	//改变文件掩码
	umask(0000);

	//关闭标准输入,输出和错误输出文件描述符
	close(STDIN_FILENO);
	close(STDOUT_FILENO);
	close(STDERR_FILENO);

	//核心操作
	//注册信号处理函数
	struct sigaction act;
	act.sa_handler = myfunc;
	act.sa_flags = 0;
	sigemptyset(&act.sa_mask);	
	sigaction(SIGALRM, &act, NULL);

	//设置时钟
	struct itimerval tm;
	tm.it_interval.tv_sec = 2;
	tm.it_interval.tv_usec = 0;
	tm.it_value.tv_sec = 3;
	tm.it_value.tv_usec = 0;
	setitimer(ITIMER_REAL, &tm, NULL);

	printf("hello world\n");

	while(1)
	{
		sleep(1);
	}
}

```



## 2. 线程

**编译时需要在gcc最后加上 -lpthread才可编译成功**

### 2.1 线程介绍

​	图中的栈不能共享是指不能在线程之间访问栈，但是当栈的生存期足够长，栈中的内容可以通过指针形式递出并且被访问。

![image-20210803151541005](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210803151541005.png)

```c++
1. 在Linux操作系统下：	
	线程：最小的执行单位
	进程：最小分配资源单位，可看成是只有一个线程的进程。
    
2. 线程的特点
	类Unix系统中，早期是没有“线程”概念的，80年代才引入，借助进程机制实现出了线程的概念。因此在这类系统中，进程和线程关系密切。
	线程是轻量级进程(light-weight process)，也有PCB，创建线程使用的底层函数和进程一样，都是clone
	从内核里看进程和线程是一样的，都有各自不同的PCB.
	进程可以蜕变成线程
	在linux下，线程最是小的执行单位；进程是最小的分配资源单位

3. 查看指定线程的LWP号：ps –Lf pid
	实际上，无论是创建进程的fork，还是创建线程的pthread_create，底层实现都是调用同一个内核函数 clone。
	如果复制对方的地址空间，那么就产出一个“进程”；
	如果共享对方的地址空间，就产生一个“线程”。
	so：Linux内核是不区分进程和线程的, 只在用户层面上进行区分。所以，线程所有操作函数 pthread_* 是库函数，而非系统调用。
```

### 2.2 线程创建

#### 2.2.1 pthread_create函数

```c
函数作用：
	创建一个新线程
函数原型
	int pthread_create(pthread_t *thread, 
				const pthread_attr_t *attr,
              	void *(*start_routine) (void *),
  				void *arg);
返回值
	成功，返回0
	失败，返回错误号
    
函数参数：
	pthread_t：传出参数，保存系统为我们分配好的线程ID
	当前Linux中可理解为：typedef unsigned long int pthread_t。
	attr：通常传NULL，表示使用线程默认属性。若想使用具体属性也可以修改该参数。
	start_routine：函数指针，指向线程主函数(线程体)，该函数运行结束，则线程结束。
	arg：线程主函数执行期间所使用的参数。
    
注意点：
	由于pthread_create的错误码不保存在errno中，因此不能直接用perror()打印错误信息，可以先用strerror()把错误码转换成错误信息再打印。
	如果任意一个线程调用了exit或_exit，则整个进程的所有线程都终止，由于从main函数return也相当于调用exit，为了防止新创建的线程还没有得到执行就终止，我们在main函数return之前延时1秒，这只是一种权宜之计，即使主线程等待1秒，内核也不一定会调度新创建的线程执行，下一节我们会看到更好的办法。

```

#### 2.2.2 创建线程的时候传递参数

```c
int main()
{
    //int n = 99;
	struct Test t;
	//int ret = pthread_create(&thread, NULL, 		mythread, &n);
    pthread_t thread;
	int ret = pthread_create(&thread, NULL, mythread, &t);
}
void *mythread(void *arg)
{
	//int n = *(int *)arg;
	struct Test *p = (struct Test *)arg;
	//struct Test *p = arg;
}
```

#### 2.2.3 循环创建子线程

```c
//循环创建子线程,并且打印是第几个子线程
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <pthread.h>

//线程执行函数
void *mythread(void *arg)
{
	int i = *(int *)arg;
	printf("[%d]:child thread, pid==[%d], id==[%ld]\n", i, getpid(), pthread_self());
	sleep(100);
}

int main()
{
	//int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
	//                      void *(*start_routine) (void *), void *arg);
	//创建子线程
	int ret;
	int i = 0;
	int n = 5;
	int arr[5];
	pthread_t thread[5];
	for(i=0; i<n; i++)
	{
		arr[i] = i;
		ret = pthread_create(&thread[i], NULL, mythread, &arr[i]);
		if(ret!=0)
		{
			printf("pthread_create error, [%s]\n", strerror(ret));
			return -1;
		}
	}
	printf("main thread, pid==[%d], id==[%ld]\n", getpid(), pthread_self());

	//目的是为了让子线程能够执行起来
	sleep(100);
	return 0;
}

```

**小技巧：创建一个文件pmak，写gcc -o $1 $1.c -lpthread，然后在终端输入   ./pmak   文件名**     **--其中 $1 表示第一个参数**

### 2.3 线程退出和线程回收

#### 2.3.1  pthread_exit函数

​		**在线程中禁止调用exit函数，否则会导致整个进程退出，取而代之的是调用pthread_exit函数，这个函数是使一个线程退出，如果主线程调用pthread_exit函数也不会使整个进程退出，不影响其他线程的执行。**

```c
pthread_exit函数
函数描述
	将单个线程退出
函数原型
	void pthread_exit(void *retval);	
函数参数
	retval表示线程退出状态，通常传NULL
	另注意，pthread_exit或者return返回的指针所指向的内存单元必须是全局的或者是用malloc分配的，不能在线程函数的栈上分配，因为当其它线程得到这个返回指针时线程函数已经退出了，栈空间就会被回收。
```

#### 2.3.2  pthread_ join函数

**阻塞等待线程退出，可以防止僵尸线程的产生**

```c
函数描述：
    阻塞等待线程退出，获取线程退出状态。其作用，对应进程中的waitpid() 函数。
函数原型：
    int pthread_join(pthread_t thread, void **retval); 
函数返回值：
	成功：0；
	失败：错误号
	函数参数： 
	thread：线程ID
	retval：存储线程结束状态，整个指针和pthread_exit的参数是同一块内存地址。

```

#### 2.3.3  pthread_detach函数

​		线程分离状态：指定该状态，线程主动与主控线程断开关系。线程结束后，其退出状态不由其他线程获取，而直接自己自动释放。网络、多线程服务器常用。

​		进程若有该机制，将不会产生僵尸线程。僵尸线程的产生主要由于进程死后，大部分资源被释放，一点残留资源仍存于系统中，导致内核认为该进程程仍存在。

​	也可使用 pthread_create函数参2(线程属性)来设置线程分离。pthread_detach函数是在创建线程之后调用的。

```c
函数描述
	实现线程分离
函数原型
	int pthread_detach(pthread_t thread);	
函数返回值
	成功：0；
	失败：错误号
	一般情况下，线程终止后，其终止状态一直保留到其它线程调用pthread_join获取它的状态为止。但是线程也可以被置为detach状态，这样的线程一旦终止就立刻回收它占用的所有资源，而不保留终止状态。不能对一个已经处于detach状态的线程调用pthread_join，这样的调用将返回EINVAL错误。也就是说，如果已经对一个线程调用了pthread_detach就不能再调用pthread_join了。
```

```c
//设置子线程为分离属性
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <pthread.h>

//线程执行函数
void *mythread(void *arg)
{
	printf("child thread, pid==[%d], id==[%ld]\n", getpid(), pthread_self());
	sleep(10);
}

int main()
{
	//int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
    //                      void *(*start_routine) (void *), void *arg);
	//创建子线程
	pthread_t thread;
	int ret = pthread_create(&thread, NULL, mythread, NULL);
	if(ret!=0)
	{
		printf("pthread_create error, [%s]\n", strerror(ret));
		return -1;
	}
	printf("main thread, pid==[%d], id==[%ld]\n", getpid(), pthread_self());

	//设置线程为分离属性
	pthread_detach(thread);

	//子线程设置分离属性,则pthread_join不再阻塞,立刻返回
	ret = pthread_join(thread, NULL);
	if(ret!=0)
	{
		printf("pthread_join error:[%s]\n", strerror(ret));
	}

	//目的是为了让子线程能够执行起来
	sleep(1);
	return 0;
}

```

#### 2.3.4pthread_cancel函数

```c
函数描述
	杀死(取消)线程。其作用，对应进程中 kill() 函数。

函数原型
	int pthread_cancel(pthread_t thread);	

函数返回值
	成功：0；
	失败：错误号

【注意】：线程的取消并不是实时的，而有一定的延时。需要等待线程到达某个取消点（检查点）。
类似于玩游戏存档，必须到达指定的场所(存档点，如：客栈、仓库、城里等)才能存储进度。杀死线程也不是立刻就能完成，必须要到达取消点。
取消点：是线程检查是否被取消，并按请求进行动作的一个位置。通常是一些系统调用creat，open，pause，close，read，write..... 执行命令man 7 pthreads可以查看具备这些取消点的系统调用列表。可粗略认为一个系统调用(进入内核)即为一个取消点。还以通过调用pthread_testcancel函数设置一个取消点。
	函数原型：void pthread_testcancel(void);

```

#### 2.3.5 pthread_equal函数

```c
函数描述：
	比较两个线程ID是否相等。
	函数原型
	int pthread_equal(pthread_t t1, pthread_t t2);
注意：这个函数是为了以能够扩展使用的， 有可能Linux在未来线程ID pthread_t 类型被修改为结构体实现。

```

#### 2.3.6进程函数和线程函数比较

| **进程**         | **线程**             |
| ---------------- | -------------------- |
| **fork**         | **pthread_create  ** |
| **exit**         | **pthread_exit  **   |
| **wait/waitpid** | **pthread_join  **   |
| **kill**         | **pthread_cancel  ** |
| **getpid**       | **pthread_self**     |

## 3. 线程属性

​		linux下线程的属性是可以根据实际项目需要，进行设置，之前讨论的线程都是采用线程的默认属性，默认属性已经可以解决绝大多数开发时遇到的问题，如果对程序的性能提出更高的要求，则需要设置线程属性，本节以设置线程的分离属性为例讲解设置线程属性。

​	线程的分离状态决定一个线程以什么样的方式来终止自己，有两种状态：
​		非分离状态：线程的默认属性是非分离状态，这种情况下，原有的线程等待创建的线程结束。只有当pthread_join()函数返回时，创建的线程才算终止，才能释放自己占用的系统资源。
​		分离状态：分离线程没有被其他的线程所等待，自己运行结束了，线程也就终止了，马上释放系统资源。应该根据自己的需要，选择适当的分离状态。

```c
设置线程属性分为以下步骤
第1步：定义线程属性类型类型的变量
	pthread_attr_t  attr;	

第2步：对线程属性变量进行初始化
	int pthread_attr_init (pthread_attr_t* attr);

第3步：设置线程为分离属性
	int pthread_attr_setdetachstate(
    				pthread_attr_t *attr, 
					int detachstate);
参数:
	attr: 线程属性
	detachstate:
	PTHREAD_CREATE_DETACHED(分离)
	PTHREAD_CREATE_JOINABLE(非分离)
 	注意：这一步完成之后调用pthread_create函数创建线程，
			则创建出来的线程就是分离线程；其实上述三步就是
			pthread_create的第二个参数做准备工作。
    
第4步：释放线程属性资源
	int pthread_attr_destroy(pthread_attr_t *attr);
参数：线程属性

```

```c
//在创建子线程的时候设置分离属性
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <pthread.h>

//线程执行函数
void *mythread(void *arg)
{
	printf("child thread, pid==[%d], id==[%ld]\n", getpid(), pthread_self());
	sleep(2);
}

int main()
{
	//定义pthread_attr_t类型的变量
	pthread_attr_t attr;
	
	//初始化attr变量
	pthread_attr_init(&attr);

	//设置attr为分离属性
	pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);

	//创建子线程
	pthread_t thread;
	int ret = pthread_create(&thread, &attr, mythread, NULL);
	if(ret!=0)
	{
		printf("pthread_create error, [%s]\n", strerror(ret));
		return -1;
	}
	printf("main thread, pid==[%d], id==[%ld]\n", getpid(), pthread_self());

	//释放线程属性
	pthread_attr_destroy(&attr);

	//验证子线程是否为分离属性
	ret = pthread_join(thread, NULL);
	if(ret!=0)
	{
		printf("pthread_join error:[%s]\n", strerror(ret));
	}

	return 0;
}

```

## 4. 线程同步

### 4.1线程同步的概念

​		线程同步，指一个线程发出某一功能调用时，在没有得到结果之前，该调用不返回。同时其它线程为保证数据一致性，不能调用该功能。

### 4.2数据混乱的原因

​	**资源共享（独享资源则不会） **

​	**调度随机（线程操作共享资源的先后顺序不确定）  **

​	**线程间缺乏必要的同步机制**

​		以上3点中，前两点不能改变，欲提高效率，传递数据，资源必须共享。只要共享资源，就一定会出现竞争。只要存在竞争关系，数据就很容易出现混乱。所以只能从第三点着手解决。使多个线程在访问共享资源的时候，出现互斥。

如何解决问题

1. 原子操作的概念

   ​		 原子操作指的是该操作要么不做，要么就完成。

2. 使用互斥锁解决同步问题

   ​		使用互斥锁其实是模拟原子操作，互斥锁示意图：

![image-20210803215952584](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210803215952584.png)

### 4.3互斥锁主要相关函数

```c
1. pthread_mutex_t 类型
	其本质是一个结构体，为简化理解，应用时可忽略其实现细节，简单当成整数看待。
	pthread_mutex_t mutex; 变量mutex只有两种取值1、0。
    
2. pthread_mutex_init函数
函数描述：
	初始化一个互斥锁(互斥量) ---> 初值可看作1
函数原型：
	int pthread_mutex_init(pthread_mutex_t *restrict mutex, const pthread_mutexattr_t *restrict attr);
函数参数
	mutex：传出参数，调用时应传 &mutex	
	attr：互斥锁属性。是一个传入参数，通常传NULL，选用默认属性(线程间共享)。
	restrict关键字：只用于限制指针，告诉编译器，所有修改该指针指向内存中内容的操作，只能通过本指针完成。不能通过除本指针以外的其他变量或指针修改互斥量mutex的两种初始化方式：   
	静态初始化：如果互斥锁 mutex 是静态分配的（定义在全局，或加了static关键字修饰），可以直接使用宏进行初始化。
pthead_mutex_t muetx = PTHREAD_MUTEX_INITIALIZER;
	动态初始化：局部变量应采用动态初始化。
pthread_mutex_init(&mutex, NULL)
    
3. pthread_mutex_destroy函数
函数描述
	销毁一个互斥锁
函数原型
	int pthread_mutex_destroy(pthread_mutex_t *mutex);
函数参数
	mutex—互斥锁变量
    
    
4. pthread_mutex_lock函数（阻塞）
函数描述
	对互斥所加锁，可理解为将mutex--
函数原型
int pthread_mutex_lock(pthread_mutex_t *mutex);
函数参数
	mutex—互斥锁变量
    
5. pthread_mutex_unlock函数
函数描述
	对互斥所解锁，可理解为将mutex ++
函数原型
	int pthread_mutex_unlock(pthread_mutex_t *mutex);

6. pthread_mutex_trylock函数(不阻塞)
函数描述
	尝试加锁，加锁成功直接返回0，失败返回EBUSY
函数原型
	int pthread_mutex_trylock(pthread_mutex_t *mutex);
函数参数
	mutex—互斥锁变量

```

### 4.4互斥锁使用步骤

1. 创建一把锁：**pthread_mutex_t mutex；  //mutex = 1;**

   **（全局定义）**

2. 在main函数中初始化互斥锁：**pthread_mutex_init(&mutex,NULL)；**

3. 锁的使用 ---在共享资源出现的位置的上下加锁和解锁

   **pthread_mutex_lock(&mutex)； //mutex - -;**

   **//共享资源操作的代码**

   **pthread_mutex_unlock(&mutex)； //mutex + +;**

4. 在main函数中释放互斥锁

   **pthread_mutex_destroy(&mutex)；**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <pthread.h>
#include <time.h>

//定义一把锁
pthread_mutex_t mutex;

void *mythread1(void *args)
{
	while(1)
	{
		//加锁
		pthread_mutex_lock(&mutex);

		printf("hello ");
		sleep(rand()%3);
		printf("world\n");
	
		//解锁
		pthread_mutex_unlock(&mutex);
		sleep(rand()%3);
	}

	pthread_exit(NULL);
}

void *mythread2(void *args)
{
	while(1)
	{
		//加锁
		pthread_mutex_lock(&mutex);

		printf("HELLO ");
		sleep(rand()%3);
		printf("WORLD\n");

		//解锁
		pthread_mutex_unlock(&mutex);
		sleep(rand()%3);
	}

	pthread_exit(NULL);
}

int main()
{
	int ret;
	pthread_t thread1;
	pthread_t thread2;

	//随机数种子
	srand(time(NULL));
	
	//互斥锁初始化
	pthread_mutex_init(&mutex, NULL);

	ret = pthread_create(&thread1, NULL, mythread1, NULL);
	if(ret!=0)
	{
		printf("pthread_create error, [%s]\n", strerror(ret));
		return -1;
	}

	ret = pthread_create(&thread2, NULL, mythread2, NULL);
	if(ret!=0)
	{
		printf("pthread_create error, [%s]\n", strerror(ret));
		return -1;
	}

	//等待线程结束
	pthread_join(thread1, NULL);
	pthread_join(thread2, NULL);

	//释放互斥锁
	pthread_mutex_destroy(&mutex);
	return 0;
}

```

### 4.5死锁

​		死锁不是linux提供给开发者的一种机制, 而是由于开发者操作不当引起的.
​	**1. 自己锁自己.**
​		注意点: 线程在异常退出的时候也需要解锁.
​	**2. A线程占用着A锁, 又想去获得B锁; B线程占用着B锁, 又想去获得A锁, 两个线程都不释放自己的锁, 又想去获得对方的锁, 从而造成了死锁.**
​	  解决方法:
​	  	**1. 需要先释放自己的锁再去获得其他锁**
​	  	**2. 避免使用嵌套的锁, 让线程按照一定的顺序加锁**
​	  	**3. 可以调用pthread_mutex_trylock函数加锁, 该函数不阻塞, 所以不会产生死锁.**

### 4.6读写锁

1. 什么是读写锁

   ​		读写锁也叫共享-独占锁。当读写锁以读模式锁住时，它是以共享模式锁住的；当它以写模式锁住时，它是以独占模式锁住的。**写独占、读共享。**

2. 读写锁使用场合

   ​		**读写锁非常适合于对数据结构读的次数远大于写的情况。**

3. 读写锁特性  

   ​		读写锁是“写模式加锁”时，解锁前，所有对该锁加锁的线程都会被阻塞。

   ​		读写锁是“读模式加锁”时，如果线程以读模式对其加锁会成功；如果线程以写模式加锁会阻塞。

   ​		读写锁是“读模式加锁”时， 既有试图以写模式加锁的线程，也有试图以读模式加锁的线程。那么读写锁会阻塞随后的读模式锁请求。优先满足写模式锁。读锁、写锁并行阻塞，写锁优先级高

4. 读写锁总结

   ​		**读并行，写独占，当读写同时等待锁的时候写的优先级高。**

5. 读写锁场景练习:

   ​	5.1  线程A加写锁成功, 线程B请求读锁
   ​			线程B阻塞, 
   ​			当线程A解锁之后, 线程B加锁成功
   ​	
   ​	5.2  线程A持有读锁, 线程B请求写锁
   ​			线程B会阻塞;
   ​			当线程A解锁之后, 线程B加锁成功
   ​	
   ​	5.3  线程A拥有读锁, 线程B请求读锁
   ​			线程B请求锁成功
   ​	

   ​	5.4 线程A持有读锁, 然后线程B请求写锁, 然后线程C请求读锁
   ​			线程B和C都阻塞;
   ​			当A释放锁之后, B先获得锁, C阻塞
   ​			当B释放锁之后, C获得锁	

   ​	5.5 线程A持有写锁, 然后线程B请求读锁, 然后线程C请求写锁
   ​			线程B和C都阻塞;
   ​			当线程A解锁之后, C先获得锁, B阻塞;
   ​			当C解锁之后, B获得锁

```c
读写锁主要操作函数
	定义一把读写锁
	pthread_rwlock_t rwlock;
	初始化读写锁
	int pthread_rwlock_init(                                
pthread_rwlock_t *restrict rwlock, 
const pthread_rwlockattr_t *restrict attr);
	函数参数
	rwlock-读写锁
	attr-读写锁属性，传NULL为默认属性
	销毁读写锁
int pthread_rwlock_destroy(pthread_rwlock_t *rwlock);        
	加读锁
int pthread_rwlock_rdlock(pthread_rwlock_t *rwlock);              
	尝试加读锁
int pthread_rwlock_tryrdlock(pthread_rwlock_t *rwlock);
	加写锁
int pthread_rwlock_wrlock(pthread_rwlock_t *rwlock);
	尝试加写锁
int pthread_rwlock_trywrlock(pthread_rwlock_t *rwlock);
	解锁
int pthread_rwlock_unlock(&pthread_rwlock_t *rwlock);

```

6. 读写锁使用步骤:
   	1 先定义一把读写锁:
   		pthread_rwlock_t rwlock;
   	2 初始化读写锁
   		pthread_rwlock_init(&rwlock, NULL);
   	3 加锁
   		pthread_rwlock_rdlock(&rwlock);---->加读锁
   		pthread_rwlock_wrlock(&rwlock);---->加写锁
   		////////////////////////
   		共享资源出现的位置
   		/////////////////////////
   	4 解锁
   		pthread_rwlock_unlock(&rwlock);
   	5 释放锁
   		pthread_rwlock_destroy(&rwlock);

```c
//读写锁测试程序
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <pthread.h>

int number = 0;

//定义一把读写锁
pthread_rwlock_t rwlock;

//写线程回调函数
void *thread_write(void *arg)
{
	int i = *(int *)arg;

	int cur;

	while(1)
	{
		//加写锁
		pthread_rwlock_wrlock(&rwlock);

		cur = number;
		cur++;
		number = cur;	
		printf("[%d]-W:[%d]\n", i, cur);

		//解锁
		pthread_rwlock_unlock(&rwlock);
		sleep(rand()%3);
	}
}

//读线程回调函数
void *thread_read(void *arg)
{
	int i = *(int *)arg;
	int cur;

	while(1)
	{
		//加读锁
		pthread_rwlock_rdlock(&rwlock);

		cur = number;
		printf("[%d]-R:[%d]\n", i, cur);

		//解锁
		pthread_rwlock_unlock(&rwlock);
		sleep(rand()%3);
	}	
}

int main()
{
	int n = 8;
	int i = 0;
	int arr[8];
	pthread_t thread[8];

	//读写锁初始化
	pthread_rwlock_init(&rwlock, NULL);

	//创建3个写子线程
	for(i=0; i<3; i++)
	{
		arr[i] = i;
		pthread_create(&thread[i], NULL, thread_write, &arr[i]);
	}

	//创建5个读子线程
	for(i=3; i<n; i++)
	{
		arr[i] = i;
		pthread_create(&thread[i], NULL, thread_read, &arr[i]);
	}

	//回收子线程
	int j = 0;
	for(j=0;j<n; j++)
	{
		pthread_join(thread[j], NULL);
	}

	//释放锁
	pthread_rwlock_destroy(&rwlock);

	return 0;
}

```

## 5.生产者和消费者模型

![image-20210805150157179](C:\Users\24846\AppData\Roaming\Typora\typora-user-images\image-20210805150157179.png)



## 6.条件变量

​		**条件本身不是锁！但它也可以造成线程阻塞。通常与互斥锁配合使用。给多线程提供一个会合的场所。**

​		**使用互斥量保护共享数据;**

​		**使用条件变量可以使线程阻塞, 等待某个条件的发生, 当条件满足的时候解除阻塞.**

条件变量的两个动作:

​		**条件不满足, 阻塞线程**

​		**条件满足, 通知阻塞的线程解除阻塞, 开始工作.**

### 6.1相关函数

```c
条件变量相关函数
	pthread_cond_t  cond;
	定义一个条件变量
    
	int pthread_cond_init(pthread_cond_t *restrict cond,
                   const pthread_condattr_t *restrict attr);
	函数描述:初始化条件变量
	函数参数: 
		cond: 条件变量
		attr: 条件变量属性, 通常传NULL
	函数返回值:成功返回0, 失败返回错误号
    
	int pthread_cond_destroy(pthread_cond_t *cond);
	函数描述: 销毁条件变量
	函数参数: 条件变量
	返回值: 成功返回0, 失败返回错误号
    
	int pthread_cond_wait(pthread_cond_t *restrict cond,pthread_mutex_t *restrict mutex);
	函数描述: 条件不满足, 引起线程阻塞并解锁;
          条件满足, 解除线程阻塞, 并加锁
	函数参数:
cond: 条件变量
mutex: 互斥锁变量
	函数返回值: 成功返回0, 失败返回错误号
    
	 int pthread_cond_signal(pthread_cond_t *cond);
	函数描述: 唤醒至少一个阻塞在该条件变量上的线程
	函数参数: 条件变量
	函数返回值: 成功返回0, 失败返回错误号

```

### 6.2 使用步骤

1.  定义条件变量

   **pthread_cont_t  cond（全局变量）;**

2. 初始化条件变量
		**pthread_cond_init(&cond, NULL);**
3. 在生成者线程中调用:
		**pthread_cond_signal(&cond);**
4. 在消费者线程中调用:
		**pthread_cond_wait(&cond, &mutex);**
5. 释放条件变量
		**pthread_cond_destroy(&cond);**

### 6.3多个生成者和多个消费者程序在执行的时候core掉的原因分析:

​		假若只有一个生产者生产了一个节点, 此时会调用pthread_cond_signal通知消费者线程, 此时若有多个消费者被唤醒了, 则最终只有消费者获得锁, 然后进行消费, 此时会将head置为NULL, 然后其余的几个消费者线程只会有一个线程获得锁, 然后读取head的内容就会core掉.

在使用条件变量的线程中, 能够引起线程的阻塞的地方有两个:
 	1. 在条件变量处引起阻塞---->这个阻塞会被pthread_cond_signal解除阻塞。
 	2. 互斥锁也会使线程引起阻塞----->其他线程解锁会使该线程解除阻塞。

```c
//使用条件变量实现生产者和消费者模型
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <pthread.h>
typedef struct node
{
	int data;
	struct node *next;
}NODE;

NODE *head = NULL;

//定义一把锁
pthread_mutex_t mutex;

//定义条件变量
pthread_cond_t cond;

//生产者线程
void *producer(void *arg)
{
	NODE *pNode = NULL;
	int n = *(int *)arg;
	while(1)
	{
		//生产一个节点
		pNode = (NODE *)malloc(sizeof(NODE));
		if(pNode==NULL)
		{
			perror("malloc error");
			exit(-1);
		}
		pNode->data = rand()%1000;
		printf("P[%d]:[%d]\n", n, pNode->data);

		//加锁
		pthread_mutex_lock(&mutex);

		pNode->next = head;
		head = pNode;

		//解锁
		pthread_mutex_unlock(&mutex);

		//通知消费者线程解除阻塞
		pthread_cond_signal(&cond);

		sleep(rand()%3);
	}
}

//消费者线程
void *consumer(void *arg)
{
	NODE *pNode = NULL;
	int n = *(int *)arg;
	while(1)
	{
		//加锁
		pthread_mutex_lock(&mutex);

		if(head==NULL)
		{
			//若条件不满足,需要阻塞等待
			//若条件不满足,则阻塞等待并解锁;
			//若条件满足(被生成者线程调用pthread_cond_signal函数通知),解除阻塞并加锁 
			pthread_cond_wait(&cond, &mutex);
		}

		if(head==NULL)
		{
			//解锁
			pthread_mutex_unlock(&mutex);	
			continue;
		}

		printf("C[%d]:[%d]\n", n, head->data);	
		pNode = head;
		head = head->next;

		//解锁
		pthread_mutex_unlock(&mutex);

		free(pNode);
		pNode = NULL;

		sleep(rand()%3);
	}
}

int main()
{
	int ret;
	int i = 0;
	pthread_t thread1[5];
	pthread_t thread2[5];

	//初始化互斥锁
	pthread_mutex_init(&mutex, NULL);

	//条件变量初始化
	pthread_cond_init(&cond, NULL);

	int arr[5];
	for(i=0; i<5; i++)
	{
		arr[i]= i;
		//创建生产者线程
		ret = pthread_create(&thread1[i], NULL, producer, &arr[i]);
		if(ret!=0)
		{
			printf("pthread_create error, [%s]\n", strerror(ret));
			return -1;
		}
        
		//创建消费者线程
		ret = pthread_create(&thread2[i], NULL, consumer, &arr[i]);
		if(ret!=0)
		{
			printf("pthread_create error, [%s]\n", strerror(ret));
			return -1;
		}
	}

	//等待线程结束
	for(i=0; i<5; i++)
	{
		pthread_join(thread1[i], NULL);
		pthread_join(thread2[i], NULL);
	}

	//释放互斥锁
	pthread_mutex_destroy(&mutex);

	//释放条件变量
	pthread_cond_destroy(&cond);

	return 0;
}

```

## 7.信号量

### 7.1 信号量介绍

​		信号量相当于多把锁, 可以理解为是加强版的互斥锁。

### 7.2 相关函数

```c
1.  定义信号量 sem_t sem;

2.  int sem_init(sem_t *sem, int pshared, unsigned int value);	
	函数描述: 初始化信号量
	函数参数:
sem: 信号量变量
pshared: 0表示线程同步, 1表示进程同步
value: 最多有几个线程操作共享数据
	函数返回值:成功返回0, 失败返回-1, 并设置errno值
    
3.  int sem_wait(sem_t *sem);
	函数描述: 调用该函数一次, 相当于sem--, 当sem为0的时候, 引起阻塞
	函数参数: 信号量变量
	函数返回值: 成功返回0, 失败返回-1, 并设置errno值
    
4.	int sem_post(sem_t *sem);
	函数描述: 调用一次, 相当于sem++
	函数参数: 信号量变量
	函数返回值: 成功返回0, 失败返回-1, 并设置errno值
    
5.	int sem_trywait(sem_t *sem);
	函数描述: 尝试加锁, 若失败直接返回, 不阻塞
	函数参数: 信号量变量
	函数返回值: 成功返回0, 失败返回-1, 并设置errno值
    
6.	int sem_destroy(sem_t *sem);
	函数描述: 销毁信号量
	函数参数: 信号量变量
	函数返回值: 成功返回0, 失败返回-1, 并设置errno值

```

### 7.3 使用步骤

1. 定义信号量变量
   		sem_t sem1;
      		sem_t sem2;

2. 初始化信号量
   		sem_init(&sem1, 0, 5);
      		sem_init(&sem2, 0, 5);	

3. 加锁
   		sem_wait(&sem1);
      		//共享资源
      		sem_post(&sem2);

   ​		sem_wait(&sem2);
   ​		//共享资源
   ​		sem_post(&sem1);

4. 释放资源
   	    sem_destroy(sem1);
            	    sem_destroy(sem2);

![信号量](E:\BaiduNetdiskDownload\传智C++32期就业班\08\LinuxSysDay09\linux系统编程\文档\信号量.png)

### 7.4使用案例

```c
//使用信号量实现生产者和消费者模型
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <unistd.h>
#include <pthread.h>
#include <semaphore.h>
typedef struct node
{
	int data;
	struct node *next;
}NODE;

NODE *head = NULL;

//定义信号量
sem_t sem_producer;
sem_t sem_consumer;

//生产者线程
void *producer(void *arg)
{
	NODE *pNode = NULL;
	while(1)
	{
		//生产一个节点
		pNode = (NODE *)malloc(sizeof(NODE));
		if(pNode==NULL)
		{
			perror("malloc error");
			exit(-1);
		}
		pNode->data = rand()%1000;
		printf("P:[%d]\n", pNode->data);

		//加锁
		sem_wait(&sem_producer); //--

		pNode->next = head;
		head = pNode;

		//解锁
		sem_post(&sem_consumer);  //相当于++

		sleep(rand()%3);
	}
}


//消费者线程
void *consumer(void *arg)
{
	NODE *pNode = NULL;
	while(1)
	{
		//加锁
		sem_wait(&sem_consumer); //相当于--
		
		printf("C:[%d]\n", head->data);	
		pNode = head;
		head = head->next;

		//解锁
		sem_post(&sem_producer); //相当于++

		free(pNode);
		pNode = NULL;

		sleep(rand()%3);
	}
}

int main()
{
	int ret;
	pthread_t thread1;
	pthread_t thread2;

	//初始化信号量
	sem_init(&sem_producer, 0, 5);
	sem_init(&sem_consumer, 0, 0);

	//创建生产者线程
	ret = pthread_create(&thread1, NULL, producer, NULL);
	if(ret!=0)
	{
		printf("pthread_create error, [%s]\n", strerror(ret));
		return -1;
	}

	//创建消费者线程
	ret = pthread_create(&thread2, NULL, consumer, NULL);
	if(ret!=0)
	{
		printf("pthread_create error, [%s]\n", strerror(ret));
		return -1;
	}

	//等待线程结束
	pthread_join(thread1, NULL);
	pthread_join(thread2, NULL);

	//释放信号量资源
	sem_destroy(&sem_producer);
	sem_destroy(&sem_consumer);

	return 0;
}
```

