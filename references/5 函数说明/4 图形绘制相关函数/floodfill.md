# floodfill

这个函数用于填充区域。
    
    
    void floodfill(
    	int x,
    	int y,
    	COLORREF color,
    	int filltype = FLOODFILLBORDER
    );
    

## 参数

### x

待填充区域内任意点的 x 坐标。

### y

待填充区域内任意点的 y 坐标。

### color

待填充的边界或区域的颜色。具体解释取决于参数 filltype 的值。

### filltype

要执行的填充操作的类型。可以是以下宏或值：

宏 | 值 | 含义  
---|---|---  
FLOODFILLBORDER | 0 | 填充动作在颜色参数 color 围成的封闭区域内填充。  
FLOODFILLSURFACE | 1 | 填充动作在颜色参数 color 指定的连续颜色表面填充。  
  
## 返回值

无

## 备注

对于 FLOODFILLBORDER 填充类型，填充动作以 (x, y) 为起点，向周围扩散，直到遇到 border 指定的颜色才会终止。指定的区域必须是封闭的。适用于填充具有固定颜色边界的区域。

对于 FLOODFILLSURFACE 填充类型，填充动作以 (x, y) 为起点，只要邻接的颜色为 color, 填充就会延伸。适用于填充具有多种颜色边界的区域。

填充的颜色通过函数 [setfillcolor](../3%20图形颜色及样式设置相关函数/setfillcolor.htm) 设置，填充的样式通过函数 [setfillstyle](../3%20图形颜色及样式设置相关函数/setfillstyle.htm) 设置。

## 示例

无
