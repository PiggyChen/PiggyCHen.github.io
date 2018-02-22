---
title: Markdown语法详解
date: 2017-01-23 15:49:00
meta: [Markdown,hexo.github]
tags: [Markdown,hexo,github]
---
hexo编写博客内容使用**Markdown**格式的语法，下面就详细讲讲Markdown语法知识。  

<!-- more -->  

### 粗体字和斜体字  
__粗体字__: `**粗体字**` 或者 `__粗体字__`  
*斜体字*: `*斜体字*` 或者 `_斜体字_`  
### 标题  

```
标题1
=======
标题2
--------  

或者  

# 标题1  
## 标题2  
### 标题3  
#### 标题4  
##### 标题5  
###### 标题6 
```  
### 链接和Email  
用尖括号将 **Email** 或 **地址** 包裹，即可使地址可点击:  
<piggychen@hotmail.com>:  `<piggychen@hotmail.com>`  
<https://piggychen.github.io>:   `<https://piggychen.github.io>`  

也可以实现点击链接文字跳转:  
[点我跳转](https://piggychen.github.io):  `[点我跳转](https://piggychen.github.io)`  

### 引用格式  
有时在文本中引用了较长的url链接看起来会比较乱，或者想让所有的url链接放到一起，则可以使用以下这种格式:  
[a link][arbitrary_id]: `[a link][arbitrary_id]`然后在该文件的任意位置键入：  

`[arbitrary_id]: https://piggychen.github.io`  

或者链接文本本身看起来就是一个独一无二的id，则可以省略后边的`arbitrary`不写:  

[like this][]:`[like this][]`，然后在该文件任意位置键入:  

`[like this]:https://piggychen.github.io`  

[arbitrary_id]: https://piggychen.github.io  
[like this]: https://piggychen.github.io  

### 图片  
![Alt Image Text](/img/bilibili.png)  
`![Alt Image Text](/path/or/url/to.JPG)`  

或者可以用引用格式展示图片:  
![AltImage Text][image-id]  
`![Alt Image Text][image-id]`  

然后在该行或者任意位置指定:  
[image-id]: /img/bilibili.png  
`[image-id]: /path/or/url/to.JPG`  

### 列表  

* 列表之前必须留有一空行  
* 无序列表每一项前面用`*`标识  
* 通过缩进可以在列表中创建一个子列表  
1.数字加点号可以用来标识有序列表  
2.标识有序列表的每一项只需用数字点开头即可，如:`1.`  

以上列表的代码如下:  

```  
* 列表之前必须留有一空行  
* 无序列表每一项前面用`*`标识  
* 通过缩进可以在列表中创建一个子列表  
1.数字加点号可以用来标识有序列表  
2.标识有序列表的每一项只需用数字点开头即可，如:`1.` 
```  

### 整块内容的引用  
> 文本中如果涉及到对其他地方内容的引用，可以用 **`>`** 来标识。如果段落中没有空行的话，当文本内容过多而另起一行的情况下，无需再次在行的开头使用 **`>`**  
> > 同样也可以在块引用中创建子块内容的引用
> > > 第三层级块引用同样可行
>  
> 其他一些Markdown语法，在区块中同样适用  
> * Lists
> * [Link][arbitrary_id]  
> * Etc.

代码：  

```  
> 文本中如果涉及到对其他地方内容的引用，可以用 `>` 来标识。如果段落中没有空行的话，当文本内容过多而另起一行的情况下，无需再次在行的开头使用 **`>`**  
> > 同样也可以在块引用中创建子块内容的引用
> > > 第三层级块引用同样可行
>  
> 其他一些Markdown语法，在区块中同样适用  
> * Lists
> * [Link][arbitrary_id]  
> * Etc.
```  

### 内联代码  
有时候文本内容中需要附加代码，例如: `string name = piggychen;`，可以使用重音符  **`**  将要显示的代码包裹:  

```  
`string name = piggychen`
```  
如果重音符本身也是代码中要显示的一部分 `` code has `backticks` `` ，那么可以使用两个重音符:```` `` code has `backticks` `` ````。  

### 代码块  
三个重音符包裹的代码组成一块代码块:    
![block code](/img/blockcode.png)  

### 水平分割线  
`***` 或者 `---` 可用于显示水平的分割线:
***  

### 表格
这是一张表格:  

First Header  | Second Header
------------- | -------------
Content Cell  | Content Cell
Content Cell  | Content Cell
代码如下:

```  
First Header  | Second Header
------------- | -------------
Content Cell  | Content Cell
Content Cell  | Content Cell
```  

你也可以使用以下语法排列表格内容:  

```  
| Left Aligned  | Center Aligned  | Right Aligned |
|:------------- |:---------------:| -------------:|
| col 3 is      | some wordy text |         $1600 |
| col 2 is      | centered        |           $12 |
| zebra stripes | are neat        |            $1 |
```  

| Left Aligned  | Center Aligned  | Right Aligned |
|:------------- |:---------------:| -------------:|
| col 3 is      | some wordy text |         $1600 |
| col 2 is      | centered        |           $12 |
| zebra stripes | are neat        |            $1 |

表格最左侧和最右侧的 `|` 只是为了美观才加上的，是可以省略的。表格中的空格也可以省略，表格每项的对其方式只依赖于表头的 ｀:｀ 标志。  

**以上就是一些我在编写博客的时候会用到的Markdown基础语法知识，希望能对你有所帮助^_^。**



















