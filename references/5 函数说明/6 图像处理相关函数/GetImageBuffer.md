# GetImageBuffer

这个函数用于获取绘图设备的显示缓冲区指针。
    
    
    DWORD* GetImageBuffer(IMAGE* pImg = NULL);
    

## 参数

### pImg

绘图设备指针。如果为 NULL，表示默认的绘图窗口。

## 返回值

返回绘图设备的显示缓冲区指针。

## 备注

获取到的显示缓冲区指针可以直接读写。

在显示缓冲区中，每个点占用 4 个字节，因此：显示缓冲区的大小 = 宽度 × 高度 × 4 (字节)。像素点在显示缓冲区中按照从左到右、从上向下的顺序依次排列。访问显示缓冲区请勿越界，否则会造成难以预料的后果。

显示缓冲区中的每个点对应 RGBTRIPLE 类型的结构体：
    
    
    struct RGBTRIPLE {
    	BYTE rgbtBlue;
    	BYTE rgbtGreen;
    	BYTE rgbtRed;
    };

RGBTRIPLE 在内存中的表示形式为：0xrrggbb (bb=蓝，gg=绿，rr=红)，而常用的 COLORREF 在内存中的表示形式为：0xbbggrr。注意，两者的红色和蓝色是相反的，请用 BGR 宏交换红色和蓝色。

如果操作绘图窗口的显示缓冲区，请在操作完毕后，执行 FlushBatchDraw() 使操作生效。

## 示例

以下代码通过直接操作显示缓冲区绘制渐变的蓝色：
    
    
    #include <graphics.h>
    #include <conio.h>
    
    int main()
    {
    	// 初始化绘图窗口
    	initgraph(640, 480);
    
    	// 获取指向显示缓冲区的指针
    	DWORD* pMem = GetImageBuffer();
    
    	// 直接对显示缓冲区赋值
    	for(int i = 0; i < 640 * 480; i++)
    		pMem[i] = BGR(RGB(0, 0, i * 256 / (640 * 480) ));
    
    	// 使显示缓冲区生效（注：操作指向 IMAGE 的显示缓冲区不需要这条语句）
    	FlushBatchDraw();
    
    	// 按任意键退出
    	_getch();
    	closegraph();
    }
    
