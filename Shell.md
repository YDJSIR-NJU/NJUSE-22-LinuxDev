# Shell编程

## 基本概念

### 基本定义

Shell是用户与操作系统的接口，作为核外程序存在。具有双重角色：

1. Shell是一种命令解释程序：Linux的开发启动过程(进程树)、Shell的工作步骤：打印提示符；得到命令行；解析命令；查找文件；准备参数；执行命令。
2. Shell还是一种独立的程序设计语言解释器。

### Shell脚本的执行方式

1. 方法1：`sh script_file`，Shell新建进程运行本方法
2. 方法2：`chmod +x script_file`授予文件可执行权限；`./script_file`，Shell新建进程运行
3. 方法3：`source script_file`；`.script_file`，Shell使用本进程运行

### 不同类型的Shell

<img src="https://ydjsir-edu.oss-cn-shanghai.aliyuncs.com/SE3/pictures/image-20220505173641754.png" alt="image-20220505173641754" style="zoom: 50%;" />

还有现代化的`zsh`什么的，比如说快上`oh-my-zsh`的坑。

### Shell与虚拟终端

shell和虚拟终端是不同的，一台电脑只有一个console，但是可以划分很多虚拟终端，虚拟中断是从console模拟出来的。

1. `VT 1-6`文本模式登陆
2. `VT 7`图形模式登陆提示
3. 可以使用Alt-Fn在VT之间切换

## 基本命令

- `passwd`：修改密码
- `mkpasswd`：生成随机密码
- `date`：当天日期
- `cat`：展示一个日历
- `who`：查看用户名以及当前登录名
- `whoami`：查看用户名
- `finger`：当前用户的home目录
- `clear`：清空当前屏幕
- `echo`：写一个信息到屏幕；-n 不换行
- `write`：为已经登录的用户发送一个消息
- `wall`：群发消息
- `talk`：希望进行对象
- `mesg`：用来设置是否允许向当前终端写消息(Y/N)
- `cd`：切换文件夹
- `mkdir`：创建文件夹
  - `-m` 设置权限(-m 777)
  - `-p` 一次性创建多个路径
  - `-v` 每次创建新目录都显示信息
- `rmdir`：删除文件夹
- `ls`：查看信息：`-l` 查看所有信息；`-a` 查看包含隐藏文件；`-R` 递归的查看所有文件信息；格式为`文件类别、权限(3种)、链接数(硬链接数)、拥有者、拥有者组、大小、最后修改时间、名称`
- `touch`：更新权限或修改文件权限
  - `-a` 更改File变量指定的文件的访问时间，不更新修改时间
  - `-c` 如果文件不存在，则不要进行创建
  - `-f` 强制touch运行，不需要管理文件的读和写许可权
  - `-m` 更改FILE的修改时间，不要更改访问时间。
- `cp`：拷贝文件
- `mv`：移动或重命名文件
- `ln`：链接文件
- `rm`：删除文件
- `cat`：打印文件内容
- `more`：显示文件内容，向下滚动
- `less`：显示文件内容，上下滚动
- `ps`：报告所有的进程状态
- `pstree`：展示树状的程序们
- `jobs`：用于显示Linux中的任务列表及任务状态，包括后台运行的任务；显示任务号及其对应的进程号。
- `fg`：将一个进程移动到前台执行
- `bg`：将一个进程移动到后台执行
- `<ctrl-Z>`：停止某一个任务
- `kill`：杀死一个进程
- `nohup`：忽略挂起的信号
- `nice`：以更改过的优先级运行进程
- `renice`：重新指定一个或多个进程的优先级
- `top`：查看当前的CPU信息状态
- `mknod`: 在/dev下创建驱动的设备文件
- `mkfifo`：创建管道文件
- `chmod`：修改文件权限
  - `who`：u，g，o，a
  - `Operator`：+，-，==
  - `what`：r(4)，w(2)，x(1)
- `chown`：修改文件所有者
- `chgrp`：修改文件分组
- `find`：查找(find dir [-name] "*.c")
  1. options
    2. -depth 在查看目录本身之前，先搜索目录的内容
    3. -follow 跟随符号链接
    4. -maxdepths N 最多搜索N层目录
    5. -mount/-xdev 不搜索其他文件系统中的目录
  6. tests
    7. -name 根据名字查找
    8. -type 根据文件类型查找
    9. -user 根据用户名查找
    10. -ctime 根据最近更新时间查询
    11. -atime N 文件在N天之前被最后访问过
    12. -mtime N 文件在N天之前被最后修改过
    13. -newer other_file 文件比other_file新
- `ar`：用于建立或修改备存文件(创建静态链接库)，或是从备存文件中抽取文件。
- `tar`：
  1. 解压：tar -xzvf test.tar.gz  
  2. 压缩：tar -zcvf test.tar.gz ./test/
- `head`：查看文件头部，默认10行
- `tail`：查看文件尾部，默认10行
- `su`：切换用户名
- `uname`：打印当前Linux的版本信息
- `man`：查看命令说明书
- `grep`：在文件中搜索字符串

## 重定向

1. 标准输入、标准输出、标准错误
   1. 对应的文件描述符：0、1、2
   2. C语言变量：stdin、stdout、stderr
2. 符号
   1. `<`：输入重定向，改变命令的输入，后面指定输入内容(文件名)
   2. `>`：输出重定向，将前面输出的部分输入到后面的文件，并清空文件内容。
   3. `>!`：同上，强制覆盖。
   4. `<<`：追加输入重定向：后面跟字符串，用来表示输入结束
   5. `>>`：追加输出重定向：把前面输出的东西追加到后面的文件尾部，不清除文件的内容
   6. `2>`：错误重定向：将错误的信息输入到后面的文件中，会删除文件原有的内容
   7. `2>>`：错误追加重定向：将错误的信息追加到后面的文件中，不会删除文件原有的内容
3. 重定向操作在底层本质上就是调用了`dup2`函数，将进程的文件描述符进行拷贝和覆盖
4. 一般情况下，每个Unix/Linux命令运行时都会打开三个文件：
   1. 标准输入文件(stdin)：stdin的文件描述符为0，Unix程序默认从stdin读取数据。
   2. 标准输出文件(stdout)：stdout的文件描述符为1，Unix程序默认向stdout输出数据。
   3. 标准错误文件(stderr)：stderr的文件描述符为2，Unix程序会向stderr流中写入错误信息。
5. 如果希望将stdout和stderr合并后重定向到file，可以这样写：command > file 2 >& 1或command >> file 2>&1，为什么需要将标准错误重定向到标准输出的原因，那就归结为标准错误没有缓冲区，而stdout有。
6. 如果希望对stdin和stdout都重定向，可以这样写：command < file1 > file2；command命令将stdin重定向到file1，将stdout重定向到file2。

## 管道

1. 将一个进程的输出作为另一个进程的输入
2. 上一个操作的标注输出指的是上一个命令在一系列重定向操作之后的仍然会输出到stdout的文件描述符

## 环境变量

1. 变量包括 用户变量、环境变量、参数变量和内部变量
2. 操作环境的参数
3. 查看和设置环境变量
   1. `echo $file_parameters`
   2. `env` 查看所有环境变量
   3. `set` 查看所有环境变量
4. 例：PATH环境变量，对变量进行读操作添加`$`，而写操作不必
   1. `echo $PATH`
   2. `export PATH`：设置和显示环境变量
5. 具体变量
   1. `HOME` 用户登录目录
   2. `PATH` 问号分割的用来搜索命令的目录清单
   3. `PS1` 命令行提示符，通常是$字符
   4. `PS2` 辅助提示符，用来提示后续输入，通常是>字符
   5. `IFS` 输入区分隔符
   6. `UID` 当前用户的识别字，取值由数位构成的字串
   7. `PWD` 当前工作目录的绝对路径名
6. 参数变量和内部变量(未提)
   1. `$#` 传递到脚本程序的参数个数
   2. `$0` 脚本程序的名字
   3. `$1, 2...` 脚本程序的参数
   4. `$*` 全体参数组成的清单，使用IFS中的第一个字符分割
   5. `$@` `$*`的变体，不使用IFS中的第一个字符分割

## 正则表达式

1. `^`指向任何一行的开头
2. `$`指向一行的结尾
3. `.`任意单个字符
4. `[]`包括
5. `?`匹配0或1次
6. `*`匹配0次或多次
7. `+`匹配一次或多次
8. `{n}`匹配n次
9. `{n,}`匹配n次或n次以上
10. `{n,m}`匹配n次到m次

## Read、单引号、双引号、转义符

1. `Read`

   1. `read var`会等待从控制台获得输入放在`$var`中，要是直接read则会把输入的值放在`$REPLY`
      1. `-p "str"` 先输出提示符
      2. `-t 5` 5s内输入
      3. `-s` 不显示输入
      4. `-a` 将分裂后的字段依次存储到指定的数组中，存储的起始位置从数组的index=0开始。
      5. `-d` 指定读取行的结束符号。默认结束符号为换行符。
      6. `-n` 限制读取N个字符就自动结束读取，如果没有读满N个字符就按下回车或遇到换行符，则也会结束读取。
      7. `-N` 严格要求读满N个字符才自动结束读取，即使中途按下了回车或遇到了换行符也不结束。其中换行符或回车算一个字符。
      8. `-r` 禁止反斜线的转义功能。这意味着"\"会变成文本的一部分。

   

## 简单操作

### Tips

紧凑模式：使用;分割语句，比如说把then和if写在同一行，后面的循环把do不单独分行等。

单引号：所有字符都保持本身字符的意思，而不会被bash进行解释

双引号：除了`$`、`''`和`\`以外，双引号内的所有字符将保持字符本身的函数而不被bash解释(打印上面的特殊字符需要配合转义符号)

转义符：`$`、`''`和`\`

### 输入`Read`

`read var`会等待从控制台获得输入放在`$var`中，要是直接read则会把输入的值放在`$REPLY`

1. `-p "str"` 先输出提示符
2. `-t 5` 5s内输入
3. `-s` 不显示输入
4. `-a` 将分裂后的字段依次存储到指定的数组中，存储的起始位置从数组的index=0开始。
5. `-d` 指定读取行的结束符号。默认结束符号为换行符。
6. `-n` 限制读取N个字符就自动结束读取，如果没有读满N个字符就按下回车或遇到换行符，则也会结束读取。
7. `-N` 严格要求读满N个字符才自动结束读取，即使中途按下了回车或遇到了换行符也不结束。其中换行符或回车算一个字符。
8. `-r` 禁止反斜线的转义功能。这意味着"\"会变成文本的一部分。

### 输出`echo`

记得echo的内容最好恰当使用引号（单/双）包起来！

### 逻辑操作

1. `-e file` 文件存在则结果为真
2. `-d file` 文件是一个子目录则结果为真
3. `-f file` 文件是一个普通文件则结果为真
4. `-s file` 文件的长度不为零则结果为真
5. `-r file` 文件可读则结果为真
6. `-w file` 文件可写则结果为真
7. `-x file` 文件可执行则结果为真

1. `str1 = str2` 字符串相同则结果真
2. `str1 != str2` 字符串不先沟通则结果为真
3. `-z str` 字符串为空则结果为真
4. `-n str` 字符串不为空则结果为真

1. `! expr` 逻辑表达式求反
2. `expr1 -a expr2` 逻辑表达式 and
3. `expr1 -o expr2` 逻辑表达式 or

1. `expr1 –eq expr2` 两个表达式相等则结果为真
2. `expr1 –ne expr2` 两个表达式不等则结果为真
3. `expr1 –gt expr2` expr1大于expr2则结果为真
4. `expr1 –ge expr2` expr1大于或等于expr2则结果为真
5. `expr1 –lt expr2` expr1小于expr2则结果为真
6. `expr1 –le expr2 expr1` 小于或等于expr2则结果为真

### [[]] 与 (())

前者可以包起逻辑操作，并具有系列增强特性，是[]的增强版。

后者则强制把被包起来的部分看作是数字。例如。

```bash
var=$(( $var + 1 ))
```

## 分支与循环

### if语句

if语句的条件判断是使用test命令获得的[ expression ]，expression是前半个括号的参数

Shell中0表示为真。

```sh
if [ expression ]
then
   statements
elif [expression]
then
   statements
else
   statements
fi
```



### case语句

```sh
case str in
   str1 | str2) statements;;
   str3 | str4) statements;;
   *) statements;;
esac
```

### for语句

```sh
for var in list
do
   statements
done

for((i=1;i<5;i++))
do
   statements
done
```

### while语句

```sh
while condition
do
   statements
done
```

### until语句

```sh
until condition
do
   statements
done
```

### select语句

Select后面输入的是数字，对应item list

```sh
select item in itemlist
do
   statements
done
```

### 命令组合

1. 分号串联:`command1;command2;...`
2. 条件组合
   1. AND命令表:`statement1 && statement2 && ...`
   2. OR命令表:`statement1 || statement2 || ...`

### 函数：相当于另一个命令

```sh
# 函数要求理解即可，不必会写
func(){
   local var # 在函数内部定义的变量默认是全局变量
   statements
   return xxx
}
func para1 para2 # 函数内部调用$1 $2 得不到脚本参数，而是调用函数时后面的参数
```

### 杂项命令

- `break` 跳出循环
- `continue` 调到下一个循环继续
- `exit n` 以退出码n退出
- `return` 函数返回
- `export` 将变量导出到shell成为环境变量
- `set` 设置环境变量
- `unset` 删除环境变量
- `trap` 收到收到操作系统信号后执行的动作
- `:` 冒号命令：空命令
- `.` 句号命令或source：在当前shell中执行命令，而不新启动进程

### 捕获命令输出

语法

1. `$(command)`：执行command并将结果作为字符串放在这里
2. `command`

算术扩展：`$(($x+1))`

参数扩展：

1. `${param=default}`：如果param不存在，则返回default，并将它设置为default的值
2. `${param:=default}`：如果param不存在或值为空，做如上操作
3. `${#param}`：给出param的长度
4. `${var_name-default}`，如果var_name不存在，则返回default的值
5. `${var_name:-default}`，如果var_name不存在或者值为空，则返回default的值
6. `${var_name+default}`，如果var_name存在，则返回default的值
7. `${var_name:+default}`，如果var_name存在且不为空，则返回default的值
8. `${var_name?default}`，如果var_name不存在，则报错并输出default的值
9. `${var_name:?default}`，如果var_name不存在或者值为空，则报错并default的值
10. `${param%word}`：从param的尾部开始删除与word匹配的最小部分，然后返回剩余部分
11. `${param%%word}`：从param的尾部开始删除与word匹配的最长部分，然后返回剩余部分
12. `${param#word}`：从param的头部开始删除与word匹配的最小部分，然后返回剩余部分
13. `${param##word}`：从param的头部开始删除与word匹配的最长部分，然后返回剩余部分
14. `${var_name:x:y}`，提取var_name的值
          1. 如果x
             1. 是非负数：从左侧开始第x个字节开始
             2. 是负数：
          2. 如果y
             1. 是非负数：的连续y个字节的内容
             2. 是负数：到从右侧开始数y个字节的内容
15. `${var_name/regex/sub}`，替换var_name中的内容，语法和sed及vim的替换指令一样，只替换一次
16. `${var_name/regex/sub}`，替换var_name中的内容，语法和sed及vim的替换指令一样，替换全部