# GetImageHDC

这个函数用于获取绘图设备句柄(HDC)。
    
    
    HDC GetImageHDC(IMAGE* pImg = NULL);
    

## 参数

### pImg

绘图设备指针。如果为 NULL，表示默认的绘图窗口。

## 返回值

返回绘图设备句柄(HDC)。

## 备注

获取到的 HDC 句柄可以用在 Windows GDI 函数中。

每个 IMAGE 对象都有一个 HDC 句柄，可以通过 HDC 句柄实现对该 IMAGE 的 GDI 函数操作。在同一个 IMAGE 设备中，请勿混用 EasyX 绘图函数和 GDI 绘图函数。

## 示例
    
    
    #include <graphics.h>
    #include <conio.h>
    
    int main()
    {
    	// 初始化绘图窗口
    	initgraph(640, 480);
    
    
    	// 获取默认绘图窗口的 HDC 句柄
    	HDC hdc = GetImageHDC();
    
    	// 执行 Windows GDI 绘图函数
    	MoveToEx(hdc, 10, 10, NULL);
    	LineTo(hdc, 100, 100);
    
    	// 创建大小为 200x200 的 img 对象
    	IMAGE img(200, 200);
    
    	// 获取该 img 对象的 HDC 句柄
    	hdc = GetImageHDC(&img);
    
    	// 执行 Windows GDI 绘图函数
    	Ellipse(hdc, 0, 50, 199, 150);
    
    	// 将 img 对象显示到绘图窗口上面
    	putimage(100, 0, &img);
    
    
    	// 按任意键退出
    	_getch();
    	closegraph();
    }
    
