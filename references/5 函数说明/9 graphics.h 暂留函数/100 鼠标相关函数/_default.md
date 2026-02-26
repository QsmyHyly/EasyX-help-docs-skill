# 鼠标相关函数

鼠标消息缓冲区可以缓冲 63 个未处理的鼠标消息。每次获取鼠标消息时，将从鼠标消息缓冲区取出一个最早发生的鼠标消息。鼠标消息缓冲区满了之后，不再接收任何鼠标消息。

相关函数如下：

函数或数据类型 | 描述  
---|---  
[FlushMouseMsgBuffer](FlushMouseMsgBuffer.htm) | 清空鼠标消息缓冲区。  
[GetMouseMsg](GetMouseMsg.htm) | 获取一个鼠标消息。如果当前鼠标消息队列中没有，就一直等待。  
[PeekMouseMsg](PeekMouseMsg.htm) | 获取一个鼠标消息，并立即返回。  
[MouseHit](MouseHit.htm) | 检测当前是否有鼠标消息。  
[MOUSEMSG](MOUSEMSG.htm) | 保存鼠标消息的结构体。
