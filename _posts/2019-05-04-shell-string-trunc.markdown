---
layout: default
title: shell之字符串截取
date: 2019-05-04 18:54:00
categories: main
---
**Linux Shell脚本的字符串截取**   
假设有变量`var=http://www.halfsre.com/test.htm`  

**一 # 号截取，删除左边字符，保留右边字符**
```
echo ${var#*//}
其中 var 是变量名，# 号是运算符，*// 表示从左边开始删除第一个 // 号及左边的所有字符
即删除 http://
```
**结果**: `www.halfsre.com/test.htm`    

**二 ## 号截取，删除左边字符，保留右边字符**
```
echo ${var##*/}
##*/ 表示从左边开始删除最后（最右边）一个 / 号及左边的所有字符
即删除 http://www.halfsre.com/   
```
**结果**: `test.htm`  

**三 %号截取，删除右边字符，保留左边字符**  
```
echo ${var%/*}
%/* 表示从右边开始，删除第一个 / 号及右边的字符
```
**结果**: `http://www.halfsre.com`  

**四 %% 号截取，删除右边字符，保留左边字符**  
```
echo ${var%%/*}
%%/* 表示从右边开始，删除最后（最左边）一个 / 号及右边的字符
```
**结果**: `http:`  

**五 从左边第几个字符开始，及字符的个数**  
```
echo ${var:0:5}
其中的 0 表示左边第一个字符开始，5 表示字符的总个数。
```
**结果**: `http:`  

**六 从左边第几个字符开始，一直到结束**  
```
echo ${var:7}
其中的 7 表示左边第8个字符开始，一直到结束。
```
**结果**: `www.halfsre.com/test.htm`  

**七 从右边第几个字符开始，及字符的个数**  
```
echo ${var:0-7:3}
其中的 0-7 表示右边算起第七个字符开始，3 表示字符的个数。
```
**结果**:`test`  

**八 从右边第几个字符开始，一直到结束**  
```
echo ${var:0-7}
表示从右边第七个字符开始，一直到结束。
```
**结果**: `test.htm`  
> 注：（左边的第一个字符是用 0 表示，右边的第一个字符用 0-1 表示）  

**shell字符串的截取的问题**  
**一、Linux shell 截取字符变量的前8位，有方法如下：**
- expr substr "$a" 1 8
- echo $a|awk '{print substr(,1,8)}'
- echo $a|cut -c1-8
- echo $a|dd bs=1 count=8 2>/dev/null  

**二、按指定的字符串截取**  
1、第一种方法:
`${varible##*string}` 从左向右截取最后一个string后的字符串
`${varible#*string}` 从左向右截取第一个string后的字符串
`${varible%%string*}` 从右向左截取最后一个string后的字符串
`${varible%string*}` 从右向左截取第一个string后的字符串  

"*"只是一个通配符可以不要  
例子：
```
$ MYVAR=foodforthought.jpg
$ echo ${MYVAR##*fo}
rthought.jpg
$ echo ${MYVAR#*fo}
odforthought.jpg
```  
2、第二种方法：  
`${varible:n1:n2}` 截取变量varible从n1到n2之间的字符串。  
可以根据特定字符偏移和长度，使用另一种形式的变量扩展，来选择特定子字符串。试着在 bash 中输入以下行：  
```
$ EXCLAIM=cowabunga
$ echo ${EXCLAIM:0:3}
cow
$ echo ${EXCLAIM:3:7}
abunga
```
这种形式的字符串截断非常简便，只需用冒号分开来指定起始字符和子字符串长度。  
三、按照指定要求分割：  
比如获取后缀名  
```
ls -al | cut -d “.” -f2
```
