# Linux开发：文件相关

## VFS

### 定义

采用标准的Unix系统调用读写位于不同物理介质上的不同文件系统，使得open()等系统调用不用关心底层的存储介质和文件类型

VFS: Virtual File System Switch

| ![](https://ydjsir-edu.oss-cn-shanghai.aliyuncs.com/SE3/pictures/4.png) | ![](https://ydjsir-edu.oss-cn-shanghai.aliyuncs.com/SE3/pictures/5.png) |
| -------------------- | -------------------- |

### VFS在系统内建立的四种结构体和含义

1. 超级块(super block)：某一个磁盘的某一个分区的文件系统的信息，记录了文件系统类型和参数。
2. i-node 对象(i-node object)：记录真正的文件，文件存储在磁盘上时是按照索引号访问文件的，软链接是不同的文件，但是硬链接是相同的`inode`号，同一个文件。
3. 文件对象(`file object`)：记录了文件描述符、索引号，不对应真正的文件，文件打开会创建出文件对象，文件关闭才会释放内核中的文件对象。记录了文件的读写状态。
4. 目录对象(`dentry object`)：维护了目录中的逻辑关系，若要通过目录来查找文件，都需要这个对象。在路径上，无论是目录还是文件，都是一个`dentry`对象对应到目录包含的i-node上，目录项包括索引节点编号，目录项名称长度以及名称。

## 硬链接和软链接

### 硬链接

1. 不同的文件名对应同一个inode
2. 不能跨越文件系统
3. 对应系统调用link
4. ln -s [原文件名] [连接文件名]

### 软链接

1. 存储被链接文件的文件名(而不是inode)实现链接
2. 可以跨越文件系统
3. 对应系统调用symlink
4. ln [原文件名] [连接文件名]

## 系统调用汇总

![image-20220505225122334](https://ydjsir-edu.oss-cn-shanghai.aliyuncs.com/SE3/pictures/image-20220505225122334.png)

![image-20220505225136542](https://ydjsir-edu.oss-cn-shanghai.aliyuncs.com/SE3/pictures/image-20220505225136542.png)

![image-20220505225159381](https://ydjsir-edu.oss-cn-shanghai.aliyuncs.com/SE3/pictures/image-20220505225159381.png)

## C标准库汇总

![image-20220505225216682](https://ydjsir-edu.oss-cn-shanghai.aliyuncs.com/SE3/pictures/image-20220505225216682.png)