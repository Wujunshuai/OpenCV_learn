### 视频处理

------

####  视频截取图片

1. 创建一个VideoCapture对象,从摄像头读取视频
2. 一般来说，该函数每25毫秒读取一帧，也就是说，视频合适帧数在40帧。

```python
import numpy as np
import cv2

cap = cv2.VideoCapture(0)
#从0开始是第一个摄像头，依次向后增加
#讲数字改为视频的地址即可读取视频

while(True):
    # Capture frame-by-frame
    ret, frame = cap.read()
    #返回一个bool值ret,如果正确读取帧，则它将为True

    # Our operations on the frame come here
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)

    # Display the resulting frame
    cv2.imshow('frame',gray)
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
 #When everything done, release the capture
cap.release()
cv2.destroyAllWindows()
```

##### 注意：

有时候摄像头无法正常启动，可以**cap.isOpened（）**方法检查它是否已初始化。如果是没问题，那好的。否则使用**cap.open（）**打开它

-----

##### **cap.get（propId）**方法访问此视频的某些功能，其中propId是0到18之间的数字。每个数字表示视频的属性（如果它适用于该视频）

```txt
CV_CAP_PROP_POS_MSEC视频文件的当前位置（以毫秒为单位）或视频捕获时间戳。
CV_CAP_PROP_POS_FRAMES接下来要解码/捕获的帧的基于0的索引。
CV_CAP_PROP_POS_AVI_RATIO视频文件的相对位置：0 - 电影的开始，1 - 电影的结尾。
CV_CAP_PROP_FRAME_WIDTH视频流中帧的宽度。
CV_CAP_PROP_FRAME_HEIGHT视频流中帧的高度。
CV_CAP_PROP_FPS帧速率。
CV_CAP_PROP_FOURCC编解码器的4字符代码。
CV_CAP_PROP_FRAME_COUNT视频文件中的帧数。
CV_CAP_PROP_FORMAT返回的Mat对象的格式retrieve()。
CV_CAP_PROP_MODE指示当前捕获模式的特定于后端的值。
CV_CAP_PROP_BRIGHTNESS图像的亮度（仅适用于相机）。
CV_CAP_PROP_CONTRAST图像对比度（仅适用于相机）。
CV_CAP_PROP_SATURATION图像的饱和度（仅适用于相机）。
CV_CAP_PROP_HUE图像的色调（仅适用于相机）。
CV_CAP_PROP_GAIN图像的增益（仅适用于相机）。
CV_CAP_PROP_EXPOSURE曝光（仅适用于相机）。
CV_CAP_PROP_CONVERT_RGB布尔标志，指示是否应将图像转换为RGB。
CV_CAP_PROP_WHITE_BALANCE_U白平衡设置的U值（注意：目前仅支持DC1394 v 2.x后端）
CV_CAP_PROP_WHITE_BALANCE_V白平衡设置的V值（注意：目前仅支持DC1394 v 2.x后端）
CV_CAP_PROP_RECTIFICATION立体摄像机的整流标志（注意：目前仅支持DC1394 v 2.x后端）
CV_CAP_PROP_ISO_SPEED摄像机的ISO速度（注意：目前仅支持DC1394 v 2.x后端）
CV_CAP_PROP_BUFFERSIZE存储在内部缓冲存储器中的帧数（注意：目前仅支持DC1394 v 2.x后端）
```

##### cap.set(propId ， value)   修改视频，其中，propld是0到18的数字，用来选择视频属性，value代表修改值。

```
CV_CAP_PROP_POS_MSEC视频文件的当前位置（以毫秒为单位）。
CV_CAP_PROP_POS_FRAMES接下来要解码/捕获的帧的基于0的索引。
CV_CAP_PROP_POS_AVI_RATIO视频文件的相对位置：0 - 电影的开始，1 - 电影的结尾。
CV_CAP_PROP_FRAME_WIDTH视频流中帧的宽度。
CV_CAP_PROP_FRAME_HEIGHT视频流中帧的高度。
CV_CAP_PROP_FPS帧速率。
CV_CAP_PROP_FOURCC编解码器的4字符代码。
CV_CAP_PROP_FRAME_COUNT视频文件中的帧数。
CV_CAP_PROP_FORMAT返回的Mat对象的格式retrieve()。
CV_CAP_PROP_MODE指示当前捕获模式的特定于后端的值。
CV_CAP_PROP_BRIGHTNESS图像的亮度（仅适用于相机）。
CV_CAP_PROP_CONTRAST图像对比度（仅适用于相机）。
CV_CAP_PROP_SATURATION图像的饱和度（仅适用于相机）。
CV_CAP_PROP_HUE图像的色调（仅适用于相机）。
CV_CAP_PROP_GAIN图像的增益（仅适用于相机）。
CV_CAP_PROP_EXPOSURE曝光（仅适用于相机）。
CV_CAP_PROP_CONVERT_RGB布尔标志，指示是否应将图像转换为RGB。
CV_CAP_PROP_WHITE_BALANCE_U白平衡设置的U值（注意：目前仅支持DC1394 v 2.x后端）
CV_CAP_PROP_WHITE_BALANCE_V白平衡设置的V值（注意：目前仅支持DC1394 v 2.x后端）
CV_CAP_PROP_RECTIFICATION立体摄像机的整流标志（注意：目前仅支持DC1394 v 2.x后端）
CV_CAP_PROP_ISO_SPEED摄像机的ISO速度（注意：目前仅支持DC1394 v 2.x后端）
CV_CAP_PROP_BUFFERSIZE存储在内部缓冲存储器中的帧数（注意：目前仅支持DC1394 v 2.x后端）
```

---

### 视频保存

我们捕获视频，并逐帧处理，照片使用cv2.imwrite(),视频使用类似读取视频的方法。

1. 创建一个VideoWriter对象，制定输出的文件名
2. 指定FourCC代码，传递每秒帧数和帧大小
3. 设定isColor标志，True为编码器需要彩色帧，否则需要灰度帧

**FourCC**是一个(占)四字节代码，用于指定视频编解码器，可在[fourcc.org](http://www.fourcc.org/codecs.php)中查看代码详情，它取决于不同的平台

#### FourCC简介：

FOURCC是“四字符代码”的缩写 - “四字符代码” - 媒体文件中使用的视频编解码器，压缩格式，颜色或像素格式的标识符，占四字节，32位。FOURCC的另一种方法是4CC。

一些比较知名的FOURCC包括“AVI”，“DIVX”，“XVID”，“H264”，“DX50”

```t
建议：
1. 在Fedora中：DIVX，XVID，MJPG，X264，WMV1，WMV2（XVID更为可取.MJPG会产生高大小的视频.X264可以提供非常小的视频）
2.在Windows中：DIVX（更多要测试和添加）
```

FourCC代码作为MJPG 传递

`cv2.VideoWriter_fourcc('M','J','P','G')`

或传递

`cv2.VideoWriter_fourcc(*'MJPG')`给MJPG

##### 案例

```python
import numpy as np
import cv2

cap = cv2.VideoCapture(0)

# Define the codec and create VideoWriter object
#设定 4CC格式，即视频解码器
fourcc = cv2.VideoWriter_fourcc(*'XVID')
#传入保存的路径、4CC、帧率、帧的大小
out = cv2.VideoWriter('output.avi',fourcc, 20.0, (640,480))

while(cap.isOpened()):
    ret, frame = cap.read()
    if ret==True:
        frame = cv2.flip(frame,0)#翻转图片函数，垂直翻转

        # write the flipped frame
        out.write(frame)

        cv2.imshow('frame',frame)
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
    else:
        break

# Release everything if job is finished
cap.release()
out.release()
cv2.destroyAllWindows()
```

