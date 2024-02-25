---
title: python3手册
description: python3
icon: material/language-python
---
# python3手册

## Python相关说明

```markdown

## 模块（modules）：

模块是Python代码的基本组织单元。

模块唯一的特殊操作是属性访问: m.name，这里m为一个模块而name访问定义在m的符号表中的一个名称。模块属性可以被赋值。
（注意：import 语句严格来说也是对模块对象的一种操作；import foo 不要求存在一个名为 foo 的模块对象，
而是要求存在一个对于名为foo的模块的(永久性) 定义）

每个模块都有一个特殊属性 __dict__。 这是包含模块的符号表的字典。 修改此字典将实际改变模块的符号表，
但是无法直接对 __dict__ 赋值 (你可以写 m.__dict__['a'] = 1，这会将 m.a 定义为 1，
但是不能写 m.__dict__ = {})。也不建议直接修改__dict__

以下划线开头的标识符是有特殊意义的
    • 以单下划线开头（_foo）的代表不能直接访问的类属性，需通过类提供的接口进行访问
    • 以双下划线开头的（__foo）代表类的私有成员
    • 以双下划线开头和结尾的（__foo__）代表python里特殊方法专用的标识，如__init__（）代表类的构造函数

## 标准模块

Python附带一个标准模块库，变量sys.ps1 和sys.ps2 定义用作主要和辅助提示的字符串；这两个变量只有在编译器交互模式下才被定义

sys.path变量是一个字符串列表，用于确定解释器的模块搜索，可以使用标准列表操作对其进行修改

如何理解__name__=="__main__"：
python每个模块都有一个名为__name__的特殊属性，当模块作为主程序运行时，__name__属性的值即为'__main__'，
否则__name__的值将设置为包含模块的名称

### 示例:创建module.py文件如下
foo = 100

def hello():
    print("i am from my_module.py")

if __name__ == "__main__":
    print("Executing as main program")
    print("Value of __name__ is: ", __name__)
    hello()

### 紧随其上，创建test.py文件如下，module.__name__是module而不是__main__，此处未输出if.....__main__的内容
import module

print(module.foo)
module.hello()

print(module.__name__)

## import语句

出于效率的考虑，每个模块在每个解释器会话中只被导入一次。因此，如果你更改了你的模块，则必须重新启动解释器， 或者，如果它只
是一个要交互式地测试的模块，请使用 importlib.reload()，例如 import importlib; importlib.reload(modulename)
模块搜索路径：解释器首先寻找具有该导入名称的内置模块，若没有则会从sys.path变量所给出的目录列表寻找，支持符号链接的文件系
统上，包含输入脚本的目录是在追加符号链接后才计算出来的。换句话说，包含符号链接的目录并没有被添加到模块的搜索路径上

sys.path的初始目录地址：

* 包含输入脚本的目录(或者未指定文件时的当期目录)
* PYTHONPATH(一个包含目录名称的列表，其和shell变量PATH具有相同的语法)
* 取决于Python安装的默认设置

了解了搜索路径的概念，就可以在脚本中修改sys.path来引入一些不在搜索路径中的模块
**__name__属性** 一个模块被另一个程序第一次引入时，其主程序将运行。如果我们想在模块被引入时，模块中的某一程序块不执行
我们可以用__name__属性来使该程序块仅在该模块自身运行时执行

if __name__ == '__main__':
 print('程序自身在运行')
else:
 print('我来自另一模块')

每个模块都有一个__name__属性，当其值是'__main__'时，表明该模块自身在运行，否则是被引入

可以在Python命令中使用-O或-OO开关,以减小编译后模块的大小。-O开关去除断言语句，-OO开关同时去除断言语句和__doc__字符串
一个从.pyc文件读出的程序并不会比它从.py读出时运行的更快。区别在于.pyc文件载入速度更快
compileall模块可以为一个目录下的所有模块创建.pyc文件

## 类：

类定义就是对类对象的定义(参考[标准类型层级结构]https://docs.python.org/zh-cn/3/reference/datamodel.html#types)

类定义是一条可执行语句。 其中继承列表通常给出基类的列表
(进阶用法请参见[元类]https://docs.python.org/zh-cn/3/reference/datamodel.html#metaclasses)，
列表中的每一项都应当被求值为一个允许子类的类对象。没有继承列表的类默认继承自基类 object

随后类体将在一个新的执行帧 (参见 命名与绑定) 中被执行，使用新创建的局部命名空间和原有的全局命名空间。（通常，类体主要包含函数定义）
当类体结束执行时，其执行帧将被丢弃而其局部命名空间会被保存。 3 一个类对象随后会被创建，其基类使用给定的继承列表，
属性字典使用保存的局部命名空间。 类名称将在原有的全局命名空间中绑定到该类对象

类的创建可使用元类进行重度定制。类也可以被装饰：就像装饰函数一样

注意事项: 在类定义内定义的变量是类属性；它们将被类实例所共享。 实例属性可通过 self.name = value 在方法中设定。
类和实例属性均可通过 "self.name" 表示法来访问，当通过此方式访问时实例属性会隐藏同名的类属性。
类属性可被用作实例属性的默认值，但在此场景下使用可变值可能导致未预期的结果。 可以使用 描述器 来创建具有不同实现细节的实例变量

类的专有方法：
__init__ : 构造函数，在生成对象时调用
__del__ : 析构函数，释放对象时使用
__repr__ : 打印，转换
__setitem__ : 按照索引赋值
__getitem__: 按照索引获取值
__len__: 获得长度
__cmp__: 比较运算
__call__: 函数调用
__add__: 加运算
__sub__: 减运算
__mul__: 乘运算
__div__: 除运算
__mod__: 求余运算
__pow__: 乘方

类提供了一种组合数据和功能的方法。创建一个新类意味着创建一个新类型的对象，从而允许创建一个该类型的新实例。每个类的实例可以拥有保存
自己状态的属性。一个类的实例也可以有改变自己状态的（定义在类中的）方法。Python的类提供了面向对象编程的所有标准特性：类继承机制
允许多个基类，派生类可以覆盖它基类的任何方法，一个方法可以调用基类中相同名称的的方法。对象可以包含任
意数量和类型的数据。和模块一样，类也拥有Python的动态特性：它们在运行时创建，可以在创建后修改

类：
    定义：
    class 类名()：
        def __init__(self,参数……):
        def 函数名1(self,参数……):
        def 函数名2(self,参数……):
    调用：
        实例名 = 类名()
        实例名.方法名(参数)

## 对象

对象是Python对数据的抽象。Python程序中的所有数据都由对象或对象之间的关系表示

每个对象都有一个编号，类型和值。一旦创建了对象，其编号就不会改变，可以理解为该对象在内存中的地址，type()函数返回对象的类型（对象本身）。
类似编号一样，对象的类型也是不可更改的，某些对象的值是可以改变的

注意：使用实现的跟踪或调试功能可能令正常情况下会被回收的对象继续存活。还要注意通过 'try...except' 语句捕捉异常也可能令对象保持存活

实例方法用于结合类、类实例和任何可调用对象(通常为用户自定义函数)。特殊的只读属性: __self__ 为类实例对象本身，__func__ 为函数对象；
__doc__ 为方法的文档 (与 __func__.__doc__ 作用相同)；__name__ 为方法名称 (与 __func__.__name__ 作用相同)；
__module__ 为方法所属模块的名称，没有则为 None

当一个实例方法对象被调用时，会调用对应的下层函数 (__func__)，并将类实例 (__self__) 插入参数列表的开头。
例如，当 C 是一个包含了 f() 函数定义的类，而 x 是 C 的一个实例，则调用 x.f(1) 就等同于调用 C.f(x, 1)

## 函数

函数对象是通过函数定义创建的。 对函数对象的唯一操作是调用它: func(argument-list)

实际上存在两种不同的函数对象：内置函数和用户自定义函数。 两者支持同样的操作（调用函数），但实现方式不同，因此对象类型也不同

函数：
   定义：
   def 函数名（参数1，参数2……）：
       语句块
       return 返回值
   调用：
       函数名（参数1，参数2……）
       变量名 = 函数名（参数1，参数2……）

## 方法

方法是使用属性表示法来调用的函数。存在两种形式：内置方法（例如列表的append()方法）和类实例方法。内置方法由支持它们的类型来描述

## 包(packages)

可以把包看成是文件系统中的目录，并把模块看成是目录中的文件，但不要对这个类似做过于字面的理解，因为包和模块不是必须来自于文件系统

要注意的一个重点概念是所有包都是模块，但并非所有模块都是包。或者换句话说，包只是一种特殊的模块。
特别地，任何具有 __path__ 属性的模块都会被当作是包

* 要使用格式化字符串字面值，在字符串开始引号或三引号之前加上一个f或F，在此字符串中，可以在{}字符之间引用变量或字面值的Python表达式
* 字符串的str.format()方法
* 使用字符串切片和连接操作自行完成对所有字符串的处理

### 导入模块

import math # 导入math模块

from math import ceil, floor    # 从一个模块导入特定的函数ceil,floor

import math as m    ### 导入并简化一个模块的名称

## Python开发环境最佳实践

```bash
# 设置pip配置文件
: << comment
Linux：$HOME/.pip/pip.conf
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

python -m SimpleHTTPServer | Serve current directory tree at http://$HOSTNAME:8000/     # 利用python简单建立webserver

# yum -y install epel-release
# yum -y install python2-pip
# yum -y install python3-pip
python -m pip install -U pip setuptools

# python -m site --user-base 找到 用户基础目录,将基础目录添加到PATH
# 在 Windows 上，您通过运行 py -m site --user-site 找到用户基础目录，将 site-packages 替换为 Scripts
# 自定义pipenv虚拟环境创建目录，windows通过设置变量WORKON_HOME，其值设置为PIPENV_VENV_IN_PROJECT时表示当项目目录中创建
# linux在.bashrc文件添加export PIPENV_VENV_IN_PROJECT=1或export PIPENV_VENV_IN_PROJECT="enabled"
pip install --user pipenv

# 在Windows安装virtualenv和virtualenvwrapper-win用于创建多个Python版本开发环境，Linux中最好使用pyenv代替
# 安装pipenv和autoenv，自动激活环境
# 在Linux上:
git clone git://github.com/kennethreitz/autoenv.git ~/.autoenv
echo 'source ~/.autoenv/activate.sh' >> ~/.bashrc

# pipes：Pipenv虚拟环境切换工具
# MacOs + Ubuntu:
pip3 install pipenv-pipes --user

# Windows:
pip3 install pipenv-pipes
pip3 install curses --find-links=https://github.com/gtalarico/curses-win/releases

```

## 简介

```markdown
#!/usr/bin/python3
# -*- coding: UTF-8 -*-
# Python中单行注释以#开头，多行注释用三个单引号（```）或者三个双引号（"""）将注释括起来
# 特殊字符比如\n在单双引号有同样的意义，不同之处是在单引号中无需转义双引号，也就是表示其实际意义。反之亦然

help(len)           # 内建len()函数帮助
help(sys)           # sys模块帮助,必须先import sys
help(sys.exit)      # exit()函数在sys模块的帮助
help('xyz'.split)   # 对于string对象splist()方法的帮助，和help(str.split)一样
help(list)          # list对象帮助
help(list.append)   # list对象append()方法帮助
dir(list)           # 显示list对象属性，包括其方法
dir(sys)            # 类似help()

python中的数据类型有四种：整数int、长整数、浮点数float、复数complex
python中变量无需声明，每个变量在使用前必须赋值，变量赋值后该变量才会被创建。变量没有类型，而通常所说的“类型”指内存中对象的类型

python3中常用的内置数据类型：
* Boolean (布尔值)
* Numbers (数字)
* String (字符串)
* Bytes (字节)
* List (列表)
  * 示例：list1 = ['Google', 'aws', 1997, 2000]
  * 空列表:list2 = []
* Tuple (元组)
  * 示例：tuple1 = ('Google', 'aws', 1997, 2000)
  * 空元组:tuple2 = ()  ======>>等价于tuple1 = tuple()
* Sets (集合)
  * 示例：set1 = {'apple', 234, 'apple', 'banana'}
  * 空集合：set2 = set()
* Dictionaries (字典)
  * 示例：dict1 = {'Alice': '21', 'Beth': '12', 'Cecil': '19'}
  * 空字典:dict1 = {}

python可变数据类型：list、dict
python不可变数据类型：int、str、tuple

```

## 内置函数列表 -- 非全面性

### abs(x)

返回数字的绝对值，参数x必须是整数或浮点数，如果参数为复数，则返回其大小

### all(iterable)

如果所有元素的迭代都是true(或iterable为空)，则返回True，相当于：

```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True

```

### any(iterable)

如果iterable的任何元素为true，即有一个为真时，则返回True，否则False，相当于：

```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False

```

### bin(x)

```python
# 将整数转换为前缀带有'0b'的二进制字符串，结果为有效的python表达式
# 去除前缀ob的方法有两种
print(format(14, '#b'), format(14, 'b'))   # 方法一

print(f'{14:#b}', f'{14:b}')    # 方法二

```

### chr(i)

返回一个字符的unicode代码点的整数，例：chr(97)返回字符串'a'，与其相反是ord()

### enumerate(iterable, start=0)

返回一个枚举对象，iterable必须为序列，迭代器或其他支持迭代的对象，__next__()是由enumerate()返回的迭代器的方法返回一个元组，
该元组包括一个计数(start为开始，默认为0)以及通过对iterable进行迭代获得的值

### eval(expression[, globals[, locals]])

```markdown
eval()能够执行任意的字符串作为python代码，其接受源字符串并返回一个对象，expression可以是任何有效的python表达式
globals：执行源时要使用的全局名称空间。它必须是字典。如果未提供，则将使用当前的全局名称空间
locals：执行源时要使用的本地名称空间。它可以是任何映射对象。如果省略，则默认为全局字典

防止不受信任的源直接传递给eval()，导致对系统造成破坏，那么使eval()安全的参考方法如下
指定名称空间：eval()可选的接受两个映射充当要被执行用于表达的全局和局部的命名空间，如果未提供映射，则使用全局和局部名称空间的当前值
print('abs(-10)', {'__builtins__':None}, {})    # 删除对内置函数的访问
以上方法仍然不够安全，如果传递的expression本身就具有强打的攻击性，也会出现安全问题，所以关键点就是仅对受信任的源使用eval()

```

### filter(function, iterable)

当function返回true时，为这些iterable的元素构造一个迭代器，iterable可以为序列、支持迭代的容器和迭代器，
如果function是None，则假定为身份函数，即删除所有false的iterable的元素

### globals()

返回表示当前全局符号表的字典，始终是当前模块的字典(在函数或方法内部，这是定义它的模块，而不是从中调用它的模块)

### id(object)

返回object的”身份“，这是个整数，用以保证在此对象的生存期内唯一且恒定，但具有不重叠生命周期的两个对象可能具有相同的id()值

### len(s)

返回对象的长度(项目数),参数可以是序列或集合

### locals()

更新并返回一个包含在本地名称空间中定义的变量的字典，locals()在功能块中调用自由变量会返回自由变量，而在类块中则不会，
在模块级别(全局名称空间)locals()和globals()是相同的字典

注意：该字典的内容不得修改；更改可能不会影响解释器使用的局部变量和自由变量的值

### map(function, iterable, ...)

返回一个迭代器，该迭代器将函数应用于iterable的每个项目并返回结果，如果传递了其他可迭代参数则函数必须采用那么多参数，并且并行地将
其应用于所有可迭代对象的项

### max()

返回可迭代的最大项目或两个或多个参数中最大的项目，如果提供了一个位置参数，则应该是可迭代的，返回iterable中最大的项目，如果提供了两个
或多个位置参数，则返回嘴的位置参数。多个最大项时，返回第一个

### min()

返回可迭代的最小项或两个或多个参数中的最小项

### ord(c)

给定一个表示unicode字符的字符串返回一个整数表示该字符的unicode代码点，对于ASCII字符，返回值是7位ASCII代码,与其相反的函数为chr()

### range(stop) -- range(start, stop[,step])

range实际上是一个不可变的序列类型，而不是一个函数
xrange()返回一个迭代器而不是列表中的列表，在内存中占用更小

### reversed(seq)

返回一个反向迭代器，seq必须具有__reversed__()方法或支持序列协议的对象

### sorted(iterable, *, key=None, reverse=False)

从iterable中的项目返回一个新的排序列表，有两个可选参数，必须将其指定为关键字参数，key指定一个参数的功能，该参数用于从iterable中
的每个元素中提取一个比较键(例如：key=str.lower)，默认为None直接比较元素，reverse是一个布尔值，True表示对列表进行反转排序

### sum(iterable, /, start=0)

从左到右开始求和迭代的项目然后返回总数，该迭代的项目通常为整数，起始值不允许为字符串。
连接字符串序列的首选快速方法是调用''.join(sequence)，要串联一系列可迭代对象，推荐使用itertools.chain()

### zip(*iterables)

创建一个迭代器以聚合每个可迭代对象中的元素

返回元组的一个迭代器，其中第i个元组包含每个参数序列或可迭代对象中的第i个元素。当最短的可迭代输入耗尽时，迭代器将停止。
使用单个可迭代参数，它将返回第一个元组的迭代器。没有参数，它将返回一个空的迭代器。相当于以下示例：

```python
def zip(*iterables):
    # zip('ABCD', 'xy') --> Ax By
    sentinel = object()
    iterators = [iter(it) for it in iterables]
    while iterators:
        result = []
        for it in iterators:
            elem = next(it, sentinel)
            if elem is sentinel:
                return
            result.append(elem)
        yield tuple(result)

```

## 内置常量

少数的常量存在于内置命名空间中：

## 内置类型

主要内置类型有数字、序列、映射、类、实例和异常，实际上所有对象都可以比较是否相等、检测逻辑值，以及转换为字符串(使用repr()函数或略有差异的str()函数)
后一个函数是在对象由 print() 函数输出时被隐式地调用的

### 逻辑值检测

任何对象都可以进行逻辑值检测，以便于在if或while中作为条件或作为下文布尔值运算的操作数来使用

一个对象默认情况下均被视为真值，除非该对象被调用时其所属类定义了__bool__()方法且返回False或者定义了__len__()方法且返回0
以下基本列出会被视为假值的内置对象：

下面基本完整罗列出会被视为假值的内置对象：

* 被定义为假值的常量: None 和 False
* 任何数值类型的零: 0, 0.0, 0j, Decimal(0), Fraction(0, 1)
* 空的序列和多项集: '', (), [], {}, set(), range(0)

### 布尔类型运算 -- and、or、not

| 运算 | 结果 | 注释 |
| :------: | :------: | :------: |
| x or y | if x is false, then y, else x | 这是个短路运算符，因此只有在第一个参数为真值时才会对第二个参数求值 |
| x and y | if x is false, then x, else y | not 的优先级比非布尔运算符低，因此 not a == b 会被解读为 not (a == b) 而<br> a == not b 会引发语法错误 |
| not x | if x is false, then True, else False | not 的优先级比非布尔运算符低，因此 not a == b 会被解读为 not (a == b)<br> 而 a == not b 会引发语法错误 |

### 比较运算

八种比较运算符，优先级相同(比布尔运算优先级高)。比较运算可以任意串连。is和is not运算符无法自定义并且可以被应用于任意两个
对象而不会引发异常；还有两种具有相同语法优先级的运算in和not in，它们被iterable或实现了__contains__()方法的类型所支持

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

### 数字类型运算 -- int、float、complex

Python有三种不同的数字类型：整数、浮点数和复数。(python2中才有long类型)布尔值属于整数的子类型。整数有无限精度，浮点数通常以C语言中
double实现复数具有实部和虚部，每个都是浮点数

数字是由数字字面值或内置函数与运算符的结果来创建。未修饰的整数字面值(包括十六进制、八进制和二进制数)会生成整数，包含小数点或幂运算符的
数字字面值会生成浮点数，在数字字面值末尾加上 'j' 或 'J' 会生成虚数(实部为零的复数)将其与整数或浮点数相加来得到具有实部和虚部的复数

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
| c.conjugate() | 复数c的结合(共轭) |  |
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

按位运算只对整数有意义，按位运算符是把数字看作二进制来进行计算的。二进制按位运算优先级全都低于数字运算，但高于比较运算
一元运算`~`和其他一元算术运算`+`和`-`具有相同的优先级

以优先级升序排序的按位运算列表：

| 运算符 | 结果 | 注释 |
| :------: | :------: | :------: |
| \| | 按位或运算符：只要对应的二个二进位有一个为1时，结果位就为1 | 使用带有至少一个额外符号扩展位的有限个二进制补码表示（有效位宽度<br>为 1 + max(x.bit_length(), y.bit_length()) 或以上）执行这些计算就足以获得相当于有无数个符号位时的同样结果 |
| ^ | 按位异或运算符：当两对应的二进位相异时，结果为1 | 使用带有至少一个额外符号扩展位的有限个二进制补码表示（有效位宽度为<br> 1 + max(x.bit_length(), y.bit_length()) 或以上）执行这些计算就足以获得相当于有无数个符号位时的同样结果 |
| & | 按位与运算符：参与运算的两个值,如果两个相应位都为1,则该位的结果为1,否则为0 | 使用带有至少一个额外符号扩展位的有限个二进制补码<br>表示（有效位宽度为 1 + max(x.bit_length(), y.bit_length()) 或以上）执行这些计算就足以获得相当于有无数个符号位时的同样结果 |
| x << n | x的各二进位全部左移n位，高位丢弃，低位补0 | 负的移位数是非法的会导致引发ValueError,左移n位等价于不带溢出检测乘以pow(2, n) |
| x >> n | x的各二进位全部右移n位 | 负的移位数是非法的，会导致引发 ValueError；右移 n 位等价于不带溢出检测地除以 pow(2, n) |
| ~x | x 逐位取反 |  |

整数int类型的附加方法：

int.bit_length()    返回以二进制表示一个整数所需的位数，不包括符号位和前面的0
int.to_bytes()      返回表示一个整数的字节数组，整数会使用length个字节来表示。 如果整数不能用给定的字节数来表示则会引发 OverflowError
int.from_tytes()    返回由给定字节数组表达的整数
int.as_integer_ratio() 返回一对整数，其比率正好等于原整数且分母为正数，整数的比率总是用这个整数本身作为分子，1作为分母

浮点类型的附加方法：

float.as_integer_ratio()    返回一对整数，其比率正好等于原浮点数且分母为正数,无穷大会引发OverflowError而NaN会引发ValueError
float.is_integer()  若float实例可用有限位整数表示则返回True，否则返回False
float.hex() 以十六进制字符串形式表示一个浮点数，此为实例方法
float.fromhex() 返回以十六进制字符串表示的浮点数，此为类方法

### 迭代器类型

Python支持迭代容器的概念。这是使用两种不同的方法实现的; 这些用于允许用户定义的类支持迭代

#### 生成器类型

Python的生成器提供了一种实现迭代器协议的便捷方式。如果容器对象的__iter__()方法被实现为生成器，它将自动返回提供__iter__()
和__next__()方法的迭代器对象（技术上，生成器对象）

```python
def my_range(start, stop, step = 1):
    if stop <= start:
        raise RuntimeError("start must be smaller than stop")
    i = start
    while i < stop:
        yield i
        i += step

try:
    for k in my_range(10, 50, 3):
        print(k)
except RuntimeError as ex:
    print(ex)
except TypeError:
    print("Unknown error occurred")

```

### 序列类型 -- list、tuple、range

通用序列类型操作：大多数序列类型包括可变类型和不可变类型都支持以下的操作，collections.abc.Sequence ABC 用来更容易的在自定
义序列类型正确实现这些操作。此表按优先级升序罗列序列操作，表格中，s 和 t 是具有相同类型的序列，n, i, j 和 k 是整数而 x 是
任何满足 s 所规定的类型和值限制的任意对象

任何的序列(或者可迭代对象)都可通过一个赋值语句解压并赋值给多个变量，唯一的前提就是变量的数量必须与序列元素数相同

相同类型的序列也支持比较。 特别地，tuple 和 list 的比较是通过比较对应元素的字典顺序。 这意味着想要比较结果相等，则每个元
素比较结果都必须相等，并且两个序列长度必须相同

大多数序列类型，包括可变类型和不可变类型都支持下表中的操作

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
| s.index(x[, i[, j]]) | x在s中首次出现项的索引号(索引和在i或其后且在j之前) | 8 |
| s.count(x) | x 在 s 中出现的总次数 |  |

注释:

1. 虽然 in 和 not in 操作在通常情况下仅被用于简单的成员检测，某些专门化序列 (例如 str, bytes 和 bytearray) 也使用它们进行子序列检测
2. 小于 0 的 n 值会被当作 0 来处理 (生成一个与 s 同类型的空序列)。 请注意序列 s 中的项并不会被拷贝；它们会被多次引用
3. 如果 i 或 j 为负值，则索引顺序是相对于序列 s 的末尾: 索引号会被替换为 len(s) + i 或 len(s) + j。 但要注意 -0 仍然为 0
4. s 从 i 到 j 的切片被定义为所有满足 i <= k < j 的索引号 k 的项组成的序列。 如果 i 或 j 大于 len(s)，则使用 len(s)。 如果 i 被省略
   或为 None，则使用 0。 如果 j 被省略或为 None，则使用 len(s)。 如果 i 大于等于 j，则切片为空
5. s 从 i 到 j 步长为 k 的切片被定义为所有满足 0 <= n < (j-i)/k 的索引号 x = i + n\*k 的项组成的序列。 换句话说，索引号为 i, i+k,
   i+2\*k, i+3*k，以此类推，当达到 j 时停止 (但一定不包括 j)。 当 k 为正值时，i 和 j 会被减至不大于 len(s)。 当 k 为负值时，i 和 j
   会被减至不大于 len(s) - 1。 如果 i 或 j 被省略或为 None，它们会成为“终止”值 (是哪一端的终止值则取决于 k 的符号)。 请注意，k 不可
   为零。 如果 k 为 None，则当作 1 处理
6. 拼接不可变序列总是会生成新的对象。 这意味着通过重复拼接来构建序列的运行时开销将会基于序列总长度的乘方。 想要获得线性的运行时开销，必须
   改用下列替代方案之一：
    * 如果拼接 str 对象，你可以构建一个列表并在最后使用 str.join() 或是写入一个 io.StringIO 实例并在结束时获取它的值
    * 如果拼接 bytes 对象，你可以类似地使用 bytes.join() 或 io.BytesIO，或者你也可以使用 bytearray 对象进行原地拼接。 bytearray
      对象是可变的，并且具有高效的重分配机制
    * 如果拼接 tuple 对象，请改为扩展 list 类
    * 对于其它类型，请查看相应的文档
7. 某些序列类型 (例如 range) 仅支持遵循特定模式的项序列，因此并不支持序列拼接或重复
8. 当 x 在 s 中找不到时 index 会引发 ValueError。 不是所有实现都支持传入额外参数 i 和 j。 这两个参数允许高效地搜索序列的子序列
   传入这两个额外参数大致相当于使用 s[i:j].index(x)，但是不会复制任何数据，并且返回的索引是相对于序列的开头而非切片的开头

#### 不可变序列类型

不可变序列类型通常实现的唯一操作是可变序列类型未实现对hash()内置函数的支持，例如：tuple实例被用作dict键以及存储在set和frozenset实例中
尝试对包含有不可哈希值的不可变序列进行哈希运算将会导致 TypeError

#### 可变序列类型

以下操作是在可变序列类型定义，collections.abc.MutableSequence ABC被提供用来更容易在自定义序列类型上正确实现这些操作

表格中的 s 是可变序列类型的实例，t 是任意可迭代对象，而 x 是符合对 s 所规定类型与值限制的任何对象 (例如，bytearray 仅接受满足
0 <= x <= 255 值限制的整数)

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
| s *= n | 使用 s 的内容重复 n 次来对其进行更新 | n值为一个整数或一个实现了__index__()的对象，n值为0或负数将清空序列。序列中的项不能<br>被拷贝，会被多次引用 |
| s.insert(i, x) | 在由 i 给出的索引位置将 x 插入 s (等同于 s[i:i] = [x]) |  |
| s.pop([i]) | 提取在 i 位置上的项，并将其从 s 中移除 | 可选参数默认为-1，因此默认情况会移除并返回最后一项 |
| s.remove(x) | 删除 s 中第一个 s[i] 等于 x 的项目。 | 当s找不到x时，remove操作会引发ValueError |
| s.reverse() | 将列表中的元素逆序 | 当反转比较大的序列时，reverse()方法会原地修改此序列来保证空间经济性，为提醒用户此操作是通过间接<br>影响进行的，它并不会返回反转后的序列 |

#### Python运算符优先级

以下表格列出了从最高到最低优先级的常用运算符,同一框中运算符具有相同优先级，除非明确给出语法，否则运算符为二进制：负几次方其实就是它的正几次方分之一

| 运算符 | 描述 |
| :------: | :------: |
| ** | 指数 (最高优先级) |
| + - ~ | 正、负、按位非 (最后两个的方法名为 +@ 和 -@) |
| * @ / // % | 乘法、矩阵乘法、除法、底数除法(取整除)、余数(取模)；%运算符还用于字符串格式化，适用于相同的优先级 |
| + - | 加法、减法 |
| << >> | 左移,右移位运算符 |
| & | 按位与 |
| ^ | 按位异或 |
| \| | 按位或 |
| in, not in, is, is not, <, <=, >, >=, !=, == | 比较,包括成员资格测试和身份测试 |
| not x | NOT 布尔类型 x |
| and | 布尔类型 AND |
| or | 布尔类型 OR |
| if - else | if条件表达式 |
| lambda | Lambda表达式 |
| := | Assignment expression |

#### List(列表)

列表list是Python中使用最频繁的数据类型;列表是写在`[]`方括号之间，用逗号隔开的元素列表，列表中元素的类型可以不同，和字符串
一样，列表也可以被索引和切片。列表被切片后返回一个包含所需元素的新列表，列表还支持串联操作，使用+符号

```markdown
# 注意：
1、List写在方括号之间，元素用逗号隔开,列表解析可以生成常规列表
2、和字符串一样，list可以被索引和切片
3、List可以使用+操作符进行拼接
4、List中的元素是可以改变的
5、遍历列表时，有时很想更改列表。但是创建新列表通常更简单，更安全

```

##### 列表对象的方法

以下方法是作用在list对象上的。而len()是将list、string或其他类型作为参数的函数，
insert，remove或者sort只修改列表没有返回值，默认返回None，这是Python中所有可变数据结构的设计原则

| 方法 | 描述 |
| :------: | :------: |
| list.append(elem) | (elem表示element)把一个元素添加到列表的结尾，相当于 a[len(a):] = [elem]|
| list.extend(iterable) | 通过附加来自iterable的所有项来扩展列表。等同于 a[len(a):] = iterable |
| list.insert(index, elem) | 在指定位置插入一个元素。第一个参数是准备插入到其前面的那个元素的索引，<br>例如 a.insert(0, x) 会插入到整个列表之前，而 a.insert(len(a), x) 相当于 a.append(x) |
| list.remove(elem) | 搜索给定元素的第一个实例并将其删除,如果不存在则抛出ValueError |
| list.pop([i]) | 从列表的指定位置删除元素，并将其返回。如果没有指定索引，a.pop()返回最后一个元素,<br>并返回所删除的值。方法中i两边的方括号表示这个参数是可选的，而不是要求你输入一对方括号 |
| list.clear() | 移除列表中的所有项，等于del a[:] |
| list.index(elem) | 从列表的开头搜索给定元素并返回其索引。如果没有匹配的元素就会返回一个错误 |
| list.count(x) | 返回 x 在列表中出现的次数 |
| list.sort() | 对列表中的元素进行永久排序,不要直接返回结果,后面使用sorted()函数临时排序显示更好 |
| list.reverse() | 倒序排列列表中的元素 |
| list.copy() | 返回列表的浅复制，等于a[:] |

##### 将列表当做堆栈使用

列表方法使得列表可以很方便的作为一个堆栈来使用，堆栈作为特定的数据结构，最后一个插入，最先取出（后进先出）。
用 append()方法可以把一个元素添加到堆栈顶。用不指定索引的 pop() 方法可以把一个元素从堆栈顶取出来

```python
stack = [3, 4, 5]
stack.append(7)
stack.pop()

```

##### 将列表当作队列使用

也可以把列表当做队列用，其中先添加的元素被最先取出 (“先进先出”)；但是列表用作这样的目的相当低效。在列表的最后添加或者
弹出元素速度快，然而在列表里插入或者从头部弹出速度却很慢（因为所有其他的元素都必须移动一位）;要实现一个队列，collections.
deque被设计用于快速从两端操作

```python
from collections import deque
queue = deque(["bob", "John", "michael"])
queue.append("Tom")
queue.popleft()

```

##### 列表推导式

列表推导式提供了从序列创建列表的简单途径。常见的用法是把某种操作应用于序列或可迭代对象的每个元素上，然后使用其结果来创建
列表，或者通过满足某些特定条件元素来创建子序列

每个列表推导式都在for之后跟一个表达式，然后有零到多个 for 或 if 子句。返回结果是一个根据表达从其后的 for 和 if 上下文环
境中生成出来的列表。如果希望表达式推导出一个元组，就必须使用括号

```python
# 计算正方形，方法一和方法二等效
squares1 = list(map(lambda x: x**2, range(10)))  # 方法一：lambda表达式法
squares2 = [x**2 for x in range(10)]    # 方法二:列表推导式

```

##### 嵌套列表推导式和del语句

列表推导式中的初始表达式可以是任何表达式，包括另一个列表推导式

*del语句*：使用del语句可以从一个列表中依索引而不是值来删除一个元素。这与使用 pop() 返回一个值不同。可以用 del 语句从列表
中移除切片，或清空整个列表（我们以前介绍的方法是空列表赋值给一个指定的切片)，del也可以用来删除整个变量

```python
# 将matrix列表的行和列转置，内置函数方法，返回结果列表中每个元素不变仍为列表
matrix = [[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]]
print([[row[i] for row in matrix] for i in range(4)])

print(list(zip(*matrix)))  # zip()方法，返回结果列表中每个元素为元组tuple

```

##### python高级特性：切片、迭代、推导式、生成器、迭代器

```python

"""
切片在列表上就像字符串一样，可以用来改变列表的子部分
给定一个list或tuple，我们可以通过for循环来遍历这个list或tuple，这种遍历我们称为迭代(Iteration)
迭代是访问集合元素的一种方式，迭代器可以记住遍历位置的对象。迭代器对象从集合的第一个元素开始访问，知道所有元素结束，迭代
器只能往前，迭代器有两个方法iter()和next()。字符串、列表和元组对象都可用于创建迭代器
"""

list1 = [1, 2, 3, 4]  # 创建列表list1
it = iter(list1)  # 创建迭代器对象it
print(next(it))  # 输出迭代器下一个元素

# 迭代器对象可以用for语句进行遍历
list2 = [1, 2, 3, 4]  # 创建列表list2
it = iter(list1)  # 创建迭代器对象it
for x in it:
    print(x, end=" ")


# 列表推导式即List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式
# for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：
print([x * x for x in range(1, 11) if x % 2 == 0])

# 运用列表推导式，可以写出非常简洁的代码。例如，列出当前目录下的所有文件和目录名，可以通过一行代码实现
import os   # 导入os模块
print([d for d in os.listdir('.')]) # 列出当前目录下文件和目录名(包含隐藏文件或目录)

# 使用itertools包中itertools.chain.from_iterable()快速碾平一个内嵌列表
import itertools
list_1 = [[1, 2], [3, 4]]
print(list(itertools.chain(*list_1)))   # 第一种方式
print(list(itertools.chain.from_iterable(list_1)))  # 第二种方式

```

```python

"""
使用了yield的函数称为生成器(generator)，生成器是一个返回迭代器的函数，简单的理解就是生成器就是一个迭代器。在调用生成器
运行的过程中，每次遇到yield函数会暂停并保存当前所有的运行信息，返回yield的值，并在下一次next()方法从当前位置继续运行
"""

# 使用生成器生成斐波那契数列,斐波那契数列：即两个元素的和确定下一个数
import sys
def fibonacci(x):
    a, b, count = 0, 1, 0
    while True:
        if count > x:
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

# 可以直接作用于for循环的数据类型有以下几种：
# 一是集合数据类型，如list、tuple、dict、set、str等
# 二是generator，包括生成器和带yield的generator function
# 这些可以直接作用于for循环的对象统称为可迭代对象：Iterable

# 可以使用isinstance()判断一个对象是否是Iterable对象
# 生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。把list、dict、str等Iterable变成Iterator可以使用iter()函数
```

```python

"""
为什么list、dict、str等数据类型不是Iterator?
这是因为Python的Iterator对象表示的是一个数据流，Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出
StopIteration错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算
下一个数据，所以Iterator的计算是惰性的，只有在需要返回下一个数据时它才会计算
Iterator甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的
"""

# 总结：
# 凡是可作用于for循环的对象都是Iterable类型
# 凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列
# 集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过iter()函数获得一个Iterator对象

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

#### Tuple（元组）

元组（tuple）与列表类似，不同之处在于元组的元素不能修改。元组写在`()`小括号里，元素之间用逗号隔开。元组中的元素类型也可
以不相同，并且通过解包或索引来访问，而列表通过迭代访问

元组是不可变序列，给元祖的单独一个元素赋值是不允许的，空元祖由一对空圆括号创建，含有一个元素的元祖可以通过再此元祖之后添加一个逗号创建
(将一个值括在括号中是不够的)

```markdown
# 元组与字符串类似，可以被索引且下标索引从0开始，也可以进行截取/切片。其实，可以把字符串看作一种特殊的元组。改元组元素的操作是非法的,元组
  也支持用+操作符
# 虽然tuple的元素不可改变，但它可以包含可变的对象，比如list列表。构造包含0个或1个元素的tuple是个特殊的问题，所以有一些额外的语法规则：
tup1 = ()    # 空元组
tup2 = (20,) # 单元组，需要在元素后添加逗号

# string、list和tuple都属于sequence（序列）
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

### 文本序列类型 -- String

Python中的文本数据由str对象或字符串处理，字符串是Unicode的不可变序列，字符串文字由多种方式编写

#### String(字符串)

```markdown
# “str”为内置字符串类;用单引号或双引号栝起来，同时使用反斜杠\转义特殊字符，若不想让反斜杠产生转义效果，在字符串前添加一
# 个r，表示原始字符串，字符串文字可以跨越多行，但每行末尾必须有一个反斜杠\来转义换行符。三引号内的字符串文字"""或”''可以
#跨越多行的文字；python中的字符串有两种索引方式，第一种是从左往右依次从0开始递增，第二种是从右往左依次从-1开始递减
# 注意：没有单独的字符类型，一个字符就是长度为1的字符串，还可以对字符串进行切片，获取一段字符串，形式为变量[头下标:尾下标]
# 截取范围是前闭后开[2:]，并且两个索引都可以省略；python字符串不能被改变，如向一个索引位置赋值，word[0]='m'会导致错误

# 注意：
1、反斜杠可以用来转义，使用r可以让反斜杠不发生转义
2、字符串可以用+运算符连接在一起，用* 运算符重复
3、Python中的字符串有两种索引方式，从左往右以0开始，从右往左以-1开始
4、Python中的字符串不能改变。因此构造*new*字符串来表示计算值；例如表达式('hello'+'there')构建新的hellothere字符串
```

##### 常见的字符串方法

字符串实现常见的序列操作,以及以下的描述的一些方法

| 方法 | 描述 |
| :------: | :------: |
| str.capitalize() | 返回字符串的副本，其首字符大写，其余字符小写 |
| str.lower(),str.upper() | 返回字符串的副本，并将所有套接字符转换为小写或大写 |
| str.strip([chars]) | 返回删除了开头和结尾字符的字符串副本。 chars参数是一个字符串，<br>指定要删除的字符集。如果省略或None，则chars参数默认为删除空格 |
| str.isalpha()/str.isdigit()/str.isspace()...  | 测试所有字符串字符是否在各种字符类中 |
| str.find() | 检测字符串中是否包含子字符串str，如果指定start和end范围，则检查是否包含在指定范围内，<br>如果包含子字符串返回最低的索引值，否则返回-1 |
| str.format(*args, **kwargs) | 执行字符串格式化操作。调用此方法的字符串可以包含字符串字面值或者以花括号{}括起来的替换域<br>每个替换字段都包含位置参数的数字索引或关键字参数的名称。返回字符串的副本，其中每个替换字段都替换为相应参数的字符串值 |
| str.split(sep=None, maxsplit=-1) | 返回字符串中的单词组成的列表，使用sep作为分割的字符，若给出sep，则连续的分隔字符不会组合在一起，<br>并被视为划分空字符串,如果给出maxsplit，列表最多只有maxsplit + 1个元素,如果maxsplit没有给出或者是-1，则没有分割数量限制
| str.splitlines([keepends]) | 返回字符串中的行作为列表，在行边界处断开。除非给出keepends且为true，<br>否则换行符不包括在结果列表中;此方法在以下边界处进行分割 |

| 表示 | 描述 |
| :------: | :------: |
| \n | 换行 |
| \r | 回车 |
| \r\n | 回车+换行 |
| \v or \x0b | 行列表 |
| \f or \x0c | 换页 |
| \x1c | 文件分隔符 |
| \x1d | 组分隔符 |
| \x1e | 记录分隔符 |
| \x85 | 下一行（C1控制代码） |
| \u2028 | 行分隔符 |
| \u2029 | 段落分隔符 |

```python
print(b'\xe5\x85\x83'.decode('utf-8'))  # 将bytes解码为utf-8字符串，语法更加Pythonic
print(str(b'\xe5\x85\x83', 'utf-8'))    # 将bytes解码为utf-8字符串，可用性范围更广
```

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

字节对象是单个字节不可变序列，由于主要的二进制协议大多基于ASCII文本编码，因此字节对象提供了几种方法，这些方法仅在处理ASCII
兼容数据时有效，并且以各种其他方式与字符串对象密切相关

字节文字的语法和字符串文字的语法类似，只是添加了前缀 b
字节文字中只允许使用ASCII字符（无论声明的源代码编码如何）。必须使用适当的转义序列将超过127的任何二进制值输入到字节文字中
与字符串文字一样，字节文字也可以使用r前缀来禁用转义序列的处理
虽然字节文字和表示基于ASCII文本，但字节对象实际上表现为不可变的整数序列，序列中的每个值都受到限制，以致0 <= x < 256（试图
违反此限制将触发ValueError）。这是故意强调的，虽然许多二进制格式包括基于ASCII的元素，并且可以使用一些面向文本的算法进行有
用的操作，但对于任意二进制数据通常不是这种情况（盲目地将文本处理算法应用于非二进制数据格式） ASCII兼容通常会导致数据损坏）
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

bytes和bytearray对象都支持公共序列操作，它们不仅与相同类型的操作数互操作，而且与任何类似字节的对象互操作；由于此灵活性
它们可以在操作中自由混合而不会导致错误，但结果的返回类型可能取决于操作数的顺序

#### printf风格字节格式化

字节对象(bytes/bytearray)有一个独特的内置操作：%运算符(模数)也成为字节格式化或插值运算符，如果format需要单个参数，则值可
以是单个非元组对象，否则值必须是具有格式字节对象或单个映射对象(字典)指定的项目数的元组

其他和字符串格式化相同

#### memory views

memoryview对象允许Python代码访问支持buffer protocol的对象的内部数据而无需复制

### Set类型 -- set、frozenset

#### Set（集合）

```markdown
集合是一个`无序不重复元素`的集。set对象是由具有唯一性的hashable对象组成的无序多项集合。基本功能包括成员关系检测和消除重复
元素。集合对象还支持数学集合类运算，如并集，交集，差集和对称差集等;作为无序集合，不记录元素位置和插入顺序，集合不支持
索引、切片或其他类似序列的行为

目前有两种内置集合类型，set和frozenset，set类型是可变的，其元素可以用add()和remove()方法改变，所以不具有哈希值且不能被用
作字典的键或其他集合的元素。frozenset类型是不可变的并且为hashable，其内容在被创建后不能再改变，因此可以用作字典的键和其他集合的元素

可以用大括号`{}`创建集合。注意：如果要创建一个空集合，你必须用 set() 而不是 {} ；后者创建一个空的字典，集合也支持set推导
a = {x for x in 'abracadabra' if x not in 'abc'}    # set推导

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

```markdown
一个mapping对象映射可哈希值到任意对象，mapping是可变对象；目前只有一种标准的mapping类型,即字典。字典以关键字作为索引
关键字可以是任意不可变类型，通常是字符串和数字。如果一个元组中只包含数字、字符串和元组，那么这个元组也可以作为关键字
如果元组直接或间接的包含了可变对象，那么就不能作为关键字。关键字必须使用不可变类型，也就是说list和包含可变类型的tuple
不能做关键字。在同一个字典中，关键字还必须互不相同。可以将字典看作是一个键值对应的集合，在一个字典中键必须是唯一的
字典主要操作是关键字存储和解析值

# 注意：

1、字典是一种映射类型，它的元素是键值对。构造函数dict()直接从键值对sequence中构建字典，也可以进行推导
2、字典的关键字必须为不可变类型，且不能重复
3、创建空字典使用{}

字典遍历技巧：
* 在字典中遍历时，关键字和对应的值可以使用 items() 方法同时解读出来
* 在序列中遍历时，索引位置和对应值可以使用 enumerate() 函数同时得到
* 同时遍历两个或更多的序列，可以使用 zip() 组合
* 要反向遍历一个序列，首先指定这个序列然后调用 reversesd() 函数
* 要按顺序遍历一个序列，使用 sorted() 函数返回一个已排序的序列，并不修改原值

collections.OrderedDict([items])    返回dict子类的实例，该实例具有专门用于重新排列字典顺序的方法

```

##### python字典常用方法

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

python的with语句支持由上下文管理器定义的运行时上下文的概念，这是使用一对方法实现的，这些方法允许用户定义的类定义在语句体
执行之前输入的运行时上下文，并在语句结束时退出

ython定义了几个上下文管理器，以支持简单的线程同步，文件或其他对象的快速关闭，以及对活动十进制算术上下文的简单操作。除了
实现上下文管理协议之外，特定类型不会被特别处理

Python的生成器和contextlib.contextmanager装饰器提供了一种实现这些协议的便捷方式。如果使用contextlib.contextmanager装饰器
修饰生成器函数，它将返回实现必要__enter__()和__exit__()方法的上下文管理器，而不是由未修饰的生成器函数生成的迭代器

请注意，Python/C API中Python对象的类型结构中没有针对这些方法的特定插槽。想要定义这些方法的扩展类型必须将它们作为普通的
Python可访问方法提供。与设置运行时上下文的开销相比，单个类字典查找的开销可以忽略不计

## 循环

```python
# python循环语句有for和while,while使用与提前知道条件的情况
# 使用while循环计算1-100和

count = 1
num_sum = 0

while count <= 100:
    num_sum += count
    count += 1
print("1~100和是:", num_sum)

# for循环可以遍历任何序列的项目，如列表或字符串,for实例使用break语句，break语句用于跳出当前循环体
# continue语句继续循环的下一个迭代
# pass语句不执行任何操作。当在语法上需要一条语句但该程序不需要任何操作时，可以使用它

foods = ["apple", "beef", "fish"]
for egg in foods:
    if egg == "beef":
        print("两个不同的")
        break   # 此处跳出循环，下一句不再输出
    print("好吃的" + egg)
else:
    print("没有beef了")
print("结束选餐")

# range可以遍历数字序列来自动生成数列，也可以指定某一区间
# 可使range从指定数字开始并指定增量。(可以是负数，有时叫做步长)
for i in range(1, 9, 2):  # 从1到9步长为2
    print(i)

# 结合range()和len()函数遍历一个序列的索引
a = ['lee', 'bob', 'bruce']
for i in range(len(a)):
    print(i, a[i])

```

## 判断语句

```markdown
# if语句基本格式
if condition_1:
    statement_block_1
elif condition_2:
    statement_block_2
else:
    statement_block_3

注意
1、每个条件后面要使用冒号（:），表示接下来是满足条件后要执行的语句块
3、在Python中没有switch – case语句
```

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

## 函数

### 函数的参数

#### 默认参数

为一个或多个参数指定默认值。这将创建一个函数，该函数可以使用比其定义所允许的参数更少的参数来调用

新的power(x, n)函数定义没有问题，但是，旧的调用代码失败了，原因是我们增加了一个参数，
导致旧的代码因为缺少一个参数而无法正常调用，这时候可以用默认参数

```python
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
# 注意：一是必选参数在前，默认参数在后，否则Python的解释器会报错。设置默认参数技巧--当函数有多个参数时，把变化大的参数放
# 前面，变化小的参数放后面。变化小的参数就可以作为默认参数
# 定义默认参数要牢记一点：默认参数必须指向不变对象,x是形参，调用power(3, n=2),3就是实参
```

#### 位置参数

```python
def power(x, n):   # x就是一个位置参数
    s = 1
    while n > 0:
        n = n -1
        s = s * x
    return s
```

#### 可变参数

```python
# 可变参数就是传入的参数个数是可变的，可以是0个到任意个例：给定一组数字a，b，c……，请计算a2 + b2 + c2 + ……
# 定义一个list或tuple参数
def calc(numbers):
    sum1 = 0
    for n in numbers:
        sum1 = sum1 + n * n
    return sum1
# 定义可变参数和定义一个list或tuple参数相比，仅仅在参数前面加了一个* 号。在函数内部，参数numbers接收到的是一个tuple，因此
# 函数代码完全不变。但是，调用该函数时，可以传入任意个参数，包括0个参数。Python允许你在list或tuple前面加一个*号，把list或
# tuple的元素变成可变参数传进去。*numbers表示把numbers这个list或tuple的所有元素作为可变参数传进去
def calc2(*numbers):
    sum2 = 0
    for n in numbers:
        sum2 = sum2 + n * n
    return sum2

```

#### 关键字参数，函数也可以使用kwarg=value的关键字参数形式被调用

可变参数列表：一个最不常用的选择是可以让函数调用可变个数的参数.这些参数被包装进一个元组tuple(查看元组和序列).在这些可
变个数的参数之前,可以有零到多个含参数名的参数:

在函数调用中，关键字参数必须位于位置参数之后,传递的所有关键字参数都必须与函数接受的参数之一匹配,并且它们的顺序并不重要

```python
def arithmetic_mean(*args):
    sum1 = 0
    for x in args:
        sum1 += x
    return sum1

print(arithmetic_mean(45,32,89,78))
print(arithmetic_mean(8989.8,78787.78,3453,78778.73))
print(arithmetic_mean(45,32))
print(arithmetic_mean(45))
print(arithmetic_mean())

# 关键字参数有什么用？它可以扩展函数的功能。比如，在person函数里，我们保证能接收到name和age这两个参数，但是，如果调用者
# 愿意提供更多的参数，我们也能收到。试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字
# 参数来定义这个函数就能满足注册的需求
```

#### 命名关键字参数

```python
# 对于关键字参数，函数的调用者可以传入任意不受限制的关键字参数。至于到底传入了哪些，就需要在函数内部通过kw检查

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
# 使用命名关键字参数时，如果没有可变参数，就必须加一个*作为特殊分隔符。如果缺少*，Python解释器将无法识别位置参数和命名关键字参数：
def person(name, age, city, job):
    # 缺少 *，city和job被视为位置参数
    pass

```

#### 参数组合

```python
# Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意
# 参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数
# 如果函数接受不同类型的实参，必须在函数定义中将接纳任意数量实参的形参放在最后

# 定义一个函数，包含上述若干种参数：在函数调用的时候，Python解释器自动按照参数位置和参数名把对应的参数传进去
def f1(a, b, c=0, *args, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'args =', args, 'kw =', kw)

def f2(a, b, c=0, *, d, **kw):
    print('a =', a, 'b =', b, 'c =', c, 'd =', d, 'kw =', kw)

# 所以，对于任意函数，都可以通过类似func(*args, **kw)的形式调用它，无论它的参数是如何定义的
```

#### 概括

python官方指导意见：

* 如果您不希望用户使用参数名称，请使用仅位置。当参数名称没有实际含义时，如果要在调用函数时强制执行参数的顺序，
或者需要使用一些位置参数和任意关键字，此功能将非常有用
* 仅当名称具有含义且通过使用名称显式使函数定义更易于理解时，或者要防止用户依赖传递的参数的位置时，才应使用关键字
* 对于API，如果将来要修改参数的名称，请仅使用位置参数以防止破坏API更改

### 递归函数

```python
"""
如果一个函数在内部调用自身本身，这个函数就是递归函数.理论上，任何循环都可以转换为递归，使用递归函数需要注意防止栈溢出。在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出
解决递归调用栈溢出的方法是通过尾递归优化，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的
尾递归是指，在函数返回的时候，调用自身本身，并且，return语句不能包含表达式。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况
"""

# 示例计算阶乘的递归函数
# 注意：python默认只能调用1000次，固n要小于等于997
import sys
sys.setrecursionlimit(2000)     # 设置递归调用次数为2000


def fact(n):
    if n == 0:
        return 1
    else:
        return n * fact(n-1)


print(fact(1000))

```

### 解压缩参数列表

当参数已经在列表或元组中，但需要为单独的位置参数的函数调用拆包时，则情况相反。
使用*-operator编写函数调用，以将参数从列表或元组中解包。而字典可以以相同方式**-operator传递关键字参数

```python
"""
python中*和**含义:
当其在函数头内(function header)时
*   收集元组中的所有位置参数
**  收集字典中的所有关键字参数
"""
def function_1(*a, **kw):
    print(a, kw)
function_1(1, 2, 3, x=1, y=2, z=3) # 传参给函数function_1，返回值为(1, 2, 3) {'x': 1, 'y': 2, 'z': 3}

"""
当其在一个函数调用中
当参数已经在列表或元组中但需要为需要单独位置参数的函数调用解包时,与*运算符一起编写函数调用以从列表或元组中解压缩参数
*   将列表或元组解包到位置参数中，即将可迭代变量中的元素作为参数传递给函数(仅当参数数量与可迭代变量中的元素数量相同时，此方法才有效)
**  将字典解压缩为关键字参数，允许传递可变数量的关键字参数给函数(函数中的参数名称必须与字典中的键名称匹配；参数数量应与字典中的键数相同)
"""
list1 = [1, 2, 3]
dic = {'x': 1, 'y': 2, 'z': 3}
function_1(*list1, **dic)

```

### Lambda表达式

可以使用lambda关键字创建小的匿名函数。此函数返回其两个参数的总和：。Lambda函数可以在需要函数对象的任何地方使用。
它们在语法上限于单个表达式,不能包含多个表达式。从语义上讲，它们只是正常函数定义的语法糖。与嵌套函数定义一样，
lambda函数可以引用包含范围的变量：lambda a, b: a+b

```python
# 用途一：使用lambda表达式返回一个函数
def make_incrementor(n):
    return lambda x: x + n

# 用途二：传递一个小函数作为参数
pairs = [(1, 'one'), (2, 'two'), (3, 'three'), (4, 'four')]
pairs.sort(key=lambda pair: pair[1])
print(pairs)

# 尽量使用列表推导, for循环取代filter(), map()以及reduce()
# 将列表所有元素传递给函数并输出
# map函数和lambda
num = [1, 2]
square = list(map(lambda x: x**2, num)) # 将num列表每个元素求其平方后组成一个新的列表

# filter函数和lambda
num = range(0, 10)
list_range = list(filter(lambda x: x > 5, num)) # filter函数过滤并返回符合条件的元素组成的迭代器

# reduce函数和lambda
# 利用reduce求列表元素乘积，也可使用循环来处理
from functools import reduce
data_list = [2, 4, 5]
result = reduce(lambda x, y: x * y, data_list)

```

### map函数

```python
# map函数使用一个函数和一个迭代器作为输入，然后将该函数应用与迭代器的每个值并返回结果列表
list5 = [1, 2, 3, 4, 5]
def square(num):
    return num ** 2

print(list(map(square, list5)))

```

## 面向对象

### 类(Class)

类提供了一种将数据和功能捆绑在一起的方法，创建一个新类将创建一种新的对象类型，从而允许创建该类型的新实例。每个类实例可以具有附加的属性
以维护其状态。类实例还可以具有用于修改其状态的方法（由其类定义），继承机制允许多个基类，派生类可以覆盖其一个或多个基类的任何方法，
一个方法可以调用具有相同名称的基类的方法。对象可以包含任意数量和种类的数据。就像模块一样，类具有Python的动态特性：它们在运行时创建，
并且可以在创建后进行进一步修改

#### 类对象

```markdown
类对象支持两种操作：属性引用和实例化
属性引用使用Python中所有属性引用使用的标准语法obj.name。有效属性名称是创建类对象时在类名称空间中的所有名称，示例：
class MyClass:
    """A simple example class"""
    i = 12345

    def f(self):
        return 'hello world'

MyClass.i和MyClass.f是有效的属性引用，分别返回整数和函数对象。类属性也可以分配给它，因此您可以MyClass.i通过赋值来更改其值。
__doc__也是有效的属性，返回属于类的文档字符串。"A simple example class"

类实例化使用函数表示法。只是假装类对象是一个无参数函数，它将返回该类的新实例
x = MyClass()   # 创建类的新实例并将该对象分配给局部变量x

```

#### 实例对象

```markdown
实例对象只能理解的操作是属性引用。有效属性名称有两种，数据属性和方法
数据属性无需声明；像局部变量一样，它们在首次分配时就存在
另一种实例属性引用是一种方法。方法是“属于”对象的功能
实例对象的有效方法名称取决于其类。根据定义，作为函数对象的类的所有属性都定义了其实例的相应方法。
因此，在我们的示例中，x.f是一个有效的方法引用，因为MyClass.f是一个函数，但是x.i不是，因为 MyClass.i不是。
但这x.f与方法不同，MyClass.f它是方法对象，而不是函数对象

```

#### 方法对象

```markdown
通常方法在绑定之后立即被调用
例如：x.f()
在MyClass示例中，这将返回字符串。但是，不必立即调用方法：方法是一个方法对象，可以立即存储并在以后调用。如下示例：
xf = x.f
while True:
    print(xf()) # 将继续打印直到时间结束

方法的特殊之处在于实例对象作为函数的第一个参数传递。在我们的示例中，该调用x.f()与完全等效MyClass.f(x)。通常，
调用具有n个参数列表的方法等效于调用带有参数列表的函数，该参数列表是通过在第一个参数之前插入方法的实例对象而创建的。

如果您仍然不了解方法的工作方式，那么看一下实现也许可以澄清问题。当引用实例的非数据属性时，将搜索实例的类。
如果名称表示作为函数对象的有效类属性，则通过将实例对象和刚在抽象对象中一起找到的函数对象打包（指向）来创建方法对象：这是方法对象。
当使用实参列表调用方法对象时，将从实例对象和实参列表构造一个新的实参列表，并使用该新的实参列表来调用函数对象
```

#### 类和实例变量

```markdown
一般来说，实例变量用于每个实例唯一的数据，类变量用于类的所有实例共享的属性和方法

其他备注：
如果在实例和类中都出现相同的属性名称，则属性查找将优先考虑该实例

数据属性可以由对象的方法以及普通用户（“客户端”）引用。换句话说，类不能用于实现纯抽象数据类型。实际上，Python中没有任何东西可以强制执行
数据隐藏-它们全都基于约定。（另一方面，用C编写的Python实现可以完全隐藏实现细节，并在必要时控制对对象的访问；这可以由C编写的Python扩展使用

客户应谨慎使用数据属性-客户可能会在数据属性上加盖戳记，从而弄乱了方法维护的不变性。请注意，只要避免名称冲突，客户端就可以将自己的数据属性
添加到实例对象，而不会影响方法的有效性-再次，命名约定可以在这里节省很多麻烦。

没有从方法内部引用数据属性（或其他方法！）的捷径。我发现这实际上提高了方法的可读性：浏览方法时，不会混淆局部变量和实例变量

通常，方法的第一个参数称为self。这只不过是一个约定：该名称self对Python绝对没有特殊含义。但是请注意，如果不遵循该约定，您的代码对于其他
Python程序员来说可能就不太可读，并且还可以想到可能会编写依赖于这种约定的 类浏览器程序。

任何作为类属性的函数对象都为该类的实例定义方法。不必在文本上将函数定义包含在类定义中：将函数对象分配给类中的局部变量也是可以的

方法可以以与普通函数相同的方式引用全局名称。与方法关联的全局范围是包含其定义的模块。（永远不要将类用作全局范围。）虽然很少有人遇到在
方法中使用全局数据的充分理由，但是有很多合法的用途来使用全局范围：一方面，导入全局范围的函数和模块可以由方法以及其中定义的函数和类使用。
通常，包含该方法的类本身是在此全局范围内定义的

每个值都是一个对象，因此具有一个类（也称为其类型）。它存储为object.__class__

```

### 继承(Inheritance)

Python具有两个可用于继承的内置函数：

* 使用isinstance()检查实例的类型：仅当obj.__class__是int或一些类派生自int时，isinstance(obj, int)是True

* 使用issubclass()检查类继承：因为bool是int的子类，issubclass(bool, int)是True，
但是，由于float不是int的子类，所以issubclass(float, int)为False。

### 多重继承(Multiple Inheritance)

### 私有变量

```markdown
Python中不存在只能从对象内部访问的“私有”实例变量。但是，大多数Python代码遵循一个约定：
以下划线开头的名称（例如_spam）应被视为API的非公开部分（无论是函数，方法还是数据成员）

名称修饰有助于让子类覆盖方法而又不中断类内方法调用。例如：
class Mapping:
    def __init__(self, iterable):
        self.items_list = []
        self.__update(iterable)

    def update(self, iterable):
        for item in iterable:
            self.items_list.append(item)

    __update = update   # private copy of original update() method

class MappingSubclass(Mapping):

    def update(self, keys, values):
        # provides new signature for update()
        # but does not break __init__()
        for item in zip(keys, values):
            self.items_list.append(item)

```

## 内置异常

Python中所有异常必须为一个派生自BaseException类的实例。在try带有except提及特定类的子句的语句中，该子句也会处理任何派生自
该类的异常类,(但不处理它所派生出的异常类)通过子类化创建的两个不相关异常类永远是不等效的，既使它们具有相同的名称
try...except语句有一个可选的else字句，使用时必须放在所有except字句后面，raise语句允许程序强制发生指定的异常，raise唯一的
参数就是抛出的异常，这个参数必须是一个异常实例或一个异常类(派生自Exception的类)

### 基类(Base classes)

以下异常主要被用作其他异常的基类

[点击此处查看](https://docs.python.org/3/library/exceptions.html#base-classes)

### Concrete exceptions

[点击此处查看](https://docs.python.org/3/library/exceptions.html#concrete-exceptions)

#### OS 异常

[点击此处查看](https://docs.python.org/3/library/exceptions.html#os-exceptions)

### 警告

[点击此处查看](https://docs.python.org/3/library/exceptions.html#warnings)

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

## 文本处理服务(text processing services)

### 常见字符串常量

| 字符串 | 描述 |
| :------: | :------: |
| string.ascii_letters | 以下lowercase和uppercase两个常量的串联 |
| string.ascii_lowercase | 小写字母abcdefghijklmnopqrstuvwxyz |
| string.ascii_uppercase | 大写字母ABCDEFGHIJKLMNOPQRSTUVWXYZ |
| string.digits | 字符串0123456789 |
| string.hexdigits | 字符串0123456789abcdefABCDEF |
| string.octdigits | 字符串01234567 |
| string.punctuation | ASCII字符的字符串,在C语言环境中被视为标点字符!"#$%&'()*+,-./:;<=>?@[\]^_`{|}~ |
| string.printable | 被视为可打印的ASCII字符串，这是一个组合digits、ascii_letters、punctuation、whitespace |
| string.whitespace | 包含所有被视为空格的ASCII字符的字符串，包括字符间隔，制表符，换行符，返回符，换页符和垂直制表符 |

```python
"""
辅助函数：string.capwords(s, sep=None)
使用str.split()将参数分解为单词，使用str.capitalize()将单词首字母大写，然后使用str.join()连接大写单词，如果sep不存在或为None
则将空格字符的行替换为单个空格，并删除开头和结尾的空格，否则使用sep分隔和连接单词，即当sep匹配到单词的字符时，开始capwords处理字符
"""
import string

s = 'street road'
print(string.capwords(s, sep='r'))  # 输出：StrEet rOad
```

### 格式化字符串语法

str.format()格式化字符串，使用{}括起来要替换的字段，不在花括号之内的内容会不加修改的打印到输出中。它将不加改变地复制到
输出中。如果你需要在文字文本中包含一个大括号字符，可以通过加倍来转义：{{ }}

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

# 示例：
>>> "{} can be {}".format("Strings", "interpolated")
>>> "{0} be nimble, {0} be quick, {0} jump over the {1}".format("Jack", "candle stick")
>>> "{name} wants to eat {food}".format(name="Bob", food="lasagna")
>>> "%s can be %s the %s way" % ("Strings", "interpolated", "old")

```

### Mini-Language格式化

```python
# 格式化整数和浮点数还可以指定是否补0和整数与小数的位数：
print('%2d-%02d' % (3, 1))
print('%.2f' % 3.1415926)

# 字符串里面的%是一个普通字符怎么办？这个时候就需要转义，用%%来表示一个%

# 利用format()方法进行格式化字符串，它会用传入的参数依次替换字符串内的占位符{0},{1}......,但要比占位符麻烦
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

re模块与Perl语言类似的正则表达式匹配操作，模式和被搜索的字符串可以使Unicode字符串也可以是8为字节串(bytes)，但Unicode和
8位字节串不能混用。因反斜杠\在Python中和在正则表达式中，都具有将把特殊字符转换为普通字符串的作用。所以在正则表达式模式
要写成`\\\\`。解决办法是对于正则表达式样式用带有r前缀的表示Python原始字符的方法

|  |  |
| :------: | :------: |
| re.search(pattern, string, flags=0) | 查找在string中与pattern匹配的第一个位置字符，并返回匹配对象和位置。<br>如果string中没有与模式匹配，则返回None |
| re.match() | 与re.search()相似，区别在于re.match在字符串的开头开始寻找匹配项。 |
| re.compile(pattern, flags=0) | 将正则表达式模式编译为正则表达式对象，可使用match()，search()以及下面所述的其他方法将其用于匹配<br>可以通过指定flags值来修改表达式的行为。可以使用`按位运算、或运算`组合 |
| re.findall() | 以string列表形式返回string中所有非重复的匹配项 |
| re.finditer() | 返回一个迭代器，该迭代器生成match对象 |
| re.fullmatch() | 如果整个字符串和正则表达式模式匹配，返回一个对应match对象，否则返回None |

正则表达式转义码：反斜杠本身在python中具有转义含义，使用前缀r消除此问题

| 符号 | 含义 |
| :------: | :------: |
| \d | 一个数字 |
| \D | 一个非数字 |
| \s | 空格(制表符、空格、换行符等) |
| \S | 非空白 |
| \w | 字母 |
| \W | 非字母数字 |

正则表达式锚转义码：

| 符号 | 含义 |
| :------: | :------: |
| ^ | 字符串或行的开头 |
| $ | 字符串或行的结尾 |
| \A | 字符串开始 |
| \Z | 字符串结尾 |
| \b | 单词开头或结尾的空字符串 |
| \B | 空字符串，不在单词的开头或结尾 |

正则表达式标志缩写:可以一起组合使用，例如：(?im)表示多行字符串不区分大小写匹配

| 标志 | 缩写 |
| :------: | :------: |
| ASCII | a |
| IGNORECASE | i |
| MULTILINE | m |
| DOTALL | s |
| VERBOSE | x |

```python
# re.match()和re.search()对比
import re


s = "python tuts"
match = re.match(r'py', s)
if match:
    print(match.group())

s2 = "python tuts"
match = re.search(r'^py', s2)   # re.search()添加^符号与上面re.match()相同
if match:
    print(match.group())

```

### difflib -- helpers for computing deltas

difflib.Differ用于比较文本行序列并s生成人类可读的差异或增量的类。Differ使用SequenceMatcher来比较行的序列并比较相似(近匹配)行中字符序列

Differ delta的每一行都以两个字母的代码开头

| 代码 | 含义 |
| :------: | :------: |
| '- ' | 匹配带有-前缀的行在第一个序列中但不在第二个序列中 |
| '+ ' | 匹配带有+前缀的行在第二个序列中但不在第一个序列中 |
| '  ' | 如果没有更改则在左列打印出多余的一个空白，以便于和其他有差异的输出对齐 |
| '? ' | 如果比较的行内容有所差异则使用?来突出显示更改 |

### textwrap -- Text wrapping and filling

通过调整段落中出现换行符的位置来格式化文本。TextWrapper构造函数接受许多可选的关键字参数。每个关键字参数对应一个实例属性

|  |  |
| :------: | :------: |
| textwrap.wrap(text, width=79, **kwargs) | 将text以width长度划分段落，输出以长度划分的行列表 |
| textwrap.fill(text, width=79, **kwargs) | 将text以width长度划分为新的一行 |
| textwrap.shorten(text, width, **kwargs) | 截断给定文本以适合给定宽度并以[...]折叠被截断的文本 |
| textwrap.dedent(text) | 从文本的每一行中删除任何常见的前导空格、制表符 |
| textwrap.indent(text, prefix, predicate=None) | 在文本中选定行的开头添加前缀 |

### unicodedata -- Unicode Database

提供对unicode字符数据库的访问，该数据库定义了所有unicode字符的字符属性

|  |  |
| :------: | :------: |
| unicodedata.lookup() | 按名称查找字符，若找到给定名称字符则返回相应的字符，若未找到则引发KeyError |
| unicodedata.name() | 返回分配给字符chr的名称作为字符串，若没有定义名称则返回default，若没有则引发ValueError |
| unicodedata.category() | 返回分配给字符chr的常规类别为字符串 |
| unicodedata.combining | 返回分配给字符chr的规范组合类作为整数，若未定义组合类则返回0 |
| unicodedata.normalize() | 返回unicode字符串的正常形式form，form有效值为：NFC、NFKC、NFD、NFKD |

### readline -- GNU readline interface

readline模块定义多种功能以帮助完成从python解释器读取或写入历史文件，可以直接使用也可以通过rlcompleter支持交互式提示下的python标识符完成
的模块使用，使用此模块会影响程序的交互式提示和内置input()功能提供的提示行为

init文件：以下功能与初始化文件和用户配置有关

readline.parse_and_bind(string)     执行在string参数中提供的初始化行
readline.read_init_file([filename]) 执行一个readline初始化文件。默认文件名为最近所使用的文件名

https://docs.python.org/zh-cn/3/library/readline.html

### rlcompleter -- Completion function for GNU readline

rlcompeleter 通过补全有效的Python标识符和关键字定义了一个适用于readline模块的补全函数
其Completer对象，此对象具有以下方法：

Completer.complete(text, state)    为text返回第state项补全

## Binary Data Services

### struct -- Interpret bytes as packed binary data

此模块可以执行 Python 值和以 Python bytes 对象表示的 C 结构之间的转换。
这可以被用来处理存储在文件中或是从网络连接等其他来源获取的二进制数据。
它使用格式字符串作为 C 结构布局的精简描述以及与 Python 值的双向转换

struct定义的函数

|  |  |
| :------: | :------: |
| struct.pack() | 返回一个 bytes 对象，其中包含根据格式字符串format打包的<br>值v1, v2, ...参数个数必须与格式字符串所要求的值完全匹配 |
| struct.unpack() | 根据格式字符串format从缓冲区buffer解包（假定是由pack(format, ...)打包），<br>结果为一个元组，即使其只包含一个条目。 缓冲区的字节大小必须匹配格式所要求的大小 |
| struct.unpack_from() | 对 buffer 从位置 offset 开始根据格式字符串 format 进行解包。 结果为一个元组，<br>即使其中只包含一个条目。 缓冲区的字节大小从位置 offset 开始必须至少为 calcsize() 显示的格式所要求的大小 |
| struct.calcsize() | 返回与格式字符串format相对应的结构的大小(即pack()方法所产生的字节串对象的大小)  |

struct模块还定义以下类型：
class struct.Struct(format)类返回一个新的Struct对象，其根据格式字符串format写入和读取二进制数据
一次性创建Struct对象并调用其方法，相比使用同样格式调用struct函数更高效，因为字符串只需被编译一次

注解：传递给Struct和模块层级函数的已编译版最新格式字符串会被缓存，
因此只使用少量格式字符串的程序无需担心重用单独的Struct实例

已编译的Struct对象支持以下方法和属性：

方法：

|  |  |
| :------: | :------: |
| pack() | 等价于pack()函数，len(result)将等于size |
| pack_into() | 等价于pack_into()函数 |
| unpack(buffer) | 等价于unpack()函数，缓冲区的字节大小必须等于size |
| unpack_from(buffer, offset=0) | 等价于unpack_from()函数，缓冲区的字节大小<br>从位置offset开始且必须至少为size大小 |
| iter_unpack(buffer) | 等价于iter_unpack()函数，缓冲区的字节大小必须为size的整数倍 |

属性：

format  用于构造此 Struct 对象的格式字符串。3.7版更改: 格式字符串类型现在是str而不再是 bytes

size    计算出对应于format的结构大小（即pack()方法所产生的字节串对象的大小）

### codecs -- Codec registry and base classes

模块定义了标准 Python 编解码器（编码器和解码器）的基类，并提供接口用来访问内部的 Python 编解码器注册表，该注册表负责管理编解码器和错误处理的查找过程。 大多数标准编解码器都属于 文本编码，它们可将文本编码为字节串，但也提供了一些编解码器可将文本编码为文本，以及字节串编码为字节串。 自定义编解码器可以在任意类型间进行编码和解码，但某些模块特性仅适用于 文本编码 或将数据编码为 字节串 的编解码器

以下用于使用任何编解码器进行编码和解码的函数:

|  |  |
| :------: | :------: |
| codecs.encode() | 使用encoding注册的编解码器对obj进行编码 |
| codecs.decode() | 使用encoding注册的编解码器对obj进行解码 |
| codecs.lookup() | 在python编解码器注册表中查找编解码器信息并返回一个CodecInfo对象 |
| codecs.getwriter() | 查找给定编码的编解码器并返回其StreamWriter类或factory函数 |
| codecs.open() | 使用给定的mode打开已编码文件并返回一个SteamReaderWriter的实例，默认文件模式为r |

## Data Types

### datetime基本的日期和时间类型

datetime模块提供操纵日期和时间的类，但实现的重点是针对输出格式化和提取操作的有效属性

#### datetime模块具有的常量

|  |  |
| :------: | :------: |
| datetime.MINYEAR | date或datetime对象中允许的最小年份,MINYEAR值为1 |
| datetime.MAXYEAR | date或datetime对象中允许的最大年份，MAXYEAR值为9999 |

#### datetime模块具有的类，这些类的对象都是不可变的

|  |  |
| :------: | :------: |
| datetime.date | 假设当前公历始终有效并且永远有效，参数：year,month,day,参数都是必须的且必须是整数 |
| datetime.time | 一个理想的时间，独立于特定一天，假设每天正好24*60*60秒，属性：hour,minute,second,microsecond,tzinfo |
| datetime.datetime | 日期和时间的组合，属性：year,month,day,hour,minute,second,microsecond,tzinfo |
| datetime.timedalta | 表达在date,time或datetime实例之间的差异的持续时间，所有参数默认值为0 |
| datetime.tzinfo | 时区信息对象的抽象基类，通过datetime和time类来提供时间调整 |
| datetime.timezone | 将tzinfo抽象基类实现为相对于UTC的固定偏移量的类 |

```markdown
对应子类关系：
Object
    timedelta
    tzinfo
        timezone
    time
    date
        datetime

公共特性：date、datetime、time和timezone类型共享这些特性
* 这些类型的对象是不可变的
* 这些类型的对象可哈希的，意味着可以用作字典的key
* 这些类型的对象通过pickle模块支持高效的序列化。

确定对象是Aware或Naive
* date类型的对象都是naive
* time或datetime类型的对象可能为aware或naive
    当以下两个条件满足时，datetime类型的对象d是aware，否则d为naive
    1. d.tzinfo不能为None
    2. d.tzinfo.utcoffset(d)不会返回None
    当以下两个条件满足时，time类型对象t是aware，否则为naive
    1. t.tzinfo不为None
    2. t.tzinfo.utcoffset(None)不返回None

```

#### timedelta类对象

表示两个date或者time的时间间隔

datetime.timedalta(days=0, seconds=0, microseconds=0, milliseconds=0, minutes=0, hours=0, weeks=0)
所有参数都是可选的并且默认为 0。 这些参数可以是整数或者浮点数，也可以是正数或者负数

类属性：
timedelta.min
The most negative timedelta object, timedelta(-999999999)

timedelta.max
The most positive timedelta object, timedelta(days=999999999, hours=23, minutes=59, seconds=59, microseconds=999999)

timedelta.resolution
两个不相等的timedelta类对象最小的间隔为timedelta(microseconds=1)

#### date对象

一个date对象表示理想化日历中的日期(年月日)

|  |  |
| :------: | :------: |
| date.today() | 返回当前的本地日期，等同于date.fromtimestamp(time.time()) |
| date.fromtimestamp(timestamp) | 返回与POSIX时间戳相对应的本地日期，非POSIX系统，时间戳包含leap秒概念会被忽略 |
| date.fromordinal(ordinal) | 返回对应于多格勒公历序号的日期，其中第一年的一月一日为序号1 |
| date.fromisoformat(date_string) | 返回date与date_string相对应的格式，格式为:YYYY-MM-DD，此为date.isofromat函数的反函数 |
| date.fromisocalendar(year,week,day) | 返回date与按年，周和日指定的iso日历日期，此为date.isocalendar函数的反函数 |

date类属性：

|  |  |
| :------: | :------: |
| date.min | 最早的日期 |
| date.max | 最大的日期 |
| date.resolution | 非相等日期对象之间的最小差异 |

实例属性(只读)：

|  |  |
| :------: | :------: |
| date.year  date.month  date.day | 分别对应年月日 |

实例方法：
|  |  |
| :------: | :------: |
| date.replace() | 返回一个具有相同值的日期，但通过指定关键字参数给定新值的参数除外 |
| date.timetuple() | 返回带有time.localtime()的元组类型时间 |
| date.toordinal() |  |
| date.weekday() | 以整数形式返回星期几，星期一为0,星期日为6，例如：date(2019, 11, 23).weekday() |
| date.isoweekday() | 以整数形式返回星期几，星期一为1，星期日为7 |
| date.isocalendar() | 返回一个三元组(iso年，iso周号，iso工作日) |
| date.isoformat() | 返回以iso8601格式表示的日期的字符串YYY-MM-DD |
| date.__str__() | 例如：对于日期d，str(d)等同于d.isoformat() |
| date.ctime() | 返回表示日期的字符串，等效于time.ctime(time.mktime(d.timetuple())) |
| date.strftime() | 返回表示日期的字符串，由明确的格式控制 |
| date.__format__() | 与date.strftime()相同，这样date在格式化字符串以及为对象指定格式字符串 |

#### datetime对象

datetime是包含从date对象和time对象的所有信息的单独对象,注意此datetime是由datetime类产生的datetime实例对象

类方法：

|  |  |
| :------: | :------: |
| datetime.datetime() | 年月日为必须参数，tzinfo可以是None或tzinfo子类的实例，fold参数范围在[0, 1]列表中 |
| datetime.today() | 通过返回当前本地日期时间 |
| datetime.now(tz=None) | 返回当前的本地日期和时间，tz值不是none时，必须为tzinfo子类的实例且当前日期和时间将转换为tz的时区 |
| datetime.utcnow() | 返回当前utc日期和时间 |
| datetime.fromtimestamp() | 返回与POSIX时间戳相对应的本地日期和时间 |
| datetime.utcfromtimestamp() | 返回与datetimePOSIX时间戳相对应的UTC时间 |
| datetime.fromordinal() |  |
| datetime.combine() | 返回一个新datetime对象，其日期分量等于给定date对象的日期，并且其时间分量等于给定time对象的日期。<br>如果提供了tzinfo 参数，则使用其值设置tzinfo结果的tzinfo属性，否则使用time参数的属性 |
| datetime.fromisoformat() | 返回datetime与date_string对应的格式 |
| datetime.fromisocalendar() | 返回datetime与按年，周和日指定的ISO日历日期相对应的日期。datetime的非日期部分将使用其默认值填充 |
| datetime.strptime() | 返回datetime与date_string相对应的内容，并根据format进行解析 |

#### time对象

time对象表示一天中的（本地）时间，与任何特定的日期无关，并且可以通过tzinfo对象进行调整

time实例方法：

|  |  |
| :------: | :------: |
| time.isoformat() | 以ISO 8601格式返回代表时间的字符串 |
| time.strftime() | 返回表示时间的字符串，由明确的格式字符串控制,参考以下strftime和strptime行为 |

下表提供了strftime()与strptime()的高级比较

|  | strftime | strftime |
| :------: | :------: | :------: |
| 用法 | 根据给定的格式将对象转换为字符串 | 将字符串解析为给定相应格式的 datetime 对象 |
| 方法类型 | 实例方法 | 类方法 |
| 方法 | date、datetime、time | datetime |
| Signature | strftime(format) | strptime(date_string, format) |

strftime()和strptime()格式代码

date,datetime,time对象都支持strftime()方法用于以字符串格式化时间，相反，striptime()方法从日期或时间的字符串返回一个datetime对象

|  | strftime | strptime |
| :------: | :------: | :------: |
| 用法 | strftime(format) | strptime(date_string, format) |

| 方法类型 |  |  |
| method of |  |  |
| signature |  |  |
|  |  |  |
|  |  |  |

### calendar与日历相关功能

calendar.Calendar(firstweekday=0),创建一个Calendar对象，firstweekday是一个整数，指定一周第一天，默认为0代表周一

Calendar实例具有以下方法

|  |  |
| :------: | :------: |
| iterweekdays() | 返回用于一周星期几的迭代器，迭代器的第一个值和firstweekday属性值相同 |
| itermonthdates() | 返回一年1-12月的迭代器，该迭代器将返回datetime.date该月的所有天（作为对象），<br>以及返回一个完整星期所需的月初或月末之后的所有天 |
| itermonthdays() | 返回天数为月份中的天数，对于月份以外的日期，天数为0 |
| itermonthdays2() | 返回天数为元组，它由一个月中的一号和一个工作日号(星期几)组成 |
| itermonthdays3() | 返回天数为元组，它由一年，一个月和一个月中的第几天组成 |
| itermonthdays4() | 返回天数为元组，它由一年，一个月，一个月中的第几天以及一个工作日号(星期几)的组成 |
| monthdatescalendar() | 以列表形式返回每月的星期列表，每周列表是七个datetime.date对象的列表 |
| monthdayscalendar() | 返回该月的周列表，周列表是工作日号组成 |
| mongthdays2calendar() | 返回该月的周列表，周列表由日期和工作日组成的七个元组组成 |
| yeardatescalendar() | 返回指定年份准备格式化的数据。返回值是月份行的列表。每个月行最多包含宽度月,每周包含1-7天。天是datetime.date对象 |
| yeardays2calendar() | 返回周列表，周列表为天数和工作日的元组，该月以外的天数为0 |
| yeardayscalendar() | 周列表为天数，该月以外的天数为0 |

此类用于生成纯文本日历TextCalendar实例具有以下方法

|  |  |
| :------: | :------: |
| formatmonth() | 以多行字符串返回一个月日历，w为宽度，l为行数 |
| prmonth() | 以formatmonth()返回值直接打印一个月的日历 |
| formatyear() | 以多行字符串返回一年的日历 |
| pryear() | 以formatyear()返回值直接打印一年的日历 |

此类用于生成html日历，HTMLCalendar实例方法

|  |  |
| :------: | :------: |
| formatmonth() | 以html表格返回一个月的日历 |
| formatyear() | 以html表格返回一年的日历 |
| formatyearpage() | 以完整的html页面返回一年的日历，css参数为要使用的级联样式表的名称，None则不使用样式表 |

TextCalendar子类LocaleTextCalendar()可以在构造函数中传递一个语言环境名称，将在指定的语言环境中返回月份和工作日
HTMLCalendar子类LocaleHtmlCalendar()可以在构造函数中传递一个语言环境名称，将在指定的语言环境中返回月份和工作日

calendar类的方法

|  |  |
| :------: | :------: |
| setfirstweekday() | 将某工作日设置为一周的开始 |
| firstweekday() | 返回每个星期开始的工作日设置 |
| isleap() | 判断是否为闰年，是则为True，否则为False |
| leapdays(y1, y2) | 返回y1至y2(不包括y2)的年数 |
| weekday() | 返回指定年月日对应的工作日号 |
| weekheader() | 返回包含工作日名称缩写的头,n表示返回头的宽度 |
| monthrange() | 返回指定年月中第一天的工作日号和月份的天数 |
| monthcalendar() | 返回代表一个月日历的周列表，月份以外的用0表示，默认以每个星期星期一开始，除非setfirstweekday()指定星期 |
| prmonth() | 通过formatmonth()返回月份日历 |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |

### collections容器数据类型

标准内建容器有dict、list、set、tuple，collections实现特定的容器

|  |  |
| :------: | :------: |
| namedtuple() | 创建命名元组子类的工厂函数 |
| deque | 类似列表(list)的容器，实现了在两端快速添加(append)和弹出(pop) |
| ChainMap | 类似字典(dict)的容器类，将多个映射集合到一个视图里面 |
| Counter | 字典的子类，提供了可哈希对象的计数功能 |
| OrderedDict | 字典的子类，保存了他们被添加的顺序 |
| defaultdict | 字典的子类，提供了一个工厂函数，为字典查询提供一个默认值 |
| UserDict | 封装了字典对象，简化了字典子类化 |
| UserList | 封装了列表对象，简化了列表子类化 |
| UserString | 封装了列表对象，简化了字符串子类化 |

Counter对象
一个Counter是一个dict的子类，用于计数可哈希对象；它是一个集合，元素像字典键(key)一样存储，计数可以是任何整数值，包括0和负数

```python
# 记录单词在列表中出现的次数
cnt = collections.Counter()
for word in ['red', 'blue', 'red', 'green', 'blue', 'blue']:
    cnt[word] += 1
print(cnt)  # 输出Counter({'blue': 3, 'red': 2, 'green': 1})

# 找出《哈姆雷特》中最常见的两个单词
import re
words = re.findall(r'\w+', open('hamlet.txt').read().lower())
print(collections.Counter(words).most_common(2))  # 输出[('the', 34545), ('and', 22226)]
```

collections.abc --- 容器的抽象基类
heapq --- 堆队列算法
bisect --- 数组二分查找算法
array --- 高效的数值数组
weakref --- 弱引用
types --- 动态类型创建和内置类型名称

### copy浅层(shallow)和深层(deep)复制

浅层复制和深层复制之间的区别仅与复合对象 (即包含其他对象的对象，如列表或类的实例) 相关

1. 一个 浅层复制 会构造一个新的复合对象，然后（在可能的范围内）将原对象中找到的 引用 插入其中
2. 一个 深层复制 会构造一个新的复合对象，然后递归地将原始对象中所找到的对象的 副本 插入

深度复制操作通常存在两个问题, 而浅层复制操作并不存在这些问题：

1. 递归对象 (直接或间接包含对自身引用的复合对象) 可能会导致递归循环。
2. 由于深层复制会复制所有内容，因此可能会过多复制（例如本应该在副本之间共享的数据）

深层复制避免以上问题方法：保留在当前复制过程中已复制的对象的 "备忘录"(memo)字典；以及允许用户定义的类重载复制操作或复制的组件集合

该模块不复制模块、方法、栈追踪（stack trace）、栈帧（stack frame）、文件、套接字、窗口、数组以及任何类似的类型。它通过不改变地返回原始对象来（浅层或深层地）“复制”函数和类；这与 pickle 模块处理这类问题的方式是相似的

### pprint数据输出美化

## math和math模块

### numbers数字的抽象基类

### math数学函数

### cmath --- 关于复数的数学函数

### decimal --- 十进制定点和浮点运算

### fractions --- 分数

### random --- 生成伪随机数

#### bookkeeping函数

|  |  |
| :------: | :------: |
| seed() | 随机数生成器的种子 |
| getstate() | 返回捕获到的生成器当前内部状态的对象，此对象可传递给setstate()来恢复状态 |
| setstate() | 将生成器内部状态恢复到被getstate()调用时的状态 |
| getrandbits(k) | 返回带有k位的随机python整数，可用randrange()来处理任意大范围 |

```python
random.seed(42)
print(random.sample(range(20), k=10))
st = random.getstate()  # remeber this state
print(random.sample(range(20), k=20))  # print 20
random.setstate(st)     # restore state
print(random.sample(range(20), k=12))  # print same first 10
```

#### 用于整数的函数

|  |  |
| :------: | :------: |
| randrange() | 从 range(start, stop, step) 返回一个随机选择的元素<br>相当于choice(range(start, stop, step)) ，但实际上并没有构建一个 range 对象 |
| randint() | 返回随机整数 N 满足 a <= N <= b。相当于 randrange(a, b+1) |

#### 用于序列的函数

|  |  |
| :------: | :------: |
| choice(seq) | 从非空序列 seq 返回一个随机元素。 如果 seq 为空，则引发 IndexError |
| choices(population, weights=None, \*, cum_weights=None, k=1) | 从*population*中选择替换，返回大小为 k 的元素列表。 如果 population 为空，则引发 IndexError |
| shuffe(x[, random]) | 将序列 x 随机打乱位置 |
|  |  |
|  |  |
|  |  |
|  |  |

### statistics --- 数学统计函数

## File & Directory access

### 文件对象的方法

open()函数打开文件并返回相应的文件对象
语法格式为：open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)

* file: 必需，文件路径（相对或者绝对路径）
* mode: 可选，决定了打开文件的模式：只读，写入，追加等。可选参数，默认模式只读(r)
* buffering: 用于设置缓冲策略的可选整数，传递0来关闭缓冲（仅在二进制模式下允许），传递1来选择行缓冲（仅在文本模式下可用），<br>传递一个大于1的整数以指示固定大小的块缓冲区的字节大小
* encoding: 用于解码或编码文件的编码的名称，仅在文本模式下使用
* errors: 可选字符串，指定处理编码和编码错误的方式，不能在二进制模式下使用
* newline: 控制换行，仅用于文本模式
* closefd: 若closefd是False并且给出了文件描述符而不是文件名，则在关闭文件时，底层文件描述符将保持打开状态。<br>如果给定文件名，closefd必须为True（默认），否则将引发错误
* opener:可以通过传递可调用的opener来使用自定义opener。然后通过使用（file,flags）调用opener获得文件对象的基础文件描述符。<br>opener必须返回一个打开的文件描述符

mode可用模式有：

| 模式 | 描述 |
| :------: | :------: |
| r | 以只读方式打开文件(默认模式) |
| rb | 以二进制格式打开一个文件用于只读 |
| r+ | 打开一个文件用于读写 |
| rb+ | 以二进制格式打开一个文件用于读写 |
| w | 打开文件用于写入。首先截断文件，即有内容会被清除覆盖 |
| wb | 以二进制格式打开文件用于写入。首先截断文件，即有内容会被清除覆盖 |
| w+ | 打开文件用于读写。首先截断文件，即有内容会被清除覆件 |
| a | 打开文件用于追加。如果该文件不存在，创建新文件进行写入 |
| ab | 以二进制格式打开文件用于追加。新的内容将会被写入到已有内容之后。如果该文件不存在，创建新文件进行写入 |
| a+ | 打开文件用于读写。文件已存在是追加模式。如果该文件不存在，创建新文件用于读写 |
| ab+ | 以二进制格式打开文件用于追加。如果该文件已存在则追加，如果该文件不存在，创建新文件用于读写 |

mode模式及对应支持操作表：

| 模式 | r | r+ | w | w+ | a | a+ |
| :------: | :------: | :------: | :------: | :------: | :------: | :------: |
| 读 | yes | yes |  | yes |  | yes |
| 写 |  | yes | yes | yes | yes | yes |
| 创建 |  |  | yes | yes | yes | yes |
| 覆盖 |  |  | yes | yes |  |  |
| 从文件开头读写 | yes | yes | yes | yes |  |  |
| 从文件结尾读写 |  |  |  |  | yes | yes |

文件对象常用方法

|  |  |
| :------: | :------: |
| file.close() | 关闭文件。关闭后文件不能再进行读写操作 |
| file.flush() | 刷新文件内部缓冲，直接把内部缓冲区的数据立刻写入文件, 而不是被动的等待输出缓冲区写入 |
| file.fileno() | 返回一个整型的文件描述符(file descriptor FD 整型, 可以用在如os模块的read方法等一些底层操作上 |
| file.isatty() | 如果文件连接到一个终端设备返回 True，否则返回 False |
| file.next() | 返回文件下一行 |
| file.read([size]) | 从文件读取指定的字节数，如果未给定或为负则读取所有 |
| file.readline([size]) | 读取整行，包括 "\n" 字符 |
| file.readlines([sizehint]) | 读取所有行并返回列表，若给定sizeint>0，返回总和大约为sizeint字节的行,<br>实际读取值可能比sizeint较大, 因为需要填充缓冲区 |
| file.seek(offset[, whence]) | 设置文件当前位置 |
| file.tell() | 返回文件当前位置 |
| file.truncate([size]) | 截取文件，截取的字节通过size指定，默认为当前文件位置 |
| file.write(str) | 将字符串写入文件，返回的是写入的字符长度 |
| file.writelines(sequence) | 向文件写入一个序列字符串列表，如果需要换行则要自己加入每行的换行符 |

OS文件或目录方法

| 方法 | 描述 |
| :------: | :------: |
| os.access(path, mode) | 检验权限模式 |
| os.chdir(path) | 改变当前工作目录 |
| os.chflags(path, flags) | 设置路径的标记为数字标记 |
| os.chmod(path, mode) | 更改权限 |
| os.chown(path, uid, gid) | 更改文件所有者 |
| os.chroot(path) | 改变当前进程的根目录 |
| os.close(fd) | 关闭文件描述符 fd |
| os.closerange(fd_low, fd_high) | 关闭所有文件描述符，从 fd_low (包含）到 fd_high (不包含）错误会忽略 |
| os.dup(fd) | 复制文件描述符 fd |
| os.dup2(fd, fd2) | 将一个文件描述符 fd 复制到另一个 fd2 |
| os.fchdir(fd) | 通过文件描述符改变当前工作目录 |
| os.fchmod(fd, mode) | 改变一个文件的访问权限，该文件由参数fd指定，参数mode是Unix下的文件访问权限 |
| os.fchown(fd, uid, gid) | 修改一个文件的所有权，这个函数修改一个文件的用户ID和用户组ID，该文件由文件描述符fd指定 |
| os.fdatasync(fd) | 强制将文件写入磁盘，该文件由文件描述符fd指定，但是不强制更新文件的状态信息 |
| os.fdopen(fd[, mode[, bufsize]]) | 通过文件描述符 fd 创建一个文件对象，并返回这个文件对象 |
| os.fpathconf(fd, name) | 返回一个打开的文件的系统配置信息。name为检索的系统配置的值，它也许是一个定义系统值的字符串，<br>这些名字在很多标准中指定（POSIX.1, Unix 95, Unix 98, 和其它） |
| os.fstat(fd) | 返回文件描述符fd的状态，像stat( |
| os.fstatvfs(fd) | 返回包含文件描述符fd的文件的文件系统的信息，像 statvfs( |
| os.fsync(fd) | 强制将文件描述符为fd的文件写入硬盘 |
| os.ftruncate(fd, length) | 裁剪文件描述符fd对应的文件, 所以它最大不能超过文件大小 |
| os.getcwd() | 返回当前工作目录 |
| os.getcwdu() | 返回一个当前工作目录的Unicode对象 |
| os.isatty(fd) | 如果文件描述符fd是打开的，同时与tty(-like)设备相连，则返回true, 否则False |
| os.lchflags(path, flags) | 设置路径的标记为数字标记，类似 chflags()，但是没有软链接 |
| os.lchmod(path, mode) | 修改连接文件权限 |
| os.lchown(path, uid, gid) | 更改文件所有者，类似 chown，但是不追踪链接 |
| os.link(src, dst) | 创建硬链接，名为参数 dst，指向参数 src |
| os.listdir(path) | 返回path指定的文件夹包含的文件或文件夹的名字的列表 |
| os.lseek(fd, pos, how) | 设置文件描述符 fd当前位置为pos, how方式修改: SEEK_SET 或者 0 设置从文件开始的计算的pos;<br>SEEK_CUR或者 1 则从当前位置计算; os.SEEK_END或者2则从文件尾部开始. 在unix，Windows中有效 |
| os.lstat(path) | 像stat() |
| os.major(device) | 从原始的设备号中提取设备major号码,使用stat中的st_dev或者st_rdev field),但是没有软链接 |
| os.makedev(major, minor) | 以major和minor设备号组成一个原始设备号 |
| os.makedirs(path[, mode]) | 递归文件夹创建函数。像mkdir(), 但创建的所有intermediate-level文件夹需要包含子文件夹 |
| os.minor(device) | 从原始的设备号中提取设备minor号码,使用stat中的st_dev或者st_rdev field |
| os.mkdir(path[, mode]) | 以数字mode的mode创建一个名为path的文件夹.默认的 mode 是 0777 (八进制) |
| os.mkfifo(path[, mode]) | 创建命名管道，mode 为数字，默认为 0666 (八进制 |
| os.mknod(filename[, mode=0600, device]) | 创建一个名为filename文件系统节点（文件，设备特别文件或者命名pipe） |
| os.open(file, flags[, mode]) | 打开一个文件，并且设置需要的打开选项，mode参数是可选的 |
| os.openpty() | 打开一个新的伪终端对。返回 pty 和 tty的文件描述符 |
| os.pathconf(path, name) | 返回相关文件的系统配置信息 |
| os.pipe() | 创建一个管道. 返回一对文件描述符(r, w)分别为读和写 |
| os.popen(command[, mode[, bufsize]]) | 从一个 command 打开一个管道 |
| os.read(fd, n) | 从文件描述符 fd 中读取最多 n 个字节，返回包含读取字节的字符串，文件描述符 fd对应文件已达到结尾, 返回一个空字符串 |
| os.readlink(path) | 返回软链接所指向的文件 |
| os.remove(path) | 删除路径为path的文件。如果path 是一个文件夹，将抛出OSError; 查看下面的rmdir()删除一个 directory |
| os.removedirs(path) | 递归删除目录 |
| os.rename(src, dst) | 重命名文件或目录，从 src 到 dst |
| os.renames(old, new) | 递归地对目录进行更名，也可以对文件进行更名 |
| os.rmdir(path) | 删除path指定的空目录，如果目录非空，则抛出一个OSError异常 |
| os.stat(path) | 获取path指定的路径的信息，功能等同于C API中的stat()系统调用 |
| os.stat_float_times([newvalue]) | 决定stat_result是否以float对象显示时间戳 |
| os.statvfs(path) | 获取指定路径的文件系统统计信息 |
| os.symlink(src, dst) | 创建一个软链接 |
| os.tcgetpgrp(fd) | 返回与终端fd（一个由os.open()返回的打开的文件描述符）关联的进程组 |
| os.tcsetpgrp(fd, pg) | 设置与终端fd（一个由os.open()返回的打开的文件描述符）关联的进程组为pg |
| os.tempnam([dir[, prefix]]) | Python3 中已删除。返回唯一的路径名用于创建临时文件 |
| os.tmpfile() | Python3 中已删除。返回一个打开的模式为(w+b)的文件对象 .这文件对象没有文件夹入口，没有文件描述符，将会自动删除 |
| os.tmpnam() | Python3 中已删除。为创建一个临时文件返回一个唯一的路径 |
| os.ttyname(fd) | 返回一个字符串，它表示与文件描述符fd 关联的终端设备。如果fd 没有与终端设备关联，则引发一个异常 |
| os.unlink(path) | 删除文件路径 |
| os.utime(path, times) | 返回指定的path文件的访问和修改的时间 |
| os.walk(top[, topdown=True[, onerror=None[, followlinks=False]]]) | 输出在文件夹中的文件名通过在树中游走，向上或者向下 |
| os.write(fd, str) | 写入字符串到文件描述符 fd中. 返回实际写入的字符串长度 |
| os.path 模块) | 获取文件的属性信息 |

### python读写json

```markdown
序列化：将对象转换为适合通过网络传输或存储在文件或数据库中的特殊格式的过程称为序列化
反序列化：与序列化相反。它将序列化返回的特殊格式转换回可用的对象

例如处理JSON格式时，当我们序列化对象时，实际上是将Python对象转换为JSON字符串(dump()进行序列化)，
反序列化则通过其JSON字符串表示形式构建Python对象(load()反序列化)

```

#### dump()序列化

```python
"""
dump()函数用于序列化数据，它接受一个python对象，对其进行序列化然后输出json字符串
语法：dump(obj, fp)
obj：要进行序列化的对象
fp：类似文件的对象，将把序列化数据写入到此对象
"""
import json


# 创建python字典类型变量person
person = {
    "first_name": "John",
    "isAlive": True,
    "age": 27,
    "address": {
        "streetAddress": "21 2nd Street",
        "city": "New York",
        "state": "NY",
        "postalCode": "10021-3100"
    },
    "hasMortgage": None
}

# 注意事项：在序列化对象时，Python的类型None将转换为JSON的null类型
with open('person.json', 'w') as f:     # 将json对象person写入到person.json文件
    json.dump(person, f)

open('person.json', 'r').read()     # 以字符串形式读取JSON对象

```

序列化数据时类型之间的转换：

| PYTHON TYPE | JSON TYPE |
| :------: | :------: |
| dict | object |
| list, tuple | array |
| int | number |
| float | number |
| str | string |
| TRUE | TRUE |
| FALSE | FALSE |
| None | null |

反序列化数据时类型之间的转换：

| JSON TYPE | PYTHON TYPE |
| :------: | :------: |
| object | dict |
| array | list |
| string | str |
| number (int) | int |
| number (real) | float |
| TRUE | TRUE |
| FALSE | FALSE |
| null | None |

#### load()反序列化

```python
import json
# load()函数从类似于object的文件中反序列化JSON对象并返回
with open('person.json', 'r') as f:
    person = json.load(f)
```

#### dumps()和loads()

```python
# dumps()和dump()函数工作方式相同，但不是将输出发送到类文件的对象，而是将输出作为字符串返回
# loads()和load()函数也一样，但不是从文件中反序列化json字符串，而是从反序列化一个字符串

```

#### pickle

```python
# pickle模块以二进制格式序列化数据，提供接口和json模块相同，load和dump
# 语法：dump(obj, file)
# obj：需要序列化的数据对象
# file：将序列化的数据对象写入到文件对象
```

## 开发者工具

### unittest

unittest.TestCase类中提供了很多断言方法，断言方法用于检验条件是否满足期望的结果

unittest类的断言方法

| 方法 | 用途 |
| :------: | :------: |
| assertEqual(a, b)  | 核实a == b |
| assertNotEqual(a, b)  | 核实a != b |
| assertTrue(x)  | 核实x为True |
| assertFalse(x)  | 核实x为False |
| assertIn(item, list)  | 核实item在list中 |
| assertNotIn(item, list) | 核实item不在list中 |

## 多线程

```markdown
线程是一种用于解耦与顺序无关的任务的技术。线程可用于提高在后台运行其他任务时接受用户输入的应用程序的响应能力。
一个相关的用例是与另一个线程中的计算并行运行I/O

以下代码显示了高级threading模块如何在主程序继续运行的同时在后台运行任务：

import threading, zipfile

class AsyncZip(threading.Thread):
    def __init__(self, infile, outfile):
        threading.Thread.__init__(self)
        self.infile = infile
        self.outfile = outfile

    def run(self):
        f = zipfile.ZipFile(self.outfile, 'w', zipfile.ZIP_DEFLATED)
        f.write(self.infile)
        f.close()
        print('Finished background zip of:', self.infile)

background = AsyncZip('mydata.txt', 'myarchive.zip')
background.start()
print('The main program continues to run in foreground.')

background.join()    # Wait for the background task to finish
print('Main program waited until background was done.')

多线程应用程序的主要挑战是协调共享数据或其他资源的线程。为此，线程模块提供了许多同步原语，包括锁，事件，条件变量和信号量。

尽管这些工具功能强大，但是较小的设计错误可能会导致难以重现的问题。因此，任务协调的首选方法是将所有对资源的访问集中在单个线程中，
然后使用该queue模块为该线程提供来自其他线程的请求。使用Queue对象进行线程间通信和协调的应用程序更易于设计，更具可读性和可靠性
```
