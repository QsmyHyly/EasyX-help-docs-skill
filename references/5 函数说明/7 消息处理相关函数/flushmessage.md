# flushmessage

这个函数用于清空消息缓冲区。
    
    
    void flushmessage(BYTE filter = -1);
    

## 参数

### filter

指定要清空的消息范围，默认 -1?清空所有类别的消息。可以用以下值或值的组合清空指定类别的消息：

标志 | 描述  
---|---  
EX_MOUSE | 鼠标消息。  
EX_KEY | 按键消息。  
EX_CHAR | 字符消息。  
EX_WINDOW | 窗口消息。  
  
## 返回值

无

## 示例

无
