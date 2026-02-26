# HSLtoRGB

该函数用于转换 HSL 颜色为 RGB 颜色。
    
    
    COLORREF HSLtoRGB(
    	float H,
    	float S,
    	float L
    );
    

## 参数

### H

原 HSL 颜色模型的 Hue(色相) 分量，0 <= H < 360。

### S

原 HSL 颜色模型的 Saturation(饱和度) 分量，0 <= S <= 1。

### L

原 HSL 颜色模型的 Lightness(亮度) 分量，0 <= L <= 1。

## 返回值

对应的 RGB 颜色。

## 备注

HSL 又称 HLS。

HSL 的颜色模型如图所示：

![](/_temp/74/HSL.jpg)

H 是英文 Hue 的首字母，表示色相，即组成可见光谱的单色。红色在 0 度，绿色在 120 度，蓝色在 240 度，以此方向过渡。

S 是英文 Saturation 的首字母，表示饱和度，等于 0 时为灰色。在最大饱和度 1 时，具有最纯的色光。

L 是英文 Lightness 的首字母，表示亮度，等于 0 时为黑色，等于 0.5 时是色彩最鲜明的状态，等于 1 时为白色。

## 示例

请参见 [示例程序](6 示例程序/_default.htm) 中的“彩虹”。
