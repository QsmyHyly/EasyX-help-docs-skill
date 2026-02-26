[![](/Content/images/codebus-white.svg)](/)

# [May](/sentence/)

”你说的每个笑话我都笑了，是你变幽默还是我变快乐“

  * [管理](/console/posts)



##  C 语言简易贪吃蛇游戏 [![铜牌收录](/content/images/bronze.png)](?medal=1 "铜牌收录")

__2025-8-13 ~ 2025-8-15 __[Sentence](/sentence/) [ __(0)](https://codebus.cn/sentence/snake#comment)

# 代码描述

贪吃蛇（Snake Game）是一款经典的单机游戏，玩家操控一条蛇在屏幕上移动，通过吃掉食物来增长身体长度。随着蛇身变长，游戏难度逐渐增加，一旦蛇头撞到墙壁或自身身体，游戏结束。

贪吃蛇最早出现在 1976 年的街机游戏《Blockade》，后来因诺基亚手机预装而风靡全球。其简单易上手的玩法、清晰的规则和逐渐提升的挑战性，使其成为编程初学者实现小项目的热门选择。

如今，贪吃蛇不仅是怀旧游戏的代表，还被用于算法教学和游戏开发入门练习。

# 操作方法

可以通过 wsad 来控制蛇的上下左右移动

通过 moveSnake 函数控制蛇吃食物

每吃到一次 Score 加一分，如果撞到四周墙壁游戏结束退出

# 完整源码
    
    
    ///////////////////////////////////////////////////
    // 程序名称：贪吃蛇游戏（Snake Game）
    // 编译环境：Codeblocks 17.12, EasyX_20240601
    // 作　　者：May <1904774314@qq.com>
    // 最后修改：2025-8-13
    //
    
    #include<graphics.h>
    #include<conio.h>
    #include<stdio.h>
    #define WIDTH 40
    #define HEIGHT 30
    #define SIZE 20
    
    int Blocks[WIDTH][HEIGHT] = {0};
    int Food_X, Food_Y;
    int Score = 0;
    char moveDirection;
    char keyQueue = '\0';
    bool isFailure = false;
    
    bool IsGameRunning();
    void InitGame();
    void Show();
    void UpdateGame();
    void moveSnake();
    void ShowGameOver();
    
    bool IsGameRunning()
    {
    	return !isFailure; // 集中管理游戏状态
    }
    
    void InitGame()
    {
    	initgraph(WIDTH * SIZE, HEIGHT * SIZE);
    	setbkcolor(LIGHTGRAY);
    	cleardevice();
    	int i;
    	for(i = SIZE; i <= WIDTH * SIZE; i += SIZE)
    	{
    		line(i, 0, i, HEIGHT * SIZE);
    	}
    	for(i = SIZE; i <= HEIGHT * SIZE; i += SIZE)
    	{
    		line(0, i, WIDTH * SIZE, i);
    	}
    	Blocks[WIDTH / 2][HEIGHT / 2] = 1;
    	moveDirection = 'd';
    	for(i = 0; i <= 4; i++)
    	{
    		Blocks[(WIDTH / 2)-i][HEIGHT / 2] = i + 1;
    	}
    	Food_X = rand() % (WIDTH - 2) + 1;
    	Food_Y = rand() % (HEIGHT - 2) + 1;
    }
    
    void ShowGameOver()
    {
    	cleardevice();
    
    	settextcolor(RED);
    	settextstyle(60, 0, "微软雅黑");
    	outtextxy(WIDTH * SIZE / 2 - 100, HEIGHT * SIZE / 2 - 100, "游戏结束");
    
    	char scoreStr[50];
    	sprintf(scoreStr, "最终得分: %d", Score);
    	outtextxy(WIDTH * SIZE / 2 - 120, HEIGHT * SIZE / 2 - 10, scoreStr);
    
    	settextstyle(20, 0, "微软雅黑");
    	outtextxy(WIDTH * SIZE / 2 - 70, HEIGHT * SIZE / 2 + 70, "按任意键退出...");
    
    	_getch();
    }
    
    void Show()
    {	
    	int i, j;
    
    	setfillcolor(LIGHTGRAY);
    	for(int i = 0; i <= WIDTH; i++)
    	{
    		for(int j = 0; j <= HEIGHT; j++)
    		{
     			if(Blocks[i][j] == 0 && !(i == Food_X && j == Food_Y))
    			{
    				fillrectangle(i * SIZE, j * SIZE, (i + 1) * SIZE, (j + 1) * SIZE);
    			}
    		}
    	}
    
    	for(i = 0; i<=WIDTH; i++)
    	{
    		for(j = 0; j<=HEIGHT; j++)
    		{
    			if(Blocks[i][j] != 0)
    			{
    				setfillcolor(HSVtoRGB(Blocks[i][j] * 10, 0.9, 1));
    				fillrectangle(i * SIZE, j * SIZE, (i + 1) * SIZE, (j + 1) *SIZE);
    			}
    		}
    	}
    	setfillcolor(LIGHTGREEN);
    	fillrectangle(Food_X * SIZE, Food_Y * SIZE, (Food_X + 1) * SIZE, (Food_Y + 1) *SIZE);
    
    	// 显示分数
    	char str[50];
    	sprintf(str, "score:%d", Score);
    	settextcolor(RED);
    	outtextxy(WIDTH * SIZE - 70, 10, str);
    }
    
    void HandleInput()
    {
    	if(_kbhit())
    	{
    		char input = _getch();
    		if(input == 'w' || input == 's' || input == 'd' || input == 'a')
    		{
    			keyQueue = input;
    		}
    	}
    }
    
    void UpdateGame()
    {
    	if (keyQueue != '\0')
    	{
     		moveDirection = keyQueue;
    		keyQueue = '\0';
    	}
    	moveSnake();
    }
    
    void moveSnake()
    {
    	// 蛇身增长
    	// 遍历整个游戏区域，为每个蛇身段增加其值（实现蛇身移动效果）
    	for(int i = 0; i < WIDTH; i++)
    	{
    		for(int j = 0; j < HEIGHT; j++)
    		{
    			if(Blocks[i][j] != 0)
    			{
    				Blocks[i][j]++; // 值越大表示蛇身段越靠近尾部
    			}
    		}
    	}
    
    	// 定位关键位置
    	// 初始化变量(推荐：声明时立即初始化）
    	int oldHead_X = 0, oldHead_Y = 0; // 原头部位置（值为 2 的格子）
    	int oldTail_X = 0, oldTail_Y = 0; // 原尾部位置（值最大的格子）
    	int tailBlocks = 0; // 记录最大蛇身段值
    
    	// 遍历查找蛇头和蛇尾
    	for(int i = 0; i < WIDTH; i++)
    	{
    		for(int j = 0; j < HEIGHT; j++)
    		{
    			// 查找蛇尾（值最大的格子）
    			if(tailBlocks < Blocks[i][j])
    			{
    		 		tailBlocks = Blocks[i][j];
    				oldTail_X = i;
    				oldTail_Y = j;
    			}
    
    			// 查找蛇头（值为 2 的格子）
    			if(Blocks[i][j] == 2)
    			{
    				oldHead_X = i;
    				oldHead_Y = j;
    			}
    		}
    	}
    
    	// 计算新头部位置
    	// 根据移动方向计算新头部坐标
    	int newHead_X = oldHead_X;
    	int newHead_Y = oldHead_Y;
    
    	switch(moveDirection)
    	{
    	case 'a': newHead_X -= 1; break; // 左移
    	case 'd': newHead_X += 1; break; // 右移
    	case 'w': newHead_Y -= 1; break; // 上移
    	case 's': newHead_Y += 1; break; // 下移
    	}
    
    	// 碰撞检测
    	// 边界检查
    	if(newHead_X < 0 || newHead_X >= WIDTH || newHead_Y < 0 || newHead_Y >= HEIGHT)
    	{
    		isFailure = true; // 撞墙，游戏结束
    		return;
    	}
    	
    	if(Blocks[newHead_X][newHead_Y] > 0)
    	{
    		isFailure = true;
    		return;
    	}
    
    	// 更新蛇位置
    	// 放置新蛇头（值设为 1, 最小）
    	Blocks[newHead_X][newHead_Y] = 1;
    
    	// 检查是否吃到食物
    	if(newHead_X == Food_X && newHead_Y == Food_Y)
    	{
    		// 吃到食物：生成新食物，不删除尾部（蛇身变长）
    		Food_X = rand() % (WIDTH - 2) + 1;
    		Food_Y = rand() % (HEIGHT - 2) + 1;
    		Score++;
    	}
    	else
    	{
    		// 未吃到食物：删除尾部（蛇身长度不变）
    		Blocks[oldTail_X][oldTail_Y] = 0;
    	}
    }
    
    // 主函数
    
    int main()
    {
    	InitGame();
    	while(IsGameRunning())
    	{
    		HandleInput();
    		UpdateGame();
    		Show();
    		Sleep(200);
    	}
    	ShowGameOver();
    	return 0;
    }
    

# 运行效果

![](/f/a/0/0/750/Snake.png)

# 参考资料

  * 童晶老师《C 和 C++ 游戏趣味编程》



![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/sentence/a/snake\))

[取消回复](javascript:void\(0\);)

![Sentence](//thirdqq.qlogo.cn/ek_qqapp/AQBggdffguq0wgN8emysP3HqKAiaaziauOfahw2rVr2PYHlKX5340JfALzGUQZgdWKL9Yst8maqIDNiawZgmY35a15Ob4iaKU1Xcvyxz3qP53BNHcZFH0NY/0)

[Sentence](/sentence/)

  * [__](/sentence/ "主页")
  * [__](https://i.codebus.cn/user/7b30aaf0-083d-bae7-a4bb-a9ea156944ab "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=1904774314 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=1904774314&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=7b30aaf0-083d-bae7-a4bb-a9ea156944ab "添加好友")



搜索

#### 标签云




广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
