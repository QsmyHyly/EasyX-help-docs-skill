# polygon

这个函数用于画无填充的多边形。
    
    
    void polygon(
    	const POINT *points,
    	int num
    );
    

## 参数

### points

每个点的坐标，数组元素个数为 num。  
该函数会自动连接多边形首尾。

### num

多边形顶点的个数。

## 返回值

无

## 示例

以下代码片段绘制一个三角形(两种方法)：
    
    
    // 方法 1
    POINT pts[] = { {50, 200}, {200, 200}, {200, 50} };
    polygon(pts, 3);
    
    
    
    // 方法 2
    int pts[] = {50, 200,  200, 200,  200, 50};
    polygon((POINT*)pts, 3);
    
