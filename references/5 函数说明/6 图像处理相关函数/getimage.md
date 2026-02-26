# getimage  
  
这个函数用于从当前绘图设备中获取图像。
    
    
    // 从当前绘图设备获取图像
    void getimage(
    	IMAGE* pDstImg,		// 保存图像的 IMAGE 对象指针
    	int srcX,			// 要获取图像区域左上角 x 坐标
    	int srcY,			// 要获取图像区域的左上角 y 坐标
    	int srcWidth,		// 要获取图像区域的宽度
    	int srcHeight		// 要获取图像区域的高度
    );
    

## 参数

### pDstImg

保存图像的 IMAGE 对象指针。

### srcX

要获取图像区域的左上角 x 坐标。

### srcY

要获取图像区域的左上角 y 坐标。

### srcWidth

要获取图像区域的宽度。

### srcHeight

要获取图像区域的高度。

## 返回值

无

## 示例

请参考 [putimage](putimage.htm) 函数示例。
