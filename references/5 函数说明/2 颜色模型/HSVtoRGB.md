# HSVtoRGB

该函数用于转换 HSV 颜色为 RGB 颜色。
    
    
    COLORREF HSVtoRGB(
    	float H,
    	float S,
    	float V
    );
    

## 参数

### H

原 HSV 颜色模型的 Hue(色相) 分量，0 <= H < 360。

### S

原 HSV 颜色模型的 Saturation(饱和度) 分量，0 <= S <= 1。

### V

原 HSV 颜色模型的 Value(明度) 分量，0 <= V <= 1。

## 返回值

对应的 RGB 颜色。

## 备注

HSV 又称 HSB。

HSV 的颜色模型如图所示：

![](/_temp/75/HSV.jpg)

H 是英文 Hue 的首字母，表示色相，即组成可见光谱的单色。红色在 0 度，绿色在 120 度，蓝色在 240 度，以此方向过渡。

S 是英文 Saturation 的首字母，表示饱和度，等于 0 时为灰色。在最大饱和度 1 时，每一色相具有最纯的色光。

V 是英文 Value 的首字母，表示明度，等于 0 时为黑色，在最大明度 1 时，是色彩最鲜明的状态。

## 示例

HSV 颜色模型类似于 HSL，[示例程序](6 示例程序/_default.htm) 中的“彩虹”是 HSL 模型的操作范例，可以参考。
