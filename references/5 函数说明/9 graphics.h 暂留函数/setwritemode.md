# setwritemode

这个函数用于设置前景的二元光栅操作模式。
    
    
    void setwritemode(int mode);
    

## 参数

### mode

二元光栅操作码，详见 [setrop2](../3%20图形颜色及样式设置相关函数/setrop2.htm) 函数。

## 返回值

无

## 备注

该函数在 graphics.h 中声明，用于兼容 Turbo C 中的同名函数，等效于 easyx.h 中的 [setrop2](../3%20图形颜色及样式设置相关函数/setrop2.htm) 函数。

建议使用 [setrop2](../3%20图形颜色及样式设置相关函数/setrop2.htm) 替代该函数。

## 示例

无
