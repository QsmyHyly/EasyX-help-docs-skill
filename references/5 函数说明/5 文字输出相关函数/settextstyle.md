# settextstyle

这个函数用于设置当前文字样式。
    
    
    void settextstyle(
    	int nHeight,
    	int nWidth,
    	LPCTSTR lpszFace
    );
    
    
    
    void settextstyle(
    	int nHeight,
    	int nWidth,
    	LPCTSTR lpszFace,
    	int nEscapement,
    	int nOrientation,
    	int nWeight,
    	bool bItalic,
    	bool bUnderline,
    	bool bStrikeOut
    );
    
    
    
    void settextstyle(
    	int nHeight,
    	int nWidth,
    	LPCTSTR lpszFace,
    	int nEscapement,
    	int nOrientation,
    	int nWeight,
    	bool bItalic,
    	bool bUnderline,
    	bool bStrikeOut,
    	BYTE fbCharSet,
    	BYTE fbOutPrecision,
    	BYTE fbClipPrecision,
    	BYTE fbQuality,
    	BYTE fbPitchAndFamily
    );
    
    
    
    void settextstyle(const LOGFONT *font);
    

## 参数

### nHeight

指定高度（逻辑单位）。

### nWidth

字符的平均宽度（逻辑单位）。如果为 0，则比例自适应。

### lpszFace

字体名称。

### nEscapement

字符串的书写角度，单位 0.1 度。

### nOrientation

每个字符的书写角度，单位 0.1 度。

### nWeight

字符的笔画粗细，范围 0~1000。0 表示默认粗细。详见?[LOGFONT](LOGFONT.htm)?结构体。

### bItalic

是否斜体。

### bUnderline

是否有下划线。

### bStrikeOut

是否有删除线。

### fbCharSet

指定字符集。详见 [LOGFONT](LOGFONT.htm)?结构体。

### fbOutPrecision

指定文字的输出精度。详见 [LOGFONT](LOGFONT.htm) 结构体。

### fbClipPrecision

指定文字的剪辑精度。详见 [LOGFONT](LOGFONT.htm) 结构体。

### fbQuality

指定文字的输出质量。详见 [LOGFONT](LOGFONT.htm) 结构体。

### fbPitchAndFamily

指定以常规方式描述字体的字体系列。详见 [LOGFONT](LOGFONT.htm) 结构体。

### font

指向 [LOGFONT](LOGFONT.htm) 结构体的指针。

## 返回值

无

## 示例
    
    
    // 设置当前字体为高 16 像素的“Consolas”。(VC6 / VC2008 / VC2010 / VC2012)
    settextstyle(16, 0, _T("Consolas"));
    outtextxy(0, 0, _T("测试"));
    
    
    
    // 设置输出效果为抗锯齿 (VC6 / VC2008 / VC2010 / VC2012)
    LOGFONT f;
    gettextstyle(&f);						// 获取当前字体设置
    f.lfHeight = 48;						// 设置字体高度为 48
    _tcscpy(f.lfFaceName, _T("黑体"));		// 设置字体为“黑体”(高版本 VC 推荐使用 _tcscpy_s 函数)
    f.lfQuality = ANTIALIASED_QUALITY;		// 设置输出效果为抗锯齿  
    settextstyle(&f);						// 设置字体样式
    outtextxy(0, 50, _T("抗锯齿效果"));
    
