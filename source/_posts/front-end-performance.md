---
title: front_end_performance
date: 2017-09-22 11:08:23
tags: [html, http, frontEnd]
---

# 前端性能优化

  前端性能优化涉及到从服务器到协议再到宿主环境本身等对方面的知识，业界目前还是以雅虎总结出来的[35条前端性能优化的黄金军规](http://www.cnblogs.com/siqi/p/3655436.html)为主；
  web前端优化的目标就是如何让用户更快、更简单易用、更为流畅地使用我们的服务，对于前端来说就是如何让我们的资源体积更小、数量更精简、内容更早呈现，交互更加人性化；业内有一条比较公认的二八原则，就是资源从服务器处理完下发到客户端的浏览器上所占的时间比例大概是整个过程的20%，也就是说服务器端可以优化的空间的效率提升并不会很明显，前端性能优化成为web性能优化重点考虑的领域；

<!--more-->

* ## 浏览器宿主环境

  1、突破单线程解析渲染阻塞限制
    浏览器是一个单线程解析模式去解析渲染从服务器端拿到的html文本，css加载的过程会对后续的脚本资源加载造成阻塞，脚本的加载也会阻塞后续DOM结构的解析造成页面的留白时间增长，
一般建议把样式文件放在头部，脚本文件放在DOM节点最末尾，减少阻塞；
    针对不需要DOM操作的脚本文件可以采用动态创建script的方式载入，动态载入的脚本不阻塞后续资源的加载；
    脚本文件加载可以加上defer或者async属性表示防止阻塞；

  2、利用事件冒泡特性
    浏览器时间注册有3个级别定义，DOM0级事件注册（利用DOM元素行内事件属性onclick注册事件回调），DOM1级事件注册（利用DOM元素对象的onclick API在外部注册回调事件），DOM2级事件注册（利用DOM元素对象的addEventListener／attachEvent API在外部注册事件回调）。这里建议就是利用DOM2级在目标DOM的父标签注册回调，收拢事件监听入口同时节约了DOM节点引用开销；
  
  3、避开Cookie性能bug
    Cookie是前端作为前后台登录校验最通用的缓存方案，但浏览器在每次都会往同域名的任何资源的http请求中自动带上cookie信息，但是像css、js、image、这些资源请求是不需要cookie信息的，所以就会造成无端的带宽浪费。优化方案就是CDN异域静态资源服务器部署我们的前端css、js、image资源；

  4、突破浏览器并发连接限制
    浏览器针对domain而非页面page做并发连接限制的特性，domain hash的技术优化方案的处理方案是将资源划分域分开部署，但是过多的域划分会增加多余的DNS开销；
    
  5、利用GPU硬件加速浏览器渲染
     针对一些界面渲染过程比较耗时的情况，可以利用CSS3属性开启GPU来加速渲染我们的DOM，一般利用-webkit-transform:translateZ(0)假的3D属性来唤起系统GPU加速渲染功能；
     
* ## 减少http请求数量
 
  1、css、script合并：gulp、webpack可以很简单地通过任务脚本的方式自动化解决；
  2、css sprites雪碧图：将网站中常用的一些小图片整合到一张大图上，然后样式里通过background-position二维坐标找到需要的图片；
  3、font-icon字体图标：字体图标由于是矢量的可以解决图像像素放大变虚的问题；
  4、图片base64编码传输：base64编码后，可以减少自身的一次http请求；
  5、图片延时加载：主要用来减少首屏一次性加载图片的数量；具体做法是，给图片标签添加一个行内属性data-image，然后监听浏览器的滚动事件。。。请脑补后续做法

* ## 缓存机制

   1、协议缓存方案：利用http缓存协议头cache-control做304缓存，或者更精确的ETAG设置依据资源的修改时间来设置缓存方案；
   2、appCache应用缓存方案；
   
* ## 减少http数据请求大小

   1、css、script、图片压缩：可以通过gulp、webpack自动化脚本定义脚本任务完成；
   2、服务器开启gzip压缩，压缩率在30%以上；