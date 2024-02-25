---
date:
  created: 2024-02-24
  updated: 2024-02-25
description: >
  日常杂乱的一些笔记和记录
  注意是一些不进行具体分类的信息和一些各种小技巧
categories:
  - General
---

# General

## 谷歌搜索技巧

```markdown
设置定时 set timer for 1 minute
查询您的IP  ip address
掷色子  roll a dice
"" 英文双引号精确搜索
- 排除字符
搜索社交标签 将 at 符号 (@) 放在您要搜索的社交标签之前，例如：@digitaltrends
iphone $75..$200   搜索 在$75..$200区间的 iphone
好: baidu.com 使用冒号搜索特定的网站
am*zing 使用星号通配符代替缺少单词
#trump 搜索关于川普的热点信息
related:amazon.com 找到与其他网站类似的网站
Black or red 合并搜索
filetype:pdf 找到特定文件
site:www.so.com 在360搜索引擎中寻找
allintitle: allinurl:
@twitter 搜索社交媒体
info: 获取有关网站的详细信息
cache:  查看Google的网站缓存版本
google.com/history 搜索历史
inurl:  搜索的关键字在url链接中
intitle:    搜索的关键字在网页标题中
inauthor:"作者名"  根据作者名搜索相关书籍及信息

AND：返回查询两端的结果。例：growth hacks AND youtube
NOT：返回除NOT之后的所有结果。例：monty python NOT bbc
使用~搜索词的同义词
使用in的翻译和转换
process management inurl:forum|forums|discussion|viewthread|showthread|viewtopic|showtopic 论坛
google.com/ncr。NCR代表无国家/地区重定向
process automation filetype:pdf

将“link:”放在您想要查找链接到的页面的网站或域的前面，例如：link:digitaltrends.com
将“related：”放在您想要查找类似结果的网站或域的前面，例如：related：digitaltrends.com

搜索ftp
inurl:ftp:// -inurl:http:// -inurl:https://

-inurl(html|htm|php) intitle:"index of" +"last modified" +"parent directory" +description +size

-inurl:(htm|html|php) intitle:"index of" +"last modified" +"parent directory" +description +size +(wmv|avi)

-inurl:(htm|html|php) intitle:"index of" +"last modified" +"parent directory" +description +size +(jpg|gif)

-inurl:(htm|html|php) intitle:"index of" +"last modified" +"parent directory" +description +size +(wma|mp3)

[-inurl:(htm|html|php) intitle:" index of"  +" last modified"  +" parent directory" +description +size +(jpg|gif) “britney spears"]

```

## 获取公网IP

```bash
# 获取公网ip
# 普通文本格式
curl icanhazip.com
curl http://members.3322.org/dyndns/getip
curl ip.6655.com/ip.aspx
curl ident.me
curl ipecho.net/plain
curl whatismyip.akamai.com
curl ifconfig.me
curl myip.dnsomatic.com
curl ifconfig.co
curl http://ipv4.ip.sb
curl http://ip-api.com/json

# json格式
curl ipinfo.io

# 返回ip和地区
curl ip.6655.com/ip.aspx?area=1
curl cip.cc
wget -q -O - http://icanhazip.com | /usr/bin/tail

https://pv.sohu.com/cityjson?ie=utf-8
```
