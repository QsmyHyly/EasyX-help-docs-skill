# drawtext  
  
这个函数用于在指定区域内以指定格式输出字符串。
    
    
    int drawtext(
    	LPCTSTR str,
    	RECT* pRect,
    	UINT uFormat
    );
    
    
    
    int drawtext(
    	TCHAR c,
    	RECT* pRect,
    	UINT uFormat
    );
    

## 参数

### str

待输出的字符串。

### pRect

指定的矩形区域的指针。某些 uFormat 标志会使用这个矩形区域做返回值。详见后文说明。

### uFormat

指定格式化输出文字的方法。详见后文说明。

### c

待输出的字符。

## 返回值

函数执行成功时，返回文字的高度。

如果指定了 DT_VCENTER 或 DT_BOTTOM 标志，返回值表示从 pRect->top 到输出文字的底部的偏移量。

如果函数执行失败，返回 0。

## 备注

默认情况下，输出字符串的背景会用当前背景色填充。使用函数 [setbkmode](../3%20图形颜色及样式设置相关函数/setbkmode.htm) 可以设置文字的背景部分保持透明或使用背景色填充。

以下是 uFormat 参数可以使用的设置项，用来设置文字输出时的格式：

标志 | 描述  
---|---  
DT_BOTTOM | 调整文字位置到矩形底部，仅当和 DT_SINGLELINE 一起使用时有效。  
DT_CALCRECT | 检测矩形的宽高。如果有多行文字，drawtext 使用 pRect 指定的宽度，并且扩展矩形的底部以容纳每一行文字。如果只有一行文字，drawtext 修改 pRect 的右边以容纳最后一个文字。无论哪种情况，drawtext 都返回格式化后的文字高度，并且不输出文字。  
DT_CENTER | 文字水平居中。  
DT_EDITCONTROL | 以单行编辑的方式复制可见文本。具体的说，就是以字符的平均宽度为计算依据，同时用这个方式应用于编辑控制，并且这种方式不显示可见部分的最后一行。  
DT_END_ELLIPSIS | 对于文本显示，如果字符串的末字符不在矩形内，它会被截断并以省略号标识。 如果是一个单词而不是一个字符，其末尾超出了矩形范围，它不会被截断。  
字符串不会被修改，除非指定了 DT_MODIFYSTRING 标志。  
DT_EXPANDTABS | 展开 TAB 符号。 默认每个 TAB 占 8 个字符位置。注意，DT_WORD_ELLIPSIS、DT_PATH_ELLIPSIS 和 DT_END_ELLIPSIS 不能和 DT_EXPANDTABS 一起用。  
DT_EXTERNALLEADING | 在行高里包含字体的行间距。通常情况下，行间距不被包含在正文的行高里。  
DT_HIDEPREFIX | 忽略文字中的前缀字符(&)，并且前缀字符后面的字符不会出现下划线。其他前缀字符仍会被处理。例如：  
输入字符串: "A&bc&&d"  
通常输出: "Abc&d"  
设置标志 DTDT_HIDEPREFIX 后输出: "Abc&d"  
DT_INTERNAL | 使用系统字体计算文字的宽高等属性。  
DT_LEFT | 文字左对齐。  
DT_MODIFYSTRING | 修改指定字符串为显示出的正文。仅当和 DT_END_ELLIPSIS 或 DT_PATH_ELLIPSIS 标志同时使用时有效。  
DT_NOCLIP | 使输出文字不受 pRect 裁剪限制。使用 DT_NOCLIP 会使 drawtext 执行稍快一些。  
DT_NOFULLWIDTHCHARBREAK | 防止换行符插入到 DBCS (double-wide character string，即宽字符串)，换行规则相当于 SBCS 字符串。仅当和 DT_WORDBREAK 一起使用时有效。例如，汉字就是宽字符，设置该标志后，连续的汉字会像英文单词一样不被换行符中断。  
DT_NOPREFIX | 关闭前缀字符的处理。通常，DrawText 解释前缀转义符 & 为其后的字符加下划线，解释 && 为显示单个 &。指定 DT_NOPREFIX，这种处理被关闭。例如：  
输入字符串: "A&bc&&d"  
通常输出: "Abc&d"  
设置标志 DT_NOPREFIX 后输出: "A&bc&&d"  
DT_PATH_ELLIPSIS | 对于显示的文字，用省略号替换字符串中间的字符以便容纳于矩形内。如果字符串包含反斜杠()，DT_PATH_ELLIPSIS 尽可能的保留最后一个反斜杠后面的文字。  
字符串不会被修改，除非指定了 DT_MODIFYSTRING 标志。  
DT_PREFIXONLY | 仅仅在(&)前缀字符的位置下绘制一个下划线。不绘制字符串中的任何其他字符。例如：  
输入字符串: "A&bc&&d"  
通常输出: "Abc&d"  
设置标志 DT_PREFIXONLY 后输出: " _   "  
DT_RIGHT | 文字右对齐。  
DT_RTLREADING | 设置从右向左的阅读顺序（当文字是希伯来文或阿拉伯文时）。默认的阅读顺序是从左向右。  
DT_SINGLELINE | 使文字显示在一行。回车和换行符都无效。  
DT_TABSTOP | 设置 TAB 制表位。uFormat 的 15–8 位指定 TAB 的字符宽度。默认 TAB 表示 8 个字符宽度。注意，DT_CALCRECT、DT_EXTERNALLEADING、DT_INTERNAL、DT_NOCLIP 和 DT_NOPREFIX 不能和 DT_TABSTOP 一起用。  
DT_TOP | 文字顶部对齐。  
DT_VCENTER | 文字垂直居中。仅当和 DT_SINGLELINE 一起使用时有效。  
DT_WORDBREAK | 自动换行。当文字超过右边界时会自动换行(不拆开单词)。回车符同样可以换行。  
DT_WORD_ELLIPSIS | 截去无法容纳的文字，并在末尾增加省略号。  
  
使用标志 DT_NOFULLWIDTHCHARBREAK、DT_HIDEPREFIX、DT_PREFIXONLY 需要在引用 graphics.h 或 easyx.h 前定义 WINVER >= 0x0500（VC6.0 不支持）。例如：
    
    
    #define WINVER 0x0500

## 示例

以下范例在屏幕中央输出字符串“Hello World”：
    
    
    #include <graphics.h>
    #include <conio.h>
    
    int main()
    {
    	// 绘图窗口初始化
    	initgraph(640, 480);
    
    	// 在屏幕中央输出字符串
    	RECT r = {0, 0, 639, 479};
    	drawtext(_T("Hello World"), &r, DT_CENTER | DT_VCENTER | DT_SINGLELINE);
    
    	// 按任意键退出
    	_getch();
    	closegraph();
    }
    
