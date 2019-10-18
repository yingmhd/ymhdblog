---
layout: mypost
title: react引入sass
description: 在react项目中使用sass
keywords: sass, react项目
tags: [sass, react]
---

## 前言
首先，明确一点，sass的存在只是为了开发时节省时间，它运行的本质还是用工具把它构建成css文件，然后被使用
## 下载sass
```
cnpm install node-sass --save-dev
```
## 下载构建工具 node-sass-chokidar
```
cnpm isntall node-sass-chokidar --save-dev
```
## 配置package.json
在`package.json`的`scripts`中添加下面两行
```
"build-css": "node-sass-chokidar src/ -o src/",
"watch-css": "npm run build-css && node-sass-chokidar src/ -o src/ --watch --recursive",
```
上面的意思就是，用构建工具，把`src/`下面的sass文件变为css文件放在`src/`目录下。           
然后我们在`src`文件夹下放几个`.scss`文件，试着运行
```
npm run build-css
```
如果`scss`文件下面多了一个`css`文件，说明成功。           
下面我们就把上面的操作集成到`start`里面去
```
"scripts": {
    "build-css": "node-sass-chokidar src/ -o src/",
    "watch-css": "npm run build-css && node-sass-chokidar src/ -o src/ --watch --recursive",
    "start-js": "react-scripts start",
    "start": "npm-run-all -p watch-css start-js",
    "build-js": "react-scripts build",
    "build": "npm-run-all build-css build-js"
  }
```

这样我们就可以快乐的使用`npm start`了
## 补充
引入css的时候，还是要写`import 'xxxx.css'`而不能写`import 'xxx.scss'`