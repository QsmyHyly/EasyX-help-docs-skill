# RGBtoHSL

该函数用于转换 RGB 颜色为 HSL 颜色。
    
    
    void RGBtoHSL(
    	COLORREF rgb,
    	float *H,
    	float *S,
    	float *L
    );
    

## 参数

### rgb

原 RGB 颜色。

### H

用于返回 HSL 颜色模型的 Hue(色相) 分量，0 <= H < 360。

### S

用于返回 HSL 颜色模型的 Saturation(饱和度) 分量，0 <= S <= 1。

### L

用于返回 HSL 颜色模型的 Lightness(亮度) 分量，0 <= L <= 1。

## 返回值

无

## 备注

HSL 详见 [HSLtoRGB](HSLtoRGB.htm)。

## 示例

无
