# setcolor

这个函数用于设置当前绘图前景色。
    
    
    void setcolor(COLORREF color);
    

## 参数

### color

要设置的前景颜色。

## 返回值

无

## 备注

该函数在 graphics.h 中声明，用于兼容 Turbo C 中的同名函数，等效于连续执行 easyx.h 中的 [setlinecolor](../3%20图形颜色及样式设置相关函数/setlinecolor.htm) 和 [settextcolor](../5%20文字输出相关函数/settextcolor.htm) 函数。

建议根据需求使用 [setlinecolor](../3%20图形颜色及样式设置相关函数/setlinecolor.htm) 或 [settextcolor](../5%20文字输出相关函数/settextcolor.htm) 代替该函数。

## 示例

无
