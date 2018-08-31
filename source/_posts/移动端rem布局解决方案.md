---
title: 移动端rem布局解决方案
date: 2018-08-30 15:32:46
categories: 移动端开发
tags:
 - h5
 - 移动端
 - 工具开发
---

> 在移动端自适应方面，百分比布局有其局限性，媒体查询又太过繁琐，vw方案过于激进，说到底，还是rem敦实可靠。

在文章[使用Flexible实现手淘H5页面的终端适配](https://github.com/amfe/article/issues/17)中，讲述了rem布局的方案。但是这种修改viewpoint的方案，也存在一些局限性，比如三方库的显示异常等。到后来，在[flexible2.0](https://github.com/amfe/lib-flexible)中，已经放弃了这种hack行为，直接设置html的font-size完事。

<!--more-->

为了节省体力，官方推出了[px2rem](https://www.npmjs.com/package/px2rem)以及对应的[postcss-px2rem](https://www.npmjs.com/package/postcss-px2rem)，结合脚手架工具，直接在代码中写px单位即可，工具会自动帮你转为rem。

但是在新的条件下，这两个工具存在一些问题。flexible2.0中，已经不会对viewopint进行修改，因此不需要针对多种dpr设置多个字体，因此下面这种转换就变得毫无必要。

```css
.selector {
  width: 150px;
  height: 64px; /*px*/
  font-size: 28px; /*px*/
  border: 1px solid #ddd; /*no*/
}
```

```css
.selector {
  width: 2rem;
  border: 1px solid #ddd;
}
[data-dpr="1"] .selector {
  height: 32px;
  font-size: 14px;
}
[data-dpr="2"] .selector {
  height: 64px;
  font-size: 28px;
}
[data-dpr="3"] .selector {
  height: 96px;
  font-size: 42px;
}
```

我们需要的是，设置一个font-size，并且确保其不会被转换为rem单位即可。

按照原有的px2rem规则，要达到这种效果，需要按照以下写法：

```Css
.selector {
  font-size: 28px; /*no*/
}
```

但是这种写法比较麻烦，并且可能出现疏漏。因此在px2rem基础上，开发了[px2rem-more](https://www.npmjs.com/package/px2rem-more)，设置***keepFontSize***属性，且默认为true，不会对font-size进行转换。



再者，postcss-px2rem在进行转换时，会将三方库中的px单位进行转换，在使用第三方的组件库时，会导致尺寸异常。因此，在插件[postcss-px2rem-exclude](https://www.npmjs.com/package/postcss-px2rem-exclude)基础上，将内置px2rem替换为px2rem-more，开发了[postcss-px2rem-more](https://www.npmjs.com/package/postcss-px2rem-more)，添加exclude配置项，用来排除不进行转换的三方库。

# 结论

最终解决方案如下：

- 使用flexible2.0
- 三方组件库使用px作为单位
- 项目本身通过postcss-px2rem-more，将px转为rem，且保持字体不变

