# Vim

```vim

"查看文件编码格式
:echo &fileencoding

vim 有四个跟字符编码方式有关的选项，encoding、fileencoding、fileencodings、termencoding (这些选项可能的取值请参考 vim 在线帮助 :help encoding-names)，它们的意义如下:

encoding: vim 内部使用的字符编码方式，包括 vim 的 buffer (缓冲区)、菜单文本、消息文本等。

fileencoding: vim 中当前编辑的文件的字符编码方式，vim 保存文件时也会将文件保存为这种字符编码方式 (不管是否新文件都如此)。

fileencodings: vim 启动时会按照它所列出的字符编码方式逐一探测即将打开的文件的字符编码方式，并且将 fileencoding 设置为最终探测到的字符编码方式。因此最好将 unicode 编码方式放到这个列表的最前面，将拉丁语系编码方式 latin1 放到最后面。

termencoding: vim 所工作的终端 (或者 windows 的 console 窗口) 的字符编码方式。这个选项在 windows 下对我们常用的 gui 模式的 gvim 无效，而对 console 模式的 vim 而言就是 windows 控制台的代码页，并且通常我们不需要改变它。

用英文菜单和提示最好，可以免去下面对菜单和提示信息（b，c部分）的设置

如果用英文菜单和提示在安装gvim的时候，将支持本地语言的选项去掉。

解决vim文件乱码，打开文件乱码，菜单，提示信息乱码：

有四个跟字符编码方式有关的选项，encoding、fileencoding、fileencodings、termencoding

在linux中修改.vimrc（在win中是_vimrc）

windows系统也可以在vim菜单项中：编辑>启动设定，直接打开_vimrc文件

"设置文件的代码形式

set encoding=utf-8
set termencoding=utf-8
set fileencoding=utf-8
set fileencodings=ucs-bom,utf-8,chinese,cp936

"vim的菜单乱码解决：

同样在 _vimrc文件里以上的中文设置后加上下列命令
source $vimruntime/delmenu.vim
source $vimruntime/menu.vim

"vim提示信息乱码的解决

language messages zh_cn.utf-8

```

## VIM主帮助文档

```vim
工作模式：命令模式（默认界面），插入模式，可视化模式

命令模式环境中的操作：

获得特定帮助：

| 类别 | 例子 |
| :------: | :------: |
| 普通模式命令 | :help x |
|  |  |
" 全局
:help keyword     关键字帮助

" 打开/保存/退出/改变文件(缓存区)
:e <path/to/file>   打开文件
:w                  保存
:saveas <path/to/file> 保存到
:x,ZZ或:wq          保存并退出(:x只保存不退出)
:w !sudo tee %      使用sudo保存
:[range]w           只保存[range]内的
:q!                 不保存退出(:qa!,即使有修改过的隐藏缓冲区也不保存退出)
:bn,:bp             显示下一个、上一个文件buffer

" 基本移动命令
h、j、k、l  ← ↓ ↑ →
b   到上个单词词尾
B   到上个单词词尾(单词含标点)
0   到第一列
^   到该行第一个非空白字符
$   到行尾
g_  到该行最后一个非空白字符
gg  到第一行
G   到最后一行
nG  到第几行
:n  到第几行
H   到屏幕上端high
M   到屏幕中央midden
L   到屏幕下端low
Ctrl + e    向下滚动一行
Ctrl + y    向上滚动一行
Ctrl + b    向后滚动一屏
Ctrl + f    向前滚动一屏
Ctrl + d    向前滚动半屏
Ctrl + u    向后滚动半屏  

" insert命令
a 光标后插入
A 本行行末插入文本；
i 光标前插入文本；
I 光标本行开始插入文本；
o 光标下插入新行；
O 光标上插入新行；

" 复制和剪切命令：
P,p     粘贴在光标所在行的后面、前面
yy,Y    复制当前行
nyy,nY  复制当前行以下n行
dd      剪切当前行
ndd     剪切当前行以下n行
u       撤销
<C-r>   恢复

" 其他
r     替换当前字符
J     将下一行合并到当前行
gJ    将下一行合并到当前行(去除空格)
cc    清空当前行并进入insert mode
C     替换到行尾
s     删除当前字符并进入insert mode
xp    当前字符后移
:%s/old/new/g     全部替换
:%s/old/new/gc    逐个替换
`.    转到上次编辑的行
==    修复行缩进

" 可视化模式命令
aw    选择当前单词
ab    选择被()包裹的区域(含括号)
aB    选择{}包裹的区域(含括号)
ib    选择()包裹的区域(不含括号)
iB    选择{}包裹的区域(不含括号)
<>    向左 向右缩进
~     大小写切换

" 寄存器
:reg  显示寄存器内容
"xy   复制到寄存器x
"xp   粘贴寄存器x中内容
" 寄存器存储在~/.viminfo中,下次重启vim仍会加载
" 寄存器0存储上一次复制的值

" 标签
:tabnew file      在新标签打开文件
gt    切换到下个标签
gT    切换到上个标签
ngt   切换到第n个标签
:tabc 关闭当前标签
:tabo 关闭其他标签






```

```vim
.   重复上一个操作
N<command>  重复<command>N次
NG  跳转到N行
gg  跳转到文件开始
G   跳转到行末
w   跳转到下一个单词
W   到下个单词(含标点)
e   跳转到当前单词词尾
E   到下个单词结尾(含标点)

" 理解ew和EW移动区别

  光标位置 ew         E W
      |   ||         | |
x = (name_1,vision_3); # this is a comment

%    跳转到对应的( { [ 符号
* #  跳转到下一个、上一个光标下所在的单词

" 大多数命令支持以下格式：
<开始位置><命令><结束位置>
0y$ 复制整行
ye  复制到词尾
gU  大写    gu  小写......
dt" 移除所有直到"符号

[move](http://yannesposito.com/Scratch/img/blog/Learn-Vim-Progressively/line_moves.jpg)

" 区域选择   <action>a<object>或<action>i<object>
" 在visual mode下操作
action可以是任何操作，如d(delete)、y(yank)等等
object可以是：w、W、s(sentence句子)、p(paragraph段落)也可以是符号，如：" ' )等

" 例子详解：(假设光标在第一个o上)
(map (+) ("foo"))

vi" 选择foo
va" 选择"foo"
vi) 选择("foo")
v2i 选择map (+) ("foo")
v2a (map (+) ("foo"))

" 选择长方形块进行注释例子

^ → 跳转到行首非空白符
<C-v> → 可视块
<C-d> → move down (could also be jjj or %, etc…)
I-- [ESC] → 用#注释每一行
" 注：Windows平台下，若剪切板非空，<C-q>取代<C-v>

" 自动完成
按<C-n>和<C-p>,输入单词开头，按键后进行选择要输入的单词

" 宏
qa录制行为到注册表a中，然后当你键入@a的时候，@a将会重放你保存在注册表a中的宏。@@是重放最后一个执行过宏的快捷键

" 可视化选择:v,V,<C-v>
< >   向左、向右缩进
=     自动缩进

" 例如：行尾添加符号;
<C-v> G $ A ; <ESC>




:set nu    ---设置行号
:set nonu  ----取消行号


删除命令：
x  删除光标所在处的字符；
nx 删除光标所在处后N个字符；
dd 删除光标所在行的内容；
dG 删除光标所在行到末尾的内容；
ndd删除N行；
D 删除光标所在行到行尾；
：n1,n2d 删除指定的范围；


替换和取消命令：
r 取代光标所在处字符；
R 从光标所在处开始替换字符，press ESc overj；
u 取消上一步操作；对于还没有同步硬盘的操作时有效的；

搜索和替换命令：
/command  n,N 的区别是前者有前往后，后者相反；
如果在搜索的过程中不区分大小写的话，使用命令:set ic;
:%s/old/new/g 全文替换；
:n1,n2s/old/new/g  g ,c的区别就是是否在进行操作的上的确认；

保持和退出的命令：
:wq
:wq!
ZZ equal :wq
:q!
:w
:w /test/important.bak

技术应用实例：
vi
:r 文件名  导入文件的内容或者命令执行的结果；
:!命令     在vi中执行命令
:r !date#这个命令很使用；

连续行注释：
:n1,n2s/^/#/g  取消的命令；:n1,n2s/^#//g
尖角号表示行首；斜杠表示分隔符；

扩展知识点：
在linux系统当中并不是所有的配置文件的注释行都是使用#键，在~./vimrc的配置文件使用的注释行是双引号；

: setshowmode 显示当前模式
x 删除字符

