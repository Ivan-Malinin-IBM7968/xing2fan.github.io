---
title: BMP文件格式
date: 2019-03-09 22:47:44 
categories: "图片处理" 
tags: 图像 BMP
     - BMP
     - 图像
description: 1.记录和介绍BMP的图像格式
---
# 1.BMP格式 #
BMP格式是在**windows系统**中使用较多的一种**图像**文件格式。它可以完整的保存一幅图像所有像素数据。

BMP图像文件里面的数据从文件头开始可分为4个部分，各个部分如下：

1. BMP文件头(BMP file header)：它提供文件的**格式、大小**等信息，一共**14**个字节。
1. 位图信息头(bitmap information)：提供图像数据的尺寸、位平面数、压缩方式、颜色索引等信息，一共**40**个字节。
1. 调色板(color palette)：**可选**，如使用索引来表示图像，调色板就是索引与其对应的颜色的映射表。
1. 位图数据(bitmap data)：就是图像裸数据。  
其中，是否含有调色板信息是根据BMP图像的位数(深度)决定的。比如，24位BMP和32位BMP不包含调色板部分，而对于8位BMP和16位BMP是需要调色板信息的。  


在BMP图像文件中存储数据时，图像像素点数据的扫描方式是按**从左到右、从下到上**的顺序，存放格式是采用“小端序”，即低字节放低地址, 高字节放高地址。  
【举例】比如数字0x12 34 56 78在内存中的表示形式为：  
1)**大端**模式：  
低地址 ------------------> 高地址  
0x12  |  0x34  |  0x56  |  0x78  
2)**小端**模式：  
低地址 ------------------> 高地址  
0x78  |  0x56  |  0x34  |  0x12   
具体内容可参见介绍大小端的博客文章：[详解大端模式和小端模式](https://blog.csdn.net/ce123_zhouwei/article/details/6971544  "With a Title"). 
# 2.BMP文件结构体  #

    /*14Bytes file header*/
    typedef struct 
    {  
    	u_int16_t bfType;   
    	u_int32_t bfSize;   
    	u_int16_t bfReserved1;   
    	u_int16_t bfReserved2;   
    	u_int32_t bfOffBits;  
    }BITMAPFILEHEADER;
	/*40Bytes bmp mapinfo header*/
    typedef struct 
    {  
    	u_int32_t biSize;   
    	u_int32_t biWidth;   
    	u_int32_t biHeight;   
    	u_int16_t biPlanes;   
    	u_int16_t biBitCount;   
    	u_int32_t biCompression;   
    	u_int32_t biSizeImage;   
    	u_int32_t biXPelsPerMeter;   
    	u_int32_t biYPelsPerMeter;   
    	u_int32_t biClrUsed;   
    	u_int32_t biClrImportant;   
    }BITMAPINFOHEADER;
若BITMAPINFOHEADER的成员biHeight为负值, 则表示BMP数据存储方向为从左到右, 从上到下；
若BITMAPINFOHEADER的成员biHeight为正值, 则表示BMP数据存储方向为从左到右, 从下到上；
此外，有时候可能需要将BMP图中的裸数据做BGR和RGB的转换，因为WINDOWS系统下多采用BGR，而有的平台或者系统可能会使用RGB,转换可以如下方式进行，即将每个像素点的红色分量和蓝色分量交换位置。
    
    for(i = 0; i < (w*h); i++)
    {
    	temp = *(bmpBuff + i*3);
    	*(bmpBuff + i*3) = *(bmpBuff + i*3 + 2);
    	*(bmpBuff + i*3 + 2) = temp;	
    }

BMP图像格式还有一个非常重要的规定：要求每一扫描行的字节数据必须能被4整除，如果图像的一行字节数不能被4整除，就需要在每行的末尾补齐0以达到规定。因此，在我们的读取数据中需要根据BMP图像的宽度来判断是否被补0。

先判断一行的字节数是否可以被4整除，如果整除则不需要补0，如果没有整除，则求出补的0的个数，具体计算方法是：
    bu_0_number =4 -  width(像素) * 3 % 4 
那么就知道在每一行的末尾都补了bu_0_number个0，因此我们在读取时绘制图片时就要忽略这些数据。

# 3.BMP文件读写时注意事项 #