# python3实例手册

|  |  |  |  |  |  |
| :------: | :------: | :------: | :------: | :------: | :------: |
|  |  |  |  |  |  |

```py
#!/usr/bin/python3
# -*- coding: UTF-8 -*-

Python中单行注释以#开头，多行注释用三个单引号（```）或者三个双引号（"""）将注释括起来。
```

***

```py
python中数的数据类型有四种：整数、长整数、浮点数、复数
python中变量无需声明，每个变量在使用前必须赋值，变量赋值后该变量才会被创建。变量没有类型，而通常所说的“类型”指内存中对象的类型。
python3中有六个标准的数据类型：
Numbers（数字）
String（字符串）
List（列表）
Tuple（元组）
Sets（集合）
Dictionaries（字典）
```

## Numbers(数字)

```py
python3支持int(整数)、float(浮点)、bool(布尔)、complex(复数)

注意：
python可以同时为多个变量赋值，如：a,b=1,2
一个变量可以通过赋值指向不同类型的对象
数值的除法总是会返回一个浮点数，要获取整数使用//操作符
在混合计算时，Python会把整数类型转换为浮点数
```

## String(字符串)

```py
python中字符串str用单引号或双引号栝起来，同时使用反斜杠\转义特殊字符
若不想让反斜杠产生转义效果，在字符串前添加一个r，表示原始字符串
python中的字符串有两种索引方式，第一种是从左往右依次从0开始递增，第二种是从右往左依次从-1开始递减
注意：没有单独的字符类型，一个字符就是长度为1的字符串
还可以对字符串进行切片，获取一段字符串，形式为变量[头下标:尾下标]
截取范围是前闭后开[2:]，并且两个索引都可以省略
python字符串不能被改变，如向一个索引位置赋值，word[0]='m'会导致错误

注意：
1、反斜杠可以用来转义，使用r可以让反斜杠不发生转义。
2、字符串可以用+运算符连接在一起，用*运算符重复。
3、Python中的字符串有两种索引方式，从左往右以0开始，从右往左以-1开始。
4、Python中的字符串不能改变。
```

## List(列表)

```py
列表是Python中使用最频繁的数据类型
列表是写在方括号之间，用逗号隔开的元素列表，列表中元素的类型可以不同
如：a = [1, 'a']
和字符串一样，列表也可以被索引和切片。列表被切片后返回一个包含所需元素的新列表
列表还支持串联操作，使用+符号
如：
a = [1, 2, 3]
a + [4, 5, 6]
>>> a = [1, 2, 3, 4, 5, 6]
>>> a[0] = 9
>>> a[2:5] = [13, 14, 15]
>>> a
[9, 2, 13, 14, 15, 6]
>>> a[2:5] = []   # 删除
>>> a
[9, 2, 6]
List内置了有很多方法，例如append()、pop()等等，这在后面会讲到。

注意：

1、List写在方括号之间，元素用逗号隔开。
2、和字符串一样，list可以被索引和切片。
3、List可以使用+操作符进行拼接。
4、List中的元素是可以改变的。
```

## Tuple（元组）

```py
元组（tuple）与列表类似，不同之处在于元组的元素不能修改。元组写在小括号里，元素之间用逗号隔开。

元组中的元素类型也可以不相同：

>>> a = (1991, 2014, 'physics', 'math')
>>> print(a, type(a), len(a))
(1991, 2014, 'physics', 'math') <class 'tuple'> 4
```

```py
元组与字符串类似，可以被索引且下标索引从0开始，也可以进行截取/切片（看上面，这里不再赘述）。

其实，可以把字符串看作一种特殊的元组。

>>> tup = (1, 2, 3, 4, 5, 6)
>>> print(tup[0], tup[1:5])
1 (2, 3, 4, 5)
>>> tup[0] = 11  # 修改元组元素的操作是非法的
虽然tuple的元素不可改变，但它可以包含可变的对象，比如list列表。

构造包含0个或1个元素的tuple是个特殊的问题，所以有一些额外的语法规则：

tup1 = () # 空元组
tup2 = (20,) # 一个元素，需要在元素后添加逗号
另外，元组也支持用+操作符：

>>> tup1, tup2 = (1, 2, 3), (4, 5, 6)
>>> print(tup1+tup2)
(1, 2, 3, 4, 5, 6)
string、list和tuple都属于sequence（序列）。

注意：

1、与字符串一样，元组的元素不能修改。
2、元组也可以被索引和切片，方法一样。
3、注意构造包含0或1个元素的元组的特殊语法规则。
4、元组也可以使用+操作符进行拼接。
```

## Sets（集合）

```py
集合（set）是一个无序不重复元素的集。

基本功能是进行成员关系测试和消除重复元素。

可以使用大括号 或者 set()函数创建set集合，注意：创建一个空集合必须用 set() 而不是 { }，因为{ }是用来创建一个空字典。

>>> student = {'Tom', 'Jim', 'Mary', 'Tom', 'Jack', 'Rose'}
>>> print(student)   # 重复的元素被自动去掉
{'Jim', 'Jack', 'Mary', 'Tom', 'Rose'}
>>> 'Rose' in student  # membership testing（成员测试）
True
>>> # set可以进行集合运算
>>> a = set('abracadabra')
>>> b = set('alacazam')
>>> a
{'a', 'b', 'c', 'd', 'r'}
>>> a - b     # a和b的差集
{'b', 'd', 'r'}
>>> a | b     # a和b的并集
{'l', 'm', 'a', 'b', 'c', 'd', 'z', 'r'}
>>> a & b     # a和b的交集
{'a', 'c'}
>>> a ^ b     # a和b中不同时存在的元素
{'l', 'm', 'b', 'd', 'z', 'r'}

```

## Dictionaries（字典）

```py
字典（dictionary）是Python中另一个非常有用的内置数据类型。

字典是一种映射类型（mapping type），它是一个无序的键 : 值对集合。

关键字必须使用不可变类型，也就是说list和包含可变类型的tuple不能做关键字。

在同一个字典中，关键字还必须互不相同。

>>> dic = {}  # 创建空字典
>>> tel = {'Jack':1557, 'Tom':1320, 'Rose':1886}
>>> tel
{'Tom': 1320, 'Jack': 1557, 'Rose': 1886}
>>> tel['Jack']   # 主要的操作：通过key查询
1557
>>> del tel['Rose']  # 删除一个键值对
>>> tel['Mary'] = 4127  # 添加一个键值对
>>> tel
{'Tom': 1320, 'Jack': 1557, 'Mary': 4127}
>>> list(tel.keys())  # 返回所有key组成的list
['Tom', 'Jack', 'Mary']
>>> sorted(tel.keys()) # 按key排序
['Jack', 'Mary', 'Tom']
>>> 'Tom' in tel       # 成员测试
True
>>> 'Mary' not in tel  # 成员测试
False
构造函数 dict() 直接从键值对sequence中构建字典，当然也可以进行推导，如下：

>>> dict([('sape', 4139), ('guido', 4127), ('jack', 4098)])
{'jack': 4098, 'sape': 4139, 'guido': 4127}

>>> {x: x**2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}

>>> dict(sape=4139, guido=4127, jack=4098)
{'jack': 4098, 'sape': 4139, 'guido': 4127}
另外，字典类型也有一些内置的函数，例如clear()、keys()、values()等。

注意：

1、字典是一种映射类型，它的元素是键值对。
2、字典的关键字必须为不可变类型，且不能重复。
3、创建空字典使用{ }。

```

## python运算符

以下假设变量a为10，变量b为21：
| 运算符 | 描述 | 实例 |
| :------: | :------: | :------: |
|运算符|描述|实例|
|+|加 - 两个对象相加|a + b 输出结果 31|
|-|减 - 得到负数或是一个数减去另一个数|a - b 输出结果 -11|
|*|乘 - 两个数相乘或是返回一个被重复若干次的字符串|a * b 输出结果 210|
|/|除 - x 除以 y|b / a 输出结果 2.1|
|%|取模 - 返回除法的余数|b % a 输出结果 1|
|**|幂 - 返回x的y次幂|a**b 为10的21次方|
|//|取整除 - 返回商的整数部分|9//2 输出结果 4 , 9.0//2.0 输出结果 4.0|

## python比较符

以下假设变量a为10，变量b为20：

| 运算符 | 描述 | 实例 |
| :------: | :------: | :------: |
|==|等于 - 比较对象是否相等|(a == b) 返回 False。|
|!=|不等于 - 比较两个对象是否不相等|(a != b) 返回 True.|
|>|大于 - 返回x是否大于y|(a > b) 返回 False。|
|<|小于 - 返回x是否小于y。所有比较运算符返回1表示真，返回0表示假。这分别与特殊的变量True和False等价。注意，这些变量名的大写。|(a < b) 返回 True。|
|>=|大于等于 - 返回x是否大于等于y。|(a >= b) 返回 False。|
|<=|小于等于 - 返回x是否小于等于y。|(a <= b) 返回 True。|

## python赋值运算符

以下假设变量a为10，变量b为20：

| 运算符 | 描述 | 实例 |
| :------: | :------: | :------: |
|=|简单的赋值运算符|c = a + b 将 a + b 的运算结果赋值为 c|
|=+=|加法赋值运算符|c += a 等效于 c = c + a|
|-=|减法赋值运算符|c -= a 等效于 c = c - a|
|*=|乘法赋值运算符|c *= a 等效于 c = c* a|
|/=|除法赋值运算符|c /= a 等效于 c = c / a|
|%=|取模赋值运算符|c %= a 等效于 c = c % a|
|**=|幂赋值运算符|c **= a 等效于 c = c** a|
|//=|取整除赋值运算符|c //= a 等效于 c = c // a|

## Python位运算符

按位运算符是把数字看作二进制来进行计算的。Python中的按位运算法则如下：

下表中变量 a 为 60，b 为 13。

|运算符|描述|实例|
| :------: | :------: | :------: |
|&|按位与运算符：参与运算的两个值,如果两个相应位都为1,则该位的结果为1,否则为0|(a & b) 输出结果 12 ，二进制解释： 0000 1100|
|||按位或运算符：只要对应的二个二进位有一个为1时，结果位就为1。|(a | b) 输出结果 61 ，二进制解释： 0011 1101|
|^|按位异或运算符：当两对应的二进位相异时，结果为1|(a ^ b) 输出结果 49 ，二进制解释： 0011 0001|
|~|按位取反运算符：对数据的每个二进制位取反,即把1变为0,把0变为1|(~a ) 输出结果 -61 ，二进制解释： 1100 0011， 在一个有符号二进制数的补码形式。|
|<<|左移动运算符：运算数的各二进位全部左移若干位，由"<<"右边的数指定移动的位数，高位丢弃，低位补0。|a << 2 输出结果 240 ，二进制解释： 1111 0000|
|>>|右移动运算符：把">>"左边的运算数的各二进位全部右移若干位，">>"右边的数指定移动的位数|a >> 2 输出结果 15 ，二进制解释： 0000 1111|

## Python逻辑运算符

Python语言支持逻辑运算符，以下假设变量 a 为 10, b为 20:

|运算符|逻辑表达式|描述|实例|
| :------: | :------: | :------: | :------: |
|and|x and y|布尔"与" - 如果 x 为 False，x and y 返回 False，否则它返回 y 的计算值。|(a and b) 返回 20。|
|or|x or y|布尔"或" - 如果 x 是 True，它返回 x的值，否则它返回 y 的计算值。|(a or b) 返回 10。|
|not|not x|布尔"非" - 如果 x 为 True，返回 False 。如果 x 为 False，它返回 True。|not(a and b) 返回 False|

## Python成员运算符

除了以上的一些运算符之外，Python还支持成员运算符，测试实例中包含了一系列的成员，包括字符串，列表或元组。
|运算符|描述|实例|
| :------: | :------: | :------: |
|in|如果在指定的序列中找到值返回 True，否则返回 False。|x 在 y 序列中 , 如果 x 在 y 序列中返回 True。|
|not in|如果在指定的序列中没有找到值返回 True，否则返回 False。|x 不在 y 序列中 , 如果 x 不在 y 序列中返回 True。|
|not|not x|布尔"非" - 如果 x 为 True，返回 False 。如果 x 为 False，它返回 True。|

## Python身份运算符

身份运算符用于比较两个对象的存储单元
|运算符|描述|实例|
| :------: | :------: | :------: |
|is|is是判断两个标识符是不是引用自一个对象|x is y, 如果 id(x) 等于 id(y) , is 返回结果 1|
|is not|is not是判断两个标识符是不是引用自不同对象|x is not y, 如果 id(x) 不等于 id(y). is not 返回结果 1|

## Python运算符优先级

以下表格列出了从最高到最低优先级的所有运算符：
|运算符|描述|
| :------: | :------: |
|**|指数 (最高优先级)|
|~ + -|按位翻转, 一元加号和减号 (最后两个的方法名为 +@ 和 -@)|
|* / % //|乘，除，取模和取整除|
|=+ -|加法减法|
|>> <<|右移，左移运算符|
|&|位 'AND'|
|^ ||位运算符|
|<= < > >=|比较运算符|
|<> == !=|等于运算符|
|= %= /= //= -= += *= **=|赋值运算符|
|is is not|身份运算符|
|in not in|成员运算符|
|not or and|逻辑运算符|

> 以下实例演示了Python所有运算符优先级的操作：

```py
#!/usr/bin/python3
#coding=utf-8
a = 20
b = 10
c = 15
d = 5
e = 0

e = (a + b) * c / d       #( 30 * 15 ) / 5
print ("(a + b) * c / d 运算结果为：",  e)

e = ((a + b) * c) / d     # (30 * 15 ) / 5
print ("((a + b) * c) / d 运算结果为：",  e)

e = (a + b) * (c / d);    # (30) * (15/5)
print ("(a + b) * (c / d) 运算结果为：",  e)

e = a + (b * c) / d;      #  20 + (150/5)
print ("a + (b * c) / d 运算结果为：",  e)

# 以上实例输出结果：

(a + b) * c / d 运算结果为： 90.0
((a + b) * c) / d 运算结果为： 90.0
(a + b) * (c / d) 运算结果为： 90.0
a + (b * c) / d 运算结果为： 50.0
```

## 元组内置函数

Python元组包含以下内置函数

```py
len(tuple)  计算元组元素个数。

>>> tuple1 = ('Google', 'W3CSchool', 'Taobao')
>>> len(tuple1)
3

max(tuple)  返回元组中元素最大值。
>>> tuple2 = ('5', '4', '8')
>>> max(tuple2)
'8'

min(tuple)  返回元组中元素最小值。
>>> tuple2 = ('5', '4', '8')
>>> min(tuple2)
'4'

tuple(seq)  将列表转换为元组。
>>> list1= ['Google', 'Taobao', 'W3CSchool', 'Baidu']
>>> tuple1=tuple(list1)
>>> tuple1
('Google', 'Taobao', 'W3CSchool', 'Baidu')
```

## Python字典

```py
字典是另一种可变容器模型，且可存储任意类型的对象
格式：
dict = {'key1': 'value1', 'key2': 'value2', 'key3': 245}
键必须是唯一的，但值则不必,值可以取任何数据类型，但键必须是不可变的，如字符串，数字或元组。

字典内置函数和方法

en(dict)    计算字典元素个数，即键的总数。
>>> dict = {'Name': 'W3CSchool', 'Age': 7, 'Class': 'First'}
>>> len(dict)
3

str(dict)   输出字典以可打印的字符串表示。
>>> dict = {'Name': 'W3CSchool', 'Age': 7, 'Class': 'First'}
>>> str(dict)
"{'Name': 'W3CSchool', 'Class': 'First', 'Age': 7}"

type(variable)  返回输入的变量类型，如果变量是字典就返回字典类型。
>>> dict = {'Name': 'W3CSchool', 'Age': 7, 'Class': 'First'}
>>> type(dict)
<class 'dict'>

```

python字典包含以下内置方法：
| 方法 | 描述 |
| :------: | :------: |
|radiansdict.clear()|删除字典内所有元素|
|radiansdict.copy()|返回一个字典的浅复制|
|radiansdict.fromkeys()|创建一个新字典，以序列seq中元素做字典的键，val为字典所有键对应的初始值|
|radiansdict.get(key, default=None)|返回指定键的值，如果值不在字典中返回default值|
|key in dict|如果键在字典dict里返回true，否则返回false|
|radiansdict.items()|以列表返回可遍历的(键, 值) 元组数组|
|radiansdict.keys()|以列表返回一个字典所有的键|
|radiansdict.setdefault(key, default=None)|和get()类似, 但如果键不存在于字典中，将会添加键并将值设为default|
|radiansdict.update(dict2)|把字典dict2的键/值对更新到dict里|
|radiansdict.values()|以列表返回字典中的所有值|

## Python条件控制语句

```py
if语句
if condition_1:
    statement_block_1
elif condition_2:
    statement_block_2
else:
    statement_block_3

注意
1、每个条件后面要使用冒号（:），表示接下来是满足条件后要执行的语句块。
2、使用缩进来划分语句块，相同缩进数的语句在一起组成一个语句块。
3、在Python中没有switch – case语句。
```

实例演示狗的年龄计算判断

```py


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
|‘==|等于，比较对象是否相等|
|!=|不等于|

演示猜数字

```py
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

```py
python循环语句有for和while
# 使用while循环计算1~100和
#!/usr/local/bin/python3
# -*- coding: UTF-8 -*-

count = 1
sum = 0

while count <= 100:
    sum += count
    count += 1
print("1~100和是:", (sum))

for语句
for循环可以遍历任何序列的项目，如列表或字符串
for实例使用break语句，break语句用于跳出当前循环体

foods = ["apple", "beef", "fish"]
for egg in foods:
    if egg == "beef":
        print("两个不同的")
        break   # 此处跳出循环，下一句不再输出
    print("好吃的" + egg)
else:
    print("没有beef了")
print("结束选餐")

range()函数，如果需要遍历数字序列，可以使用内置range()函数，可以自动生成数列
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

## 迭代器和生成器

```py
迭代是访问集合元素的一种方式，迭代器可以记住遍历位置的对象。迭代器对象从集合的第一个元素开始访问，知道所有元素结束，迭代器只能往前，迭代器有两个方法iter()和next()。字符串、列表和元组对象都可用于创建迭代器

list = [1, 2, 3, 4]  # 创建列表list
it = iter(list)  # 创建迭代器对象it
print(next(it))  # 输出迭代器下一个元素

迭代器对象可以用for语句进行遍历
list = [1, 2, 3, 4]  # 创建列表list
it = iter(list)  # 创建迭代器对象it
for x in it:
    print(x, end=" ")

使用了yield的函数称为生成器，生成器是一个返回迭代器的函数，简单的理解就是生成器就是一个迭代器。在调用生成器运行的过程中，每次遇到yield函数会暂停并保存当前所有的运行信息，返回yield的值，并在下一次next()方法从当前位置继续运行。
# 使用生成器生成斐波那契数列,斐波那契数列：即两个元素的和确定下一个数
import sys
def fibonacci(x):
    a, b, count = 0, 1, 0
    while True:
        if (count > x):
            return
        yield a
        a, b = b, a + b
        count += 1
f = fibonacci(21)   # f是一个迭代器，由生成器返回生成

while True:
    try:
        print(next(f), end="")
    except StopIteration:
        sys.exit()

```

## Python相关说明

```py
# 库：相关功能的集合
    ##
    ## 函数：
    ##    定义：
    ##
    ##    def 函数名（参数1，参数2……）：
    ##        语句块
    ##        return 返回值
    ##
    ##    调用：
    ##        函数名（参数1，参数2……）
    ##        变量名 = 函数名（参数1，参数2……）
    ##
    ## 类：
    ##    定义：
    ##    class 类名()：
    ##        def __init__(self,参数……):
    ##        def 函数名1(self,参数……):
    ##        def 函数名2(self,参数……):
    ##
    ##    调用：
    ##        实例名 = 类名()
    ##        实例名.方法名(参数)
    ##

# 框架：控制反转

# 模块：具有显式导出和导入的抽象接口，实现和接口是分开的，可能有多个实现并且实现是隐藏的
    #
    #    以下划线开头的标识符是有特殊意义的。
    #    •以单下划线开头（_foo）的代表不能直接访问的类属性，需通过类提供的接口进行访    问；
    #    •以双下划线开头的（__foo）代表类的私有成员；
    #    •以双下划线开头和结尾的（__foo__）代表python里特殊方法专用的标识，如   __init__（）代表类的构造函数。

    # 模块（modules）：
    #    模块(module)是python最高级别的程序组织单位。它可以打包程序代码和数据以备重 用。
    #    模块采用python程序的文件形式（或者C扩展程序的形式）存储，客户导入模块并对使用他们定义的名字。
```

## 函数

```py
函数基本格式:
def 函数名(参数列表)
    函数体
# 计算四方形面积
def area(width, height):
    return width * height

w = 345
h = 2345
print("四方形面积是：", area(w, h))

函数中变量的作用域
x = 4  # 创建一个全局变量x。赋值为4


def func1():
    x = 21  # 创建一个全局变量x。赋值为21
    print("在函数1中x值为", x)


def func2():
    print("在函数2中x值为", x)


func1()
func2()
print(x)

关键字参数，函数也可以使用kwarg=value的关键字参数形式被调用。
Python的函数的返回值使用return语句，可以将函数作为一个值赋值给指定变量,可返回空值
可变参数列表：一个最不常用的选择是可以让函数调用可变个数的参数.这些参数被包装进一个元组(查看元组和序列).在这些可变个数的参数之前,可以有零到多个普通的参数:
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
```

## 数据结构

## 示例列表的大部分方法

|方法|描述|
| :------: | :------: |
|list.append(x)| 把一个元素添加到列表的结尾，相当于 a[len(a):] = [x]|
|list.extend(L)|通过添加指定列表的所有元素来扩充列表，相当于 a[len(a):] = L|
|list.insert(i, x)|在指定位置插入一个元素。第一个参数是准备插入到其前面的那个元素的索引，例如 a.insert(0, x) 会插入到整个列表之前，而 a.insert(len(a), x) 相当于 a.append(x) |
|list.remove(x)|删除列表中值为 x 的第一个元素。如果没有这样的元素，就会返回一个错误|
|list.pop([i])|从列表的指定位置删除元素，并将其返回。如果没有指定索引，a.pop()返回最后一个元素。元素随即从列表中被删除。（方法中 i 两边的方括号表示这个参数是可选的，而不是要求你输入一对方括号，你会经常在 Python 库参考手册中遇到这样的标记|
|list.clear()|移除列表中的所有项，等于del a[:]|
|list.index(x)|返回列表中第一个值为 x 的元素的索引。如果没有匹配的元素就会返回一个错误|
|list.count(x)|返回 x 在列表中出现的次数|
|list.sort()|对列表中的元素进行排序|
|list.reverse()|倒排列表中的元素|
|list.copy()|返回列表的浅复制，等于a[:]|

## 将列表当做堆栈使用

列表方法使得列表可以很方便的作为一个堆栈来使用，堆栈作为特定的数据结构，最先进入的元素最后一个被释放（后进先出）。用 append() 方法可以把一个元素添加到堆栈顶。用不指定索引的 pop() 方法可以把一个元素从堆栈顶释放出来。例如：

## 将列表当作队列使用

也可以把列表当做队列用，只是在队列里第一加入的元素，第一个取出来；但是拿列表用作这样的目的效率不高。在列表的最后添加或者弹出元素速度快，然而在列表里插入或者从头部弹出速度却不快（因为所有其他的元素都得一个一个地移动）

## 列表推导式

列表推导式提供了从序列创建列表的简单途径。通常应用程序将一些操作应用于某个序列的每个元素，用其获得的结果作为生成新列表的元素，或者根据确定的判定条件创建子序列。
每个列表推导式都在 for 之后跟一个表达式，然后有零到多个 for 或 if 子句。返回结果是一个根据表达从其后的 for 和 if 上下文环境中生成出来的列表。如果希望表达式推导出一个元组，就必须使用括号。
这里我们将列表中每个数值乘三，获得一个新的列表：

## 嵌套列表解析

## del 语句

使用 del 语句可以从一个列表中依索引而不是值来删除一个元素。这与使用 pop() 返回一个值不同。可以用 del 语句从列表中删除一个切割，或清空整个列表（我们以前介绍的方法是给该切割赋一个空列表）

## 元组和序列

元组由若干逗号分隔的值组成

## 集合

集合是一个无序不重复元素的集。基本功能包括关系测试和消除重复元素。
可以用大括号({})创建集合。注意：如果要创建一个空集合，你必须用 set() 而不是 {} ；后者创建一个空的字典，下一节我们会介绍这个数据结构，集合也支持推导式

## 字典

另一个非常有用的 Python 内建数据类型是字典。
序列是以连续的整数为索引，与此不同的是，字典以关键字为索引，关键字可以是任意不可变类型，通常用字符串或数值。
理解字典的最佳方式是把它看做无序的键=>值对集合。在同一个字典之内，关键字必须是互不相同。
一对大括号创建一个空的字典：{}
构造函数 dict() 直接从键值对元组列表中构建字典。如果有固定的模式，列表推导式指定特定的键值对
此外，字典推导可以用来创建任意键和值的表达式词典。如果关键字只是简单的字符串，使用关键字参数指定键值对有时候更方便

## 遍历技巧

在字典中遍历时，关键字和对应的值可以使用 items() 方法同时解读出来

在序列中遍历时，索引位置和对应值可以使用 enumerate() 函数同时得到：

同时遍历两个或更多的序列，可以使用 zip() 组合：

要反向遍历一个序列，首先指定这个序列，然后调用 reversesd() 函数：

要按顺序遍历一个序列，使用 sorted() 函数返回一个已排序的序列，并不修改原值：

## python3模块

通俗的讲，将自行定义或已经存在的方法和变量存放在一个文件中，为一些脚步或交互式解释器实例使用，这个文件被称之为模块
模块是一个包含所有你定义的函数和变量的文件，其后缀名是.py。模块可以被别的程序引入，以使用该模块中的函数等功能。这也是使用python标准库的方法。下面是一个使用python标准库中模块的例子

```py
import sys

print("命令参数如下：")
for i in sys.argv:
    print(i)

print("\n\nPython3的路径为：", sys.path, '\n')

import语句
一个模块只会被导入一次，不管你执行了多少次import。这样可以防止导入模块被一遍又一遍地执行。
当我们使用import语句的时候，Python解释器是怎样找到对应的文件的呢？
这就涉及到Python的搜索路径，搜索路径是由一系列目录名组成的，Python解释器就依次从这些目录中去寻找所引入的模块。
这看起来很像环境变量，事实上，也可以通过定义环境变量的方式来确定搜索路径。
搜索路径是在Python编译或安装的时候确定的，安装新的库应该也会修改。搜索路径被存储在sys模块中的path变量
sys.path输出是一个列表，其中第一项是空串''，代表当前目录（若是从一个脚本中打印出来的话，可以更清楚地看出是哪个目录），亦即我们执行python解释器的目录（对于脚本的话就是运行的脚本所在的目录）。
因此若像我一样在当前目录下存在与要引入模块同名的文件，就会把要引入的模块屏蔽掉。
了解了搜索路径的概念，就可以在脚本中修改sys.path来引入一些不在搜索路径中的模块

__name__属性
一个模块被另一个程序第一次引入时，其主程序将运行。如果我们想在模块被引入时，模块中的某一程序块不执行，我们可以用__name__属性来使该程序块仅在该模块自身运行时执行
if __name__ == '__main__':
 print('程序自身在运行')
else:
 print('我来自另一模块')

每个模块都有一个__name__属性，当其值是'__main__'时，表明该模块自身在运行，否则是被引入

dir() 函数
内置的函数 dir() 可以找到模块内定义的所有名称。以一个字符串列表的形式返回

标准模块
Python 本身带着一些标准的模块库，在 Python 库参考文档中将会介绍到（就是后面的"库参考文档"）。
有些模块直接被构建在解析器里，这些虽然不是一些语言内置的功能，但是他却能很高效的使用，甚至是系统级调用也没问题。
这些组件会根据不同的操作系统进行不同形式的配置，比如 winreg 这个模块就只会提供给 Windows 系统。
应该注意到这有一个特别的模块 sys ，它内置在每一个 Python 解析器中。变量 sys.ps1 和 sys.ps2 定义了主提示符和副提示符所对应的字符串:

包
包是一种管理 Python 模块命名空间的形式，采用"点模块名称"。
比如一个模块的名称是 A.B， 那么他表示一个包 A中的子模块 B 。
就好像使用模块的时候，你不用担心不同模块之间的全局变量相互影响一样，采用点模块名称这种形式也不用担心不同库之间的模块重名的情况。
这样不同的作者都可以提供 NumPy 模块，或者是 Python 图形库。
不妨假设你想设计一套统一处理声音文件和数据的模块（或者称之为一个"包"）。
现存很多种不同的音频文件格式（基本上都是通过后缀名区分的，例如： .wav，:file:.aiff，:file:.au，），所以你需要有一组不断增加的模块，用来在不同的格式之间转换。
并且针对这些音频数据，还有很多不同的操作（比如混音，添加回声，增加均衡器功能，创建人造立体声效果），所以你还需要一组怎么也写不完的模块来处理这些操作。
这里给出了一种可能的包结构（在分层的文件系统中）:
sound/                          顶层包
      __init__.py               初始化 sound 包
      formats/                  文件格式转换子包
              __init__.py
              wavread.py
              wavwrite.py
              aiffread.py
              aiffwrite.py
              auread.py
              auwrite.py
              ...
      effects/                  声音效果子包
              __init__.py
              echo.py
              surround.py
              reverse.py
              ...
      filters/                  filters 子包
              __init__.py
              equalizer.py
              vocoder.py
              karaoke.py
              ...
在导入一个包的时候，Python 会根据 sys.path 中的目录来寻找这个包中包含的子目录。

目录只有包含一个叫做 __init__.py 的文件才会被认作是一个包，主要是为了避免一些滥俗的名字（比如叫做 string）不小心的影响搜索路径中的有效模块。

最简单的情况，放一个空的 :file:__init__.py就可以了。当然这个文件中也可以包含一些初始化代码或者为（将在后面介绍的） __all__变量赋值。

用户可以每次只导入一个包里面的特定模块，比如:

import sound.effects.echo
这将会导入子模块:mod:song.effects.echo。 他必须使用全名去访问:

sound.effects.echo.echofilter(input, output, delay=0.7, atten=4)
还有一种导入子模块的方法是：

from sound.effects import echo
这同样会导入子模块 echo ，并且他不需要那些冗长的前缀，所以他可以这样使用：

echo.echofilter(input, output, delay=0.7, atten=4)
还有一种变化就是直接导入一个函数或者变量：

from sound.effects.echo import echofilter
同样的，这种方法会导入子模块 echo ，并且可以直接使用他的 echofilter() 函数：

echofilter(input, output, delay=0.7, atten=4)

注意当使用from package import item这种形式的时候，对应的item既可以是包里面的子模块（子包），或者包里面定义的其他名称，比如函数，类或者变量。

import语法会首先把item当作一个包定义的名称，如果没找到，再试图按照一个模块去导入。如果还没找到，恭喜，一个:exc:ImportError 异常被抛出了。

反之，如果使用形如import item.subitem.subsubitem这种导入形式，除了最后一项，都必须是包，而最后一项则可以是模块或者是包，但是不可以是类，函数或者变量的名字。
```

python3输入和输出

```py
输出格式化
Python两种输出值的方式: 表达式语句和 print() 函数。
第三种方式是使用文件对象的 write() 方法，标准输出文件可以用 sys.stdout 引用。
如果你希望输出的形式更加多样，可以使用 str.format() 函数来格式化输出值。
如果你希望将输出的值转成字符串，可以使用 repr() 或 str() 函数来实现。
str()： 函数返回一个用户易读的表达形式。
repr()： 产生一个解释器易读的表达形式。

读取键盘输入
python提供了input()内置函数从标准输入读取一行文本，默认标准输入是键盘
str = input("输入："):
print(str)

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

python3面向对象

```py
类(Class): 用来描述具有相同的属性和方法的对象的集合。它定义了该集合中每个对象所共有的属性和方法。对象是类的实例。
方法：类中定义的函数。
类变量：类变量在整个实例化的对象中是公用的。类变量定义在类中且在函数体之外。类变量通常不作为实例变量使用。
数据成员：类变量或者实例变量用于处理类及其实例对象的相关的数据。
方法重写：如果从父类继承的方法不能满足子类的需求，可以对其进行改写，这个过程叫方法的覆盖（override），也称为方法的重写。
实例变量：定义在方法中的变量，只作用于当前实例的类。
继承：即一个派生类（derived class）继承基类（base class）的字段和方法。继承也允许把一个派生类的对象作为一个基类对象对待。例如，有这样一个设计：一个Dog类型的对象派生自Animal类，这是模拟"是一个（is-a）"关系（例图，Dog是一个Animal）。
实例化：创建一个类的实例，类的具体对象。
对象：通过类定义的数据结构实例。对象包括两个数据成员（类变量和实例变量）和方法。

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
```

```py
# ###################################################
# # 1. 基本数据类型和运算
# ###################################################

# You have numbers
3  # => 3

# Math is what you would expect
1 + 1   # => 2
8 - 1   # => 7
10 * 2  # => 20
35 / 5  # => 7.0

# Result of integer division truncated down both for positive and negative.
5 // 3       # => 1
5.0 // 3.0   # => 1.0 # works on floats too
-5 // 3      # => -2
-5.0 // 3.0  # => -2.0

# The result of division is always a float
10.0 / 3  # => 3.3333333333333335

# Modulo operation
7 % 3  # => 1

# Exponentiation (x**y, x to the yth power)
2**3  # => 8

# Enforce precedence with parentheses
(1 + 3) * 2  # => 8

# Boolean values are primitives (Note: the capitalization)
True
False

# negate with not
not True   # => False
not False  # => True

# Boolean Operators
# Note "and" and "or" are case-sensitive
True and False  # => False
False or True   # => True
'''
# Note using Bool operators with ints
# False is 0 and True is 1
# Don't mix up with bool(ints) and bitwise and/or (&,|)
0 and 2     # => 0
-5 or 0     # => -5
0 == False  # => True
2 == True   # => False
1 == True   # => True
-5 != False != True #=> True
'''
# Equality is ==
1 == 1  # => True
2 == 1  # => False

# Inequality is !=
1 != 1  # => False
2 != 1  # => True

# More comparisons
1 < 10  # => True
1 > 10  # => False
2 <= 2  # => True
2 >= 2  # => True

# Comparisons can be chained!
1 < 2 < 3  # => True
2 < 3 < 2  # => False

# (is vs. ==) is checks if two variables refer to the same object, but == checks
# if the objects pointed to have the same values.
a = [1, 2, 3, 4]  # Point a at a new list, [1, 2, 3, 4]
b = a             # Point b at what a is pointing to
b is a            # => True, a and b refer to the same object
b == a            # => True, a's and b's objects are equal
b = [1, 2, 3, 4]  # Point b at a new list, [1, 2, 3, 4]
b is a            # => False, a and b do not refer to the same object
b == a            # => True, a's and b's objects are equal

# Strings are created with " or '
"This is a string."
'This is also a string.'

# Strings can be added too! But try not to do this.
"Hello " + "world!"  # => "Hello world!"
# String literals (but not variables) can be concatenated without using '+'
"Hello " "world!"    # => "Hello world!"

# A string can be treated like a list of characters
"This is a string"[0]  # => 'T'

# You can find the length of a string
len("This is a string")  # => 16

# .format can be used to format strings, like this:
"{} can be {}".format("Strings", "interpolated")  # => "Strings can be interpolated"

# You can repeat the formatting arguments to save some typing.
"{0} be nimble, {0} be quick, {0} jump over the {1}".format("Jack", "candle stick")
# => "Jack be nimble, Jack be quick, Jack jump over the candle stick"

# You can use keywords if you don't want to count.
"{name} wants to eat {food}".format(name="Bob", food="lasagna")  # => "Bob wants to eat lasagna"

# If your Python 3 code also needs to run on Python 2.5 and below, you can also
# still use the old style of formatting:
"%s can be %s the %s way" % ("Strings", "interpolated", "old")  # => "Strings can be interpolated the old way"


# None is an object
None  # => None

# Don't use the equality "==" symbol to compare objects to None
# Use "is" instead. This checks for equality of object identity.
"etc" is None  # => False
None is None   # => True

# None, 0, and empty strings/lists/dicts/tuples all evaluate to False.
# All other values are True
bool(0)   # => False
bool("")  # => False
bool([])  # => False
bool({})  # => False
bool(())  # => False

####################################################
## 2. Variables and Collections
####################################################

# Python has a print function
print("I'm Python. Nice to meet you!")  # => I'm Python. Nice to meet you!

# By default the print function also prints out a newline at the end.
# Use the optional argument end to change the end string.
print("Hello, World", end="!")  # => Hello, World!

# Simple way to get input data from console
input_string_var = input("Enter some data: ") # Returns the data as a string
# Note: In earlier versions of Python, input() method was named as raw_input()

# There are no declarations, only assignments.
# Convention is to use lower_case_with_underscores
some_var = 5
some_var  # => 5

# Accessing a previously unassigned variable is an exception.
# See Control Flow to learn more about exception handling.
some_unknown_var  # Raises a NameError

# if can be used as an expression
# Equivalent of C's '?:' ternary operator
"yahoo!" if 3 > 2 else 2  # => "yahoo!"

# Lists store sequences
li = []
# You can start with a prefilled list
other_li = [4, 5, 6]

# Add stuff to the end of a list with append
li.append(1)    # li is now [1]
li.append(2)    # li is now [1, 2]
li.append(4)    # li is now [1, 2, 4]
li.append(3)    # li is now [1, 2, 4, 3]
# Remove from the end with pop
li.pop()        # => 3 and li is now [1, 2, 4]
# Let's put it back
li.append(3)    # li is now [1, 2, 4, 3] again.

# Access a list like you would any array
li[0]   # => 1
# Look at the last element
li[-1]  # => 3

# Looking out of bounds is an IndexError
li[4]  # Raises an IndexError

# You can look at ranges with slice syntax.
# The start index is included, the end index is not
# (It's a closed/open range for you mathy types.)
li[1:3]   # => [2, 4]
# Omit the end
li[2:]    # => [4, 3]
# Omit the beginning
li[:3]    # => [1, 2, 4]
# Select every second entry
li[::2]   # =>[1, 4]
# Return a reversed copy of the list
li[::-1]  # => [3, 4, 2, 1]
# Use any combination of these to make advanced slices
# li[start:end:step]

# Make a one layer deep copy using slices
li2 = li[:]  # => li2 = [1, 2, 4, 3] but (li2 is li) will result in false.

# Remove arbitrary elements from a list with "del"
del li[2]  # li is now [1, 2, 3]

# Remove first occurrence of a value
li.remove(2)  # li is now [1, 3]
li.remove(2)  # Raises a ValueError as 2 is not in the list

# Insert an element at a specific index
li.insert(1, 2)  # li is now [1, 2, 3] again

# Get the index of the first item found matching the argument
li.index(2)  # => 1
li.index(4)  # Raises a ValueError as 4 is not in the list

# You can add lists
# Note: values for li and for other_li are not modified.
li + other_li  # => [1, 2, 3, 4, 5, 6]

# Concatenate lists with "extend()"
li.extend(other_li)  # Now li is [1, 2, 3, 4, 5, 6]

# Check for existence in a list with "in"
1 in li  # => True

# Examine the length with "len()"
len(li)  # => 6


# Tuples are like lists but are immutable.
tup = (1, 2, 3)
tup[0]      # => 1
tup[0] = 3  # Raises a TypeError

# Note that a tuple of length one has to have a comma after the last element but
# tuples of other lengths, even zero, do not.
type((1))   # => <class 'int'>
type((1,))  # => <class 'tuple'>
type(())    # => <class 'tuple'>

# You can do most of the list operations on tuples too
len(tup)         # => 3
tup + (4, 5, 6)  # => (1, 2, 3, 4, 5, 6)
tup[:2]          # => (1, 2)
2 in tup         # => True

# You can unpack tuples (or lists) into variables
a, b, c = (1, 2, 3)  # a is now 1, b is now 2 and c is now 3
# You can also do extended unpacking
a, *b, c = (1, 2, 3, 4)  # a is now 1, b is now [2, 3] and c is now 4
# Tuples are created by default if you leave out the parentheses
d, e, f = 4, 5, 6
# Now look how easy it is to swap two values
e, d = d, e  # d is now 5 and e is now 4


# Dictionaries store mappings from keys to values
empty_dict = {}
# Here is a prefilled dictionary
filled_dict = {"one": 1, "two": 2, "three": 3}

# Note keys for dictionaries have to be immutable types. This is to ensure that
# the key can be converted to a constant hash value for quick look-ups.
# Immutable types include ints, floats, strings, tuples.
invalid_dict = {[1,2,3]: "123"}  # => Raises a TypeError: unhashable type: 'list'
valid_dict = {(1,2,3):[1,2,3]}   # Values can be of any type, however.

# Look up values with []
filled_dict["one"]  # => 1

# Get all keys as an iterable with "keys()". We need to wrap the call in list()
# to turn it into a list. We'll talk about those later.  Note - Dictionary key
# ordering is not guaranteed. Your results might not match this exactly.
list(filled_dict.keys())  # => ["three", "two", "one"]


# Get all values as an iterable with "values()". Once again we need to wrap it
# in list() to get it out of the iterable. Note - Same as above regarding key
# ordering.
list(filled_dict.values())  # => [3, 2, 1]


# Check for existence of keys in a dictionary with "in"
"one" in filled_dict  # => True
1 in filled_dict      # => False

# Looking up a non-existing key is a KeyError
filled_dict["four"]  # KeyError

# Use "get()" method to avoid the KeyError
filled_dict.get("one")      # => 1
filled_dict.get("four")     # => None
# The get method supports a default argument when the value is missing
filled_dict.get("one", 4)   # => 1
filled_dict.get("four", 4)  # => 4

# "setdefault()" inserts into a dictionary only if the given key isn't present
filled_dict.setdefault("five", 5)  # filled_dict["five"] is set to 5
filled_dict.setdefault("five", 6)  # filled_dict["five"] is still 5

# Adding to a dictionary
filled_dict.update({"four":4})  # => {"one": 1, "two": 2, "three": 3, "four": 4}
filled_dict["four"] = 4         # another way to add to dict

# Remove keys from a dictionary with del
del filled_dict["one"]  # Removes the key "one" from filled dict

# From Python 3.5 you can also use the additional unpacking options
{'a': 1, **{'b': 2}}  # => {'a': 1, 'b': 2}
{'a': 1, **{'a': 2}}  # => {'a': 2}



# Sets store ... well sets
empty_set = set()
# Initialize a set with a bunch of values. Yeah, it looks a bit like a dict. Sorry.
some_set = {1, 1, 2, 2, 3, 4}  # some_set is now {1, 2, 3, 4}

# Similar to keys of a dictionary, elements of a set have to be immutable.
invalid_set = {[1], 1}  # => Raises a TypeError: unhashable type: 'list'
valid_set = {(1,), 1}

# Add one more item to the set
filled_set = some_set
filled_set.add(5)  # filled_set is now {1, 2, 3, 4, 5}

# Do set intersection with &
other_set = {3, 4, 5, 6}
filled_set & other_set  # => {3, 4, 5}

# Do set union with |
filled_set | other_set  # => {1, 2, 3, 4, 5, 6}

# Do set difference with -
{1, 2, 3, 4} - {2, 3, 5}  # => {1, 4}

# Do set symmetric difference with ^
{1, 2, 3, 4} ^ {2, 3, 5}  # => {1, 4, 5}

# Check if set on the left is a superset of set on the right
{1, 2} >= {1, 2, 3} # => False

# Check if set on the left is a subset of set on the right
{1, 2} <= {1, 2, 3} # => True

# Check for existence in a set with in
2 in filled_set   # => True
10 in filled_set  # => False



####################################################
## 3. Control Flow and Iterables
####################################################

# Let's just make a variable
some_var = 5

# Here is an if statement. Indentation is significant in Python!
# Convention is to use four spaces, not tabs.
# This prints "some_var is smaller than 10"
if some_var > 10:
    print("some_var is totally bigger than 10.")
elif some_var < 10:    # This elif clause is optional.
    print("some_var is smaller than 10.")
else:                  # This is optional too.
    print("some_var is indeed 10.")


"""
For loops iterate over lists
prints:
    dog is a mammal
    cat is a mammal
    mouse is a mammal
"""
for animal in ["dog", "cat", "mouse"]:
    # You can use format() to interpolate formatted strings
    print("{} is a mammal".format(animal))

"""
"range(number)" returns an iterable of numbers
from zero to the given number
prints:
    0
    1
    2
    3
"""
for i in range(4):
    print(i)

"""
"range(lower, upper)" returns an iterable of numbers
from the lower number to the upper number
prints:
    4
    5
    6
    7
"""
for i in range(4, 8):
    print(i)

"""
"range(lower, upper, step)" returns an iterable of numbers
from the lower number to the upper number, while incrementing
by step. If step is not indicated, the default value is 1.
prints:
    4
    6
"""
for i in range(4, 8, 2):
    print(i)
"""

While loops go until a condition is no longer met.
prints:
    0
    1
    2
    3
"""
x = 0
while x < 4:
    print(x)
    x += 1  # Shorthand for x = x + 1

# Handle exceptions with a try/except block
try:
    # Use "raise" to raise an error
    raise IndexError("This is an index error")
except IndexError as e:
    pass                 # Pass is just a no-op. Usually you would do recovery here.
except (TypeError, NameError):
    pass                 # Multiple exceptions can be handled together, if required.
else:                    # Optional clause to the try/except block. Must follow all except blocks
    print("All good!")   # Runs only if the code in try raises no exceptions
finally:                 #  Execute under all circumstances
    print("We can clean up resources here")

# Instead of try/finally to cleanup resources you can use a with statement
with open("myfile.txt") as f:
    for line in f:
        print(line)

# Python offers a fundamental abstraction called the Iterable.
# An iterable is an object that can be treated as a sequence.
# The object returned by the range function, is an iterable.

filled_dict = {"one": 1, "two": 2, "three": 3}
our_iterable = filled_dict.keys()
print(our_iterable)  # => dict_keys(['one', 'two', 'three']). This is an object that implements our Iterable interface.

# We can loop over it.
for i in our_iterable:
    print(i)  # Prints one, two, three

# However we cannot address elements by index.
our_iterable[1]  # Raises a TypeError

# An iterable is an object that knows how to create an iterator.
our_iterator = iter(our_iterable)

# Our iterator is an object that can remember the state as we traverse through it.
# We get the next object with "next()".
next(our_iterator)  # => "one"

# It maintains state as we iterate.
next(our_iterator)  # => "two"
next(our_iterator)  # => "three"

# After the iterator has returned all of its data, it raises a StopIteration exception
next(our_iterator)  # Raises StopIteration

# You can grab all the elements of an iterator by calling list() on it.
list(filled_dict.keys())  # => Returns ["one", "two", "three"]


####################################################
## 4. Functions
####################################################

# Use "def" to create new functions
def add(x, y):
    print("x is {} and y is {}".format(x, y))
    return x + y  # Return values with a return statement

# Calling functions with parameters
add(5, 6)  # => prints out "x is 5 and y is 6" and returns 11

# Another way to call functions is with keyword arguments
add(y=6, x=5)  # Keyword arguments can arrive in any order.

# You can define functions that take a variable number of
# positional arguments
def varargs(*args):
    return args

varargs(1, 2, 3)  # => (1, 2, 3)

# You can define functions that take a variable number of
# keyword arguments, as well
def keyword_args(**kwargs):
    return kwargs

# Let's call it to see what happens
keyword_args(big="foot", loch="ness")  # => {"big": "foot", "loch": "ness"}


# You can do both at once, if you like
def all_the_args(*args, **kwargs):
    print(args)
    print(kwargs)
"""
all_the_args(1, 2, a=3, b=4) prints:
    (1, 2)
    {"a": 3, "b": 4}
"""

# When calling functions, you can do the opposite of args/kwargs!
# Use * to expand tuples and use ** to expand kwargs.
args = (1, 2, 3, 4)
kwargs = {"a": 3, "b": 4}
all_the_args(*args)            # equivalent to foo(1, 2, 3, 4)
all_the_args(**kwargs)         # equivalent to foo(a=3, b=4)
all_the_args(*args, **kwargs)  # equivalent to foo(1, 2, 3, 4, a=3, b=4)

# Returning multiple values (with tuple assignments)
def swap(x, y):
    return y, x  # Return multiple values as a tuple without the parenthesis.
                 # (Note: parenthesis have been excluded but can be included)

x = 1
y = 2
x, y = swap(x, y)     # => x = 2, y = 1
# (x, y) = swap(x,y)  # Again parenthesis have been excluded but can be included.

# Function Scope
x = 5

def set_x(num):
    # Local var x not the same as global variable x
    x = num    # => 43
    print(x)   # => 43

def set_global_x(num):
    global x
    print(x)   # => 5
    x = num    # global var x is now set to 6
    print(x)   # => 6

set_x(43)
set_global_x(6)


# Python has first class functions
def create_adder(x):
    def adder(y):
        return x + y
    return adder

add_10 = create_adder(10)
add_10(3)   # => 13

# There are also anonymous functions
(lambda x: x > 2)(3)                  # => True
(lambda x, y: x ** 2 + y ** 2)(2, 1)  # => 5

# There are built-in higher order functions
list(map(add_10, [1, 2, 3]))          # => [11, 12, 13]
list(map(max, [1, 2, 3], [4, 2, 1]))  # => [4, 2, 3]

list(filter(lambda x: x > 5, [3, 4, 5, 6, 7]))  # => [6, 7]

# We can use list comprehensions for nice maps and filters
# List comprehension stores the output as a list which can itself be a nested list
[add_10(i) for i in [1, 2, 3]]         # => [11, 12, 13]
[x for x in [3, 4, 5, 6, 7] if x > 5]  # => [6, 7]

# You can construct set and dict comprehensions as well.
{x for x in 'abcddeef' if x not in 'abc'}  # => {'d', 'e', 'f'}
{x: x**2 for x in range(5)}  # => {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}


####################################################
## 5. Modules
####################################################

# You can import modules
import math
print(math.sqrt(16))  # => 4.0

# You can get specific functions from a module
from math import ceil, floor
print(ceil(3.7))   # => 4.0
print(floor(3.7))  # => 3.0

# You can import all functions from a module.
# Warning: this is not recommended
from math import *

# You can shorten module names
import math as m
math.sqrt(16) == m.sqrt(16)  # => True

# Python modules are just ordinary Python files. You
# can write your own, and import them. The name of the
# module is the same as the name of the file.

# You can find out which functions and attributes
# are defined in a module.
import math
dir(math)

# If you have a Python script named math.py in the same
# folder as your current script, the file math.py will
# be loaded instead of the built-in Python module.
# This happens because the local folder has priority
# over Python's built-in libraries.


####################################################
## 6. Classes
####################################################

# We use the "class" statement to create a class
class Human:

    # A class attribute. It is shared by all instances of this class
    species = "H. sapiens"

    # Basic initializer, this is called when this class is instantiated.
    # Note that the double leading and trailing underscores denote objects
    # or attributes that are used by Python but that live in user-controlled
    # namespaces. Methods(or objects or attributes) like: __init__, __str__,
    # __repr__ etc. are called special methods (or sometimes called dunder methods)
    # You should not invent such names on your own.
    def __init__(self, name):
        # Assign the argument to the instance's name attribute
        self.name = name

        # Initialize property
        self._age = 0

    # An instance method. All methods take "self" as the first argument
    def say(self, msg):
        print("{name}: {message}".format(name=self.name, message=msg))

    # Another instance method
    def sing(self):
        return 'yo... yo... microphone check... one two... one two...'

    # A class method is shared among all instances
    # They are called with the calling class as the first argument
    @classmethod
    def get_species(cls):
        return cls.species

    # A static method is called without a class or instance reference
    @staticmethod
    def grunt():
        return "*grunt*"

    # A property is just like a getter.
    # It turns the method age() into an read-only attribute of the same name.
    # There's no need to write trivial getters and setters in Python, though.
    @property
    def age(self):
        return self._age

    # This allows the property to be set
    @age.setter
    def age(self, age):
        self._age = age

    # This allows the property to be deleted
    @age.deleter
    def age(self):
        del self._age


# When a Python interpreter reads a source file it executes all its code.
# This __name__ check makes sure this code block is only executed when this
# module is the main program.
if __name__ == '__main__':
    # Instantiate a class
    i = Human(name="Ian")
    i.say("hi")                     # "Ian: hi"
    j = Human("Joel")
    j.say("hello")                  # "Joel: hello"
    # i and j are instances of type Human, or in other words: they are Human objects

    # Call our class method
    i.say(i.get_species())          # "Ian: H. sapiens"
    # Change the shared attribute
    Human.species = "H. neanderthalensis"
    i.say(i.get_species())          # => "Ian: H. neanderthalensis"
    j.say(j.get_species())          # => "Joel: H. neanderthalensis"

    # Call the static method
    print(Human.grunt())            # => "*grunt*"

    # Cannot call static method with instance of object
    # because i.grunt() will automatically put "self" (the object i) as an argument
    print(i.grunt())                # => TypeError: grunt() takes 0 positional arguments but 1 was given

    # Update the property for this instance
    i.age = 42
    # Get the property
    i.say(i.age)                    # => "Ian: 42"
    j.say(j.age)                    # => "Joel: 0"
    # Delete the property
    del i.age
    # i.age                         # => this would raise an AttributeError


####################################################
## 6.1 Inheritance
####################################################

# Inheritance allows new child classes to be defined that inherit methods and
# variables from their parent class.

# Using the Human class defined above as the base or parent class, we can
# define a child class, Superhero, which inherits the class variables like
# "species", "name", and "age", as well as methods, like "sing" and "grunt"
# from the Human class, but can also have its own unique properties.

# To take advantage of modularization by file you could place the classes above in their own files,
# say, human.py

# To import functions from other files use the following format
# from "filename-without-extension" import "function-or-class"

from human import Human


# Specify the parent class(es) as parameters to the class definition
class Superhero(Human):

    # If the child class should inherit all of the parent's definitions without
    # any modifications, you can just use the "pass" keyword (and nothing else)
    # but in this case it is commented out to allow for a unique child class:
    # pass

    # Child classes can override their parents' attributes
    species = 'Superhuman'

    # Children automatically inherit their parent class's constructor including
    # its arguments, but can also define additional arguments or definitions
    # and override its methods such as the class constructor.
    # This constructor inherits the "name" argument from the "Human" class and
    # adds the "superpower" and "movie" arguments:
    def __init__(self, name, movie=False,
                 superpowers=["super strength", "bulletproofing"]):

        # add additional class attributes:
        self.fictional = True
        self.movie = movie
        self.superpowers = superpowers

        # The "super" function lets you access the parent class's methods
        # that are overridden by the child, in this case, the __init__ method.
        # This calls the parent class constructor:
        super().__init__(name)

    # overload the sing method
    def sing(self):
        return 'Dun, dun, DUN!'

    # add an additional class method
    def boast(self):
        for power in self.superpowers:
            print("I wield the power of {pow}!".format(pow=power))


if __name__ == '__main__':
    sup = Superhero(name="Tick")

    # Instance type checks
    if isinstance(sup, Human):
        print('I am human')
    if type(sup) is Superhero:
        print('I am a superhero')

    # Get the Method Resolution search Order used by both getattr() and super()
    # This attribute is dynamic and can be updated
    print(Superhero.__mro__)    # => (<class '__main__.Superhero'>,
                                # => <class 'human.Human'>, <class 'object'>)

    # Calls parent method but uses its own class attribute
    print(sup.get_species())    # => Superhuman

    # Calls overloaded method
    print(sup.sing())           # => Dun, dun, DUN!

    # Calls method from Human
    sup.say('Spoon')            # => Tick: Spoon

    # Call method that exists only in Superhero
    sup.boast()                 # => I wield the power of super strength!
                                # => I wield the power of bulletproofing!

    # Inherited class attribute
    sup.age = 31
    print(sup.age)              # => 31

    # Attribute that only exists within Superhero
    print('Am I Oscar eligible? ' + str(sup.movie))

####################################################
## 6.2 Multiple Inheritance
####################################################

# Another class definition
# bat.py
class Bat:

    species = 'Baty'

    def __init__(self, can_fly=True):
        self.fly = can_fly

    # This class also has a say method
    def say(self, msg):
        msg = '... ... ...'
        return msg

    # And its own method as well
    def sonar(self):
        return '))) ... ((('

if __name__ == '__main__':
    b = Bat()
    print(b.say('hello'))
    print(b.fly)


# And yet another class definition that inherits from Superhero and Bat
# superhero.py
from superhero import Superhero
from bat import Bat

# Define Batman as a child that inherits from both Superhero and Bat
class Batman(Superhero, Bat):

    def __init__(self, *args, **kwargs):
        # Typically to inherit attributes you have to call super:
        #super(Batman, self).__init__(*args, **kwargs)
        # However we are dealing with multiple inheritance here, and super()
        # only works with the next base class in the MRO list.
        # So instead we explicitly call __init__ for all ancestors.
        # The use of *args and **kwargs allows for a clean way to pass arguments,
        # with each parent "peeling a layer of the onion".
        Superhero.__init__(self, 'anonymous', movie=True,
                           superpowers=['Wealthy'], *args, **kwargs)
        Bat.__init__(self, *args, can_fly=False, **kwargs)
        # override the value for the name attribute
        self.name = 'Sad Affleck'

    def sing(self):
        return 'nan nan nan nan nan batman!'


if __name__ == '__main__':
    sup = Batman()

    # Get the Method Resolution search Order used by both getattr() and super().
    # This attribute is dynamic and can be updated
    print(Batman.__mro__)       # => (<class '__main__.Batman'>,
                                # => <class 'superhero.Superhero'>,
                                # => <class 'human.Human'>,
                                # => <class 'bat.Bat'>, <class 'object'>)

    # Calls parent method but uses its own class attribute
    print(sup.get_species())    # => Superhuman

    # Calls overloaded method
    print(sup.sing())           # => nan nan nan nan nan batman!

    # Calls method from Human, because inheritance order matters
    sup.say('I agree')          # => Sad Affleck: I agree

    # Call method that exists only in 2nd ancestor
    print(sup.sonar())          # => ))) ... (((

    # Inherited class attribute
    sup.age = 100
    print(sup.age)              # => 100

    # Inherited attribute from 2nd ancestor whose default value was overridden.
    print('Can I fly? ' + str(sup.fly)) # => Can I fly? False



####################################################
## 7. Advanced
####################################################

# Generators help you make lazy code.
def double_numbers(iterable):
    for i in iterable:
        yield i + i

# Generators are memory-efficient because they only load the data needed to
# process the next value in the iterable. This allows them to perform
# operations on otherwise prohibitively large value ranges.
# NOTE: `range` replaces `xrange` in Python 3.
for i in double_numbers(range(1, 900000000)):  # `range` is a generator.
    print(i)
    if i >= 30:
        break

# Just as you can create a list comprehension, you can create generator
# comprehensions as well.
values = (-x for x in [1,2,3,4,5])
for x in values:
    print(x)  # prints -1 -2 -3 -4 -5 to console/terminal

# You can also cast a generator comprehension directly to a list.
values = (-x for x in [1,2,3,4,5])
gen_to_list = list(values)
print(gen_to_list)  # => [-1, -2, -3, -4, -5]


# Decorators
# In this example `beg` wraps `say`. If say_please is True then it
# will change the returned message.
from functools import wraps


def beg(target_function):
    @wraps(target_function)
    def wrapper(*args, **kwargs):
        msg, say_please = target_function(*args, **kwargs)
        if say_please:
            return "{} {}".format(msg, "Please! I am poor :(")
        return msg

    return wrapper


@beg
def say(say_please=False):
    msg = "Can you buy me a beer?"
    return msg, say_please


print(say())                 # Can you buy me a beer?
print(say(say_please=True))  # Can you buy me a beer? Please! I am poor :(

