[![](/Content/images/codebus-white.svg)](/)

# [BestAns](/bestans/)

路漫漫其修远兮，吾将上下而求索

  * [管理](/console/posts)



##  CLion + MinGW 使用 EasyX 输出中文乱码的解决方案 [![铜牌收录](/content/images/bronze.png)](?medal=1 "铜牌收录")

__2025-8-12 __[BestAns](/bestans/) [ __(0)](https://codebus.cn/bestans/mingw-easyx-chinese-encoding#comment)

在 CLion 里面使用 EasyX 需要安装 easyx for mingw，目前支持 MBCS 和 Unicode 两种编码，分别来说。

# 一、MBCS 编码

一句话概述：用 GB18030 文件格式，代码用 char 定义字符串，链接 libeasyx.a 库文件，可以确保中文不乱码。

具体可以参考如下步骤：

1\. 默认情况下，CLion 使用 MBCS 编码（就是使用 char 定义字符串）。

2\. 敲以下代码：
    
    
    #include<easyx.h>
    #include<conio.h>
    
    int main()
    {
        initgraph(640, 480);
        char s[100] = "我命由我不由天";
        outtextxy(50, 100, s);
        _getch();
        closegraph();
        return 0;
    }

3\. 默认代码文件格式为 UTF-8，务必修改为 GB18030（或者 GBK）（注：GB18030 是 GBK 的超集）。

具体操作方法：打开代码，在 CLion 底部状态栏的右边，有一个“UTF-8”字样，表示当前代码文件的编码。单击“UTF-8”，在列表中选择“GB18030”，然后会有弹窗询问“重新加载或转换为 GB18030”，点“转换”，可以看到 CLion 底部状态栏的“UTF-8”已经改成了“GB18030”。

4\. 修改 CMakeLists.txt，链接 EasyX 库文件（注：将 job01 替换为实际项目名）：
    
    
    target_link_libraries(job01 libeasyx.a)

5\. 执行，会看到中文完美输出。

# 二、Unicode 编码

一句话概述：用 UTF-8 文件格式，代码用 wchar_t 定义字符串，链接 libeasyxw.a 库文件，可以确保中文不乱码。

具体可以参考如下步骤：

1\. 默认情况下，CLion 使用 MBCS 编码。要改为 Unicode 编码（就是使用 wchar_t 定义字符串），需要修改 CMakeLists.txt，增加一行：
    
    
    add_definitions(-DUNICODE -D_UNICODE)

2\. 敲以下代码：
    
    
    #include<easyx.h>
    #include<conio.h>
    
    int main()
    {
        initgraph(640, 480);
        wchar_t s[100] = L"我命由我不由天";
        outtextxy(50, 100, s);
        _getch();
        closegraph();
        return 0;
    }

3\. 默认代码文件格式为 UTF-8，无需改动。如果 CLion 底部状态栏的右边没有显示“UTF-8”，务必修改为“UTF-8”（参考前面步骤）。

4\. 修改 CMakeLists.txt，链接 EasyX 库文件（注：将 job01 替换为实际项目名）：
    
    
    target_link_libraries(job01 libeasyxw.a)

5\. 执行，会看到中文完美输出。

![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/bestans/a/mingw-easyx-chinese-encoding\))

[取消回复](javascript:void\(0\);)

![BestAns](//thirdqq.qlogo.cn/ek_qqapp/AQD82rib8N8zE7ic5Va89dnSV03hc816rN9g3PRUy5JQxHgGZ9W9Yib6oKBA4B0ulCZ6fue4OIg/0)

[BestAns](/bestans/)

  * [__](/bestans/ "主页")
  * [__](https://i.codebus.cn/user/6b0dc13a-e624-651a-5a14-41d39d24a38b "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=1438018116 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=1438018116&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=6b0dc13a-e624-651a-5a14-41d39d24a38b "添加好友")



搜索

#### 标签云

  * [MinGW](/bestans/t/MinGW)
  * [VC2010](/bestans/t/VC2010)
  * [工具](/bestans/t/%E5%B7%A5%E5%85%B7)
  * [绘图](/bestans/t/%E7%BB%98%E5%9B%BE)
  * [3D 绘图](/bestans/t/3D%20%E7%BB%98%E5%9B%BE)
  * [EasyX_2023大暑版](/bestans/t/EasyX_2023%E5%A4%A7%E6%9A%91%E7%89%88)
  * [游戏开发](/bestans/t/%E6%B8%B8%E6%88%8F%E5%BC%80%E5%8F%91)
  * [相对路径](/bestans/t/%E7%9B%B8%E5%AF%B9%E8%B7%AF%E5%BE%84)
  * [文本框](/bestans/t/%E6%96%87%E6%9C%AC%E6%A1%86)
  * [ARM64](/bestans/t/ARM64)
  * [EasyX](/bestans/t/EasyX)
  * [VisualStdio](/bestans/t/VisualStdio)
  * [VC6](/bestans/t/VC6)
  * [CLion](/bestans/t/CLion)
  * [教程](/bestans/t/%E6%95%99%E7%A8%8B)
  * [实战](/bestans/t/%E5%AE%9E%E6%88%98)
  * [EasyX_20210730](/bestans/t/EasyX_20210730)
  * [视觉错觉艺术](/bestans/t/%E8%A7%86%E8%A7%89%E9%94%99%E8%A7%89%E8%89%BA%E6%9C%AF)



广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
