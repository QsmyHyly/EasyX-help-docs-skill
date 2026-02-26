# setlinestyle

这个函数用于设置当前设备画线样式。
    
    
    void setlinestyle(
    	const LINESTYLE* pstyle
    );
    
    
    
    void setlinestyle(
    	int style,
    	int thickness = 1,
    	const DWORD *puserstyle = NULL,
    	DWORD userstylecount = 0
    );
    

## 参数

### pstyle

指向画线样式 [LINESTYLE](LINESTYLE.htm) 的指针。

### style

画线样式（详见备注）。

### thickness

线的宽度，以像素为单位。

### puserstyle

用户自定义样式数组，仅当线型为 PS_USERSTYLE 时该参数有效。  
数组第一个元素指定画线的长度，第二个元素指定空白的长度，第三个元素指定画线的长度，第四个元素指定空白的长度，以此类推。

### userstylecount

用户自定义样式数组的元素数量。

## 返回值

无

## 备注

参数 style 指定了画线样式，该样式由直线样式、端点样式、连接样式三类组成。可以是其中一类或多类的组合。同一类型中只能指定一个样式。

直线样式可以是以下值：

值 | 含义  
---|---  
PS_SOLID | 线形为实线。  
PS_DASH | 线形为：------------  
PS_DOT | 线形为：・・・・・・・・・・・・  
PS_DASHDOT | 线形为：-・-・-・-・-・-・  
PS_DASHDOTDOT | 线形为：-・・-・・-・・-・・  
PS_NULL | 线形为不可见。  
PS_USERSTYLE | 线形样式为用户自定义，由参数 puserstyle 和 userstylecount 指定。  
  
宏 PS_STYLE_MASK 是直线样式的掩码，可以通过该宏从画线样式中分离出直线样式。

端点样式可以是以下值：

值 | 含义  
---|---  
PS_ENDCAP_ROUND | 端点为圆形。  
PS_ENDCAP_SQUARE | 端点为方形。  
PS_ENDCAP_FLAT | 端点为平坦。  
  
宏 PS_ENDCAP_MASK 是端点样式的掩码，可以通过该宏从画线样式中分离出端点样式。

连接样式可以是以下值：

值 | 含义  
---|---  
PS_JOIN_BEVEL | 连接处为斜面。  
PS_JOIN_MITER | 连接处为斜接。  
PS_JOIN_ROUND | 连接处为圆弧。  
  
宏 PS_JOIN_MASK 是连接样式的掩码，可以通过该宏从画线样式中分离出连接样式。

掩码宏表示对应样式组所占用的所有位。例如，对于一个已经混合了多种样式的 style 变量，如果希望仅将直线样式修改为点划线，可以这么做：
    
    
    style = (style & ~PS_STYLE_MASK) | PS_DASHDOT;
    

## 示例

以下代码片段设置画线样式为点划线：
    
    
    setlinestyle(PS_DASHDOT);
    

以下代码片段设置画线样式为宽度 3 像素的虚线，端点为平坦的：
    
    
    setlinestyle(PS_DASH | PS_ENDCAP_FLAT, 3);
    

以下代码片段设置画线样式为宽度 10 像素的实线，连接处为斜面：
    
    
    setlinestyle(PS_SOLID | PS_JOIN_BEVEL, 10);
    

以下代码片段设置画线样式为自定义样式（画 5 个像素，跳过 2 个像素，画 3 个像素，跳过 1 个像素……），端点为平坦的：
    
    
    DWORD a[4] = {5, 2, 3, 1};
    setlinestyle(PS_USERSTYLE | PS_ENDCAP_FLAT, 1, a, 4);
    
