---
title: HTML5 标签列表
date: 2019-04-08 20:48:51
tags:
    - HTML5
keyword: 
    - 标签
    - 语义化
description: 整理Html5中所有的标签及标签作用
---

[mdn](https://developer.mozilla.org/zh-CN/docs/Web/Guide/HTML/HTML5/HTML5_element_list)

## 根元素

|标签|描述|
|---|---|
|\<html\>|文档根节点。其他所有元素必须是这个元素的子节点|

## 文档元数据

|标签|描述|
|---|---|
|\<head\>|文档元数据的集合，包括脚本或样式表的链接或内容|
|\<title\>|文档的标题，将显示在浏览器的标题栏或标签页上。该元素只能包含文本，包含的标签不会被解释。|
|\<base\>|定义页面上相对URL的基准URL|
|\<link\>|用于链接外部的css到该文档|
|\<meta\>|定义其他HTML元素无法描述的元数据|
|\<style\>|用于内联css|

## 脚本

|标签|描述|
|---|---|
|\<script\>|定义一个内联脚本或链接到外部脚本|
|\<noscript\>|当浏览器不支持脚本时显示的替代文字|
|\<template\>|通过js在运行时实例化内容的容器（不会显示）|

## 章节

|标签|描述|
|---|---|
|\<body\>|代表HTML文档的内容。在文档中只能有一个\<body\>元素|
|\<section\>![update](/images/binary_indexed_tree/update.jpg)|定义文档中的一个章节|
|\<nav\>![update](/images/binary_indexed_tree/update.jpg)|定义只包含导航链接的章节|
|\<article\>![update](/images/binary_indexed_tree/update.jpg)|定义可以独立于内容其余部分的额完整独立内容块|
|\<aside\>![update](/images/binary_indexed_tree/update.jpg)|定义和页面内容关联度较低的内容——如果被删除，剩下的内容依然合理|
|\<h1\>, \<h2\>, \<h3\>, \<h4\>, \<h5\>, \<h6\>|标题元素实现了六层文档标题。标题元素简要地描述章节的主题|
|\<header\>![update](/images/binary_indexed_tree/update.jpg)|定义页面或章节的头部。它经常包含logo、页面标题和导航性的目录|
|\<footer\>![update](/images/binary_indexed_tree/update.jpg)|定义页面或章节的尾部。它经常包含版权信息、法律信息链接和反馈建议用的地址|
|\<address\>|定义包含联系信息的一个章节|
|\<main\>![update](/images/binary_indexed_tree/update.jpg)|定义文档中主要或重要的内容|

## 组织内容

|标签|描述|
|---|---|
|\<p\>|定义一个段落|
|\<hr\>|代表章节、文章或其他长内容中段落之间的分隔符|
|\<pre\>|代表其内容已经预先排版过，格式应当保留|
|\<blockquote\>|代表引用来自其他来源的内容|
|\<ol\>|定义一个有序列表|
|\<ul\>|定义一个无序列表|
|\<li\>|定义列表中的一个列表项|
|\<dl\>|定义一个定义列表（一系列术语和其定义）|
|\<dt\>|代表一个由下一个\<dd\>定义的术语|
|\<dd\>|代表出现在它之前的术语的定义|
|\<figure\>![update](/images/binary_indexed_tree/update.jpg)|代表一个和文档有关的图例|
|\<figcaption\>![update](/images/binary_indexed_tree/update.jpg)|代表一个图例的说明|
|\<div\>|代表一个通用的容器，没有特殊含义|

## 文字形式

|标签|描述|
|---|---|
|\<a\>|代表一个链接到其他资源的超链接|
|\<em\>|代表强调文字|
|\<strong\>|代表特别重要的文字|
|\<small\>|代表注释，如免责声明、版权声明等，对理解文档不重要|
|\<s\>|代表不准确或不相关的内容|
|\<cite\>|代表作品标题|
|\<q\>|代表内联的引用|
|\<dfn\>|代表一个术语包含在其最近祖先内容的定义|