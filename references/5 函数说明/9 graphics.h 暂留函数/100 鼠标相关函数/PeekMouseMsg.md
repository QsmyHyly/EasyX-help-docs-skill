# PeekMouseMsg

这个函数用于获取一个鼠标消息，并立即返回。
    
    
    bool PeekMouseMsg(MOUSEMSG *pMsg, bool bRemoveMsg);
    

## 参数

### pMsg

指向 [MOUSEMSG](MOUSEMSG.htm) 结构体的指针，用来保存接收到的鼠标消息。

### bRemoveMsg

在 PeekMouseMsg 处理完消息后，是否将其从消息队列中移除。

## 返回值

如果有鼠标消息，返回 true。

如果当前没有鼠标消息，返回 false。

## 备注

默认情况下，连续的鼠标单击会被识别为一系列的单击事件。如果希望两个连续的鼠标单击识别为双击事件，请在创建绘图窗口的时候指定标志位 EW_DBLCLKS。

该函数已废弃，仅在 graphics.h 中声明。建议使用 [peekmessage](../../7%20消息处理相关函数/peekmessage.htm) 替代。

## 示例

无
