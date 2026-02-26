# outtextxy

这个函数用于在指定位置输出字符串。
    
    
    void outtextxy(
    	int x,
    	int y,
    	LPCTSTR str
    );
    
    
    
    void outtextxy(
    	int x,
    	int y,
    	TCHAR c
    );
    

## 参数

### x

字符串输出时头字母的 x 轴的坐标值。

### y

字符串输出时头字母的 y 轴的坐标值。

### str

待输出的字符串的指针。

### c

待输出的字符。

## 返回值

无

## 备注

该函数不会改变当前位置。

字符串常见的编码有两种：MBCS 和 Unicode。VC6 新建的项目默认为 MBCS 编码，VC2008 及高版本的 VC 默认为 Unicode 编码。LPCTSTR 可以同时适应两种编码。为了适应两种编码，请使用 TCHAR 字符串及相关函数。

默认情况下，输出字符串的背景会用当前背景色填充。使用函数 [setbkmode](../3%20图形颜色及样式设置相关函数/setbkmode.htm) 可以设置文字的背景部分保持透明或使用背景色填充。

## 示例
    
    
    // 输出字符串（MBCS 字符集）
    char s[] = "Hello World";
    outtextxy(10, 20, s);
    
    
    
    // 输出字符串（Unicode 字符集）
    wchar_t s[] = L"Hello World";
    outtextxy(10, 20, s);
    
    
    
    
    // 输出字符串（自适应字符集）
    TCHAR s[] = _T("Hello World");
    outtextxy(10, 20, s);
    
    
    
    // 输出字符（MBCS 字符集）
    char c = 'A';
    outtextxy(10, 40, c);
    
    
    
    // 输出字符（自适应字符集）
    TCHAR c = _T('A');
    outtextxy(10, 40, c);
    
    
    
    // 输出数值，先将数字格式化输出为字符串（MBCS 字符集）
    char s[5];
    sprintf(s, "%d", 1024);
    outtextxy(10, 60, s);
    
    
    
    // 输出数值 1024，先将数字格式化输出为字符串（自适应字符集）
    TCHAR s[5];
    _stprintf(s, _T("%d"), 1024);		// 高版本 VC 推荐使用 _stprintf_s 函数
    outtextxy(10, 60, s);
    
