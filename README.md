# PythonComputerVision-6-CameraCalibration
使用Python及OpenCV实现相机参数标定
# 一.针孔相机模型原理：
在相机模型中，针孔相机是相对简单而常用的模型。简单的说，针孔相机模型就是把相机简化成小孔成像，如图下图，f标注的距离是焦距。  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/1.jpg)  
## 简单的相机标定原理：
相机标定(Camera Calibration)可以理解为从普通世界坐标系变换到图像坐标系的过程，传统三平面标定板制作难度较大，所以这里我们讨论**“张正友相机标定法（棋盘格标定）”**。  
简单来说，相机参数标定分为两大步骤————世界坐标系到相机坐标系的转换以及相机坐标系到图像坐标系的转换。前者是相机外参（R，t）等参数的转换，后者则是包括相机内参（K）等参数。  
#### 相机坐标系到图像坐标系的转换：
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/2.jpg)  
如上图所示（图片来自UIUC计算机视觉课件），是一个小孔成像的模型，其中：  
·C点表示camera centre，即相机的中心点，也是相机坐标系的中心点;  
·Z轴表示principal axis，即相机的主轴；  
·P点所在的平面表示image plane，即相机的像平面，也就是图片坐标系所在的二维平面；  
·P点表示principal point，即主点，主轴与像平面相交的点；  
·C点到P点的距离，也就是右边图中的f表示focal length，即相机的焦距；  
·像平面上的x和y坐标轴是与相机坐标系上的X和Y坐标轴互相平行的；  
·相机坐标系是以X,Y,Z（大写）三个轴组成的且原点在C点，度量值为米（m）；  
·像平面坐标系是以x,y（小写）两个轴组成的且原点在P点，度量值为米（m）；  
·图像坐标系一般指图片相对坐标系，在这里可以认为和像平面坐标系在一个平面上，不过原点是在图片的角上，而且度量值为像素的个数（pixel）；  
  
  如果知道相机坐标系中的一个点X（现实三维世界中的点），在像平面坐标系对应的点是x，要求求从相机坐标系转为像平面坐标系的转换，也就是从 X点的(X,Y,Z)(X,Y,Z)通过一定的转换变为x点的(x,y)(x,y)。注意：(X,Y,Z)（大写）是在相机坐标系，而(x,y)（小写）是在像平面坐标系（还不是图像坐标系，原点不同。）我们可以得到如下变换：  
  ![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/3.jpg)  
  
齐次坐标矩阵运算表示为：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/4.jpg)  
通过上面，可以把相机坐标系转换到像平面坐标系，但是像平面坐标系和图像坐标系虽然在同一个平面上，但是原点并不是同一个，而目标是要转换到图像坐标系下，所以还需要加入偏移量，如图所示：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/5.jpg)  
如上图所示，其中主点P是像平面坐标系的原点，但在图像坐标系中的位置为(Px,Py) ,在这里，图形坐标系的原点是图片的左下角，所以可以得到：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/6.jpg)  
相当于在上面的基础上加了一个P点坐标的偏移量，同时可以表示为矩阵计算为（在这里用的是齐次坐标的表示方式）：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/7.jpg)  
整理得：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/8.jpg)  
所以最后可以得到相机内参 K：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/9.jpg)  
以及投影矩阵 P（在这里可以认为旋转矩阵 R 为单位矩阵 I，平移矩阵 t 都为0，这也是为什么要拆成这种方式），为：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/10.jpg)  
# 二.相机标定示例：  
## 1.基本步骤：
①准备一张棋盘标定纸。  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/0.jpg)  
②拍摄不同角度的照片，10-20张为佳。  
③对每张图片提取角点信息（对应代码中findChessboardCorners(image,(w,h),None)函数）。  
④对每张图片，进一步提取亚像素角点信息（cornerSubPix(gray, corners, (winsize, winsize), (-1, -1), criteria)函数）。  
⑤相机标定。  
## 2.示例结果：
## 3.源码：
~~~python
~~~
