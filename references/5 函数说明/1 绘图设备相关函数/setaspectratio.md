# setaspectratio

这个函数用于设置当前缩放因子。
    
    
    void setaspectratio(
    	float xasp,
    	float yasp
    );
    

## 参数

### xasp

x 方向上的缩放因子。例如绘制宽度为 100 的矩形，实际的绘制宽度为 100 * xasp。

### yasp

y 方向上的缩放因子。例如绘制高度为 100 的矩形，实际的绘制高度为 100 * yasp。

## 返回值

无

## 备注

如果缩放因子为负，可以实现坐标轴的翻转。例如，执行 setaspectratio(1, -1) 后，可使 y 轴向上为正。

## 示例

无
