---
title: Linux命令备忘录
date: 2022-08-25 08:27:43
tags:
- Linux
- 备忘录
---
## 快捷键
### 常用
|按键|功能|
|-|-|
|[Tab]| 命令补全或文件补齐|
|[ctrl]-c |中断当前执行的程序|
|[ctrl]-d |键盘输入结束,即EOF,相当于输入exit|
|[ctrl]-l |清屏|
|[ctrl]-m |回车|
|[ctrl]-r |按字符串寻找历史命令|
### 编辑
|按键|功能|
|-|-|
|Ctrl A | 移动光标到命令行首(同home)|
|Ctrl E | 移动光标到命令行尾(同end)|
|Ctrl ] | 从当前光标往后搜索字符串，用于快速移动到该字符串|
|Ctrl Alt ] | 从当前光标往前搜索字符串，用于快速移动到该字符串|
|Ctrl K | 删除光标之后所有字符|
|Ctrl U | 清空当前行|
|Ctrl W | 删除光标前的单词(Word, 不包含空格的字符串)|
|Ctrl Y | 粘贴Ctrl W或Ctrl K删除的内容|
|Ctrl X Ctrl E | 调出系统默认编辑器编辑当前输入的命令，退出编辑器时，命令执行|
### 其他
|按键|功能|
|-|-|
|Ctrl Z |把当前进程放到后台（之后可用''fg''命令回到前台）|
|Shift Insert | 粘贴（相当于Windows的Ctrl V）|
|Ctrl PageUp |屏幕输出向上翻页|
|Ctrl PageDown | 屏幕输出向下翻页|
## 帮助与提示
### --help指令
### man page
> man是manual(操作说明)  
> 用法为`man [指令(手册页)]`
> man 手册页分为很多区块,具体分类可参见下表


 man 手册页通过名称和所属分类标识。有些不同分类的 man 手册页名字可能相同，比如 man(1) 和 man(7)，这时需要额外指明分类以访问需要的手册。例如: 
`$ man 5 passwd` 
会显示有关文件/etc/passwd，而非命令 `passwd`，的内容。

|代号 | 代表内容|
|:-:|:-|
|1 | 使用者在shell环境中可以操作的指令或可可执行文件|
|2 | 系统核心可调用的函数与工具等|
|3 | 一些常用的函数（function）与函数库（library），大部分为C的函数库（libc）|
|4 | 设备文件的说明，通常在/dev下的文件|
|5 | 配置文件或者是某些文件的格式|
|6 | 游戏（games）|
|7 | 惯例与协定等，例如Linux文件系统、网络协定、ASCII code等等的说明|
|8 | 系统管理员可用的管理指令|
|9 | 跟kernel有关的文件|

## 文件系统
### 用量与容量
- df:列出文件系统整体磁盘用量

```shell
[root@study ~]# df [-ahikHTm] [目录或文件名]
选项与参数: 
-a : 列出所有的文件系统，包括系统特有的 /proc 等文件系统；
-k : 以 KBytes 的容量显示各文件系统；
-m : 以 MBytes 的容量显示各文件系统；
-h : 以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；(常用)
-H : 以 M=1000K 取代 M=1024K 的进位方式；
-T : 连同该 partition 的 filesystem 名称 （例如 xfs） 也列出；
-i : 不用磁盘容量，而以 inode 的数量来显示
```
- du:评估文件系统磁盘用量

```shell
[root@study ~]# du [-ahskm] 文件或目录名称
选项与参数: 
-a : 列出所有的文件与目录容量，因为默认仅统计目录下面的文件量而已。
-h : 以人们较易读的容量格式 （G/M） 显示；(常用)
-s : 列出总量而已，而不列出每个各别的目录占用容量；
-S : 不包括子目录下的总计，与 -s 有点差别。
-k : 以 KBytes 列出容量显示；
-m : 以 MBytes 列出容量显示；
```

### 链接:ln
```shell
[root@study ~]# ln [-sf] 来源文件 目标文件
选项与参数: 
-s : 如果不加任何参数就进行链接，那就是hard link，至于 -s 就是symbolic link
-f : 如果 目标文件 存在时，就主动的将目标文件直接移除后再创建！
```

### 磁盘分区、格式化、检验、挂载（暂缓）
### 压缩、打包、备份
#### 压缩
- gzip,zcat/zmore/zless/zgrep

```shell
[dmtsai@study ~]$ gzip [-cdtv#] 文件名
[dmtsai@study ~]$ zcat 文件名.gz
选项与参数: 
-c : 将压缩的数据输出到屏幕上，可通过数据流重导向来处理；
-d : 解压缩的参数；
-t : 检验压缩文件的一致性
-v : 显示原文件/压缩文件的压缩比等信息；
-# : \# 为数字的意思，代表压缩等级，-1 最快，但是压缩比最差 -9 最慢，但是压缩比最好 默认是 -6
```

- bzip2,bzcat/bzmore/bzless/bzgrep

```shell
[dmtsai@study ~]$ bzip2 [-cdkzv#] 文件名
[dmtsai@study ~]$ bzcat 文件名.bz2
选项与参数: 
-c : 将数据输出到屏幕上
-d : 解压缩的参数
-k : 保留原始文件，而不会删除原始的文件喔！
-z : 压缩的参数 （默认值，可以不加）
-v : 可以显示出原文件/压缩文件的压缩比等信息；
-# : 与 gzip 同样的，都是在计算压缩比的参数， -9 最佳， -1 最快！
```
- xz,xzcat/xzmore/xzless/xzgrep

```shell
[dmtsai@study ~]$ xz [-dtlkc#] 文件名
[dmtsai@study ~]$ xcat 文件名.xz
选项与参数: 
-c : 将数据由屏幕上输出
-d : 解压缩
-k : 保留原本的文件不删除
-t : 测试压缩文件的完整性，看有没有错误
-l : 列出压缩文件的相关信息
-# : 同gzip
```

#### 打包:tar
```shell
# 打包与压缩
[dmtsai@study ~]$ tar [-z|-j|-J] [cv] [-f 待创建的新文件名] filename... 
# 察看文件名
[dmtsai@study ~]$ tar [-z|-j|-J] [tv] [-f 既有的 tar文件名] 
# 解压缩
[dmtsai@study ~]$ tar [-z|-j|-J] [xv] [-f 既有的 tar文件名] [-C 目录] 
选项与参数: 
-c : 创建打包文件，可搭配 -v 来察看过程中被打包的文件名（filename）
-t : 察看打包文件的内容含有哪些文件名，重点在察看“文件名”就是了；
-x : 解打包或解压缩的功能，可以搭配 -C （大写） 在特定目录解压,特别留意的是， -c, -t, -x 不可同时出现在一串命令行中。
-z : 通过 gzip 的支持进行压缩/解压缩: 此时文件名最好为 *.tar.gz
-j : 通过 bzip2 的支持进行压缩/解压缩: 此时文件名最好为 *.tar.bz2
-J : 通过 xz 的支持进行压缩/解压缩: 此时文件名最好为 *.tar.xz,特别留意， -z, -j, -J 不可以同时出现在一串命令行中
-v : 在压缩/解压缩的过程中，将正在处理的文件名显示出来！
-f filename: -f 后面要立刻接要被处理的文件名！建议 -f 单独写一个选项啰！（比较不会忘记）
-C 目录 : 这个选项用在解压缩，若要在特定目录解压缩，可以使用这个选项。其他后续练习会使用到的选项介绍: 
-p（小写） : 保留备份数据的原本权限与属性，常用于备份（-c）重要的配置文件
-P（大写） : 保留绝对路径，亦即允许备份数据中含有根目录存在之意；
--exclude=FILE: 在压缩的过程中，不要将 FILE 打包！
```
记法:
- 压缩:`tar -jcv -f filename.tar.bz2 要被压缩的文件或目录名称`
- 解压:`tar -jxv -f filename.tar.bz2 -C 欲解压缩的目录`
- 查询:`tar -jtv -f filename.tar.bz2`
- z:gzip
- j:bzip2
- J:xz


解压单独文件方法:
1.先查询:
`tar -jtv -f filename.tar.bz2 | grep '单独文件名'`
2.单独解开:
`tar -jxv -f filename.tar.bz2 单独文件名`

#### 备份
- xfs 文件系统的备份与恢复
```shell
# 备份
[root@study ~]# xfsdump [-L S_label] [-M M_label] [-l #] [-f 备份文件] 待备份数据
[root@study ~]# xfsdump -I
选项与参数: 
-L : xfsdump 会纪录每次备份的 session 标头，这里可以填写针对此文件系统的简易说明
-M : xfsdump 可以纪录储存媒体的标头，这里可以填写此媒体的简易说明
-l : 是 L 的小写，就是指定等级～有 0~9 共 10 个等级喔！ （默认为 0，即完整备份）
-f : 类似 tar 后面接产生的文件，亦可接例如 /dev/st0 设备文件名或其他一般文件文件名等
-I : 从 /var/lib/xfsdump/inventory 列出目前备份的信息状态
# 还原
[root@study ~]# xfsrestore -I <==用来察看备份文件数据
[root@study ~]# xfsrestore [-f 备份文件] [-L S_label] [-s] 待复原目录 <==单一文件全系统复原
[root@study ~]# xfsrestore [-f 备份文件] -r 待复原目录 <==通过累积备份文件来复原系统
[root@study ~]# xfsrestore [-f 备份文件] -i 待复原目录 <==进入互动模式
选项与参数: 
-I : 跟 xfsdump 相同的输出！可查询备份数据，包括 Label 名称与备份时间等
-f : 后面接的就是备份文件！企业界很有可能会接 /dev/st0 等磁带机！我们这里接文件名！
-L : 就是 Session 的 Label name 喔！可用 -I 查询到的数据，在这个选项后输入！
-s : 需要接某特定目录，亦即仅复原某一个文件或目录之意！
-r : 如果是用文件来储存备份数据，那这个就不需要使用。如果是一个磁带内有多个文件，
需要这东西来达成累积复原
-i : 进入互动模式，进阶管理员使用的！一般我们不太需要操作它！
```
- 其他常见压缩和备份工具
```shell
# dd
[root@study ~]# dd if="input_file" of="output_file" bs="block_size" count="number"
选项与参数: 
if : input file 也可以是设备
of : output file 也可以是设备
bs : 规划的一个 block 的大小，若未指定则默认是 512 Bytes（一个 sector 的大小）
count: 多少个 bs 的意思。
# cpio
[root@study ~]# cpio -ovcB > [file|device] <==备份
[root@study ~]# cpio -ivcdu < [file|device] <==还原
[root@study ~]# cpio -ivct < [file|device] <==察看
备份会使用到的选项与参数: 
-o : 将数据 copy 输出到文件或设备上
-B : 让默认的 Blocks 可以增加至 5120 Bytes ，默认是 512 Bytes 这样的好处是可以让大文件的储存速度加快（参考 inodes 概念）还原会使用到的选项与参数: 
-i : 将数据自文件或设备 copy 到系统中
-d : 自动创建目录 使用 cpio 所备份的数据内容不见得会在同一层目录中，因此需要让 cpio 在还原时可以创建新目录
-u : 自动的将较新的文件覆盖较旧的文件
-t : 需配合 -i 选项，可用在"查看"以 cpio 创建的文件或设备的内容一些可共享的选项与参数: 
-v : 让储存的过程中文件名称可以在屏幕上显示
-c : 一种较新的 portable format 方式储存
```

## 文件与目录
### ls
    [root@study ~]# ls [-aAdfFhilnrRSt] filepath..
    [root@study ~]# ls [--color={never,auto,always}] 文件名或目录名称..
    [root@study ~]# ls [--full-time] 文件名或目录名称..
    选项与参数: 
    -a : 全部的文件，连同隐藏文件（ 开头为 . 的文件） 一起列出来（常用）
    ----------------------------------------------------------------------
    -A : 全部的文件，连同隐藏文件，但不包括 . 与 .. 这两个目录
    -d : 仅列出目录本身，而不是列出目录内的文件数据（常用）
    ----------------------------------------------------------------------
    -f : 直接列出结果，而不进行排序 （ls 默认会以文件名排序！）
    -F : 根据文件、目录等信息，给予附加数据结构，例如: 
    *:代表可可执行文件； /:代表目录； =:代表 socket 文件； |:代表 FIFO 文件；
    -h : 将文件大小以人类较易读的方式（例如 GB, KB 等等）列出来；
    -i : 列出 inode 号码，inode 的意义下一章将会介绍；
    -l : 长数据串行出，包含文件的属性与权限等等数据；（常用）
    ----------------------------------------------------------------------
    -n : 列出 UID 与 GID 而非使用者与群组的名称 （UID与GID会在帐号管理提到！）
    -r : 将排序结果反向输出，例如: 原本文件名由小到大，反向则为由大到小；
    -R : 连同子目录内容一起列出来，等于该目录下的所有文件都会显示出来；
    -S : 以文件大小大小排序，而不是用文件名排序；
    -t : 依时间排序，而不是用文件名。
    --color=never : 不要依据文件特性给予颜色显示；
    --color=always : 显示颜色
    --color=auto : 让系统自行依据设置来判断是否给予颜色
    --full-time : 以完整时间模式 （包含年、月、日、时、分） 输出
    --time={atime,ctime} : 输出 access 时间或改变权限属性时间 （ctime）
    而非内容变更时间 （modification time）


### cp rm mv
- cp

> 在复制时清楚4点  
> - 是否完整保留源文件信息
> - 源文件是否为链接文件
> - 源文件是否为特殊文件(FIFO socket)
> - 源文件是否为目录

```shell
[root@study ~]# cp [-adfilprsu] 来源文件（source） 目标文件（destination）
[root@study ~]# cp [options] source1 source2 source3 .... directory
例:若 ~/.bashrc 比 /tmp/bashrc 新才复制过来
[root@study tmp]# cp -u ~/.bashrc /tmp/bashrc

选项与参数: 
-a : 相当于 -dr --preserve=all 的意思，至于 dr 请参考下列说明；（常用）
----------------------------------------------------------------------
-d : 若来源文件为链接文件的属性（link file），则复制链接文件属性而非文件本身；
-f : 为强制（force）的意思，若目标文件已经存在且无法打开，则移除后再尝试一次；
-i : 若目标文件（destination）已经存在时，在覆盖时会先询问动作的进行（常用）
----------------------------------------------------------------------
-l : 进行硬式链接（hard link）的链接文件创建，而非复制文件本身；
-p : 连同文件的属性（权限、用户、时间）一起复制过去，而非使用默认属性（备份常用）；
-r : 递回持续复制，用于目录的复制行为；（常用）
----------------------------------------------------------------------
-s : 复制成为符号链接文件 （symbolic link），亦即“捷径”文件；
-u : destination 比 source 旧才更新 destination，或 destination 不存在的情况下才复制。
--preserve=all : 除了 -p 的权限相关参数外，还加入 SELinux 的属性, links, xattr 等也复制了。
最后需要注意的，如果来源文件有两个以上，则最后一个目的文件一定要是“目录”才行！
```


- rm

```shell
[root@study ~]# rm [-fir] 文件或目录
选项与参数: 
-f : 就是 force 的意思，忽略不存在的文件，不会出现警告讯息；
-i : 互动模式，在删除前会询问使用者是否动作
-r : 递回删除啊！最常用在目录的删除了！这是非常危险的选项！！！
```  
- mv

```shell
[root@study ~]# mv [-fiu] source destination
[root@study ~]# mv [options] source1 source2 source3 .... directory
选项与参数: 
-f : force 强制的意思，如果目标文件已经存在，不会询问而直接覆盖；
-i : 若目标文件 （destination） 已经存在时，就会询问是否覆盖！
-u : 若目标文件已经存在，且 source 比较新，才会更新 （update）
```


### 文件内容查看
#### 直接查看
- cat(第一行到最后一行连续显示在屏幕上)

```shell
[root@study ~]# cat [-AbEnTv]
选项与参数: 
-A : 相当于 -vET 的整合选项，可列出一些特殊字符而不是空白而已；
-b : 列出行号，仅针对非空白行做行号显示，空白行不标行号！
-E : 将结尾的断行字符 $ 显示出来；
-n : 打印出行号，连同空白行也会有行号，与 -b 的选项不同；
-T : 将 [tab] 按键以 ^I 显示出来；
-v : 列出一些看不出来的特殊字符
```
- tac(cat倒过来)
- nl(添加行号打印)

```shell
[root@study ~]# nl [-bnw] 文件
选项与参数: 
-b : 指定行号指定的方式，主要有两种: 
    -b a : 表示不论是否为空行，也同样列出行号（类似 cat -n）；
    -b t : 如果有空行，空的那一行不要列出行号（默认值）；
-n : 列出行号表示的方法，主要有三种: 
    -n ln : 行号在屏幕的最左方显示；
    -n rn : 行号在自己字段的最右方显示，且不加 0 ；
    -n rz : 行号在自己字段的最右方显示，且加 0 ；
-w : 行号字段的占用的字符数。
```

#### 分页查看
- more

|按键|功能|
|:-|:-|
|空格|代表向下翻一页；|
|Enter |代表向下翻“一行”|
|/string |代表在这个显示的内容当中，向下查找string这个关键字|
|:f |立刻显示出文件名以及目前显示的行数|
|q |代表立刻离开 more ，不再显示该文件内容|
|b 或 [ctrl]-b |代表往回翻页，不过这动作只对文件有用，对管线无用|
- less

|按键|功能|
|:-|:-|
|空格|代表向下翻一页；|
|[pagedown]|向下翻动一页|
|[pageup]|向上翻动一页|
|/string |代表在这个显示的内容当中，向下查找string这个关键字|
|?string |代表在这个显示的内容当中，向上查找string这个关键字|
|n |重复前一个查找 （与 / 或 ? 有关！）|
|N |反向地重复前一个查找 （与 / 或 ? 有关！|
|g |前进到这个数据的第一行去|
|G |前进到这个数据的最后一行去 （注意大小写）|
|q |离开|
#### 数据撷取
- head(从头取出几行)

```shell
[root@study ~]# head [-n number] 文件
选项与参数: 
-n : 后面接数字，代表显示几行的意思
```
- tail(从最后取出几行) 

```shell
[root@study ~]# tail [-n number] 文件
选项与参数: 
-n : 后面接数字，代表显示几行的意思
-f : 表示持续监测后面所接的文件名，要等到按下[ctrl]-c才会结束tail的监测
```

#### 非纯文本:od
```shell
[root@study ~]# od [-t TYPE] 文件
选项或参数: 
-t : 后面可以接各种“类型 （TYPE）”的输出，例如: 
    a : 利用默认的字符来输出；
    c : 使用 ASCII 字符来输出
    d[size] : 利用十进制（decimal）来输出数据，每个整数占用 size Bytes ；
    f[size] : 利用浮点数值（floating）来输出数据，每个数占用 size Bytes ；
    o[size] : 利用八进位（octal）来输出数据，每个整数占用 size Bytes ；
    x[size] : 利用十六进制（hexadecimal）来输出数据，每个整数占用 size Bytes ；
```

#### 修改文件时间或创建文件:touch
>- modification time （mtime）:  文件的“内容数据”变更的时间，内容数据指的是文件的内容，而不是文件的属性或权限！
>- status time （ctime）:  文件的“状态 （status）”改变的时间.比如权限与属性.
>- access time （atime）:  当“该文件的内容被取用”时，就会更新这个读取时间（access）。比如使用 cat 去读取 /etc/man_db.conf .

```shell
[root@study ~]# touch [-acdmt] 文件
选项与参数: 
-a : 仅修订 access time；
-c : 仅修改文件的时间，若该文件不存在则不创建新文件；
-d : 后面可以接欲修订的日期而不用目前的日期，也可以使用 --date="日期或时间"
-m : 仅修改 mtime ；
-t : 后面可以接欲修订的时间而不用目前的时间，格式为[YYYYMMDDhhmm]

# 如果 touch 不加参数后接文件，
# 则该文件的三个时间 （atime/ctime/mtime） 都会更新为目前的时间
# 若该文件不存在，
# 则会主动的创建一个新的空的文件
```

### 文件默认权限:umask
> umask 管理一个文件或目录创建时的权限默认值

```shell
[root@study ~]# umask
0022  这是root的权限默认值,一般用户是0002
[root@study ~]# umask -S
u=rwx,g=rx,o=rx
```

### 文件隐藏属性
- chattr 设置文件隐藏属性

```shell
[root@study ~]# chattr [+-=][ASacdistu] 文件或目录名称
选项与参数: 
+ : 增加某一个特殊参数，其他原本存在参数则不动。
- : 移除某一个特殊参数，其他原本存在参数则不动。
= : 设置一定，且仅有后面接的参数
A : 当设置了 A 这个属性时，若你有存取此文件（或目录）时，他的存取时间 atime 将不会被修改，
可避免 I/O 较慢的机器过度的存取磁盘。（目前建议使用文件系统挂载参数处理这个项目）
S : 一般文件是非同步写入磁盘的（原理请参考[前一章sync](../Text/index.html#sync)的说明），如果加上 S 这个属性时，
当你进行任何文件的修改，该更动会“同步”写入磁盘中。
a : 当设置 a 之后，这个文件将只能增加数据，而不能删除也不能修改数据，只有root 才能设置这属性
c : 这个属性设置之后，将会自动的将此文件“压缩”，在读取的时候将会自动解压缩，
但是在储存的时候，将会先进行压缩后再储存（看来对于大文件似乎蛮有用的！）
d : 当 dump 程序被执行的时候，设置 d 属性将可使该文件（或目录）不会被 dump 备份
i : 这个 i 可就很厉害了！他可以让一个文件“不能被删除、改名、设置链接也无法写入或新增数据！”
对于系统安全性有相当大的助益！只有 root 能设置此属性
s : 当文件设置了 s 属性时，如果这个文件被删除，他将会被完全的移除出这个硬盘空间，
所以如果误删了，完全无法救回来了喔！
u : 与 s 相反的，当使用 u 来设置文件时，如果该文件被删除了，则数据内容其实还存在磁盘中，
可以使用来救援该文件喔！
注意1: 属性设置常见的是 a 与 i 的设置值，而且很多设置值必须要身为 root 才能设置
注意2: xfs 文件系统仅支持 AadiS 而已
主要使用 +i和+a
```
- lsattr 显示文件隐藏属性

```shell
[root@study ~]# lsattr [-adR] 文件或目录
选项与参数: 
-a : 将隐藏文件的属性也秀出来；
-d : 如果接的是目录，仅列出目录本身的属性而非目录内的文件名；
-R : 连同子目录的数据也一并列出来！
```
### 观察文件类型:file
### 命令与文件查找
#### 命令查找:which(寻找二进制文件)
```shell
[root@study ~]# which [-a] command
选项或参数: 
-a : 将所有由 PATH 目录中可以找到的指令均列出，而不止第一个被找到的指令名称
```
#### 文件与文件名查找
- whereis(在特定目录中寻找文件名)

```shell
[root@study ~]# whereis [-bmsu] 文件或目录名
选项与参数: 
-l :可以列出 whereis 会去查询的几个主要目录而已
-b :只找 binary 格式的文件
-m :只找在说明文档 manual 路径下的文件
-s :只找 source 来源文件
-u :查找不在上述三个项目当中的其他特殊文件
```
- locate/updatedb

```shell
[root@study ~]# locate [-ir] keyword
选项与参数: 
-i : 忽略大小写的差异；
-c : 不输出文件名，仅计算找到的文件数量
-l : 仅输出几行的意思，例如输出五行则是 -l 5
-S : 输出 locate 所使用的数据库文件的相关信息，包括该数据库纪录的文件/目录数量等
-r : 后面可接正则表达式的显示方式
updatedb 用来更新locate的数据库
```
- find

```shell
[root@study ~]# find [PATH] [option] [action]
选项与参数: 
1.  与时间有关的选项: 共有 -atime, -ctime 与 -mtime ，以 -mtime 为例,参见下图
    -mtime n : n 为数字，意义为在 n 天之前的“一天之内”被更动过内容的文件；
    -mtime +n : 列出在 n 天之前（不含 n 天本身）被更动过内容的文件文件名；
    -mtime -n : 列出在 n 天之内（含 n 天本身）被更动过内容的文件文件名。
    -newer file : file 为一个存在的文件，列出比 file 还要新的文件文件名
2.  与使用者或群组名称有关的参数: 
    -uid n : n 为数字，这个数字是使用者的帐号 ID，亦即 UID 
    -gid n : n 为数字，这个数字是群组名称的 ID，亦即 GID
    -user name : name 为使用者帐号名称
    -group name: name 为用户组名称
    -nouser : 寻找文件的拥有者不存在于 /etc/passwd 的文件
    -nogroup : 寻找文件的拥有组不存在于 /etc/group 的文件.自行安装软件时，很可能该软件的属性当中并没有文件拥有者，此时可以使用 -nouser 与 -nogroup 查找。
3.  与文件权限及名称有关的参数: 
    -name filename: 查找文件名称为 filename 的文件；
    -size [+-]SIZE: 查找比 SIZE 还要大（+）或小（-）的文件。这个 SIZE 的规格有: c: 代表 Byte， k: 代表 1024Bytes。所以，要找比 50KB 还要大的文件，就是“ -size +50k ”
    -type TYPE : 查找文件的类型为 TYPE 的，类型主要有: 一般正规文件 （f）, 设备文件 （b, c）, 目录 （d）, 链接文件 （l）, socket （s）, 及 FIFO （p） 等属性。
    -perm mode : 查找文件权限“刚好等于” mode 的文件，这个 mode 为类似 chmod的属性值，举例来说， -rwsr-xr-x 的属性为 4755 ！
    -perm -mode : 查找“至少符合每一位(rwx) mode 的权限”的文件，比如查找 -rwxr--r-- ，亦即 0744 的文件，使用 -perm -0744，那么文件权限为 4755 时，也会被列出来，因为 -rwsr-xr-x 的属性已经囊括了 -rwxr--r-- 的属性了。
    -perm /mode : 查找文件权限“符合任一(rwx中任一位符合 mode 皆可) mode 的权限”的文件，比如查找-rwxr-xr-x ，亦即 -perm /755 时，但一个文件属性为 -rw-------也会被列出来，因为他有 -rw.... 的属性存在
4.  额外可进行的动作: 
    -exec command : command 为其他指令，-exec 后面可再接额外的指令来处理查找到的结果。
    -print : 将结果打印到屏幕上，这个动作是默认动作！
```
![](https://i.imgur.com/IirSlhO.png)

## vim&vi
> vi 三种模式:
> - 命令模式 （command mode）
> - 编辑模式 （insert mode）
> - 命令行命令模式 （command-line mode）
### 按键说明
- 命令模式

|按键| 功能|
|-|-|
|**移动光标**|
|h 或（←） |光标向左移动一个字符|
|j 或（↓） |光标向下移动一个字符|
|k 或（↑） |光标向上移动一个字符|
|l 或（→） |光标向右移动一个字符|
|***[Ctrl] + f*** |屏幕向下移动一页，相当于 [Page Down]（常用）|
|***[Ctrl] + b*** |屏幕向上移动一页，相当于 [Page Up]（常用）|
|[Ctrl] + d |屏幕向下移动半页|
|[Ctrl] + u |屏幕向上移动半页|
|+ |到非空白字符的下一行|
|- |到非空白字符的上一行|
|n + [space]|在本行向后移动 n 个字符。|
|***0 或[Home]***| 到行头（常用）|
|***$ 或[End]***| 到行尾（常用）|
|^| 到本行第一个非`blank`字符处 所谓`blank`字符,就是空格，tab，换行，回车等|
|g_| 到本行最后一个非`blank`字符处|
|H |到屏幕最上方那行的第一个字符|
|M |到屏幕中央那行的第一个字符|
|L |到屏幕最下方那行的第一个字符|
|***G*** |到最后一行（常用）|
|***nG*** |到第 n 行（可配合:set nu）|
|***gg*** |到第一行（常用）|
|***n+ [Enter]***| n 为数字。光标向下移动 n 列（常用）|
|w 或 W | 到下一个单词的开头。|
|e 或 E | 到下一个单词的结尾。|
|% | 匹配括号移动(光标必须在括号上)|
|* 或 #|  匹配光标当前所在的单词|
|***fa*** | 到下一个为a的字符处，a可以是其他字符|
|***t,*** | 到,前的第一个字符。,可以变成其它字符|
|F 或 T| 与 f 和 t 方向相反(上一个)|
|**查找与替换**|
|/word |向下查找 （常用）
|?word |向上查找|
| n | 重复上一次查找的动作(无论向上还是向下)|
| N |与 n 作用相反|
|***:n1,n2s/word1/word2/g*** |在 n1 与 n2 行间查找 word1 , 并将其替换为 word2（常用）|
|***:1,$s/word1/word2/g*** |从第一行到最后一行,查找 word1 , 并将其替换为 word2 ,也可用 ***:%s/word1/word2/g***（常用）|
|***:1,$s/word1/word2/gc*** |从第一行到最后一行,查找 word1 ,并将其替换为 word2 ,且显示提示字符给使用者确认 （confirm） 是否需要替换（常用）
|**删除、复制与粘贴**|
|x, X |x 为向后删除一个字符 （相当于 [del] 按键）， X 为向前删除一个字符（相当于 [backspace] 亦即是退格键） （常用）|
|nx |连续向后删除 n 个字符|
|dd |删除当前行（常用）|
|ndd |向下删除 n 行（常用）|
|d1G |删除本行到第一行所有数据|
|dG |删除本行到最后一行的所有数据|
|d$ |删除光标到本行的最后一个字符|
|d0 |删除光标到本行的第一个字符|
|yy |复制光标所在的那一行（常用）|
|nyy |向下复制 n 行（常用）|
|y1G |复制本行到第一行的数据|
|yG |复制本行到最后一行的数据|
|y0 |复制光标到行头的数据|
|y$ |复制光标到行尾的数据|
|Y  |复制整行数据|
|p, P| p 将已复制的数据向后粘贴，P 则为向前粘贴（常用）通常复制的数据超过一行时,会从下(上)一行开始粘贴|
|J |将本行与下一行合为一行|
|c |重复删除多个数据，例如向下删除 10 行，[10cj ]|
|u |撤销（常用）|
|[Ctrl]+r |取消撤销（常用）|
|. |重复前一个动作（常用）|
|**进阶**||
|start position + [N] command + end position|从start position到end position重复某个命令N次|


- 进入编辑模式

|按键| 功能|
|-|-|
|i, I|插入模式（Insert mode）: i为从当前光标所在处插入, I为从本行第一个非空白字符处插入（常用）|
|a, A|插入模式（Insert mode）: a为从当前光标的下一个字符处插入, A为本行最后一个字符处插入（常用）|
|o, O|插入模式（Insert mode）: o为从当前行的下一行插入新的一行, O为从当前行的上一行插入新的一行（常用）|
|r, R|取代模式（Replacemode）: r只会取代光标所在的那一个字符一次, R会一直取代光标所在的文字，直到按下 ESC 为止（常用）|
|[Esc] |退出编辑模式，回到命令模式中（常用）|





- 命令行模式

|按键| 功能|
|-|-|
|:w |存盘（常用）|
|:w! |若文件属性为“只读”时，强制写入该文件。|
|:q |离开 vi （常用）|
|:q! |强制离开(改动过文件). !在vim中通常表示强制|
|:wq |保存退出（常用）|
|ZZ |若文件没有改动，则相当于:q，若文件已经被改动过，则相当于:wq|
|:w [filename] |另存为 filename |
|:r [filename] |将 filename 的内容追加到光标所在行之后|
|:n1,n2 w [filename]| 将 n1 到 n2 行的内容写入 filename |
|:! command|暂时离开 vi 到命令行界面下执行 command 的显示结果！比如`! ls /home`|
|:set nu|显示行号，设置之后，会在每一列的字首显示该列的行号|
|:set nonu |与 set nu 相反，为取消行号！|
|:nohl|取消高亮|


### vim 的块操作

|按键| 功能|
|:-:|-|
|v| 字符选择|
|V| 行选择|
|[Ctrl]+v| 区块选择，可用矩形形状选择数据|
|y| 将反白的地方复制起来|
|d| 将反白的地方删除掉|
|p| 将刚刚复制的区块，粘贴在光标所在处|
|J| 把所有的行连接起来（变成一行）|
|< 或 > | 左右缩进|
|=| 自动给缩进|


### 多文件编辑
|按键| 功能|
|-|-|
|:n |编辑下一个文件|
|:N |编辑上一个文件|
|:files |列出目前这个 vim 的打开的所有文件|
### 多窗口
|按键| 功能|
|-|-|
|:sp(split) [filename] | 创建分屏 (:vsp创建垂直分屏) |
|[Ctrl]+ 方向 | dir就是方向，可以是 hjkl 或是 ←↓↑→ 中的一个，其用来切换分屏。|
|[Ctrl]+ w + _ (或 [Ctrl] + w + \|) | 竖直方向最大化尺寸 ([Ctrl] + w + \|为 水平方向最大化)|
|[Ctrl]+ w + \+ (或 [Ctrl]+ w + -) | 调整尺寸|
|[Ctrl]+ w + q|离开当前窗口|


## shell
### 管道命令
#### 选取命令
- cut

> cut以行为单位

```shell
[dmtsai@study ~]$ cut -d '分隔字符' -f fieldnumber <==用于有特定分隔字符
[dmtsai@study ~]$ cut -c 字符区间 <==用于排列整齐的讯息
选项与参数: 
-d : 后面接分隔字符。与 -f 一起使用；
-f : 依据 -d 的分隔字符将一段讯息分区成为数段，用 -f 取出第几段的意思；
-c : 以字符 （characters） 的单位取出固定字符区间,比如 cut -c 12-20 或cut -c 12- 意味着取出第12个到第20个字符(第12个之后所有字符)
```
- grep 查询满足条件的行

```shell
[dmtsai@study ~]$ grep [-acinvAB] [--color=auto] '查询内容' filename
选项与参数: 
-a : 将 binary 文件以 text 文件的方式搜寻数据
-c : 计算找到 '查询内容' 的次数
-i : 忽略大小写的不同，所以大小写视为相同
-n : 顺便输出行号
-v : 反向选择，亦即显示出没有 '查询内容' 内容的那一行
-A<num>: 意为 after ，除了列出该行外，后续的 n 行也列出来；
-B<num>: 意为 befer ，除了列出该行外，前面的 n 行也列出来；
-E : 将样式为延伸的正则表达式来使用。也可写为egrep
--color=auto : 将找到的关键字部分加上颜色,默认alias已支持
```

#### 排序命令
- sort 排序

```shell
[dmtsai@study ~]$ sort [-bcdfimMnr][-o<输出文件>][-t<分隔字符>][+<起始栏位>-<结束栏位>][--help][--verison][文件][-k field1[,field2]]
选项与参数: 
-f :忽略大小写的差异
-b :忽略最前面的空白字符部分
-n :使用 数值大小 进行排序（默认是以字符串排序）
-m : 将几个排序好的文件进行合并
-r :反向排序
-u :就是 uniq ,去重
-t<分隔字符> : 分隔符号，默认以 [tab] 键来分隔
+<起始栏位>-<结束栏位> : 以指定的栏位来排序，范围由起始栏位到结束栏位的前一栏位。
[-k field1[,field2]] : 按指定的列进行排序,注意是从1开始
```
- wc 计算输出内容的各项数据

```shell
[dmtsai@study ~]$ wc [-lwm]
选项与参数: 
-l : 仅列出行；
-w : 仅列出多少字（英文单字）；
-m : 多少字符；
```
- uniq 去重

```shell
[dmtsai@study ~]$ uniq [-ic]
选项与参数: 
-i : 忽略大小写字符的不同；
-c : 进行计数
```

#### 数据流处理 tee
```shell
[dmtsai@study ~]$ tee [-a] file...
选项与参数: 
-a : 追加
```

#### 字符处理
- tr 用于转换或删除文件中的字符

```shell
[dmtsai@study ~]$ tr [-ds] SET1 ...
选项与参数: 
-d : 删除 SET1 这个字符串
-s : 缩减连续重复的字符成指定的单个字符

示例: 将输出流中小写字母转为大写
[dmtsai@study ~]$ cat testfile |tr a-z A-Z 
或
[dmtsai@study ~]$ cat testfile |tr [:lower:] [:upper:] 
```
- col 用于过滤控制字符

```shell
[dmtsai@study ~]$ col [-xb]
选项与参数: 
-x :将 tab 键转换成对等的空格键
-b :过滤掉所有的控制字符，包括RLF和HRLF。
```
- join 用于将两个文件中，指定栏位内容相同的行连接起来。

```shell
[dmtsai@study ~]$ join [-ti12] file1 file2
选项与参数: 
-t <字符> : 使用<字符>分隔字符。
-i : 忽略大小写的差异；
-1 <栏位> : 连接[文件1]指定的栏位。
-2 <栏位> : 连接[文件2]指定的栏位。
```
- paste 用于合并文件的列。

```shell
[dmtsai@study ~]$ paste [-d] file1 file2
选项与参数: 
-d <间隔字符>: 默认是以 [tab] 来分隔的！
- : 如果 file 部分写成 - ，表示使用来自 standard input 的数据。
```
- expand 将 [tab] 按键转成空格键

```shell
[dmtsai@study ~]$ expand [-t] file
选项与参数: 
-t num: 默认 [tab] 相当于 8 个空格键。
```

#### 文件分割 split
```shell
[dmtsai@study ~]$ split [-bl] file PREFIX
选项与参数: 
-b <字节>: 后面可接欲分区成的文件大小，可加单位，例如 b, k, m 等
-l <行数>: 以行数来进行分区,也可以直接写-<行数>
PREFIX : 设置切割后文件的前置文件名， split会自动在前置文件名后再加上编号
```

#### 参数处理 xargs
> xargs（英文全拼:  eXtended ARGuments）是给命令传递参数的一个过滤器，也是组合多个命令的一个工具。
> xargs 可以将管道或标准输入（stdin）数据转换成命令行参数，也能够从文件的输出中读取数据。
> xargs 也可以将单行或多行文本输入转换为其他格式，例如多行变单行，单行变多行。
> xargs 默认的命令是 echo，这意味着通过管道传递给 xargs 的输入将会包含换行和空白，不过通过 xargs 的处理，**换行和空白将被空格取代**。
> xargs 是一个强有力的命令，它能够捕获一个命令的输出，然后传递给另外一个命令。
> 之所以能用到这个命令，关键是由于很多命令不支持|管道来传递参数，而日常工作中有有这个必要，所以就有了 xargs 命令，例如: 
```shell
find /sbin -perm +700 |ls -l       #这个命令是错误的
find /sbin -perm +700 |xargs ls -l   #这样才是正确的
```
命令格式:
```shell
[dmtsai@study ~]$ xargs [-a0epn] command
选项与参数: 
-a :file 从文件中读入作为 stdin
-0 :如果输入的 stdin 含有特殊字符，例如 \`, \, 空格等等字符时，这个 -0 参数可以将他还原成一般字符。
-e <str> :表示以<str>为 EOF （end of file）。当 xargs 分析到<str>时就会停止
-p :当每次执行一个argument的时候询问一次用户。
-n<num> :后面接次数，表示命令在执行的时候一次用的argument的个数，默认是用所有的。
当 xargs 后面没有接任何的命令时，默认是以 echo 来进行输出
```

## 正则表达式与文件格式化处理
### 特殊符号
|特殊符号| 代表意义|
|:- |-|
|**[:alnum:]**| 英文字母及数字，亦即 0-9, A-Z, a-z|
|**[:alpha:]**| 英文字母，亦即 A-Z, a-z|
|[:blank:]| 代表空格与 [Tab] 两者|
|[:cntrl:]| 控制字符，亦即包括 CR, LF, Tab, Del.. 等等|
|**[:digit:]**| 数字，亦即 0-9|
|[:graph:]| 除了空白字符 （空格与 [Tab]） 外的其他所有按键|
|**[:lower:]**| 小写字母，亦即 a-z|
|**[:upper:]**| 大写字母，亦即 A-Z|
|[:print:]| 代表任何可以被打印出来的字符|
|[:punct:]| 代表标点符号 （punctuation symbol），亦即: " ' ? ! ; : # $...|
|[:space:]| 任何会产生空白的字符，包括空格键, [Tab], CR 等等|
|[:xdigit:]| 16 进制的数字类型，包括:  0-9, A-F, a-f 的数字与字符|

### 延申型正则表达式
|RE字符 |意义|
|:-: |-|
|+|匹配前面的子表达式一次或多次。要匹配 + 字符，请使用 \\+。例: > egrep -n 'go+d' regular_express.txt|
|?|匹配前面的子表达式零次或一次，或指明一个非贪婪限定符。要匹配 ? 字符，请使用 \\?。 > egrep -n 'go?d' regular_express.txt|
|\||用或（ or ）的方式进行匹配 如 > egrep -n 'gd&#124;good' regular_express.txt > egrep -n 'gd&#124;good&#124;dog' regular_express.txt|
|()|标记一个子表达式的开始和结束位置。子表达式可以获取供以后使用.如 > egrep -n 'g（la&#124;oo）d' regular_express.txt|
|() +|多个重复子表达式的判别 例: > echo 'AxyzxyzxyzxyzC' &#124; egrep 'A（xyz）+C'|

### sed 
> sed 会根据脚本命令来处理文本文件中的数据，这些命令要么从命令行中输入，要么存储在一个文本文件中，此命令执行数据的顺序如下：
> 1. 每次仅读取一行内容；
> 2. 根据提供的规则命令匹配并修改数据。注意，sed 默认不会直接修改源文件数据，而是会将数据复制到缓冲区中，修改也仅限于缓冲区中的数据；
> 3. 将执行结果输出。
> 
> 当一行数据匹配完成后，它会继续读取下一行数据，并重复这个过程，直到将文件中所有数据处理完毕。
```shell
[root@VM-16-9-centos ~]$ sed [-nefr] [动作]
选项与参数: 
-n : 安静（silent）模式。仅显示script处理后的结果。注:在一般 sed 的用法中，所有来自 STDIN 的数据一般都会被列出到屏幕上。
-e<script>或--expression=<script>  : 以选项中指定的script来处理输入的文本文件。
-f<script文件>或--file=<script文件> : 以选项中指定的script文件来处理输入的文本文件。
-r : sed 的动作支持的是延伸型正则表达式的语法。（默认是基础正则表达式语法）
-i : 直接修改源文件，而不是由屏幕输出。慎用
动作说明:  [n1[,n2]]function也可为[address]function,一般加单引号
n1, n2 : 表示 进行动作的行数,比如如果我的动作是需要在 10 到 20 行之间进行的，则“ 10,20[动作行为] ”
function : 
a [str]: 新增, a 后可接字符串,表示在指定行的后面附加一行
c [str]: 取代, c 后可接字符串,表示以str取代n1-n2之前的行
d : 删除, 后面不接字符,比如'2,5d',意为删除2-5行
i [str]: 插入, i 后可接字符串，表示在指定行的前面插入一行
r filename: 于指定位置插入文件数据,基本格式为r filename
p : 打印,亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行
y : 替换单个字符,基本格式为 y/inchars/outchars/ ,默认转换所有字符
s : 替换,通常搭配正则表达式使用,其基本格式为s/pattern/replacement/flags
```
|flags|功能|
|:-: |-|
|n	|1~512 之间的数字，表示指定要替换的字符串出现第几次时才进行替换，例如，一行中有 3 个 A，但用户只想替换第二个 A，这是就用到这个标记；|
|g	|对数据中所有匹配到的内容进行替换，如果没有 g，则只会在第一次匹配成功时做替换操作。例如，一行数据中有 3 个 A，则只会替换第一个 A；|
|p	|会打印与替换命令中指定的模式匹配的行。此标记通常与 -n 选项一起使用。|
|w file|将缓冲区中的内容写到指定的 file 文件中；|
|&	|用正则表达式匹配的内容进行替换；|
|\n|	匹配第 n 个子串，该子串之前在 pattern 中用 \(\) 指定。|
|\	|转义（转义替换部分包含: &、\ 等）。|

### awk
> 和 sed 命令类似，awk 命令也是逐行扫描文件（从第 1 行到最后一行），寻找含有目标文本的行，如果匹配成功，则会在该行上执行用户想要的操作；反之，则不对行做任何处理。

```shell
[dmtsai@study ~]$ awk [options] 'scripts...' filename
选项与参数:
-F fs: 指定以 fs 作为输入行的分隔符，awk 命令默认分隔符为空格或制表符。
-f file: 从脚本文件中读取 awk 脚本指令，以取代直接在命令行中输入指令。
-v var=val: 在执行处理过程之前，设置一个变量 var，并给其设备初始值为 val。
scripts说明:脚本命令由2部分组成:'匹配规则{执行命令}',且可以同时执行多个脚本命令.整个脚本命令由''括起来,其中的执行命令部分用{}括起来
```
> 在 awk 程序执行时，如果没有指定执行命令，则默认会把匹配的行输出；  
> 如果不指定匹配规则，则默认匹配文本中所有的行。

#### 数据字段
默认情况下, awk 会将如下变量分配给它在文本**行**中发现的数据字段:
- $0 代表整个文本行
- $1 代表文本行中的第 1 个数据字段
- $2 代表文本行中的第 2 个数据字段
- $n 代表文本行中的第 n 个数据字段
注意:操作对象是行
例:
```shell
[root@localhost ~]$ cat data2.txt
One line of test text.
Two lines of test text.
Three lines of test text.
[root@localhost ~]$ awk '{print $1}' data2.txt
One
Two
Three
```
#### 多命令
awk 允许将多条命令组合成一个正常的程序。要在命令行上的程序脚本中使用多条命令，只要在命令之间放个分号即可，例如：
```shell
[root@localhost ~]$ echo "My name is Rich" | awk '{$4="Christine"; print $0}'
My name is Christine
```
除此之外，也可以一次一行地输入程序脚本命令，比如说：
```shell
[root@localhost ~]$ awk '{
> $4="Christine"
> print $0}'
My name is Rich
My name is Christine
```

#### BEGIN 及 END 关键字
awk可以指定脚本命令的运行时机:
- BEGIN 关键字允许我们在处理数据前运行一些脚本命令
- END 关键字允许我们处理数据后允许一些脚本命令

例:
```shell
[root@localhost ~]$ cat data3.txt
Line 1
Line 2
Line 3

[root@localhost ~]$ awk 'BEGIN {print "The data3 File Contents:"}
> {print $0}' data3.txt
The data3 File Contents:
Line 1
Line 2
Line 3

[root@localhost ~]$ awk 'BEGIN {print "The data3 File Contents:"}
> {print $0}
> END {print "End of File"}' data3.txt
The data3 File Contents:
Line 1
Line 2
Line 3
End of File
```

#### 内置变量
awk内置一些变量用于获取文件信息
|变量名称| 代表意义|
|:-:|-|
|NF| 每一行 （$0） 拥有的字段总数|
|NR| 目前 awk 所处理的是“第几行”数据|
|FS| 目前的分隔字符，默认是空格键|
|ARGC    |           命令行参数个数|
|ARGV    |           命令行参数排列|
|ENVIRON |           支持队列中系统环境变量的使用|
|FILENAME|           awk浏览的文件名|
|FNR     |           浏览文件的记录数|
|OFS     |           输出域分隔符|
|ORS     |           输出记录分隔符|
|RS      |           控制记录分隔符|
例:
```shell
[dmtsai@study ~]$ last -n 5 | awk '{print $1 "\t lines: " NR "\t column_number: " NF}'
dmtsai lines: 1 column_number: 10
dmtsai lines: 2 column_number: 10
dmtsai lines: 3 column_number: 10
dmtsai lines: 4 column_number: 10
dmtsai lines: 5 column_number: 9
```
除了awk的内置变量，awk还可以自定义变量。比如:
```shell
[dmtsai@study ~]$ awk '{count++;print $0;} END{print "user count is ", count}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
......
user count is  40

[dmtsai@study ~]$ awk 'BEGIN {count=0;print "[start]user count is ", count} {count=count+1;print $0;} END{print "[end]user count is ", count}' /etc/passwd
[start]user count is  0
root:x:0:0:root:/root:/bin/bash
...
[end]user count is  40
```
#### 匹配规则
awk的匹配规则可以使用条件语句,循环语句