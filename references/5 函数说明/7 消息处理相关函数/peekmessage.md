# peekmessage

这个函数用于获取一个消息，并立即返回。
    
    
    bool peekmessage(ExMessage *msg, BYTE filter = -1, bool removemsg = true);
    

## 参数

### msg

指向消息结构体 [ExMessage](ExMessage.htm) 的指针，用来保存获取到的消息。

### filter

指定要获取的消息范围，默认 -1 获取所有类别的消息。可以用以下值或值的组合获取指定类别的消息：

标志 | 描述  
---|---  
EX_MOUSE | 鼠标消息。  
EX_KEY | 按键消息。  
EX_CHAR | 字符消息。  
EX_WINDOW | 窗口消息。  
  
### removemsg

在 peekmessage 处理完消息后，是否将其从消息队列中移除。

## 返回值

如果获取到了消息，返回 true。

如果当前没有消息，返回 false。

## 备注

默认情况下，连续的鼠标单击会被识别为一系列的单击事件。如果希望两个连续的鼠标单击识别为双击事件，请在创建绘图窗口的时候指定标志位 EX_DBLCLKS。

## 示例

无
