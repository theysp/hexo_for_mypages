---
title: IMGUI用法小结
date: 2017-07-29 10:48:05
categories: "技术积累" #文章分類目錄 可以省略
tags:
    - opengl
---

在本文中，“窗口”imgui在操作系统提供的窗口中创建的img界面容器。
“控件”指imgui在“窗口”中创建的界面元素。

## 字体导入与使用

由于是国内的项目，需要使用中文字体，因此借鉴[imgui主页](https://github.com/ocornut/imgui)的例子。
``` c++
ImGuiIO& io = ImGui::GetIO();
io.Fonts->AddFontFromFileTTF("ArialUni.ttf", 18.0f, NULL, io.Fonts->GetGlyphRangesJapanese());

// For Microsoft IME, pass your HWND to enable IME positioning:
io.ImeWindowHandle = my_hwnd;
```
![](https://raw.githubusercontent.com/wiki/ocornut/imgui/web/code_sample_01_jp.png)
可以实现相应的中文输入。

基本上imgui也是个状态机，字体的颜色、窗口的背景等等均可通过PushStyle和PosStyle实现，所以以往接触过opengl立即模式的话应该很快能理解。

目前imgui没有实现运行时字体动态大小切换功能，一个比较费事的方法是在导入字体时生成多种大小的字体，在程序运行中通过PushFont和PopFont实现。
此外，imgui可支持子界面的缩放，适当运用也可以缓解字体缩放的困难，如[#772 (comment)](https://github.com/ocornut/imgui/issues/772#issuecomment-244512595)所示。

## UI排版

主要是从[参考文章](http://blog.csdn.net/sunny__chen/article/details/51273726)中获取的信息。
imgui的排版建议开始按自动的来。
自动布局时，默认情况下，各控件由上到下依次显示。
可以使用GUILayout.BeginHoriztontal(),GUILayout.EndHorizontal(), GUILayout.BeginVertical(), and GUILayout.EndVertical().来控制各控件水平显示或垂直显示。可嵌套。

关于UI在主窗口的停靠，目前官方没有出排版套路，但是一些用户给出了解决方法。

目前我的做法是直接把子界面的位置固定在窗口内，只做一个隐藏弹出的切换。

## c++11支持utf8 + vs2017 community

支持utf8了！再也不用与编码纠缠了，再见GTK，再见GB。

## imgui窗口的定位

imgui所有的定位坐标都是相对于操作系统提供的总体窗口的。
imgui提供了一系列的函数用于窗口定位：
    IMGUI_API ImVec2        GetContentRegionMax();       //窗口右下角坐标（含需要滚动条才能到达的区域）
    IMGUI_API ImVec2        GetContentRegionAvail();     //窗口尺寸
    IMGUI_API float         GetContentRegionAvailWidth();//窗口尺寸宽度
    IMGUI_API ImVec2        GetWindowContentRegionMin(); //窗口左上角坐标
    IMGUI_API ImVec2        GetWindowContentRegionMax(); //窗口
    IMGUI_API float         GetWindowContentRegionWidth(); 
以及ImGui::GetIO().DisplaySize用于获得总体窗口的尺寸（在总体窗口缩放时会用到）。

## 隐去windows窗口标题栏

原文来自：[http://blog.csdn.net/LYP951018/article/details/24503207](http://bbs.csdn.net/topics/370099236)
> ————————以下为原文——————————
> case WM_SIZE:
>	{
>		LONG_PTR Style = ::GetWindowLongPtr(hWnd,GWL_STYLE);
>		Style = Style &~WS_CAPTION &~WS_SYSMENU &~WS_SIZEBOX;
>		::SetWindowLongPtr(hWnd, GWL_STYLE, Style);
>		break;
>	}

## 
