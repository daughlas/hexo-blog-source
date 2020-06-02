---
title:  学习 CSS
date: 
categories:
- 临时
tags:
- 临时
---

## flex 容器属性

### 开启

1. 注意 flex 容器不是块状元素
2. 属性值
   1. `display: flex;`
   2. `display: inline-flex;`

### flex-direction

1. 主轴方向
2. 属性值
   1. row 横排从左到右
   2. row-reserve 横排从右到左
   3. column 纵排从上到下
   4. column-reverse 纵排从下到上

### flex-wrap

1. 换行
2. 属性值
   1. nowrap （默认值）单行显示
   2. wrap 多行显示，换行

### justify-content

1. 主轴方向排列方式，对齐方式
2. 用于在主轴上对齐伸缩项目。这一行为会在所有可伸缩长度及所有自动边距均被解释后进行。著作权归作者所有。商业转载请联系作者获得授权,非商业转载请注明出处。当一行上的所有伸缩项目都不能伸缩或可伸缩但是已经达到其最大长度时，这一属性才会对多余的空间进行分配。著作权归作者所有。商业转载请联系作者获得授权,非商业转载请注明出处。
3. 属性值
   1. flex-start: 伸缩项目向一行的起始weizhi
   2. flex-end
   3. center: 主轴方向居中
   4. space-between：主轴方向内容分居两侧，其他内容的间距平均分配剩余的空间
   5. space-around：主轴方向内容的间距平均分配生育的空间

### align-items

1. 侧轴对齐方式
2. 属性值： 
   1. flex-start
   2. flex-end
   3. center
   4. stretch
   5. baseline

### align-content

1. 当伸缩容器的侧轴还有多余空间时，用来调整伸缩行在伸缩容器里的对其方式
2. 只有多行的伸缩容器才会在侧轴上有多余的空间以供对齐，因为仅包含一行的伸缩容器中，唯一的一行会自动伸展填充全部的空间。著作权归作者所有。商业转载请联系作者获得授权,非商业转载请注明出处。
3. 属性值： 
   1. flex-start
   2. flex-end
   3. center
   4. stretch
   5. baseline



## flex 项目属性

order 顺序

flex-grow 扩张

Flex-shrink 缩小

flex-basis 基础值

flex flex-grow 和 fl

align-self 对齐方式



#### order

控制项目的摆放顺序，默认值为 0， 可以为负数，值越小项目越靠前拜访。



#### flex-grow

容器宽度（row）超过所有项目宽度之和的时候，项目是不是需要拉伸。

控制项目的放大比例，默认为0，不放大。

值得注意的是，放大的比例是相对于剩余空间而言，而不是项目自己的大小，分得剩余空间相应比例加到自身宽度或高度上。



#### flex-shrink

容器宽度不能容纳所有项目的时候，每个项目是不是要缩小，

控制项目缩小比例，默认值为1，同比缩小。

缩小比例也是按照剩余空间。

？搜死的骆驼比马大

Flex-shrink ：0 表示不缩小



#### flex-basis

若横轴为主轴，flex-basis 可以当做width 来用；

若纵轴是主轴，flex-basis 可以当做 height 来使用



#### flex

Flex 是 flex-grow flex-shrink flex-basis 三个属性的缩写

flex: 1 代表flex-grow: 1 加上 flex-shrink: 1 加上 flex-basis: 0%。子元素一个flex：1，父元素给个宽度，直接等宽布局。



#### align-self

控制自身在侧轴方向的对齐方式。



## 多行文本省略显示

#### 所有浏览器兼容的方法

```
p {
width: 300px;
position: relative;
line-height: 1.5em;
height: 1.5em;
overflow: hidden;
}
p:after {
content: '...';
position: absolute;
bottom: 0;
right: 0;
padding: 0 5px;
background-color: #fff;
}
```

## CSS 瀑布流
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .grid {
      -webkit-column-count: 3;
      -webkit-column-gap: 10px;
      -webkit-column-fill: auto;
      -moz-column-count: 3;
      -moz-column-gap: 10px;
      -moz-column-fill: auto;
      column-count: 4;
      column-gap: 15px;
      column-fill: auto;
    }
    .block {
      background-color: #ee01ca;
      display: block;
      padding: 20px;
      word-wrap: break-word;
      margin-bottom: 20px;
      -webkit-column-break-inside: avoid;
      -moz-column-break-inside: avoid;
      column-break-inside: avoid;
    }

    .one {
      height: 100px;
    }

    .two {
      height: 200px;
    }

    .three {
      height: 300px;
    }
  </style>
</head>
<body>
  <div class="grid">
    <div class="block one">ervewrv</div>
    <div class="block two">afrvewrwerb</div>
    <div class="block three">dfvdsf</div>
    <div class="block one">vdfvdf</div>
    <div class="block two">dfw45g4</div>
    <div class="block one">ervewrv</div>
    <div class="block two">afrvewrwerb</div>
    <div class="block three">dfvdsf</div>
    <div class="block one">vdfvdf</div>
    <div class="block two">dfw45g4</div>
    <div class="block three">4rv4r</div>
    <div class="block one">4rv454</div>
    <div class="block three">4rv4r</div>
    <div class="block one">4rv454</div>
    <div class="block one">ervewrv</div>
    <div class="block one">ervewrv</div>
    <div class="block two">afrvewrwerb</div>
    <div class="block three">dfvdsf</div>
    <div class="block one">vdfvdf</div>
    <div class="block two">dfw45g4</div>
    <div class="block three">4rv4r</div>
    <div class="block one">4rv454</div>
    <div class="block one">ervewrv</div>
    <div class="block two">afrvewrwerb</div>
    <div class="block three">dfvdsf</div>
    <div class="block one">vdfvdf</div>
    <div class="block two">dfw45g4</div>
    <div class="block three">4rv4r</div>
    <div class="block one">4rv454</div>
    <div class="block two">afrvewrwerb</div>
    <div class="block three">dfvdsf</div>
    <div class="block one">vdfvdf</div>
    <div class="block two">dfw45g4</div>
    <div class="block one">ervewrv</div>
    <div class="block two">afrvewrwerb</div>
    <div class="block three">dfvdsf</div>
    <div class="block one">vdfvdf</div>
    <div class="block two">dfw45g4</div>
    <div class="block three">4rv4r</div>
    <div class="block one">4rv454</div>
    <div class="block three">4rv4r</div>
    <div class="block one">4rv454</div>
  </div>
</body>
</html>
```

## label 的使用

```html
<label class="ui_button ui_button_primary" for="xFile">上传文件</label>
<form><input type="file" id="xFile" style="position:absolute;clip:rect(0 0 0 0);"></form>
```









