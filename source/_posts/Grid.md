title: Grid Layout
author: Howard Wong
date: 2018-03-10 12:29:09
tags:
---
## 术语

- Grid Container

设置了 display: gird 的元素。 这是所有 grid item 的直接父项。 在下面的例子中，.container 就是是 grid container。

```
<div class="container">
  <div class="item item-1"></div>
  <div class="item item-2"></div>
  <div class="item item-3"></div>
</div> 
```

- Grid Item

Grid 容器的孩子（直接子元素）。下面的 .item 元素就是 grid item，但 .sub-item不是。
```
<div class="container">
  <div class="item"></div> 
  <div class="item">
    <p class="sub-item"></p>
  </div>
  <div class="item"></div>
</div>
```

- Grid Line

![upload successful](/images/pasted-8.png)


- Grid Track

![upload successful](/images/pasted-9.png)

- Grid Cell

![upload successful](/images/pasted-10.png)

- Grid Area

![upload successful](/images/pasted-11.png)


## Grid Container属性

