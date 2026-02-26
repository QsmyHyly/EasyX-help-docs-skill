# setcapture

这个函数用于设置允许捕获绘图窗口外的鼠标消息。
    
    
    void setcapture();

## 参数

无

## 返回值

无

## 备注

默认情况下，绘图窗口只能捕获窗口范围内的鼠标消息。在必要时刻，可以通过该函数设置允许捕获绘图窗口外的鼠标消息。并且设置允许后，仅当鼠标左键、中键或右键按下时，才可以获取窗口外的鼠标消息。

当绘图窗口不再需要捕获窗口范围外的鼠标消息时，应当及时调用 [releasecapture](releasecapture.htm) 函数来释放鼠标，恢复为只能捕获绘图窗口范围内的鼠标消息。

通常这样使用 setcapture 和 [releasecapture](releasecapture.htm)：

  1. 当按下鼠标左键时，调用 setcapture 开始捕获鼠标。
  2. 响应鼠标移动消息。
  3. 当松开鼠标左键时，调用 [releasecapture](releasecapture.htm) 释放鼠标，恢复原捕获范围。



## 示例

以下完整示例实现了画直线的功能，画直线操作不会因为鼠标超出绘图窗口而中断：
    
    
    #include <graphics.h>
    
    int main()
    {
    	initgraph(640, 480);				// 创建绘图窗口
    
    	POINT from, to;						// 画线的起点和终点
    	bool drawing = false;				// 是否在画线
    	ExMessage msg;						// 消息变量
    
    	setrop2(R2_XORPEN);					// 设置画笔为异或模式
    	setlinecolor(GREEN);				// 设置画线颜色为绿色
    
    	do
    	{
    		msg = getmessage();				// 获取消息
    
    		switch (msg.message)
    		{
    			case WM_LBUTTONDOWN:		// 当左键按下时
    				setcapture();
    				from.x = to.x = msg.x;
    				from.y = to.y = msg.y;
    				drawing = true;
    				line(from.x, from.y, to.x, to.y);
    				break;
    
    			case WM_LBUTTONUP:			// 当左键抬起时
    				releasecapture();
    				drawing = false;
    				break;
    
    			case WM_MOUSEMOVE:			// 当鼠标移动时
    				if (drawing)
    				{
    					line(from.x, from.y, to.x, to.y);		// 擦掉上次的线
    					to.x = msg.x;
    					to.y = msg.y;
    					line(from.x, from.y, to.x, to.y);		// 画新的线
    				}
    				break;
    
    			default:
    				break;
    		}
    	} while (msg.message != WM_RBUTTONDOWN);				// 按右键退出
    	
    	closegraph();
    	return 0;
    }

## 请参阅

[releasecapture](releasecapture.htm)
