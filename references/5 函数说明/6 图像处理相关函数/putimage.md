# putimage

这个函数的几个重载用于在当前设备上绘制指定图像。
    
    
    // 绘制图像
    void putimage(
    	int dstX,				// 绘制位置的 x 坐标
    	int dstY,				// 绘制位置的 y 坐标
    	IMAGE *pSrcImg,			// 要绘制的 IMAGE 对象指针
    	DWORD dwRop = SRCCOPY	// 三元光栅操作码
    );
    
    
    
    // 绘制图像(指定宽高和起始位置)
    void putimage(
    	int dstX,				// 绘制位置的 x 坐标
    	int dstY,				// 绘制位置的 y 坐标
    	int dstWidth,			// 绘制的宽度
    	int dstHeight,			// 绘制的高度
    	IMAGE *pSrcImg,			// 要绘制的 IMAGE 对象指针
    	int srcX,				// 绘制内容在 IMAGE 对象中的左上角 x 坐标
    	int srcY,				// 绘制内容在 IMAGE 对象中的左上角 y 坐标
    	DWORD dwRop = SRCCOPY	// 三元光栅操作码
    );
    

## 参数

详见函数原型内的注释。

## 备注

三元光栅操作码（即位操作模式），支持全部的 256 种三元光栅操作码，常用的几种如下：

值 | 含义  
---|---  
DSTINVERT | 目标图像 = NOT 目标图像  
MERGECOPY | 目标图像 = 源图像 AND 当前填充颜色  
MERGEPAINT | 目标图像 = 目标图像 OR (NOT 源图像)  
NOTSRCCOPY | 目标图像 = NOT 源图像  
NOTSRCERASE | 目标图像 = NOT (目标图像 OR 源图像)  
PATCOPY | 目标图像 = 当前填充颜色  
PATINVERT | 目标图像 = 目标图像 XOR 当前填充颜色  
PATPAINT | 目标图像 = 目标图像 OR ((NOT 源图像) OR 当前填充颜色)  
SRCAND | 目标图像 = 目标图像 AND 源图像  
SRCCOPY | 目标图像 = 源图像  
SRCERASE | 目标图像 = (NOT 目标图像) AND 源图像  
SRCINVERT | 目标图像 = 目标图像?XOR 源图像  
SRCPAINT | 目标图像 = 目标图像?OR 源图像  
  
注：

  1. AND / OR / NOT / XOR 为布尔运算。
  2. "当前填充颜色"是指通过 setfillcolor 设置的用于当前填充的颜色。
  3. 查看全部的三元光栅操作码请参考这里：[三元光栅操作码](三元光栅操作.htm)。



## 返回值

无

## 示例

以下代码片段将屏幕 (0,0) 起始的 100x100 的图像拷贝至 (200,200) 位置：
    
    
    IMAGE img;
    getimage(&img, 0, 0, 100, 100);
    putimage(200, 200, &img);
    
