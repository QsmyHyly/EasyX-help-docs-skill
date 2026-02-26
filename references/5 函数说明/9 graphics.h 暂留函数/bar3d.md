# bar3d

这个函数用于画有边框三维填充矩形。
    
    
    void bar3d(
    	int left,
    	int top,
    	int right,
    	int bottom,
    	int depth,
    	bool topflag
    );
    

## 参数

### left

矩形左部 x 坐标。

### top

矩形顶部 y 坐标。

### right

矩形右部 x 坐标。

### bottom

矩形底部 y 坐标。

### depth

矩形深度。

### topflag

为 false 时，将不画矩形的三维顶部。该选项可用来画堆叠的三维矩形。

## 返回值

无

## 备注

该函数已废弃，仅在 graphics.h 中声明，不推荐使用。

## 示例

无
