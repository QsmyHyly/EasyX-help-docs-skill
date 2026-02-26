# IMAGE

图像对象。
    
    
    class IMAGE(int width = 0, int height = 0);
    

## 公有成员

### int getwidth();

返回 IMAGE 对象的宽度，以像素为单位。

### int getheight();

返回 IMAGE 对象的高度，以像素为单位。

### operator =

实现 IMAGE 对象的直接赋值。该操作仅拷贝源图像的内容，不拷贝源图像的绘图环境。

## 示例

以下代码片段创建 img1、img2 两个对象，之后加载图片 test.jpg 到 img1，并通过赋值操作将 img1 的内容拷贝到 img2：
    
    
    IMAGE img1, img2;
    loadimage(&img1, _T("test.jpg"));
    img2 = img1;
    

以下代码片段创建 img 对象，之后加载图片 test.jpg，并将图片的宽高赋值给变量 w、h：
    
    
    IMAGE img;
    loadimage(&img, _T("test.jpg"));
    int w, h;
    w = img.getwidth();
    h = img.getheight();
    

更多示例请参考 [putimage](putimage.htm) 函数示例。
