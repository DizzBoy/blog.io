---
layout: post
title: 此'懒'非彼'懒'——使用Jquery实现图片懒加载
date: 2018-07-30
categories: blog
tags: [Web,网站建设,前端开发,jQuery]
description: 文章金句。
---

## 背景

在实际的项目开发中，我们通常会遇见这样的场景：一个页面有很多图片，而首屏出现的图片大概就一两张。  

正常情况下，浏览器会解析整个HTML代码，然后从上到下依次加载```<img src="xxx">```的图片标签。如果页面很长，隐藏在页面下方的图片其实已经被浏览器加载了。如果用户不向下滚动页面，就没有看到这些图片，相当于白白浪费了图片的流量。

所以，淘宝、京东这些流量非常巨大的电商，商品介绍页又必须有大量的图片，因此，这些页面的图片都是“按需加载”，即用户滚动页面时显示出来的时候才加载图片。当网速非常快的时候，用户并不能感知懒加载的动作，既省流量又不影响用户浏览。

## 思路：
将页面里所有img标签的**src**属性用**data-xx**代替，当页面滚动直至此图片出现在可视区域时，用js取到该图片的**data-xx**的值赋给**src**。

## 实现

编写代码前，先让我们复习一下DOM中的各种区域对象。

~~~
页可见区域宽： document.body.clientWidth;
网页可见区域高： document.body.clientHeight;
网页可见区域宽： document.body.offsetWidth (包括边线的宽);
网页可见区域高： document.body.offsetHeight (包括边线的宽);
网页正文全文宽： document.body.scrollWidth;
网页正文全文高： document.body.scrollHeight;
网页被卷去的高： document.body.scrollTop;
网页被卷去的左： document.body.scrollLeft;
网页正文部分上： window.screenTop;
网页正文部分左： window.screenLeft;
屏幕分辨率的高： window.screen.height;
屏幕分辨率的宽： window.screen.width;
屏幕可用工作区高度： window.screen.availHeight;
~~~

好了,接下来我们开始动手~  

首先将网页中所有的img替换为以下样式：
~~~
<img src="/static/loading加载示意图片.gif" data-src="http://真正的图片地址/xxx.jpg">
~~~

加入jQuery脚本
~~~
$(function() {
    // 获取window的引用:
    var $window = $(window);
    // 获取包含data-src属性的img，并以jQuery对象存入数组:
    var lazyImgs = $('img[data-src]');
    // 定义事件函数:
    var onScroll = function() {
        // 获取页面已滚动的高度:
        var wtop = $window.scrollTop();
        // 判断是否还有未加载的img:
        if (lazyImgs.length > 0) {
            // 获取可视区域高度((注意考虑全屏和iframe)):
            var wheight = $window.height();
            // 循环处理数组的每个img元素:
            lazyImgs.each(function(){
                // 判断是否在可视范围内:
                if ($(this).offset().top - wtop < wheight) {
                    // 设置src属性:
                    $(this).attr('src', $(this).attr('data-src'));
                    //删除此元素
                    lazyImgs.splice($.inArray($(this),lazyImgs),1);
                }
            });
            };
        }
            // 绑定事件:
    $window.scroll(onScroll);
    // 手动触发一次:
    onScroll();
    });
~~~
onScroll()函数最后要手动触发一次，因为页面显示时，并未触发scroll事件。如果图片已经在可视区域内，这些图片仍然是loading状态，需要手动触发一次，就可以正常显示。


*PS:测试时请清理浏览器缓存,建议使用Chrome并将调试-Network界面中模拟3G慢速网络,并给预载图片src设置一个默认的loading图标,效果会非常明显~*

## 最终效果
![没有效果!](http://wx3.sinaimg.cn/large/006WKNnMgy1ftsctn4v02g30jz0ak4qp.gif)

>部分文档引用:  
[cnblogs-liliangel](https://www.cnblogs.com/liliangel/p/6122836.html)、[liaoxuefeng](https://www.liaoxuefeng.com/article/00151045553343934ba3bb4ed684623b1bf00488231d88d000)









