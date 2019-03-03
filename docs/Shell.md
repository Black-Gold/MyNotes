# shell笔记

bash shell本身不支持正则表达式，使用正则的是shell命令和工具，如grep、sed等。bash shell可以使用正则表达式中的一些元字符实现通配(Globbing)功能。通配就是把一个包含通配符的非具体文件名扩展存储在计算机或网络上的一批具体文件名的过程。

- Reference List

  - Bash Guide for Beginners
  - Advanced Bash-Scripting Guide

## shell特殊变量基本解释

```sh

$*     传递给程序的所有参数组成的字符串。
$@     所有的参数，每个都用双括号括起
$#     传递给程序的总的参数数目
$*     传递给程序的所有参数组成的字符串。
$-     在Shell启动或使用set命令时提供选项
$?     上一条命令执行后返回值

返回值参考：
0      表示运行成功
2      权限拒绝
1~125  运行失败、脚本命令、系统命令错误或参数传递错误
126    找到该命令但无法执行
127    未找到要运行的命令
>128   命令被系统强制结束
$$     当前shell的进程号
$!     上一个子进程的进程号
$@     所有的参数，每个都用双括号括起
$n     位置参数值，n表示位置
$0     当前shell名（当前执行进程的进程名）

数值比较：

-eq ：等于
-ne ：不等于
-gt ：大于
-lt  ：小于
-le  ：小于或等于
-ge ：大于或等于

逻辑测试：
&& ：逻辑与
|| ：逻辑或
！ ：逻辑否
```

## 在shell中常用的特殊符号罗列如下：

### ; 分号 (Command separator)

```sh
在 shell 中，担任\"连续指令\"功能的符号就是\"分号\"。
譬如以下的例子：cd ~/backup ; mkdir startup ;cp ~/.* startup/.

```

### ;; 连续分号 (Terminator)

```sh
专用在 case 的选项，担任 Terminator 的角色。
case \"$fop\" inhelp) echo \"Usage: Command -help -version filename\";;version) echo \"version 0.1\" ;;esac
```

### . 逗号 (dot,就是“点”)

```sh
在 shell 中，使用者应该都清楚，一个 dot 代表当前目录，两个 dot 代表上层目录。
CDPATH=.:~:/home:/home/web:/var:/usr/local
在上行 CDPATH 的设定中，等号后的 dot 代表的就是当前目录的意思。
如果档案名称以 dot 开头，该档案就属特殊档案，用 ls 指令必须加上 -a 选项才会显示。除此之外，在 regularexpression 中，一个 dot 代表匹配一个字元。
```

### 'string' 单引号 (single quote)

```sh
被单引号用括住的内容，将被视为单一字串。在引号内的代表变数的$符号，没有作用，也就是说，他被视为一般符号处理，防止任何变量替换。
heyyou=homeecho '$heyyou' # We get $heyyou
```

### \"string" 双引号 (double quote)

```sh
被双引号用括住的内容，将被视为单一字串。它防止通配符扩展，但允许变量扩展。这点与单引数的处理方式不同。
heyyou=homeecho \"$heyyou\" # We get home
```

### \`command` 倒引号 (backticks)

```sh
在前面的单双引号，括住的是字串，但如果该字串是一列命令列，会怎样？答案是不会执行。要处理这种情况，我们得用倒单引号来做。
fdv=`date +%F`echo \"Today $fdv\"
在倒引号内的 date +%F 会被视为指令，执行的结果会带入 fdv 变数中。
```

### , 逗点 (comma，标点中的逗号)

```sh
这个符号常运用在运算当中当做\"区隔\"用途。如下例
#!/bin/bashlet \"t1 = ((a = 5 + 3, b = 7 - 1, c = 15 / 3))\"echo \"t1= $t1, a = $a, b = $b\"
```

### / 斜线 (forward slash)

```sh
在路径表示时，代表目录。
cd /etc/rc.dcd ../..cd /
通常单一的 / 代表 root 根目录的意思；在四则运算中，代表除法的符号。
let \"num1 = ((a = 10 / 2, b = 25 / 5))\"
```

### \\\ 倒斜线

```sh
在交互模式下的escape 字元，有几个作用；放在指令前，有取消 aliases的作用；放在特殊符号前，则该特殊符号的作用消失；放在指令的最末端，表示指令连接下一行。
# type rmrm is aliased to `rm -i'# \\rm ./*.log
上例，我在 rm 指令前加上 escape 字元，作用是暂时取消别名的功能，将 rm 指令还原。
# bkdir=/home# echo \"Backup dir, \\$bkdir = $bkdir\"Backup dir,$bkdir = /home
上例 echo 内的 \\$bkdir，escape 将 $ 变数的功能取消了，因此，会输出 $bkdir，而第二个 $bkdir则会输出变数的内容 /home。
```

### | 管道 (pipeline)

```sh
pipeline 是 UNIX 系统，基础且重要的观念。连结上个指令的标准输出，做为下个指令的标准输入。
who | wc -l
善用这个观念，对精简 script 有相当的帮助。
```

### ! 惊叹号(negate or reverse)

```sh
通常它代表反逻辑的作用，譬如条件侦测中，用 != 来代表\"不等于\"
if [ \"$?\" != 0 ]thenecho \"Executes error\"exit 1fi
在规则表达式中她担任 \"反逻辑\" 的角色
ls a[!0-9]
上例，代表显示除了a0, a1 .... a9 这几个文件的其他文件。
```

### : 冒号

```sh
在 bash 中，这是一个内建指令：\"什么事都不干\"，但返回状态值 0。
:
echo $? # 回应为 0
: > f.$$
上面这一行，相当于 cat /dev/null >f.$$。不仅写法简短了，而且执行效率也好上许多。
有时，也会出现以下这类的用法
: ${HOSTNAME?} ${USER?} ${MAIL?}
这行的作用是，检查这些环境变数是否已设置，没有设置的将会以标准错误显示错误讯息。像这种检查如果使用类似 test 或 if这类的做法，基本上也可以处理，但都比不上上例的简洁与效率。
```

### ? 问号 (wild card)

```sh
在文件名扩展(Filename expansion)上扮演的角色是匹配一个任意的字元，但不包含 null 字元。
# ls a?a1
善用它的特点，可以做比较精确的档名匹配。
```

### * 星号 (wild card)

```sh
相当常用的符号。在文件名扩展(Filename expansion)上，她用来代表任何字元，包含 null 字元。
# ls a*a a1 access_log
在运算时，它则代表 \"乘法\"。
let \"fmult=2*3\"
除了内建指令 let，还有一个关于运算的指令expr，星号在这里也担任\"乘法\"的角色。不过在使用上得小心，他的前面必须加上escape 字元。
```

### ** 次方运算

```sh
两个星号在运算时代表 \"次方\" 的意思。
let \"sus=2**3\"echo \"sus = $sus\" # sus = 8
```

### $ dollar符号(dollar sign)

```sh
变量替换(Variable Substitution)的代表符号。
vrs=123echo \"vrs = $vrs\" # vrs = 123
另外，在 Regular Expressions 里被定义为 \"行\" 的最末端 (end-of-line)。这个常用在grep、sed、awk 以及 vim(vi) 当中。
```

### ${} 变量的正规表达式

```sh
bash 对 ${} 定义了不少用法。以下是取自线上说明的表列
${parameter:-word}   ${parameter:=word}   ${parameter:?word}  ${parameter:+word}   ${parameterffset}   {parameterffset:length}   ${!prefix*}   ${#parameter}   {parameter#word}   ${parameter##word}   ${parameter%word}   {parameter%%word}   ${parameter/pattern/string}   {parameter//pattern/string}
```

### $*符号

```sh
$* 引用script的执行引用变量，引用参数的算法与一般指令相同，指令本身为0，其后为1，然后依此类推。引用变量的代表方式如下：
$0, $1, $2, $3, $4, $5, $6, $7, $8, $9, ${10}, ${11}.....
个位数的，可直接使用数字，但两位数以上，则必须使用 {} 符号来括住。
$* 则是代表所有引用变量的符号。使用时，得视情况加上双引号。
echo \"$*\"
还有一个与 $* 具有相同作用的符号，但效用与处理方式略为不同的符号。
```

### $@

```sh
$@ 与 $* 具有相同作用的符号，不过她们两者有一个不同点。
符号 $* 将所有的引用变量视为一个整体。但符号 $@ 则仍旧保留每个引用变量的区段观念。
```

$#

```sh
这也是与引用变量相关的符号，她的作用是告诉你，引用变量的总数量是多少。
echo \"$#\"
```

### $? 状态值 (status variable)

```sh
一般来说，UNIX(linux) 系统的进程以执行系统调用exit()来结束的。这个回传值就是status值。回传给父进程，用来检查子进程的执行状态。
一般指令程序倘若执行成功，其回传值为 0；失败为 1。
tar cvfz dfbackup.tar.gz /home/user > /dev/nullecho\"$?\"$$
由于进程的ID是唯一的，所以在同一个时间，不可能有重复性的 PID。有时，script会需要产生临时文件，用来存放必要的资料。而此script亦有可能在同一时间被使用者们使用。在这种情况下，固定文件名在写法上就显的不可靠。唯有产生动态文件名，才能符合需要。符号$$或许可以符合这种需求。它代表当前shell 的 PID。
echo \"$HOSTNAME, $USER, $MAIL\" > ftmp.$$
使用它来作为文件名的一部份，可以避免在同一时间，产生相同文件名的覆盖现象。
ps: 基本上，系统会回收执行完毕的 PID，然后再次依需要分配使用。所以 script 即使临时文件是使用动态档名的写法，如果script 执行完毕后仍不加以清除，会产生其他问题。
```

### $$

```sh
进程ID变量，保存了脚本运行时的ID
```

### (   ) 指令群组 (command group)

```sh
用括号将一串连续指令括起来，这种用法对 shell 来说，称为指令群组。如下面的例子：(cd ~ ; vcgh=`pwd` ;echo $vcgh)，指令群组有一个特性，shell会以产生 subshell来执行这组指令。因此，在其中所定义的变数，仅作用于指令群组本身。我们来看个例子
# cat ftmp-01#!/bin/basha=fsh(a=incg ; echo -e \"\\n $a \\n\")echo $a#./ftmp-01incgfsh
除了上述的指令群组，括号也用在 array 变数的定义上；另外也应用在其他可能需要加上escape字元才能使用的场合，如运算式。
```

### (( ))

```sh
这组符号的作用与 let 指令相似，用在算数运算上，是 bash 的内建功能。所以，在执行效率上会比使用 let指令要好许多。
#!/bin/bash(( a = 10 ))echo -e \"inital value, a = $a\\n\"(( a++))echo \"after a++, a = $a\"
```

### { } 大括号 (Block of code)

```sh
有时候脚本当中会出现，大括号中会夹着一段或几段以\"分号\"做结尾的指令或变数设定。
# cat ftmp-02#!/bin/basha=fsh{a=inbc ; echo -e \"\\n $a \\n\"}echo $a#./ftmp-02inbcinbc
这种用法与上面介绍的指令群组非常相似，但有个不同点，它在当前的 shell 执行，不会产生 subshell。
大括号也被运用在 \"函数\" 的功能上。广义地说，单纯只使用大括号时，作用就像是个没有指定名称的函数一般。因此，这样写 script也是相当好的一件事。尤其对输出输入的重导向上，这个做法可精简 script 的复杂度。
此外，大括号还有另一种用法，如下
{xx,yy,zz,...}
这种大括号的组合，常用在字串的组合上，来看个例子
mkdir {userA,userB,userC}-{home,bin,data}
我们得到 userA-home, userA-bin, userA-data, userB-home, userB-bin,userB-data, userC-home, userC-bin,userC-data，这几个目录。这组符号在适用性上相当广泛。能加以善用的话，回报是精简与效率。像下面的例子
chown root /usr/{ucb/{ex,edit},lib/{ex?.?*,how_ex}}
如果不是因为支援这种用法，我们得写几行重复几次呀！
```

### [ ] 中括号

```sh
常出现在流程控制中，扮演括住判断式的作用。if [ \"$?\" != 0 ]thenecho \"Executes error\"exit1fi
这个符号在正则表达式中担任类似 \"范围\" 或 \"集合\" 的角色
rm -r 200[1234]
上例，代表删除 2001, 2002, 2003, 2004 等目录的意思。
```

{} \\;

```sh
路径名，基本用于find命令内，不是shell內建命令
分号; 结束find命令中-exec选项的命令序列，应该转义以免被shell误解释
```

### [[ ]]

```sh
这组符号与先前的 [] 符号，基本上作用相同，但她允许在其中直接使用 || 与&& 逻辑等符号。
#!/bin/bashread akif [[ $ak > 5 || $ak< 9 ]]thenecho $akfi
```

### || 逻辑符号----代表 or 逻辑的符号。

### && 逻辑符号----代表 and 逻辑的符号。

### & 后台工作

```sh
单一个& 符号，且放在完整指令列的最后端，即表示将该指令列放入后台中工作。
tar cvfz data.tar.gz data > /dev/null&
```

### \\<...\\> 单字边界

```sh
这组符号在规则表达式中，被定义为\"边界\"的意思。譬如，当我们想找寻 the 这个单字时，如果我们用
grep the FileA
你将会发现，像 there 这类的单字，也会被当成是匹配的单字。因为 the 正巧是 there的一部份。如果我们要必免这种情况，就得加上 \"边界\" 的符号
grep '\\' FileA
```

### + 加号 (plus)

```sh
在运算式中，她用来表示 \"加法\"。
expr 1 + 2 + 3
此外在规则表达式中，用来表示\"很多个\"的前面字元的意思。
# grep '10\\+9' fileB109100910000910000931010009#这个符号在使用时，前面必须加上escape 字元。
```

### - 减号 (dash)

```sh
在运算式中，她用来表示 \"减法\"。
expr 10 - 2
此外也是系统指令的选项符号。
ls -expr 10 - 2
在 GNU 指令中，如果单独使用 - 符号，不加任何该加的文件名称时，代表\"标准输入\"的意思。这是 GNU指令的共通选项。譬如下例
tar xpvf -
这里的 - 符号，既代表从标准输入读取资料。
不过，在 cd 指令中则比较特别
cd -
这代表变更工作目录到\"上一次\"工作目录。
```

### % 除法 (Modulo)

```sh
在运算式中，用来表示 \"除法\"。
expr 10 % 2
此外，也被运用在关于变量的规则表达式当中的下列
${parameter%word}${parameter%%word}
一个 % 表示最短的 word 匹配，两个表示最长的 word 匹配。
```

### = 等号 (Equals)

```sh
常在设定变数时看到的符号。
vara=123echo \" vara = $vara\"
或者像是 PATH 的设定，甚至应用在运算或判断式等此类用途上。
```

### == 等号 (Equals)

```sh
常在条件判断式中看到，代表 \"等于\" 的意思。
if [ $vara == $varb ]
...下略
```

### != 不等于

```sh
常在条件判断式中看到，代表 \"不等于\" 的意思。
if [ $vara != $varb ]
...下略
```

^
在规则表达式中，代表行的 \"开头\" 位置，在[]中也与\"!\"(叹号)一样表示“非”

输出/输入重定向 > >>   <   <<   :>   &>   2&>   2<>>&   >&2

```sh
文件描述符(File Descriptor)，用一个数字（通常为0-9）来表示一个文件。
常用的文件描述符如下：
文件描述符     名称     常用缩写    默认值
0        标准输入       stdin      键盘
1        标准输出       stdout     屏幕
2        标准错误输出    stderr     屏幕
我们在简单地用<或>时，相当于使用 0< 或 1>（下面会详细介绍）。
* cmd > file
把cmd命令的输出重定向到文件file中。如果file已经存在，则清空原有文件，使用bash的noclobber选项可以防止复盖原有文件。
* cmd >> file
把cmd命令的输出重定向到文件file中，如果file已经存在，则把信息加在原有文件後面。
* cmd < file
使cmd命令从file读入
* cmd << text
从命令行读取输入，直到一个与text相同的行结束。除非使用引号把输入括起来，此模式将对输入内容进行shell变量替换。如果使用<<- ，则会忽略接下来输入行首的tab，结束行也可以是一堆tab再加上一个与text相同的内容，可以参考後面的例子。
* cmd <<< word
把word（而不是文件word）和後面的换行作为输入提供给cmd。
* cmd <> file
以读写模式把文件file重定向到输入，文件file不会被破坏。仅当应用程序利用了这一特性时，它才是有意义的。
* cmd >| file
功能同>，但即便在设置了noclobber时也会复盖file文件，注意用的是|而非一些书中说的!，目前仅在csh中仍沿用>!实现这一功能。
: > filename    把文件\"filename\"截断为0长度.# 如果文件不存在, 那么就创建一个0长度的文件(与'touch'的效果相同).
cmd >&n 把输出送到文件描述符n
cmd m>&n 把输出 到文件符m的信息重定向到文件描述符n
cmd >&- 关闭标准输出
cmd <&n 输入来自文件描述符n
cmd m<&n m来自文件描述各个n
cmd <&- 关闭标准输入
cmd <&n- 移动输入文件描述符n而非复制它。（需要解释）
cmd >&n- 移动输出文件描述符 n而非复制它。（需要解释）
注意： >&实际上复制了文件描述符，这使得cmd > file 2>&1与cmd 2>&1 >file的效果不一样。
&> file和 > file 2>&1相同
```

### -

用于stdin或stdout重定向的源或目的[dash]

```sh
echo "whatever" | cat - #输出 whatever
```

使用- 的tar命令的真实例子

```sh
#!/bin/bash
#  备份当前目录下所有前24小时被修改的文件为一个归档压缩包（归档并且压缩）
 BACKUPFILE=backup-$(date +%m-%d-%Y)
  #                 在备份文件中嵌入日期.
  #                 多谢Joshua Tschida的这个主意.
  archive=${1:-$BACKUPFILE}
  #  如果没有在命令行上指定备份的归档文件名,
  #+ 会以"backup-MM-DD-YYYY.tar.gz."作为默认的文件名
  
  tar cvf - `find . -mtime -1 -type f -print` > $archive.tar
  gzip $archive.tar
  echo "Directory $PWD backed up in archive file \"$archive.tar.gz\"."
  
  
  #  Stephane Chazelas指出：如果有许多文件被找到
  #+ 或任何一个文件名中包含有空白字符
  #+ 上面的代码将会失败.
  
  # 他建议用下面的代码:
  # -------------------------------------------------------------------
  #   find . -mtime -1 -type f -print0 | xargs -0 tar rvf "$archive.tar"
  #      using the GNU version of "find".

  #   find . -mtime -1 -type f -exec tar rvf "$archive.tar" '{}' \;
  
  exit 0

```

先前的工作目录. 命令cd - 可以回到原来的工作目录.它使用了$OLDPWD 环境变量.
不要弄混了这儿使用的"-"和上面刚讨论的"-"重定向操作符.对于"-"字符的解释应依赖于它出现的环境.

负号或减号. 减号用于算术操作

### ~

主目录或称为家目录[波浪号]. 它与内部变量 $HOME 是一致的. ~bozo是bozo'的主目录,而ls ~bozo 会列出此目录的内容. ~/ 是当前用户的主目录,并且ls ~/ 会列出此目录的内容.

### ~+

当前工作目录. 它与外部变量$PWD是一致的

### ~-

先前的工作目录. 它与外部变量$OLDPWD是一致的

### 控制字符

```sh

更改终端行为或文本显示. 控制字符都是以CONTROL + key的组合键.

在脚本文件中控制字符是不起作用的
Ctl-B

退格 (非破坏性的).

Ctl-C

中断. 终结一个前台作业.


Ctl-D

从一个shell中退出 (类似于exit).

"EOF" (文件结尾：end of file).它也用于表示标准输入（stdin）的结束.

在控制台或xterm 窗口输入文本时, Ctl-D删除在光标下的字符.如果没有字符存在，Ctl-D 则会登录出该会话. 在一个xterm窗口中，则会产生关闭此窗口的效果。

Ctl-G

"哔" (beep).在一些老式的打字机终端上，它会响一下铃.

Ctl-H

"杀掉" (破坏性的退格). 删除光标前的一个字符＝＝＝.

   1 #!/bin/bash
   2 # 在一个字符串里嵌入 Ctl-H.
   4 a="^H^H"                  # 两个 Ctl-H (退格).
   5 echo "abcdef"             # abcdef
   6 echo -n "abcdef$a "       # abcd f
   7 #以一个空格结尾  ^              ^ 退二格.
   8 echo -n "abcdef$a"        # abcdef
   9 #  现在没有尾部的空格            不退格了 (为什么?).
  10                           # 结果和预料的不一样.
  11 echo; echo

Ctl-I

水平制表符.

Ctl-J

新行(换一行并到行首).

Ctl-K

垂直制表符.

在控制台或xterm 窗口输入文本时, Ctl-K 会删除从光标所在处到行尾的所有字符。

Ctl-L

清屏 (重绘屏幕，清除前面的打印信息).这与clear命令作用相同.

Ctl-M

回车.

   1 #!/bin/bash
   2 # 多谢Lee Maschmeyer的例子
   4 read -n 1 -s -p $'Control-M leaves cursor at beginning of this line. Press Enter. \x0d'
   5                                   # 是的, '0d'是Control-M的十六进制值.
   6 echo >&2   #  '-s'使所有被键入的字符都不回显,
   7            #+ 所以需要明确地键入新行.
   9 read -n 1 -s -p $'Control-J leaves cursor on next line. \x0a'
  10 echo >&2   #  Control-J 是换行.
  12 ###
  14 read -n 1 -s -p $'And Control-K\x0bgoes straight down.'
  15 echo >&2   #  Control-K 是垂直制表符.
  17 # 展示垂直制表符作用的更好的例子是:
  19 var=$'\x0aThis is the bottom line\x0bThis is the top line\x0a'
  20 echo "$var"
  21 #  这和上面的例子一样工作.但是:
  22 echo "$var" | col
  23 #  这使行的右端比左端更高.
  24 #  这也解释了为什么我们以一个换行符开始和结束 --
  25 #+ 是为了避免屏幕显示混乱.
  27 # 这是Lee Maschmeyer的解释:
  28 # --------------------------
  29 #  在第一个垂直制表符例子中 . . . 垂直制表符使还未打印回车就直接垂直打印下来。
  30 #
  31 #  这只在不能“倒后”的设备里才成立,比如在Linux控制台,
  32 #
  33 #  垂直制表符真正的意图是能垂直地往上移，而不是往下移.
  34 #  可以在打印机里用于打印上标.
  35 #  这个要点的作用被用于仿效垂直制表符正确的功能.
  37 exit 0

Ctl-Q

解冻 (XON).

它解冻终端的标准输入.

Ctl-S

挂起输入 (XOFF).

它冻结终端的标准输入. (用 Ctl-Q 可恢复输入.)

Ctl-U

删除从光标到行首的一行输入.在某些设置里，Ctl-U 删除整行的输入，而不管光标的位置.

Ctl-V

当输入一个文本, Ctl-V允许插入控制字符。例如，下面两个命令是相等的:
   1 echo -e '\x0a'
   2 echo <Ctl-V><Ctl-J>

Ctl-V 主要用于文本编辑.

Ctl-W

当在控制台或一个xterm窗口敲入文本时, Ctl-W 会删除从在光标处往后的第一个空白符之间的内容.在某些设置里, Ctl-W 删除光标往后到第一个非文字和数字之间的字符.

Ctl-Z

暂停一个前台作业.

```

### shell多行注释实现方式

```sh

方法一：
: << 字符
语句
字符

方法二：
: '
语句
'
```

### 变量替换

```sh
Var1=12
Var2=$Var1 #等号两边不能有空白符

echo $Var1
echo $Var2
echo ${Var2} #与上句表示的一样，$Var2是其简写形式

Var2="a b  c"
echo $Var2 #输出为a b c
echo "$Var2" #输出为a b  c
# 两者输出不同，把变量用双引号引起来会保留空白字符

echo '$Var2' #把变量用单引号引起来只表示普通的字符

#设置Var2为null value
Var2=
echo "\$Var2(null value)=$Var2"
#注意：具有null值的变量不等同于废弃(unset)此变量，虽然结果都相同

Nub1="1 2 3" #给变量赋值有空白时，引号时必须的
echo $Nub1

#未初始化的变量具有null值
Nub2=

#一个未初始化的变量有一个”null”值――表示从没有被赋值过（注意null值不等于零）。在一个变量从未赋值之前就使用它通常会引起问题。然而，仍然有可能在执行算术计算时使用一个未初始化的变量。

echo "$Nub2"
let "Nub2 += 2"
echo $Nub2

##变量赋值
#赋值操作符(它的两边不能有空白符)
#不要搞混=和-eq，-eq是比赋值操作更高级的测试
#等号=在不同的环境不同，可能时赋值操作符也可能时测试操作符

#当变量被赋值而不是引用时，称之为裸变量
#赋值
a=3
echo "这个变量\"a\"是$a"

#用let命令赋值
let a=12+42
echo "let赋值的变量\"a\"是$a"

#在一个循环中赋值(其实是一种伪赋值)
for a in 1 2 3 4 5
do
    echo -n "$a"
done

: <<!
#使用read命令(也是一种赋值)
echo -n "输入：\"a\""
read a
echo "read赋值后\"a\"是$a"
!

# 简述简单而又奇特的变量赋值
a=1
echo $a
b=$a
echo $b

#下面讲奇怪的赋值(命令替换)
a=`echo hello!` #把echo命令的结果赋值给变量a
echo $a
#若只在一个命令替换结构中包含一个感叹号!，将不会工作，因为感叹号触发了bash历史命令机制。不过在脚本中，历史命令机制是被禁止的。
a=`ls -l` #把ls -a的结果赋值给变量a
echo $a #没有引号则会删除多余的tab键和空白符
echo "$a" #加引号可以保留空白符
R=$(cat /etc/lsb-release)
arch=$(uname -m)

echo $R
echo $arch
```

echo命令使用的转义序列

| 序列 | 含义 |
| :------: | :------: |
| \a | Alert (bell) |
| \b | Backspace |
| \c | Suppress trailing newline |
| \e | Escape |
| \f | Form feed |
| \n | Newline |
| \r | Carriage return |
| \t | Horizontal tab |
| \v | Vertical tab |
| \\ | Backslash |
| \0NNN | The eight-bit character whose value is the octal value NNN (zero to three octal digits) |
| \NNN | The eight-bit character whose value is the octal value NNN (one to three octal digits) |
| \xHH | The eight-bit character whose value is the hexadecimal value (one or two hexadecimal digits) |

### bash变量是无类型的

```sh
#不同与许多其他的编程语言，Bash不以"类型"来区分变量。本质上来说，Bash变量是字符串，但是根据环境的不同，Bash允许变量有整数计算和比较。其中的决定因素是变量的值是不是只含有数字.
#整数还是字符串？
var1=2314
let "var1+=1"
echo "var1=$var1" #var1仍然是整数

var2=${var1/23/aa} #将var1中的23替换成aa并赋值给var2，这使var2成为字符串
echo "var2=$var2"
declare -i var2 #即使明确声明var2是整数也没用
echo "var2=$var2"

let "var2+=1"
echo "var2=$var2"

var3=aa14
echo "var3=$var3"
var4=${var3/aa/23}

echo "var4=$var4"
let "var+=1"
echo "var4=$var4"

#如果变量是null如何
var5=""
echo "var5=$var5"
let "var5+=1"
echo "var5=$var5"

#如果没有声明变量如何
echo "var6=$var6"
let "var6+=1"
echo "var6=$var6"

#结论：在bash中变量确实是无类型的
exit 0
```

### 特殊变量类型

```sh
#局部变量：只在代码块或一个函数中有效
#环境变量：其会影响shell的行为和用户接口，大部分情况下，每个进程都有一个环境，其由进程使用的环境变量组成
#分配给环境变量的总空间有限，若创建太多环境变量或有些环境变量的值太长而占用过多空间则会出错
#例如：
: <<comment
eval "`seq 10000 | sed -e 's/.*/export var&=ZZZZZZZZZZZZZZ/'`"
du
/usr/bin/du:Argument list too long
comment
#如果一个脚步要设置一个环境变量，则需要将其导出(export)，也就是讲需通知到脚步的环境表，这是export命令功能
#一个脚本只能导出（export）变量到子进程，也就是说只能导出到由此脚本生成的命令或进程中。在一个命令行中运行的脚本不能导出一个变量影响到命令行的环境。子进程不能导出变量到生成它的父进程中。
#位置参数
: <<comment
命令行传递给脚本的参数是$0,$1,$2....
$0是脚本的名字，$1是第一个参数，$2是第二个参数......在位置参数$9之后的参数必须用大括号括起来。如：${10}
特殊变量$*和$@表示所有的位置参数
comment

#位置参数例子：
: <<comment
#!/bin/bash
#至少以10个参数运行此脚本。例如：./script.sh 1 2 3 4 5 6 7 8 9 10
Minparams=10

echo "脚本名字是\"$0\"."
#用./表示当前目录
echo "脚本名字是\"`basename $0`\"."

comment
```

### 转义字符

```sh
echo "\v\v\v"

#用-e选项，echo会打印出转义字符
echo -e "\v\v\v"

#打印出字符" ("引号字符的八进制ASCII码为42)
echo -e "\042"

#当使用像$'\x'的结构时，-e的选项是多余的
echo "NEW LINE and BEEP"
echo $'\n'  #新行
echo $'\a'  #警告(峰鸣)

#版本2开始bash允许使用$'\nnn'结构，这里'\nnn'表示一个八进制的值
echo $'\042'

#使用$'\xnnn'结构也可以使用十六进制来转义
echo $'\x22'

#用ASCII码值把字符赋给变量
quote=$'\042'   #引号"被赋值给变量quote
echo "$quote 这是一个引号字符串"

# 用连串的ASCII码把一串字符赋给变量..
triple_underline=$'\137\137\137'  # 137是字符'_'的ASCII码
echo "$triple_underline UNDERLINE $triple_underline"

ABC=$'\101\102\103\010' # 101, 102, 103分别是A, B, C字符的八进制ASCII码.
echo $ABC

escape=$'\033'  # 033是ESC的ASCII码的八进制值
echo "\"escape\" echoes as $escape" # 不可见的输出

一个字符串赋给变量时里面的组成部分可能会被转义，但如果单独一个转义字符（\）是不能赋给变量的。

转义一个空格可以防止一个字符串参数被分割成多个命令行参数。

转义符也提供了写一个多行命令的手段。一般地，每个单独的行有一个不同的命令，而在一行末尾的转义符转义新行符，命令序列则由下一行继续。
```

### bash退出和退出状态

```sh
exit命令一般用于结束一个脚本，它也能返回一个值给父进程
每一个命令都能返回一个退出状态（有时也看做返回状态).一个命令执行成功返回0，一个执行不成功的命令则返回一个非零值，此值通常可以被解释成一个对应的错误值。除了一些例外的情况，一个行为端庄的UNIX命令，程序或是软件包执行成功能返回0的作为退出码。

同样的，在脚本里的函数和脚本自身都会返回一个退出状态码。在脚本或函数里被执行的最后一个命令将决定退出状态码。在一个脚本里，exit nnn 命令将会返回shell一个nnn的退出状态码。（nnn必须是一个在0-255范围的十进制整数）。

如果一个脚本以不带参数的exit命令结束，脚本的退出状态码将会是执行exit命令前的最后一个命令的退出码。

脚本结束没有exit,不带参数的exit和exit $?三者等价
$?变量保存了最后一个命令执行后的退出状态，当一个函数返回时，$?保存了函数里最后一个命令的退出状态码。这就是Bash里函数返回值的处理办法。当一个脚本运行结束，$? 变量保存脚本的退出状态，而脚本的退出状态则是脚本中最后一个已执行命令的退出状态。并且依照惯例，0表示执行成功，1-255的整数范围表示错误或反常的条件。

逻辑非操作符!，反转一个命令或一个测试的结果。也能反转退出状态

```

### 和if一起使用的主要表达式

| 主要的 | 含义 |
| :------: | :------: |
| [ -a FILE ] | 文件如果存在返回True |
| [ -b FILE ] | 文件如果存在并且是块设备返回True(软盘,光驱等)|
| [ -c FILE ] | 文件如果存在并且时字符设备返回True(键盘,调制解调器,声卡等) |
| [ -d FILE ] | 如果是目录返回True |
| [ -e FILE ] | 文件如果存在返回True |
| [ -f FILE ] | 文件是普通文件并且不是一个目录或是一个设备文件返回True |
| [ -g FILE ] | 文件如果存在并且文件或目录的设置-组-ID(sgid)标记被设置返回True，如果一个目录的sgid标志被设置，在这个目录下创建的文件都属于拥有此目录的用户组，而不必是创建文件的用户所属的组。这个特性对在一个工作组里的同享目录很有用处 |
| [ -h FILE ] | 文件如果存在并且是个符号链接返回True |
| [ -k FILE ] | 文件如果存在返回True and 粘住位设置。俗称“粘性位”，保存文本模式标志是一种特殊类型的文件权限。如果一个文件设置了这个标志，该文件将被保存在缓存内存中，以便更快地访问。〔2〕如果设置在目录上，则限制写入权限。设置粘性位将T添加到文件或目录列表的权限(drwxrwxrw**t**)，如果用户不拥有设置了粘性位的目录，但在该目录中具有写权限，则他只能删除其拥有的目录中的文件。这样可以防止用户在公共可访问目录（如/tMP）中无意中覆盖或删除彼此的文件 |
| [ -p FILE ] | 文件如果存在并且文件是一个管道返回True |
| [ -r FILE ] | 文件如果存在并且文件是否可读返回True(指运行这个测试命令的用户的读权限) |
| [ -s FILE ] | 文件如果存在并且文件大小大于0返回True |
| [ -t FD ] | 如果一个文件描述符FD(对应函数的返回值)打开并引用了终端则为True，文件(描述符)与一个终端设备相关，这个测试选项可以用于检查脚本中是否标准输入 ([ -t 0 ])或标准输出([ -t 1 ])是一个终端 |
| [ -u FILE ] | 文件如果存在返回True，文件的设置-用户-ID(suid)标志被设置，一个root用户拥有的二进制执行文件如果设置了设置-用户-ID位(suid)标志普通用户可以以root权限运行。[1] 这对需要存取系统硬件的执行程序（比如说pppd和cdrecord）很有用。如果没有设置suid位，则这些二进制执行程序不能由非root的普通用户调用。被设置了suid标志的文件在权限列中以s标志表示(-rw**s**r-xr-t) |
| [ -w FILE ] | 文件如果存在并且可写的返回True(运行这个测试命令的用户的写权限) |
| [ -x FILE ] | 文件如果存在并且是可执行的返回(测试命令的用户的执行权限) |
| [ -O FILE ] | 文件如果存在并且由有效用户ID所拥有的返回True(你是文件拥有者) |
| [ -G FILE ] | 文件如果存在并且由用户组ID所拥有的返回True(你所在的组和文件的groupID一致) |
| [ -L FILE ] | 文件如果存在并且是一个符号链接返回True |
| [ -N FILE ] | 文件如果存在并且最后一次读后被修改返回True |
| [ -S FILE ] | 文件如果存在并且是一个socket返回True |
| [ FILE1 -nt FILE2 ] | 如果FILE1比FILE2更新，或者FILE1存在且FILE2不存在，则为True |
| [ FILE1 -ot FILE2 ] | 如果FILE1比FILE2旧，或者FILE2存在且FILE1不存在，则为True |
| [ FILE1 -ef FILE2 ] | 如果FILE1和FILE2引用相同的设备和inode编号(相同的文件硬链接)，则为True |
| [ -o OPTIONNAME ] | 如果启用了shell选项“OPTIONNAME”(选项名称)，则为True |
| [ -z STRING ] | 如果字符串为0则为True |
| [ -n STRING ] or [ STRING ] | 如果字符串不为0则为True |
| [ STRING1 == STRING2 ] | 如果字符串相等则为True，对于严格的POSIX合规性，可以使用“=”代替“==” |
| [ STRING1 != STRING2 ] | 如果字符串不相等则为True |
| [ STRING1 < STRING2 ] | 如果“STRING1”在当前locale环境中按字典顺序在“STRING2”之前排序，则为True |
| [ STRING1 > STRING2 ] | 如果“STRING1”在当前locale环境中按字典顺序在“STRING2”之后排序，则为True |
| [ ARG1 OP ARG2 ] | “OP”是-eq，-ne，-lt，-le，-gt或-ge之一。 如果“ARG1”分别等于，不等于，小于，小于或等于，大于或大于或等于“ARG2”，则这些算术二元运算符返回true。 “ARG1”和“ARG2”是整数 |

可以使用以下运算符组合表达式，按优先级递减顺序列出：
结合表达式
| 类别 | 例子 |
| :------: | :------: |
| [ ! EXPR ] | "非" -- 反转上面所有测试的结果(如果没有给出条件则返回真),如果EXPR是false则为真 |
| [ ( EXPR ) ] | 返回EXPR的值。 这可以用于覆盖运算符的正常优先级 |
| [ EXPR1 -a EXPR2 ] | 如果EXPR1和EXPR2都为真，则为真 |
| [ EXPR1 -o EXPR2 ] | 如果EXPR1或EXPR2为真，则为真 |

大多数程序员更喜欢使用test内置命令，这相当于使用方括号进行比较，如下所示

```sh
test "$(whoami)" != 'root' && (echo you are using a non-privileged account; exit 1)

如果在子shell中调用exit，则不会将变量传递给父级。如果您不希望Bash派生子shell，请使用{ }代替()
```

### if语句[ ] [[ ]]比较

[] vs [[]]

Contrary to [, [[ prevents word splitting of variable values. So, if VAR="var with spaces", you do not need to double quote $VAR in a test - eventhough using quotes remains a good habit. Also, [[ prevents pathname expansion, so literal strings with wildcards do not try to expand to filenames. Using [[, == and != interpret strings to the right as shell glob patterns to be matched against the value to the left, for instance: [[ "value" == val* ]]

| 测试类型 | [ ] | [[ ]] |
| :------: | :------: | :------: |
| 数字测试 | -eq -ne -lt -le -gt -ge | 同[ ] |
| 文件测试 | -r -l -w -x -f -d -s -nt -ot | 同[ ] |
| 字符测试 | = != -n -z ==(同=) 不可使用<= >= | 同[ ] |
| 逻辑测试 | -a -o ! | && \|\| ! |
| 数学运算 | 不可以使用 | + - * / % |
| 组合 | 用各自逻辑符号连接的数字(运算)测试、文件测试、字符测试 | 同[ ] |

情况较复杂的字符测试

| 字符测试 | [ ] | [[ ]] |
| :------: | :------: | :------: |
| < > | 比较结果异常，如:$[ 12 > 21 ]与$[ 21 > 12 ]、$[ a > b ]与$[ b > a ]的返回值一样 | 根据对于ASCII码进行比较(若多个字母和数字组合，则先比较首个，相同再比较下一个) |
| \\> \\< | 根据对于ASCII码进行比较(若多个字母和数字组合，则先比较首个，相同再比较下一个) | 不可以使用 |

### if/then/else构造

```sh
# 测试断掉的连接

#!/bin/bash
# broken-link.sh
# 由Lee bigelow所写<ligelowbee@yahoo.com>

#一个用于发现死符号链接并且输出它们的链接文件的纯shell的脚本。
#所以它们能把输出提供给xargs并处理 :)
#例如： broken-link.sh /somedir /someotherdir|xargs rm
#
#下面是更好的发现死符号链接的办法:
#
#find "somedir" -type l -print0|\
#xargs -r0 file|\
#grep "broken symbolic"|
#sed -e 's/^\|: *broken symbolic.*$/"/g'
#
#但这不是纯bash脚本，下面的则是.
#注意: 谨防在/proc文件系统和死循环链接中使用!
###############################################


#如果没有参数被传递给脚本作为搜索目录，则使用当前目录
#
#
###############
[ $# -eq 0 ] && directorys=`pwd` || directorys=$@

#Setup the function linkchk to check the directory it is passed
#for files that are links and don't exist, then print them quoted.
#If one of the elements in the directory is a subdirectory then
#send that send that subdirectory to the linkcheck function.
########
linkchk () {
    for element in $1/*; do
    [ -h "$element" -a ! -e "$element" ] && echo \"$element\"
    [ -d "$element" ] && linkchk $element
    # Of course, '-h' tests for symbolic link, '-d' for directory.
    done
}

#Send each arg that was passed to the script to the linkchk function
#if it is a valid directoy.  If not, then print the error message
#and usage info.
############
for directory in $directorys; do
    if [ -d $directory ]
    then linkchk $directory
    else
        echo "$directory is not a directory"
        echo "Usage: $0 dir1 dir2 ..."
    fi
done

[1] 一定要意识到suid二进制文件可能引起潜在的安全漏洞，并且在shell脚本中suid标志已经不起作用了。
[2] 在现代UNIX操作系统，粘住位已经不在文件中再使用，只用在目录中

```

### then语句后面的命令

then语句后面的CONSEQUENT-COMMANDS列表可以是任何有效的UNIX命令，任何可执行程序，任何可执行shell脚本或任何shell语句，但结束fi除外。重要的是要记住，then和fi被认为是shell中的分隔语句。因此，在命令行上发出时，它们用分号分隔。

```sh
#第一个示例检查文件是否存在
#!/bin/bash

echo "This scripts checks the existence of the messages file."
echo "Checking..."
if [ -f /var/log/messages ]
  then
    echo "/var/log/messages exists."
fi
echo
echo "...done."
```

```sh
一个if/then结构测试一列命令的退出状态是否为0（因为依照惯例，0意味着命令执行成功），如果是0则会执行一个或多个命令。

有一个命令 [ (左方括是特殊字符). 它和test是同义词,因为效率的原因，它被内建在shell里。这个命令的参数是比较表达式或者文件测试，它会返回一个退出状态指示比较的结果(0表示真，1表示假)。

#一个if/then结构能包含嵌套的比较和测试。
if echo "Next *if* is part of the comparison for the first *if*."

  if [[ $comparison = "integer" ]]
    then (( a < b ))
  else
    [[ $a < $b ]]
  fi

then
  echo '$a is less than $b'
fi

#if基本结构
if [ condition-true ]
then
   command 1
   command 2
   ...
else
   # 或选的(如果不需要就可去掉).
   # 如果条件测试失败，就在这里加入默认的执行命令.
   command 3
   command 4
   ...
fi

当if和then在同一行的时候，一个分号（;）必须用在if语句的结尾。if和then都是关键字.关键字（或命令）开始一个语句，如果在同一行开始另一个新语句时，前面一个语句必须用分号（;）结束。
if [ -x "$filename" ]; then

#等价的测试命令：test,/usr/bin/test,[]和/usr/bin/[
#!/bin/bash

echo

if test -z "$1"
then
  echo "No command-line arguments."
else
  echo "First command-line argument is $1."
fi

echo

if /usr/bin/test -z "$1"      # 和内建的"test"命令一样.
then
  echo "No command-line arguments."
else
  echo "First command-line argument is $1."
fi

echo

if [ -z "$1" ]                # 和上面代码块的功能一样
#   if [ -z "$1"                应该来说会运行, 但是...
#+  Bash给出错误说少了一个封闭的右方括.
then
  echo "No command-line arguments."
else
  echo "First command-line argument is $1."
fi

echo


if /usr/bin/[ -z "$1" ]       # 同样和上面的代码块一样.
# if /usr/bin/[ -z "$1"       # 工作, 但还是给出一个错误信息.
#                             # 注意:
#                               这个已经在bash 3.x版本被修补好了。
then
  echo "No command-line arguments."
else
  echo "First command-line argument is $1."
fi

在[[和]]之间的所有的字符都不会被文件扩展或是标记分割，但是会有参数引用和命令替换。
file=/etc/passwd

if [[ -e $file ]]
then
  echo "Password file exists."
fi

用[[ ... ]]测试结构比用[ ... ]更能防止脚本里的许多逻辑错误。比如说，&&,||,<和>操作符能在一个[[]]测试里通过，但在[]结构会发生错误。

在一个if的后面，不必一定是test命令或是test结构（[]或是[[]]）。
dir=/home/bozo

if cd "$dir" 2>/dev/null; then   # "2>/dev/null"会隐藏错误的信息.
  echo "Now in $dir."
else
  echo "Can't change to $dir."
fi
"if COMMAND"结构会返回COMMAND命令的退出状态码。

同样的，在一个测试方括号里面的条件测试也可以用列表结构(list construct)而不必用if。
var1=20
var2=22
[ "$var1" -ne "$var2" ] && echo "$var1 is not equal to $var2"

home=/home/bozo
[ -d "$home" ] || echo "$home directory does not exist."

(( ))结构扩展并计算一个算术表达式的值。如果表达式值为0，会返回1或假作为退出状态码。一个非零值的表达式返回一个0或真作为退出状态码。这个结构和先前test命令及[]结构的讨论刚好相反。

#用(( ))进行算术测试
#!/bin/bash
# 算术测试.

# (( ... ))结构会求值并测试该值。
# 退出状态码与[ ... ]结构正好相反!

(( 0 ))
echo "Exit status of \"(( 0 ))\" is $?."         # 1

(( 1 ))
echo "Exit status of \"(( 1 ))\" is $?."         # 0

(( 5 > 4 ))                                      # 真
echo "Exit status of \"(( 5 > 4 ))\" is $?."     # 0

(( 5 > 9 ))                                      # 假
echo "Exit status of \"(( 5 > 9 ))\" is $?."     # 1

(( 5 - 5 ))                                      # 0
echo "Exit status of \"(( 5 - 5 ))\" is $?."     # 1

(( 5 / 4 ))                                      # 除法有效.
echo "Exit status of \"(( 5 / 4 ))\" is $?."     # 0

(( 1 / 2 ))                                      # 除法计算结果< 1
echo "Exit status of \"(( 1 / 2 ))\" is $?."     # 截取为0.
                                                 # 1

(( 1 / 0 )) 2>/dev/null                          # 除以0的非法计算.
#           ^^^^^^^^^^^
echo "Exit status of \"(( 1 / 0 ))\" is $?."     # 1

```

### 其他比较操作符

```sh
二元比较操作符比较两个变量或是数值。注意整数和字符串比较的分别。

整数比较

-eq

等于

if [ "$a" -eq "$b" ]

-ne
不等于

if [ "$a" -ne "$b" ]

-gt
大于

if [ "$a" -gt "$b" ]

-ge
大于等于

if [ "$a" -ge "$b" ]

-lt
小于

if [ "$a" -lt "$b" ]

-le
小于等于

if [ "$a" -le "$b" ]

<
小于(在双括号里使用)

(("$a" < "$b"))

<=
小于等于 (在双括号里使用)

(("$a" <= "$b"))

>
大于 (在双括号里使用)

(("$a" > "$b"))

>=
大于等于(在双括号里使用)

(("$a" >= "$b"))

字符串比较

=

等于

if [ "$a" = "$b" ]

==
等于

if [ "$a" == "$b" ]

它和=是同义词。

==比较操作符在一个双方括号测试和一个单方括号号里意思不同。
[[ $a == z* ]]    # 如果变量$a以字符"z"开始(模式匹配)则为真.
[[ $a == "z*" ]]  # 如果变量$a与z*(字面上的匹配)相等则为真.

[ $a == z* ]      # 文件扩展和单元分割有效.
[ "$a" == "z*" ]  # 如果变量$a与z*(字面上的匹配)相等则为真.

!=
不相等

if [ "$a" != "$b" ]

操作符在[[ ... ]]结构里使用模式匹配.

<
小于，依照ASCII字符排列顺序

if [[ "$a" < "$b" ]]

if [ "$a" \< "$b" ]

注意"<"字符在[ ] 结构里需要转义

>
大于，依照ASCII字符排列顺序

if [[ "$a" > "$b" ]]

if [ "$a" \> "$b" ]

注意">"字符在[ ] 结构里需要转义.

参考例子 26-11 中这种比较的一个应用.

-z
字符串为"null"，即是指字符串长度为零。

-n
字符串不为"null"，即长度不为零.

在测试方括号里进行-n测试时一定要把字符串用引号起来。用没有引号引起的! -z或者在方括号里只有未引号引起的字符串 (参考例子 7-6)来进行测试一般都能工作，然而，这其实是不安全的测试。应该总是用引号把测试字符串引起来。[1]
[1] 就像S.C.指出的那样，在一个混合测试中，把一个字符串变量引号引起来可能还不够。如果$string变量是空的话，表达式[ -n "$string" -o "$a" = "$b" ]在一些Bash版本中可能会引起错误。安全的办法是附加一个外部的字符串给可能有空字符串变量比较的所有变量，[ "x$string" != x -o "x$a" = "x$b" ](x字符可以互相抵消)。

#算术和字符串比较
#!/bin/bash

a=4
b=5

#  这儿的"a"和"b"既能被看成是整数也能被看着是字符串。
#  在字符串和数字之间不是严格界定的，
#+ 因为Bash变量不是强类型的。

#  Bash允许变量的整数操作和比较，
#+ 但这些变量必须只包括数字字符.
#  不管怎么样，你应该小心考虑.

echo

if [ "$a" -ne "$b" ]
then
  echo "$a is not equal to $b"
  echo "(arithmetic comparison)"
fi

echo

if [ "$a" != "$b" ]
then
  echo "$a is not equal to $b."
  echo "(string comparison)"
  #     "4"  != "5"
  # ASCII 52 != ASCII 53
fi
# 在这个实际的例子中,"-ne"和"!="都能工作


#测试一个字符串是否是null值
#!/bin/bash
#  str-test.sh: 测试null字符串和没有用引号引起的字符串,
#+ but not strings and sealing wax, not to mention cabbages and kings . .

# 用if [ ... ]结构


# 如果一个字符串变量没有被初始化，它里面的值未定义。
# 这种情况称为"null"值(不同于零).

if [ -n $string1 ]    # $string1没有被声明或初始化.
then
  echo "String \"string1\" is not null."
else  
  echo "String \"string1\" is null."
fi  
# 错误结果.
# Shows $string1 as not null, although it was not initialized.


echo


# 让我们再试试.

if [ -n "$string1" ]  # 这次, $string1变量被引号引起.
then
  echo "String \"string1\" is not null."
else  
  echo "String \"string1\" is null."
fi                    # 在一个测试方括里用引号引起变量!


echo


if [ $string1 ]       # 这次,变量$string1什么也不加.
then
  echo "String \"string1\" is not null."
else  
  echo "String \"string1\" is null."
fi  
# This works fine.
# The [ ] test operator alone detects whether the string is null.
# However it is good practice to quote it ("$string1").
#
# As Stephane Chazelas points out,
#    if [ $string1 ]    has one argument, "]"
#    if [ "$string1" ]  has two arguments, the empty "$string1" and "]"

string1=initialized

if [ $string1 ]       # Again, $string1 stands naked.
then
  echo "String \"string1\" is not null."
else  
  echo "String \"string1\" is null."
fi  
# Again, gives correct result.
# Still, it is better to quote it ("$string1"), because . . .


string1="a = b"

if [ $string1 ]       # Again, $string1 stands naked.
then
  echo "String \"string1\" is not null."
else  
  echo "String \"string1\" is null."
fi  
# Not quoting "$string1" now gives wrong result!


#例子：zmore
#!/bin/bash
# zmore

#用'more'查看被压缩过的文件

NOARGS=65
NOTFOUND=66
NOTGZIP=67

if [ $# -eq 0 ] # 等同于:  if [ -z "$1" ]
# $1可以存在,但为空:  zmore "" arg2 arg3
then
  echo "Usage: `basename $0` filename" >&2
  # 错误信息打印到标准错误.
  exit $NOARGS
  # 返回65作为脚本的退出状态(错误码).
fi  

filename=$1

if [ ! -f "$filename" ]   # Quoting $filename allows for possible ces.
then
  echo "File $filename not found!" >&2
  # 错误信息打印到标准错误.
  exit $NOTFOUND
fi  

if [ ${filename##*.} != "gz" ]
# 在方括号里使用变量替换.
then
  echo "File $1 is not a gzipped file!"
  exit $NOTGZIP
fi  

zcat $1 | more

# 用过滤程序'more.'
# 如果你想，也可以用'less'代替。

exit $?   # 脚本返回管道的退出状态
# 实际上"exit $?"是多余的,因为在任何情况下，
# 脚本会返回最后一个执行命令的退出码。


#混合比较
-a
逻辑与

如果exp1和exp2都为真，则exp1 -a exp2返回真.

-o
逻辑或

只要exp1和exp2任何一个为真，则exp1 -o exp2 返回真.

它们和Bash中用于双方括号结构的比较操作符&&和||很相似。
   1 [[ condition1 && condition2 ]]
-o和-a操作符和test命令或单方括号一起使用。
   1 if [ "$exp1" -a "$exp2" ]
```

### 嵌套的fi/then条件语句

```sh
使用if/then结构的测试可以嵌套。最终的结果和使用上一节的&&混合比较操作符一样。
if [ condition1 ]
then
  if [ condition2 ]
  then
    do-something  # But only if both "condition1" and "condition2" valid.
   fi  
 fi


#检测你对测试命令掌握
一个xinitrc系统文件能用来启动一个X服务器。这个文件包含了相当多的if/then测试，就像下面这个文件的一个摘录展示的一样。
if [ -f $HOME/.Xclients ]; then
  exec $HOME/.Xclients
elif [ -f /etc/X11/xinit/Xclients ]; then
  exec /etc/X11/xinit/Xclients
else
     # 失败后的安全设置。　虽然我们决不该执行到这儿
     # (我们也在X客户提供可靠保证) 它不能被破坏.
     xclock -geometry 100x100-5+5 &
     xterm -geometry 80x50-50+150 &
     if [ -f /usr/bin/netscape -a -f /usr/share/doc/HTML/index.html ]; then
             netscape /usr/share/doc/HTML/index.html &
     fi
fi

解释上面摘录的test结构，然后检查整个/etc/X11/xinit/xinitrc文件并分析那儿的所有if/then测试结构。xubuntu在(/etc/xdg/xfce4/xinitrc)路径下

```

### 操作符

```sh
赋值

变量赋值
初始化或改变一个变量的值

=
通用的变量赋值操作符，可以用于数值和字符串的赋值
var=27
category=minerals  # "="字符后面不能加空白字符.

不要把"="赋值操作符和=测试操作符搞混了。
#    = 用于测试操作符

if [ "$string1" = "$string2" ]
# if [ "X$string1" = "X$string2" ] 会更安全,
# 它为了防止其中有一个字符串为空时产生错误信息.
# (增加的"X"字符可以互相抵消.)
then
   command
fi

计算操作符

+
加

-
减

*
乘

/
除

**
求幂
# Bash在版本2.02引入了"**"求幂操作符.
let "z=5**3"
echo "z = $z"   # z = 125

%
求模（它返回整数整除一个数后的余数）

expr 5 % 3
2

5/3 = 1 余 2

This operator finds use in, among other things, generating numbers within a specific range (see Example 9-24 and Example 9-27) and formatting program output (see Example 26-15 and Example A-6). It can even be used to generate prime numbers, (see Example A-16). Modulo turns up surprisingly often in various numerical recipes.

#求最大公约数
#!/bin/bash
# gcd.sh: 最大公约数
#         用Euclid运算法则
#  两个整数的"最大公约数"
#+ 是能被这两个整数整除的大最整数.
#  Euclid运算法则采用逐次除法.
#  每一次都重新赋值,
#+ 被除数 <---  除数
#+ 除数  <---  余数
#+ 直到 余数 = 0.
#+ 最后被传递的值中：最大公约数 = 被除数.
#
#  关于Euclid运算法则的讨论有一个出色的讨论,
#  访问Jim Loy的网站, http://www.jimloy.com/number/euclids.htm.
# ------------------------------------------------------
# 参数检查
ARGS=2
E_BADARGS=65
if [ $# -ne "$ARGS" ]
then
  echo "Usage: `basename $0` first-number second-number"
  exit $E_BADARGS
fi
# ------------------------------------------------------
gcd ()
{
      dividend=$1                    #  随意赋值.
  divisor=$2                     #+ 这里在两个参数赋大的还是小的都没有关系.
                                 #  为什么?
  remainder=1                    #  如果在循环中使用未初始化的变量,
                                 #+ 在循环中第一个传递值会使它返回一个错误信息
                                 #
  until [ "$remainder" -eq 0 ]
  do
    let "remainder = $dividend % $divisor"
    dividend=$divisor            # 现在用最小的两个数字来重复.
    divisor=$remainder
  done                           # Euclid运算法则
}                                # 最后的$dividend变量值就是最大公约数.
gcd $1 $2
echo; echo "GCD of $1 and $2 = $dividend"; echo
# 练习:
# --------
#  检测命令行参数以确保它们是整数,
#+ 如果不是整数则给出一个适当的错误信息并退出脚本.
exit 0
+=
"加-等(plus-equal)" (把原变量值增加一个常量并重新赋值给变量)

let "var += 5"会使变量var值加了5并把值赋给var.

-=
"(减-等)minus-equal" (把原变量值减少一个常量并重新赋值给变量)

*=
"(乘-等)times-equal" (把原变量值乘上一个常量并重新赋值给变量)

let "var *= 4" 使变量var的值乘上4并把值赋给var.

/=
"(除-等)slash-equal" (把原变量值除以一个常量并重新赋值给变量)

%=
"(模-等)mod-equal" (把原变量值除以一个常量整除（译者注：即取模）并重新赋余数的值给变量)

计算操作符常常出现在expr或let命令的表达式中.

例子 8-2. 使用计算操作符

#!/bin/bash
# 用10种不同的方法计数到11.

n=1; echo -n "$n "

let "n = $n + 1"   # let "n = n + 1"也可以.
echo -n "$n "

: $((n = $n + 1))
#  ":"是需要的，
#+ 否则Bash会尝试把"$((n = $n + 1))"作为命令运行.
echo -n "$n "

(( n = n + 1 ))
#  上面是更简单的可行的办法.
#  多谢David Lombard指出这一点.
echo -n "$n "

n=$(($n + 1))
echo -n "$n "

: $[ n = $n + 1 ]
#  ":"是需要的，
#+ 否则Bash会尝试把"$[ n = $n + 1 ]"作为命令运行.
#  即使"n"被当作字符串来初始化也能工作.
echo -n "$n "

n=$[ $n + 1 ]
#  即使"n"被当作字符串来初始化也能工作.
#* 应避免这种使用这种结构,因为它是被废弃并不可移植的.
#  多谢Stephane Chazelas.
echo -n "$n "

# 现在是C风格的增加操作.
# 多谢Frank Wang指出这一点.

let "n++"          # let "++n"也可以.
echo -n "$n "

(( n++ ))          # (( ++n )也可以.
echo -n "$n "

: $(( n++ ))       # : $(( ++n ))也可以.
echo -n "$n "

: $[ n++ ]         # : $[ ++n ]]也可以.
echo -n "$n "

echo

exit 0

Bash中的整数变量实际上是有符号的长整数(32位)，它的范围在-2147483648至2147483647之间。如果有在此范围限制之外的操作将会得到一个错误的结果。
a=2147483646
echo "a = $a"      # a = 2147483646
let "a+=1"         # 把变量"a"的值自增一.
echo "a = $a"      # a = 2147483647
let "a+=1"         # 再自增"a"一次,超过这个限制.
echo "a = $a"      # a = -2147483648
                   #      错误 (溢出)
到2.05b版本为止，Bash支持64位的整数。

Bash不能处理浮点计算。它会把含有小数点的数当成字符串。
a=1.5

let "b = $a + 1.3"  # 错误
# t2.sh: let: b = 1.5 + 1.3: syntax error in expression (error token is 5 + 1.3") 意为表达式错误(错误的符号".5 + 1.3")

echo "b = $b"       # b=1
在脚本中用bc需要浮点计算或数学库函数的支持。

位操作符. 位操作符很少在脚本中使用。他们主要用于操作和测试从端口或sockets中读到的数据。“位运算”更多地用于编译型的语言，比如说C和C++，它们运行起来快地像飞。

位操作符

<<
位左移（每移一位相当乘以2）

: <<comment

<<=
"位左移赋值"

comment

let "var <<= 2" 结果使var的二进制值左移了二位（相当于乘以4）

>>
位右移（每移一位相当除以2）

: <<comment

>>=
"位右移赋值"（和<<=相反）

comment

&
位与

&=
"位于赋值"

|
位或

|=
"位或赋值"

~
位反

!
位非

^
位或

^=
"位或赋值"

逻辑操作符

&&
逻辑与

if [ $condition1 ] && [ $condition2 ]
# 等同于:  if [ $condition1 -a $condition2 ]
# 如果condition1和condition2都为真则返回真...

if [[ $condition1 && $condition2 ]]    # Also works.
# 注意&&操作不能在[ ... ]结构中使用.

依据上下文，&&也可以在与列表(and list)连接命令中。

||
逻辑或

if [ $condition1 ] || [ $condition2 ]
# 等同于:  if [ $condition1 -o $condition2 ]
# 如果condition1和condition2有一个为真则返回真...
if [[ $condition1 || $condition2 ]]    # Also works.
# 注意||操作不能在[ ... ]结构中使用.

Bash测试由逻辑操作符连接起来的每一个表达式的退出状态。


#使用&&和||进行混合条件测试
#!/bin/bash

a=24
b=47

if [ "$a" -eq 24 ] && [ "$b" -eq 47 ]
then
  echo "Test #1 succeeds."
else
  echo "Test #1 fails."
fi

# 错误:   if [ "$a" -eq 24 && "$b" -eq 47 ]
#+         这会尝试执行' [ "$a" -eq 24 '
#+         然后会因没找到匹配的']'而失败.
#
#  注意:  if [[ $a -eq 24 && $b -eq 24 ]]也可以.
#  双方括号的if-test比
#+ 单方括号的结构更灵活
#    (第17行和第6行的"&&"有不同的意思.)
#    多谢Stephane Chazelas指出这一点.


if [ "$a" -eq 98 ] || [ "$b" -eq 47 ]
then
  echo "Test #2 succeeds."
else
  echo "Test #2 fails."
fi


#  -a和-o选项提供
#+ 混合条件测试另一个选择.
#  多谢Patrick Callahan指出这一点.


if [ "$a" -eq 24 -a "$b" -eq 47 ]
then
  echo "Test #3 succeeds."
else
  echo "Test #3 fails."
fi


if [ "$a" -eq 98 -o "$b" -eq 47 ]
then
  echo "Test #4 succeeds."
else
  echo "Test #4 fails."
fi


a=rhino
b=crocodile
if [ "$a" = rhino ] && [ "$b" = crocodile ]
then
  echo "Test #5 succeeds."
else
  echo "Test #5 fails."
fi

exit 0
在算术计算的环境中，&&和||操作符也可以使用。

echo $(( 1 && 2 )) $((3 && 0)) $((4 || 0)) $((0 || 0))
1 0 1 0

杂合的其他操作符

,   逗号操作符

逗号操作符连接两个或更多的算术操作。所有的操作都被求值(可能会有副作用)，但只返回最后一个操作的结构
let "t1 = ((5 + 3, 7 - 1, 15 - 4))"
echo "t1 = $t1"               # t1 = 11

let "t2 = ((a = 9, 15 / 3))"  # 初始化"a"并求"t2"的值.
echo "t2 = $t2    a = $a"     # t2 = 5    a = 9

逗号操作符主要用在for 循环里.

```

### 数字常量

```sh
除非一个数字有特别的前缀或符号，否则shell脚本把它当成十进制的数。一个前缀为0的数字是八进制数。一个前缀为0x的数字是十六进制数。一个数用内嵌的#来求值则看成BASE#NUMBER(有范围和符号限制)(译者注：BASE#NUMBER即：基数#数值，参考下面的例子)

#数字常量的表示法
#!/bin/bash
# numbers.sh: 不同基数的数字表示法.

# 十进制数: 它是默认的
let "dec = 32"
echo "decimal number = $dec"             # 32
# 这儿没有什么特别的.


# 八进制数: 以'0'(零)为前缀
let "oct = 032"
echo "octal number = $oct"               # 26
# 结果表示为十进制.
# --------- ------ -- -------

# 十六进制: 以'0x'或'0X'为前缀
let "hex = 0x32"
echo "hexadecimal number = $hex"         # 50
# 以十进制的形式表示.

# 其他的进制的表示形式: BASE#NUMBER
# BASE值在2和64之间.
# NUMBER必须使用在BASE范围内的符号,看下面的示例.


let "bin = 2#111100111001101"
echo "binary number = $bin"              # 31181

let "b32 = 32#77"
echo "base-32 number = $b32"             # 231

let "b64 = 64#@_"
echo "base-64 number = $b64"             # 4031
# 这个符号只能工作在ASCII码值为2-64的范围限制.
# 10个数字+26个小写字母+26个大写字母+ @ + _


echo

echo $((36#zz)) $((2#10101010)) $((16#AF16)) $((53#1aA))
# 1295 170 44822 3375


#  重要提示:
#  --------------
#  使用一个超出给定进制的数字将会引起一个错误信息。
#+ gives an error message.

let "bad_oct = 081"
# ((部分的) 错误信息输出:
#  bad_oct = 081: value too great for base (error token is "081")
# 八进制数字只能使用数字0-7.





```

### read内置命令是echo和printf命令的对应命令

读取内置的选项

| 选项 | 含义 |
| :------: | :------: |
| -a ANAME | The words are assigned to sequential indexes of the array variable ANAME, starting at 0 All elements are removed from ANAME before the assignment Other NAME arguments are ignored |
| -d DELIM | The first character of DELIM is used to terminate the input line, rather than newline |
| -e | readline is used to obtain the line |
| -n NCHARS | read returns after reading NCHARS characters rather than waiting for a complete line of input |
| -p PROMPT | Display PROMPT, without a trailing newline, before attempting to read any input The prompt is displayed only if input is coming from a terminal |
| -r | If this option is given, backslash does not act as an escape character The backslash is considered to be part of the line In particular, a backslash-newline pair may not be used as a line continuation |
| -s | Silent mode If input is coming from a terminal, characters are not echoed |
| -t TIMEOUT | Cause read to time out and return failure if a complete line of input is not read within TIMEOUT seconds This option has no effect if read is not reading input from the terminal or from a pipe |
| -u FD | Read input from file descriptor FD |
