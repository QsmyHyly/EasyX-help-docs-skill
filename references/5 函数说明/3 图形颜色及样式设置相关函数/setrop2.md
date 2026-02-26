# setrop2

这个函数用于设置当前设备二元光栅操作模式。
    
    
    void setrop2(int mode);
    

## 参数

### mode

二元光栅操作码。该函数支持全部的 16 种二元光栅操作码，罗列如下：

值 | 描述  
---|---  
R2_BLACK | 绘制出的像素颜色 = 黑色  
R2_COPYPEN | 绘制出的像素颜色 = 当前颜色（默认）  
R2_MASKNOTPEN | 绘制出的像素颜色 = 屏幕颜色 AND (NOT 当前颜色)  
R2_MASKPEN | 绘制出的像素颜色 = 屏幕颜色 AND 当前颜色  
R2_MASKPENNOT | 绘制出的像素颜色 = (NOT 屏幕颜色) AND 当前颜色  
R2_MERGENOTPEN | 绘制出的像素颜色 = 屏幕颜色 OR (NOT 当前颜色)  
R2_MERGEPEN | 绘制出的像素颜色 = 屏幕颜色 OR 当前颜色  
R2_MERGEPENNOT | 绘制出的像素颜色 = (NOT 屏幕颜色) OR 当前颜色  
R2_NOP | 绘制出的像素颜色 = 屏幕颜色  
R2_NOT | 绘制出的像素颜色 = NOT 屏幕颜色  
R2_NOTCOPYPEN | 绘制出的像素颜色 = NOT 当前颜色  
R2_NOTMASKPEN | 绘制出的像素颜色 = NOT (屏幕颜色 AND 当前颜色)  
R2_NOTMERGEPEN | 绘制出的像素颜色 = NOT (屏幕颜色 OR 当前颜色)  
R2_NOTXORPEN | 绘制出的像素颜色 = NOT (屏幕颜色 XOR 当前颜色)  
R2_WHITE | 绘制出的像素颜色 = 白色  
R2_XORPEN | 绘制出的像素颜色 = 屏幕颜色 XOR 当前颜色  
  
注：

  1. AND / OR / NOT / XOR 为布尔运算。
  2. "屏幕颜色"指绘制所经过的屏幕像素点的颜色。
  3. "当前颜色"是指将要绘制的颜色。



## 返回值

无

## 备注

该函数设置的二元光栅操作码仅影响线条和填充（包括 IMAGE 填充）的输出，不影响文字和 IMAGE 的输出。

## 示例

无
