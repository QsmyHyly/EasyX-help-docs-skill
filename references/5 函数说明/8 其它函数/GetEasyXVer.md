# GetEasyXVer

这个函数用于获取当前 EasyX 库的版本信息。
    
    
    const TCHAR* GetEasyXVer();
    

## 参数

无

## 返回值

返回当前 EasyX 库的版本信息。

## 示例

以下代码实现输出当前 EasyX 版本号：
    
    
    // 以下代码的字符集设置: MBCS
    //
    #include <stdio.h>
    #include <graphics.h>
    
    int main()
    {
    	const char* s = GetEasyXVer();
    	printf("EasyX 当前版本：%s\n", s);
    }
    
