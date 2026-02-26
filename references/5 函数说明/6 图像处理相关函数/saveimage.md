# saveimage

这个函数用于保存绘图内容至图片文件。
    
    
    void saveimage(
    	LPCTSTR strFileName,
    	IMAGE* pImg = NULL
    );
    

## 参数

### strFileName

指定目标图片的文件名。图片类型由文件名的扩展名指定，支持 bmp / gif / jpg / png / tif 格式。保存文件时，已存在的文件将被覆盖。

### pImg

指定源 IMAGE 对象的指针。如果为 NULL，表示绘图窗口。

## 返回值

无

## 示例

以下示例保存绘图窗口的内容为 "D:\test.bmp"：
    
    
    #include <graphics.h>
    #include <conio.h>
    
    int main()
    {
    	// 绘图窗口初始化
    	initgraph(640, 480);
    
    	// 绘制图像
    	outtextxy(100, 100, _T("Hello World!"));
    	
    	// 保存绘制的图像
    	saveimage(_T("D:\\test.bmp"));
    
    	// 按任意键退出
    	_getch();
    	closegraph();
    }
    
