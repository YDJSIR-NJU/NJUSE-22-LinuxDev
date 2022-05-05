# Linux 内核开发

## 基本概念

### 定义

操作系统是一系列程序的集合，其中最重要的部分构成了内核

内核是操作系统最基本的也是最重要的部分，为众多应用程序提供对电脑硬件的安全访问，提供操作系统最基本的功能。

### 单内核/微内核

1. 单内核是一个很大的进程，内部可以分为若干模块，运行时是一个独立的二进制文件，模块间通讯通过直接调用函数实现。
2. 微内核中大部分内核作为独立的进程在特权下运行，通过消息传递进行通信。

Linux内核的能力：内存管理，文件系统，进程管理，多线程支持，抢占式，多处理支持

### Linux内核的特点

Linux内核区别于其他UNIX商业内核的优点

1. 单内核，模块支持
2. 免费/开源
3. 支持多种CPU，硬件支持能力非常强大
4. Linux开发者都是非常出色的程序员.
5. 通过学习Linux内核的源码可以了解现代操作系统的实现原理

## 编译内核

> 这个好像不考。
>
> 可以尝试拿`OpenWRT`编译一次，下面的流程都能体验一次（肝疼）

### 获取源代码

1. https://www.kernel.org/
2. apt-get方式
   1. `apt-cache search linux-source` # 查看内核版本
   2. `apt-get install linux-source-3.2`
   3. 下载下来的位置一般在/usr/src
3. 从Ubuntu的源码库中获得内核源码
   1. `git clone git://kernel.ubuntu.com/ubuntu/ubuntuhardy.git`

### 后续操作

1. 解压：`tar jxvf /home/ldd/linux-3.2.tar.bz2`
2. 清除先前编译产生的目标文件：`make clean`
3. 配置内核：`make menuconfig`

### 内核组件选项

1. Y(*) 要集成该组件
2. N() 不需要该组件，以后会没有这项功能，以后你想加载模块都不行
3. M 以后再加该组件为一个外部模块

### 编译内核

```bash
make # 正常构建镜像
make zImage # 打包出这种形态的镜像
make bzImage # 打包出这种形态的镜像
make modules # 编译模块
```

### 启用新内核

1. make install(慎用)
   1. 将编译好的内核copy到/boot
2. 配置引导菜单（略）

### 初始化程序的建立

#### initrd

```
mkinitrd /boot/initrd.img $(uname -r)
```

#### initramfs

```bash
mkinitramfs -o /boot/initrd.img 2.6.24-16
update-initramfs -u
```

## 内核模块

### 加载和释放模块 `.ko`

1. 底层命令
   1. `insmod <module.ko> [module parameters]`：装载一个模块，只有超级用户才可以使用
   1. `rmmod`：卸载一个模块
2. 高层命令
   1. `modprobe` 类比用包管理器安装软件，对应于`insmod`
   2. `modprobe -r` 类比用包管理器移除软件，对应于`rmmod`

### 模块间依赖

理解模块之间的依赖关系(比如模块A需要引用模块B所导出的符号)：自动按需加载、卸载

1. `moddep`
2. `lsmod`：列举所有在内核中装载的模块，等价于cat /proc/modules
3. `modinfo`

### 最简单的内核模块例子

```c++
#include <linux/kernel.h>
#include <linux/module.h>
#include <linux/init.h>
static int __init hello_init(void)
{
   // __init标记 该函数只在初始化期间使用。模块装载后，将函数占用的空间释放
   printk(KERN_INFO "Hello world\n");
   return 0;
}
static void __exit hello_exit(void)
{
   // __exit标记 该代码近用于模块卸载
   printk(KERN_INFO "Goodbye world\n");
}
module_init(hello_init);
module_exit(hello_exit);
```

### 内核模块开发注意点

1. 不能使用C库开发驱动程序
2. 没有内存保护机制
3. 小内核栈
4. 需要有并发考虑

### 内核程序和用户态程序区别

| 用户态程序   | 内核态程序              |
| ------------ | ----------------------- |
| 用户空间运行 | 内核空间运行            |
| 入口是main() | 入口由module_init()指定 |
| 没有出口     | 出口由module_exit()指定 |
| 直接运行     | 程序由insmod命令载入    |
| gdb调试      | kdbug、kdb、kgdb等调试  |

### 内核模块操作

1. 导出符号表
   1. `EXPORT_SYMBOL(name)`
   2. `EXPORT_SYMBOL_GPL(name)`
2. Modules仅可以使用由Kernel或者其他Modules导出的符号，不能使用Libc
3. 显示所有导出符号:`cat /proc/kallsy`
4. 模块间参数传递
   1. 加载时传递:`insmod hello.ko test=2`
   2. 参数需要使用`module_param`宏声明:`module_param(变量名称，类型，访问许可掩码)`

## 驱动设备

### 设备分类

Linux系统将设备分为3种类型：

1. 字符设备 Character Driver->字符型文件
2. 块设备 Block Driver->块文件 
3. 网络接口设备 Network Driver->Socket

### 字符设备驱动程序的初始化加载过程（重点）

> 2022年考的就是这个，全段默写

#### 申请设备号：每一个字符设备或块设备都有一个主设备号或次设备号

1. 主设备号：表示一个特定的驱动程序
2. 次设备号：表示使用该驱动程序的各设备

```c
int register_chrdev_region(dev_t first, unsigned int count, char *name);
int alloc_chrdev_region(dev_t *dev, unsigned int firstminor, unsigned int count, char *name);
void unregister_chrdev_region(dev_t first, unsigned int count);
```

#### 定义文件操作结构体file_operations

```c
struct file_operations scull_fops = {
   .owner = THIS MODULE,
   .llseek = scull_llseek,
   .read = scull_read,
   .Write = scull_write,
   .ioctl = scull_ioctl,
   .open = scull_open,
   .release = scull_release,
};
```

#### 创建并初始化定义结构体`cdev`

1. `cdev`结构体描述字符设备
2. 该结构体是所有字符设备的抽象，其包含了大量字符设备所共有的特性。

```c
struct cdev *my_cdev = cdev_alloc();
my_cdev->ops = &my_fops;
void cdev_init(struct cdev *cdev, struct file_operations *fops);

struct scull_dev {
   struct scull qset *data; /* Pointer to first quantum set */
   int quantum; /* the current quantum size */
   int qset; /* the current array size */
   unsigned long size; /* amount of data stored here */
   unsigned int access_ key; /* used by sculluid and scullpriv */
   struct semaphore sem; /* mutual exclus ion semaphore */
   struct cdev cdev;/* Char device structure*/
};
```

#### 将`cdev`注册到系统，并和对应的设备号绑定

1. 将设备注册到系统中：`int cdev_add(struct cdev *dev, dev_t num, unsigned int count);`
2. 释放一个已经注册的设备：`void cdev_del(struct cdev *dev);`

#### 在/dev文件系统中用`mknod`创建设备文件，并将该文件绑定到设备号上

1. 设定设备号:`device=scull`
2. 定义主设备号:`major=15`
3. 用户可以通过访问`/dev/scull`来访问当前的驱动设备

```bash
mknod /dev/${device}0 c ${major} 0
```

