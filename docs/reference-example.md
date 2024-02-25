---
title: mkdocs-material语法参考
description: mkdocs-material-reference翻译成中文并方便日常写作参考
icon: simple/materialformkdocs
---

# mkdocs-material语法中文示例参考

[英文原文参考链接](https://squidfunk.github.io/mkdocs-material/reference/)
此页面目的是直接显示具体效果，具体如何使用需要打开源文件参考。

## Icons, Emojis

[搜索icons, emojis链接 :material-heart-search:](https://squidfunk.github.io/mkdocs-material/reference/icons-emojis/#search){ .md-button }

## 警告note

**支持的警告类型有**: `note`,`abstract`,`info`,`tip`,`success`,`question`,`warning`,`failure`,`danger`,`bug`,`example`,`quote`

==警告示例1==: 警告遵循一个简单的语法：一个块以`!!!`开始，后面跟着一个作为警告类型限定符的关键字，块的内容在下一行，缩进四个空格

!!! note
    此页面目的是直接显示具体效果，具体如何使用需要打开源文件参考。

!!! quote
    此页面目的是直接显示具体效果，具体如何使用需要打开源文件参考。


==警告示例2==: 自定义警告标题

!!! example "带自定义标题的警告"
    此页面目的是直接显示具体效果，具体如何使用需要打开源文件参考。


==警告示例2==: 无图标和标题的警告，注意这不适用于折叠块

!!! warning ""
    无图标和标题的warning警告，注意无标题警告不能用于折叠块


==警告示例3==: 可折叠警告

??? tip
    当启用`Details`并且警告块以`???`而不是`!!!`开始时，警告块被渲染为可折叠且初始收起

???+ success
    `???`后添加一个`+`符号，警告块被渲染为可折叠且初始展开


==警告示例4==: 警告也可以被渲染为内联块(例如，用于边栏)，使用 `inline end` 修饰符将警告放置在右侧，或者使用 `inline` 修饰符将警告放置在左侧

!!! info inline end "将警告放置在右侧"
    使用 `inline end` 将警告放置在右侧
    [left for rtl languages
    :point_left:从右往左书写阅读的语言，如阿拉伯语]

```markdown title="inline end 将警告放置在右侧"
!!! info inline end "将警告放置在右侧"
    使用 `inline end` 将警告放置在右侧
    [left for rtl languages
    从右往左书写阅读的语言，如阿拉伯语]
```

!!! info inline "将警告放置在左侧"
    使用 `inline` 将警告放置在左侧
    [right for rtl languages
    :point_left:从右往左书写阅读的语言，如阿拉伯语]

```markdown title="inline 将警告放置在左侧"
!!! info inline "将警告放置在左侧"
    使用 `inline` 将警告放置在左侧
    [right for rtl languages
    从右往左书写阅读的语言，如阿拉伯语]
```

## 注释

==注释示例1==: 注释必须从数字1开始，且正文和注释中间须有一行空行 (1)
{ .annotate }

1. :man_raising_hand: 我是一个注释！我可以包含`code`、格式化的文本、
    图像...基本上任何可以用Markdown表达的东西。


==注释示例2==: 嵌套式注释，第二个注释要和第一个注释中间空一行 (1)
{ .annotate }

1.  :man_raising_hand: 我是第一个注释! (1)
    { .annotate }

    1.  :woman_raising_hand: 我是第二个注释!


==注释示例3==: 内容选型卡中添加注释
=== "内容选型卡 1"
    内容选型卡1中内容的注释 (1)
    { .annotate }

    1.  :man_raising_hand: 选项卡1内容注释!

=== "内容选型卡 2"

    内容选型卡2中内容的注释 (1)
    { .annotate }

    1.  :woman_raising_hand: 选项卡2内容注释!


==注释示例4==: 警告的标题和内容也可以承载注释，只需在类型限定符后面添加`annotate`修饰符，这和inline block的工作原理类似：
!!! note annotate "警告的标题添加注释示例: (1)"

    警告的内容添加注释示例, (2) 此处均为警告内容

1.  :man_raising_hand: 我是警告标题的注释!
2.  :woman_raising_hand: 我是警告内容的注释!

---

## 按钮

==按钮示例1==: 链接作为一个按钮显示，后缀加上大括号并添加`.md-button`类选择器即可，按钮颜色继承于mkdocs.yml的primary color和accent color

[访问此网站](https://19950128.com/){ .md-button }

[访问此网站](https://19950128.com/){ .md-button .md-button--primary }

==按钮示例2==: 带图标的按钮

[访问我的GitHub :octicons-logo-github-16: :material-source-branch:](https://github.com/Black-Gold){ .md-button }

## 代码块

==代码块示例1==: 带有标题的代码块

``` py title="bubble_sort.py"
def bubble_sort(items):
    for i in range(len(items)):
        for j in range(len(items) - 1 - i):
            if items[j] > items[j + 1]:
                items[j], items[j + 1] = items[j + 1], items[j]
```


==代码块示例2==: 如果你想去掉代码注释周围的注释字符，只需在代码注释的右括号后面加上`!`感叹号即可
```bash title="99TimesTable.sh"
# 利用bash的for循环生成99乘法表 (1)
# 利用bash的for循环生成99乘法表 (2)!
#!/bin/bash
for i in {1..9}
do
    for ((j=1; j<=i; j++))
    do
        product=$((i*j))
        printf "%d*%d=%d " "$j" "$i" "$product"
    done
    printf "\n"
done
```

1. :simple-gnubash: 未去掉代码中注释字符的注释示例!此时可以看到`#`注释字符和内容
2. :simple-gnubash: 去掉代码中注释字符的注释示例!此时已看不到`#`注释字符和内容


==代码块示例3==: 带行号的代码块,并高亮第6行
``` py title="99TimesTable.sh" linenums="1" hl_lines="6"
# 利用bash的while循环生成99乘法表
i=1
while [ $i -le 9 ]
do
    j=1
    while [ $j -le $i ]
    do
        product=$((i*j))
        echo -n "$j*$i=$product "
        j=$((j+1))
    done
    echo
    i=$((i+1))
done
```


==代码块示例4==: 高亮多行
=== "高亮不连续多行"

    ```py hl_lines="1 3"
    def bubble_sort(items):
        for i in range(len(items)):
            for j in range(len(items) - 1 - i):
                if items[j] > items[j + 1]:
                    items[j], items[j + 1] = items[j + 1], items[j]
    ```

=== "高亮连续多行"

    ```py hl_lines="3-5"
    def bubble_sort(items):
        for i in range(len(items)):
            for j in range(len(items) - 1 - i):
                if items[j] > items[j + 1]:
                    items[j], items[j + 1] = items[j + 1], items[j]
    ```


==代码块示例5==: 高亮内联代码块，添加一个shebang前缀加对应语言缩写

^^shebang前缀示例^^: `#!python`，`#!c++`，`#!csharp`，`#!cpp`，`#!js`，`#!javascript`，`#!ts`，`#!typescript`，`#!php`，`#!go`，`#!java`，`#!ruby`，`#!swift`，`#!scala`，`#!kotlin`，`#!rust`，`#!scala`，`#!gro`

`#!python range()` 用于生成数字序列


==代码块示例6==: 代码块引入文件`.gitignore`内容
``` title=".gitignore文件内容"
--8<-- ".gitignore"
```

## 内容标签

==内容标签示例1==: 带有代码块的内容标签
=== "C"

    ```c
    #include <stdio.h>

    int main(void) {
      printf("Hello world!\n");
      return 0;
    }
    ```

=== "C++"

    ```c++
    #include <iostream>

    int main(void) {
      std::cout << "Hello world!" << std::endl;
      return 0;
    }
    ```


==内容标签示例2==: 带有列表的内容标签，目的是对内容进行分组
=== "无序列表"
    * 射手座
    * 白羊座
    * 金牛座

=== "有序列表"
    1. 射手座
    2. 白羊座
    3. 金牛座


==内容标签示例3==: 当启用SuperFences时，内容选项卡可以包含任意嵌套内容，包括更多的内容选项卡，也可以嵌套在其他块中，如警告或block quote
!!! 内容标签示例3

    === "无序列表1"
        ``` markdown
        * 射手座
        * 白羊座
        * 金牛座
        ```
    === "有序列表1"

        ``` markdown
        1. 射手座
        2. 白羊座
        3. 金牛座
        ```

## 表格

==表格示例1==: 可以包含任意的Markdown，包括内联代码块，以及图标和表情符号

| Method      | Description                          |
| ----------- | ------------------------------------ |
| `GET`       | :material-check:     Fetch resource  |
| `PUT`       | :material-check-all: Update resource |
| `DELETE`    | :material-close:     Delete resource |


==表格示例2==: 定义表格对齐方式
=== "左对齐"

    ``` markdown hl_lines="2" title="Data table, columns aligned to left"
    | Method      | Description                          |
    | :---------- | :----------------------------------- |
    | `GET`       | :material-check:     Fetch resource  |
    | `PUT`       | :material-check-all: Update resource |
    | `DELETE`    | :material-close:     Delete resource |
    ```

=== "居中对齐"

    ``` markdown hl_lines="2" title="Data table, columns centered"
    | Method      | Description                          |
    | :---------: | :----------------------------------: |
    | `GET`       | :material-check:     Fetch resource  |
    | `PUT`       | :material-check-all: Update resource |
    | `DELETE`    | :material-close:     Delete resource |
    ```

=== "右对齐"

    ``` markdown hl_lines="2" title="Data table, columns aligned to right"
    | Method      | Description                          |
    | ----------: | -----------------------------------: |
    | `GET`       | :material-check:     Fetch resource  |
    | `PUT`       | :material-check-all: Update resource |
    | `DELETE`    | :material-close:     Delete resource |
    ```

## Diagrams图表

[其他图参考链接](https://mermaid.js.org/syntax/gitgraph.html)


==图表示例1==: 流程图

``` mermaid
graph LR
  A[Start] --> B{Error?};
  B -->|Yes| C[Hmm...];
  C --> D[Debug];
  D --> B;
  B ---->|No| E[Yay!];
```


==图表示例2==: 序列图

``` mermaid
sequenceDiagram
  autonumber
  Alice->>John: Hello John, how are you?
  loop Healthcheck
      John->>John: Fight against hypochondria
  end
  Note right of John: Rational thoughts!
  John-->>Alice: Great!
  John->>Bob: How about you?
  Bob-->>John: Jolly good!
```


==图表示例2==: 状态图

``` mermaid
stateDiagram-v2
  state fork_state <<fork>>
    [*] --> fork_state
    fork_state --> State2
    fork_state --> State3

    state join_state <<join>>
    State2 --> join_state
    State3 --> join_state
    join_state --> State4
    State4 --> [*]
```

## 格式化

Text can be {--deleted--} and replacement text {++added++}. This can also be
combined into {~~one~>a single~~} operation. {==Highlighting==} is also
possible {>>and comments can be added inline<<}.

{==

Formatting can also be applied to blocks by putting the opening and closing
tags on separate lines and adding new lines between the tags and the content.

==}


- ==This was marked==
- ^^This was inserted^^
- ~~This was deleted~~

文本上下标和、

- H~2~O
- A^T^A
- text^a\ superscript^

[显示键盘按键参考链接](https://facelessuser.github.io/pymdown-extensions/extensions/keys/#alphanumeric-and-space-keys)

++ctrl+alt+del++

## grids网格

==网格示例1==: 列表语法，本质上是卡片网格的快捷方式，由一个无序(或有序)的列表组成，该列表由div包裹，同时包含grid和cards类

<div class="grid cards" markdown>
- :fontawesome-brands-html5: __HTML__ for content and structure
- :fontawesome-brands-js: __JavaScript__ for interactivity
- :fontawesome-brands-css3: __CSS__ for text running out of boxes
- :fontawesome-brands-internet-explorer: __Internet Explorer__ ... huh?
</div>

==网格示例2==: 块语法，允许将卡片和其他元素一起排列在网格中，如在通用网格部分中解释的那样，只需将card类添加到grid中的任何块元素中

<div class="grid" markdown>
:fontawesome-brands-html5: __HTML__ for content and structure
{ .card }

:fontawesome-brands-js: __JavaScript__ for interactivity
{ .card }

:fontawesome-brands-css3: __CSS__ for text running out of boxes
{ .card }

> :fontawesome-brands-internet-explorer: __Internet Explorer__ ... huh?
</div>

==网格示例3==: 复杂卡片网格

<div class="grid cards" markdown>
-   :material-clock-fast:{ .lg .middle } __Set up in 5 minutes__

    ---

    Install [`mkdocs-material`](#) with [`pip`](#) and get up
    and running in minutes

    [:octicons-arrow-right-24: Getting started](#)

-   :fontawesome-brands-markdown:{ .lg .middle } __It's just Markdown__

    ---

    Focus on your content and generate a responsive and searchable static site

    [:octicons-arrow-right-24: Reference](#)

-   :material-format-font:{ .lg .middle } __Made to measure__

    ---

    Change the colors, fonts, language, icons, logo and more with a few lines

    [:octicons-arrow-right-24: Customization](#)

-   :material-scale-balance:{ .lg .middle } __Open Source, MIT__

    ---

    Material for MkDocs is licensed under MIT and available on [GitHub]

    [:octicons-arrow-right-24: License](#)
</div>


==网格示例4==: 通用网格，允许在网格中安排任意块元素，包括警告、代码块、内容标签等等。只需使用带有grid类的div来包装一组块：

<div class="grid" markdown>
=== "grid无序列表"

    * 射手座
    * 白羊座
    * 金牛座

=== "grid有序列表"

    1. 射手座
    2. 白羊座
    3. 金牛座

``` title="内容标签"
=== "无序列表"

    * 射手座m
    * 白羊座
    * 金牛座

=== "有序列表"

    1. 射手座
    2. 白羊座
    3. 金牛座
```
</div>

## 图片

图片居左

![Image title](https://dummyimage.com/100x100/eee/aaa){ align=left }

<figure markdown="span" title="带说明图片">
  ![Image title](https://dummyimage.com/100x100/){ width="100" }
  <figcaption>带说明图片</figcaption>
</figure>

![图片延迟加载](https://dummyimage.com/100x100/){ loading=lazy }

## 列表

==列表示例1==: 无序列表可以通过在一行前加上`-` `*` `+` 标记来编写，所有这些标记都可以互换使用。此外，所有类型的列表都可以嵌套在彼此之中

- 无序列表示例
    * 射手座
    * 白羊座
    * 金牛座


==列表示例2==: 有序列表必须以数字开头，紧接着一个点。数字不需要连续，可以全部设置为1.，因为它们在渲染时将重新编号

1.  射手座
    1. 射手座
    2. 白羊座
        1. 射手座
        2. 白羊座
        3. 金牛座


==列表示例3==: 自定义列表启用时，任意键值对的列表，例如函数或模块的参数，可以用简单的语法枚举

`顾客很高兴`
: 取决于生活

`明天免费`
: 不担心天气


==列表示例4==: 当Tasklist启用时，无序列表项可以使用`[ ]`作为前缀来呈现未选中的复选框，或者使用`[x]`来呈现选中的复选框，从而允许定义任务列表

- [x] Lorem ipsum dolor sit amet, consectetur adipiscing elit
- [ ] Vestibulum convallis sit amet nisi a tincidunt
    * [x] In hac habitasse platea dictumst
    * [x] In scelerisque nibh non dolor mollis congue sed et metus
    * [ ] Praesent sed risus massa
- [ ] Aenean pretium efficitur erat, donec pharetra, ligula non scelerisque

## 脚注

[:octicons-arrow-down-24: Jump to footnote1](#fn:1)
[^1]: 脚注1

[:octicons-arrow-down-24: Jump to footnote2](#fn:2)
[^2]: 脚注2
