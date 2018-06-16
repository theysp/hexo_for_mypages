---
title: UNITY_LEARN/2018-06-17
date: 2018-06-17 00:07:47
categories: 技术积累 U3D #文章分類目錄 可以省略
tags:
    - U3D
---

Today, I followed the tutorial online, and finished the rounded mesh.

Some lessons learnt.

1. Setting vertices to the mesh means transfer from the RAM of CPU to the RAM of GPU. Though C# treat variables as reference, but it's neccessary to assign the vertices to the mesh after every edit process.
