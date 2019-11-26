---
layout: mypost
title: 使用promise封装ajax
description: 使用promise封装ajax，使用promise优化ajax
keywords: promise,ajax
tags: ['ES6','JS']
---

## 前言
ajax虽然用回调解决了JS异步的问题，但是回调函数中再异步再回调，回调嵌套就形成了一个地狱回调，不仅代码不美观而且影响阅读，但是自从有了promise之后，就勉强解决了地域回调的现象。

> promise的基本语法

移步至我的另外一篇博客[ES6之Promise和async](https://blog.ymhd.xyz/posts/2018/06/01/ES-3.html)

## 封装
```
    function ajax(options) {
        return new Promise((resolve, reject) => {
            if (!options.url) {
                alert("确认你的url");
                return;
            }
            let method = options.method || 'GET';
            let async = options.async || true;
            let xhr = new XMLHttpRequest();
            if (method === 'GET') {
                // url后面添加随机数防止请求缓存
                xhr.open(method, options.url + "?" + Math.random(), async);
                xhr.send(null);
            } else if (method === 'POST') {
                xhr.open(method, options.url, async);
                xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
                xhr.send(options.data);
            }

            xhr.onreadystatechange = () => {
                if (xhr.responseText) {
                    resolve(xhr.responseText);
                }
            };
            xhr.onerror = (e) => {
                reject(e);
            }
        }).catch(e => {
        });
    }
```

## 调用
```
    let req = ajax({
        url: '/getFoodList',
        method: 'POST',
        data: {
            page: 1
        }
    });
    req.then(data=>{
        console.log(data)
    })
```