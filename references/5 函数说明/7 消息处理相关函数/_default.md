# 消息处理相关函数

消息缓冲区可以缓冲 63 个未处理的消息。每次获取消息时，将从消息缓冲区取出一个最早发生的消息。消息缓冲区满了之后，不再接收任何消息。

相关函数如下：

函数或数据类型 | 描述  
---|---  
[ExMessage](ExMessage.htm) | 消息结构体。  
[flushmessage](flushmessage.htm) | 清空消息缓冲区。  
[getmessage](getmessage.htm) | 获取一个消息。如果当前消息缓冲区中没有，就一直等待。  
[peekmessage](peekmessage.htm) | 获取一个消息，并立即返回。  
[setcapture](setcapture.htm) | 设置允许捕获绘图窗口外的鼠标消息。  
[releasecapture](releasecapture.htm) | 设置禁止捕获绘图窗口外的鼠标消息。  
  
 
