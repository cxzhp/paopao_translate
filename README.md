# 摘要

电子显微镜下双目图像的物体断裂表面形状的三维重建是微观力学和断裂力学的研究应用。 三维形状重建是通过分析双目图像上匹配对应的点来实现的。 "interesting points" 可以有效的减少匹配失败的问题，同时也减少了大量的CPU计算时间。 "interesting points" 是在像素级别上搜索到的双目图像中重要的特征点，然后仅匹配这些"interesting points"。本文提供了两个搜索 "interesting points"算法。第一个算法是选择局部熵\(local entropy\)最大值的像素点。  第二个方法首先对图像使用基于拉普拉斯算子的高斯滤波处理，然后选择拥有局部最大灰度值的像素点。 同时，为了减少影子的影响，在高斯滤波之前，需要对图像强度进行一次对数计算。 在双目图像中匹配对应的点，是基于互相关\(cross correlation\)的理论。 为了减少匹配失败，本文使用了由粗到细的匹配算法\(a coarse and fine matching algorithm\)。 最后，物体表面的高度是基于双目三角测试理论进行计算的。

关键词：  形状重建\(shape reconstruction\) ， 互相关\(cross correlation\)， 图像处理\(image processing\)，实验力学\(experimental mechanics\)

# 1.介绍

在微观力学中，物体材质的研究是微型的或更小的规模。在这种角度下，材料的表面是非常粗糙的。 微观力学从3个维度来研究物体的材质，并进行测量：外形，形变 和 应力。这三个维度的测量变的越来越重要，这是因为所有的理论都需要基于一个实验结果，并且在实验中验证。而在所有的这些测量技术中， 外形的测定需要使用计算机视觉和图像处理,这两个都不是非常成熟的领域。

在计算机视觉领域的形状重建领域，有大量的令人印象深刻的研究结论\[引用中的2，3，7，8，9，10，11，12\]， 但是大部分都集中在宏观项目的三维重建，然而物体特征和表面在微米级别有一些明显的不同。有好多种方法可以对物体进行三维重建， 比如双目图像，纹理，明暗阴影。 同纹理和明暗阴影相比，双目图像的物体外形重建需要相对较少的假设。同时，在电子显微镜下可以通过改变样本的角度来获得双目图像。因此，在大多数例子中，双目图像和其他方法相比，拥有有更高的精准度。

电子显微镜\(SEM\)下， 在双目图像中匹配对应点的研究已经在应用了\(Russ\[1\], Lee\[4\], Thompson\[5\]  , and Moravec\[6\]\)， 它是基于互相关\(cross correlation\)理论。为了减少运算的时间消耗，本文开发出了搜索"interesting points"的算法，这个算法仅仅在已经选中的像素点中匹配。匹配窗口的大小\(matching-window size\)的效果也在一些计算机视觉的研究者中进行讨论，因此在未来还需要开发出更鲁棒和更快的算法。

本篇论文提供了一个物体形状的三维重建算法，这个算法是根据电子显微镜下拿到的双目图像，针对破裂物体的表面的进行三维重建。第二部分，开发并讨论了两个搜索"interesting points"的算法，并对基于互相关\(cross correlation\) 理论的"interesting points"匹配算法进行了详细的讨论。第三部分，给出了几个实验结果， 包括验证了真实的断裂的表面重建的效果。第四部分是讨论和结论。

# 2.算法

本算法是从一对双目图像中提取信息，并计算出表面的立体图。这个任务困难的地方是从两幅图中找出对应的位置，这是因为目标匹配失败问题\(false-target problem\)。 我们知道，在人类的视觉中，由于视觉上的错觉，目标匹配失败\(false stereo-matching\)也是会发生的。因此，匹配问题是算法首先要解决的第一个问题。

### 2.1 搜索 interesting points

从两张图中匹配每一个点是计算密集型任务，而且有时甚至是行不通的，因为在搜索范围内有非常多相似的特征点，这些点可能比正确的点还要像。因此，搜索"interesting points"是我们算法中第一个重要的事情，之后的匹配则仅仅针对"interesting points"。

"interesting points" 是一对图片中重要特征的像素位置。通常他们是倾斜的并且不连续的。或者和其他像素点相比，有比较大的局部差异或者明显的亮度的偏差。

简单的匹配"interesting points" 和 匹配像素中的每一个点相比，速度并不会快，但它会更鲁棒。 从"interesting points" 的定义可以看出，"interesting poinsts"在局部的亮度中一个有很大的差异，在互相关\(cross correlation\)中是一个很大的值，和周围的点相比会有很大的差异。

##### 1\) 搜索"interesting points"的两个算法：

**a. 局部熵算法**

第一个是将局部熵的最大值作为"interesting points"。  局部熵的计算是基于周围NxN的邻居每个点的亮度值，如下图：![](/assets/a.png)

其中：$$g_i$$是第i个点的灰度值。 n\($$g_i$$\)是一个比率， 是在匹配区域中，和$$g_i$$点灰度值相同的所有像素点的数量/总的像素点的数量。

**b.高斯滤波器算法**

另一个搜索 "interesting points"的方法是使用拉普拉斯算子的高斯滤波器。 为了减少影子的影响，需要首先对图像强度进行一次对数计算，然后使用拉普拉斯算子的高斯滤波器进行卷积计算 。  在我们的程序中，高斯滤波的标准差是1.5像素。![](/assets/f1.png)其中$$▽^2G$$是拉普拉斯算子的高斯滤波器的核。 "interesting points"是拉普拉斯的高斯滤波后，局部值最大的那个点。

"interesting points"的数量依赖于图像的特征，而经常的情况是"interesting points"的数量非常的小。 比如一张512x512的图片可能只有几千个 "interesting points"

### 2.2 匹配对应点

匹配对应点的算法是基于互相关\(cross correlation\)的理论。匹配点是找出最相似的那个点。我们会将双目两个图像中的进行图片分片，划分为若干个片断，然后对图片片断逐个处理。我们定义f\(i,j\)是第一张图像的图片片断中 \(i,j\) 点的灰度值，g\(i,j\) 是第二张图像对应的图片片断中对应的\(i,j\)点的灰度值。 然后归一化互相关\(cross correlation\) 函数就可以计算出每一个相关点\(m,n\)的值并用于测试。

![](/assets/f2.png)

考虑到不同光照强度下，一张图片可能会认为是两张不同的图片的情况，需要对$$f(j,k)$$和 $$g(j,k)$$进行归一化处理，归一化处理的方法是  减去两个灰度值的均值 $$f_m$$ 和$$g_m$$， 如下面表达式的那样：

$$σ_f$$是第一张图像的图片片断中灰度值的标准差。$$σ_g(m,n)$$是第二个图中，对应位置的图片片断中灰度值的标准差。 我们用法如下：

![](/assets/f3.png)

需要的是，匹配时仅仅处理之前定义的"interesting points"，而并不是所有的像素点。  另一方面，我们需要知道，并不是所有能够在两张图中对应上的点都是"interesting"， 所以，在匹配时，除了互相关的最大值之外，还应该有一些约束。在算法中，先定义了一个粗糙的阈值作为筛选出候选者的条件，然后在这个候选者周围的区域执行进行一个精细的搜索。 这个搜索的结果必然远大于 阈值,进而被认为是正确的的点。

为了减少搜索域，要搜索窗口的位置是由周围的点的视差来决定的,这依赖于表面连续性的假设，而且大多数情况是符合这个假设的。 匹配第一个点时，是没有有效的视差的，所以，程序会从用户那里接收一个估计值，并且搜索域也比其他的点要大。

### 2.3 表面高度的估计

![](/assets/f4.png)

图1 显示了计算表面高度的原理

考虑两个匹配点 A 和B。  左图的A 和 右图的A'匹配 ，  左图的B 和右图的B' 匹配。 左图中，点A和B 的距离用水平线d1表示。 而右图，则经过了一个已知的角度 delta 的偏移，于是点A'  和 B' 会有一个新的距离 d2.  原图中两个点之间的重直起伏\(vertical relief\)用下面的表达式表达：

![](/assets/f5.png)

# 3.算法使用举例

### 3.1 根据表面纹理重建立方体

在本次测试中， 程序将重建一个有纹理的立方体\(图2，图3\)。  如果双目图像是通过相机的移动而产生的，高度是无法被计算出来的\(5\)。这时，我们计算视差的倒数, 它和表面高度是成比例的。 图片的"interesting points"显示在图4，5，6，7中。其中 图4，5 是根据局部熵方法计算出来的。  图6，7是根据拉普拉斯算子的高斯滤波计算出来的。重建出的表面，可以在图8，图9中看到。

在Table 1中可以看到计算出的"interesting points"的数量，从表中可以看出通过局部熵算法搜索到的"interesting points"比拉普拉斯-高斯滤波算法多出2倍。 更多的"interesting points" 意味着更多的细节包含进来， 错误匹配的可能性会增加，计算的时间也会比较长。![](/assets/f6.png)![](/assets/f7.png)![](/assets/f8.png)![](/assets/f9.png)![](/assets/f10.png)

### 3.2 在电子扫描显微镜中重建断裂的表面

在这里，程序的功能是用来重建断裂的物体的表面，它基于双目图像\(由电子扫描显微镜生成\)来进行重建。  双目图像在图10，图11中展示。"interesting points" 在 图12，图13，图14，图15 中展示。 图16是重建后的形状， 是基于拉普拉斯算子的高斯滤波器算出来的。

和Table1一样 ， Table2 比较了根据基于拉普拉斯算子的高斯滤波器和局部熵算法计算出 "interesting points"的数量， 结论也和Table 1是一样的。

# ![](/assets/f11.png)![](/assets/f12.png)![](/assets/f13.png)![](/assets/f14.png)![](/assets/f15.png)

# 4.讨论和结论

基于双目图像进行3维重建是非常复杂的，很难构建一个算法可以适应所有的情况。电子扫描显微镜下的材料表面有它自己的特性，这些特性对于匹配到对应的点很有用处。 比如，表面通常是连续的: 这种情况下，我们就不需要考虑一个表面会在另外一个表面的后面的情况。除此之外,电子扫描显微镜产生的图像里有丰富的细节，包括空隙和微小的断裂，这些对于识别来说，都是有用的特征。因此，电子扫描显微镜下双目图像的形状重建就有可能产出高度精确的结果。

本论文中，提供了一个算法，实现了搜索 "interesting points"和匹配对应点，其中匹配对应点是基于互相关\(cross-correlation\)理论的。讨论了搜索"interesting points" 的两个方式： 一个是使用局部熵的最大值，这个概念通常用于热力学。另外一个是基于拉普拉斯算子的高斯滤波器，它成功的应用于计算机视觉领域的纹理识别。在实验结果中， 我们知道了通常情况下计算出的"interesting points"的数量，使用基于拉普拉斯算子的高斯滤波会比局部熵方式少很多，这意味着更少的时间消耗。同时，这两个算法的结果是非常相近的。 我们还知道了，如果核心窗口的大小有变化，那么不论使用基于拉普拉斯算子的高斯滤波，还是局部熵，得出的 "interesting points"的数量也会变化。 更多的 "interesting points"将展示更多的图像细节。 而另一方面， 更多的"interesting points" 在另外一张图片上找不到，意味着"错误的匹配"。在我们的实验中，我们发现大部分情况下， 基于拉普拉斯算子的高斯滤波比局部熵最大值更有效。 然而, 这个并不能说明会在所有的情况下都有效，在不同材料下的效果需要进一步的观察和讨论。

由于电子显微镜下大多数的断裂图有丰富的细节，因此基于互相关\(cross-correlation\)理论的匹配是比其他形状重建更有效的方法，即使如此，从实验中我们知道，使用互相关\(cross-correlation\) 还是不够的。甚至一些简单的case，仍然需要一些约束来减少错误匹配\(error-matching\)。大部分的约束都是基于图像的相关性的，或者，换句话说， 事实上图像的细节通常情况下是逐渐的变化的。这样，由粗到细的搜索能够有效的减少将要搜索的点，同时提高匹配的精确度。

本论文中提到的匹配算法除了应用在形状重建之外，还可用于测量面内变形\(in-plane deformation\)的应变\(strain\)。  在面内变形的例子中，如果知道两个不同的角度， 那么3D位移\(3D displacement\)和应变场\(strain field\)可以被三角测量理论计算出来。  这样，匹配算法在微观力学的实验中就可以有很多有潜力的应用。

从实验结果可以看出本算法是鲁棒的。在互相关\(cross-correlation\)匹配中， 如果一个随机的匹配错误发生了，那么在匹配点之后通常会产生一个匹配错误。而在我们的算法中，会通过一些约束，用来避免大部分匹配错误。此外，本算法并没有匹配所有像素点，而是仅仅匹配"interesting points"，这样可以减少大量的计算时间。 与此同时，结果也会更加精确，这是因为 "interesting points"总是带有明显的个体特征 ，这样也不容易在这些点上出现错误的匹配。

在我们的算法中，匹配区域\(matching window\)的大小是固定的。在未来，可以将更多的精力放在自动匹配时，如何确定合适的匹配区域的大小上面，这在图像快速变化时将会更有用。

# 5.感谢

我在这里向教授 David, M.Barnett和教授 Huajian Gao真诚的表达感谢， 感谢他们项目上的鼓励和支持。 感谢 Stefan Scherer 提供电子扫描显微镜下表面断裂的双目图像。

# 6.引用

1. Russ, John C. Computer-assisted Microscopy; the Measurement and Analysis of Images, Chap 11, Plenum Press, New York, 1990
2. 2.Vishvjit S. Naiwa, A Guided Tour ofComputer Vision, Chap 11, Addison-Wesley, Reading, Mass., 1993
3. 3.Takeo Kanade, "A stereo matching algorithm with an adaptive window: theory and experiment", Proc. of the 1991 IEEE International Conference on Robotics and Automation
4. Ju-Hyeon Lee, "Metrology of microelecironic devices by stereo SEM", J. of Computer-Assisted Microscopy, Vol. 1 No. 1, 1989

5. K. A. Thompson, "Surface characterization by use of automated stereo analysis and fractals", Microbeam Analysis 1987, San Francisco Press

6. H. P. Moravec, "Towards automatic visual obstacle avoidance", Proc. 5th IJCAI, August 1977, pp. 584

7. J. Barba, "The use of local entropy measures in edge detection for cytological image analysis", J. of Microscopy, 1988

8. B. Julesz, "Binocular Depth Perception of Computer-Generated Pattern", BEll system Technical Journal, Vol. 39, No. 5 \(September\), pp. 1125-1161, 1960

9. L. Matthies, etc. "Kalman Filter-Based Algorithm Estimating Depth from Image Sequences", International Journal of Computer Vision, Vol.3, pp. 209-238, 1989

10. K. A. Stevens, "The Information Content of Texture Gradients, Biological Cybernetics", Vol. 42, pp. 95-105, 1981

11. K. Ikeuchi, etc. "Numerical Shape from Shading and Occluding Boundaries, Artificial Intelligence", Vol. 17, pp. 141-184, 1981

12. J. J. Koenderink, and A. J. van Doom, "Photometric Invariants Related to Solid Shape", Optical Acta, VoL 27, No. 7, pp. 981-996, 1980

---

**【翻译版权声明】泡泡机器人SLAM的所有文章全部由泡泡机器人的成员花费大量心血制作而成的原创内容，希望大家珍惜我们的劳动成果，转载请务必注明出自【泡泡机器人SLAM】微信公众号，否则侵权必究！同时，我们也欢迎各位转载到自己的朋友圈，让更多的人能进入到SLAM这个领域中，让我们共同为推进中国的SLAM事业而努力！**

---

* 译者： 周平
* 审稿： 
* 泡泡机器人翻译负责人： Reality，刘富强
* 以及感谢【泡泡机器人SLAM】社区热心的大牛，达人同学和老师们

---

如果错别字，翻译流畅性问题，或者译者概念搞错，有误导之嫌，或者概念不清晰等，请麻烦告知译者，或写在译文批注里面，译者学识有限，初次翻译和学习，请赐教～～

Ce n'est pas parce que les choses sont difficiles que nous n'osons pas, mais parce que nous n'osons pas qu'elles sont difficiles （Sénèque）

不是因為事情困難，而讓我們不敢做；是因為我們不敢做，事情才變得困難。 （塞內卡）

---

[![](https://stormtiti.gitbooks.io/lsd-slam/content/assets/cca_nc_nd_3.0.png)](https://creativecommons.org/licenses/by-nc-nd/3.0/)

This work is licensed under a[Creative Commons Attribution-NonCommercial-NoDerivs 3.0 Unported License](https://creativecommons.org/licenses/by-nc-nd/3.0/).

全篇仅提供学习，请勿用于商业用途，翻译版权【泡泡机器人】 all right reserved，powered by Gitbook

修订时间： 2017-05-07

