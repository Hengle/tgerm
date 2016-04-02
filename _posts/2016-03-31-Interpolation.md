---
layout:	post
comments: true
tag: [Stage3D]
title: Interpolation 插值
---

####插值

在离散数据的基础上补差连续函数，使得这条连续曲线通过全部的离散数据点。插值是离散函数逼近的重要方法，利用它可通过函数在有限个点处的取值状况，估算出函数在其他点处的近似值。

插值比较常用在骨骼动画，物体移动，灯光渐隐，摄像机动画，图形渲染中。

####Linear Interpolation(线性插值)

假如我们需要将物体X通过N步从A点移动到B点，可以使用下面的代码：

{% highlight as3 %}

for(i = 0; i < N; i++)
{    
	X = ((A * i) + (B * (N - i))) / N;
}

{% endhighlight %}



```c#
for(i = 0; i < N; i++)
{    
X = ((A * i) + (B * (N - i))) / N;
}
```
