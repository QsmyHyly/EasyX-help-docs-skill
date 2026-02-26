# LOGFONT

这个结构体定义了字体的属性。
    
    
    struct LOGFONT {
    	LONG lfHeight;
    	LONG lfWidth;
    	LONG lfEscapement;
    	LONG lfOrientation;
    	LONG lfWeight;
    	BYTE lfItalic;
    	BYTE lfUnderline;
    	BYTE lfStrikeOut;
    	BYTE lfCharSet;
    	BYTE lfOutPrecision;
    	BYTE lfClipPrecision;
    	BYTE lfQuality;
    	BYTE lfPitchAndFamily;
    	TCHAR lfFaceName[LF_FACESIZE];
    };
    

## 成员

### lfHeight

指定高度。

### lfWidth

指定字符的平均宽度。如果为 0，则比例自适应。

### lfEscapement

字符串的书写角度，单位 0.1 度，默认为 0。

### lfOrientation

每个字符的书写角度，单位 0.1 度，默认为 0。

### lfWeight

字符的笔画粗细，范围 0~1000，0 表示默认粗细，使用数字或下表中定义的宏均可。

宏 | 粗细值  
---|---  
FW_DONTCARE | 0  
FW_THIN | 100  
FW_EXTRALIGHT | 200  
FW_ULTRALIGHT | 200  
FW_LIGHT | 300  
FW_NORMAL | 400  
FW_REGULAR | 400  
FW_MEDIUM | 500  
FW_SEMIBOLD | 600  
FW_DEMIBOLD | 600  
FW_BOLD | 700  
FW_EXTRABOLD | 800  
FW_ULTRABOLD | 800  
FW_HEAVY | 900  
FW_BLACK | 900  
  
### lfItalic

指定字体是否是斜体。

### lfUnderline

指定字体是否有下划线。

### lfStrikeOut

指定字体是否有删除线。

### lfCharSet

指定字符集。可以使用以下预定义的值：

  * ANSI_CHARSET
  * BALTIC_CHARSET
  * CHINESEBIG5_CHARSET
  * DEFAULT_CHARSET （字符集基于本地操作系统。例如，系统位置是 English (United States)，字符集将设置为 ANSI_CHARSET）
  * EASTEUROPE_CHARSET
  * GB2312_CHARSET
  * GREEK_CHARSET
  * HANGUL_CHARSET
  * MAC_CHARSET
  * OEM_CHARSET （字符集依赖本地操作系统）
  * RUSSIAN_CHARSET
  * SHIFTJIS_CHARSET
  * SYMBOL_CHARSET
  * TURKISH_CHARSET



### lfOutPrecision

指定文字的输出精度。输出精度定义输出与所请求的字体高度、宽度、字符方向、行距、间距和字体类型相匹配必须达到的匹配程度。可以是以下值：

值 | 含义  
---|---  
OUT_DEFAULT_PRECIS | 指定默认的映射行为。  
OUT_DEVICE_PRECIS | 当系统包含多个名称相同的字体时，指定设备字体。  
OUT_OUTLINE_PRECIS | 指定字体映射选择 TrueType 和其它的 outline-based 字体。  
OUT_RASTER_PRECIS | 当系统包含多个名称相同的字体时，指定光栅字体（即点阵字体）。  
OUT_STRING_PRECIS | 这个值并不能用于指定字体映射，只是指定点阵字体枚举数据。  
OUT_STROKE_PRECIS | 这个值并不能用于指定字体映射，只是指定 TrueType 和其他的 outline-based 字体，以及矢量字体的枚举数据。  
OUT_TT_ONLY_PRECIS | 指定字体映射只选择 TrueType 字体。如果系统中没有安装 TrueType 字体，将选择默认操作。  
OUT_TT_PRECIS | 当系统包含多个名称相同的字体时，指定 TrueType 字体。  
  
### lfClipPrecision

指定文字的剪辑精度。剪辑精度定义如何剪辑字符的一部分位于剪辑区域之外的字符。可以是以下值：

值 | 含义  
---|---  
CLIP_DEFAULT_PRECIS | 指定默认的剪辑行为。  
CLIP_STROKE_PRECIS | 这个值并不能用于指定字体映射，只是指定光栅（即点阵）、矢量或 TrueType 字体的枚举数据。  
CLIP_EMBEDDED | 当使用内嵌的只读字体时，必须指定这个标志。  
CLIP_LH_ANGLES | 如果指定了该值，所有字体的旋转都依赖于坐标系统的方向是逆时针或顺时针。  
如果没有指定该值，设备字体始终逆时针旋转，但是其它字体的旋转依赖于坐标系统的方向。  
该设置影响 lfOrientation 参数的效果。  
  
### lfQuality

指定文字的输出质量。输出质量定义图形设备界面 (GDI) 必须尝试将逻辑字体属性与实际物理字体的字体属性进行匹配的仔细程度。可以是以下值：

值 | 含义  
---|---  
ANTIALIASED_QUALITY | 指定输出质量是抗锯齿的（如果字体支持）。  
DEFAULT_QUALITY | 指定输出质量不重要。  
DRAFT_QUALITY | 草稿质量。字体的显示质量是不重要的。对于光栅字体（即点阵字体），缩放是有效的，这就意味着可以使用更多的尺寸，但是显示质量并不高。如果需要，粗体、斜体、下划线和删除线字体会被合成。  
NONANTIALIASED_QUALITY | 指定输出质量不是抗锯齿的。  
PROOF_QUALITY | 正稿质量。指定字体质量比匹配字体属性更重要。对于光栅字体（即点阵字体），缩放是无效的，会选用其最接近的字体大小。虽然选中 PROOF_QUALITY 时字体大小不能精确地映射，但是输出质量很高，并且不会有畸变现象。如果需要，粗体、斜体、下划线和删除线字体会被合成。  
  
如果 ANTIALIASED_QUALITY 和 NONANTIALIASED_QUALITY 都未被选择，抗锯齿效果将依赖于控制面板中字体抗锯齿的设置。

### lfPitchAndFamily

指定以常规方式描述字体的字体系列。字体系列描述大致的字体外观。字体系列用于在所需精确字体不可用时指定字体。1~2 位指定字体间距，可以是以下值：

值 | 含义  
---|---  
DEFAULT_PITCH | 指定默认间距。  
FIXED_PITCH | 指定固定间距。  
VARIABLE_PITCH | 指定可变间距。  
  
4~7 位指定字体系列，可以是以下值：

值 | 含义  
---|---  
FF_DECORATIVE | 指定特殊字体。例如 Old English。  
FF_DONTCARE | 指定字体系列不重要。  
FF_MODERN | 指定具有或不具有衬线的等宽字体。例如，Pica、Elite 和 Courier New 都是等宽字体。  
FF_ROMAN | 指定具有衬线的等比字体。例如 MS Serif。  
FF_SCRIPT | 指定设计为类似手写体的字体。例如 Script 和 Cursive。  
FF_SWISS | 指定不具有衬线的等比字体。例如 MS Sans Serif。  
  
字体间距和字体系列可以用布尔运算符 OR 连接 ( 即符号 | )。

### lfFaceName

字体名称，名称不得超过 31 个字符。如果是空字符串，系统将使用第一个满足其它属性的字体。
