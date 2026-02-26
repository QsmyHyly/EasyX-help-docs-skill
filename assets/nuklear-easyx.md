[![](/Content/images/codebus-white.svg)](/)

# [XiongFJ](/xiongfj/)

寄蜉蝣于天地，渺沧海之一粟

  * [管理](/console/posts)



##  即时模式 GUI 的 EasyX 实现（基于 Nuklear） [![金牌收录](/content/images/gold.png)](?medal=3 "金牌收录")

__2025-9-22 ~ 2025-12-26 __[xiongfj◑◑](/xiongfj/) [ __(0)](https://codebus.cn/xiongfj/nuklear-easyx#comment)

Nuklear 是一个 C 编写的、只有头文件的 GUI 界面库，

也是 IMGUI (Immediate Mode User Interface) 类的界面实现工具，

支持多种渲染后端，包括 gdi/gdi+/D3D/X11/Xft/SDL/opengl 等，本文实现了 EasyX 的后端渲染。

确保中文正常显示的方式：

  1. 使用 u8"中文xxx"，该方式支持 vs2015 及以上版本。
  2. 使用 #pragma execution_character_set("utf-8")，该方式支持 vs 2013 及以上版本。



### Nuklear 实现常用控件：

Input TextBox Button Popup CheckBox Combo Selection 等等。

 ![](/f/a/0/0/753/4.jpg) ![](/f/a/0/0/753/2.jpg) ![](/f/a/0/0/753/3.jpg)

![](/f/a/0/0/753/aaaaa.jpg)

### 示例 Demo 展示：

对应项目文件夹下 /demo/common/xxxx.c 具体实现，取消相应的注释即可运行文档中 demo 示例程序。
    
    
    /* ===============================================================
     *
     *				打开相应的注释，可以运行不同的示例
     *
     * ===============================================================*/
    //#define INCLUDE_ALL 
    #define INCLUDE_STYLE 
    #define INCLUDE_CALCULATOR 
    //#define INCLUDE_CANVAS 
    #define INCLUDE_OVERVIEW
    #define INCLUDE_CONFIGURATOR 
    //#define INCLUDE_NODE_EDITOR 

### 完整示例代码：
    
    
    /********** 摘自文档 **********/
    #define _CRT_SECURE_NO_WARNINGS
    #define WIN32_LEAN_AND_MEAN
    #define NK_INCLUDE_FIXED_TYPES				// 这个宏会引入 <stdint.h> 以帮助 nuklear 使用正确的数据类型
    // nuklear 的设计目标是适配所有平台，以上几个宏是控制 nuklear 特性和行为的
    // 不同平台基本数据类型大小不一样，指针长度不一样
    // 有些平台(例如嵌入式)不提供 IO 功能，或者使用者希望 nuklear context 使用固定分配的内存等各种特殊需求
    // 在 PC 平台上定义以上几个宏几乎是标准动作
    #pragma warning(disable:4996)				// nuklear 需要使用新版本 VS 默认禁止的 vsprintf 等旧版 C 函数
    #pragma execution_character_set("utf-8")	// VS 需要设定执行字符集为 utf8 格式，否则非英文字符会乱码
    /********** 摘自文档 - End **********/
    
    
    #include <windows.h>
    #include <stdio.h>
    #include <string.h>
    #include <time.h>
    #include <math.h>
    #include <limits.h>
    #include <Shlobj.h>
    
    #define WINDOW_WIDTH 800
    #define WINDOW_HEIGHT 600
    
    #define NK_INCLUDE_FIXED_TYPES
    #define NK_INCLUDE_STANDARD_IO
    #define NK_INCLUDE_STANDARD_VARARGS
    #define NK_INCLUDE_DEFAULT_ALLOCATOR
    #define NK_IMPLEMENTATION
    #define NK_EASYX_IMPLEMENTATION
    #include "../nuklear.h"
    #include "nuklear_easyx.h"
    
    /* ===============================================================
     *
     *				打开相应的注释，可以运行不同的示例
     *
     * ===============================================================*/
    //#define INCLUDE_ALL 
    #define INCLUDE_STYLE 
    #define INCLUDE_CALCULATOR 
    //#define INCLUDE_CANVAS 
    #define INCLUDE_OVERVIEW
    #define INCLUDE_CONFIGURATOR 
    //#define INCLUDE_NODE_EDITOR 
    
    #ifdef INCLUDE_ALL
      #define INCLUDE_STYLE
      #define INCLUDE_CALCULATOR
      #define INCLUDE_CANVAS
      #define INCLUDE_OVERVIEW
      #define INCLUDE_CONFIGURATOR
      #define INCLUDE_NODE_EDITOR
    #endif
    
    #ifdef INCLUDE_STYLE
      #include "../demo/common/style.c"
    #endif
    #ifdef INCLUDE_CALCULATOR
      #include "../demo/common/calculator.c"
    #endif
    #ifdef INCLUDE_CANVAS
      #include "../demo/common/canvas.c"
    #endif
    #ifdef INCLUDE_OVERVIEW
      #include "../demo/common/overview.c"
    #endif
    #ifdef INCLUDE_CONFIGURATOR
      #include "../demo/common/style_configurator.c"
    #endif
    #ifdef INCLUDE_NODE_EDITOR
      #include "../demo/common/node_editor.c"
    #endif
    
    int main(void)
    {
        EasyXFont* font;
        struct nk_context *ctx;
    
        HWND wnd;
        HDC dc;
        int running = 1;
        int needs_refresh = 1;
    
        #ifdef INCLUDE_CONFIGURATOR
        static struct nk_color color_table[NK_COLOR_COUNT];
        memcpy(color_table, nk_default_color_style, sizeof(color_table));
        #endif
    
    	initgraph(900 , 700);
    	wnd = GetHWnd();
    	dc = GetDC(wnd);
    	HDC ddc = GetImageHDC();
    
        font = nk_easyxfont_create("Consolas", 16);
        ctx = nk_easyx_init(font, dc, WINDOW_WIDTH, WINDOW_HEIGHT);
    
    	BeginBatchDraw();
        while (running)
        {
    		Sleep(10);
    
            ExMessage msg;
            nk_input_begin(ctx);
            if (needs_refresh == 0) {
    			msg = getmessage();
                if (msg.message == WM_QUIT)
                    running = 0;
                else
    				nk_easyx_handle_event(wnd, &msg);
                
                needs_refresh = 1;
            }
    		else
    			needs_refresh = 0;
    
            while (peekmessage(&msg)) {
                if (msg.message == WM_QUIT)
                    running = 0;
    			nk_easyx_handle_event(wnd, &msg);
                needs_refresh = 1;
            }
            nk_input_end(ctx);
    
            if (nk_begin(ctx, "Demo", nk_rect(50, 50, 200, 200),
                NK_WINDOW_BORDER|NK_WINDOW_MOVABLE|NK_WINDOW_SCALABLE|
                NK_WINDOW_CLOSABLE|NK_WINDOW_MINIMIZABLE|NK_WINDOW_TITLE))
            {
                enum {EASY, HARD};
                static int op = EASY;
                static int property = 20;
    
                nk_layout_row_static(ctx, 30, 80, 2);
    			if (nk_button_label(ctx, "button")) {
    				fprintf(stdout, "button pressed\n");
    			}
    
                nk_layout_row_dynamic(ctx, 30, 2);
                if (nk_option_label(ctx, "easy", op == EASY)) op = EASY;
                if (nk_option_label(ctx, "hard", op == HARD)) op = HARD;
    
                nk_layout_row_dynamic(ctx, 22, 1);
                nk_property_int(ctx, "Compression:", 0, &property, 100, 10, 1);
            }
            nk_end(ctx);
    
            /* -------------- EXAMPLES 摘自文档 ---------------- */
            #ifdef INCLUDE_CALCULATOR
              calculator(ctx);
            #endif
            #ifdef INCLUDE_CANVAS
              canvas(ctx);
            #endif
            #ifdef INCLUDE_OVERVIEW
              overview(ctx);
            #endif
            #ifdef INCLUDE_CONFIGURATOR
              style_configurator(ctx, color_table);
            #endif
            #ifdef INCLUDE_NODE_EDITOR
              node_editor(ctx);
            #endif
            /* --------------- EXAMPLES END -------------------------- */
    
    		nk_easyx_render(nk_rgb(30,30,30));
    		FlushBatchDraw();
        }
    
    	EndBatchDraw();
    
        nk_easyxfont_del(font);
        ReleaseDC(wnd, dc);
    	closegraph();
        return 0;
    }
    
    

### 完整项目文件下载：

[NuklearEasyX](/f/a/0/0/753/Code.7z "NuklearEasyX")

[EasyX](/t/EasyX)[GUI](/t/GUI)[nuklear](/t/nuklear)

![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/xiongfj/a/nuklear-easyx\))

[取消回复](javascript:void\(0\);)

![xiongfj ◑◑](//thirdqq.qlogo.cn/ek_qqapp/AQCyc3afyHEcqXFIGvvgMqbY34hcoBbmp2uoiaEb6sphHrJDtX0k4MuQCzbJ2yIcG1yYshWbIMjQmPpia658WsZ717vCtXqzFvPoqNxfU11vxIP5omxCY/0)

[xiongfj ◑◑](/xiongfj/)

  * [__](/xiongfj/ "主页")
  * [__](https://i.codebus.cn/user/1fbb814a-6c15-ef30-3002-8713fc873e17 "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=837943056 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=837943056&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=1fbb814a-6c15-ef30-3002-8713fc873e17 "添加好友")



搜索

#### 标签云

  * [GitHub](/xiongfj/t/GitHub)
  * [MinGW](/xiongfj/t/MinGW)
  * [桌球](/xiongfj/t/%E6%A1%8C%E7%90%83)
  * [游戏](/xiongfj/t/%E6%B8%B8%E6%88%8F)
  * [nuklear](/xiongfj/t/nuklear)
  * [Game](/xiongfj/t/Game)
  * [EasyX_20210730](/xiongfj/t/EasyX_20210730)
  * [easyx_20250910](/xiongfj/t/easyx_20250910)
  * [setup](/xiongfj/t/setup)
  * [EasyX](/xiongfj/t/EasyX)
  * [C++](/xiongfj/t/C%2B%2B)
  * [绘图](/xiongfj/t/%E7%BB%98%E5%9B%BE)
  * [GUI](/xiongfj/t/GUI)
  * [vscode](/xiongfj/t/vscode)



广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
