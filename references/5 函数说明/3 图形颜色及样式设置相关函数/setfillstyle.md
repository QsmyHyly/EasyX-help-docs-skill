# setfillstyle

这个函数用于设置当前设备填充样式。
    
    
    void setfillstyle(
    	FILLSTYLE* pstyle
    );
    
    
    
    void setfillstyle(
    	int style,
    	long hatch = NULL,
    	IMAGE* ppattern = NULL
    );
    
    
    
    void setfillstyle(
    	BYTE* ppattern8x8
    );
    

## 参数

### pstyle

指向填充样式 [FILLSTYLE](FILLSTYLE.htm) 的指针。

### style

指定填充样式。可以是以下宏或值：

宏 | 值 | 含义  
---|---|---  
BS_SOLID | 0 | 固实填充。  
BS_NULL | 1 | 不填充。  
BS_HATCHED | 2 | 图案填充。  
BS_PATTERN | 3 | 自定义图案填充。  
BS_DIBPATTERN | 5 | 自定义图像填充。  
  
### hatch

指定填充图案，仅当 style 为 BS_HATCHED 时有效。填充图案的颜色由函数 [setfillcolor](setfillcolor.htm) 设置，背景区域使用背景色还是保持透明由函数 [setbkmode](setbkmode.htm) 设置。hatch 参数可以是以下宏或值：

宏 | 值 | 含义  
---|---|---  
HS_HORIZONTAL | 0 | ?![](/_temp/119/hs_horizontal.gif)  
HS_VERTICAL | 1 | ?![](/_temp/119/hs_vertical.gif)  
HS_FDIAGONAL | 2 | ?![](/_temp/119/hs_fdiagonal.gif)  
HS_BDIAGONAL | 3 | ?![](/_temp/119/hs_bdiagonal.gif)  
HS_CROSS | 4 | ?![](/_temp/119/hs_cross.gif)  
HS_DIAGCROSS | 5 | ?![](/_temp/119/hs_diagcross.gif)  
  
### ppattern

指定自定义填充图案或图像，仅当 style 为 BS_PATTERN 或 BS_DIBPATTERN 时有效。  
当 style 为 BS_PATTERN 时，ppattern 指向的 IMAGE 对象表示自定义填充图案，IMAGE 中的黑色（BLACK）对应背景区域，非黑色对应图案区域。图案区域的颜色由函数 [settextcolor](../5%20文字输出相关函数/settextcolor.htm) 设置。  
当 style 为 BS_DIBPATTERN 时，ppattern 指向的 IMAGE 对象表示自定义填充图像，以该图像为填充单元实施填充。

### ppattern8x8

指定自定义填充图案，效果同 BS_PATTERN，该重载以 BYTE[8] 数组定义 8 x 8 区域的填充图案。数组中，每个元素表示一行的样式，BYTE 类型有 8 位，按位从高到低表示从左到右每个点的状态，由此组成 8 x 8 的填充单元，将填充单元平铺实现填充。对应的二进制位为 0 表示背景区域，为 1 表示图案区域。

## 返回值

无

## 示例

以下代码片段设置固实填充：
    
    
    setfillstyle(BS_SOLID);
    

以下代码片段设置填充图案为斜线填充：
    
    
    setfillstyle(BS_HATCHED, HS_BDIAGONAL);
    

以下代码片段设置自定义图像填充（由 res\bk.jpg 指定填充图像）：
    
    
    IMAGE img;
    loadimage(&img, _T("res\\bk.jpg"));
    setfillstyle(BS_DIBPATTERN, NULL, &img);
    

以下完整代码设置自定义的填充图案（小矩形填充），并使用该图案填充一个三角形：
    
    
    #include <conio.h>
    #include <graphics.h>
    
    int main()
    {
    	// 创建绘图窗口
    	initgraph(640, 480);
    
    	// 定义填充单元
    	IMAGE img(10, 8);
    
    	// 绘制填充单元
    	SetWorkingImage(&img);	// 设置绘图目标为 img 对象
    	setbkcolor(BLACK);		// 黑色区域为背景色
    	cleardevice();
    	setfillcolor(WHITE);	// 白色区域为自定义图案
    	solidrectangle(1, 1, 8, 5);
    	SetWorkingImage(NULL);	// 恢复绘图目标为默认绘图窗口
    
    	// 设置填充样式为自定义填充图案
    	setfillstyle(BS_PATTERN, NULL, &img);
    
    	// 设置自定义图案的填充颜色
    	settextcolor(GREEN);
    
    	// 绘制无边框填充三角形
    	POINT pts[] = { {50, 50}, {50, 200}, {300, 50} };
    	solidpolygon(pts, 3);
    
    	// 按任意键退出
    	_getch();
    	closegraph();
    }
    

以下代码片段设置自定义的填充图案（圆形图案填充）：
    
    
    setfillstyle((BYTE*)"\x3e\x41\x80\x80\x80\x80\x80\x41");
    

以下代码片段设置自定义的填充图案（细斜线夹粗斜线图案填充）：
    
    
    setfillstyle((BYTE*)"\x5a\x2d\x96\x4b\xa5\xd2\x69\xb4");
    
