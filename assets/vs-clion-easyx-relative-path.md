[![](/Content/images/codebus-white.svg)](/)

# [BestAns](/bestans/)

路漫漫其修远兮，吾将上下而求索

  * [管理](/console/posts)



##  Visual Studio / CLion 使用 EasyX 通过相对路径加载图片的详细解释 [![金牌收录](/content/images/gold.png)](?medal=3 "金牌收录")

__2025-8-15 ~ 2025-8-16 __[BestAns](/bestans/) [ __(0)](https://codebus.cn/bestans/vs-clion-easyx-relative-path#comment)

# 什么是“相对路径”？

加载图片时，一定要有图片的“绝对路径”才能找到图片。为了方便使用，一个图片的“绝对路径” = “当前路径” \+ “相对路径”。

例如，当前路径为 D:\Work\Job01，相对路径为 res\cat.png，那么绝对路径就是：D:\Work\Job01\res\cat.png 。

通常情况下，“当前路径”就是 exe 所在路径。当然，也可以独立设置程序执行时的“当前路径”。

# Visual Studio 项目的路径设定

## 基础环境示例

例如，Visual Studio 2012 项目的常见文件结构如下（仅列出主要文件）：
    
    
    D:\Work\Job01\
    	├ Debug\  <folder>
    	│	└ Job01.exe				// 编译后的可执行文件
    	├ res\  <folder>
    	│	└ hero.png				// 待加载的图片
    	├ main.cpp					// 代码文件
    	├ Job01.sln					// 解决方案文件
    	└ Job01.vcxproj				// 项目文件
    

其中，编译后的可执行文件路径是：D:\Work\Job01\Debug\Job01.exe

默认的当前路径是项目文件 Job01.vcxproj 的所在路径，即：D:\Work\Job01

## 使用相对路径的方案

▶ 在 VS 开发环境下，执行程序时的默认当前路径是：D:\Work\Job01

因此，通过相对路径加载图片的代码这样写：
    
    
    	loadimage(&img, L"res\\hero.png");		// 如果项目是 MBCS 编码，就去掉字符串前面的 L。

那么图片的绝对路径为：“D:\Work\Job01” \+ "res\hero.png" = D:\Work\Job01\res\hero.png，可以找到图片。

▶ 在操作系统环境下，双击可执行文件 D:\Work\Job01\Debug\Job01.exe ，那么此时的“当前路径”为 Job01.exe 所在路径，即 D:\Work\Job01\Debug\ ，此时图片的绝对路径为 D:\Work\Job01\Debug\res\hero.png ，这就导致找不到图片。

为了解决这个问题，需要将 res 文件夹拷贝到 Job01.exe 所在路径下，这样在双击 Job01.exe 的时候，就能通过相对路径找到图片 res\hero.png 。

▶ 发布应用时，可以这样组织应用的文件：
    
    
    D:\App\Job01\
    	├ Job01.exe				// 可执行文件
    	└ res\  <folder>			// 资源文件夹
    		└ hero.png			// 待加载的图片
    

# CLion 项目的路径设定

## 基础环境示例

例如，CLion 2025.1.3 项目的常见文件结构如下（仅列出主要文件）：
    
    
    D:\Work\Job02\
    	├ .idea\  <folder>
    	├ cmake-build-debug\  <folder>
    	│	└ Job02.exe				// 编译后的可执行文件
    	├ res\  <folder>
    	│	└ hero.png				// 待加载的图片
    	├ CMakeLists.txt			// 编译配置文件
    	└ main.cpp					// 代码文件
    

其中，编译后的可执行文件路径是：D:\Work\Job02\cmake-build-debug\Job02.exe

默认的当前路径是编译后的可执行文件 Job02.exe 的所在路径，即：D:\Work\Job02\cmake-build-debug

# 使用相对路径的方案一（推荐）

▶ 在 CLion 开发环境下，通过修改 CMakeLists.txt 使相关图片资源自动拷贝到编译输出文件夹下，修改方法（以下内容增加到 CMakeLists.txt 的末尾）：
    
    
    # 拷贝资源文件夹 res 到编译输出文件夹
    add_custom_command(
    		TARGET Job02 POST_BUILD
    		COMMAND ${CMAKE_COMMAND} -E copy_directory_if_different
    			"${CMAKE_CURRENT_SOURCE_DIR}/res"
    			"${CMAKE_CURRENT_BINARY_DIR}/res"
    )

然后点 CLion 的菜单项：文件 / 重新加载 CMake 项目，使前述修改生效。

这样调整后，项目的文件结构会变为：
    
    
    D:\Work\Job02\
    	├ .idea\  <folder>
    	├ cmake-build-debug\  <folder>
    	│	├ Job02.exe					// 编译后的可执行文件
    	│	└ res\  <folder>			// 资源文件夹（自动复制的）
    	│		└ hero.png				// 待加载的图片（自动复制的）
    	├ res\  <folder>				// 资源文件夹
    	│	└ hero.png					// 待加载的图片
    	├ CMakeLists.txt				// 编译配置文件
    	└ main.cpp						// 代码文件

默认的当前路径是编译后的可执行文件 Job02.exe 的所在路径，即：D:\Work\Job02\cmake-build-debug

由于资源文件夹 res 已经自动拷贝到了 D:\Work\Job02\cmake-build-debug\res ，因此通过相对路径加载图片的代码这样写：
    
    
    	loadimage(&img, "res\\hero.png");

那么图片的绝对路径为：“D:\Work\Job02\cmake-build-debug” \+ "res\hero.png" = D:\Work\Job02\cmake-build-debug\res\hero.png，可以找到图片。

▶ 在操作系统环境下，双击可执行文件 D:\Work\Job02\cmake-build-debug\Job02.exe ，可以通过相对路径找到图片。

▶ 发布应用时，可以这样组织应用的文件：
    
    
    D:\App\Job02\
    	├ Job02.exe				// 可执行文件
    	└ res\  <folder>		// 资源文件夹
    		└ hero.png			// 待加载的图片

## 使用相对路径的方案二

▶ 在 CLion 开发环境下，通过修改 CMakeLists.txt 调整生成的 Job02.exe 的路径，修改方法：
    
    
    set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})		# 增加的内容，必须写在下面一行之前
    add_executable(Job02 main.cpp)										# 原有的内容

然后点 CLion 的菜单项：文件 / 重新加载 CMake 项目，使前述修改生效。

这样调整后，项目的文件结构会变为：
    
    
    D:\Work\Job02\
    	├ .idea\  <folder>
    	├ cmake-build-debug\  <folder>
    	├ res\  <folder>
    	│	└ hero.png				// 待加载的图片
    	├ CMakeLists.txt			// 编译配置文件
    	├ Job02.exe					// 编译后的可执行文件
    	└ main.cpp					// 代码文件
    

默认的当前路径是编译后的可执行文件 Job02.exe 的所在路径，即：D:\Work\Job02

因此，通过相对路径加载图片的代码这样写：
    
    
    	loadimage(&img, "res\\hero.png");

那么图片的绝对路径为：“D:\Work\Job02” \+ "res\hero.png" = D:\Work\Job02\res\hero.png，可以找到图片。

▶ 在操作系统环境下，双击可执行文件 D:\Work\Job02\Job02.exe ，也可以通过相对路径找到图片。

▶ 发布应用时，可以这样组织应用的文件：
    
    
    D:\App\Job02\
    	├ Job02.exe				// 可执行文件
    	└ res\  <folder>		// 资源文件夹
    		└ hero.png			// 待加载的图片

## 使用相对路径的方案三

▶ 在 CLion 开发环境下，执行程序时的当前路径，就是编译后的可执行文件 Job02.exe 的所在路径，即：D:\Work\Job02\cmake-build-debug

通过相对路径加载图片的代码这样写（ ..\ 表示上一级文件夹）：
    
    
    	loadimage(&img, "..\\res\\hero.png");

▶ 在操作系统环境下，双击可执行文件 D:\Work\Job02\cmake-build-debug\Job02exe ，那么此时的“当前路径”为 Job02.exe 所在路径，即 D:\Work\Job02\cmake-build-debug\ ，此时图片的绝对路径为 D:\Work\Job02\cmake-build-debug\\..\res\hero.png ，即 D:\Work\Job02\res\hero.png ，可以找到图片。

▶ 发布应用时，可以这样组织应用的文件：
    
    
    D:\App\Job02\
    	├ bin\  <folder>				// 存放二进制文件的文件夹
    	│	└ Job02.exe				// 编译后的可执行文件
    	└ res\  <folder>
    		└ hero.png				// 待加载的图片
    

## 使用相对路径的方案四（不推荐）

▶ 在 CLion 开发环境下，点击右上角的下拉菜单（通常显示为当前配置名称，如 Job02），选择“编辑配置...”。在“工作目录”一栏中，输入：$ProjectFileDir$

这个设置可以将“当前路径”设置为 $ProjectFileDir$（即项目的根文件夹 D:\Work\Job02），因此通过相对路径加载图片的代码这样写：
    
    
    	loadimage(&img, "res\\hero.png");

那么图片的绝对路径为：“D:\Work\Job02” \+ "res\hero.png" = D:\Work\Job02\res\hero.png，可以找到图片。

▶ 在操作系统环境下，双击可执行文件 D:\Work\Job02\cmake-build-debug\Job02.exe 无法通过相对路径找到图片，需要手动将 D:\Work\Job02\res\ 复制到 D:\Work\Job02\cmake-build-debug\res\ 。

▶ 发布应用时，可以这样组织应用的文件：
    
    
    D:\App\Job02\
    	├ Job02.exe					// 可执行文件
    	└ res\  <folder>			// 资源文件夹
    		└ hero.png				// 待加载的图片

使用该方案请注意：设置“工作目录”为 $ProjectFileDir$ ，这个设置无法保存在项目路径下，导致你的项目文件夹分享给别人后，别人无法直接运行成功，必须同样进行一次手动设置才可以。

# 注

  1. 以上只是举例，实际使用时记得替换掉例子中对应的文件名。
  2. 以上只是列举了部分常用方案，不是只有这几种方案，实际中可以根据自己的需求进行调整。



[CLion](/t/CLion)[EasyX](/t/EasyX)[MinGW](/t/MinGW)[VisualStdio](/t/VisualStdio)[相对路径](/t/%E7%9B%B8%E5%AF%B9%E8%B7%AF%E5%BE%84)

![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/bestans/a/vs-clion-easyx-relative-path\))

[取消回复](javascript:void\(0\);)

![BestAns](//thirdqq.qlogo.cn/ek_qqapp/AQD82rib8N8zE7ic5Va89dnSV03hc816rN9g3PRUy5JQxHgGZ9W9Yib6oKBA4B0ulCZ6fue4OIg/0)

[BestAns](/bestans/)

  * [__](/bestans/ "主页")
  * [__](https://i.codebus.cn/user/6b0dc13a-e624-651a-5a14-41d39d24a38b "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=1438018116 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=1438018116&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=6b0dc13a-e624-651a-5a14-41d39d24a38b "添加好友")



搜索

#### 标签云

  * [绘图](/bestans/t/%E7%BB%98%E5%9B%BE)
  * [3D 绘图](/bestans/t/3D%20%E7%BB%98%E5%9B%BE)
  * [教程](/bestans/t/%E6%95%99%E7%A8%8B)
  * [EasyX_20210730](/bestans/t/EasyX_20210730)
  * [工具](/bestans/t/%E5%B7%A5%E5%85%B7)
  * [相对路径](/bestans/t/%E7%9B%B8%E5%AF%B9%E8%B7%AF%E5%BE%84)
  * [视觉错觉艺术](/bestans/t/%E8%A7%86%E8%A7%89%E9%94%99%E8%A7%89%E8%89%BA%E6%9C%AF)
  * [EasyX](/bestans/t/EasyX)
  * [EasyX_2023大暑版](/bestans/t/EasyX_2023%E5%A4%A7%E6%9A%91%E7%89%88)
  * [VisualStdio](/bestans/t/VisualStdio)
  * [MinGW](/bestans/t/MinGW)
  * [ARM64](/bestans/t/ARM64)
  * [游戏开发](/bestans/t/%E6%B8%B8%E6%88%8F%E5%BC%80%E5%8F%91)
  * [VC6](/bestans/t/VC6)
  * [VC2010](/bestans/t/VC2010)
  * [文本框](/bestans/t/%E6%96%87%E6%9C%AC%E6%A1%86)
  * [实战](/bestans/t/%E5%AE%9E%E6%88%98)
  * [CLion](/bestans/t/CLion)



广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
