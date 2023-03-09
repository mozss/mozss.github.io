---
title: 服务化治理脚本----把线上的二进制包拉下来并查找特定的关键字来定位问题
tags:
  - shell
categories:
  - code
abbrlink: 11307
date: 2022-06-01 06:26:10
---

[![jTaODx.jpg](https://s1.ax1x.com/2022/07/19/jTaODx.jpg)](https://imgtu.com/i/jTaODx)

<!--more-->


## grep-in-jar

在jar包中进行二进制内容查找, 通常会解决线上出现的一些"不可思议"的问题, 例如: 某些功能没有生效, 某些日志没有打印, 通常是线上工具或上线过程中出现了问题, 可以把线上的二进制包拉下来并查找特定的关键字来定位问题.

- 命令格式:
grep-in-jar 关键字 路径



```shell
#!/bin/bash
### grep text inside jar content

if [ $# -lt 2 ]; then
    echo 'Usage : grep-in-jar text path'
    exti 1;
fi

LOOK_FOR=$1
LOOK_FOR='echo ${LOOK_FOR//\./\/}'
folder=$2
echo "find '$LOOK_FOR' in $folder"
for i in 'find $2 -name "*jar"'
do
  unzip -p $i | grep "$LOOK_FOR" > /dev/null
  if [ $? = 0 ]; then
      echo "==> Found \ "$LOOK_FOR\" in $i"
  fi
done
```

































