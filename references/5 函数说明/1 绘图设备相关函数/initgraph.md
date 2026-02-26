# initgraph

这个函数用于初始化绘图窗口。
    
    
    HWND initgraph(
    	int width,
    	int height,
    	int flag = NULL
    );
    

## 参数

### width

绘图窗口的宽度。

### height

绘图窗口的高度。

### flag

绘图窗口的样式，默认为 NULL。可为以下值：

值 | 含义  
---|---  
EX_DBLCLKS | 在绘图窗口中支持鼠标双击事件。  
EX_NOCLOSE | 禁用绘图窗口的关闭按钮。  
EX_NOMINIMIZE | 禁用绘图窗口的最小化按钮。  
EX_SHOWCONSOLE | 显示控制台窗口。  
  
## 返回值

返回新建绘图窗口的句柄。

## 示例

以下代码片段创建一个尺寸为 640x480 的绘图窗口：
    
    
    initgraph(640, 480);
    

以下代码片段创建一个尺寸为 640x480 的绘图窗口，同时显示控制台窗口：
    
    
    initgraph(640, 480, EX_SHOWCONSOLE);
    

以下代码片段创建一个尺寸为 640x480 的绘图窗口，同时显示控制台窗口，并禁用关闭按钮：
    
    
    initgraph(640, 480, EX_SHOWCONSOLE | EX_NOCLOSE);
    
