# shell笔记

bash shell本身不支持正则表达式，使用正则的是shell命令和工具，如grep、sed等。bash shell可以使用正则表达式中的一些元字符实
现通配(Globbing)功能。通配就是把一个包含通配符的非具体文件名扩展存储在计算机或网络上的一批具体文件名的过程

注意事项：

* 不要使用for循环读取文件的行。请改用while读取循环
* cat file | grep pattern   不要使用此方式，而应该使用grep 'pattern' file | sed 'expression'
* 使用$(...)取代`...`
* bash测试时使用[[]]代替[]更安全
* ls -l | awk '{ print $8 }' 不要解析ls命令的输出，ls输出并非很靠谱
* for number in $(seq 1 10); do 不要使用seq计数
* i=`expr $i + 1` 不要使用expr，用i++代替

* Reference List

  * Bash Guide for Beginners
  * Advanced Bash-Scripting Guide

## shell多行注释实现方式

```bash

方法一：
: << 字符(一般使用comment便于识别)
语句
字符

方法二：
: '
语句
'
```

## EOF常见用法

```bash
# 编写SHELL显示多个信息
cat <<EOF
+--------------------------------------------------------------+
|         === Welcome to Tunoff services ===                   |
+--------------------------------------------------------------+
EOF

# 将mongodbrepo写入到mongodb.repo文件
cat << EOF > /etc/yum.repos.d/mongodb.repo
[mongodb-org-3.4]
name=MongoDB 3.4 Repository
baseurl=https://mirrors.aliyun.com/mongodb/yum/redhat/\$releasever/mongodb-org/3.4/\$basearch/
gpgcheck=0
enabled=1
EOF

```

## shell特殊变量基本解释

```markdown

$*     # 传递给程序的所有参数组成的字符串
$@     # (常用)传递给程序的所有参数，和$*类似，但每个都用双括号括起，且仍保留每个引用变量的区段含义
$#     # (常用)传递给程序的总的参数数目
$*     # (常用)传递给程序的所有参数组成的字符串
$_     # 获取在此之前执行的命令或脚本的最后一个参数
$?     # (常用)上一条命令执行后返回值;成功返回0,失败返回1

# 返回值参考：
0      # 表示运行成功
2      # 权限拒绝
1~125  # 运行失败、脚本命令、系统命令错误或参数传递错误
126    # 找到该命令但无法执行
127    # 未找到要运行的命令
>128   # 命令被系统强制结束
$$     # 当前shell运行时的进程ID
$!     # 上一个子进程的进程号
$@     # 所有的参数，每个都用双括号括起
$n     # (常用)位置参数值，n表示位置，获取当前脚本第n个参数值，n大于9时要用大括号，例${10}，接的参数以空格隔开
$0     # (常用)当前shell名(当前执行进程的进程名，如果执行脚本包含路径则返回也包含脚本路径)

# 数值比较：

-eq     # 等于
-ne     # 不等于
-gt     # 大于
-ge     # 大于或等于
-lt     # 小于
-le     # 小于或等于

# 逻辑测试：
&&      # 逻辑与
||      # 逻辑或
!       # 逻辑否
```

## 在shell中常用的符号一般用法

```markdown
# ;(Command separator)分号
在shell中担任"连续指令"功能的符号就是分号,分号符允许在同一行有两个或多个命令
例如：cd ~/backup;mkdir startup;cp ~/.* startup/.  # 进入家目录下backup并创建目录startup，复制家目录下所有到startup下

# ;;(Terminator)连续分号
专用在case流程控制选项，担任Terminator角色。case语句分支的结束符使用双分号，如下：
case "$variable" in
abc) echo "\$variable = abc" ;;
xyz) echo "\$variable = xyz" ;;
esac

# .(dot)英文句号
shell中一个dot代表当前目录，两个代表上层目录
正则表达式中则代表一个字符
作为命令使用时等同于source
文件名前缀使用时象征为隐藏文件

# 'string'(single quote)单引号
单引号括住的内容将被视为单一字符串，且$符号也被当做一般字符处理

# "string"(double quote)双引号
双引号括住的内容将被视为单一字符串，可以防止通配符扩展，但是允许变量扩展。此处和单引号不同

# `command`(backticks)倒引号
倒引号内的command会被当做命令来执行,最好使用$(command)来代替

# ,(comma)英文逗号
此符号常用在运算中，用作区域隔离。例如：
let "t1 = ((a = 5 + 3, b = 7 - 1, c = 15 - 4))";echo "t1 = $t1, a = $a, b = $b"

# /(forward slash)斜线
路径中表示文件路径分隔符，四则运算中代表除法符号。例如：let "num1 = ((a = 10 / 2, b = 25 / 5))";echo $num1,$a,$b

# \\ 倒斜线
交互模式下的escape字符
作用：放在指令前取消aliases；放在特殊符号前则该特殊符号不再有特殊意义；放在指令最末端表示连接下一行

# | (pipeline)管道
pipeline用来连接上个指令的标准输出作为下个指令的标准输入

# !(negate or reverse)英文感叹号
通常作为反逻辑使用，如条件测试中!=表示不等于,例如判断上个指令是否执行成功
if [ "$?" != 0 ]; then echo "Executes error"; fi
还用来调用属于历史命令机制的调用，但在脚本中命令历史机制是被禁止的

# : 英文冒号
bash中作为内建指令表示什么都不做，但返回状态值0 例如：
: ${HOSTNAME}${USER}    # 检查这两个环境变量是否已设置，没有设置则以标准错误显示错误信息

# ? (wild card)问号
正则表示匹配任意一个字符，但不包含null
在一些表达式中，作为测试操作符，问号表示一个条件测试，在双括号(())中表示C风格的三元操作符，在参数替换表达式用来测试一个变量是否被赋值

# * (wild card)星号
正则中表示任何字符，包含null；四则运算中表示乘法

# ** 幂运算
let sum=2**3;echo $sum  # 输出8

# $ (dollar sign)dollar符号
变量替换代表符号，用来引用一个变量的内容；正则中表示匹配行尾

# ${}   变量的正则表达式
bash中很多用法，详情见下文，参数替换

# ()   (command group)指令群组
用括号将一串连续指令括起来，称作指令群组，例如：(cd ~ ; var=`pwd` ;echo $var)
数组初始化：array=(element1 element2 element3)

# (())
作用和let命令相似，用在算数运算中，但执行效率略高.例如：
((a = 10));echo -e "初始值: a = $a \n";((a++));echo "a++ 后结果: a=$a"

# { }   大括号(block of code)
用法一：是作为代码块，实际是创建了一个匿名函数，与小括号不同的是不会新创建一个sub shell运行。脚本余下部分仍可使用括号内
变量，括号内命令之间用分号隔开，最后一个也必须有分号，{}第一个命令和左边的大括号必须要有一个空格。例如：
a=one; { a=two; echo -e "\n$a \n";}; echo $a

用法二：大括号拓展。(通配(globbing))将对大括号中的文件名做扩展。在大括号中，不允许有空白，除非这个空白被引用或转义。
第一种：对大括号中的以逗号分割的文件列表进行拓展。如 touch {a,b}.txt 结果为a.txt b.txt。第二种：对大括号中以点点（..）
分割的顺序文件列表起拓展作用，如：touch {a..d}.txt 结果为a.txt b.txt c.txt d.txt
还可以用在字符串组合上，如：mkdir {a,b}-{1,2} 结果为：a-1 a-2 b-1 b-2

# [] 中括号------尽量使用[[]]替代[]
与test等同，通常用作条件判断，参数作为比较表达式或文件测试，根据结果返回状态码
test和[]中比较运算符只有==和!=，用于字符串比较，整数比较只能-gt,-eq等
正则中表示一个字符范围
在一个array结构的上下文中，中括号用于引用数组中每个元素的编号

# [[]]
此括号之间任何字符都不会发生文件扩展名或者单词的分割，但会有参数扩展和命令替换
支持字符串的模式匹配，字符串比较时可以把右边当做一个模式
使用[[]]作为条件判断可防止脚本中逻辑错误，&&,||,<,>操作符可在[[]]正常使用，但在[]中会报错
例如：read ak; if [[ $ak > 5 || $ak < 2 ]]; then echo "$ak"; fi

# 输出/输入重定向操作符：> >> < << &> 2&> 2<>>& >&2
文件描述符(file descriptor）通常用一个数字(0-9)表示文件
常见描述符如下：
文件描述符       名称           缩写      默认值
    0          标准输入        stdin      键盘
    1          标准输出        stdout     屏幕
    2        标准错误输出      stderr     屏幕
简单使用<或>时，相当于0<或1>

command > file      # command命令输出重定向到file
command >> file     # command命令输出重定向追加到file
command < file      # command命令从file读取
command << file     # 从命令行读取输入，知道与file匹配的行结束，除非使用引号将输入括起来，此模式将对输入内容进行shell变量替换如果使用<<-则忽略接下来输入行首的tab
command <<< word    # 把字符word和后面的换行作为输入提供给command
command <> file     # 以读写模式将file重定向到输入，file不会被破坏
command >| file     # 和>功能相同,强迫重定向，会强制覆盖一个已存在文件
: > file            # 将file截断为0长度
command >&n         # 把输出送到文件描述符n，注意： >&实际上复制了文件描述符，使cmd > file 2>&1与cmd 2>&1 >file的效果不一样
command m>&n        # 把输出到文件符m的信息重定向到文件描述符n
command >&-         # 关闭标准输出
command <&n         # 输入来自文件描述符n
command m<&n        # m来自文件描述各个n
command <&-         # 关闭标准输入
command <&n-        # 移动输入文件描述符n而非复制它（需要解释）
&> file和 > file 2>&1相同 # 将stdout和stderr组合覆盖到file

# ~+
表示当前工作路径，与变量$PWD一致

```

### shell变量子串说明

| 表达式 | 说明 |
| :------: | :------: |
| ${parameter} | 返回变量$parameter的内容 |
| ${#parameter} | 返回变量$parameter内容的字符长度 |
| ${parameter:offset} | 在变量$parameter中，从位置offset后提取字符串到结尾 |
| ${parameter:offset:length} | 在变量$parameter中从位置offset后提取字符串长度为length的子串 |
| ${parameter#word} | 从变量$parameter开头删除最短匹配的word子串 |
| ${parameter##word} | 从变量$parameter开头删除最长匹配的word子串 |
| ${parameter%word} | 从变量$parameter结尾开始删除最短匹配的word子串 |
| ${parameter%%word} | 从变量$parameter结尾开始删除最长匹配的word子串 |
| ${parameter/pattern/string} | 使用string代替第一个匹配的pattern |
| ${parameter//pattern/string} | 使用string代替所有匹配的pattern |
| ${parameter:-word} | 如果变量parameter没有值为空或未赋值则返回word字符串作为变量的值 |
| ${parameter:=word} | 如果变量parameter没有值为空或未赋值则返回word字符串作为变量的值,位置变量和特殊变量不适用 |
| ${parameter:?word} | 如果变量parameter没有值为空或未赋值则word字符串作为标准错误输出，否则输出变量的值 |
| ${parameter:+word} | 如果变量parameter没有值为空或未赋值则什么都不做，否则word字符串替代变量的值 |

word可以表示为一串字符，比如a*c表示删除从字符a到c的所有字符

## bash退出和退出状态

```bash
: << comment
exit命令一般用于结束一个脚本，它也能返回一个值给父进程,每一个命令都能返回一个退出状态（有时也看做返回状态).一个命令执行
成功返回0，一个执行不成功的命令则返回一个非零值，此值通常可以被解释成一个对应的错误值。除了一些例外的情况，UNIX命令，程
序或是软件包执行成功都是以返回0的作为退出码

同样的，在脚本里的函数和脚本自身都会返回退出状态码。在脚本或函数里最后一个命令将决定退出状态码。在一个脚本里，exit n命令
将会返回shell一个nnn的退出状态码。（nnn必须是一个在0-255范围的十进制整数）

如果一个脚本以不带参数的exit命令结束，脚本的退出状态码将会是执行exit命令前的最后一个命令的退出码，脚本结束没有exit,不带
参数的exit和exit $?三者等价，$?变量保存了最后一个命令执行后的退出状态，当一个函数返回时，$?保存了函数里最后一个命令的退
出状态码。这就是Bash里函数返回值的处理办法。当一个脚本运行结束，$? 变量保存脚本的退出状态，而脚本的退出状态则是脚本中最
后一个已执行命令的退出状态。并且依照惯例，0表示执行成功，1-255的整数范围表示错误或反常的条件

逻辑非操作符!，反转一个命令或一个测试的结果。也能反转退出状态
comment

```

## 特殊变量类型

```bash
# 局部变量：只在代码块或一个函数中有效
# 环境变量：其会影响shell的行为和用户接口，大部分情况下，每个进程都有一个环境，其由进程使用的环境变量组成
# 分配给环境变量的总空间有限，若创建太多环境变量或有些环境变量的值太长而占用过多空间则会出错
# 例如：
: <<comment
eval "`seq 10000 | sed -e 's/.*/export var&=ZZZZZZZZZZZZZZ/'`"
du
/usr/bin/du:Argument list too long
comment
# 如果一个脚步要设置一个环境变量，则需要将其导出(export)，也就是讲需通知到脚本的环境表，这是export命令功能
# 一个脚本只能导出（export）变量到子进程，也就是说只能导出到由此脚本生成的命令或进程中。在一个命令行中运行的脚本不能导出
# 一个变量影响到命令行的环境。子进程不能导出变量到生成它的父进程中。
# 位置参数
: <<comment
命令行传递给脚本的参数是$0,$1,$2....
$0是脚本的名字，$1是第一个参数，$2是第二个参数......在位置参数$9之后的参数必须用大括号括起来。如：${10}
特殊变量$*和$@表示所有的位置参数
comment

: <<comment
#!/bin/bash
# 位置参数例子：
# 至少以10个参数运行此脚本。例如：./script.sh 1 2 3 4 5 6 7 8 9 10
min_params=10
echo "脚本名字是\"$0\"."
# 用./表示当前目录
echo "脚本名字是\"`basename $0`\"."
comment
```

## 测试

### if使用的主要表达式

| 主要的表达式 | 含义 |
| :------: | :------: |
| [ -a FILE ] | 文件如果存在返回True，与-e一样但不赞成使用 |
| [ -b FILE ] | 文件如果存在并且是块设备返回True(软盘,光驱等)|
| [ -c FILE ] | 文件如果存在并且时字符设备返回True(键盘,调制解调器,声卡等) |
| [ -d FILE ] | 如果是目录返回True |
| [ -e FILE ] | 文件如果存在返回True |
| [ -f FILE ] | 文件是普通文件并且不是一个目录或是一个设备文件返回True |
| [ -g FILE ] | 文件如果存在并且文件或目录的设置-组-ID(sgid)标记被设置则返回True，如果一个目录的sgid标志被设置，在这个<br>目录下创建的文件都属于拥有此目录的用户组，而不必是创建文件的用户所属的组。这个特性对在一个工作组里的同享目录很有用处 |
| [ -h FILE ] | 文件如果存在并且是个符号链接返回True |
| [ -k FILE ] | 文件如果存在且粘住位设置返回True。俗称“粘性位”，保存文本模式标志是一种特殊类型的文件权限。如果<br>一个文件设置了这个标志，该文件将被保存在缓存内存中，以便更快地访问。〔2〕如果设置在目录上，则限制写入权限。设置粘性<br>位将T添加到文件或目录列表的权限(drwxrwxrw**t**)，如果用户不拥有设置了粘性位的目录，但在该目录中具有写权限，则他只能<br>删除其拥有的目录中的文件。这样可以防止用户在公共可访问目录（如/tMP）中无意中覆盖或删除彼此的文件 |
| [ -p FILE ] | 文件如果存在并且文件是一个管道返回True |
| [ -r FILE ] | 文件如果存在并且文件可读返回True(指运行这个测试命令的用户的读权限) |
| [ -s FILE ] | 文件如果存在并且文件大小大于0返回True |
| [ -t FD ] | 如果一个文件描述符FD(对应函数的返回值)打开并引用了终端则为True，文件(描述符)与一个终端设备相关，这个测<br>试选项可以用于检查脚本中是否标准输入 ([ -t 0 ])或标准输出([ -t 1 ])是一个终端 |
| [ -u FILE ] | 文件如果存在返回True，文件的设置-用户-ID(suid)标志被设置，一个root用户拥有的二进制执行文件如果设置了<br>设置-用户-ID位(suid)标志普通用户可以以root权限运行。[1] 这对需要存取系统硬件的执行程序（比如说pppd和cdrecord）很有用<br>如果没有设置suid位，则这些二进制执行程序不能由非root调用。被设置了suid标志的文件在权限列中以s标志表示(-rw**s**r-xr-t) |
| [ -w FILE ] | 文件如果存在并且可写的返回True(运行这个测试命令的用户的写权限) |
| [ -x FILE ] | 文件如果存在并且是可执行的返回True(测试命令的用户的执行权限) |
| [ -O FILE ] | 文件如果存在并且由有效用户ID所拥有的返回True(你是文件拥有者) |
| [ -G FILE ] | 文件如果存在并且与用户组ID相同返回True(文件所在的组和文件的groupID一致) |
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
| [ ARG1 OP ARG2 ] | “OP”是-eq，-ne，-lt，-le，-gt或-ge之一。 如果“ARG1”分别等于，不等于，小于，小于或等于，大于<br>或大于或等于“ARG2”，则这些算术二元运算符返回true。 “ARG1”和“ARG2”是整数 |

可以使用以下运算符组合表达式，按优先级递减顺序列出：

| 类别 | 例子 |
| :------: | :------: |
| [ ! EXPR ] | "非" -- 反转上面所有测试的结果(如果没有给出条件则返回真),如果EXPR是false则为真 |
| [ ( EXPR ) ] | 返回EXPR的值。 这可以用于覆盖运算符的正常优先级 |
| [ EXPR1 -a EXPR2 ] | 如果EXPR1和EXPR2都为真，则为真 |
| [ EXPR1 -o EXPR2 ] | 如果EXPR1或EXPR2为真，则为真 |

```bash
# 大多数程序员更喜欢使用test内置命令，这相当于使用方括号进行比较，如下所示
test "$(whoami)" != 'root' && (echo you are using a non-privileged account; exit 1)
# 如果在子shell中调用exit，则不会将变量传递给父级。如果您不希望Bash派生子shell，请使用{ }代替()
```

#### if语句[ ] [[ ]]比较

| 测试类型 | [ ] | [[ ]] |
| :------: | :------: | :------: |
| 数字测试 | -eq -ne -lt -le -gt -ge | 同[ ] |
| 文件测试 | -r -l -w -x -f -d -s -nt -ot | 同[ ] |
| 字符测试 | = != -n -z ==(同=) 不可使用<= >= | 同[ ] |
| 逻辑测试 | -a -o ! | && \|\| ! |
| 数学运算 | 不可以使用 | + - * / % |
| 组合 | 用各自逻辑符号连接的数字(运算)测试、文件测试、字符测试 | 同[ ] |

| 情况复杂的字符测试 | [ ] | [[ ]] |
| :------: | :------: | :------: |
| < > | 比较结果异常，如:$[ 12 > 21 ]与$[ 21 > 12 ]、$[ a > b ]与$[ b > a ]的返回值一样 | 根据对于ASCII码进行比较<br>(若多个字母和数字组合，则先比较首个，相同再比较下一个) |
| \\> \\< | 根据对于ASCII码进行比较(若多个字母和数字组合，则先比较首个，相同再比较下一个) | 不可以使用 |

```bash
# if then else结构测试断掉的连接

#!/bin/bash
# broken-link.sh
# 由Lee bigelow所写<ligelowbee@yahoo.com>

# 一个用于发现死符号链接并且输出它们的链接文件的纯shell的脚本。
# 所以它们能把输出提供给xargs并处理 :)
# 例如： broken-link.sh /somedir /someotherdir|xargs rm
# 下面是更好的发现死符号链接的办法:
# find "somedir" -type l -print0|\
# xargs -r0 file|\
# grep "broken symbolic"|
# sed -e 's/^\|: *broken symbolic.*$/"/g'
# 但这不是纯bash脚本，下面的则是.
# 注意: 谨防在/proc文件系统和死循环链接中使用!
# 如果没有参数被传递给脚本作为搜索目录，则使用当前目录
[[ $# -eq 0 ]] && directories=`pwd` || directories=$@

#Setup the function link_chk to check the directory it is passed
#for files that are links and don't exist, then print them quoted.
#If one of the elements in the directory is a subdirectory then
#send that send that subdirectory to the link_check function.
########
link_chk () {
    for element in $1/*; do
    [[ -h "$element" -a ! -e "$element" ]] && echo \"$element\"
    [[ -d "$element" ]] && linkchk $element
    # Of course, '-h' tests for symbolic link, '-d' for directory.
    done
}

#Send each arg that was passed to the script to the link_chk function
#if it is a valid directory.  If not, then print the error message
#and usage info.
############
for directory in $directories; do
    if [[ -d $directories ]]
    then link_chk $directories
    else
        echo "$directories is not a directory"
        echo "Usage: $0 dir1 dir2 ..."
    fi
done

# [1] 一定要意识到suid二进制文件可能引起潜在的安全漏洞，并且在shell脚本中suid标志已经不起作用了
# [2] 在现代UNIX操作系统，粘住位已经不在文件中再使用，只用在目录中

```

#### then语句后面的命令

```bash
# then语句后面的CONSEQUENT-COMMANDS列表可以是任何有效的UNIX命令，任何可执行程序，任何可执行shell脚本或任何shell语句，但结
# 束fi除外。重要的是要记住，then和fi被认为是shell中的分隔语句。因此，在命令行上发出时，它们用分号分隔
#第一个示例检查文件是否存在
#!/bin/bash

echo "This scripts checks the existence of the messages file."
echo "Checking..."
if [[ -f /var/log/messages ]]
  then
    echo "/var/log/messages exists."
fi
echo
echo "...done."

: << comment
一个if/then结构测试一列命令的退出状态是否为0（因为依照惯例，0意味着命令执行成功），如果是0则会执行一个或多个命令
有一个命令 [ (左方括是特殊字符). 它和test是同义词,因为效率的原因，它被内建在shell里。这个命令的参数是比较表达式或者
文件测试，它会返回一个退出状态指示比较的结果(0表示真，1表示假)

#一个if/then结构能包含嵌套的比较和测试
if echo "Next *if* is part of the comparison for the first *if*."
  if [[ $comparison = "integer" ]]
    then (( a < b ))
  else
    [[ $a < $b ]]
  fi
then
  echo '$a is less than $b'
fi
comment

# 当if和then在同一行的时候，一个分号（;）必须用在if语句的结尾。if和then都是关键字.关键字（或命令）开始一个语句，如果在同
# 一行开始另一个新语句时，前面一个语句必须用分号（;）结束  例如：if [ -x "$filename" ]; then

#等价的测试命令：test,/usr/bin/test,[]和/usr/bin/[
#!/bin/bash
if test -z "$1"
then
  echo "No command-line arguments."
else
  echo "First command-line argument is $1."
fi

if /usr/bin/test -z "$1"      # 和内建的"test"命令一样.
then
  echo "No command-line arguments."
else
  echo "First command-line argument is $1."
fi

if [ -z "$1" ]                # 和上面代码块的功能一样
then
  echo "No command-line arguments."
else
  echo "First command-line argument is $1."
fi

if /usr/bin/[ -z "$1" ]       # 同样和上面的代码块一样.
# if /usr/bin/[ -z "$1"       # 工作, 但还是给出一个错误信息.注意:这个已经在bash 3.x版本被修补好了。
then
  echo "No command-line arguments."
else
  echo "First command-line argument is $1."
fi

# 在[[和]]之间的所有的字符都不会被文件扩展或是标记分割，但是会有参数引用和命令替换
file=/etc/passwd

if [[ -e $file ]]
then
  echo "Password file exists."
fi

# 用[[ .. ]]测试结构比用[ .. ]更能防止脚本逻辑错误。例如：&&,||,<和>操作符能在一个[[]]测试里通过，但在[]结构会发生错误
# 在一个if的后面，不必一定是test命令或是test结构（[]或是[[]]）
dir=/home/bozo

if cd "$dir" 2>/dev/null; then   # "2>/dev/null"会隐藏错误的信息.
  echo "Now in $dir."
else
  echo "Can't change to $dir."
fi
# "if COMMAND"结构会返回COMMAND命令的退出状态码
# 同样的，在一个测试方括号里面的条件测试也可以用列表结构(list construct)而不必用if。
var1=20
var2=22
[ "$var1" -ne "$var2" ] && echo "$var1 is not equal to $var2"

home=/home/bozo
[ -d "$home" ] || echo "$home directory does not exist."
```

## 操作符

### 算术运算符

| 算术运算符 | 描述 |
| :------: | :------: |
|  | bash无法处理浮点运算，默认会把小数点的数字作为字符串，脚本中使用bc进行浮点计算或添加数学库函数的支持 |
| + - | 加法 减法(常用) |
| * / % | 乘法 除法 取模(常用) |
| ** | 幂运算(常用) |
| ++ -- | 自加 自减，可放在变量前变量后,变量之前表示先自增或自减再赋值给变量，之后表示先赋值再自增或自减(常用) |
| ! && \|\| | 逻辑非（取反） 逻辑与（and） 逻辑或（or） (常用) |
| < <= > >= | 比较符号 |
| == != = | 比较符号 |
| << >> | 向左位移 向右位移 |
| ~ | & ^ | 按位取反 按位异或 按位与 按位或 |
| = += -= *= /= %= | 赋值运算符 |

| 算术运算表达式和运算命令 | 描述 |
| :------: | :------: |
| (()) | 整数运算的常用操作符 |
| let | 用于整数运算和(())类似 |
| expr | 通常用于整数计算也有其他额外功能 |
| bc | 适合整数和小数运算 |
| $[] | 用于整数运算 |
| awk | 整数和小数运算 |
| declare | 定义变量值和属性,-i参数用于定义整型变量 |

#### 算术测试

```markdown
# (( ))结构扩展并计算一个算术表达式的值。如果表达式值为0，会返回1或假作为退出状态码。一个非零值的表达式返回一个0或真作为
# 退出状态码。这个结构和先前test命令及[]结构的讨论刚好相反

#用(( ))进行算术测试
#!/bin/bash
# 算术测试.(( ... ))结构会求值并测试该值。退出状态码与[ ... ]结构正好相反!返回0代表真，1代表假

(( 0 )); echo "Exit status of \"(( 0 ))\" is $?."         # 1
(( 1 )); echo "Exit status of \"(( 1 ))\" is $?."         # 0
(( 5 > 4 )); echo "Exit status of \"(( 5 > 4 ))\" is $?."     # 0
(( 5 > 9 )); echo "Exit status of \"(( 5 > 9 ))\" is $?."     # 1
(( 5 - 5 )); echo "Exit status of \"(( 5 - 5 ))\" is $?."     # 1
(( 5 / 4 )); echo "Exit status of \"(( 5 / 4 ))\" is $?."     # 0
(( 1 / 2 )); echo "Exit status of \"(( 1 / 2 ))\" is $?."     # 1
(( 1 / 0 )) 2>/dev/null; echo "Exit status of \"(( 1 / 0 ))\" is $?."     # 1

```

### 其他比较操作符

二元比较操作符比较两个变量或是数值。注意整数和字符串比较的分别

#### 整数比较

| 表达式 | 含义 | 示例 |
| :---------: | :---------: | :---------: |
| -eq | 等于 | if [ "$a" -eq "$b" ] |
| -ne | 不等于 | if [ "$a" -ne "$b" ] |
| -gt | 大于 | if [ "$a" -gt "$b" ] |
| -ge | 大于等于 | if [ "$a" -ge "$b" ] |
| -lt | 小于 | if [ "$a" -lt "$b" ] |
| -le | 小于等于 | if [ "$a" -le "$b" ] |
| > | 小于(在双小括号内使用) | (("$a" > "$b")) |
| >= | 大于等于(在双小括号里使用) | (("$a" >= "$b")) |
| < | 小于(在双小括号里使用) | (("$a" < "$b")) |
| <= |  小于等于 (在双括号里使用) | (("$a" <= "$b")) |

#### 字符串比较

| 表达式 | 含义 | 示例 |
| :---------: | :---------: | :---------: |
| = | 等于 | if [ "$a" = "$b" ] |
| == | 等于① | if [ "$a" == "$b" ] |
| != | 不等于 | if [ "$a" != "$b" ] |
| < | 小于,按照ASCII字符排序进行比较 | if [[ "$a" < "$b" ]]或if [ "$a" \< "$b" ]，在[]要注意转义字符< |
| > | 大于,按照ASCII字符排序进行比较 | if [[ "$a" > "$b" ]]或if [ "$a" \> "$b" ]，在[]要注意转义字符> |
| -z | 字符串为null,即指定字符串长度为零 |  |
| -n | 字符串不为null,即长度不为零 |  |

①区别解析：
* ==和=同义词，但==比较操作符在一个双中括号测试和一个单中括号测试意思不同
* [[ $a == z* ]]    # 如果变量$a以字符"z"开始(模式匹配)则为真
* [[ $a == "z*" ]]  # 如果变量$a与z*(字面上的匹配)相等则为真
* [ $a == z* ]      # 文件扩展和单元分割有效
* [ "$a" == "z*" ]  # 如果变量$a与z*(字面上的匹配)相等则为真

注意事项：

在方括号测试里，进行-n测试时必须把字符串用引号引起来，-z一般不引起来都能工作，但最好把测试字符串用引号括起来。如果$string
变量为空，此字符串变量引起来依然会报错，安全的方法是附加一个外部字符串给可能有空字符变量。如：

[ "x$string" != x -o "x$a" = "x$b" ]（x字符可以互相抵消）

#### 混合比较

| 表达式 | 含义 |
| :---------: | :---------: |
| -a | 逻辑与，如果exp1和exp2都为真，则exp1 -a exp2返回真 |
| -o | 逻辑或，只要exp1和exp2任何一个为真，则exp1 -o exp2返回真 |

#### 算术和字符串比较

```bash

# a和b既能被看成是整数也能被看着是字符串。在字符串和数字之间不是严格界定的，bash变量是无类型的，Bash变量不是强类型的
#  Bash允许变量的整数操作和比较，但这些变量必须只包括数字字符,不管怎么样，应该小心考虑。例如：
#!/bin/bash
# 此例子中-ne和!=均能工作，以下是具体比较
a=4
b=5

if [[ "$a" -ne "$b" ]]; then echo "$a 不等于 $b"; echo "(整数比较)"; fi   # ab作为整数比较
if [[ "$a" != "$b" ]]; then echo "$a 不等于 $b"; echo "(字符串比较)"; fi    # ab作为字符串比较

# 测试一个字符串是否是null值
#!/bin/bash

# 用if [ ... ]结构,测试null字符串和没有用引号引起的字符串
# 如果一个字符串变量没有被初始化，它里面的值未定义。这种情况称为"null"值(不同于零)

# 第一次，$string1没有被声明或初始化
if [[ -n $string1 ]]
    then
    echo "String \"string1\" is not null."
else
    echo "String \"string1\" is null."
fi
# 错误结果.
# Shows $string1 as not null, although it was not initialized.

# 第二次, $string1变量被引号引起
if [[ -n "$string1" ]]
    then
    echo "String \"string1\" is not null."
else
    echo "String \"string1\" is null."
fi                    # 在一个测试方括里用引号引起变量!

# 第三次,变量$string1什么也不加
if [[ $string1 ]]
    then
    echo "String \"string1\" is not null."
else
    echo "String \"string1\" is null."
fi
# 以上正常执行,[]测试运算符单独检测字符串是否为空,但用引号括起来"$string1"才是更好习惯

string1=initialized

if [ $string1 ]       # Again, $string1 stands naked.
then
echo "String \"string1\" is not null."
else
echo "String \"string1\" is null."
fi
# 再次显示正确结果，然而更好仍然是用引号括起来，且看以下的列子

string1="a = b"

if [ $string1 ]       # 没有用引号引起来
    then
    echo "String \"string1\" is not null."
else
    echo "String \"string1\" is null."
fi
# $string1没有引号显示出错误的结果

#!/bin/bash
# 用more命令查看被压缩过的文件

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

zcat $1 | more  # 用过滤程序more,也可以用less代替

exit $?   # 脚本返回管道的退出状态,实际上"exit $?"是多余的,因为在任何情况下，脚本会返回最后一个执行命令的退出码

```

### 位操作符

很少在脚本中使用，主要用于操作和测试从端口或sockets中读取的数据，位运算多用于编译型语言，如C、C++语言

| 表达式 | 含义 |
| :---------: | :---------: |
| << | 左位移，每移一位相当于乘以2 |
| <<= | 左位移赋值,如：let "var <<=2" 时var变量二进制值左移两位，相当于乘以4 |
| >> | 右位移，每移一位相当除以2 |
| >>= | 右位移赋值 |
| & | 位与 |
| &= | 位与赋值 |
| \| | 位或 |
| \|= | 位或赋值 |
| ~ | 位反 |
| ! | 位非 |
| ^ | 位或 |
| ^= | 位或赋值 |

### 数字常量

除非一个数字有特别的前缀或符号，否则shell脚本把它当成十进制的数。一个前缀为0的数字是八进制数。一个前缀为0x的数字是十六
进制数。一个数用内嵌的#来求值则看成BASE#NUMBER(有范围和符号限制)(译者注：BASE#NUMBER即：基数#数值，参考下面的例子)

```bash

#数字常量的表示法
#!/bin/bash
# numbers.sh: 不同基数的数字表示法.

# 十进制数: 它是默认的
let "dec = 32"; echo "decimal number = $dec"             # 32

# 八进制数: 以'0'(零)为前缀
let "oct = 032"; echo "octal number = $oct"               # 26,结果表示为十进制.

# 十六进制: 以'0x'或'0X'为前缀
let "hex = 0x32"; echo "hexadecimal number = $hex"         # 50,以十进制的形式表示.

# 其他的进制的表示形式: BASE#NUMBER
# BASE值在2和64之间.
# NUMBER必须使用在BASE范围内的符号,看下面的示例.

let "bin = 2#111100111001101"; echo "binary number = $bin"              # 31181
let "b32 = 32#77"; echo "base-32 number = $b32"             # 231
let "b64 = 64#@_"; echo "base-64 number = $b64"             # 4031
# 这个符号只能工作在ASCII码值为2-64的范围限制.
# 10个数字+26个小写字母+26个大写字母+ @ + _

echo $((36#zz)) $((2#10101010)) $((16#AF16)) $((53#1aA))    # 1295 170 44822 3375

#  提示：使用一个超出给定进制的数字将会引起一个错误信息，例如：
let "bad_oct = 081"     # 此例子会发生错误，因为八进制数字只能使用数字0-7.

```

## 变量访问

### 变量间接引用

```bash
a=b
b=c
echo "a=$a" # a=1 直接引用
echo "a=\$$a"   # a=c   间接引用

```

### $RANDOM随机整数

$RANDOM是Bash的一个返回伪随机[1]整数(范围为0-32767)的内部函数(而不是一个常量或变量)，不应该用于产生加密的密钥

## 循环与分支

循环控制：break命令将会跳出循环,continue命令将会跳过本次循环下边的语句,直接进入下次循环

## 定义一个颜色输出字符串函数

```bash
# function关键字定义一个函数，可加或不加
方法一：
function echo_color(){
 if [$1 == "green"];then
  echo -e "\033[32;40m$2\033[0m"
 elif [$1 == "red"0];then
  echo -e "\\033[31;40m$2\033[0m"
 fi
}

方法二：
function echo_color(){
 case $1 in
  green)
   echo -e "\033[32;40m$2\033[0m"
   ;;
  red)
   echo -e "\033[31;40m$2\033[0m"
   ;;
  *)
   echo "Example:echo_color red string"
  esac
}
```

## 批量创建用户

```bash
#!/bin/bash
DATE=$(date+%F_%T)
USER_FILE=user.txt
echo_color(){
 if [$1 == "green"];then
  echo -e "\033[32;40m$2\033[0m"
 elif [$1 == "red"0];then
  echo -e "\\033[31;40m$2\033[0m"
 fi
}
# 如果用户文件存在并大于0就备份
 if [-s $USER_FILE];then
  mv $USER_FILE ${USER_FILE}-${DATE}.bak
  echo_color green "$USER_FILE exist,rename ${USER_FILE}-${DATE}.bak"
 fi
 echo
```

## shell采集系统cpu 内存 磁盘 网络信息

### cpu信息采集

```bash
# cpu使用率采集算法;通过/proc/stat文件采集并计算CPU总使用率或者单个核使用率。以cpu0为例，算法如下：
: << comment
cat /proc/stat | grep ‘cpu0’ # 得到cpu0的信息
cpuTotal1=user+nice+system+idle+iowait+irq+softirq
cpuUsed1=user+nice+system+irq+softirq
sleep 30秒
# 再次cat /proc/stat | grep ‘cpu0’ 得到cpu的信息
cpuTotal2=user+nice+system+idle+iowait+irq+softirq
cpuUsed2=user+nice+system+irq+softirq
# 得到cpu0 在30秒内的单核利用率：(cpuUsed2 – cpuUsed1) * 100 / (cpuTotal2 – cpuTotal1)
# 相当于使用top –d 30命令，把user、nice、system、irq、softirq五项的使用率相加
comment

a=(`cat /proc/stat | grep -E "cpu\b" | awk -v total=0 \
'{$1="";for(i=2;i<=NF;i++){total+=$i};used=$2+$3+$4+$7+$8 }END{print total,used}'`)
sleep 30
b=(`cat /proc/stat | grep -E "cpu\b" | awk -v total=0 '{$1="";for(i=2;i<=NF;i++){total+=$i};used=$2+$3+$4+$7+$8 }\
END{print total,used}'`)
cpu_usage=(((${b[1]}-${a[1]})*100)/(${b[0]}-${a[0]})))  # 此处语法有问题，暂未修复

# cpu负载采集算法：读取/proc/loadavg得到机器的1/5/15分钟平均负载，再乘以100

cpuload=(`cat /proc/loadavg | awk '{print $1,$2,$3}'`)
load1=${cpuload[0]}
load5=${cpuload[1]}
load15=${cpuload[2]}

```

### 内存采集

```bash
# 应用程序使用内存采集算法：读取/proc/meminfo文件，(MemTotal – MemFree – Buffers – Cached)/1024得到应用程序使用内存数

awk '/MemTotal/{total=$2}/MemFree/{free=$2}/Buffers/{buffers=$2}/^Cached/{cached=$2}END\
{print (total-free-buffers-cached)/1024}'  /proc/meminfo

# MEM使用量采集算法：读取/proc/meminfo文件，MemTotal – MemFree得到MEM使用量

awk '/MemTotal/{total=$2}/MemFree/{free=$2}END{print (total-free)/1024}'  /proc/meminfo

# SWAP使用大小采集算法：通过/proc/meminfo文件，SwapTotal – SwapFree得到SWAP使用大小

awk '/SwapTotal/{total=$2}/SwapFree/{free=$2}END{print (total-free)/1024}'  /proc/meminfo

```

### 磁盘信息采集

```bash

# 磁盘I/O
# IN：平均每秒把数据从硬盘读到物理内存的数据量
# 采集算法：读取/proc/vmstat文件得出最近240秒内pgpgin的增量，把pgpgin的增量再除以240得到每秒的平均增量
# 相当于vmstat 240命令bi一列的输出

a=`awk '/pgpgin/{print $2}' /proc/vmstat`
#sleep 240
b=`awk '/pgpgin/{print $2}' /proc/vmstat`
ioin=$((b-a)/240))

# OUT：平均每秒把数据从物理内存写到硬盘的数据量
# 采集算法：读取/proc/vmstat文件得出最近240秒内pgpgout的增量，把pgpgout的增量再除以240得到每秒的平均增量
# 相当于vmstat 240命令bo一列的输出

a=`awk '/pgpgout/{print $2}' /proc/vmstat`
sleep 240
b=`awk '/pgpgout/{print $2}' /proc/vmstat`
ioout=$(((b-a)/240))

```

### 网络信息采集

```bash
# 流量:以https://www.centos.bz/为例，eth0是内网，eth1外网，获取60秒的流量
# 机器网卡的平均每秒流量
# 采集算法：读取/proc/net/dev文件，得到60秒内发送和接收的字节数（KB），然后乘以8，再除以60，得到每秒的平均流量

traffic_be=(`awk -F'[: ]+' 'BEGIN{ORS=" "}/eth0/{print $3,$10}/eth1/{print $3,$11}' /proc/net/dev`)
sleep 60
traffic_af=(`awk -F'[: ]+' 'BEGIN{ORS=" "}/eth0/{print $3,$10}/eth1/{print $3,$11}' /proc/net/dev`)
eth0_in=$(( (${traffic_af[0]}-${traffic_be[0]})/60 ))
eth0_out=$(( (${traffic_af[1]}-${traffic_be[1]})/60 ))
eth1_in=$(( (${traffic_af[2]}-${traffic_be[2]})/60 ))
eth1_out=$(( (${traffic_af[3]}-${traffic_be[3]})/60 ))

# 数据包量：机器网卡的平均每秒包量
# 采集算法：读取/proc/net/dev文件，得到60秒内发送和接收的包量，然后除以60，得到每秒的平均包量

packet_be=(`awk -F'[: ]+' 'BEGIN{ORS=" "}/eth0/{print $4,$12}/eth1/{print $4,$12}' /proc/net/dev`)
sleep 60
packet_af=(`awk -F'[: ]+' 'BEGIN{ORS=" "}/eth0/{print $4,$12}/eth1/{print $4,$12}' /proc/net/dev`)
eth0_in=$(( (${packet_af[0]}-${packet_be[0]})/60 ))
eth0_out=$(( (${packet_af[1]}- ${packet_be[1]})/60 ))
eth1_in=$(( (${packet_af[2]}- ${packet_be[2]})/60 ))
eth1_out=$(( (${packet_af[3]}- ${packet_be[3]})/60 ))

```
