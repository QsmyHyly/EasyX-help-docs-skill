# InputBox

这个函数用于以对话框形式获取用户输入。
    
    
    bool InputBox(
    	LPTSTR	pString,
    	int		nMaxCount,
    	LPCTSTR	pPrompt = NULL,
    	LPCTSTR	pTitle = NULL,
    	LPCTSTR	pDefault = NULL,
    	int		width = 0,
    	int		height = 0,
    	bool	bHideCancelBtn = true
    );
    

## 参数

### pString

指定接收用户输入字符串的指针。

### nMaxCount

指定 pString 指向的缓冲区的大小，该值会限制用户输入内容的长度。缓冲区的大小包括表示字符串结尾的 '\0' 字符。当允许多行输入时，用户键入的回车占两个字符位置。

### pPrompt

指定显示在对话框中的提示信息。提示信息中可以用“\n”分行。InputBox 的高度会随着提示信息内容的多少自动扩充。如果该值为 NULL，则不显示提示信息。

### pTitle

指定 InputBox 的标题栏。如果为 NULL，将显示应用程序的名称。

### pDefault

指定显示在用户输入区的默认值。

### width

指定 InputBox 的宽度（不包括边框），最小为 200 像素。如果为 0，则使用默认宽度。

### height

指定 InputBox 的高度（不包括边框）。如果为 0，表示自动计算高度，用户输入框只允许输入一行内容，按“回车”确认输入信息；如果大于 0，用户输入框的高度会自动拓展，同时允许输入多行内容，按“Ctrl+回车”确认输入信息。

### bHideCancelBtn

指定是否隐藏取消按钮禁止用户取消输入。如果为 true(默认)，InputBox 只有一个“确定”按钮，没有“X”关闭按钮，按 ESC 无效；如果为 false，InputBox 有“确定”和“取消”按钮，允许点“X”和按 ESC 关闭窗口。

## 返回值

返回用户是否输入信息。如果用户按“确定”，返回 true；如果用户按“取消”，返回 false。

## 示例

以下示例提示用户输入圆的半径，并画圆：
    
    
    // 编译环境：VC2008 及以上版本，Unicode 字符集
    //
    #include <graphics.h>
    #include <conio.h>
    
    int main()
    {
    	// 初始化绘图窗口
    	initgraph(640, 480);
    
    	// 定义字符串缓冲区，并接收用户输入
    	wchar_t s[10];
    	InputBox(s, 10, L"请输入半径");
    
    	// 将用户输入转换为数字
    	int r = _wtoi(s);
    
    	// 画圆
    	circle(320, 240, r);
    
    	// 按任意键退出
    	_getch();
    	closegraph();
    
    	return 0;
    }
    
