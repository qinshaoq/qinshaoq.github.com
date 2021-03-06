---

title: SIFT资源汇总
layout: post
categories: [image processing]
tags: [image processing, program]

---

最近用到了Scale Invariant Feature Transform(SIFT)算法，在此对相关资源作一个汇总。

## 中文资源

### [SIFT算法详解][1]

这个是中文资料里边对SIFT算法解释的最详细的，如果看英文论文不是那么流畅，又想详细了解SIFT整个算法过程，那么这篇博文无疑是你的首选。而且作者在文末给出了VS2010+OpenCV v2.2的一个实现，对照代码应该更容易理解算法的原理。

### [SIFT特征提取-应用篇][2]

如果你暂时不想了解SIFT算法的细节，只是想尽快尝试一下算法的效果，这篇文章很适合你。里边不仅有Matlab代码，C+OpenCV代码，在文章下面链接的另外一篇博文里还有Python+OpenCV的实现。无论你使用的是哪种语言，都可以很快上手使用。另外博主针对SIFT算法也有一系列的博文进行讲解，看看也会对算法原理有一个大致的了解。

### [RobHess的SIFT源码分析：综述][3]

RobHess的opensift([source][4])是一个开源版本的SIFT算法的C实现，代码写得很规范，对于研究该算法的朋友是一个很好的参考。而这篇博文就是对RobHess代码进行解读的文章，看一看对了解代码的结构是很有帮助的。


## 英文资源

### [David Lowe大牛主页][5]

在David Lowe的主页里可以下载到他提出SIFT算法的两篇文章的PDF版本，同时也能下载到一个SIFT demo程序，程序利用mex编译之后可以直接在matlab中使用。不过代码是看不到了，matlab程序中实际上也是调用一个`siftWin32.exe`文件。不过对于了解算法的效果已经是足够了。

### [VLFeat][6]

VLFeat是一系列机器视觉算法的开源实现，包括HOG,SIFT,MSER,k-means,hierarchical k-means等。出于效率的考虑，算法是用C实现的，不过同时提供了MATLAB的接口。

### [MATLAB File Exchange][7]

MATLAB File Exchange上也有几个SIFT算法的实现，但是评价和使用的人数不多。

[1]: http://blog.csdn.net/zddblog/article/details/7521424
[2]: http://blog.csdn.net/abcjennifer/article/details/7365882
[3]: http://blog.csdn.net/masibuaa/article/details/9191309
[4]: https://github.com/robwhess/opensift
[5]: http://www.cs.ubc.ca/~lowe/keypoints/
[6]: http://www.vlfeat.org/
[7]: http://www.mathworks.com/matlabcentral/fileexchange/index?utf8=%E2%9C%93&term=SIFT