# GetHWnd

这个函数用于获取绘图窗口句柄。
    
    
    HWND GetHWnd();
    

## 参数

无

## 返回值

返回绘图窗口句柄。

## 备注

在 Windows 下，句柄是一个窗口的标识，得到句柄后，可以使用 Windows API 中的函数实现对窗口的控制。

注意，请不要通过该窗口句柄获取窗口的 DC 然后利用 GDI 函数实现对窗口的绘图操作。由于实现机制的问题，获取窗口的 DC 请使用 [GetImageHDC](../6%20图像处理相关函数/GetImageHDC.htm) 函数。

## 示例
    
    
    // 获得窗口句柄
    HWND hWnd = GetHWnd();
    // 使用 Windows API 修改窗口名称
    SetWindowText(hWnd, "Hello!");
    
