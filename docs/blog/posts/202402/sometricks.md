---
date:
  created: 2024-02-24
  updated: 2024-02-26
description: >
  日常杂乱的一些笔记和记录
  注意是一些不进行具体分类的信息和一些各种小技巧
categories:
  - General
---

# General

## 谷歌搜索技巧

| [用法](#fn:1)             | 描述                                              |
| :----------------------- | :------------------------------------------------ |
| `关键词 filetype:pdf`     | 包含关键词且文件类型为PDF的内容                       |
| `关键词 site:trump.com`   | 在trump.com中搜索包含关键词的页面                    |
| `Trump or Ivanka`        | 搜索结果中包含关键词"Trump"或"Ivanka"的内容           |
| `Trump and Ivanka`       | 搜索结果中必须同时包含关键词"Trump"和"Ivanka"的内容    |
| `Trump not Ivanka`       | 搜索结果中包含关键词"Trump"但不包含关键词"Ivanka"的内容 |
| `~Trump`                 | 搜索结果中包含与关键词"Trump"相关的同义词或类似意思的内容 |
| `Do*ld Trump`            | 使用星号通配符代替缺少单词                            |
| `"Trump"`                | 关键词添加双引号精确搜索Trump                         |
| `Trump @twitter`         | 搜索关于Trump的推特信息,用法: @符号后添加社交平台名称    |
| `#Trump`                 | 筛选出包含Trump主题的内容                             |
| `iphone $75..$200`       | 搜索结果筛选出价格在75到200美元之间的iPhone商品         |
| `info:trump.com`         | trump.com网站的详细信息                              |
| `link:trump.com`         | 搜索网页中链接到trump.com网站的页面                    |
| `related:trump.com`      | 找出与trump.com网站类似的网站                         |
| `cache:trump.com`        | trump.com网站的缓存版本                              |
| `allintitle:`            | 搜索结果的标题必须包含所有指定的关键词                  |
| `intitle:`               | 搜索的关键字在网页标题中                              |
| `allinurl:`              | 搜索结果的URL必须包含所有指定的关键词                   |
| `inurl:`                 | 搜索的关键字在url链接中                               |
| `inauthor:"作者名"`       | 根据作者名搜索相关书籍及信息                           |
| `roll a dice`            | 投掷色子`shǎi zi`,还会显示其他类似小游戏               |
| `set timer for 1 minute` | 1分钟的定时器                                        |


??? tip "搜索ftp网站语法参考或示例"
    inurl:ftp:// -inurl:http:// -inurl:https://

    -inurl(html|htm|php) intitle:"index of" +"last modified" +"parent directory" +description +size

    -inurl:(htm|html|php) intitle:"index of" +"last modified" +"parent directory" +description +size +(wmv|avi)

    -inurl:(htm|html|php) intitle:"index of" +"last modified" +"parent directory" +description +size +(jpg|gif)

    -inurl:(htm|html|php) intitle:"index of" +"last modified" +"parent directory" +description +size +(wma|mp3)

    [-inurl:(htm|html|php) intitle:" index of"  +" last modified"  +" parent directory" +description +size +(jpg|gif) “britney spears"]


[^1]: 最重要是多种用法可以组合使用,善于思考并应用组合搜索！
