# solidpolygon

这个函数用于画无边框的填充多边形。
    
    
    void solidpolygon(
    	const POINT *points,
    	int num
    );
    

## 参数

### points

每个点的坐标，数组元素个数为 num。  
该函数会自动连接多边形首尾。

### num

多边形顶点的个数。

## 返回值

无

## 备注

该函数使用当前线形和当前填充样式绘制无边框的填充多边形。

## 示例

以下代码片段绘制一个封闭的填充三角形(两种方法)：
    
    
    // 方法 1
    POINT pts[] = { {50, 200}, {200, 200}, {200, 50} };
    solidpolygon(pts, 3);
    
    
    
    // 方法 2
    int pts[] = {50, 200,  200, 200,  200, 50};
    solidpolygon((POINT*)pts, 3);
    

以下完整代码绘制一个五角星：
    
    
    #include <graphics.h>
    #include <conio.h>
    #include <math.h>
    
    #define PI 3.14159265359
    
    int main()
    {
    	// 创建绘图窗口
    	initgraph(640, 480);
    
    	// 定义数组，保存五角星的五个顶点坐标
    	POINT pts[5];
    
    	// 计算五角星的五个顶点坐标
    	double a = PI / 2;
    	for (int i = 0; i < 5; i++)
    	{
    		pts[i].x = int(320 + cos(a) * 100);
    		pts[i].y = int(240 - sin(a) * 100);
    		a += PI * 4 / 5;
    	}
    
    	// 设置填充模式为 WINDING
    	setpolyfillmode(WINDING);
    	// 设置填充颜色为红色
    	setfillcolor(RED);
    	// 绘制五角星(无边框)
    	solidpolygon(pts, 5);
    
    	// 按任意键退出
    	_getch();
    	closegraph();
    }
    
