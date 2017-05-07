摘要

电子显微镜下双目图像的物体断裂表面形状的三维重建是微观力学和断裂力学的研究应用。 三维形状重建是通过分析双目图像上匹配对应的点来实现的。 "interesting points" 可以有效的减少匹配失败的问题，同时也减少了大量的CPU计算时间。 "interesting points" 是在像素级别上搜索到的双目图像中重要的特征点，然后仅匹配这些"interesting points"。本文提供了两个搜索 "interesting points"算法。第一个算法是选择局部熵\(local entropy\)最大值的像素点。  第二个方法首先对图像使用基于拉普拉斯算子的高斯滤波处理，然后选择拥有局部最大灰度值的像素点。 同时，为了减少影子的影响，在高斯滤波之前，需要对图像强度进行一次对数计算。 在双目图像中匹配对应的点，是基于互相关\(cross correlation\)的理论。 为了减少匹配失败，本文使用了由粗到细的匹配算法\(a coarse and fine matching algorithm\)。 最后，物体表面的高度是基于双目三角测试理论进行计算的。

  


关键词：  形状重建\(shape reconstruction\) ， 互相关\(cross correlation\)， 图像处理\(image processing\)，实验力学\(experimental mechanics\)

