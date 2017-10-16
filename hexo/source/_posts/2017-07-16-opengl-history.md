---
title: OpenGL 1 从OpenGL的角度看三维计算机图形库的发展
date: 2017-07-16 15:14:32
categories: "技术积累" #文章分類目錄 可以省略
tags: #文章標籤 可以省略
     - opengl
     - 历史
description: #你對本頁的描述 可以省略
---

对于可视化，从来没有系统地了解相关的知识，对相关工作确实也没有切实有效的思路。因此决定趁在法国期间系统地看一下相关的知识。在此作一些记录也算是督促自己的学习吧。

就从OpengGL这一历史悠久的API库开始我的旅程吧。

以史为鉴可以明得失，这次我不再跳过history了，从[OpengGL官网的对其历史的介绍的文章][1]一路看下来。

OpenGL是对底层图形硬件的封装，是对程序员开放的一些接口的规范以及各大厂商的底层实现的集合。
这篇文章从使用灯泡/纸带的时代说起，一直讲到了和Direct3D相爱相杀的过程中互取所长，进化至今形成OpengGL3.1版本的过程。

## 从无到有

计算机发明之初，技术人员和科学家们仅能够通过连着计算机的灯泡或者长长的纸带来获取计算机输出的信息。翻译难度无异于现在人肉识别二维码。一方面刚发明的计算机的计算能力略弱，如果还要从中抽一部分来翻译输出也嫌浪费；另一方面这星辰大海还等待能人异士的到来。

Ferdinand Braun在1897年发明了显像管（CRT），是近一个世纪的显示设备的主要形态。1951年MIT在为海军研发Whirlwind计算机期间，使用了CRT进行了简单的程序输出，从而避免了翻译灯泡和纸带等冗余工作。尽管Whirlwind由于运行成本过高而不甚成功，但是作为一个开创式的里程碑，CRT在之后的半个世纪成为了计算机输出的主要设备。很多时候，要理解一件事情还是需要看一看实物，比如要知道，当时的计算机的内存是下面这玩意~~

![Whirlwind内存模块](https://upload.wikimedia.org/wikipedia/commons/thumb/1/19/8868-Project-Whirlwind-CRMI.JPG/220px-8868-Project-Whirlwind-CRMI.JPG)

此后，1961年Ivan Sutherland发明了可和计算机交互的Sketchpad，成为了鼠标的前身。从此，计算机不仅可以看，还可以实时交互地看，往后一切这一切就飞速发展起来了，直到可视化可以媲美甚至超越现实，而与这虚拟的现实的交互可以媲美现实。。。

## 飞速发展的个人电脑

随着计算机的计算能力的飞速发展，计算机发展至四代计算机，出现个人计算机。为了让计算机能够更容易地为普通人掌握，20世纪80年代计算机的交互界面由命令行的方式发展出了图形交互界面。嗯？和我的时间线不太一致，我上初中时香港都回归了，都快来千年虫了我们学校还用着DOS操作系统。

当然，80年代单机游戏主要都运行在特殊的硬件上（FC啦之类的）。而三维图形主要还是应用在可视化领域，因为个人电脑那点运算能力还不够格运行实时的三维图形系统。

而这一状况到90年代得到了极大的改观，因为这时个人电脑的计算能力有了长足的进步，而游戏也开始做到PC上了。比如划时代的3D游戏Doom。

这个时候有啥问题呢，主要就是不同的图形系统是运行在不同的硬件上的。各家硬件厂商是卖电脑送软件，程序员是直接基于各自的程序库来直接操作图形硬件的。这样很多人的青春都花费在了给不同的硬件做移植上了。

## OpenGL的第一个时代

OpenGL的前身是Silicon Graphics（SGI）开发的IRIS GL，当时SGI凭借自身硬件系统的性能优势，使IRIS GL成为了业界事实上的3D图形库标准。但是尽管该图形库非常流行，但是由于SGI的图形库与其硬件深度结合，其竞争对手均使用了各自的程序接口并逐渐追上了原本处于领先位置的SGI。

这样的情况下，SGI棋走险招，在1992年将IRIS GL中与计算机图形不相关的部分剥离形成了OpenGL并对外开放。OpenGL就此向跨平台、标准实时图形库的方向迈进了。

从此，软件开发商（操作系统开发商）必须在他们的系统上提供OpengGL实现；而硬件开发商需要对OpenGL提供驱动支持。OpenGL就此走上了康庄大道，程序员和老板们也都为能用上好用的图形接口程序而欢呼雀跃。

## OpenGL的特点

### 第一个特点就是灵活
灵活是因为SGI开放的并不是源码，而是一些实现这一套OpenGL的API的准则。因此不同的软件平台可以有不同的实现。而实现的不同并不影响基于这同一套API的应用级程序的实现。

此外，OpenGL支持扩展，不同的硬件供应商可以根据特定的需求提供扩展的API。当然啦，这种东西可以说灵活了，对跨平台的程序又是一道移植的门槛哦。

### 第二个特点是开放
开放嘛，就不提了，各种硬件商、软件商都可以实现自己的OpenGL底层和API。横跨ios, linux, windows等平台，甚至衍生出ES和WebGL这种种体系。你说不开放我把键盘吃了。

## 和Direct3D的故事
微软为了稳固其桌面操作系统的地位，硬是收购了RenderMorphics 并将其Reality Lab捋了捋形成了Direct3D。在发展之初Direct3D不给力阿，程序员怨声载道，不得已微软还是实现了一套1.1版本的OpenGL库，当然啦，这是最后一次他们更新的OpenGL。

为了杀人于无形，微软搞了一个MCD，本来这是个挺好的东西，可以让用户分离硬件加速与软件计算方式，但是呢，不让OpenGL用硬件加速方式，硬生生把OpenGL搞成GDI一样的效率。当然啦，SGI不是吃素的，搞出一个ICD，让OpenGL还是可以在Windows上进行硬件加速，哈，就是这样，OpenGL和Direct在windows上开始了长期的竞争。

微软不是吃素的，应硬件的发展率先在Direct 3D上搞出了非立即模式的渲染方式，发展了shaders方法等等。而感觉OpenGL这样的组织方式反应略迟缓，在这一点上晚了一步哦。

为了理解立即模式的缺点，这是原话
>To get things to render on the screen up until this point, programmers issued lists of commands from their program that would be interpreted by the GPU, called the immediate mode. This methodology performed fine for smaller data sets, but with larger data sets, performance was hindered by the performance of the CPU since all function calls originated from the program itself.

## 奋起直追到4.0
最好的朋友其实是敌人，这一点我们老板也经常提起。就像谷歌之于百度，像苹果之于三星。
Direct3D简直是教OpenGL的最好的老师，OpenGL在3.1版本中逐渐废弃了立即模式，而在4.0中，干脆已不支持立即模式。
现在开始学OpenGL的话，可以抛弃以前那一套立即模式的API了，让我们拥抱新的支持大数据量的buffer和shaders吧。

## 结语

这里面一句话说的非常好：*The requirement for visual feedback is so important that it is hard to acknowledge the existence of something if you cannot see it. Bacteria, for instance, were purely speculative before their visual discovery in the seventeenth century by Antonie van Leeuwenhoek who invented the microscope, but became an integral part of modern science.*，可视化对于人来说，是对现实存在的感知的重要的不可替代的途径。

看了这么久，总想一两句总结一下这段历史。我想应该也就是两句话吧，一方面，程序员要用得爽、一方面架构设计要能支持高效的运算。哼，这不是废话嘛，老了，只能说说废话了。


[1]: http://openglbook.com/chapter-0-preface-what-is-opengl.html
