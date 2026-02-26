# RGB

RGB 宏用于将红、绿、蓝颜色分量合成颜色。
    
    
    COLORREF RGB(
    	BYTE byRed,		// 颜色的红色部分
    	BYTE byGreen,	// 颜色的绿色部分
    	BYTE byBlue		// 颜色的蓝色部分
    );
    

## 参数

### byRed

颜色的红色部分，取值范围：0~255。

### byGreen

颜色的绿色部分，取值范围：0~255。

### byBlue

颜色的蓝色部分，取值范围：0~255。

## 返回值

返回合成的颜色。

## 备注

可以通过 [GetRValue](GetRValue.htm)、[GetGValue](GetGValue.htm)、[GetBValue](GetBValue.htm) 宏从颜色中分离出红、绿、蓝颜色分量。

RGB 宏在 Windows SDK 中定义。

## 示例

无
