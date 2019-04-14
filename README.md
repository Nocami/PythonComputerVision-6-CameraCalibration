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
采用下图这种格子数为7×5，内角点为6×4的棋盘格图片：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/111.jpg)  
将其放置在平面上拍摄10张不同角度照片：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/112.jpg)  
提取角点后的照片如下图所示：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/113.jpg)  
得到的相机参数如下：  
**mtx为内参数矩阵，dist为畸变系数：**  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/114.jpg)  
**rvecs为旋转向量：**  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/115.jpg)  
**tvecs为平移向量：**  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/116.jpg)  
**畸变矫正：**  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/118.jpg)  
矫正前：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/13.jpg)  
矫正后：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/111.jpg)  
## 小结：  
拍摄用手机型号为锤子科技出品的Smartisan M1L，其主摄像头为索尼SONY产2300W像素IMX318传感器。我们通过实验测得的其相机参数为下图：  
![image](https://github.com/Nocami/PythonComputerVision-6-CameraCalibration/blob/master/images/117.jpg)
## 3.源码及说明：
~~~python
# -*- coding: cp936 -*-
import cv2
import numpy as np
import glob

# 设置寻找亚像素角点的参数，采用的停止准则是最大循环次数30和最大误差容限0.001
criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 30, 0.001)

# 获取标定板角点的位置
objp = np.zeros((4*6,3), np.float32)
objp[:,:2] = np.mgrid[0:6,0:4].T.reshape(-1,2)  # 将世界坐标系建在标定板上，所有点的Z坐标全部为0，所以只需要赋值x和y

obj_points = []  # 存储3D点
img_points = []  # 存储2D点

images = glob.glob("images4/*.jpg")
i=0;
for fname in images:
    img = cv2.imread(fname)
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    size = gray.shape[::-1]
    ret, corners = cv2.findChessboardCorners(gray, (6, 4), None)
    #print(corners)

    if ret:

        obj_points.append(objp)

        corners2 = cv2.cornerSubPix(gray, corners, (5, 5), (-1, -1), criteria)  # 在原角点的基础上寻找亚像素角点
        #print(corners2)
        if [corners2]:
            img_points.append(corners2)
        else:
            img_points.append(corners)

        cv2.drawChessboardCorners(img, (6, 4), corners, ret)  # 记住，OpenCV的绘制函数一般无返回值
        i+=1;
        cv2.imwrite('conimg'+str(i)+'.jpg', img)
        cv2.waitKey(4000)

print(len(img_points))
cv2.destroyAllWindows()

# 标定
ret, mtx, dist, rvecs, tvecs = cv2.calibrateCamera(obj_points, img_points, size, None, None)

print("ret:", ret)
print("mtx:\n", mtx) # 内参数矩阵
print("dist:\n", dist)  # 畸变系数   distortion cofficients = (k_1,k_2,p_1,p_2,k_3)
print("rvecs:\n", rvecs)  # 旋转向量  # 外参数
print("tvecs:\n", tvecs ) # 平移向量  # 外参数

print("-----------------------------------------------------")

img = cv2.imread(images[2])
h, w = img.shape[:2]
newcameramtx, roi = cv2.getOptimalNewCameraMatrix(mtx,dist,(w,h),1,(w,h))#显示更大范围的图片（正常重映射之后会删掉一部分图像）
print (newcameramtx)
print("------------------使用undistort函数-------------------")
dst = cv2.undistort(img,mtx,dist,None,newcameramtx)
x,y,w,h = roi
dst1 = dst[y:y+h,x:x+w]
cv2.imwrite('calibresult3.jpg', dst1)
print ("方法一:dst的大小为:", dst1.shape)

~~~
