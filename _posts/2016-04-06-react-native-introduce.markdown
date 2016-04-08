---
layout:     post
title:      "搜易贷在react-native的实践"
subtitle:   ""
date:       2016-04-06 15:00:00 +0800
author:     "Terry"
header-img: "http://7xqrig.com1.z0.glb.clouddn.com/img/post-bg-2015.jpg"
tags:
    - react-native
    - react
---


## react-native是什么

其实就是一个用js写ios和android客户端的框架。你可以写好了插进现有的Native app中，也可以用它写一个全新的。
官方鼓吹他是：learn once, write anywhere.

当然，他运用了很多技术和编程思想，比如强制组件化，单项数据流传递，对于前端同学有重大意义的Virtual Dom，把APP看做有限状态机，然后引入immutable object等。

这些概念与思想网上一搜一大堆，这些也的的确确是RN最具革命性的一点，但搜易贷是一家从实践中来到实践中去的公司，编程哲学光看是理解不了的，因此这里就不做介绍了，本文主要介绍搜易贷在RN方面的实践与探索。
	

---

## 来，看看原理

图片来自[IBM developerWorks](http://www.ibm.com/developerworks/library/mo-bluemix-react-native-ios8/)，上面介绍会更加具体。

![rn](http://www.ibm.com/developerworks/library/mo-bluemix-react-native-ios8/Figure1.png)

这里我非常简单的介绍一下：

- 从左往右第一根虚线，是调试时候和chrome通信的；
- 从左往右第二根实线，就是你的js代码执行的地方；
- 绿框中的唯一一个橙色的矩形，就是RN最核心的部分，桥。这座桥的作用就是让JS代码可以调用native的API（暴露什么接口，还取决于你，不暴露还是没法调用）。
- 右上角的实线矩形，是专门用来绘制界面的模块。RN将界面部分做了封装，可以做到用native来绘制界面。
- 右下角的虚线矩形，就是可以被调用的API

**桥怎么做到的**

> native中有一个模块可以执行javascript的函数并传参，然后拿到函数的返回值。参数就是native给js传递信息，返回值就是js给native传递信息，剩下的就是约定了。


## 和其他库或插件的结合情况

非常建议与redux结合，用redux来管理APP的状态，可以极大地提升状态的可控性。当然，使用redux需要你点亮redux的技能树。官方文档看一下，理解并不难。当然，不结合redux也是可以的。

CodePush是必须要用的，绕过IOS两周的审核，远程更新，错误回滚。

推荐一个 boilerplate, [snowflake](https://github.com/bartonhammond/snowflake)，里面包含RN, redux的结合，和对于第三方插件的引入，在redux中状态的管理都有很强的指导性。




## 实际运用的情况如何

官方的guide做的还是比较完善的，很容易上手，建议在使用RN之前先把react过一遍，做一下react的guide，了解react是个什么东西即可。

目前的社区较为不完善的情况下，使用RN仍然需要自己编写很多组件。并且存在一些需求仅依靠前端无法完成，需要native的支持（native module）。当然，如果产品对功能要求不是那么严格，允许做出一些妥协，那么从前端来讲，是有替代方案的。

截止目前，我们做了一些控件出来。在与redux的结合上，我们发现，对于状态的管理，redux可以更专注于与数据和APP全局相关的状态管理。而针对组件内部的状态，还是沿用react自身的setState即可。同时，组件层级不宜太深，我们最深不超过三个层级：

* Component（包括原有的系统组件和少部分定制组件）
* Module（比如一张表，图片上传和预览的组件，form表单）
* page（单个页面）

这样可以有效的避免多级 props 传参的问题。并且在 page 层，我们就直接与 redux store 相接取数据。

**关于坑：**

RN的坑基本到了现在0.22 stable已经填的差不多了。但是在交互方面，坑还是有的。这里的交互方面指，表单提交，图片上传之类的。这些坑都需要你去手动填，并且由于RN官方版本跑的太快，文档对于很多很重要的细节都没有说明，stackoverflow反而是更好的解决问题探索文档之外的API的地方。

另外，关于流畅度，IOS没有问题，android上较差的机型可能会有卡顿，尤其是路由的跳转，因此遇到单独做优化。


## RN 和其他hybrid App的区别

这一点之所以拿出来单独说是因为有很多人都误会了RN，认为它只不过是另一个phoneGap，cordova，ionic。其实并不是。跟RN比较类似的还有一个叫NativeScript，他们两个与PhoneGap, Cordova, Ionic这些最大的不同在于，他们是在用native绘制页面，而不是webview。在整个RN中，和webview一点关系都没有。相比之下，PhoneGap, cordova他们虽然可以调用原生API，但并不能用native绘制页面，对于页面的绘制，依然要依靠webview里的dom。这也是为什么目前为止市面上所有的基于webview的hybrid app都有两大问题无法解决，一是兼容性。二是页面性能。

## 写到最后

本文通篇基本没讲基本概念，对于初识RN的同学不太友好，原因是网上写guide的人实在太多，搜易贷技术部一直都致力于做点不一样的事情。因此新司机可以先搜一搜，看一看，玩一玩，再来看这篇文章，或许会更有感觉。当然，如果玩6了，也欢迎扔个简历当面交流。zhaotai@souyidai.com

