# fillpolygon

这个函数用于画有边框的填充多边形。
    
    
    void fillpolygon(
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

该函数使用当前线形和当前填充样式绘制有外框的填充多边形。

## 示例

以下代码片段绘制一个封闭的填充三角形(两种方法)：
    
    
    // 方法 1
    POINT pts[] = { {50, 200}, {200, 200}, {200, 50} };
    fillpolygon(pts, 3);
    
    
    
    // 方法 2
    int pts[] = {50, 200,  200, 200,  200, 50};
    fillpolygon((POINT*)pts, 3);
    
