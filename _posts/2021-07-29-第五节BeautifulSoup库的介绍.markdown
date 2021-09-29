---
layout: post
title:  "第五节BeautifulSoup库的介绍"
date:   2021-07-29 11:09:28 +0800
categories: 网络爬虫
---

上次课讲到如何使用 requests 库的 GET 方法来获取页面的 HTML 代码。本节课来讲下如何使用 Beautiful Soup 来解析 HTML 以获取我们想要的信息。

## 1.安装第三方库 
### 1.1 安装 Beautiful Soup
在命令行下通过 pip 安装  `pip install beautifulsoup4` 。
### 1.2 安装解析器 lxml
在命令行下通过 pip 安装 `pip install lxml` 。
## 2.使用 Beautiful Soup 提取信息
Beautiful Soup 将复杂 HTML 文档转换成一个复杂的树形结构，每个节点都是 Python 对象，所有对象可以归纳为 4 种：`Tag`，`NavigableString`，`BeautifulSoup`，`Comment`。在介绍对上述四种对象的提取时，使用的 HTML 代码为：
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>我的 Web 页面</title>
  </head>
  <body>
    <h1>Python 是一门优雅的语言。</h1>
    <img src="images/Python.png" alt="Python 语言 Logo" />

    <p class="python" name="application">使用 Python，我们可以做许多事情。</p>

    <ul>
      <li>Web 开发</li>
      <li>网络爬虫</li>
      <li>数据分析</li>
      <li>人工智能与机器学习</li>
      <li>自动化运维</li>
      <li>游戏开发</li>
    </ul>

    <p>人生苦短，我用 Python。</p>

    <p>
      如果想了解更多的 Python 语言的相关知识，可以查看
      <a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a
      >。
    </p>
  </body>
</html>
```
### 2.1 Tag
Tag 就是 HTML 中的标签，标签的概念，我们在「HTML 简介」中已经做了介绍，需要的同学可以回看下。下面我们来看下如何使用 Beautiful Soup 来提取 HTML 中的标签。首先，用 HTML 创建一个 Beautiful Soup 对象。
```python
soup = BeautifulSoup(html, 'lxml')
```
#### 2.1.1 各种标签的提取
##### 2.1.1.1 head 标签
```python
# head 标签
head = soup.head
print(head)
```
输出：
```html
<head>
<meta charset="utf-8"/>
<title>我的 Web 页面</title>
</head>
```
##### 2.1.1.2 title 标签
```python
# title 标签
title = soup.title
print(title)
```
输出：
```html
<title>我的 Web 页面</title>
```
##### 2.1.1.3 a 标签
```python
# a 标签
a = soup.a
print(a)
```
输出：
```html
<a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>
```
##### 2.1.1.4 p 标签
```python
# p 标签
p = soup.p
print(p)
```
输出：
```html
<p class="python" name="application">使用 Python，我们可以做许多事情。</p>
```
#### 2.1.2 标签的属性
标签有两个重要的属性：name 和 attrs。
```python
name = soup.p.name
attrs = soup.p.attrs
print(name)
print(attrs)
```
输出：
```python
p
{'class': ['python'], 'name': 'application'}
```
p 标签的 name 属性的值为 p，p 标签的 attrs 属性的值为 `{'class': ['python'], 'name': 'application'}`。这里把 p 标签的所有属性都打印了出来，得到的是一个字典。如果想单独获取某个属性，例如获取 class 属性，可以这样：
```python
attrs = soup.p.attrs
print(attrs['class'])
```
输出：
```python
['python']
```
还可以这样，利用 get 方法，传入属性的名称，二者是等价的。
```python
attrs = soup.p.attrs
print(attrs.get('class'))
```
输出：
```python
['python']
```
### 2.2 NavigableString
上面我们已经得到了标签，如果想获取标签内部的文字怎么办？很简单，用 .string 即可，例如：
```python
title = soup.title
print(title.string)
```
输出：
```html
我的 Web 页面
```
### 2.3 BeautifulSoup
BeautifulSoup 对象表示一个文档的全部内容，大部分时候，它是一个特殊的 Tag，可以分别获取它的类型、名称和属性。
```python
print(type(soup))
print(soup.name)
print(soup.attrs)
```
输出：
```python
<class 'bs4.BeautifulSoup'>
[document]
{}
```
### 2.4 Comment
Comment 对象是特殊类型的 NavigableString 对象，在对其进行内容输出的时候，是不包括注释符号的。对于 Comment 对象，如果不谨慎处理的话，可能会对我们的文本处理造成意想不到的麻烦。例如：我们将 HTML 中的 
```html
<a href="https://docs.python.org/zh-cn/3/"  id="link1">Python 官方文档</a>
```
改成 
```html
<a href="https://docs.python.org/zh-cn/3/"  id="link1"><!-- Python 官方文档 --></a>
```
我们来获取标签的内容：
```python
a = soup.a
print(a.string)
```
输出：
`Python 官方文档`
我们发现在利用 .string 进行内容输出的时候，注释符号被去掉了，所以这可能会给我们带来不必要的麻烦。所以，在使用之前最好做下判断，判断代码如下：
```python
if type(soup.a.string)==bs4.element.Comment:
    print soup.a.string
```
代码中，首先判断是否为 Comment 类型，然后再进行其他操作。
### 2.5 遍历文档树
#### 2.5.1 直接子节点
##### 2.5.1.1 .contents 属性
标签的 .contents 属性可以将标签的子节点以列表的方式输出
```python
head = soup.head
print(head.contents)
```
输出：
```python
['\n', <meta charset="utf-8"/>, '\n', <title>我的 Web 页面</title>, '\n']
```
可以通过遍历的方式获取列表中的内容：
```python
for item in soup.head:
    print(item)
```
输出：
```html


<meta charset="utf-8"/>


<title>我的 Web 页面</title>


```
##### 2.5.1.2 .children 属性
标签的 .children 属性可以将标签的子节点以列表生成器的方式输出
```python
head = soup.head
print(head.children)
```
输出：
```python
<list_iterator object at 0x7fbfa5d6bdc0>
```
可以通过遍历生成器的方式来获得里面的内容：
```python
for item in head.children:
    print(item)
```
输出：
```html


<meta charset="utf-8"/>


<title>我的 Web 页面</title>


```
#### 2.5.2 所有子孙节点
##### 2.5.2.1 .descendants 属性
标签的 .contents 和 .children 属性仅包含标签的直接子节点，.descendants 属性包含标签的所有子孙节点。
```python
head = soup.head
for item in head.descendants:
    print(item)
```
输出：
```html


<meta charset="utf-8"/>


<title>我的 Web 页面</title>
我的 Web 页面


```
再举一个例子：
```python
body = soup.body
for item in body.descendants:
    print(item)
```
输出：
```html


<h1>Python 是一门优雅的语言。</h1>
Python 是一门优雅的语言。


<img alt="Python 语言 Logo" src="images/Python.png"/>


<p class="python" name="application">使用 Python，我们可以做许多事情。</p>
使用 Python，我们可以做许多事情。


<ul>
<li>Web 开发</li>
<li>网络爬虫</li>
<li>数据分析</li>
<li>人工智能与机器学习</li>
<li>自动化运维</li>
<li>游戏开发</li>
</ul>


<li>Web 开发</li>
Web 开发


<li>网络爬虫</li>
网络爬虫


<li>数据分析</li>
数据分析


<li>人工智能与机器学习</li>
人工智能与机器学习


<li>自动化运维</li>
自动化运维


<li>游戏开发</li>
游戏开发




<p>人生苦短，我用 Python。</p>
人生苦短，我用 Python。


<p>
      如果想了解更多的 Python 语言的相关知识，可以查看
      <a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>。
    </p>

      如果想了解更多的 Python 语言的相关知识，可以查看
      
<a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>
Python 官方文档
。
    

```
#### 2.5.3 节点内容
##### 2.5.3.1 .string 属性
#### 2.5.4 多个内容
##### 2.5.4.1 .strings 属性
.strings 属性可以获取多个内容，需要通过遍历获取。
```python
for str in soup.strings:
    print(repr(str))
```
输出：
```
'\n'
'\n'
'\n'
'我的 Web 页面'
'\n'
'\n'
'\n'
'Python 是一门优雅的语言。'
'\n'
'\n'
'使用 Python，我们可以做许多事情。'
'\n'
'\n'
'Web 开发'
'\n'
'网络爬虫'
'\n'
'数据分析'
'\n'
'人工智能与机器学习'
'\n'
'自动化运维'
'\n'
'游戏开发'
'\n'
'\n'
'人生苦短，我用 Python。'
'\n'
'\n      如果想了解更多的 Python 语言的相关知识，可以查看\n      '
'Python 官方文档'
'。\n    '
'\n'
'\n'
'\n'
```
##### 2.5.4.2 .stripped_strings 属性
使用 .strings 输出的字符串包含了很多空格和空行，使用 .stripped_strings 可以去除空白内容。
```python
for str in soup.stripped_strings:
    print(repr(str))
```
输出：
```
'我的 Web 页面'
'Python 是一门优雅的语言。'
'使用 Python，我们可以做许多事情。'
'Web 开发'
'网络爬虫'
'数据分析'
'人工智能与机器学习'
'自动化运维'
'游戏开发'
'人生苦短，我用 Python。'
'如果想了解更多的 Python 语言的相关知识，可以查看'
'Python 官方文档'
'。'
```
#### 2.5.5 父节点
##### 2.5.5.1 .parent 属性
我们可以通过 .parent 属性来获取元素的父节点。
```python
p = soup.p
print(p.parent.name)
```
输出：
`body`
例外一个例子：
```python
content = soup.head.title.string
print(content.parent.name)
```
输出：
`title`
#### 2.5.6 全部父节点
##### 2.5.6.1 .parents 属性
我们可以通过 .parents 属性来递归获取元素的所有父辈节点。
```python
content = soup.head.title.string
for parent in content.parents:
    print(parent.name)
```
输出：
```
title
head
html
[document]
```
#### 2.5.7 兄弟节点
##### 2.5.7.1 .next_sibling属性 .previous_sibling属性
兄弟节点是和本节点处于同一级的节点，.next_sibling 属性获取该节点的下一个兄弟节点，.previous_sibling 则与之相反，如果节点不存在，则返回 None。注意：实际文档中标签的 .next_sibling 和 .previous_sibling 属性通常是字符串或空白，因为空白或者换行也可以被视作一个节点，所以得到的结果可能是空白或者换行。
```python
print(soup.p.next_sibling)
```
输出：
```

```
输出为空，是因为 p 的后面兄弟节点是一个换行，所以输出为空。
```python
print(soup.p.next_sibling.next_sibling)
```
输出：
```html
<ul>
<li>Web 开发</li>
<li>网络爬虫</li>
<li>数据分析</li>
<li>人工智能与机器学习</li>
<li>自动化运维</li>
<li>游戏开发</li>
</ul>
```
p 的后面兄弟节点的后面兄弟节点是一个列表，所以输出了上面的内容。
```python
print(soup.p.previous_sibling)
```
输出：
```

```
输出为空，是因为 p 的前面兄弟节点是一个换行，所以输出为空。
```python
print(soup.p.previous_sibling.previous_sibling)
```
输出：
```html
<img alt="Python 语言 Logo" src="images/Python.png"/>
```
p 的前面兄弟节点的前面兄弟节点是一个图片的标签，所以输出了上面的内容。
#### 2.5.8 全部兄弟节点
##### 2.5.8.1 .next_siblings属性 .previous_siblings属性
通过 .next_siblings 和 .previous_siblings 属性可以对当前节点的兄弟节点迭代输出
```python
for sibling in soup.img.next_siblings:
    print(repr(sibling))
```
输出：
```html
'\n'
<p class="python" name="application">使用 Python，我们可以做许多事情。</p>
'\n'
<ul>
<li>Web 开发</li>
<li>网络爬虫</li>
<li>数据分析</li>
<li>人工智能与机器学习</li>
<li>自动化运维</li>
<li>游戏开发</li>
</ul>
'\n'
<p>人生苦短，我用 Python。</p>
'\n'
<p>
      如果想了解更多的 Python 语言的相关知识，可以查看
      <a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>。
    </p>
'\n'
```
#### 2.5.9 前后节点
##### 2.5.9.1 .next_element .previous_element 属性
与 `.next_element` `.previous_element` 不同，它并不是针对于兄弟节点，而是所有节点，不分层次。
```python
print(soup.head.next_element)
```
输出：
```

```
head 的下一个元素为空行。
```python
print(soup.head.next_element.next_element)
```
输出：
```html
<meta charset="utf-8"/>
```
head 的下一个元素的下一个元素为 `<meta charset=“utf-8”/>`。
#### 2.5.10 所有前后节点
##### 2.5.10.1 .next_elements .previous_elements 属性
通过 `.next_elements` 和 `.previous_elements` 的迭代器可以向前或向后访问文档的解析内容。
```python
for element in soup.img.next_elements:
    print(repr(element))
```
输出：
```html
'\n'
<p class="python" name="application">使用 Python，我们可以做许多事情。</p>
'使用 Python，我们可以做许多事情。'
'\n'
<ul>
<li>Web 开发</li>
<li>网络爬虫</li>
<li>数据分析</li>
<li>人工智能与机器学习</li>
<li>自动化运维</li>
<li>游戏开发</li>
</ul>
'\n'
<li>Web 开发</li>
'Web 开发'
'\n'
<li>网络爬虫</li>
'网络爬虫'
'\n'
<li>数据分析</li>
'数据分析'
'\n'
<li>人工智能与机器学习</li>
'人工智能与机器学习'
'\n'
<li>自动化运维</li>
'自动化运维'
'\n'
<li>游戏开发</li>
'游戏开发'
'\n'
'\n'
<p>人生苦短，我用 Python。</p>
'人生苦短，我用 Python。'
'\n'
<p>
      如果想了解更多的 Python 语言的相关知识，可以查看
      <a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>。
    </p>
'\n      如果想了解更多的 Python 语言的相关知识，可以查看\n      '
<a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>
'Python 官方文档'
'。\n    '
'\n'
'\n'
'\n'
```
next_elements 输出了 img 标签后的所有元素。
### 2.6 搜索文档树
#### 2.6.1 find_all(name, attrs, recursive, text,kwargs)
##### 2.6.1.1 name 参数
查找所有 p 标签
```python
print(soup.find_all('p'))
```
输出：
```python
[<p class="python" name="application">使用 Python，我们可以做许多事情。</p>, <p>人生苦短，我用 Python。</p>, <p>
      如果想了解更多的 Python 语言的相关知识，可以查看
      <a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>。
    </p>]
```
##### 2.6.1.2 keyword 参数
搜索属性值符合条件的标签。
搜索 id 属性的值为 'link1' 的标签
```
print(soup.find_all(id='link1'))
```
输出：
```python
[<a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>]
```
搜索 href 属性的值为 "https://docs.python.org/zh-cn/3/" 的标签。
```
print(soup.find_all(href="https://docs.python.org/zh-cn/3/"))
```
输出：
```python
[<a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>]
```
### 2.7 CSS 选择器
#### 2.7.1 通过标签名查找
查找标签名为 `title` 的标签。
```python
print(soup.select('title'))
```
输出：
```python
[<title>我的 Web 页面</title>]
```
查找标签名为 `p` 的标签
```python
print(soup.select('p'))
```
输出：
```python
[<p class="python" name="application">使用 Python，我们可以做许多事情。</p>, <p>人生苦短，我用 Python。</p>, <p>
      如果想了解更多的 Python 语言的相关知识，可以查看
      <a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>。
    </p>]
```
#### 2.7.2 通过类名查找
查询类名为 python 的标签
```python
print(soup.select('.python'))
```
输出：
```python
[<p class="python" name="application">使用 Python，我们可以做许多事情。</p>]
```
#### 2.7.3 通过 id 名查找
查找 id 为 link1 的标签。
```python
print(soup.select('#link1'))
```
输出：
```python
[<a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>]
```
#### 2.7.4 组合查找
查找 p 标签中，id 等于 link1 的内容，二者需要用空格分开。
```python
print(soup.select('p #link1'))
```
输出：
```python
[<a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>]
```
#### 2.7.5 属性查找
查找时还可以加入属性元素，属性需要用中括号括起来，属性和标签属于同一节点，所以中间不能加空格，否则会无法匹配到。
查找属性 class 的值为 "python" 的 p 标签。
```python
print(soup.select('p[class="python"]'))
```
输出：
```python
[<p class="python" name="application">使用 Python，我们可以做许多事情。</p>]
```
查找属性 href 的值为 "https://docs.python.org/zh-cn/3/" 的 a 标签。
```python
print(soup.select('a[href="https://docs.python.org/zh-cn/3/"]'))
```
输出：
```python
[<a href="https://docs.python.org/zh-cn/3/" id="link1">Python 官方文档</a>]
```
## 3.小结
本节课我们结合实例对 Beautiful Soup 库做了介绍，Beautiful Soup库是从 HTML 中提取我们想要数据的利器，可以大大方便我们对数据的提取。
