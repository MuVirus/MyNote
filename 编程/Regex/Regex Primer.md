# 一、基本匹配
## 1、完整匹配
直接输入对应的字符串。
如：匹配Hello
直接输入
``` regex
Hello
```
## 2、字符
### 1. 匹配任何字符
使用点(.)
``` regex
.
```
### 2. 字符集
用中括号表示字符集
``` regex
[]
```
比如：要匹配a、c、d的所有字符。
``` regex
[acd]
```
### 3. 匹配字符集
比如：要匹配bee开头，a、c、d的所有的字符串
``` regex
bee[acd]
```
### 4. 否定字符集
使用^符号
比如：要匹配bee开头，a、c、d以外的所有的字符串
``` regex
bee[^acd]
```
### 5. 字符范围
使用-符号表示范围
1、字母范围：匹配a到e所有的字符
``` regex
[a-e]
```
2、数字范围：匹配3-7所有的字符
``` regex
[3-7]
```
## 3、重复
### 1. `*`
不匹配**0次**或可以匹配**多次**
比如：br中间匹配0个或多个e(br、ber、beer等等)
``` regex
be*r
```
### 2. `+`
匹配**一次或多次**
比如：br中间匹配1个或者多个e（ber、beer等等）
``` regex
be+r
```
### 3. `?`
表示该字符**可选**
比如要匹配color、colour
``` regex
colou?r
```
### 4. `{}`
1、确定重复次数
``` regex
e{n}
```
2、重复次数范围
``` regex
e{n,m}
```
3、至少重复次数
``` regex
e{n,}
```
### 5. 示例
#### 1）阿拉伯不同重复数字
让阿拉伯数字重复n次。
```
[0-9]{n}
```
## 分组

# 参考
[Regex Learn - 正则表达式备忘单](https://regexlearn.com/zh-cn/cheatsheet)
[RegexOne 中文 - 通过简单的交互式练习来学习正则表达式](https://imageslr.github.io/regexone-cn/)
