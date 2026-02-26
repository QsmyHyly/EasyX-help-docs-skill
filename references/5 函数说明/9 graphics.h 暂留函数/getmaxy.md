# getmaxy

这个函数用于获取绘图窗口的物理坐标中的最大 y 坐标。
    
    
    int getmaxy();
    

## 参数

无

## 返回值

返回绘图窗口的物理坐标中的最大 y 坐标。

## 备注

该函数已废弃，仅在 graphics.h 中声明，推荐使用 [getheight()](../4%20图形绘制相关函数/getheight.htm)?- 1?替代该函数。

getmaxy() 总是返回绘图窗口的物理坐标中的最大 y 坐标，与缩放设置无关。等价于?[getheight()](../4%20图形绘制相关函数/getheight.htm) \- 1。例如，初始化为 640 x 480 的绘图窗口，getheight() 返回 480，getmaxy() 返回 479。

## 示例

无
