## 色彩空间转换

## 目标

> - 在本教程中，将学习如何将图像从一个颜色空间转换为另一个颜色空间，如BGR ![\ leftrightarrow](https://opencv-python-tutroals.readthedocs.io/en/latest/_images/math/41a61df92c33a32be9fd6375536739eca63f43ab.png)Gray，BGR ![\ leftrightarrow](https://opencv-python-tutroals.readthedocs.io/en/latest/_images/math/41a61df92c33a32be9fd6375536739eca63f43ab.png)HSV等。
> - 除此之外，我们还将创建一个提取视频中彩色对象的应用程序
> - 您将学习以下函数：**cv2.cvtColor（）**，**cv2.inRange（）**等。

### 改变颜色空间

OpenCV中有150多种颜色空间转换方法。但我们将只研究两种最广泛使用的产品，**BGR** ![\ leftrightarrow](https://opencv-python-tutroals.readthedocs.io/en/latest/_images/math/41a61df92c33a32be9fd6375536739eca63f43ab.png)**Gray**和**BGR** ![\ leftrightarrow](https://opencv-python-tutroals.readthedocs.io/en/latest/_images/math/41a61df92c33a32be9fd6375536739eca63f43ab.png)**HSV**

我们使用下面的函数进行颜色转换

**cv2.cvtColor(input_img , flag)**

1. 第一个参数：代表要操作的照片
2. 第二个参数flag：代表要进行的转换类型

> BGR ![\rightarrow](https://opencv-python-tutroals.readthedocs.io/en/latest/_images/math/a9c4c6156d25f42923975ce449aadad9848ed7dc.png) Gray  : 使用   cv2.COLOR_BGR2GRAY
>
> BGR ![\rightarrow](https://opencv-python-tutroals.readthedocs.io/en/latest/_images/math/a9c4c6156d25f42923975ce449aadad9848ed7dc.png) HSV   :使用  v2.COLOR_BGR2HSV

查看其他flag，运行代码：

```python
import cv2
flags = [i for i in dir(cv2) if i.startswith('COLOR_')]
print(flags)
```

注意：
对于**HSV**，色调范围为[0 , 179] ，饱和范围为[0 , 255]，值(亮度)范围为[0 , 255]

不同的软件使用不同的规模。因此，如果要将OpenCV值与它们进行比较，则需要对这些范围进行标准化。

> 色调H：用角度度量，取值范围为0°～360°，从红色开始按逆时针方向计算，红色为0°，绿色为120°,蓝色为240°。它们的补色是：黄色为60°，青色为180°,品红为300°；

>饱和度S：取值范围为0.0～1.0

> 亮度V：取值范围为0.0(黑色)～1.0(白色)

RGB和CMY颜色模型都是面向硬件的，而HSV（Hue Saturation Value）颜色模型是面向用户的



### HSV六棱锥

H参数表示色彩信息，即所处的光谱颜色的位置。该参数用一角度量来表示，红、绿、蓝分别纯度S为一比例值，范围从0到1，它表示成所选颜色的纯度和该颜色最大的纯度之间的比率。S=0时，只有灰度。相隔120度。互补色分别相差180度。

V表示色彩的明亮程度，范围从0到1。有一点要注意：它和光强度之间并没有直接的联系。

![img](https://images2015.cnblogs.com/blog/900592/201607/900592-20160727130231466-1525470417.png)![img](https://images2015.cnblogs.com/blog/900592/201607/900592-20160727130251013-1162701264.png)

HSV对用户来说是一种直观的颜色模型。我们可以从一种纯色彩开始，即指定色彩角H，并让V=S=1，然后我们可以通过向其中加入黑色和白色来得到我们需要的颜色。增加黑色可以减小V而S不变，同样增加白色可以减小S而V不变。例如，要得到深蓝色，V=0.4 S=1 H=240度。要得到淡蓝色，V=1 S=0.4 H=240度



HSV坐标系最顶部的截面最外圈，饱和度和亮度都是100%，随着色相从0°到360°变化，RGB值的变化如上图，红色、绿色、蓝色分别位于0°、120°、240°；0°到60°之间绿色分量均匀增加，60°到120°之间红色分量均匀减少，以此类推。将 [这个演示](http://web.bentley.edu/empl/c/ncarter/MA307/color-converter.html)中的S和B调到100%，调整H的值观察RGB的变换就能发现这个规律。这时RGB中最大值一定是255（因为亮度为100%），最小值一定是0（如果不是，比如RGB=（10,20,255），可以看成在（0,10,255）分量的基础上加上一个灰色分量（10,10,10），加灰色后饱和度就不是100%了）。



### **HSV转RGB计算**

---

要算出（h,s,b）对应的（r,g,b）,可以分3步，以HSB=（130°，50%，80%）为例说明：

 

1、先算出（h,100%,100%）对应的（r',g',b'）。

先固定色相，色环图上色相**H∈[-60°，60°]时红色分量**最大，**H∈[60°，180°]时绿色分**量最大，**H∈[180°，300°]时蓝色分量**最大，此时B=100%，所以RGB的最大分量为255。

先算出HSB=（130°，100%，100%）对应的（r',g',b'）：H=130°，在[60°，180°]区间，所以绿色分量为g'=255，红色分量r'=0；进一步地，130°处于[120°，180°]，在这60°的区间上，色环上蓝色分量对应地从0递增到255，所以b'=(130°-120°）/60°*255=43。所以（r',g',b'）=（0,255,43）

 

2、固定色相后再调整饱和度，算出（h,s,100%)对应的（r'',g'',b''）。

在亮度B=100%时，从 [演示](http://web.bentley.edu/empl/c/ncarter/MA307/color-converter.html)中发现，饱和度S降低，即“不饱和度”（1-S）升高，会使得RGB与最大值255相差的部分对应增大，RGB三个分量越趋于相同就使得图像越灰。所以，

r''=r'+(255-r')*(1-S) .................................... ①

g''和b''用同样的方法求出。当然，r'g'b'中的最大值不会变化。（r'',g'',b''）=(128,255,149)

 

3、最后算出（h,s,b）对应的（r,g,b）。

最后调整亮度，只要依照亮度值的百分比缩小就行了，（r,g,b）=(r'',g'',b'')*80%=(102,204,119)就是HSB=（130°,50%,80%）对应的RGB

![](https://img-blog.csdn.net/20160526142406662?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)