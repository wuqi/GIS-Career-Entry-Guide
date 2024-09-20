---
title: ini文件
date: 2024-09-13T08:35:19Z
lastmod: 2024-09-13T08:37:33Z
---

**INI文件**是一种无固定标准格式的[配置文件](https://zh.wikipedia.org/wiki/%E8%A8%AD%E5%AE%9A%E6%AA%94 "配置文件")。它以简单的文字与简单的结构组成，常常使用在[Windows操作系统](https://zh.wikipedia.org/wiki/Windows%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F "Windows操作系统")上，许多程序也会采用INI文件做为配置文件之用。Windows操作系统后来以[注册表](https://zh.wikipedia.org/wiki/%E7%99%BB%E9%8C%84%E6%AA%94 "注册表")的形式取代掉INI档。INI文件的命名来源，是取自英文“初始（Initial）”的首字缩写，正与它的用途——初始化程序相应。有时候，INI文件也会以不同的扩展名，如“.CFG”、“.CONF”、或是“.TXT”代替。

ini的格式很简单，主要由节，键值对，注释组成：

### 节(section)

节用[方括号](https://baike.baidu.com/item/%E6%96%B9%E6%8B%AC%E5%8F%B7/9896244?fromModule=lemma_inlink)括起来，单独占一行，例如：

[section]

### 键(key)

键(key)又名属性(property)，单独占一行用[等号](https://baike.baidu.com/item/%E7%AD%89%E5%8F%B7/7804825?fromModule=lemma_inlink)连接[键名](https://baike.baidu.com/item/%E9%94%AE%E5%90%8D/8825107?fromModule=lemma_inlink)和[键值](https://baike.baidu.com/item/%E9%94%AE%E5%80%BC/10086175?fromModule=lemma_inlink)，例如：

name\=value

### 注释(comment)

注释使用英文[分号](https://baike.baidu.com/item/%E5%88%86%E5%8F%B7/998579?fromModule=lemma_inlink)（;）开头，单独占一行。在分号后面的文字，直到该行结尾都全部为注释，例如：

; comment text

### 示例

```ini
; last modified 1 April 2001 by John Doe
[owner]
name=John Doe
organization=Acme Products

[database]
server=192.0.2.42 ; use IP address in case network name resolution is not working
port=143
file="acme payroll.dat"
```
