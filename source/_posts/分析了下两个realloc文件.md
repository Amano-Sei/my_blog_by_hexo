---
title: 分析了下两个realloc文件
cwd: hexo/myblog
date: 2020-08-18 17:59:52
tags:
- 日常
- csapp
---

得出了这种模式下，realloc一有机会就往前挪的话利用率很高，并且很快就不用挪来挪去了...

