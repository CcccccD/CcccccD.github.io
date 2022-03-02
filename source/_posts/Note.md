---
title: 笔记
date: 2022-03-02 13:34:55
tags: 笔记
categories: 前端
---

## 笔记整理


### CSS相关

 #### 相邻的两个inline-block节点为什么会出现间隔

        元素被当成行内元素排版的时候，元素之间的空白符（空格、回车换行等）都会被浏览器处理，
        根据white-space的处理方式（默认是normal，合并多余空白），
        原来HTML代码中的回车换行被转成一个空白符，在字体不为0的情况下，
        空白符占据一定宽度，所以inline-block的元素之间就出现了空隙。
        这些元素之间的间距会随着字体的大小而变化，当行内元素font-size:16px时，间距为8px。


            1. 设置font-size=0
            2. HTML代码中去除换行，空格
            3. 用margin负值抵消
        
#### 如何从html元素继承box-sizing
```     
            html {
                box-sizing: border-box;
             }
            *, *:before, *:after {
                box-sizing: inherit;
            } 
```

#### 如何检测浏览器所支持的最小字体大小？
        可以使用 JS 设置 DOM 的字体为某一个值，然后再取出来，如果值设置成功，就说明支持。

#### Js 动画与 CSS 动画区别及相应实现
    CSS3 的动画的优点
        在性能上会稍微好一些，浏览器会对 CSS3 的动画做一些优化
        代码相对简单
    缺点
        在动画控制上不够灵活
        兼容性不好
        JavaScript 的动画正好弥补了这两个缺点，控制能力很强，可以单帧的控制、变换，
        同时写得好完全可以兼容 IE6，并且功能强大。
        对于一些复杂控制的动画，使用 javascript 会比较靠谱。
        而在实现一些小的交互动效的时候，就多考虑考虑 CSS 吧。
        requestAnimationFrame替代setTimeOut

#### IconFont 的原理是什么
    IconFont 的使用原理来自于 css 的 @font-face 属性。

    这个属性用来定义一个新的字体，基本用法如下：
```
            @font-face {
                font-family: <YourFontName>;
                src: <url> [<format>],[<source> [<format>]], *;
                [font-weight: <weight>];
                [font-style: <style>];
            }
```
font-family：为载入的字体取名字。
src：[url]加载字体，可以是相对路径，可以是绝对路径，也可以是网络地址。[format]定义的字体的格式，用来帮助浏览器识别。主要取值为：【truetype(.ttf)、opentype（.otf）、truetype-aat、embedded-opentype(.eot)、svg(.svg)、woff(.woff)】。
font-weight：定义加粗样式。
font-style：定义字体样式。

#### display:none与visibility:hidden 有什么区别？
1. 表现上
            display:none是彻底消失，不在文档流中占位，浏览器也不会解析该元素；
            visibility:hidden是视觉上消失了，可以理解为透明度为0的效果，在文档流中占位，浏览器会解析该元素；
2. 性能上
            使用visibility:hidden比display:none性能上要好，display:none切换显示时，
            页面产生回流（当页面中的一部分元素需要改变规模尺寸、布局、显示隐藏等，页面重新构建，
            此时就是回流。所有页面第一次加载时需要产生一次回流），
            而visibility切换是否显示时则不会引起回流。

#### html和css中的图片加载与渲染规则是什么样的
简单的归纳就是浏览器渲染Web页面大约会经过六个过程：

            解析HTML，构成DOM树
            解析加载的样式，构建样式规则树
            加载JavaScript，执行JavaScript代码
            DOM树和样式规则树进行匹配，构成渲染树
            计算元素位置进行页面布局
            绘制页面，最终在浏览器中呈现

图片加载和渲染的时机有可能是下面这样：

            解析HTML时，如果遇到img或picture标签，将会加载图片
            解析加载的样式，遇到background-image时，并不会加载图片，而会构建样式规则树
            加载JavaScript，执行JavaScript代码，如果代码中有创建img元素之类，会添加到DOM树中；
            如查有添加background-image规则，将会添加到样式规则树中
            DOM树和样式规则匹配时构建渲染树，如果DOM树节点匹配到样式规则中的backgorund-image，则会加载背景图片
            计算元素（图片）位置进行布局
            开始渲染图片，浏览器将呈现渲染出来的图片
            
什么时候会真正的加载，加载规则又是什么？

            `<img>`、`<picture>`和设置background-image的元素遇到display:none时，图片会加载但不会渲染
            `<img>`、`<picture>`和设置background-image的元素祖先元素设置display:none时，
            background-image不会渲染也不会加载，而img和picture引入的图片不会渲染但会加载
            `<img>`、`<picture>`和background-image引入相同路径相同图片文件名时，图片只会加载一次
            样式文件中background-image引入的图片，如果匹配不到DOM元素，图片不会加载
            伪类引入的background-image，比如:hover，只有当伪类被触发时，图片才会加载

#### CSS中的 “flex:1;” 是什么意思？
                flex 是 flex-grow, flex-shrink 和 flex-basis的简写。
                除了auto (1 1 auto) 和 none (0 0 auto)这两个快捷值外，还有以下设置方式：
                当 flex 取值为一个非负数字，则该数字为 flex-grow 值，flex-shrink 取 1，flex-basis 取 0%

#### 什么是BFC
                BFC是Web页面的可视CSS渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

                根元素，即html
                float的值不为none（默认）
                overflow的值不为visible（默认）
                display的值为table-cell, table-caption, inline-block, flex, 或者 inline-flex 中的其中一个
                position的值为absolute或fixed

#### display:none: 会让元素完全从渲染树中消失，渲染的时候不占据任何空间, 不能点击，
            visibility: hidden:不会让元素从渲染树消失，渲染元素继续占据空间，只是内容不可见，
            不能点击 opacity: 0: 不会让元素从渲染树消失，渲染元素继续占据空间，只是内容不可见，可以点击

#### css加载会造成阻塞吗

        css加载不会阻塞DOM树的解析
        css加载会阻塞DOM树的渲染
        css加载会阻塞后面js语句的执行、
