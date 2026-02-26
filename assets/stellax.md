[![](/Content/images/codebus-white.svg)](/)

# [StellarX](/stellarx/)

A lightweight, modular C++ GUI framework for Windows

  * [管理](/console/posts)



##  【StellarX】基于 EasyX 实现轻量级 GUI 框架 [![金牌收录](/content/images/gold.png)](?medal=3 "金牌收录")

__2025-10-7 ~ 2025-12-21 __[霜霜不会压枪](/stellarx/) [ __(0)](https://codebus.cn/stellarx/stellax#comment)

**用 EasyX 封装出 “可用、可教、可拓展”的 C++ GUI —— StellarX 框架实践**

轻级 的 C++ 原生 GUI 框架，面向教学与桌面小工具开发。  
基于 EasyX 实现渲染，保留原生 Windows 开发体验，却把“控件体系、事件回调、布局组织”都帮你封装好了。  
项目主页：<https://github.com/Ysm-04/StellarX>  
       官网：<https://stellarx-gui.top>  
适用人群：C++ 初学者、在校大学生、EasyX 用户、Windows 小型桌面工具开发者  
关键词：EasyX、C++17、GUI 框架、教学、轻量、OOP

**为什么要做 StellarX？**

很多同学做 Windows GUI 时会遇到：

  *  直接用 Win32/MFC：流程重、样板代码多，教学演示成本高。
  * 上手 Qt 等大框架：功能强，但安装体量大、依赖多、学习曲线陡。
  * 仅用 EasyX：绘图简单、易安装，但没有“按钮/文本框/表格”这些完整控件体系，想做应用 UI 还得自己造轮子。



_StellarX 想填补这块空白：_

        在 EasyX 的基础上，提供一套 原生 C++ 的控件体系 + 事件模型 + 统一风格 API，把“做应用 UI”这件事变得轻量、可教、可拓展。它不与 Qt 竞争“全家桶”，而是主打教学与小工具：足够好用、足够轻、足够清晰。

**框架特点一览**

  1. 轻量：核心库约 ~14MB，零第三方庞大依赖（仅需 EasyX）。
  2. 现代 C++：C++17，OOP 组织清晰，易读易教。
  3. 控件齐全：Window、Canvas 容器、Button、Label、TextBox、Dialog/MessageBox 等常用控件。
  4. 事件即回调：setOnClickListener、setOnToggleOnListener… 写法直观。
  5. 样式可调：枚举化控件形状与色彩接口，快速统一 UI 风格。
  6. 教学友好：源码体量小、结构清楚，适合课堂讲解与自学。
  7. 定位明确：面向Windows 与小型应用，不追求“大而全”。



**快速开始（5 分钟）**

环境：Windows 10/11、Visual Studio 2019+、已安装与 VS 匹配的 EasyX。

**方式 A：Release 包集成（推荐上手）**

在 GitHub Releases 下载最新二进制包（<https://github.com/Ysm-04/StellarX/releases/tag/v2.0.0>）。

VS 工程中：

C/C++ → 常规 → 附加包含目录：加入 ./include  
链接器 → 常规/输入：加入库目录与 StellarX.lib  
代码中 #include "StellarX.h"，编译运行即可。

**方式 B：CMake 源码构建**

git clone https://github.com/Ysm-04/StellarX.git  
cd StellarX  
cmake -B build -S .  
cmake --build build --config Release

生成的库与示例可直接使用；也可将 include/、lib/ 整合到你的项目。

Hello, GUI（最小示例）
    
    
    // StellarX唯一对外头文件
    #include "StellarX.h"
    // StellarX命名空间：包含StellarX所有数据类型
    using namespace StellarX;
    
    int main() 
    {
    	// 创建窗口类对象
    	Window win(400, 300, nullptr, RGB(255,255,255), "Hello StellarX");
    	// 创建按钮
    	auto btn = std::make_unique<Button>(140, 120, 120, 36, "点我试试");
    	// 绑定按钮事件回调
    	btn->setOnClickListener([](){
    		// 消息框工厂模式调用
    		MessageBox(nullptr, "按钮被点击！", "提示", OK);
    	});
    	// 添加控件到窗口
    	win.addControl(std::move(btn));
    	// 绘制窗口及控件
    	win.draw();
    	//进入事件循环
    	return win.runEventLoop();
    }

**要点：**

  1. 不再手写底层消息循环与绘图细节；
  2. 用 OOP 的控件加回调，把“业务逻辑”直接写在 C++ 代码里；
  3. EasyX 负责绘制，StellarX 负责 UI 组织与事件。



**教学级示例：** 32 位寄存器可视化工具（Register Viewer）

仓库自带 Demo：examples/register-viewer/

它用**不到 500 行代码** ，做了一个可视化的 32 位寄存器编辑器：

顶部 32 个 Toggle 按钮：代表 bit31..bit0，点击切换 0/1；  
中部 三组功能：位范围取反、整体左移/右移；  
下方 显示区：即时显示十六进制/十进制值，以及分组二进制字符串；  
有符号/无符号 切换：十进制显示按需解释为 int32_t 或 uint32_t。  
核心片段 1：批量创建 32 位 Toggle 按钮
    
    
    auto area = std::make_unique<Canvas>(10, 10, 680, 150);
    for (int row = 0; row < 2; ++row) 
    {
    	for (int col = 0; col < 16; ++col) 
    	{
    		int x = col * 35 + 42 + 28 * (col / 4);
    		int y = (row == 0 ? 58 : 120);
    		auto btn = std::make_unique<Button>(
    			x, y, 20, 32, "0",
    			RGB(0,0,0), RGB(171,196,220),
    			ButtonMode::TOGGLE
    		);
    		int bit = (row == 0 ? 31 - col : 15 - col);
    		btn->setOnToggleOnListener([bit, p = btn.get()]()
    		{
    			p->setButtonText("1"); /* 更新内部位数组 … */
    		});
    		btn->setOnToggleOffListener([bit, p = btn.get()]()
    		{
    			p->setButtonText("0");
    		});
    		area->addControl(std::move(btn));
    	}
    }
    win.addControl(std::move(area));

核心片段 2：左移 / 右移 / 位取反回调
    
    
    invBtn->setOnClickListener([&](){
    	/* 读取起止位 L/H -> 翻转 [L,H] 区间 -> 刷新显示 */
    });
    
    shlBtn->setOnClickListener([&](){
    	/* 读取 n -> 整体左移 n 位 -> 刷新显示 */
    });
    
    shrBtn->setOnClickListener([&](){
    	/* 读取 n -> 整体右移 n 位 -> 刷新显示 */
    });

核心片段 3：显示区刷新
    
    
    void refreshDisplays(const std::array<bool,32>& bits) {
    	uint32_t u = /* 从 bits 组装无符号值 */;
    	int32_t  s = static_cast<int32_t>(u);
    	hexBox->setText(fmtHex(u));					// 8位16进制
    	decBox->setText(gSigned ? to_string(s) : to_string(u));
    	lastBox->setText(thisBox->getText());
    	thisBox->setText(groupedBinary(bits));		// 0000_0000_…
    }

 ![](/f/a/0/0/756/QQ20251007-172519.png)  
   
**与几类常见方案的对比**

方案 | 依赖/体量 | 学习曲线 | 控件体系 | 跨平台 | 典型场景  
---|---|---|---|---|---  
Win32/MFC | 轻/中 | 陡 | 需手写/陈旧 | Windows | 老项目/系统级  
Qt | 重 | 中~陡 | 全家桶 | ✅ | 大型/跨平台  
Dear ImGui | 中 | 中 | 工具风 | ✅ | 调试 UI/游戏内  
EasyX | 轻 | 低 | 无控件（自行绘制） | Windows | 入门绘图/教学  
StellarX（基于 EasyX） | 轻（~14MB） | 低 | 常用控件齐全 | Windows | 教学 & 小工具  
  
  
**结论：**

_做课堂演示、课程设计、实验/比赛、内部小工具：StellarX 够用且省心；_  
 _做跨平台/大型 GUI：请选 Qt 等成熟框架；_  
 _做嵌入式调试 UI：ImGui 更合适。_  
 _ _  
**教学怎么用更 “顺手”？**

  1. 课堂演示：先用 EasyX 讲绘图基础，再用 StellarX 展示“如何把绘图封装成控件、用回调组织交互”。
  2. 作业/课程设计：给出“窗口 + 若干控件 + 事件骨架”的模板，让学生填充业务逻辑即可。
  3. 源码导读：带学生读 Control 基类、事件分发、最小重绘等关键模块，理解 GUI 框架的“幕后”。
  4. 实战题材：如寄存器编辑器、串口助手、图片批量重命名、简易标注工具、日志查看器等。



**常见问题（FAQ）**  
Q1：一定要会 EasyX 吗？  
A：建议安装 EasyX 并了解基础（坐标/颜色/窗口），StellarX 会自动链接 EasyX。不会也能用，但懂 EasyX 更容易理解背后原理。

Q2：能跨平台吗？  
A：暂不支持。StellarX 面向 Windows 与教学/小工具定位；跨平台请考虑 Qt/wxWidgets 等。

Q3：能做复杂 UI 吗？  
A：适合“小而美”应用；高 DPI、自适应布局、无障碍、重型控件等不是当前目标。欢迎以 PR 的方式共建。

Q4：可以商用吗？  
A：MIT 许可，商用/二次开发均可。请遵循 LICENSE 并在合适位置标注来源。

**路线图**  
✅ 常用控件与事件体系（已完成）  
🔄 更多示例与模板工程（进行中）  
🔄 文档站与 API 参考（进行中）  
🧪 自动化测试与 CI（计划）  
🧩 第三方控件生态（计划：欢迎贡献者加入）  
  
**参与贡献 & 交流**  
 _GitHub 提交 Issue/PR：https://github.com/Ysm-04/StellarX_  
 _欢迎贡献 示例/文档/翻译/美化主题 等多种形式，不限于写代码_  
 _建议为新人标注 good first issue，降低首次参与门槛_  
 _也欢迎老师同学把课堂作品回贴到仓库 Discussions_

**结语**  
StellarX 并不追求“面面俱到”，而是把“可用、可教、可拓展”做到了极致：

对老师：更易讲授 GUI 原理；  
对学生：更快做出“能跑起来”的桌面工具；  
对个人开发者：更顺手地把想法落地成小应用。  
如果你正打算在 Windows + C++ 上做一个轻量 GUI，欢迎试试 StellarX ——  
繁星为界，轻若尘埃。

项目主页：https://github.com/Ysm-04/StellarX  
欢迎 Star、反馈与贡献，一起把这套“基于 EasyX 的 GUI 教学实战框架”做得更好。

[GUI](/t/GUI)

![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/stellarx/a/stellax\))

[取消回复](javascript:void\(0\);)

![霜霜不会压枪](//thirdqq.qlogo.cn/ek_qqapp/AQIW3fgqXQQZI45bXepspYPtK5vEp3FDCGXnsI4N5PIyI590cLToFOwic4SnqLwoqPDoCfDNLmibHsJu7Qzh0Jk1ZfdXVLTSXfEictyDNuiboGAdHr1qDRGCWsNG7dZxUA/0)

[霜霜不会压枪](/stellarx/)

  * [__](/stellarx/ "主页")
  * [__](https://i.codebus.cn/user/ec945ff3-9ccf-b385-2832-644dbab0c2c0 "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=3150131407 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=3150131407&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=ec945ff3-9ccf-b385-2832-644dbab0c2c0 "添加好友")



搜索

#### 标签云

  * [GUI](/stellarx/t/GUI)



广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
