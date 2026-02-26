# getcolor

这个函数用于获取当前绘图前景色。
    
    
    COLORREF getcolor();
    

## 参数

无

## 返回值

返回当前的前景颜色。

## 备注

该函数已废弃，仅在 graphics.h 中声明，推荐使用 [getlinecolor](../3%20图形颜色及样式设置相关函数/getlinecolor.htm)?或?[gettextcolor](../5%20文字输出相关函数/gettextcolor.htm)? 替代该函数。

由于该函数设置的前景色同时包括画线和文字颜色，因此不完全等效于 easyx.h 中的 [getlinecolor](../3%20图形颜色及样式设置相关函数/getlinecolor.htm) 或 [gettextcolor](../5%20文字输出相关函数/gettextcolor.htm) 函数，请根据实际情况选择替代函数。

## 示例

无
