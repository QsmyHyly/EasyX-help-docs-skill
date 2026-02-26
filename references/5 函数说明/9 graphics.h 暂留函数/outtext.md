# outtext

这个函数用于在当前位置输出字符串。
    
    
    void outtext(LPCTSTR str);
    
    
    
    void outtext(TCHAR c);
    

## 参数

### str

待输出的字符串的指针。

### c

待输出的字符。

## 返回值

无

## 备注

该函数已废弃，仅在 graphics.h 中声明，不推荐使用。

该函数会改变当前位置至字符串末尾。可以连续使用该函数使输出的字符串保持连续。

## 示例
    
    
    // 输出字符串（MBCS 字符集）
    char s[] = "Hello World";
    outtext(s);
    
    
    
    // 输出字符串（Unicode 字符集）
    wchar_t s[] = L"Hello World";
    outtext(s);
    
    
    
    
    // 输出字符串 (项目字符集)
    TCHAR s[] = _T("Hello World");
    outtext(s);
    
    
    
    // 输出字符（MBCS 字符集）
    char c = 'A';
    outtext(c);
    
    
    
    // 输出数值，先将数字格式化输出为字符串（MBCS 字符集）
    char s[5];
    sprintf(s, "%d", 1024);
    outtext(s);
    
