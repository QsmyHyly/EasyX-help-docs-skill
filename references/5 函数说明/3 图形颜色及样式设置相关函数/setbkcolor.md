# setbkcolor

这个函数用于设置当前设备绘图背景色。
    
    
    void setbkcolor(COLORREF color);
    

## 参数

### color

指定要设置的背景颜色。

## 返回值

无

## 备注

在设置背景色之后，并不会改变现有背景色，而是只改变背景色的值，之后再执行绘图语句，例如 outtextxy，会使用新设置的背景色值。

如果需要修改全部背景色，可以在设置背景色后执行 cleardevice()?函数。

## 示例

以下示例实现在蓝色背景下绘制红色的矩形：
    
    
    #include <graphics.h>
    #include <conio.h>
    
    int main()
    {
    	// 初始化绘图窗口
    	initgraph(640, 480);
    
    	// 设置背景色为蓝色
    	setbkcolor(BLUE);
    	// 用背景色清空屏幕
    	cleardevice();
    
    	// 设置绘图色为红色
    	setlinecolor(RED);
    	// 画矩形
    	rectangle(100, 100, 300, 300);
    
    	// 按任意键退出
    	_getch();
    	closegraph();
    }
    
