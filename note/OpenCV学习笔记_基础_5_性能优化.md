# 性能测量和改进技术

在图像处理中，由于您每秒处理大量操作，因此您的代码不仅要提供正确的解决方案，还要以最快的方式提供。所以在本章中，您将学习

> - 衡量代码的性能。
> - 一些提高代码性能的技巧。
> - 您将看到以下函数：**cv2.getTickCount**，**cv2.getTickFrequency**等



### 使用OpenCV测量性能

---

#### **cv2.getTickCount（）**

**cv2.getTickCount**函数返回参考事件（如机器开启时刻）到调用此函数的时钟周期数。因此，如果在函数执行之前和之后调用它，则会获得用于执行函数的**时钟周期数**。

#### **cv2.getTickFrequency**

**cv2.getTickFrequency**函数返回时钟**周期的频率**或**每秒的时钟周期数**。因此，要在几秒钟内找到执行时间

```python
e1 = cv2.getTickCount()
# code
e2 = cv2.getTickCount()
time = (e2 - e1)/ cv2.getTickFrequency(
```





### OpenCV中的默认优化

---

许多OpenCV功能都使用SSE2，AVX等进行了优化。编译时默认启用它。因此，OpenCV在启用时运行优化代码，否则运行未优化代码。

可以使用**cv2.useOptimized（）**来检查它是否已启用/禁用，并使用**cv2.setUseOptimized（）**来启用/禁用它。





### 在IPython中测量性能

----

**IPython**提供了**`%timeit`**执行此操作的神奇命令。它运行代码几次以获得更准确的结果





**注：**

Python标量操作比Numpy标量操作更快。因此对于包含一个或两个元素的操作，Python标量比Numpy数组更好。当阵列的大小稍大时，Numpy会占据优势。



#### 其他优化措施

1. 尽量避免在Python中使用循环，尤其是双循环/三循环等。它们本质上很慢。
2. 将算法/代码矢量化到最大可能范围，因为Numpy和OpenCV针对向量运算进行了优化。
3. 利用缓存一致性。
4. 除非需要，否则永远不要复制数组。尝试使用视图。阵列复制是一项昂贵的操作。
5. 使用其他库（如Cython）来加快速度



### 其他资源

---

1. [Python优化技术](http://wiki.python.org/moin/PythonSpeed/PerformanceTips)
2. Scipy讲义 - [高级Numpy](http://scipy-lectures.github.io/advanced/advanced_numpy/index.html#advanced-numpy)
3. [IPython中的时序和分析](http://pynash.org/2013/03/06/timing-and-profiling.html)