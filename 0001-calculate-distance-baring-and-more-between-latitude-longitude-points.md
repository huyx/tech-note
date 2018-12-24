# 计算两点之间的距离、方向等

原文：[Calculate distance, bearing and more between Latitude/Longitude points](https://www.movable-type.co.uk/scripts/latlong.html)

注：原文中提供了在线计算的功能，这里忽略了这部分内容的翻译。如有需要，请查看原文。

本文展示了关于经纬度形式的点的几种计算，列出了相应的公式和实现公式的代码片段。

本文所有公式均建立在球形地球（忽略椭圆形的影响）的基础上。大多数情况下，这种假设均可以提供足够的精度。（实际上，地球只是稍微有些椭球形，使用球形模型通常至多引入 0.3% 的误差。）

## 距离（Distance）

这里使用**`haversine`**公式来计算两点之间的大圆距离（great-circle distance）——即，在地球表面的最短距离。

Haversine formula:

$a=sin^2(\Delta\phi/2) + cos\phi1 \sdot cos\phi2 \sdot sin^2(\Delta\lambda/2)$
$c=2 \sdot atan2(\sqrt{a}, \sqrt{1-a})$
$d=R \sdot c$

其中：$\phi$是纬度，$\lambda$是经度，R是地球半径（平均半径=6371km）。注意传递给三角函数的角度值应该是弧度。

Javascript:

```javascript
var R = 6371e3; // metres
var φ1 = lat1.toRadians();
var φ2 = lat2.toRadians();
var Δφ = (lat2-lat1).toRadians();
var Δλ = (lon2-lon1).toRadians();

var a = Math.sin(Δφ/2) * Math.sin(Δφ/2) +
        Math.cos(φ1) * Math.cos(φ2) *
        Math.sin(Δλ/2) * Math.sin(Δλ/2);
var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));

var d = R * c;
```

 注意：在脚本代码中，我通常使用 lat/lon 表示以度形式的经纬度，而用$\phi/\lambda$来表示弧度形式的经纬度。因为我发现混用度和弧度非常容易导致令人挠头的bugs……

`haversine`公式……TODO……



## 方位角

……

## 中点

……



## 给定起点、距离和方位角求终点

给定起点、初始方位角、距离，计算沿着大圆的弧（最短距离）行进的终点和最后的方位角。

公式：

$\phi_2=asin(sin\phi_1 \sdot cos\delta+cos\phi_1 \sdot sin\delta \sdot cos\theta)$

$\lambda_2=\lambda_1 + atan2(sin\theta \sdot \sin\delta \sdot cos\phi_i, cos\delta - sin\phi_1 \sdot sin\phi_2)$

其中：$\phi$是纬度，$\lambda$是经度，R是地球半径（平均半径=6371km）。注意传递给三角函数的角度值应该是弧度。

Javascript:

```javascript
var φ2 = Math.asin( Math.sin(φ1)*Math.cos(d/R) +
                    Math.cos(φ1)*Math.sin(d/R)*Math.cos(brng) );
var λ2 = λ1 + Math.atan2(Math.sin(brng)*Math.sin(d/R)*Math.cos(φ1),
                         Math.cos(d/R)-Math.sin(φ1)*Math.sin(φ2));
```

经度可以用 $(lon+540)\%360-180$ 标准化到 -180...+180 的范围内。

Excel（所有角度均为弧度）:
```vbscript
lat2: =ASIN(SIN(lat1)*COS(d/R) + COS(lat1)*SIN(d/R)*COS(brng))
lon2: =lon1 + ATAN2(COS(d/R)-SIN(lat1)*SIN(lat2), SIN(brng)*SIN(d/R)*COS(lat1)) 
```

注意：Excel 颠倒了 ATAN2 的参数。

