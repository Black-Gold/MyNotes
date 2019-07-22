# python3手册

## Python相关说明

```markdown
# 库：相关功能的合集

 函数：
    定义：

    def 函数名（参数1，参数2……）：
        语句块
        return 返回值

    调用：
        函数名（参数1，参数2……）
        变量名 = 函数名（参数1，参数2……）
 
类提供了一种组合数据和功能的方法。创建一个新类意味着创建一个新 类型 的对象，从而允许创建一个该类型的新 实例 。每个类的实例可以拥有保存自己状态的属性。一个类的实例也可以有改变自己状态的（定义在类中的）方法。Python 的类提供了面向对象编程的所有标准特性：类继承机制允许多个基类，派生类可以覆盖它基类的任何方法，一个方法可以调用基类中相同名称的的方法。对象可以包含任意数量和类型的数据。和模块一样，类也拥有Python的动态特性：它们在运行时创建，可以在创建后修改
 类：
    定义：
    class 类名()：
        def __init__(self,参数……):
        def 函数名1(self,参数……):
        def 函数名2(self,参数……):

    调用：
        实例名 = 类名()
        实例名.方法名(参数)

# 框架：控制反转

# 模块：具有显式导出和导入的抽象接口，实现和接口是分开的，可能有多个实现并且实现是隐藏的。通俗的讲，将自行定义或已经存在的方法和变量存放在文件中，为一些脚本或交互式解释器实例使用，这样的文件被称之为模块；模块是一个包含Python定义函数、变量和语句的文件。模块可以被别的程序引入，以使用该模块中的函数等功能。这也是使用python标准库的方法

    以下划线开头的标识符是有特殊意义的。
    •以单下划线开头（_foo）的代表不能直接访问的类属性，需通过类提供的接口进行访问；
    •以双下划线开头的（__foo）代表类的私有成员；
    •以双下划线开头和结尾的（__foo__）代表python里特殊方法专用的标识，如__init__（）代表类的构造函数。
# 模块（modules）：
    模块(module)是python最高级别的程序组织单位。它可以打包程序代码和数据以备重用
    模块采用python程序的文件形式（或者C扩展程序的形式）存储，客户导入模块并对使用他们定义的名字

## 标准模块

Python附带了一个标准模块库，变量 sys.ps1 和 sys.ps2 定义用作主要和辅助提示的字符串；这两个变量只有在编译器交互模式下才被定义。

sys.path变量是一个字符串列表，用于确定解释器的模块搜索，可以使用标准列表操作对其进行修改。

## 包(packages)

包是一种通过用"带点号的模块名"来构造Python模块命名空间的形式。

请注意，当使用 from package import item 时，item可以是包的子模块（或子包），也可以是包中定义的其他名称，如函数，类或变量。 import 语句首先测试是否在包中定义了item；如果没有，它假定它是一个模块并尝试加载它。如果找不到它，则引发 ImportError 异常。

相反，当使用 import item.subitem.subsubitem 这样的语法时，除了最后一项之外的每一项都必须是一个包；最后一项可以是模块或包，但不能是前一项中定义的类或函数或变量

* 要使用格式化字符串字面值，在字符串开始引导或三引号之前加上一个f或F，在此字符串中，可以在{}字符之间引用变量或字面值的Python表达式
* 字符串的str.format()方法
* 使用字符串切片和连接操作自行完成对所有字符串的处理

# import语句:

出于效率的考虑，每个模块在每个解释器会话中只被导入一次。因此，如果你更改了你的模块，则必须重新启动解释器， 或者，如果它只是一个要交互式地测试的模块，请使用 importlib.reload()，例如 import importlib; importlib.reload(modulename)。
模块搜索路径：解释器首先寻找具有该导入名称的内置模块，若没有则会从sys.path变量所给出的目录列表寻找，支持符号链接的文件系统上，包含输入脚本的目录是在追加符号链接后才计算出来的。换句话说，包含符号链接的目录并没有被添加到模块的搜索路径上

sys.path的初始目录地址：

* 包含输入脚本的目录(或者未指定文件时的当期目录)
* PYTHONPATH(一个包含目录名称的列表，其和shell变量PATH具有相同的语法)
* 取决于Python安装的默认设置

了解了搜索路径的概念，就可以在脚本中修改sys.path来引入一些不在搜索路径中的模块
**__name__属性** 一个模块被另一个程序第一次引入时，其主程序将运行。如果我们想在模块被引入时，模块中的某一程序块不执行，我们可以用__name__属性来使该程序块仅在该模块自身运行时执行

if __name__ == '__main__':
 print('程序自身在运行')
else:
 print('我来自另一模块')

每个模块都有一个__name__属性，当其值是'__main__'时，表明该模块自身在运行，否则是被引入

可以在Python命令中使用-O或-OO开关,以减小编译后模块的大小。-O开关去除断言语句，-OO开关同时去除断言语句和__doc__字符串；
一个从.pyc文件读出的程序并不会比它从.py读出时运行的更快。区别在于.pyc文件载入速度更快
compileall模块可以为一个目录下的所有模块创建.pyc文件
```

## Python开发环境最佳实践

```bash
python -m SimpleHTTPServer | Serve current directory tree at http://$HOSTNAME:8000/     # 利用python简单建立webserver

# yum -y install epel-release
# yum -y install python2-pip
# yum -y install python3-pip
python -m pip install -U pip setuptools

# python -m site --user-base 找到 用户基础目录,将基础目录添加到PATH
# 在 Windows 上，您通过运行 py -m site --user-site 找到用户基础目录，将 site-packages 替换为 Scripts
pip install --user pipenv

# 在Windows安装virtualenv和virtualenvwrapper-win用于创建多个Python版本开发环境，Linux中最好使用pyenv代替
# 安装pipenv和autoenv，自动激活环境
# 在Linux上:
git clone git://github.com/kennethreitz/autoenv.git ~/.autoenv
echo 'source ~/.autoenv/activate.sh' >> ~/.bashrc

# 设置pip配置文件
: << comment
Linux：$HOME/.config/pip/pip.conf
Windows：%APPDATA%\pip\pip.ini
virtualenv：%VIRTUAL_ENV%\pip.ini
全局生效设置如下：(不支持Windows Vista)
Linux：/etc/pip.conf
Windows XP：C:\Documents and Settings\All Users\Application Data\pip\pip.ini
Windows7或之后版本：C:\ProgramData\pip\pip.ini
comment

# pip配置文件内容如下：
[global]
timeout = 120
no-cache-dir = true
index-url = https://mirrors.aliyun.com/pypi/simple/
extra-index-url = https://mirrors.huaweicloud.com/repository/pypi/simple

[freeze]
timeout = 10

[install]
ignore-installed = true
trusted-host =
    mirrors.aliyun.com
    mirrors.huaweicloud.com
```

## 简介

```markdown
#!/usr/bin/python3
# -*- coding: UTF-8 -*-
# Python中单行注释以#开头，多行注释用三个单引号（```）或者三个双引号（"""）将注释括起来。
# 特殊字符比如\n在单双引号有同样的意义，不同之处是在单引号中无需转义双引号，也就是表示其实际意义。反之亦然

help(len)           # 内建len()函数帮助
help(sys)           # sys模块帮助,必须先import sys
help(sys.exit)      # exit()函数在sys模块的帮助
help('xyz'.split)   # 对于string对象splist()方法的帮助，和help(str.split)一样
help(list)          # list对象帮助
help(list.append)   # list对象append()方法帮助
dir(list)           # 显示list对象属性，包括其方法
dir(sys)            # 类似help()

python中数的数据类型有四种：整数、长整数、浮点数、复数
python中变量无需声明，每个变量在使用前必须赋值，变量赋值后该变量才会被创建。变量没有类型，而通常所说的“类型”指内存中对象的类型

python3中常用的内置数据类型：
* Boolean (布尔值)
* Numbers (数字)
* String (字符串)
* Bytes (字节)
* List (列表)
  * 示例：list1 = ['Google', 'aws', 1997, 2000]
  * 空列表:list2 = []   ======>>等价于list1 = list[]
* Tuple (元组)
  * 示例：tuple1 = ('Google', 'aws', 1997, 2000)
  * 空元组:tuple2 = ()  ======>>等价于tuple1 = tuple()
* Sets (集合)
  * 示例：set1 = {'apple', 234, 'apple', 'banana'}
  * 空集合：set2 = set()
* Dictionaries (字典)
  * 示例：dict1 = {'Alice': '21', 'Beth': '12', 'Cecil': '19'}
  * 空字典:dict1 = {}   ======>>等价于dict1 = dict{}
```

## 内置标准库

python内置函数列表：具体用法见help(函数)

|  |  | 内置函数 |  |  |
| :------: | :------: | :------: | :------: | :------: |
| abs() | delattr() | hash() | memoryview() | set() |
| all() | dict() | help() | min() | setattr() |
| any() | dir() | hex() | next() | slice() |
| ascii() | divmod() | id() | object() | sorted() |
| bin() | enumerate() | input() | oct() | staticmethod() |
| bool() | eval() | int() | open() | str() |
| breakpoint() | exec() | isinstance() | ord() | sum() |
| bytearray() | filter() | issubclass() | pow() | super() |
| bytes() | float() | iter() | print() | tuple() |
| callable() | format() | len() | property() | type() |
| chr() | frozenset() | list() | range() | vars() |
| classmethod() | getattr() | locals() | repr() | zip() |
| compile() | globals() | map() | reversed() | \_\_import__() |
| complex() | hasattr() | max() | round() |  |

## 内置常量

少数的常量存在于内置命名空间中：

## 内置类型

### 逻辑值检测：任何对象都可以进行逻辑值检测

下面基本完整罗列出会被视为假值的内置对象：
* 被定义为假值的常量: None 和 False
* 任何数值类型的零: 0, 0.0, 0j, Decimal(0), Fraction(0, 1)
* 空的序列和多项集: '', (), [], {}, set(), range(0)

### 布尔类型运算：and、or、not

| 运算 | 结果 | 注释 |
| :------: | :------: | :------: |
| x or y | if x is false, then y, else x | 这是个短路运算符，因此只有在第一个参数为真值时才会对第二个参数求值 |
| x and y | if x is false, then x, else y | not 的优先级比非布尔运算符低，因此 not a == b 会被解读为 not (a == b) 而 a == not b 会引发语法错误 |
| not x | if x is false, then True, else False | not 的优先级比非布尔运算符低，因此 not a == b 会被解读为 not (a == b) 而 a == not b 会引发语法错误 |

### 比较运算：

八种比较运算符，优先级相同(比布尔运算优先级高)。比较运算可以任意串连。is和is not运算符无法自定义并且可以被应用于任意两个对象而不会引发异常；还有两种具有相同语法优先级的运算in和not in，它们被iterable或实现了__contains__()方法的类型所支持

| 运算符 | 含义 |
| :------: | :------: |
| < | 小于 |
| <= | 小于或等于 |
| > | 大于 |
| >= | 大于或等于 |
| == | 等于 |
| != | 不等于 |
| is | 对象标识 |
| is not | 否定对象标识 |

### 数字类型运算：int、float、complex

Python有三种不同的数字类型：整数、浮点数和复数。布尔值属于整数的子类型。整数有无限精度，浮点数通常以C语言中double实现,复数具有实部和虚部，每个都是浮点数

数字是由数字字面值或内置函数与运算符的结果来创建。未修饰的整数字面值(包括十六进制、八进制和二进制数)会生成整数，包含小数点或幂运算符的数字字面值会生成浮点数，在数字字面值末尾加上 'j' 或 'J' 会生成虚数(实部为零的复数)将其与整数或浮点数相加来得到具有实部和虚部的复数

#### Numbers(数字)

```markdown
python3支持int(整数)、float(浮点)、bool(布尔)、complex(复数)

注意：
python可以同时为多个变量赋值，如：a,b=1,2
一个变量可以通过赋值指向不同类型的对象
数值的除法总是会返回一个浮点数，要获取整数使用//操作符
在混合计算时，Python会把整数类型转换为浮点数
```

所有数字类型（复数除外）都支持下列运算，按优先级升序排序(所有数字运算的优先级都高于比较运算)

* 向上取整--不管四舍五入的规则 只要后面有小数前面的整数就加1
* 向下取整--不管四舍五入的规则 只要后面有小数忽略小数

| 运算符 | 描述 | 注释 |
| :------: | :------: | :------: |
| + | 加 |  |
| - | 减 |  |
| * | 乘 |  |
| / | 除 |  |
| //| 取整数--返回商的整数部分(向下取整) | 结果是个整数，但类型不一定是int，运算结果总是向负无穷方向舍入。如：1//2为0，(-1)//2为-1 |
| % | 取模--返回除法的余数 | 不可用于复数，而应在适当条件下使用abs()转换为浮点数 |
| -x | x 取反 |  |
| +x | x 不变 |  |
| abs(x) | x 的绝对值或大小 |  |
| int(x) | 将 x 转换为整数 |  |
| float(x) | 将 x 转换为浮点数 |  |
| complex(re, im) | 一个带有实部 re 和虚部 im 的复数。im 默认为0 |  |
| c.conjugate() | 复数 c 的结合(共轭) |  |
| divmod(x, y) | (x // y, x % y) | 不可用于复数 |
| pow(x, y) | x 的 y 次幂 | Python将pow(0, 0)和0**0定义为1，这是编程语言普遍做法 |
| ** | 幂 |  |

所有的numbers.Real类型(int和float)还包括以下运算(更多数字运算参考math和cmath模块):

| 运算符 | 结果 |
| :------: | :------: |
| math.trunc(x) | x 截断为Integral |
| round(x[, n]) | x 舍入到n位小数，半数值会舍入到偶数，如果省略n，则默认为0 |
| math.floor(x) | <= x 的最大 Integral |
| math.ceil(x) | >= x 的最小 Integral |

#### 整数类型的按位运算

按位运算只对整数有意义，按位运算符是把数字看作二进制来进行计算的。二进制按位运算优先级全都低于数字运算，但高于比较运算；一元运算`~`和其他一元算术运算`+`和`-`具有相同的优先级。

以优先级升序排序的按位运算列表：

| 运算符 | 结果 | 注释 |
| :------: | :------: | :------: |
| \| | 按位或运算符：只要对应的二个二进位有一个为1时，结果位就为1 | 使用带有至少一个额外符号扩展位的有限个二进制补码表示（有效位宽度为 1 + max(x.bit_length(), y.bit_length()) 或以上）执行这些计算就足以获得相当于有无数个符号位时的同样结果 |
| ^ | 按位异或运算符：当两对应的二进位相异时，结果为1 | 使用带有至少一个额外符号扩展位的有限个二进制补码表示（有效位宽度为 1 + max(x.bit_length(), y.bit_length()) 或以上）执行这些计算就足以获得相当于有无数个符号位时的同样结果 |
| & | 按位与运算符：参与运算的两个值,如果两个相应位都为1,则该位的结果为1,否则为0 | 使用带有至少一个额外符号扩展位的有限个二进制补码表示（有效位宽度为 1 + max(x.bit_length(), y.bit_length()) 或以上）执行这些计算就足以获得相当于有无数个符号位时的同样结果 |
| x << n | x的各二进位全部左移 n 位，高位丢弃，低位补0 | 负的移位数是非法的，会导致引发 ValueError；左移 n 位等价于不带溢出检测地乘以 pow(2, n) |
| x >> n | x的各二进位全部右移 n 位 | 负的移位数是非法的，会导致引发 ValueError；右移 n 位等价于不带溢出检测地除以 pow(2, n) |
| ~x | x 逐位取反 |  |

### 迭代器类型

Python支持迭代容器的概念。这是使用两种不同的方法实现的; 这些用于允许用户定义的类支持迭代

#### 生成器类型

Python的生成器提供了一种实现迭代器协议的便捷方式。如果容器对象的__iter__()方法被实现为生成器，它将自动返回提供__iter__()和__next__()方法的迭代器对象（技术上，生成器对象）

### 序列类型 -- list、tuple、range

通用序列类型操作：大多数序列类型包括可变类型和不可变类型都支持以下的操作，collections.abc.Sequence ABC 用来更容易的在自定义序列类型正确实现这些操作。此表按优先级升序罗列序列操作，表格中，s 和 t 是具有相同类型的序列，n, i, j 和 k 是整数而 x 是任何满足 s 所规定的类型和值限制的任意对象

相同类型的序列也支持比较。 特别地，tuple 和 list 的比较是通过比较对应元素的字典顺序。 这意味着想要比较结果相等，则每个元素比较结果都必须相等，并且两个序列长度必须相同

| 运算符 | 结果 | 注释 |
| :------: | :------: | :------: |
| x in s | 如果 s 中的某项等于 x 则结果为 True，否则为 False | 1 |
| x not in s | 如果 s 中的某项等于 x 则结果为 False，否则为 True | 1 |
| s + t | s 与 t 相拼接 | 67 |
| s \* n 或 n * s | 相当于 s 与自身进行 n 次拼接 | 27 |
| s[i] | s 的第 i 项，起始为 0 | 3 |
| s[i:j] | s 从 i 到 j 的切片 | 34 |
| s[i:j:k] | s 从 i 到 j 步长为 k 的切片 | 35 |
| len(s) | s 的长度 |  |
| min(s) | s 的最小项 |  |
| max(s) | s 的最大项 |  |
| s.index(x[, i[, j]]) | x 在 s 中首次出现项的索引号（索引号在 i 或其后且在 j 之前） | 8 |
| s.count(x) | x 在 s 中出现的总次数 |  |

注释:

1. 虽然 in 和 not in 操作在通常情况下仅被用于简单的成员检测，某些专门化序列 (例如 str, bytes 和 bytearray) 也使用它们进行子序列检测
2. 小于 0 的 n 值会被当作 0 来处理 (生成一个与 s 同类型的空序列)。 请注意序列 s 中的项并不会被拷贝；它们会被多次引用
3. 如果 i 或 j 为负值，则索引顺序是相对于序列 s 的末尾: 索引号会被替换为 len(s) + i 或 len(s) + j。 但要注意 -0 仍然为 0
4. s 从 i 到 j 的切片被定义为所有满足 i <= k < j 的索引号 k 的项组成的序列。 如果 i 或 j 大于 len(s)，则使用 len(s)。 如果 i 被省略或为 None，则使用 0。 如果 j 被省略或为 None，则使用 len(s)。 如果 i 大于等于 j，则切片为空
5. s 从 i 到 j 步长为 k 的切片被定义为所有满足 0 <= n < (j-i)/k 的索引号 x = i + n\*k 的项组成的序列。 换句话说，索引号为 i, i+k, i+2\*k, i+3*k，以此类推，当达到 j 时停止 (但一定不包括 j)。 当 k 为正值时，i 和 j 会被减至不大于 len(s)。 当 k 为负值时，i 和 j 会被减至不大于 len(s) - 1。 如果 i 或 j 被省略或为 None，它们会成为“终止”值 (是哪一端的终止值则取决于 k 的符号)。 请注意，k 不可为零。 如果 k 为 None，则当作 1 处理
6. 拼接不可变序列总是会生成新的对象。 这意味着通过重复拼接来构建序列的运行时开销将会基于序列总长度的乘方。 想要获得线性的运行时开销，必须改用下列替代方案之一：
    * 如果拼接 str 对象，你可以构建一个列表并在最后使用 str.join() 或是写入一个 io.StringIO 实例并在结束时获取它的值
    * 如果拼接 bytes 对象，你可以类似地使用 bytes.join() 或 io.BytesIO，或者你也可以使用 bytearray 对象进行原地拼接。 bytearray 对象是可变的，并且具有高效的重分配机制
    * 如果拼接 tuple 对象，请改为扩展 list 类
    * 对于其它类型，请查看相应的文档
7. 某些序列类型 (例如 range) 仅支持遵循特定模式的项序列，因此并不支持序列拼接或重复
8. 当 x 在 s 中找不到时 index 会引发 ValueError。 不是所有实现都支持传入额外参数 i 和 j。 这两个参数允许高效地搜索序列的子序列。 传入这两个额外参数大致相当于使用 s[i:j].index(x)，但是不会复制任何数据，并且返回的索引是相对于序列的开头而非切片的开头

#### 不可变序列类型

不可变序列类型通常实现的唯一操作是可变序列类型未实现对hash()内置函数的支持，例如：tuple实例被用作dict键以及存储在set和frozenset实例中

#### 可变序列类型

以下操作是在可变序列类型定义，collections.abc.MutableSequence ABC被提供用来更容易在自定义序列类型上正确实现这些操作

表格中的 s 是可变序列类型的实例，t 是任意可迭代对象，而 x 是符合对 s 所规定类型与值限制的任何对象 (例如，bytearray 仅接受满足 0 <= x <= 255 值限制的整数)

| 运算符 | 结果 | 注释 |
| :------: | :------: | :------: |
| s[i] = x | 将 s 的第 i 项替换为 x |  |
| s[i:j] = t | 将 s 从 i 到 j 的切片替换为可迭代对象 t的内容 |  |
| del s[i:j] | 等同于 s[i:j] = [] |  |
| s[i:j:k] = t | 将 s[i:j:k] 的元素替换为 t 的元素 | t必须和它所替换的切片具有相同长度 |
| del s[i:j:k] | 从列表中移除 s[i:j:k] 的元素 |  |
| s.append(x) | 将 x 添加到序列的末尾 (等同于 s[len(s):len(s)] = [x]) |  |
| s.clear() | 从 s 中移除所有项 (等同于 del s[:]) | clear()和copy()均是为了与不支持切片操作的可变容器(如:dict、set)的接口保持一致 |
| s.copy() | 创建 s 的浅拷贝 (等同于 s[:]) | 同上 |
| s.extend(t) 或 s += t | 用 t 的内容扩展 s (基本上等同于 s[len(s):len(s)] = t) |  |
| s *= n | 使用 s 的内容重复 n 次来对其进行更新 | n值为一个整数或一个实现了__index__()的对象，n值为0或负数将清空序列。序列中的项不能被拷贝，会被多次引用 |
| s.insert(i, x) | 在由 i 给出的索引位置将 x 插入 s (等同于 s[i:i] = [x]) |  |
| s.pop([i]) | 提取在 i 位置上的项，并将其从 s 中移除 | 可选参数默认为-1，因此默认情况会移除并返回最后一项 |
| s.remove(x) | 删除 s 中第一个 s[i] 等于 x 的项目。 | 当s找不到x时，remove操作会引发ValueError |
| s.reverse() | 将列表中的元素逆序 | 当反转比较大的序列时，reverse()方法会原地修改此序列来保证空间经济性，为提醒用户此操作是通过间接影响进行的，它并不会返回反转后的序列 |

#### Python运算符优先级

以下表格列出了从最高到最低优先级的所有运算符：

| 运算符 | 描述 |
| :------: | :------: |
| ** | 指数 (最高优先级) |
| ~ + - | 按位翻转, 一元加号和减号 (最后两个的方法名为 +@ 和 -@) |
| * / % // | 乘，除，取模和取整除 |
| + - | 加法减法 |
| >> << | 右移，左移位运算符 |
| & | AND |
| \| ^ | 或 异或--位运算 |
| <= < > >= | 比较运算符 |
| <> == != | 等于运算符 |
| = %= /= //= -= += *= **= | 赋值运算符 |
| is is not | 身份运算符 |
| in not in | 成员运算符 |
| not or and | 逻辑运算符 |

#### List(列表)

列表list是Python中使用最频繁的数据类型;列表是写在`[]`方括号之间，用逗号隔开的元素列表，列表中元素的类型可以不同，和字符串一样，列表也可以被索引和切片。列表被切片后返回一个包含所需元素的新列表，列表还支持串联操作，使用+符号。

```markdown
# 注意：
1、List写在方括号之间，元素用逗号隔开
2、和字符串一样，list可以被索引和切片
3、List可以使用+操作符进行拼接
4、List中的元素是可以改变的
```

##### 示例列表的大部分方法

以下方法是作用在list对象上的。而len()是将list、string或其他类型作为参数的函数
| 方法 | 描述 |
| :------: | :------: |
| list.append(elem) | (elem表示element)把一个元素添加到列表的结尾，相当于 a[len(a):] = [elem]|
| list.insert(index, elem) | 在指定位置插入一个元素。第一个参数是准备插入到其前面的那个元素的索引，例如 a.insert(0, x) 会插入到整个列表之前，而 a.insert(len(a), x) 相当于 a.append(x) |
| list.extend(list2) | 在列表list2最后添加元素，相当于 a[len(a):] = list2;在一个列表使用+或+=和extend()类似;extend()方法只接受一个列表作为参数 |
| list.index(elem) | 从列表的开头搜索给定元素并返回其索引。如果没有匹配的元素就会返回一个错误 |
| list.remove(elem) | 搜索给定元素的第一个实例并将其删除,如果不存在则抛出ValueError |
| list.sort() | 对列表中的元素进行排序,不要直接返回结果,后面使用sorted()函数显示更好 |
| list.reverse() | 倒排列表中的元素 |
| list.pop([i]) | 从列表的指定位置删除元素，并将其返回。如果没有指定索引，a.pop()返回最右边一个元素,并返回所删除的值。方法中 i 两边的方括号表示这个参数是可选的，而不是要求你输入一对方括号，你会经常在Python库参考手册中遇到这样的标记 |
| list.clear() | 移除列表中的所有项，等于del a[:] |
| list.count(x) | 返回 x 在列表中出现的次数 |
| list.copy() | 返回列表的浅复制，等于a[:] |

##### 将列表当做堆栈使用

列表方法使得列表可以很方便的作为一个堆栈来使用，堆栈作为特定的数据结构，最后一个插入，最先取出（后进先出）。用 append() 方法可以把一个元素添加到堆栈顶。用不指定索引的 pop() 方法可以把一个元素从堆栈顶取出来

##### 将列表当作队列使用

也可以把列表当做队列用，其中先添加的元素被最先取出 (“先进先出”)；但是列表用作这样的目的相当低效。在列表的最后添加或者弹出元素速度快，然而在列表里插入或者从头部弹出速度却很慢（因为所有其他的元素都必须移动一位）;要实现一个队列，collections.deque被设计用于快速从两端操作

##### 列表推导式

列表推导式提供了从序列创建列表的简单途径。常见的用法是把某种操作应用于序列或可迭代对象的每个元素上，然后使用其结果来创建列表，或者通过满足某些特定条件元素来创建子序列。
每个列表推导式都在 for 之后跟一个表达式，然后有零到多个 for 或 if 子句。返回结果是一个根据表达从其后的 for 和 if 上下文环境中生成出来的列表。如果希望表达式推导出一个元组，就必须使用括号。

##### 嵌套列表推导式和del语句

列表推导式中的初始表达式可以是任何表达式，包括另一个列表推导式。
*del语句*：使用del语句可以从一个列表中依索引而不是值来删除一个元素。这与使用 pop() 返回一个值不同。可以用 del 语句从列表中移除切片，或清空整个列表（我们以前介绍的方法是空列表赋值给一个指定的切片)，del也可以用来删除整个变量

#### Tuple（元组）

元组（tuple）与列表类似，不同之处在于元组的元素不能修改。元组写在`()`小括号里，元素之间用逗号隔开。元组中的元素类型也可以不相同，并且通过解包或索引来访问，而列表通过迭代访问

元组是不可变序列，给元祖的单独一个元素赋值是不允许的，空元祖由一对空圆括号创建，含有一个元素的元祖可以通过再此元祖之后添加一个逗号创建。

```markdown
# 元组与字符串类似，可以被索引且下标索引从0开始，也可以进行截取/切片。其实，可以把字符串看作一种特殊的元组。改元组元素的操作是非法的,元组也支持用+操作符
# 虽然tuple的元素不可改变，但它可以包含可变的对象，比如list列表。构造包含0个或1个元素的tuple是个特殊的问题，所以有一些额外的语法规则：
tup1 = ()    # 空元组
tup2 = (20,) # 单元组，需要在元素后添加逗号

# string、list和tuple都属于sequence（序列）。
# 注意：
1、与字符串一样，元组的元素不能修改;创建单元素元组，需要在值之后加上一个逗号。没有逗号，Python 会假定这只是一对额外的圆括号，并不创建元组
2、元组也可以被索引和切片，方法一样
3、注意构造包含0或1个元素的元组的特殊语法规则
4、元组也可以使用+操作符进行拼接
```

##### 元组内置函数

| 方法 | 描述 |
| :------: | :------: |
| len(tuple) | 计算元组元素个数 |
| max(tuple) | 返回元组中元素最大值 |
| min(tuple) | 返回元组中元素最小值 |
| tuple(seq) | 将列表转换为元组 |

#### range(范围)

range类型是不可变的数字序列，通常用于在for循环中循环特定的次数

### 文本序列类型

Python中的文本数据由str对象或字符串处理，字符串是Unicode的不可变序列，字符串文字由多种方式编写

#### String(字符串)

```markdown
# “str”为内置字符串类;用单引号或双引号栝起来，同时使用反斜杠\转义特殊字符，若不想让反斜杠产生转义效果，在字符串前添加一个r，表示原始字符串，字符串文字可以跨越多行，但每行末尾必须有一个反斜杠\来转义换行符。三引号内的字符串文字"""或”''可以跨越多行的文字；python中的字符串有两种索引方式，第一种是从左往右依次从0开始递增，第二种是从右往左依次从-1开始递减
# 注意：没有单独的字符类型，一个字符就是长度为1的字符串，还可以对字符串进行切片，获取一段字符串，形式为变量[头下标:尾下标]；截取范围是前闭后开[2:]，并且两个索引都可以省略；python字符串不能被改变，如向一个索引位置赋值，word[0]='m'会导致错误

# 注意：
1、反斜杠可以用来转义，使用r可以让反斜杠不发生转义。
2、字符串可以用+运算符连接在一起，用*运算符重复。
3、Python中的字符串有两种索引方式，从左往右以0开始，从右往左以-1开始。
4、Python中的字符串不能改变。因此构造*new*字符串来表示计算值；例如表达式('hello'+'there')构建新的hellothere字符串
```

##### 常见的字符串方法

字符串实现常见的序列操作,以及以下的描述的一些方法

| 方法 | 描述 |
| :------: | :------: |
| str.capitalize() | 返回字符串的副本，其首字符大写，其余字符小写 |
| str.lower(),str.upper() | 返回字符串的副本，并将所有套接字符转换为小写或大写 |
| str.strip([chars]) | 返回删除了开头和结尾字符的字符串副本。 chars参数是一个字符串，指定要删除的字符集。如果省略或None，则chars参数默认为删除空格 |
| str.isalpha()/str.isdigit()/str.isspace()...  | 测试所有字符串字符是否在各种字符类中 |
| str.find() | 检测字符串中是否包含子字符串str，如果指定start和end范围，则检查是否包含在指定范围内，如果包含子字符串返回最低的索引值，否则返回-1 |

str.splitlines([keepends])      返回字符串中的行列表，在行边界处断开。除非给出keepends且为true，否则换行符不包括在结果列表中;此方法在以下边界处进行分割
| 表示 | 描述 |
| :------: | :------: |
| \n | 换行 |
| \r | 回程 |
| \r\n | 回车+换行 |
| \v or \x0b | 行列表 |
| \f or \x0c | 换页 |
| \x1c | 文件分隔符 |
| \x1d | 组分隔符 |
| \x1e | 记录分隔符 |
| \x85 | 下一行（C1控制代码） |
| \u2028 | 行分隔符 |
| \u2029 | 段落分隔符 |

##### printf风格的字符串格式化

字符串具有一种特殊内置操作：使用%(取模)运算符，类似于C语言的sprintf()

转换标记符包含两个或更多字符并具有以下组成，且必须遵循此处规定的顺序：

1. '%' 字符，用于标记转换符的起始
2. 映射键（可选），由加圆括号的字符序列组成 (例如 (somename))
3. 转换标志（可选），用于影响某些转换类型的结果
4. 最小字段宽度（可选）。 如果指定为 '*' (星号)，则实际宽度会从 values 元组的下一元素中读取，要转换的对象则为最小字段宽度和可选的精度之后的元素
5. 精度（可选），以在 '.' (点号) 之后加精度值的形式给出。 如果指定为 '*' (星号)，则实际精度会从 values 元组的下一元素中读取，要转换的对象则为精度之后的元素
6. 长度修饰符（可选）
7. 转换类型

转换标志字符为：

| 标志  | 含义  |
| :---------: | :---------: |
| '#' | 值的转换将使用“替代形式”(具体定义见下文) |
| '0' | 转换将为数字值填充零字符  |
| '-' | 转换值将靠左对齐（如果同时给出 '0' 转换，则会覆盖后者）  |
| ' ' | (空格) 符号位转换产生的正数（或空字符串）前将留出一个空格  |
| '+' | 符号字符 ('+' 或 '-') 将显示于转换结果的开头(会覆盖 "空格" 标志) |

转换类型为：

| 转换符 | 含义 | 注释 |
| :---------: | :---------: | :---------: |
| d | 有符号十进制整数 |  |
| i | 有符号十进制整数 |  |
| o | 有符号八进制数 | 1 |
| u | 过时类型  等价于 'd' | 6 |
| x | 有符号十六进制数（小写） | 2 |
| X | 有符号十六进制数（大写） | 2 |
| e | 浮点指数格式（小写） | 3 |
| E | 浮点指数格式（大写） | 3 |
| f | 浮点十进制格式 | 3 |
| F | 浮点十进制格式 | 3 |
| g | 浮点格式。 如果指数小于 4 或不小于精度则使用小写指数格式，否则使用十进制格式 | 4 |
| G | 浮点格式。 如果指数小于 4 或不小于精度则使用大写指数格式，否则使用十进制格式 | 4 |
| c | 单个字符（接受整数或单个字符的字符串） |  |
| r | 字符串（使用 repr() 转换任何 Python 对象） | 5 |
| s | 字符串（使用 str() 转换任何 Python 对象） | 5 |
| a | 字符串（使用 ascii() 转换任何 Python 对象） | 5 |
| % | 不转换参数，在结果中输出一个 '%' 字符 |  |

注释:

1. 此替代形式会在第一个数码之前插入标示八进制数的前缀 ('0o')
2. 此替代形式会在第一个数码之前插入 '0x' 或 '0X' 前缀（取决于是使用 'x' 还是 'X' 格式）
3. 此替代形式总是会在结果中包含一个小数点，即使其后并没有数字,小数点后的数字位数由精度决定，默认为 6
4. 此替代形式总是会在结果中包含一个小数点，末尾各位的零不会如其他情况下那样被移除,小数点前后的有效数码位数由精度决定，默认为 6
5. 如果精度为 N，输出将截短为 N 个字符
6. 参见[PEP 237](http://www.python.org/dev/peps/pep-0237)

### 二进制序列类型 -- bytes、bytearray、memoryview

用于操作二进制数据的核心内置类型是bytes和bytearray，它们支持memoryview使用buffer protocol协议来访问其他二进制对象的内存而无需做一个复制
该array模块支持有效存储基本数据类型，如32位整数和IEEE754双精度浮点值

#### bytes对象

```markdown

字节对象是单个字节不可变序列，由于主要的二进制协议大多基于ASCII文本编码，因此字节对象提供了几种方法，这些方法仅在处理ASCII兼容数据时有效，并且以各种其他方式与字符串对象密切相关
字节文字的语法和字符串文字的语法类似，只是添加了前缀 b
字节文字中只允许使用ASCII字符（无论声明的源代码编码如何）。必须使用适当的转义序列将超过127的任何二进制值输入到字节文字中。与字符串文字一样，字节文字也可以使用r前缀来禁用转义序列的处理
虽然字节文字和表示基于ASCII文本，但字节对象实际上表现为不可变的整数序列，序列中的每个值都受到限制，以致0 <= x < 256（试图违反此限制将触发ValueError）。这是故意强调的，虽然许多二进制格式包括基于ASCII的元素，并且可以使用一些面向文本的算法进行有用的操作，但对于任意二进制数据通常不是这种情况（盲目地将文本处理算法应用于非二进制数据格式） ASCII兼容通常会导致数据损坏）
```

除了文字形式之外，还可通过其他方式创建字节对象：
* 指定长度的零填充字节对象：types(10)
* 从可迭代的整数：bytes(range(10))
* 通过buffer protocol协议复制现有二进制数据：bytes(obj)

#### bytearray对象

bytearray对象是bytes对象的可变副本，由于bytearray是可变的，还支持可变序列操作

bytearray对象没有专用的文字语法，而是始终通过调用构造函数来创建：
* 创建一个空实例：bytearray()
* 创建具有给定长度的零填充实例：bytearray(10)
* 从可迭代的整数：bytearray(range(10))
* 通过buffer protocol协议复制现有二进制数据：bytearray(b'Hello')

bytes和bytearray对象都支持公共序列操作，它们不仅与相同类型的操作数互操作，而且与任何类似字节的对象互操作；由于此灵活性，它们可以在操作中自由混合而不会导致错误，但结果的返回类型可能取决于操作数的顺序

#### printf风格字节格式化

字节对象(bytes/bytearray)有一个独特的内置操作：%运算符(模数)也成为字节格式化或插值运算符，如果format需要单个参数，则值可以是单个非元组对象，否则值必须是具有格式字节对象或单个映射对象(字典)指定的项目数的元组

其他和字符串格式化相同

#### memory views

memoryview对象允许Python代码访问支持buffer protocol的对象的内部数据而无需复制

### Set类型 -- set、frozenset

#### Set（集合）

集合是一个无序不重复元素的集。set对象是由具有唯一性的hashable对象组成的无序多项集合。基本功能包括成员关系检测和消除重复元素。集合对象还支持数学集合类运算，如并集，交集，差集和对称差集等;作为无序集合，不记录元素位置和插入顺序，集合不支持索引、切片或其他类似序列的行为

目前有两种内置集合类型，set和frozenset，set类型是可变的，其元素可以用add()和remove()方法改变，所以不具有哈希值且不能被用作字典的键或其他集合的元素。frozenset类型是不可变的并且为hashable，其内容在被创建后不能再改变，因此可以用作字典的键和其他集合的元素

可以用大括号`{}`创建集合。注意：如果要创建一个空集合，你必须用 set() 而不是 {} ；后者创建一个空的字典，集合也支持推导式形式

```markdown
# 注意：
1、从列表创建集合,可使用set()函数
2、集合可以包含任何数据类型的值;但如果添加集合已有的值，不会发生任何事情，也不会报错
```

##### 集合内置方法

| 方法 | 描述 |
| :------: | :------: |
| add() | add() 方法接受单个可以是任何数据类型的参数，并将该值添加到集合之中 |
| update() | update() 方法仅接受一个集合作为参数，并将其所有成员添加到初始列表中;如果有重复值，将会被忽略 |
| discard() | discard() 接受一个单值作为参数，并从集合中删除该值;针对一个集合中不存在的值调用 discard()方法，它不进行任何操作。不产生错误 |
| remove() | remove() 方法也接受一个单值作为参数，也从集合中将其删除;针对一个集合中不存在的值调用 remove()方法将会引发KeyError错误 |
| pop() | 从集合删除某个值并返回该值 |

### 映射类型 -- 字典

#### Dict（字典）

一个mapping对象映射可哈希值到任意对象，mapping是可变对象；目前只有一种标准的mapping类型,即字典。字典以关键字作为索引，关键字可以是任意不可变类型，通常是字符串和数字。如果一个元组中只包含数字、字符串和元组，那么这个元组也可以作为关键字。如果元组直接或间接的包含了可变对象，那么就不能作为关键字。关键字必须使用不可变类型，也就是说list和包含可变类型的tuple不能做关键字。在同一个字典中，关键字还必须互不相同。可以将字典看作是一个键值对应的集合，在一个字典中键必须是唯一的，字典主要操作是关键字存储和解析值。

```markdown
# 注意：

1、字典是一种映射类型，它的元素是键值对。构造函数dict()直接从键值对sequence中构建字典，也可以进行推导
2、字典的关键字必须为不可变类型，且不能重复。
3、创建空字典使用{}

# 字典遍历技巧，在字典中遍历时，关键字和对应的值可以使用 items() 方法同时解读出来。在序列中遍历时，索引位置和对应值可以使用 enumerate() 函数同时得到。同时遍历两个或更多的序列，可以使用 zip() 组合。要反向遍历一个序列，首先指定这个序列，然后调用 reversesd() 函数。要按顺序遍历一个序列，使用 sorted() 函数返回一个已排序的序列，并不修改原值。

```

##### python字典常用方法：

| 方法 | 描述 |
| :------: | :------: |
| dict.clear() | 删除字典内所有元素 |
| dict.copy() | 返回原字典的浅拷贝 |
| dict.fromkeys() | 创建一个新字典，以序列seq中元素做字典的键，val为字典所有键对应的初始值 |
| dict.get(key, default=None) | 返回指定键的值，如果值不在字典中返回default值 |
| key in dict | 如果键在字典dict里返回true，否则返回false |
| dict.items() | 以列表返回可遍历的(键, 值) 元组数组 |
| dict.keys() | 以列表返回一个字典所有的键 |
| dict.setdefault(key, default=None) | 和get()类似, 但如果键不存在于字典中，将会添加键并将值设为default |
| dict.update(dict2) | 把字典dict2的键/值对更新到dict里 |
| dict.values() | 以列表返回字典中的所有值 |

##### 字典视图对象(dictionary view objects)

这些对象通过dict.keys()、dict.values()、dict.items()返回的是视图对象，它们提供字典条目的动态视图，意味着当字典更改时，视图则会反应这些更改

### 上下文管理器类型(Context Manager Types)

python的with语句支持由上下文管理器定义的运行时上下文的概念，这是使用一对方法实现的，这些方法允许用户定义的类定义在语句体执行之前输入的运行时上下文，并在语句结束时退出

ython定义了几个上下文管理器，以支持简单的线程同步，文件或其他对象的快速关闭，以及对活动十进制算术上下文的简单操作。除了实现上下文管理协议之外，特定类型不会被特别处理。

Python的生成器和contextlib.contextmanager装饰器提供了一种实现这些协议的便捷方式。如果使用contextlib.contextmanager装饰器修饰生成器函数，它将返回实现必要__enter__()和__exit__()方法的上下文管理器，而不是由未修饰的生成器函数生成的迭代器。

请注意，Python / C API中Python对象的类型结构中没有针对这些方法的特定插槽。想要定义这些方法的扩展类型必须将它们作为普通的Python可访问方法提供。与设置运行时上下文的开销相比，单个类字典查找的开销可以忽略不计

## 内置异常

Python中所有异常必须为一个派生自BaseException类的实例。在try带有except提及特定类的子句的语句中，该子句也会处理任何派生自该类的异常类,(但不处理它所派生出的异常类)通过子类化创建的两个不相关异常类永远是不等效的，既使它们具有相同的名称。try...except语句有一个可选的else字句，使用时必须放在所有except字句后面，raise语句允许程序强制发生指定的异常，raise唯一的参数就是抛出的异常，这个参数必须是一个异常实例或一个异常类(派生自Exception的类)

### 基类(Base classes)

以下异常主要被用作其他异常的基类

[详情请点击此处查看](https://devdocs.io/python~3.7/library/exceptions#base-classes)

### Concrete exceptions

[点击此处查看](https://devdocs.io/python~3.7/library/exceptions#concrete-exceptions)

#### OS 异常

[点击此处查看](https://devdocs.io/python~3.7/library/exceptions#concrete-exceptions)

### 警告

[详情点击此处查看](https://devdocs.io/python~3.7/library/exceptions#warnings)

### 异常层次结构

```markdown
BaseException
 +-- SystemExit
 +-- KeyboardInterrupt
 +-- GeneratorExit
 +-- Exception
      +-- StopIteration
      +-- StopAsyncIteration
      +-- ArithmeticError
      |    +-- FloatingPointError
      |    +-- OverflowError
      |    +-- ZeroDivisionError
      +-- AssertionError
      +-- AttributeError
      +-- BufferError
      +-- EOFError
      +-- ImportError
      |    +-- ModuleNotFoundError
      +-- LookupError
      |    +-- IndexError
      |    +-- KeyError
      +-- MemoryError
      +-- NameError
      |    +-- UnboundLocalError
      +-- OSError
      |    +-- BlockingIOError
      |    +-- ChildProcessError
      |    +-- ConnectionError
      |    |    +-- BrokenPipeError
      |    |    +-- ConnectionAbortedError
      |    |    +-- ConnectionRefusedError
      |    |    +-- ConnectionResetError
      |    +-- FileExistsError
      |    +-- FileNotFoundError
      |    +-- InterruptedError
      |    +-- IsADirectoryError
      |    +-- NotADirectoryError
      |    +-- PermissionError
      |    +-- ProcessLookupError
      |    +-- TimeoutError
      +-- ReferenceError
      +-- RuntimeError
      |    +-- NotImplementedError
      |    +-- RecursionError
      +-- SyntaxError
      |    +-- IndentationError
      |         +-- TabError
      +-- SystemError
      +-- TypeError
      +-- ValueError
      |    +-- UnicodeError
      |         +-- UnicodeDecodeError
      |         +-- UnicodeEncodeError
      |         +-- UnicodeTranslateError
      +-- Warning
           +-- DeprecationWarning
           +-- PendingDeprecationWarning
           +-- RuntimeWarning
           +-- SyntaxWarning
           +-- UserWarning
           +-- FutureWarning
           +-- ImportWarning
           +-- UnicodeWarning
           +-- BytesWarning
           +-- ResourceWarning
```

## 文本处理服务

### 常见字符串操作

| 字符串常量 | 描述 |
| :------: | :------: |
| string.ascii_letters | 以下lowercase和uppercase两个常量的串联 |
| string.ascii_lowercase | 小写字母abcdefghijklmnopqrstuvwxyz |
| string.ascii_uppercase | 大写字母ABCDEFGHIJKLMNOPQRSTUVWXYZ |
| string.digits | 字符串0123456789 |
| string.hexdigits | 字符串0123456789abcdefABCDEF |
| string.octdigits | 字符串01234567 |
| string.punctuation | ASCII字符的字符串,在C语言环境中被视为标点字符 |
| string.printable | 被视为可打印的ASCII字符串，这是一个组合digits、ascii_letters、punctuation、whitespace |
| string.whitespace | 包含所有被视为空格的ASCII字符的字符串，包括字符间隔,tab, linefeed, return, formfeed, and vertical tab |

### 格式化字符串语法

str.format()格式化字符串，使用{}括起来要替换的字段，不在花括号之内的内容会不加修改的打印到输出中。它将不加改变地复制到输出中。如果你需要在文字文本中包含一个大括号字符，可以通过加倍来转义：{{ }}

替换字段语法如下：

```markdown
replacement_field ::=  "{" [field_name] ["!" conversion] [":" format_spec] "}"
field_name        ::=  arg_name ("." attribute_name | "[" element_index "]")*
arg_name          ::=  [identifier | digit+]
attribute_name    ::=  identifier
element_index     ::=  digit+ | index_string
index_string      ::=  <any source character except "]"> +
conversion        ::=  "r" | "s" | "a"
format_spec       ::=  <described in the next section>
```

### Mini-Language格式化

```python
# 格式化整数和浮点数还可以指定是否补0和整数与小数的位数：
print('%2d-%02d' % (3, 1))
print('%.2f' % 3.1415926)

# 字符串里面的%是一个普通字符怎么办？这个时候就需要转义，用%%来表示一个%

# 利用format()方法进行格式化字符串，它会用传入的参数依次替换字符串内的占位符{0},{1}......,但要比占位符麻烦。
'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
```

字符串 % 操作，python有一个printf格式将字符串放在一起，%操作符将左边(%d int, %s string, %f或%g floating point)以printf-type格式化，匹配在右边的元组里的值

| 格式化符号 | 替换内容类型 |
| :------: | :------: |
| %s | 字符串 |
| %d | 十进制整数 |
| %x | 十六进制整数 |
| %f | 浮点数,精度默认为6位 |
| %g | 浮点数,精度默认为6位,不尾随0 |

### re--正则表达式

re模块与Perl语言类似的正则表达式匹配操作，模式和被搜索的字符串可以使Unicode字符串也可以是8为字节串(bytes)，但Unicode和8位字节串不能混用。因反斜杠\在Python中和在正则表达式中，都具有将把特殊字符转换为普通字符串的作用。所以在正则表达式模式要写成`\\\\`。解决办法是对于正则表达式样式用带有r前缀的表示Python原始字符的方法。

#### 常用正则表达式



[详细点击此处了解](https://devdocs.io/python~3.7/library/re)

### difflib -- helpers for computing deltas

[详细点击此处了解](https://devdocs.io/python~3.7/library/difflib)

## Python条件判断语句

```markdown
# if语句
if condition_1:
    statement_block_1
elif condition_2:
    statement_block_2
else:
    statement_block_3

注意
1、每个条件后面要使用冒号（:），表示接下来是满足条件后要执行的语句块
3、在Python中没有switch – case语句

# 实例演示狗的年龄计算判断

age = int(input("输入狗的年龄:"))
print()

if age > 2:
    human = 22 + (age - 2) * 5
    print("human years", human)
elif age == 1:
    print("about 14 human years")
elif age == 2:
    print("about 22 human years")
else:
    print("年龄不能小于0")

input('press Return>')
```

if语句常用操作运算符
|操作符|描述|
| :------: | :------: |
|<|小于|
|<=|小于或等于|
|>|大于|
|>=|大于或等于|
|==|等于，比较对象是否相等|
|!=|不等于|

```python
# 演示猜数字
#!/usr/local/bin/python3
# -*- coding: UTF-8 -*-

number = 1
guess = 0
print("猜数字")
while guess != number:
    guess = int(input("输入要猜的数字："))
    if guess == number:
        print("holly shit,你猜对了!")
    elif guess < number:
        print("猜的太小了")
    elif guess > number:
        print("猜的太大了")
```

## 循环

```python
# python循环语句有for和while
# 使用while循环计算1~100和

count = 1
num_sum = 0

while count <= 100:
    num_sum += count
    count += 1
print("1~100和是:", num_sum)

# for循环可以遍历任何序列的项目，如列表或字符串,for实例使用break语句，break语句用于跳出当前循环体

foods = ["apple", "beef", "fish"]
for egg in foods:
    if egg == "beef":
        print("两个不同的")
        break   # 此处跳出循环，下一句不再输出
    print("好吃的" + egg)
else:
    print("没有beef了")
print("结束选餐")

# range()函数，如果需要遍历数字序列，可以使用内置range()函数，可以自动生成数列
for i in range(7):
    print(i)
# 也可使用range指定区间的值
for i in range(1, 6):
    print(i)

# 可使range从指定数字开始并指定增量。(可以是负数，有时叫做步长)
for i in range(1, 9, 2):  # 从1到9步长为2
    print(i)

# 结合range()和len()函数遍历一个序列的索引
a = ['lee', 'bob', 'bruce']
for i in range(len(a)):
    print(i, a[i])

```

## 函数

```markdown
# 函数对象是通过函数定义创建，对函数唯一的操作就是调用它。
# 函数的参数
## 默认参数：
# 新的power(x, n)函数定义没有问题，但是，旧的调用代码失败了，原因是我们增加了一个参数，导致旧的代码因为缺少一个参数而无法正常调用，这时候可以用默认参数
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
# 注意：一是必选参数在前，默认参数在后，否则Python的解释器会报错。设置默认参数技巧--当函数有多个参数时，把变化大的参数放前面，变化小的参数放后面。变化小的参数就可以作为默认参数
# 定义默认参数要牢记一点：默认参数必须指向不变对象！

## 位置参数：
def power(x, n):   #x就是一个位置参数
    s = 1
    while n > 0:
        n = n -1
        s = s * x
    return s

## 可变参数
# 可变参数就是传入的参数个数是可变的，可以是0个到任意个
# 例：给定一组数字a，b，c……，请计算a2 + b2 + c2 + ……
# 定义一个list或tuple参数
def calc(numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
# 定义可变参数和定义一个list或tuple参数相比，仅仅在参数前面加了一个*号。在函数内部，参数numbers接收到的是一个tuple，因此，函数代码完全不变。但是，调用该函数时，可以传入任意个参数，包括0个参数。Python允许你在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去。*numbers表示把numbers这个list或tuple的所有元素作为可变参数传进去
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum

## 关键字参数，函数也可以使用kwarg=value的关键字参数形式被调用。
# 可变参数列表：一个最不常用的选择是可以让函数调用可变个数的参数.这些参数被包装进一个元组tuple(查看元组和序列).在这些可变个数的参数之前,可以有零到多个含参数名的参数:
def arithmetic_mean(*args):
    sum = 0
    for x in args:
        sum += x
    return sum

print(arithmetic_mean(45,32,89,78))
print(arithmetic_mean(8989.8,78787.78,3453,78778.73))
print(arithmetic_mean(45,32))
print(arithmetic_mean(45))
print(arithmetic_mean())

关键字参数有什么用？它可以扩展函数的功能。比如，在person函数里，我们保证能接收到name和age这两个参数，但是，如果调用者愿意提供更多的参数，我们也能收到。试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字参数来定义这个函数就能满足注册的需求

## 命名关键字参数
# 对于关键字参数，函数的调用者可以传入任意不受限制的关键字参数。至于到底传入了哪些，就需要在函数内部通过kw检查。

# 以person()函数为例，我们希望检查是否有city和job参数
def person(name, age, **kw):
    if 'city' in kw:
        # 有city参数
        pass
    if 'job' in kw:
        # 有job参数
        pass
    print('name:', name, 'age:', age, 'other:', kw)

# 命名关键字参数必须传入参数名，这和位置参数不同。如果没有传入参数名，调用将报错
# 使用命名关键字参数时，要特别注意，如果没有可变参数，就必须加一个*作为特殊分隔符。如果缺少*，Python解释器将无法识别位置参数和命名关键字参数：
def person(name, age, city, job):
    # 缺少 *，city和job被视为位置参数
    pass

## 参数组合
# Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数

# 定义一个函数，包含上述若干种参数：

def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)

# 在函数调用的时候，Python解释器自动按照参数位置和参数名把对应的参数传进去。
>>> f1(1, 2)
a = 1 b = 2 c = 0 args = () kw = {}
>>> f1(1, 2, c=3)
a = 1 b = 2 c = 3 args = () kw = {}
>>> f1(1, 2, 3, 'a', 'b')
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {}
>>> f1(1, 2, 3, 'a', 'b', x=99)
a = 1 b = 2 c = 3 args = ('a', 'b') kw = {'x': 99}
>>> f2(1, 2, d=99, ext=None)
a = 1 b = 2 c = 0 d = 99 kw = {'ext': None}

# 通过一个tuple和dict，你也可以调用上述函数：

>>> args = (1, 2, 3, 4)
>>> kw = {'d': 99, 'x': '#'}
>>> f1(*args, **kw)
a = 1 b = 2 c = 3 args = (4,) kw = {'d': 99, 'x': '#'}
>>> args = (1, 2, 3)
>>> kw = {'d': 88, 'x': '#'}
>>> f2(*args, **kw)
a = 1 b = 2 c = 3 d = 88 kw = {'x': '#'}
# 所以，对于任意函数，都可以通过类似func(*args, **kw)的形式调用它，无论它的参数是如何定义的。
```

### 递归函数

```py
# 如果一个函数在内部调用自身本身，这个函数就是递归函数.理论上，所有的递归函数都可以写成循环的方式，但循环的逻辑不如递归清晰
# 使用递归函数需要注意防止栈溢出。在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出。
# 解决递归调用栈溢出的方法是通过尾递归优化，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的
# 尾递归是指，在函数返回的时候，调用自身本身，并且，return语句不能包含表达式。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况。

```

### python高级特性：切片、迭代、列表推导式、生成器、迭代器

```py
# 切片在列表上就像字符串一样，可以用来改变列表的子部分
# 给定一个list或tuple，我们可以通过for循环来遍历这个list或tuple，这种遍历我们称为迭代(Iteration)
# 迭代是访问集合元素的一种方式，迭代器可以记住遍历位置的对象。迭代器对象从集合的第一个元素开始访问，知道所有元素结束，迭代器只能往前，迭代器有两个方法iter()和next()。字符串、列表和元组对象都可用于创建迭代器

list = [1, 2, 3, 4]  # 创建列表list
it = iter(list)  # 创建迭代器对象it
print(next(it))  # 输出迭代器下一个元素

# 迭代器对象可以用for语句进行遍历
list = [1, 2, 3, 4]  # 创建列表list
it = iter(list)  # 创建迭代器对象it
for x in it:
    print(x, end=" ")
```

```py
# 列表推导式即List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式
#for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：
print([x * x for x in range(1, 11) if x % 2 == 0])

# 运用列表推导式，可以写出非常简洁的代码。例如，列出当前目录下的所有文件和目录名，可以通过一行代码实现
import os   # 导入os模块
print([dir for dir in os.listdir('.')]) # 列出当前目录下文件和目录名(包含隐藏文件或目录)
```

```py
使用了yield的函数称为生成器(generator)，生成器是一个返回迭代器的函数，简单的理解就是生成器就是一个迭代器。在调用生成器运行的过程中，每次遇到yield函数会暂停并保存当前所有的运行信息，返回yield的值，并在下一次next()方法从当前位置继续运行。
# 使用生成器生成斐波那契数列,斐波那契数列：即两个元素的和确定下一个数
import sys
def fibonacci(x):
    a, b, count = 0, 1, 0
    while True:
        if (count > x):
            return
        yield a # 将fibonacci函数变成generator，print(a)改成yield a
        a, b = b, a + b
        count += 1
f = fibonacci(21)   # f是一个迭代器，由生成器返回生成

while True:
    try:
        print(next(f), end="")
    except StopIteration:
        sys.exit()

```

```py
# 可以直接作用于for循环的数据类型有以下几种：
# 一是集合数据类型，如list、tuple、dict、set、str等；
# 二是generator，包括生成器和带yield的generator function。
# 这些可以直接作用于for循环的对象统称为可迭代对象：Iterable

# 可以使用isinstance()判断一个对象是否是Iterable对象
# 生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。把list、dict、str等Iterable变成Iterator可以使用iter()函数
# 为什么list、dict、str等数据类型不是Iterator？

# 这是因为Python的Iterator对象表示的是一个数据流，Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出StopIteration错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算下一个数据，所以Iterator的计算是惰性的，只有在需要返回下一个数据时它才会计算。
# Iterator甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的

# 总结：
# 凡是可作用于for循环的对象都是Iterable类型；
# 凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列；
# 集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过iter()函数获得一个Iterator对象。

# Python的for循环本质上就是通过不断调用next()函数实现的，例如：
for x in [1, 2, 3, 4, 5]:
    pass

# 实际上完全等价于：
# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```

## Python3 File(文件) 方法

```py
open() 将会返回一个 file 对象，基本语法格式如下:
open(filename, mode)
filename：包含了你要访问的文件名称的字符串值。
mode：决定了打开文件的模式：只读，写入，追加等。所有可取值见如下的完全列表。这个参数是非强制的，默认文件访问模式为只读(r)

Python open() 方法用于打开一个文件，并返回file对象，在对文件进行处理过程都需要使用到这个函数，如果该文件无法被打开，会抛出 OSError。
注意：使用 open() 方法一定要保证关闭文件对象，即调用 close() 方法。
open() 函数常用形式是接收两个参数：文件名(file)和模式(mode)。

基本语法格式如下:
open(file, mode='r')

完整的语法格式为：
open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)
参数说明:
file: 必需，文件路径（相对或者绝对路径）。
mode: 可选，决定了打开文件的模式：只读，写入，追加等。这个参数是非强制的，默认文件访问模式为只读(r)
buffering: 设置缓冲
encoding: 一般使用utf8
errors: 报错级别
newline: 区分换行符
closefd: 传入的file参数类型
opener:可以通过传递可调用的* opener *来使用自定义opener。 然后通过使用*（* file *，* flags *）调用* opener *获得文件对象的基础文件描述符。 * opener *必须返回一个打开的文件描述符（将os.open作为* opener *传递给类似于传递None的功能）。
```

mode 参数有：

|模式|描述|
| :------: | :------: |
|r|以只读方式打开文件。文件的指针将会放在文件的开头。这是默认模式。|
|rb|以二进制格式打开一个文件用于只读。文件指针将会放在文件的开头。|
|r+|打开一个文件用于读写。文件指针将会放在文件的开头。|
|rb+|以二进制格式打开一个文件用于读写。文件指针将会放在文件的开头。|
|w|打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。|
|wb|以二进制格式打开一个文件只用于写入。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。|
|w+|打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。|
|wb+|以二进制格式打开一个文件用于读写。如果该文件已存在则打开文件，并从开头开始编辑，即原有内容会被删除。如果该文件不存在，创建新文件。|
|a|打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。|
|ab|以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。也就是说，新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入。|
|a+|打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写。|
|ab+|以二进制格式打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾。如果该文件不存在，创建新文件用于读写。|

| | | | | | | |
| :------: | :------: | :------: | :------: | :------: | :------: | :------: | :------: |
|模式|r|r+|w|w+|a|a+|
|读|=+|=+||=+||=+|
|写||=+|=+|=+|=+|=+|
|创建|||=+|=+|=+|=+|
|覆盖|||=+|=+|||
|指针在开始|=+|=+|=+|=+|||
|指针在结尾|||||=+|=+|

将字符串写入到文件 foo.txt 中

```py
# 打开一个文件
f = open("/tmp/foo.txt", "w")
f.write( "Python 是一个非常好的语言。\n是的，的确非常好!!\n" )
# 关闭打开的文件
f.close()

# 第一个参数为要打开的文件名。
# 第二个参数描述文件如何使用的字符。 mode 可以是 'r' 如果文件只读, 'w' 只用于写 (如果存在同名文件则将被删除), 和 'a' 用于追加文件内容; 所写的任何数据都会被自动增加到末尾. 'r+' 同时用于读写。 mode 参数是可选的; 'r' 将是默认值。
```

file 对象使用 open 函数来创建，下表列出了 file 对象常用的函数：
| | |
| :------: | :------: |
|file.close()|关闭文件。关闭后文件不能再进行读写操作。|
|file.flush()|刷新文件内部缓冲，直接把内部缓冲区的数据立刻写入文件, 而不是被动的等待输出缓冲区写入。|
|file.fileno()|返回一个整型的文件描述符(file descriptor FD 整型, 可以用在如os模块的read方法等一些底层操作上。|
|file.isatty()|如果文件连接到一个终端设备返回 True，否则返回 False。|
|file.next()|返回文件下一行。|
|file.read([size])|从文件读取指定的字节数，如果未给定或为负则读取所有。|
|file.readline([size])|读取整行，包括 "\n" 字符。|
|file.readlines([sizehint])|读取所有行并返回列表，若给定sizeint>0，返回总和大约为sizeint字节的行, 实际读取值可能比sizeint较大, 因为需要填充缓冲区。|
|file.seek(offset[, whence])|设置文件当前位置|
|file.tell()|返回文件当前位置。|
|file.truncate([size])|截取文件，截取的字节通过size指定，默认为当前文件位置。|
|file.write(str)|将字符串写入文件，返回的是写入的字符长度。|
|file.writelines(sequence)|向文件写入一个序列字符串列表，如果需要换行则要自己加入每行的换行符。|

## Python3 OS 文件/目录方法

| 方法 | 描述 |
| :------: | :------: |
| os.access(path, mode) | 检验权限模式 |
| os.chdir(path) | 改变当前工作目录 |
| os.chflags(path, flags) | 设置路径的标记为数字标记。 |
| os.chmod(path, mode) | 更改权限 |
| os.chown(path, uid, gid) | 更改文件所有者 |
| os.chroot(path) | 改变当前进程的根目录 |
| os.close(fd) | 关闭文件描述符 fd |
| os.closerange(fd_low, fd_high) | 关闭所有文件描述符，从 fd_low (包含）到 fd_high (不包含）错误会忽略 |
| os.dup(fd) | 复制文件描述符 fd |
| os.dup2(fd, fd2) | 将一个文件描述符 fd 复制到另一个 fd2 |
| os.fchdir(fd) | 通过文件描述符改变当前工作目录 |
| os.fchmod(fd, mode) | 改变一个文件的访问权限，该文件由参数fd指定，参数mode是Unix下的文件访问权限。 |
| os.fchown(fd, uid, gid) | 修改一个文件的所有权，这个函数修改一个文件的用户ID和用户组ID，该文件由文件描述符fd指定。 |
| os.fdatasync(fd) | 强制将文件写入磁盘，该文件由文件描述符fd指定，但是不强制更新文件的状态信息。 |
| os.fdopen(fd[, mode[, bufsize]]) | 通过文件描述符 fd 创建一个文件对象，并返回这个文件对象 |
| os.fpathconf(fd, name) | 返回一个打开的文件的系统配置信息。name为检索的系统配置的值，它也许是一个定义系统值的字符串，这些名字在很多标准中指定（POSIX.1, Unix 95, Unix 98, 和其它）。 |
| os.fstat(fd) | 返回文件描述符fd的状态，像stat( |
| os.fstatvfs(fd) | 返回包含文件描述符fd的文件的文件系统的信息，像 statvfs( |
| os.fsync(fd) | 强制将文件描述符为fd的文件写入硬盘。 |
| os.ftruncate(fd, length) | 裁剪文件描述符fd对应的文件, 所以它最大不能超过文件大小。 |
| os.getcwd() | 返回当前工作目录 |
| os.getcwdu() | 返回一个当前工作目录的Unicode对象 |
| os.isatty(fd) | 如果文件描述符fd是打开的，同时与tty(-like)设备相连，则返回true, 否则False。 |
| os.lchflags(path, flags) | 设置路径的标记为数字标记，类似 chflags()，但是没有软链接 |
| os.lchmod(path, mode) | 修改连接文件权限 |
| os.lchown(path, uid, gid) | 更改文件所有者，类似 chown，但是不追踪链接。 |
| os.link(src, dst) | 创建硬链接，名为参数 dst，指向参数 src |
| os.listdir(path) | 返回path指定的文件夹包含的文件或文件夹的名字的列表。 |
| os.lseek(fd, pos, how) | 设置文件描述符 fd当前位置为pos, how方式修改: SEEK_SET 或者 0 设置从文件开始的计算的pos; SEEK_CUR或者 1 则从当前位置计算; os.SEEK_END或者2则从文件尾部开始. 在unix，Windows中有效 |
| os.lstat(path) | 像stat() |
| os.major(device) | 从原始的设备号中提取设备major号码,使用stat中的st_dev或者st_rdev field),但是没有软链接 |
| os.makedev(major, minor) | 以major和minor设备号组成一个原始设备号 |
| os.makedirs(path[, mode]) | 递归文件夹创建函数。像mkdir(), 但创建的所有intermediate-level文件夹需要包含子文件夹。 |
| os.minor(device) | 从原始的设备号中提取设备minor号码,使用stat中的st_dev或者st_rdev field |
| os.mkdir(path[, mode]) | 以数字mode的mode创建一个名为path的文件夹.默认的 mode 是 0777 (八进制) |
| os.mkfifo(path[, mode]) | 创建命名管道，mode 为数字，默认为 0666 (八进制 |
| os.mknod(filename[, mode=0600, device]) | 创建一个名为filename文件系统节点（文件，设备特别文件或者命名pipe）。 |
| os.open(file, flags[, mode]) | 打开一个文件，并且设置需要的打开选项，mode参数是可选的 |
| os.openpty() | 打开一个新的伪终端对。返回 pty 和 tty的文件描述符。 |
| os.pathconf(path, name) | 返回相关文件的系统配置信息。 |
| os.pipe() | 创建一个管道. 返回一对文件描述符(r, w)分别为读和写 |
| os.popen(command[, mode[, bufsize]]) | 从一个 command 打开一个管道 |
| os.read(fd, n) | 从文件描述符 fd 中读取最多 n 个字节，返回包含读取字节的字符串，文件描述符 fd对应文件已达到结尾, 返回一个空字符串。 |
| os.readlink(path) | 返回软链接所指向的文件 |
| os.remove(path) | 删除路径为path的文件。如果path 是一个文件夹，将抛出OSError; 查看下面的rmdir()删除一个 directory。 |
| os.removedirs(path) | 递归删除目录。 |
| os.rename(src, dst) | 重命名文件或目录，从 src 到 dst |
| os.renames(old, new) | 递归地对目录进行更名，也可以对文件进行更名。 |
| os.rmdir(path) | 删除path指定的空目录，如果目录非空，则抛出一个OSError异常。 |
| os.stat(path) | 获取path指定的路径的信息，功能等同于C API中的stat()系统调用。 |
| os.stat_float_times([newvalue]) | 决定stat_result是否以float对象显示时间戳 |
| os.statvfs(path) | 获取指定路径的文件系统统计信息 |
| os.symlink(src, dst) | 创建一个软链接 |
| os.tcgetpgrp(fd) | 返回与终端fd（一个由os.open()返回的打开的文件描述符）关联的进程组 |
| os.tcsetpgrp(fd, pg) | 设置与终端fd（一个由os.open()返回的打开的文件描述符）关联的进程组为pg。 |
| os.tempnam([dir[, prefix]]) | Python3 中已删除。返回唯一的路径名用于创建临时文件。 |
| os.tmpfile() | Python3 中已删除。返回一个打开的模式为(w+b)的文件对象 .这文件对象没有文件夹入口，没有文件描述符，将会自动删除。 |
| os.tmpnam() | Python3 中已删除。为创建一个临时文件返回一个唯一的路径 |
| os.ttyname(fd) | 返回一个字符串，它表示与文件描述符fd 关联的终端设备。如果fd 没有与终端设备关联，则引发一个异常。 |
| os.unlink(path) | 删除文件路径 |
| os.utime(path, times) | 返回指定的path文件的访问和修改的时间。 |
| os.walk(top[, topdown=True[, onerror=None[, followlinks=False]]]) | 输出在文件夹中的文件名通过在树中游走，向上或者向下。 |
| os.write(fd, str) | 写入字符串到文件描述符 fd中. 返回实际写入的字符串长度 |
| os.path 模块) | 获取文件的属性信息。 |
