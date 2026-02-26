# FlushBatchDraw

这个函数用于执行未完成的绘制任务。
    
    
    // 执行未完成的绘制任务
    void FlushBatchDraw();
    
    
    
    // 执行指定区域内未完成的绘制任务
    void FlushBatchDraw(
    	int left,
    	int top,
    	int right,
    	int bottom
    ); 
    

## 参数

### left

指定区域的左部 x 坐标。

### top

指定区域的上部 y 坐标。

### right

指定区域的右部 x 坐标。

### bottom

指定区域的下部 y 坐标。

## 返回值

无

## 示例

请参见 [BeginBatchDraw](BeginBatchDraw.htm) 的示例。
