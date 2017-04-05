---
title: Markdown语法
date: 2017-04-04 22:35:20
categories: 笔记
tags:
  - Markdown
---

## Markdown 简介
`Markdown` 是一门适用于网络的书写语言，其目标是实现「易读易写」。

众多博客网站，代码仓库，笔记等都支持`Markdown` 语法。他们都有`Markdown` 的解析引擎，会将`Markdown`语法的内容，转译为适合网络显示的`html`。比如大家熟知的`Github`在每个开源项目中都会有个 `README.md`，这个文件就是用`Markdown `语法写的，最终会被解析成优美的，格式整齐的项目简介和使用教程。许多社区也是，不熟悉`Markdown `语法都不好意思评论，进去根本就玩不转。

那么说了这么多，`Markdown `到底有什么好处呢?总结下来如下：
* 纯文本，所以兼容性极强，可以用所有文本编辑器打开。
* 让你专注于文字而不是排版。
* 格式转换方便，`Markdown` 的文本可以轻松转换为 `html`、电子书等。
* `Markdown` 的标记语法有极好的可读性。
* 我认为最最重要的是，支持各种代码的高亮显示，让你不用对着一坨全黑的代码看半天。

总之在我看来，`Markdown ` 就是**程序猿写文档必备的一门语言**。

## Markdown 常用语法说明
*一听把`Markdown `当作一门语言，心里一定发虚，又要花费一段时间学习了- -///。 
不用！你只需记住一些常用语法，正常打字，敲敲回车，在一些关键地方做一些语法标记。保存以后发现一篇优美的文档就出现在你面前了，就是这么神奇。*

好，下面介绍`Markdown `几个常用语法：

- - -

### 标题
*一篇文章要划分多个部分，标题不可少，标题`Markdown`语法如下(# 与 文字间要有空格)*
```Markdown 
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

- - -

### 列表
*列表格式也很常用，在 Markdown 中，你只需要在文字前面加上 - 就可以了，例如：*
```Markdown 
- 文本1
- 文本2
- 文本3
```
效果如下：
- 文本1
- 文本2
- 文本3

*如果你希望有序列表，也可以在文字前面加上 1. 2. 3. 就可以了，例如：*
```Markdown 
1. 文本1
2. 文本2
3. 文本3
```
效果如下：
1. 文本1
2. 文本2
3. 文本3

- - -

### 引用
*Markdown 中，你只需要在你希望引用的文字前面加上 > 就好了（符号与文字间要有空格，换行可加可不加，不过建议都加），例如：*
```Markdown 
> 一盏灯， 一片昏黄； 
> 一简书， 一杯淡茶。 
守着那一份淡定， 品读属于自己的寂寞。 保持淡定， 才能欣赏到最美丽的风景！ 保持淡定， 人生从此不再寂寞。
```
效果如下:
> 一盏灯， 一片昏黄； 
> 一简书， 一杯淡茶。 
守着那一份淡定， 品读属于自己的寂寞。 保持淡定， 才能欣赏到最美丽的风景！ 保持淡定， 人生从此不再寂寞。

- - -

### 粗体和斜体
```Markdown 
*我是斜体*
**我是粗体**
```
效果如下:
*我是斜体*
**我是粗体**

- - -

### 链接和图片
*`[链接文字](链接地址)` 和 `![图片说明](图片链接地址)` ，例如：*
```Markdown 
[百度](https://www.baidu.com/)
![百度logo](https://www.baidu.com/img/bd_logo1.png)
```
效果如下：
[百度](https://www.baidu.com/)
![百度logo](https://www.baidu.com/img/bd_logo1.png)

*ps: 在我们实际写文档时可以直接用为知笔记富文本编辑器，将图片插入相应位置即可。*

- - -

### 分割线
*最常用两种，效果一样*
```Markdown 
- - -
***
```
效果如下：
- - -
***
### 代码
*用`Markdown` 标记你的代码段，简直不要太方便，这也是为什么说程序员的文档应该用`Markdown`来写*
#### 短代码段
```Markdown 
使用 `printf()`函数
```
效果如下：
使用 `printf()`函数
#### 代码块
*代码块是以`三点号`后跟`代码类型` 开始 + `三点号` 结束的，例如*
**java代码**

\`\`\`java

private InfoStaticViewManager mgr = null;

//获取Manager

private InfoStaticViewManager getManager() {

  if(null == mgr) {
  
  mgr = new InfoStaticViewManager();
      
  }
  
  return mgr;
}

\`\`\`

效果如下
```java
private InfoStaticViewManager mgr = null;
//获取Manager    
private InfoStaticViewManager getManager() {
     if(null == mgr) {
         mgr = new InfoStaticViewManager();
     }
     return mgr;
}
```

同理

**javascript 代码**

```javascript
var PortalRemote =  function(defaultServiceName) {
    this.defaultServiceName = defaultServiceName;  //设置service
}
```

**sql**

```sql
--我是注释
SELECT LastName,FirstName FROM Persons;
--我是注释
SELECT * FROM Persons;
```
各式各样的代码段都基本支持高亮显示，阅读起来有木有很爽！

- - -

### 表格
*把一系列文本精心组织起来，我们甚至可以得到一个表格。我们需要把表头的那一行用一串横杠（-）隔出来，然后把每一列用竖杠（|）隔开：*
```
第一行标题 | 第二行标题 
------- | -------
表格元素 | 表格元素
表格元素 | 表格元素
表格元素 | 表格元素
表格元素 | 表格元素
```
效果如下：

第一行标题 | 第二行标题 
----- | -----
表格元素 | 表格元素
表格元素 | 表格元素
表格元素 | 表格元素
表格元素 | 表格元素

好了到此为止就，介绍这么多了，这基本涵盖了常用的几种语法，熟练使用以上语法，绝壁可以写出很好，很工整的文档。而且会发现效率要比富文本编辑器高的多。
