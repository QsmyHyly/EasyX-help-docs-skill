# BeginBatchDraw

这个函数用于开始批量绘图。执行后，任何绘图操作都将暂时不输出到绘图窗口上，直到执行 FlushBatchDraw 或 EndBatchDraw 才将之前的绘图输出。
    
    
    void BeginBatchDraw();
    

## 参数

无

## 返回值

无

## 示例

以下代码实现一个圆从左向右移动，会有比较明显的闪烁。  
请取消 main 函数中的三个注释，以实现批绘图功能，可以消除闪烁。
    
    
    #include <graphics.h>
    
    int main()
    {
    	initgraph(640,480);
    	// BeginBatchDraw();
    
    	setlinecolor(WHITE);
    	setfillcolor(RED);
    
    	for(int i=50; i<600; i++)
    	{
    		cleardevice();
    		circle(i, 100, 40);
    		floodfill(i, 100, WHITE);
    		// FlushBatchDraw();
    		Sleep(10);
    	}
    
    	// EndBatchDraw();
    	closegraph();
    }
    
