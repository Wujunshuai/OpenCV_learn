## 图像阈值

## 目标

- 在本教程中，您将学习简单阈值处理，自适应阈值处理，Otsu阈值处理等。
- 您将学习以下函数：**cv2.threshold**，**cv2.adaptiveThreshold**等。

#### 简单阈值

##### **cv2.threshold**    -->  固定阈值二值化

如果像素值大于阈值，则为其分配一个值（可以是白色），否则为其分配另一个值（可以是黑色）。

参数：

1. 第一个参数：源图像，它应该是**灰度**照片
2. 第二个参数：对于像素进行分类的阈值
3. 第三个参数：maxVal，它表示如果像素值大于（有时小于）阈值则要给出的值
4. 第四个参数：**阈值类型**

> - cv2.THRESH_BINARY
> - cv2.THRESH_BINARY_INV
> - cv2.THRESH_TRUNC
> - cv2.THRESH_TOZERO
> - cv2.THRESH_TOZERO_INV
>
> **INV 表示的是取反**

![](https://img-blog.csdn.net/20180723181854671?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ExOTk5MDQxMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

效果：
![](https://opencv-python-tutroals.readthedocs.io/en/latest/_images/threshold.jpg)



### **案例**

````python

img = cv2.imread('2.jpg' , 0)

ret , thresh1 = cv2.threshold(img , 127 , 255 , cv2.THRESH_BINARY)
ret , thresh2 = cv2.threshold(img , 127 , 255 , cv2.THRESH_BINARY_INV)
ret , thresh3 = cv2.threshold(img , 127 , 255 , cv2.THRESH_TRUNC)
ret , thresh4 = cv2.threshold(img , 160 , 255 , cv2.THRESH_TOZERO)
ret , thresh5 = cv2.threshold(img , 127 , 255 , cv2.THRESH_TOZERO_INV)

titles = ['Original Image','BINARY','BINARY_INV','TRUNC','TOZERO','TOZERO_INV']
images = [img, thresh1, thresh2, thresh3, thresh4, thresh5]

for i in range(6):
    plt.subplot(2 , 3  , i+1) , plt.imshow(images[i] , 'gray')
    plt.title(titles[i])
    plt.xticks([]) , plt.yticks([])

plt.show()
````



### 自适应阈值

----

固定阈值处理，即全局值作为阈值，但图像在不同区域具有不同的照明条件，因此全局阈值并不一定好用。因此，我们进行自适应阈值处理，**算法计算图像的小区域的阈值**，我们为同一图像的不同区域获得不同的阈值，并且它为具有不同照明的图像提供了更好的结果。

#### cv2.adaptiveThreshold（）

参数：

1. 第一个参数：源图像

2. 第二个参数：maxVal，它表示如果像素值大于（有时小于）阈值则要给出的值

3. 第三个参数：自适应方法（决定了如何计算阈值）

   >- cv2.ADAPTIVE_THRESH_MEAN_C：阈值是邻域的平均值。
   >- cv2.ADAPTIVE_THRESH_GAUSSIAN_C：阈值是邻域值的加权和，其中权重是高斯窗口

4. 第四个参数：块大小，决定了领域的大小

5. 第五个参数：常数C，从计算的平均值或加权平均值中减去的常数





---

### 扩展 medianBlur   -------  **中值滤波**

中值滤波是一种典型的**非线性**滤波，是基于**排序统计**理论的一种能够有效**抑制噪声**的非线性信号处理技术.

**基本思想：**

使用像素点**邻域 (以当前像素为中心的正方形区域)灰度值**的**中值**来代替该像素点的灰度值，让周围的像素值接近真实的值从而**消除孤立**的噪声点。

**优点：**

 该方法在取出脉冲噪声、椒盐噪声的同时能保留图像的边缘细节。这些优良特性是线性滤波所不具备的。

中值滤波对消除椒盐噪声非常有效，能够克服线性滤波器带来的图像细节模糊等弊端，能够有效保护图像边缘信息，是非常经典的平滑噪声处理方法。在光学测量条纹图像的香味分析处理方法中有特殊作用，但在条纹中心分析方法中作用不大

**原理：**

首先也得生成一个滤波模板，将该模板内的各像素值进行排序，生成单调上升或单调下降的二维数据序列，二维中值滤波输出为
$$
g(x , y) = medf\{f(x-k , y-1),(k ,|∈w) \}
$$
**f(x,y)**和**g(x,y)**分别是**原图像**和**处理后图像**，w为输入的二维模板，能够在整幅图像上滑动，通常尺寸为3*3或5*5区域，也可以是不同的形状如线状、圆形、十字形、圆环形等。通过从图像中的二维模板取出奇数个数据进行排序，用排序后的中值取代要处理的数据即可。 

**缺点：**

中值滤波相较于线性滤波中的均值滤波优点在前面已经提到，取得良好滤波效果的代价就是耗时的提升，可能达到均值滤波的数倍，而且对于细节较多的图像也不太适用。

**函数：**

##### medianBlur(InputArray src, OutputArray dst, int ksize)

参数：

1. 第一个参数：InputArray src: 输入图像，图像为1、3、4通道的图像，当模板尺寸为3或5时，图像深度只能为CV_8U、CV_16U、CV_32F中的一个，如而对于较大孔径尺寸的图片，图像深度只能是CV_8U。
2. 第二个参数：OutputArray dst: 输出图像，尺寸和类型与输入图像一致，可以使用Mat::Clone以原图像为模板来初始化输出图像dst 
3. 第三个参数：int ksize: 滤波模板的尺寸大小，必须是大于1的奇数，如3、5、7……



**椒盐噪声**

![](https://img-blog.csdn.net/20180731132636400?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FfWjY2NjY2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**高斯噪声**

![](https://img-blog.csdn.net/20180731132618154?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0FfWjY2NjY2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

---



