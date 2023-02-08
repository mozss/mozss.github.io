---
title: 服务化治理脚本----jar包的包名和类名中查找某一关键字
tags:
  - shell
categories:
  - kits
abbrlink: 57828
date: 2022-05-02 06:26:10
---
[![jTa5UU.jpg](https://s1.ax1x.com/2022/07/19/jTa5UU.jpg)](https://imgtu.com/i/jTa5UU)
<!--more-->


## find-in-jar

此脚本用于在jar包的包名和类名中查找某一关键字, 并高亮显示匹配的包名, 类名和路径,多用于定位java.lang.NoClassDefFoundError和java.lang.ClassNotFoundException的问题, 以及类版本重复或冲突的问题.

- 命令格式:
find-in-jar 关键字 类名根路径

```shell
#!/bin/bash

find . -name "*.jar" > /tmp/find_in_jar_temp
while read line
do
  if unzip -l $line | grep $1 &> /tmp/find_in_jar_temp_second
  then 
    echo $line | sed 's#\(.*\)#\x1b[1;31m\1\x1b[00m#'
    cat /tmp/find_in_jar_temp_second fi 
  done < /tmp/find_in_jar_temp

```

































