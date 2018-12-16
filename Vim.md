# 有效文本编辑的七个习惯：

```vim
快速移动

* 快速匹配光标下单词并搜索下一个单词

% 从一个大括号跳转到其匹配的大括号，或者从if匹配endif。对于检查()、{}是否正确平衡很有用，使用[{跳转到{在当前代码块的开始，用于gd从使用变量跳转到其本地声明。
:s 替代命令，如果只有几个地方需要更改，使用*查找下一个单词并用cw更改单词，然后键入n查找下一个单词和.重复cw命令。

某些函数和变量名称可能难以输入。你能否快速地输入“XpmCreatePixmapFromData”而没有拼写错误，也不用查看它？Vim有一个完成机制，这使得这一切变得更加简单。它在您正在编辑的文件中查找单词，并在#include的文件中查找。你可以输入“XpmCr”，然后点击CTRL-N，Vim会将它扩展为“XpmCreatePixmapFromData”。这不仅可以节省很多打字时间，而且还可以避免出现拼写错误，并且稍后在编译器给出错误消息时需要修复它。

当您多次输入短语或句子时，还有更快的方法。Vim有一个记录宏的机制。您键入qa以开始录制到注册'a'。然后像往常一样输入命令，最后q再次点击停止录制。当您想要重复录制的录制命令时@a。有26个寄存器可用于此。

通过录制，您可以重复许多不同的操作，而不仅仅是插入文本。当你知道你要重复某些事情时，请记住这一点。
```

```sh

1.查看文件编码格式
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

2."设置文件的代码形式

set encoding=utf-8
set termencoding=utf-8
set fileencoding=utf-8
set fileencodings=ucs-bom,utf-8,chinese,cp936

3."vim的菜单乱码解决：

"同样在 _vimrc文件里以上的中文设置后加上下列命令，
source $vimruntime/delmenu.vim
source $vimruntime/menu.vim

4."vim提示信息乱码的解决

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
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |

移动命令：
h 左
l 右
j 下
k 上

插入命令
a 在光标后插入文本；
A 在本行行末插入文本；
i 在光标前插入文本；
I 在光标本行开始插入文本；
o 在光标下插入新行；
O 在光标上插入新行；

H移至屏幕上端； high
M移至屏幕中央   midden
L移至屏幕下端； low

:set nu    ---设置行号
:set nonu  ----取消行号
gg  到第一行
G   到最后一行
nG到第几行
:n到第几行
备注：在命令模式下使用冒号设置行号后，会自动跳回到命令模式；

删除命令：
x  删除光标所在处的字符；
nx 删除光标所在处后N个字符；
dd 删除光标所在行的内容；
dG 删除光标所在行到末尾的内容；
ndd删除N行；
D 删除光标所在行到行尾；
：n1,n2d 删除指定的范围；

复制和剪切命令：
yy,Y 复制当前行
nyy,nY 复制当前行以下n行
dd 剪切当前行
ndd 剪切当前行以下n行
p,P 粘贴在当前光标所在行的下面或者上面；

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

