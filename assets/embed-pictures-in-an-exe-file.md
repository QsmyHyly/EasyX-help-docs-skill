[![](/Content/images/codebus-white.svg)](/)

# [慢羊羊的空间](/yangw/)

工作做不完了，300出，无瑕。

  * [管理](/console/posts)



##  读取图片的技巧：将图片内嵌到 exe 文件中 [![铜牌收录](/content/images/bronze.png)](?medal=1 "铜牌收录")

__2010-8-25 ~ 2025-10-17 __[慢羊羊](/yangw/) [ __(7)](https://codebus.cn/yangw/embed-pictures-in-an-exe-file#comment)

**注：Visual C++ Express（学习版）不支持资源编辑，无法创建资源文件，因此也就无法实现内嵌图片资源。**

# 场景描述

如果程序中需要使用一张图片，通常会用相对路径方式指定一个外部文件。例如：
    
    
    loadimage(NULL, _T("test.jpg"));
    

这样，将编译后的 .exe 和 test.jpg 放在一起，就可以正确加载图片。许多游戏有几十个甚至上千个文件，就是有类似这样的许多外部数据。

但还有一些情况，希望图片能嵌入编译后的 .exe 里面，这样只需要拷贝一个 .exe 文件就能附带上所需图片。本文就介绍这种情况的实现方法。

# 什么是资源文件

windows 应用程序是可以包含各种“资源”的，例如：图标、对话框、菜单、快捷键等等，这些资源按照一定的格式，可以和 .exe 链接在一起。

我们所要做的，就是把图片放到资源中，然后从资源中加载图片。

# 操作步骤

以英文版的 VC6 和 VC2010 为例，嵌入资源的操作步骤如下：

  1. 创建项目 
     1. 打开 VC6（或 VC2010 等其他版本），建立控制台应用程序，并建立相应 cpp 程序，确保可以正确编译执行。
  2. 创建资源文件 
     1. 对于 VC6：点菜单 File -> New...，选择 Files 中的 Resource Script，并在右侧 File 中写入名称 test，点 OK 添加到项目中。VC 会默认打开 test.rc 文件，先关闭它，我们可以 FileView 找到新添加的 test.rc 文件。双击 test.rc，会在 Workspace 区中打开 Resource View 视图。  
对于 VC2010：在 Solution Explorer 中找到 Resource Files，右击，选择 Add -> New Item...，在弹出的新窗口中选择 Resource File (.rc)，在 Name 中写入名称 test，点 Add 添加。此时会自动切换到 Resource View 视图的选项卡。
     2. 在新打开的 Resource View 视图中，会显示本项目中使用的资源，例如图标、位图、字符串等等。当然，现在还是空的。
  3. 添加图片到资源文件中 
     1. 为了整齐，我们在项目路径下建立 res 文件夹，并将图片放入该文件夹内。举例，我们放入一个 bk.jpg 文件。  
添加 JPG 资源。
     2. 对于 VC6：切换到 ResourceView 视图，右击 test resources，选择 Import...，导入 res\bk.jpg 文件。之后在 Custom Resource Type 中为资源取一个类型名称，例如"IMAGE"，点 OK。此时 VC 会在"IMAGE"下默认创建一个 IDR_IMAGE1 的资源，并以二进制形式打开。我们暂时关掉它。  
对于 VC2010：切换到 Resource View 视图，右击 test.rc，选择 Add Resource...，在弹出的窗口中点 Import... 按钮，选择 res\bk.jpg 文件。之后在 Custom Resource Type 中为资源取一个类型名称，例如"IMAGE"，点 OK。此时 VC 会在"IMAGE"下默认创建一个 IDR_IMAGE1 的资源，并以二进制形式打开。我们暂时关掉它。
     3. 重命名资源。  
对于 VC6：右击 IDR_IMAGE1，选 Properties，将 ID 一栏的 IDR_IMAGE1 修改为符合其意义的名称，例如"Background"，切记，一定要加上英文的双引号。这时，资源中可以看到 "IMAGE" / "Background" (注意都有双引号)。  
对于 VC2010：选中 IDR_IMAGE1，按 Alt + Enter 显示 Properties，将 ID 一栏的 IDR_IMAGE1 修改为符合其意义的名称，例如"Background"，切记，一定要加上英文的双引号。这时，资源中可以看到 "IMAGE" / "Background" (注意都有双引号)。
  4. 加载资源中的图片  
加载图片很简单，只需要指定“资源类型”和“资源名称”。例如我们前面的例子，资源类型是 "IMAGE"，资源名称是 "Background"，将这个图片资源显示在绘图窗体上可以这样做：


    
    
    loadimage(NULL, _T("IMAGE"), _T("Background"));
    

最后，编译程序，资源文件会自动和 exe 打包在一起。

# 特殊情况

## 1\. 以资源 ID 的形式加载资源图片

使用图片的资源 ID 也是一种常用的加载资源的方法。默认情况下，将图片导入资源后，会自动生成一个 ID，并且会在 resource.h 里面定义这个 ID。这里说的，就是直接引用这个 ID 而不命名为字符串。

为了实现这个目的，需要首先引用资源头文件，然后用宏 MAKEINTRESOURCE 将 ID 转换为字符串。例如加载一个名称为 IDR_BACKGROUND 的资源：
    
    
    #include "resource.h"
    ……
    loadimage(NULL, _T("IMAGE"), MAKEINTRESOURCE(IDR_BACKGROUND));
    

## 2\. 将 BMP 格式的图片嵌入资源

由于 BMP 格式的图片在资源中的情况特殊，导入资源后，会导致文件头丢失，从而引起加载失败。

所以，需要明确指定 BMP 图片的资源类型为其它类型，方法如下：

  1. 在资源中导入 test.bmp 图片。默认会导入在 Bitmap 类别下，并命名为 IDB_BITMAP1。作为范例，我们修改这个资源 ID 为字符串“test_bmp”。编译项目，确保没有错误。
  2. 以文本方式打开资源文件。  
对于 VC6，点菜单 File -> Open...，选中项目的资源文件 test.rc，底部的 Open as 选择 Text，点 Open 打开（如果此时资源视图已打开，会提示“This file is open for resource editing. Continuing will close the resource editor.”，点 OK）。  
对于 VC2010，在 Solution Explorer 里面找到资源文件 test.rc，右击，选择 View Code（如果此时资源视图已打开，会提示“The document 'xxx' is already open. Do you want to close it?”，点 Yes）。  
这样就可以以文本方式打开资源文件。
  3. 在资源文件的文本中，找到这样的内容：


    
    
    /////////////////////////////////////////////////////////////////////////////
    //
    // Bitmap
    //
    
    test_bmp             BITMAP  DISCARDABLE     "test.bmp"
    

然后将这一行里面的 BITMAP 修改为自己定义的一个类型，例如 IMAGE：
    
    
    test_bmp             IMAGE  DISCARDABLE     "test.bmp"
    

然后就可以按照前述方式加载这个图片资源，例如：
    
    
    loadimage(NULL, _T("IMAGE"), _T("test_bmp"));
    

[技术分享](/t/%E6%8A%80%E6%9C%AF%E5%88%86%E4%BA%AB)

###  评论 (7) [-](javascript:toggle_visibility\('commentlist', 'commenttoggle'\);)

  * ![](//thirdqq.qlogo.cn/ek_qqapp/AQF371tBDZ44kCpCgPmct0ADrpRlTZwu7ibbHoq4E0Zicl9hquQIibEniadLicMxvP6jFqKj5g0suPUIRLd2SI06RkAYtZoBdB5QxzlXTqhGW73iasQLyrPUo/0)

[贼鸥团伙](https://i.codebus.cn/user/261eaa26-08b0-f59e-1910-1db21a2e4192)

如果id命名为 引号内纯数字 （例如："1"），则loadimage函数无效 无法从资源中加载图片。  
个人经验，仅供参考

2025-10-4 \- [回复](javascript:void\(0\);)



  * ![](//thirdqq.qlogo.cn/ek_qqapp/AQJGAhHwyZicE0GJIwDpAPic8mZFicWZWWXeTjEaUztsM2IkCZqMBzHblS4W1ekQ4BOFiaFlhic6I28XiceOsQpEkdTOBu27Bw7ZjDew0KWJdv9BZTx1hfqoo/0)

[情怀里市侩º](https://i.codebus.cn/user/a3f3bec4-702e-3d54-ea61-db4d67d0210e)

时不时黑屏怎么回事

2024-3-24 \- [回复](javascript:void\(0\);)



  * ![](//thirdqq.qlogo.cn/ek_qqapp/AQPffQWibEN8YIOVVmAswmAsIWsBgJE4FMiav21rJsHVpaQ4wOTsyh6libcGm5qoxicicVKOPKaiah/0)

[huidong](https://i.codebus.cn/user/204215aa-df22-6a69-9c9e-288d67b9f5f8)

【操作步骤】3.3. 重命名资源  这一栏下面的两个”切忌“都误用了。

2022-7-12 \- [回复](javascript:void\(0\);)



  * ![](//thirdqq.qlogo.cn/g?b=oidb&k=vRAYpk2UULzzDvCc0Wf8nw&s=640&t=1621267366)

[无往回首](https://i.codebus.cn/user/5cb2c9d2-1b72-ca71-bc2e-2e02020a20f7)

好多人都搬运你的文章，不打包的游戏看起来太普通了ヾ(≧O≦)〃嗷~

2021-6-22 \- [回复](javascript:void\(0\);)



  * ![](//thirdqq.qlogo.cn/g?b=oidb&k=C5SVQiaqxQZPueBbdXVZ43g&s=640&t=1614794577)

[李狗蛋](https://i.codebus.cn/user/914339cb-bf4c-a148-c05e-c38f77311229)

加不加双引号，本质上是什么区别呢？

2021-3-13 \- [回复](javascript:void\(0\);)



  * ![](//thirdqq.qlogo.cn/g?b=oidb&k=kYIW8iaTlfQoBQhbm4wjHMw&s=640&t=1628782448)

[我爱橘子](https://i.codebus.cn/user/ad571e49-f985-cc8d-0581-b1785781267b)

加载的jpg资源会被自动转换为bmp文件，导致编译出的exe文件特别大，有什么解决方法吗？

2020-8-16 \- [回复](javascript:void\(0\);)

    * ![](//thirdqq.qlogo.cn/ek_qqapp/AQC0szBkKoJVMfC0yGFQezib6icmdJvFBKBCvgH6oXbc5aSicoZe02iba6yGibKzMDfUXQQoiayibbQ/0)

[慢羊羊](https://i.codebus.cn/user/a93da1fd-72a2-3d2b-a049-1d3d3619b8bc)

很可能是你的 jpg 文件仍是 bmp 格式。扩展名只是文件格式的一个标识，直接修改扩展名并不能改变文件本身的格式。

2020-8-17 \- [回复](javascript:void\(0\);)




![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/yangw/a/embed-pictures-in-an-exe-file\))

[取消回复](javascript:void\(0\);)

![慢羊羊](//thirdqq.qlogo.cn/ek_qqapp/AQC0szBkKoJVMfC0yGFQezib6icmdJvFBKBCvgH6oXbc5aSicoZe02iba6yGibKzMDfUXQQoiayibbQ/0)

[慢羊羊](/yangw/)

  * [__](/yangw/ "主页")
  * [__](https://i.codebus.cn/user/a93da1fd-72a2-3d2b-a049-1d3d3619b8bc "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=16491848 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=16491848&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=a93da1fd-72a2-3d2b-a049-1d3d3619b8bc "添加好友")



搜索

#### 标签云

  * [GB2312](/yangw/t/GB2312)
  * [技术分享](/yangw/t/%E6%8A%80%E6%9C%AF%E5%88%86%E4%BA%AB)
  * [EasyX_20210730](/yangw/t/EasyX_20210730)
  * [VC2010](/yangw/t/VC2010)
  * [EasyX_20200727](/yangw/t/EasyX_20200727)
  * [MBCS](/yangw/t/MBCS)
  * [36题](/yangw/t/36%E9%A2%98)
  * [EasyX_2023大暑版](/yangw/t/EasyX_2023%E5%A4%A7%E6%9A%91%E7%89%88)
  * [图形学](/yangw/t/%E5%9B%BE%E5%BD%A2%E5%AD%A6)
  * [教程](/yangw/t/%E6%95%99%E7%A8%8B)
  * [物理模拟](/yangw/t/%E7%89%A9%E7%90%86%E6%A8%A1%E6%8B%9F)
  * [视觉错觉艺术](/yangw/t/%E8%A7%86%E8%A7%89%E9%94%99%E8%A7%89%E8%89%BA%E6%9C%AF)
  * [EasyX_20200902](/yangw/t/EasyX_20200902)
  * [VC6](/yangw/t/VC6)
  * [图形学与图像学](/yangw/t/%E5%9B%BE%E5%BD%A2%E5%AD%A6%E4%B8%8E%E5%9B%BE%E5%83%8F%E5%AD%A6)
  * [游戏](/yangw/t/%E6%B8%B8%E6%88%8F)
  * [分形学](/yangw/t/%E5%88%86%E5%BD%A2%E5%AD%A6)
  * [EasyX_20211109](/yangw/t/EasyX_20211109)
  * [绘图](/yangw/t/%E7%BB%98%E5%9B%BE)
  * [天罡36题](/yangw/t/%E5%A4%A9%E7%BD%A136%E9%A2%98)



广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
