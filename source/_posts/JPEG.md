JPEG编码

[jpeg官网]https://jpeg.org/
JPEG是一种对数字图像进行有损压缩的常用方法，特别是对由数码摄影产生的图像。JPEG(Joint Photographic Experts Group)是在国际标准化组织(ISO)领导之下制定静态图像压缩标准的委员会，第一套国际静态图像压缩标准ISO 10918-1(JPEG)就是该委员会制定的。由于JPEG优良的品质，被广泛应用于互联网和数码相机领域，互联网上大多数的图像都采用了JPEG压缩标准，目前JPEG格式可以分为标准JPEG、渐进式JPEG和JPEG2000三种。

# 1.JPEG编码过程 #
## 1.彩色空间转换和降采样 ##
首先，图像像素格式应该从RGB转换成YCBCR。它具有三个分量，其中Y分量表示像素的亮度，CB和CR分量表示色度和饱和度，在转换完成之后，还需要对YCBCR进行颜色分量的降采样，一般采用4:2:0格式。
## # 2.DCT变换 # ##
DCT（DiscreteConsineTransform）是将图像数据在频率域上分离出高频和低频信息的过程。然后再对图像的高频部分（即图像细节）进行压缩，以达到压缩图像数据的目的。在JPEG标准中首先将一帧图像划分为多个8*8的矩阵。然后对每一个矩阵作DCT变换。变换后将得到一个浮点频率系数矩阵。
## # 3.量化 # ##
由于JPEG在后续编码过程中使用的码本都是整数，因此需要对变换后的频率系数进行量化，将浮点数转换为整数。进行数据量化后，矩阵中的数据都是近似值，和原始图像数据之间有了差异，会造成图像压缩后失真。
## # 4.编码 # ##
编码采用两种机制：一种是0值的行程长度编码；二是熵编码（EntropyCoding）。在JPEG中，采用曲徊序列，即以矩阵对角线的法线方向作“之”字排列矩阵中的元素。这样做的优点是使得靠近矩阵左上角、值比较大的元素排列在行程的前面，而行程的后面所排列的矩阵元素基本上为0值。行程长度编码是非常简单和常用的编码方式，在此不再赘述。编码实际上是一种基于统计特性的编码方法。在JPEG中允许采用HUFFMAN编码或者算术编码。

# 2.JPEG图片格式详解 #
JPEG图片格式组成部分有： 
SOI（文件头）+APP0（图像识别信息）+ DQT（定义量化表）+ SOF0（图像基本信息）+ DHT（定义Huffman表） + DRI（定义重新开始间隔）+ SOS（扫描行开始）+ EOI（文件尾）
