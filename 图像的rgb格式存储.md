这是一个很基本的问题，但是以前没有仔细的考虑这个问题。

现在的彩色图像是有三原色构成的，这个没有问题，算是基本常识。

如果你训练过cnn模型，那么这里有一个tensor的概念，tensor，张量，因为tensorflow的名字，让大家熟悉起来了。

张量流，这个名字不错。

其实tensor是一个通用的概念，向量vector，矩阵matrix 都可以用tensor来表示。

---

tensor是逻辑上的概念，一般实现都是一块连续的内存，可以认为是一维数组，那么怎么把实际的物理内存和逻辑上的tensor概念联系起来呢。

引入一个概念shape，表示形状，比如（1，2，3）表示的就是3维的，分别为1，2，3.

还有就是dtype，mxnet和tf中，都用这个来表示数据类型，这个的全称是data type.

---

彩色图像一般可以表示为（3，width，higth），其实也可以表示为（width，highth，3）

这个有什么区别吗，有区别
三个纬度分别表示为rgb，那么我们来分析一下这个（3，width,highth）和（width,highth,3）

---

### 第一种情况（3，width，highth）
这个是怎么一个情况，

（3,8,8）

（rrrrrrrrggggggggbbbbbbbb）

(8,3,3)
(rgbrgbrgbrgbrgbrgbrgb)
