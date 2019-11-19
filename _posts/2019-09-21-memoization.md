---
layout: mypost
title: memoization提升递归效率
description: 利用memoization提升递归效率
keywords: 递归,memoization
tags: JS
---

## 递归算法
实现n的阶乘
```
    const factorial = n => {
        if(n == 1) {
            return 1;
        } else {
            return factorial(n-1) * n;
        }
    };
```
本身这个函数是没什么可优化的，优化的需求来自下面
```
    factorial(5);
    factorial(6);
    factorial(7);
    factorial(8);
```
连续调用4次阶乘函数，每一次计算都必须从1开始，这就大大浪费了，当我们第一次执行`factorial(5)`的时候，把它的值缓存下来，等执行`factorial(6)`直接取这个值跟6相乘不就OK了。

> 优化

在这个阶乘函数里面，加个缓存，专门来储存已经执行过阶乘函数的结果，这玩意就是`memoization`。

```
    const factorial2 = (function () {
        let cache = [];
        return function f1(n) {
            if(n===1) {
                return 1;
            }else if(cache[n-1]) {
                // 这里加个console就可以确定数据是从缓存里面获取
                console.log(cache[n-1]);
                return cache[n-1];
            }else {
                let result = f1(n-1)*n;
                cache[n-1] = result;
                return  result;
            }
        }
    })();
    console.log(factorial2(5))
    console.log(factorial2(6))
```