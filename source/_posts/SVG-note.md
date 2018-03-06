title: SVG note
author: Howard Wong
date: 2018-03-06 10:24:56
tags:
---
# SVG in HTML

## 矩形

```html
<svg>
  <rect width="300" height="100"
	style="fill:rgb(0,0,255);stroke-width:1;stroke:rgb(0,0,0)"/>
</svg>
```

### props
- width, height

### style
- fill, fill-opacity
- stroke, stroke-width, stroke-opacity
- 圆角 rx, ry


## 圆形

```html
<svg>
    <circle cx="100" cy="50" r="40" stroke="black"
  stroke-width="2" fill="red"/>
</svg>
```

### props
- 圆点坐标 cx, cy
- 半径 r

## 椭圆

```html
<svg>
  <ellipse cx="300" cy="80" rx="100" ry="50"
  style="fill:yellow;stroke:purple;stroke-width:2"/>
</svg>
```
### props
- 椭圆中心 cx, cy
- 水平/垂直半径 rx, ry

## 直线

```html
<svg>
  <line x1="0" y1="0" x2="200" y2="200"
  style="stroke:rgb(255,0,0);stroke-width:2"/>
</svg>
```

### props
- x1, y1, x2, y2

## 多边形

```html
<svg>
  <polygon points="200,10 250,190 160,210"
  style="fill:lime;stroke:purple;stroke-width:1"/>
</svg>
```

## 曲线

```html
<svg>
  <polyline points="20,20 40,25 60,40 80,120 120,140 200,180"
  style="fill:none;stroke:black;stroke-width:3" />
</svg>
```

## 路径

```
M = moveto
L = lineto
H = horizontal lineto
V = vertical lineto
C = curveto
S = smooth curveto
Q = quadratic Bézier curve
T = smooth quadratic Bézier curveto
A = elliptical Arc
Z = closepath
```

### 闭合路径

```html
<svg>
  <path d="M150 0 L75 200 L225 200 Z" />
</svg>
```

### 曲线

```html
<path d="M 100 350"
```

### Animate

```html
<svg width="300px" height="100px">
  <rect x="0" y="0" width="300" height="100" stroke="black" stroke-width="1" />
  <circle cx="0" cy="50" r="15" fill="blue" stroke="black" stroke-width="1">
    <animate attributeName="cx" from="0" to="100" dur="5s" repeatCount="indefinite" />
  </circle>
</svg>
```

### AnimateMotion

```html
<svg>
  <rect x="0" y="0" width="300" height="100" stroke="black" stroke-width="1" />
  <circle cx="0" cy="50" r="15" fill="blue" stroke="black" stroke-width="1">
    <animateMotion path="M 0 0 H 300 Z" dur="3s" repeatCount="indefinite" />
  </circle>
</svg>
```
