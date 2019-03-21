## 对象跟踪

在知道如何将BGR图像转换为HSV之后，我们可以使用它来提取彩色对象。

在HSV中，表示颜色比RGB颜色空间更容易。在我们的程序中，我们将尝试提取蓝色对象

案例：

- 拍摄视频的每一帧
- 从BGR转换为HSV色彩空间
- 我们将HSV图像阈值为一系列蓝色
- 现在单独提取蓝色对象，我们可以对我们想要的图像做任何事情。

函数``cv2.inRange()``

一个阈值处理函数，函数很简单，参数有三个

> 第一个参数：hsv指的是原图

> 第二个参数：lower_red指的是图像中低于这个lower_red的值，图像值变为0

> 第三个参数：upper_red指的是图像中高于这个upper_red的值，图像值变为0,而在lower_red～upper_red之间的值变成255

```Python
import cv2
import numpy as np

cap = cv2.VideoCapture(0)

while(1):
    f , frame = cap.read()
    #转换颜色空间
    hsv = cv2.cvtColor(frame , cv2.COLOR_BGR2HSV)

    lower_blue = np.array([0, 35 , 0])

    upper_blue = np.array([43, 255 , 255])
    
    #lower_blue = np.array([0, 35 , 0])检测人脸的一组数据
    #upper_blue = np.array([43, 255 , 255]) 

    mask = cv2.inRange(hsv , lower_blue , upper_blue)

    res = cv2.bitwise_and(frame , frame , mask=mask)

    cv2.imshow('frame' , frame)
    cv2.imshow('mask' , mask)
    cv2.imshow('res' , res)

    if cv2.waitKey(10) & 0xFF == ord('q'):
        break
cv2.destroyAllWindows()


```

颜色转换：

```
green = np.uint8([[[0,255,0 ]]])
hsv_green = cv2.cvtColor(green,cv2.COLOR_BGR2HSV)
prin(hsv_green)
```

