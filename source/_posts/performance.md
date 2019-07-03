---
title: performance
date: 2019-07-03 22:35:43
tags: 
- Iteration
---
# for,for...of, for...in, array iteration 运行速度
- 由于不同浏览器，不同版本性能不一，且控制台本质是是套用了一大堆[eval](https://www.zhihu.com/question/29352114/answer/44050599)，沙盒化程度高,所以需使用[node](https://nodejs.org/en/)环境测试来提高准确性


## for