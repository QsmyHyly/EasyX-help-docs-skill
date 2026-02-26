# getmessage

这个函数用于获取一个消息。如果当前消息队列中没有，就一直等待。
    
    
    ExMessage getmessage(BYTE filter = -1);
    
    
    
    void getmessage(ExMessage *msg, BYTE filter = -1);
    

## 参数

### msg

指向消息结构体 [ExMessage](ExMessage.htm) 的指针，用来保存获取到的消息。

### filter

指定要获取的消息范围，默认 -1 获取所有类别的消息。可以用以下值或值的组合获取指定类别的消息：

标志 | 描述  
---|---  
EX_MOUSE | 鼠标消息。  
EX_KEY | 按键消息。  
EX_CHAR | 字符消息。  
EX_WINDOW | 窗口消息。  
  
## 返回值

重载 1 返回保存有消息 [ExMessage](ExMessage.htm) 的结构体。

重载 2 没有返回值。

## 备注

默认情况下，连续的鼠标单击会被识别为一系列的单击事件。如果希望两个连续的鼠标单击识别为双击事件，请在创建绘图窗口的时候指定标志位 EX_DBLCLKS。

## 示例

请参见 [示例程序](6 示例程序/_default.htm) 中的“鼠标操作范例”。
