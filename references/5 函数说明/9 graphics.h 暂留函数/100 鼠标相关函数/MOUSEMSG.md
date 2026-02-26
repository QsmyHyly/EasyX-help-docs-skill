# MOUSEMSG

这个结构体用于保存鼠标消息，定义如下：
    
    
    struct MOUSEMSG
    {
    	UINT uMsg;			// 当前鼠标消息
    	bool mkCtrl;		// Ctrl 键是否按下
    	bool mkShift;		// Shift 键是否按下
    	bool mkLButton;		// 鼠标左键是否按下
    	bool mkMButton;		// 鼠标中键是否按下
    	bool mkRButton;		// 鼠标右键是否按下
    	int x;				// 当前鼠标 x 坐标（物理坐标）
    	int y;				// 当前鼠标 y 坐标（物理坐标）
    	int wheel;			// 鼠标滚轮滚动值
    };
    

## 成员

### uMsg

指定鼠标消息类型，可为以下值：

值 | 含义  
---|---  
WM_MOUSEMOVE | 鼠标移动消息。  
WM_MOUSEWHEEL | 鼠标滚轮拨动消息。  
WM_LBUTTONDOWN | 左键按下消息。  
WM_LBUTTONUP | 左键弹起消息。  
WM_LBUTTONDBLCLK | 左键双击消息。  
WM_MBUTTONDOWN | 中键按下消息。  
WM_MBUTTONUP | 中键弹起消息。  
WM_MBUTTONDBLCLK | 中键双击消息。  
WM_RBUTTONDOWN | 右键按下消息。  
WM_RBUTTONUP | 右键弹起消息。  
WM_RBUTTONDBLCLK | 右键双击消息。  
  
### mkCtrl

Ctrl 键是否按下

### mkShift

Shift 键是否按下

### mkLButton

鼠标左键是否按下

### mkMButton

鼠标中键是否按下

### mkRButton

鼠标右键是否按下

### x

当前鼠标 x 坐标（物理坐标）

### y

当前鼠标 y 坐标（物理坐标）

### wheel

鼠标滚轮滚动值，为 120 的倍数。

## 备注

该结构体已废弃，仅在 graphics.h 中声明。建议使用 [ExMessage](../../7%20消息处理相关函数/ExMessage.htm)?替代。

## 示例

无
