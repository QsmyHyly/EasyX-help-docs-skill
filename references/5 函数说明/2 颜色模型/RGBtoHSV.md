# RGBtoHSV

该函数用于转换 RGB 颜色为 HSV 颜色。
    
    
    void RGBtoHSV(
    	COLORREF rgb,
    	float *H,
    	float *S,
    	float *V
    );
    

## 参数

### rgb

原 RGB 颜色。

### H

用于返回 HSV 颜色模型的 Hue(色相) 分量，0 <= H < 360。

### S

用于返回 HSV 颜色模型的 Saturation(饱和度) 分量，0 <= S <= 1。

### V

用于返回 HSV 颜色模型的 Value(明度) 分量，0 <= V <= 1。

## 返回值

无

## 备注

HSV 详见 [HSVtoRGB](HSVtoRGB.htm)。

## 示例

无
