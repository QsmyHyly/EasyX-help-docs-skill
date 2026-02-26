# fillpoly

这个函数用于画有边框的填充多边形。
    
    
    void fillpoly(
    	int numpoints,
    	const int *polypoints
    );
    

## 参数

### numpoints

多边形顶点的个数。

### polypoints

每个点的坐标，数组元素个数为 numpoints * 2。  
该函数会自动连接多边形首尾。

## 返回值

无

## 备注

该函数已废弃，仅在 graphics.h 中声明，推荐使用 [fillpolygon](../4%20图形绘制相关函数/fillpolygon.htm)?替代该函数。

## 示例

以下代码片段绘制一个封闭的填充三角形：
    
    
    int points[] = {50, 200,  200, 200,  200, 50};
    fillpoly(3, points);
    
