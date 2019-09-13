---
layout: mypost
title: CSS3动画
tags: CSS3
---

其实构建CSS3动画很简单，总的来说就是三个属性
1. transform(变形)
2. transition(过度)
3. animation(动画)

### transform

```css
transform: rotate(360deg) scale(-1.2,1.2) translate(100px,50px) skew(45deg,45deg)
```
1. 旋转rotate       
2. 缩放scale
3. 位移translate
4. 倾斜skew

### transition

```css
.first{
left:110px;
top:0;
-webkit-transition-property:left,top,background;
-webkit-transition-duration:1s,2s,2s;
-webkit-transition-delay:0,0,2s;
-webkit-transition-timing-function:ease,ease-in,linear;
}
.first:hover{
left:310px;
top:200px;
background:red;
}
```
具体来说就是让属性的变化有一个过渡的效果

### animation

```css
div{aninmation:demo 1s ease}

@keyframes mymove
{
from {width:30rem;}
to {width:20rem;}
}
```
首先需要`@keyframes`定义一个动画，然后用`animation`启动它，`transition`有点像它的简化版

### 3D

上面的`transition`属性值都是两个，是基于`2D`，即X,Y轴，而如果要加一个Z轴，就必须声明它

```css
transform-style: perserce-3d;
```

画一个正方体

```css
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>perspective</title>
	<style>
         .wrapper{
         	width: 50%;
         	float: left;
         }
         .cube{
         	font-size: 4em;
         	width: 2em;
         	margin: 1.5em auto;
         	transform-style:preserve-3d;
         	transform:rotateX(-35deg) rotateY(30deg);
         }
         .side{
         	position: absolute;
         	width: 2em;
         	height: 2em;
         	background: rgba(255,99,71,0.6);
         	border: 1px solid rgba(0,0,0,0.5);
         	color: white;
         	text-align: center;
         	line-height: 2em;
         }
         .front{
         	transform:translateZ(1em);
         }
         .bottom{
         	transform:rotateX(-90deg) translateZ(1em);
         }
         .top{
         	transform:rotateX(90deg) translateZ(1em);
         }
         .left{
         	transform:rotateY(-90deg) translateZ(1em);
         }
         .right{
         	transform:rotateY(90deg) translateZ(1em);
         }
         .back{
         	transform:translateZ(-1em);
         }
	</style>
</head>
<body>
	<div class="wrapper">
		<div class="cube">
			<div class="side front">1</div>
			<div class="side back">6</div>
			<div class="side right">4</div>
			<div class="side left">3</div>
			<div class="side top">5</div>
			<div class="side bottom">2</div>
		</div>
	</div>
</body>
</html>

```

