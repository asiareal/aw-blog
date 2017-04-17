---
title: inline-block 间距问题解决
date: 2017-04-17 21:10:08
categories: 笔记
tags:
  - css
---
> `inline-block` 为CSS2.1 新增的值，表示行内块元素。从[caniuse](http://caniuse.com/)上看
市面上主流浏览器都已支持，（IE为IE8及以上都已完全支持，如果要兼容IE6，IE7有hack解决方法）。
>
> 行内块元素可以很方便的使块元素水平排列显示，并且配合`text-align`属性，很方便完成向左，居中，向右对齐。
很多地方可以完全替代`float`属性。
>
>但是，使用过程中会发现inline-block的元素之间会存在“4px”的空白间距问题，今天就总结一下它的几种解决方法。

## 问题展示
<script async src="//jsfiddle.net/asiareal/pqnbLdca/embed/result,html,css/"></script>

由上可以看到几个`inline-block`元素间，存在着空白间距。

那么出现这种原因是为什么呢？本质inline元素中间的空白符引起的，解决办法：首先想到的就是去掉空白符

## 解决方法

### 方法1 改变HTML结构
<script async src="//jsfiddle.net/asiareal/um5uf9bq/3/embed/html,result,css/"></script>

这种方法适用于自己写静态页面时候使用，一旦html为后台生成这就完了，自己控制不了，但这个时候可以利用jQuery来解决

<script async src="//jsfiddle.net/asiareal/rdbvh7wa/1/embed/js,result,html,css/"></script>

### 方法2 纯css解决方法
<script async src="//jsfiddle.net/asiareal/1s98y5so/embed/css,result,html/"></script>
在父元素中设置font-size:0,用来兼容chrome，而使用letter-space:-N px来兼容safari。

缺点就是 `letter-spacing` 需要根据字号大小，做不同程度调整

---
好了，翻阅网上资料，这两种解决方案是比较完美的，总结于此。至于一些其他方法，我觉得都不是很可取。希望这篇文章对你处理这类问题有帮助。

