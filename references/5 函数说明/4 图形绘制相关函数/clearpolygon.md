# clearpolygon

这个函数用于清空多边形区域。
    
    
    void clearpolygon(
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

## 备注

该函数使用当前背景色清空多边形区域。

## 示例

以下代码片段清空一个三角形区域(两种方法)：
    
    
    // 方法 1
    POINT pts[] = { {50, 200}, {200, 200}, {200, 50} };
    clearpolygon(pts, 3);
    
    
    
    // 方法 2
    int pts[] = {50, 200,  200, 200,  200, 50};
    clearpolygon((POINT*)pts, 3);
    
