# ExMessage  
  
这个结构体用于保存鼠标消息，定义如下：
    
    
    struct ExMessage
    {
    	USHORT message;					// 消息标识
    	union
    	{
    		// 鼠标消息的数据
    		struct
    		{
    			bool ctrl		:1;		// Ctrl 键是否按下
    			bool shift		:1;		// Shift 键是否按下
    			bool lbutton	:1;		// 鼠标左键是否按下
    			bool mbutton	:1;		// 鼠标中键是否按下
    			bool rbutton	:1;		// 鼠标右键
    			short x;				// 鼠标的 x 坐标
    			short y;				// 鼠标的 y 坐标
    			short wheel;			// 鼠标滚轮滚动值，为 120 的倍数
    		};
    
    		// 按键消息的数据
    		struct
    		{
    			BYTE vkcode;			// 按键的虚拟键码
    			BYTE scancode;			// 按键的扫描码（依赖于 OEM）
    			bool extended	:1;		// 按键是否是扩展键
    			bool prevdown	:1;		// 按键的前一个状态是否按下
    		};
    
    		// 字符消息的数据
    		TCHAR ch;
    
    		// 窗口消息的数据
    		struct
    		{
    			WPARAM wParam;
    			LPARAM lParam;
    		};
    	};
    };
    

## 成员

### message

消息标识，可为以下值：

消息标识 | 消息类别 | 描述  
---|---|---  
WM_MOUSEMOVE | EX_MOUSE | 鼠标移动消息。  
WM_MOUSEWHEEL | EX_MOUSE | 鼠标滚轮拨动消息。  
WM_LBUTTONDOWN | EX_MOUSE | 左键按下消息。  
WM_LBUTTONUP | EX_MOUSE | 左键弹起消息。  
WM_LBUTTONDBLCLK | EX_MOUSE | 左键双击消息。  
WM_MBUTTONDOWN | EX_MOUSE | 中键按下消息。  
WM_MBUTTONUP | EX_MOUSE | 中键弹起消息。  
WM_MBUTTONDBLCLK | EX_MOUSE | 中键双击消息。  
WM_RBUTTONDOWN | EX_MOUSE | 右键按下消息。  
WM_RBUTTONUP | EX_MOUSE | 右键弹起消息。  
WM_RBUTTONDBLCLK | EX_MOUSE | 右键双击消息。  
WM_KEYDOWN | EX_KEY | 按键按下消息  
WM_KEYUP | EX_KEY | 按键弹起消息。  
WM_CHAR | EX_CHAR | 字符消息。  
WM_ACTIVATE | EX_WINDOW | 窗口激活状态改变消息。  
WM_MOVE | EX_WINDOW | 窗口移动消息。  
WM_SIZE | EX_WINDOW | 窗口大小改变消息。  
  
### ctrl

Ctrl 键是否按下。仅当消息所属类别为 EX_MOUSE 时有效。

### shift

Shift 键是否按下。仅当消息所属类别为 EX_MOUSE 时有效。

### lbutton

鼠标左键是否按下。仅当消息所属类别为 EX_MOUSE 时有效。

### mbutton

鼠标中键是否按下。仅当消息所属类别为 EX_MOUSE 时有效。

### rbutton

鼠标右键是否按下。仅当消息所属类别为 EX_MOUSE 时有效。

### x

当前鼠标 x 坐标（物理坐标）。仅当消息所属类别为 EX_MOUSE 时有效。

### y

当前鼠标 y 坐标（物理坐标）。仅当消息所属类别为 EX_MOUSE 时有效。

### wheel

鼠标滚轮滚动值，为 120 的倍数。仅当消息所属类别为 EX_MOUSE 时有效。

### vkcode

按键的虚拟键码。仅当消息所属类别为 EX_KEY 时有效。

在微软网站上列出有所有的虚拟键码：<https://docs.microsoft.com/windows/win32/inputdev/virtual-key-codes>

### scancode

按键的扫描码（依赖于 OEM）。仅当消息所属类别为 EX_KEY 时有效。

### extended

按键是否为扩展按键，例如功能键和数字键盘。仅当消息所属类别为 EX_KEY 时有效。

### prevdown

按键的前一个状态是否为按下。仅当消息所属类别为 EX_KEY 时有效。

### ch

收到的字符。仅当消息所属类别为 EX_CHAR 时有效。

### wParam

消息对应的 wParam 参数。仅当消息所属类别为 EX_WINDOW 时有效。

### lParam

消息对应的 lParam 参数。仅当消息所属类别为 EX_WINDOW 时有效。

## 示例

无
