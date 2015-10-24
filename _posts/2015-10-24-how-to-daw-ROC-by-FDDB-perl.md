---
layout: post
title: 'how to daw ROC by FDDB perl'
date: '2015-10-24'
header-img: "img/home.jpg"
tags:
     - 计算机视觉
     - 人脸检测
author: 'Kingback'
---

做人脸检测方面的算法得有一个评估的工具在标准的人脸库上的性能评估，[FDDB](http://vis-www.cs.umass.edu/fddb/)(Face Detection Data Set and Benchmark)就是这样的人脸检测标准库。Face++就在2014年在这个库上夺得了[桂冠](http://www.faceplusplus.com.cn/fddb/)。人脸检测性能很重要的曲线就是ROC曲线，来表示算法在人脸库上的误检数和召回率。下面就来讲讲如何利用FDDB官网的脚本生成自己算法的ROC曲线，并和其他算法compare的ROC曲线。


## 1.在FDDB官网下载图片和标注文件
下载originalPics（里面是数据库图片），FDDB-folds（里面有图片目录和标注人脸椭圆框的ground truth）

## 2.人脸检测算法输出txt
读FDDB-fold-%02d.txt文件，依照顺序做人脸检测，将检测结果输出出来，文件名格式如 fold-%02d-out.txt，格式如下：

    <image name i>
    <number of faces in this image =im>
    <face i1>
    <face i2>
    ...
    <face im>

`<face im>`的表达形式有一下两种：

- Rectangular regions. Each face region is represented as:`<left_x top_y width height detection_score>`
- Elliptical regions. Each face region is represented as: `<major_axis_radius minor_axis_radius angle center_x center_y detection_score>`
这里的detection_score是我们自己算法得出的是人脸的分数。

## 3.linux下编译生成FDDB 评估脚本
下载evaluation.tgz，修改Makefile 文件如下：

    NCS = -I/usr/local/include/opencv

    LIBS = -L/usr/local/lib -lopencv_core -lopencv_imgproc -lopencv_highgui
           -lopencv_ml -lopencv_video -lopencv_features2d -lopencv_calib3d
           -lopencv_objdetect -lopencv_contrib -lopencv_legacy

    evaluate: $(OBJS)

         $(CC) $(OBJS) -o $@ $(LIBS)

就会生成evaluate和runEvaluate.pl两个脚本。修改runEvaluate.pl：

- $GNUPLOT gnuplot路径
- $evaluateBin 编译生成的evaluate可执行文件
- $imDir  FDDB数据库图片文件目录
- $fddbDir FDDB folds目录
- $detDir 人脸检测输出10个txt的目录
- $detFormat 人脸框的形式，如果是矩形则是0，椭圆是1，默认是0

## 4.生成ROC曲线
执行脚本./runEvaluate.pl，出现的Could not find/open font when opening font "arial", using internal non-scalable font，字体warning可以忽略。
可以看到$detDir生成了ContROC.png，ContROC.txt，DiscROC.png，DiscROC.txt。我生成的DiscROC.png如下，有点没明白就是横坐标只有80，难道是我算法对误检处理的太好了？
![img](/img/2015/10/DiscROC.png)

## 5.生成和其他检脸算法的compare_ROC曲线
下载compareROC.tar.gz。将4生成的ContROC.txt， DiscROC.txt拷贝在rocCurves下。对应修改contROC.p和discROC.p路径和title。分别运行contROC.p， discROC.p(gnuplot contROC.p)， 就会看到有contROC-compare.png和discROC-compare.png两个图片，就是compare_ROC曲线图。
![img](/img/2015/10/discROC-compare.png)

### 参考资料
[官方说明](http://vis-www.cs.umass.edu/fddb/fddb.pdf)
[博客](http://blog.csdn.net/phoenix_zhou/article/details/47399819)
