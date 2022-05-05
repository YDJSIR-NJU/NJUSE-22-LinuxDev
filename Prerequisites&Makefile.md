# Linux开发环境

## gcc

```bash
gcc [options] [filename]
```

gcc 链接(如果文件为.o，则自动只做链接，生成可执行文件) 或 编译+链接

1. `-E`：只对源程序进行预处理(调用cpp预处理器)
2. `-S`：只对源程序进行预处理、编译
3. `-c`：执行预处理、编译、汇编而不连接
4. `-o`：output_file：输出执行文件名
5. `-g`：产生调试工具所必须的符号信息
6. `-O/On`：在程序编译、连接过程中进行优化处理
7. `-Wall`：显示所有的警告信息
8. `-ldir`：指定额外的**头文件**搜索路径，dir是文件路径
9. `-Ldir`：指定额外的**库文件**搜索路径，dir是文件路径
10. `-lname`：链接时搜索指定的库文件
11. `-DMACRO[=DEFN]`：定义MACRO宏，比如`gcc -DAA=2`相当于源码中添加了`#define AA 2`

>  g++ C++对应命令

```sh
gcc –o hello hello.c ： 将hello.c文件编译成hello的可执行文件
gcc –c hello.c ： 将hello.c文件生成hello.o文件 gcc -E hello.c ： 只是激活预处理，不生成文档，需要把它重定向到另外一个文档里
gcc –S hello.c ： 将hello.c文件生成hello.s文件的汇编代码
gcc –pipe –o hello hello.c ： 使用管道代替编译中临时文档
gcc hello.c –include /root/hello.h ：包含某个代码，简单来说，就是以某个文档，需要另外一个文档的时候，就能够用它设定，功能就相当于在代码中使
```

## gdb

1. `file` 打开要调试的文件
2. `break/tbreak` 设置调试
3. `run` 执行当前调试的程序
4. `list` 列出源代码一部分
5. `next` 执行一步但不进入函数、简称n
6. `step` 执行一步进入函数、简称s
7. `display` 显示表达式的值
8. `print` 临时显示表达式的值、简称p
9. `kill` 中止调试
10. `quit` 退出

## 编译链接

### C的编译连接过程

#### Linux

![](https://ydjsir-edu.oss-cn-shanghai.aliyuncs.com/SE3/pictures/3.png)

1. 每一个源代码和目标文件是一一对应的
2. 链接器是将所有的目标文件进行连接
3. 打包后得到了.a静态库文件

#### Windows

![image-20220505222746794](https://ydjsir-edu.oss-cn-shanghai.aliyuncs.com/SE3/pictures/image-20220505222746794.png)



#### 其它语言

并不是每个语言都需要编译和链接，能拿Java和Python举例子就行。什么VC++、.Net不管。

### 静态库和动态库

#### 静态库

编译链接时，把库文件的代码全部加入可执行文件中，因此生成的文件比较大，但是运行时也就不需要库文件了，后缀名一般为`.a`

为什么需要静态库：通过静态库的方式降低复杂度，在升级更新时尽量做到增量更新，但是静态库会导致复用性降低，磁盘占用高。

#### 动态库

在编译链接时并没有把库文件的代码加入可执行文件中，而是在程序执行时由运行时链接文件加载库，这样可以节省系统的开销，后缀名一般为`.so`。

动态库的作用

1. 库文件不在可执行文件中，放置在外侧
2. 升级更新会方便快捷
3. 动态库会存在冲突(版本问题)

gcc/g++在编译时默认使用动态库。无论静态库还是动态库，都是由.o文件构成的。

#### 对比

二者的不同点在于代码被载入的时刻不同。

1. 静态库的代码在编译过程中已经被载入可执行程序，因此体积比较大。
2. 动态库(共享库)的代码在可执行程序运行时才载入内存，在编译过程中仅简单的引用，因此代码体积比较小。
3. 不同的应用程序如果调用相同的库，那么在内存中只需要有一份该动态库(共享库)的实例。
4. 静态库和动态库的最大区别：静态情况下，把库直接加载到程序中，而动态库链接的时候，它只是保留接口，将动态库与程序代码独立，这样就可以提高代码的可复用度，和降低程序的耦合度。

## Makefile

### 作用

1. 定义编译过程
2. 自动化编译流程

### 流程

1. Makefile描述模块间的依赖关系，会记录所有文件的信息，在make时决定链接时需要重新编译哪些
2. 如果找到，它会找文件中的第一个目标文件(target)
3. 如果文件不存在，或后依赖的.o文件的修改时间新于这个文件，那么则会执行后面所定义的命令来生成这个文件。

### make语法

命令根据Makefile对程序进行管理和维护

make命令格式:`make [-f Makefile][option][target]`

make判断被维护文件的时序关系

make install 很可能需要root权限

### 语法

#### 缩进

1. 顶格的为规则
2. 缩进的为命令

#### makefile语法

1. target是一个目标文件
2. prerequisites是生成target所需的文件或目标
3. command是make需要执行的命令
4. 默认回显：如果不想要回显则在命令前添加@
5. "%.o"表明要所有以".o"结尾的目标，即"foo.o bar.o"，就是变量$object集合的模式

```makefile
target: prerequisites
   command
```

3. 举例：依赖关系，如果后面的文件更新，则执行如下代码，若输出文件不存在是执行如下代码则为违反规则。

```makefile
hello : main.o kbd.o
  gcc -o hello main.o kbd.o
main.o : main.c defs.h
  cc -c main.c
kbd.o : kbd.c defs.h command.h
  cc -c kbd.c
clean :
  rm edit main.o kbd.o # 伪目标
```

### 伪目标

```makefile
clean:
   rm *.o hello
```

1. 伪目标并不是一个文件，只是一个标签，所以make无法生成器依赖关系和决定它是否要执行，只能显式的指明目标才能使其生效。
2. 伪目标取名不能和文件名重名：可以使用`.PHONY`标记来显示地指明一个目标是伪目标，向make说明，不管是否有这个文件，这个目标是伪目标
3. 伪目标一般无依赖文件，但是也可以执行
4. 伪目标可以作为默认目标，只要放到第一个

### 实例

```makefile
TOPDIR = ../
include $(TOPDIR)Rules.mak
EXTRA LIBS += :
EXEC = $(INSTALL_DIR)/hello
# make时会找到makefile文件，找到其中的第一个目标文件，如果不存在或旧的，则会执行后面命令来生成hello文件
OBJS = hello.o # make uninstall之后系统中源代码仍然存在
# 变量定义，makefile可以include别的makefile

all: $(EXEC) # 默认执行make all
  $(EXEC): $(OBJS)
  $(CC) $(LDFLAGS) -0 $@ $(OBJS) $(EXTRA_LIBS) # gcc的别名CC，$@明确了目标文件放置位置
install:
  $(EXP_INSTALL) $(EXEC) $(INSTALL_ DIR) # make install执行的指定目标
clean:
  -rm -f $(EXEC) *.elf*.gdb *.o
```

### 预定义变量

1. `$<` 第一个依赖文件的名称
2. `$?` 所有的依赖文件，以空格分开，这些依赖文件的修改日期比目标的创建日期晚
3. `$+` 所有的依赖文件，以空格分开，并以出现的先后为序，可能包含重复的依赖文件
4. `$^` 所有的依赖文件，以空格分开，不包含重复的依赖文件
5. `$*` 不包括扩展名的目标文件名称
6. `$@` 目标的完整名称
7. `$%` 如果目标是归档成员，则该变量表示目标的归档成员名称