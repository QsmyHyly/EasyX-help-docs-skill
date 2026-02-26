# polyline

这个函数用于画连续的多条线段。
    
    
    void polyline(
    	const POINT *points,
    	int num
    );
    

## 参数

### points

每个点的坐标，数组元素个数为 num。

### num

多条线段的顶点个数。

## 返回值

无

## 示例

以下代码片段绘制连续的两条线段(两种方法)：
    
    
    // 方法 1
    POINT pts[] = { {50, 200}, {200, 200}, {200, 50} };
    polyline(pts, 3);
    
    
    
    // 方法 2
    int pts[] = {50, 200,  200, 200,  200, 50};
    polyline((POINT*)pts, 3);
    
