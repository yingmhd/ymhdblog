---
layout: mypost
title: flask中post和get请求方式
description: python框架flask之http请求方式
keywords: flask post请求,flask get
tags: flask
---

## GET请求

前端
```javascript
    $.ajax({
        url:'/getStockData',
        method: 'GET',
        data:{
            "code":123
        },
        success:function (data) {
            // do something
        }
    })
```
后端
```python
from flask import request
@app.route('/getStockData')
def getStockData():
    code = request.args.get("code")
    list = getList(code)
    return json.dumps({
        "flag": "1",
        "data": list
    })
```

## POST请求

前端
```javascript
    $.ajax({
        url:'/getStockData',
        method: 'POST',
        data:JSON.stringify({
           "code":code
        }),
        success:function (data) {
            // do something
        }
    })
```
后端
```python
from flask import request
@app.route('/getStockData',methods=['POST'])
def getStockData():
    data = request.get_json("data")
    code = data['code']
    list = getList(code)
    return json.dumps({
        "flag": "1",
        "data": list
    })
```