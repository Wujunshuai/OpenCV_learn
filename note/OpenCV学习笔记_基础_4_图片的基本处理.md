## 图像的基本操作

### 目标

学会：

> - 访问像素值并修改它们
> - 访问图像属性
> - 设置图像区域（ROI）
> - 拆分和合并图像

本节中的几乎所有操作主要与**Numpy**而不是**OpenCV**有关

ROI（region of interest），感兴趣区域。机器视觉、图像处理中，从被处理的图像以方框、圆、椭圆、不规则多边形等方式勾勒出需要处理的区域，称为感兴趣区域，ROI。在Halcon、OpenCV、Matlab等机器视觉软件上常用到各种算子（Operator）和函数来求得感兴趣区域ROI，并进行图像的下一步处理。



### 访问和修改像素值

----

可以通过行和列坐标访问像素值.

对于BGR图像，它返回一个蓝色，绿色，红色值的数组。对于灰度图像，仅返回相应的强度

```python
import cv2
import numpy as np

img = cv2.imread('messi5.jpg')

px = img[100,100]
# 以相同的方式修改像素值
img[100,100] = [255,255,255]
```

**注:**

Numpy是一个用于快速阵列计算的优化库。因此，简单地访问每个像素值并对其进行修改将非常缓慢，并且不鼓励这样做。

##### numpy的阵列方法，`array.item()` 和 `array.itemset()`被认为是更好的，但这两个函数一般只接受一个标量参数，因此，如果想修改R、G、B的话，需要为每一个单独修改

```python
pig_img.itemset((10, 10, 2), 100)  # 编辑像素，0 1 2 -> GBR
k = pig_img.item(10, 10, 2)  # 复制数组中的一个元素，并返回
```

**扩展**

numpy的函数

```
ndarray.item(*args)	复制数组中的一个元素，并返回
ndarray.tolist()	将数组转换成python标准list
ndarray.itemset(*args)	修改数组中某个元素的值
ndarray.tostring([order])	构建一个包含ndarray的原始字节数据的字节字符串
ndarray.tobytes([order])	功能同tostring
ndarray.byteswap(inplace)	将ndarray中每个元素中的字节进行大小端转换
ndarray.copy([order])	复制数组并返回（深拷贝）ndarray.fill(value)	使用值value填充数组
```





### 拆分和合并图像通道

----

图像属性包括行数，列数和通道数，图像数据类型，像素数等。

> 访问图像的形状`img.shape`。它返回一组行，列和通道的元组（如果图像是彩色的）

> 通过`img.size`以下方式访问总像素数

> img.dtype查看图像数据类型

```python
k2 = pig_img.shape  # 查看图片矩阵的行、列、通道
k3 = pig_img.size  # 查看总像素大小
k4 = pig_img.dtype  # 查看图像数据类型
print(k, k2, k3, k4)
```



**注 ：**

> 如果图像是灰度，则返回的元组仅包含行数和列数。因此，检查加载的图像是灰度还是彩色图像是一种很好的方法。

> `img.dtype` 在调试时非常重要，因为OpenCV-Python代码中的大量错误是由无效的数据类型引起的。



### 图像剪切和粘贴

---

使用Numpy索引获得ROI。在这里，选择一部分图像（ROI）并将其复制到图像中的另一个区域

```python
pig_img = cv2.imread('2.jpg')
ball = pig_img[137 :237 , 110:285]# 前面代表x1 , x2，后面代表y1 y2,默认以上侧和右侧为分割线剪切
pig_img[147 :247 , 120:295] = ball
```





### 拆分和合并图像通道

---

当需要时，图像的B，G，R通道可以分成它们各自的平面。然后，可以将各个通道合并在一起以再次形成BGR图像。

```python
b , g , r = cv2.split(pig_img)#通带分割，分割为b g r非常耗时间，不建议对大照片使用
img = cv2.merge((b,g,r))#通道合并

#或者：
b = img[ : , : ,0]#只保留r
```

由于split()方法较为浪费时间，故建议采用numpy方法来进行分割图层通道

```python
img[: , : , 1] = 0# 讲红色变为0
# 删除红色，但查看此时的图片数据类型为
# 对于一个为(540, 960, 3)的图片处理
# split()和img[ : , : ,0] -> (540, 960)
# img[: , : , 1] = 0 -> (540, 960, 3)
```





### 为图像制作边框（填充）

---

如果要在图像周围创建边框，比如相框，可以使用**cv2.copyMakeBorder（）**函数。但它有更多卷积运算，零填充等应用。

参数：

- **src** - 输入图像
- **顶部**，**底部**，**左侧**，**右侧** - 相应方向上的像素数的边框宽度
- **borderType** - 定义要添加的边框类型的标志。它可以是以下类型：
  - **cv2.BORDER_CONSTANT** - 添加一个恒定的彩色边框。该值应作为下一个参数
  - **cv2.BORDER_REFLECT** - 边框将是边框元素的镜像反射，如下所示：*fedcba | abcdefgh | hgfedcb*
  - **cv2.BORDER_REFLECT_101**或**cv2.BORDER_DEFAULT**- 与上面相同，但略有改动，如下所示：*gfedcb | abcdefgh | gfedcba*
  - **cv2.BORDER_REPLICATE** - 最后一个元素被复制，如下所示：*aaaaaa | abcdefgh | hhhhhhh*
  - **cv2.BORDER_WRAP** - 无法解释，它看起来像这样：*cdefgh | abcdefgh | abcdefg*
- **value** - 边框类型为的边框颜色`cv2.BORDER_CONSTANT`

注意：其中各个参数也可以用逗号`,`分隔开。第一个参数代表子图的行数；第二个参数代表该行图像的列数； 第三个参数代表每行的第几个图像

**案例**

```python

img = cv2.imread('2.jpg')
Blue = [225 , 0 , 0]

replicate = cv2.copyMakeBorder(img , 100 , 100 , 100 , 100 , cv2.BORDER_CONSTANT)
reflect = cv2.copyMakeBorder(img,100,100,100,100,cv2.BORDER_REFLECT)
reflect101 = cv2.copyMakeBorder(img,100,100,100,100,cv2.BORDER_REFLECT_101)
wrap = cv2.copyMakeBorder(img,100,100,100,100,cv2.BORDER_WRAP)
constant= cv2.copyMakeBorder(img,100,100,100,100,cv2.BORDER_CONSTANT,value=Blue)

plt.subplot(231),plt.imshow(img,'gray'),plt.title('ORIGINAL')
plt.subplot(232),plt.imshow(replicate,'gray'),plt.title('REPLICATE')
plt.subplot(233),plt.imshow(reflect,'gray'),plt.title('REFLECT')
plt.subplot(234),plt.imshow(reflect101,'gray'),plt.title('REFLECT_101')
plt.subplot(235),plt.imshow(wrap,'gray'),plt.title('WRAP')
plt.subplot(236),plt.imshow(constant,'gray'),plt.title('CONSTANT')

plt.show()



```

----

#### plt.subplot（）

```
plt.subplot(221) # 第一行的左图
plt.subplot(222) # 第一行的右图
plt.subplot(212) # 第二整行
plt.show()
```

**注意：其中各个参数也可以用逗号 `,`分隔开。**

> **第一个参数代表子图的行数；**

> **第二个参数代表该行图像的列数； **

> **第三个参数代表每行的第几个图像。**

在matplotlib中，`整个图像`为一个`Figure`对象。在Figure对象中可以包含一个或者多个`Axes`对象。每个Axes(ax)对象都是一个拥有自己坐标系统的**绘图区域**。

一个"Figure"意味着用户交互的整个窗口。在这个figure中容纳着"subplots"。当我们调用plot时，matplotlib会调用`gca()`获取当前的axes绘图区域，而且`gca`反过来调用`gcf()`来获得当前的figure。如果figure为空，它会自动调用`figure()`生成一个figure, 严格的讲，是生成`subplots(111)`

#### plt.imshow()

plt.imshow()函数负责对图像进行处理，并显示其格式，但是不能显示。

其后跟着plt.show()才能显示出来。





### 图像的算术运算

----

### 目标

> - 学习对图像的几种算术运算，如加法，减法，按位运算等。
> - 将学习以下函数：**cv2.add（）**，**cv2.addWeighted（）**等



### 图像添加

---

1. 可以通过OpenCV函数添加两个图像，`cv2.add()`

2. 或者简单地通过numpy操作添加。两个图像应该具有相同的**深度和类型**，或者第二个图像可以是**标量值**。`res = img1 + img2`

**注意：**

>  OpenCV添加和Numpy添加之间存在差异。OpenCV添加是饱和操作，而Numpy添加是模运算。
>
> 区别在于，opencv在操作时对于溢出的处理是直接取最大最小值，而numpy这是将溢出取模，模的大小取决于数据类型。往往opencv方法更好用。

**样例：**

```python
x = np.uint8([250])
y = np.uint8([10])

k1 = cv2.add(x , y)
k2 = x + y

print(k1 , k2)
```

```
输出： [[255]] [4]
```



### 图像混合

----

#### cv2.addWeighted()：

原理：

这也是图像添加，但是对图像赋予不同的权重，从而给出混合感或透明感。图像按以下等式添加：
$$
g(x) = (1-\alpha)f_0(x) + \alpha_1(x)
$$
通过改变![\α](https://opencv-python-tutroals.readthedocs.io/en/latest/_images/math/ad59b6e24a4a00ac621801f8d7513d68be654ab5.png)从![0 \右箭头1](https://opencv-python-tutroals.readthedocs.io/en/latest/_images/math/e8b0946e02b57d6440cad75c8e0666f071d5ab3c.png)，您可以执行到另一个图像之间的过渡，改变权值。



两张图片将它们混合在一起。第一图像的权重为0.7，第二图像的权重为0.3。`cv2.addWeighted()`在图像上应用以下等式
$$
dst = \alpha*img1 + \beta*img2 + \gamma 
$$

$$
此处\gamma被视为零
$$



### 按位运算

---

包括按位AND，OR，NOT和XOR运算。它们在提取图像的任何部分时非常有用，定义和使用非矩形ROI等

下面学习如何更改图像的特定区域的示例

> cv2.bitwise_not() 会对255进行取反

>cv2.bitwise_or()  

>cv2.bitwise_and()

>cv2.bitwise_xor()

```python
我想在左上角放置徽标，所以创建ROI
rows , color , channels = img2.shape
roi = img1[0 : rows , 0 : color]

# ＃现在创建一个徽标掩码并创建其反向掩码
# 变为灰度照片
img2gray = cv2.cvtColor(img2 , cv2.COLOR_BGR52GRAY)
# 阈值处理
ret , mask = cv2.threshold(img2gray , 10 , 225 , cv2.THRESH_BINARY)
mask_inv = cv2.bitwise_not(mask)

#现在在ROI
img1_bg = cv2.bitwise_and(roi , roi , mask = mask)
# ＃仅从徽标图像中获取徽标区域。
img2_fg = cv2.bitwise_and(img2 , img2 , mask = mask)
# 在ROI中放置徽标并修改主图像
dest = cv2.add(img1_bg , img2_fg)
img1[0 : rows , 0:color] = dest

while(1):
    cv2.imshow('dest' , img1_bg)

    #
    cv2.imshow('dest2' , img1)


    #print(img1.shape , img2.shape)
    #

    if cv2.waitKey(10) & 0xFF == ord('q'):
        break

cv2.destroyAllWindows()

```



#### cv2.threshold()  -->  图像的简单阈值处理

这个函数有四个参数：

1. 第一个原图像
2. 第二个进行分类的阈值
3. 第三个是高于（低于）阈值时赋予的新值
4. 第四个是一个方法选择参数，常用的有： 

> • cv2.THRESH_BINARY（黑白二值） 
> • cv2.THRESH_BINARY_INV（黑白二值反转） 
> • cv2.THRESH_TRUNC （得到的图像为多像素值） 
> • cv2.THRESH_TOZERO 
> • cv2.THRESH_TOZERO_INV 

该函数有两个返回值，第一个retVal（得到的阈值值（在后面一个方法中会用到）），第二个就是阈值化后的图像。 



#### Mask

掩膜(maks)

在一些图像处理函数中会有mask参数的存在，则该函数支掩膜操作。

> mask概念来源于PCB制版过程，在半导体制造中，许多芯片工艺采用光刻工艺，用于这些步骤的图形“底片”称为掩膜（也称作“掩模”），其作用是：在硅片上选定的区域中对一个不透明的图形模板遮盖，继而下面的腐蚀或扩散将只影响选定的区域以外的区域。

图像Mask掩膜与其类似，用选定的图像，对物体的图像（全部或局部）进行遮挡，来控制图像处理的区域或处理过程。

数字图像处理中，掩膜为二维矩阵数组，有时也用多值图像。

作用：

①提取感兴趣区,用预先制作的感兴趣区掩模与待处理图像相乘,得到感兴趣区图像,感兴趣区内图像值保持不变,而区外图像值都为0。 
②屏蔽作用,用掩模对图像上某些区域作屏蔽,使其不参加处理或不参加处理参数的计算,或仅对屏蔽区作处理或统计。 
③结构特征提取,用相似性变量或图像匹配方法检测和提取图像中与掩模相似的结构特征。 

④特殊形状图像的制作。

**注：**

在所有图像基本运算的操作函数中，凡是带有掩膜（mask）的处理函数，其掩膜都参与运算（输入图像运算完之后再与掩膜图像或矩阵运算）

