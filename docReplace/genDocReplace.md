+++
title = "生成文档前的替换"
date = 2023-06-05T09:37:31+08:00
description = ""
isCJKLanguage = true
draft = false

+++

# 生成文档前的替换

```
{{< ref "">}}

// 在浏览器控制台中执行以下代码
var dlElements = document.querySelectorAll('dl');

dlElements.forEach(function(element) {        
    // 创建一个新的 div 元素
    var newDiv = document.createElement('div');
    
    // 复制 dl 元素的所有属性到新的 div
    Array.from(element.attributes).forEach(attr => {
        newDiv.setAttribute(attr.name, attr.value);
    });
    
    // 将 dl 元素的所有子节点移动到新的 div
    while (element.firstChild) {
        newDiv.appendChild(element.firstChild);
    }
    
    // 用新的 div 替换原来的 dl 元素
    element.parentNode.replaceChild(newDiv, element);
});

var dtElements = document.querySelectorAll('dt');

dtElements.forEach(function(element) {        
    // 创建一个新的 h2 元素
    var newh2 = document.createElement('h2');
    
    // 复制 dl 元素的所有属性到新的 h2
    Array.from(element.attributes).forEach(attr => {
        newh2.setAttribute(attr.name, attr.value);
    });
    
    // 将 dl 元素的所有子节点移动到新的 h2
    while (element.firstChild) {
        newh2.appendChild(element.firstChild);
    }
    
    // 用新的 h2 替换原来的 dl 元素
    element.parentNode.replaceChild(newh2, element);
});

var ddElements = document.querySelectorAll('dd');

ddElements.forEach(function(element) {        
    // 创建一个新的 div 元素
    var newdiv = document.createElement('div');
    
    // 复制 dd 元素的所有属性到新的 h2
    Array.from(element.attributes).forEach(attr => {
        newdiv.setAttribute(attr.name, attr.value);
    });
    
    // 将 dd 元素的所有子节点移动到新的 div
    while (element.firstChild) {
        newdiv.appendChild(element.firstChild);
    }
    
    // 用新的 div 替换原来的 dd 元素
    element.parentNode.replaceChild(newdiv, element);
});

// 在浏览器控制台中执行以下代码
var elements = document.querySelectorAll('p');

elements.forEach(function(element) {        
   var parent = element.parentElement; // 获取 element 的直接父元素
   if (parent.tagName != 'LI' && parent.tagName != 'TH' && parent.tagName != 'TD') {
   		element.firstChild.nodeValue = '&zeroWidthSpace;' +element.firstChild.nodeValue;
   }
});


var dtElements = document.querySelectorAll('dt');

dtElements.forEach(function(element) {        
    // 创建一个新的 div 元素
    var newDiv = document.createElement('div');
    
    // 复制 dl 元素的所有属性到新的 div
    Array.from(element.attributes).forEach(attr => {
        newDiv.setAttribute(attr.name, attr.value);
    });
    
    // 将 dt 元素的所有子节点移动到新的 div
    while (element.firstChild) {
        newDiv.appendChild(element.firstChild);
    }
    
    // 用新的 div 替换原来的 dl 元素
    element.parentNode.replaceChild(newDiv, element);
});


```

## 替换掉`&zeroWidthSpace;`

```
// 查找匹配如下字符
&zeroWidthSpace;

// 替换成 tab
​	
```

## 替换掉`> 原文: `中的链接存在锚的情况

```
// 查找匹配如下正则表达式
> 原文：\[([^#\]]+)#([^#\]]+)\]\(([^#\]]+)#([^#\]]+)\)
// 替换成
> 原文：[$1]($1)
```



## 替换掉`\>>>`开头的行

```
// 查找匹配如下字符
\>>>

// 替换成 空

```

## 替换掉`参见`后面的h2标题为普通字符串

```
// 查找匹配如下正则表达式
参见\n

```

## 替换掉`Added in version`

```
// 查找匹配如下正则表达式
^\*(Added in version[^\n]+)\*

// 替换成
> $1\n>
```

## 替换掉`*在 3.11 版本发生变更:*`等

```
// 查找匹配如下正则表达式
\*(在 \d+.\d+ 版本发生变更:)\*

// 替换成
> $1
```

## 替换掉`*自 3.6 版本弃用:*`等

```
// 查找匹配如下正则表达式
\*(自 \d+.\d+ 版本弃用:)\*

// 替换成
> $1
```



## 替换掉`## 作者:`  等等

```
// 查找匹配如下字符
## 作者:

// 替换成
作者:

// 查找匹配如下字符
## 发布版本:

// 替换成
发布版本:
```

## 替换掉代码块没有编程语言名称

```
// 查找匹配如下正则表达式
```\n>>>

// 替换成
``` python\n>>>


// 查找匹配如下正则表达式
```\n#!

// 替换成
``` sh\n#!


```



## 替换掉`**__`的字符串

```
// 查找匹配如下正则表达式
\*\*__([^\*\n\_]+)__\*\*

// 替换成
`__$1__`
```

替换掉`**--help**`等的字符串

```
// 查找匹配如下正则表达式
\*\*--([^\*\n]+)\*\*

// 替换成 空
`--$1`
```

