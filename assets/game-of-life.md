[![](/Content/images/codebus-white.svg)](/)

# [tofuHut](/tofu/)

写一些简单的EasyX小游戏

  * [管理](/console/posts)



##  Game of Life [![铜牌收录](/content/images/bronze.png)](?medal=1 "铜牌收录")

__2025-9-22 __[の拽着糖纸不放手](/tofu/) [ __(0)](https://codebus.cn/tofu/game-of-life#comment)

**Game of Life - 元胞自动机**

**介绍**  
生命游戏（Game of Life）是由英国数学家约翰·何顿·康威在 1970 年发明的元胞自动机。它在一个二维网格上运行，每个网格有两种状态：存活或死亡。每个细胞的状态由它周围八个邻居的状态决定，规则如下：

  1. 任何活细胞，如果活邻居少于 2 个，则死亡（模拟人口不足）
  2. 任何活细胞，如果活邻居为 2 个或 3 个，则继续存活
  3. 任何活细胞，如果活邻居超过 3 个，则死亡（模拟过度拥挤）
  4. 任何死细胞，如果活邻居正好是 3 个，则复活（模拟繁殖）



这个程序使用 C++ 编写，图形界面使用 EasyX 库实现，提供了一个交互式的生命游戏模拟环境。

![](/f/a/0/0/755/1.png)

**软件架构**

程序采用面向对象设计，主要类为 GameOfLife，封装了游戏的核心逻辑和图形界面操作：

成员变量：

  * cellSize：细胞大小（像素）
  * gridWidth, gridHeight：网格尺寸
  * currentGrid, nextGrid：当前和下一代细胞状态
  * running：模拟运行状态
  * generation：当前代数
  * population：存活细胞数量



核心方法：

  * countNeighbors()：计算邻居数量
  * update()：更新细胞状态
  * draw()：绘制网格和细胞
  * handleInput()：处理用户输入
  * clearGrid()：清空网格
  * randomGrid()：随机初始化网格



程序使用双缓冲技术避免画面闪烁，支持自定义网格大小和细胞大小。

**使用说明**

程序启动后，您将看到一个网格窗口：

鼠标操作

  * 左键点击：在网格上点击可以设置细胞存活（彩色）或死亡（黑色）
  * 左键拖动：可以连续绘制细胞



键盘操作

  * 空格键：开始/暂停模拟
  * C 键：清空网格
  * R 键：随机生成网格（约 10% 的细胞存活）
  * \+ 键：增加模拟速度（减少延迟）
  * \- 键：降低模拟速度（增加延迟）
  * ESC 键：退出程序



**界面说明**

  * 顶部显示当前代数、存活细胞数量、模拟速度和运行状态
  * 底部显示操作提示



**特色功能**

  * 使用双缓冲技术实现流畅动画
  * 支持自定义网格大小和细胞大小
  * 细胞颜色随代数变化，视觉效果丰富
  * 实时显示统计信息



# 源码

可以通过工具栏的 **{;}** 按钮插入源代码。效果如下：
    
    
    #include <graphics.h>
    #include <conio.h>
    #include <vector>
    #include <chrono>
    #include <thread>
    #include <string>
    #include <sstream>
    
    using namespace std;
    
    const int DEFAULT_CELL_SIZE = 8;
    const int DEFAULT_GRID_WIDTH = 100;
    const int DEFAULT_GRID_HEIGHT = 100;
    
    class GameOfLife
    {
    private:
    	int cellSize;
    	int gridWidth;
    	int gridHeight;
    	int windowWidth;
    	int windowHeight;
    
    	vector<vector<bool>> currentGrid;
    	vector<vector<bool>> nextGrid;
    
    	bool running;
    	bool drawing;
    	int generation;
    	int population;
    	int speed;
    
    	// 计算邻居数量
    	int countNeighbors(int x, int y)
    	{
    		int count = 0;
    		for (int i = -1; i <= 1; i++)
    		{
    			for (int j = -1; j <= 1; j++)
    			{
    				if (i == 0 && j == 0)continue;
    
    				int nx = x + i;
    				int ny = y + j;
    
    				// 边界检查（环形边界）
    				if (nx < 0)nx = gridWidth - 1;
    				if (nx >= gridWidth)nx = 0;
    				if (ny < 0)ny = gridHeight - 1;
    				if (ny >= gridHeight)ny = 0;
    
    				if (currentGrid[ny][nx])count++;
    			}
    		}
    		return count;
    	}
    
    public:
    	GameOfLife(int cellSize = DEFAULT_CELL_SIZE,
    		int gridWidth = DEFAULT_GRID_WIDTH,
    		int gridHeight = DEFAULT_GRID_HEIGHT)
    		: cellSize(cellSize), gridWidth(gridWidth), gridHeight(gridHeight),
    		windowWidth(gridWidth* cellSize), windowHeight(gridHeight* cellSize),
    		running(false), drawing(false), generation(0), population(0), speed(100)
    	{
    
    		// 初始化网格
    		currentGrid.resize(gridHeight, vector<bool>(gridWidth, false));
    		nextGrid.resize(gridHeight, vector<bool>(gridWidth, false));
    
    		// 初始化图形窗口
    		initgraph(windowWidth, windowHeight, EW_SHOWCONSOLE | EW_DBLCLKS);
    		setbkcolor(RGB(20, 20, 30));
    		cleardevice();
    
    		// 启用双缓冲
    		BeginBatchDraw();
    	}
    
    	~GameOfLife()
    	{
    		// 结束双缓冲
    		EndBatchDraw();
    		closegraph();
    	}
    
    	// 更新网格状态
    	void update()
    	{
    		if (!running)return;
    
    		population = 0;
    
    		for (int y = 0; y < gridHeight; y++)
    		{
    			for (int x = 0; x < gridWidth; x++)
    			{
    				int neighbors = countNeighbors(x, y);
    
    				// 应用生命游戏规则
    				if (currentGrid[y][x])
    				{
    					// 活细胞规则：少于2个或多于3个邻居则死亡
    					nextGrid[y][x] = (neighbors == 2 || neighbors == 3);
    				}
    				else
    				{
    					// 死细胞规则：恰好3个邻居则复活
    					nextGrid[y][x] = (neighbors == 3);
    				}
    
    				if (nextGrid[y][x])population++;
    			}
    		}
    
    		// 交换网格
    		swap(currentGrid, nextGrid);
    		generation++;
    	}
    
    	// 绘制网格
    	void draw()
    	{
    		cleardevice();
    
    		// 绘制网格线
    		setlinecolor(RGB(40, 40, 60));
    		for (int x = 0; x <= gridWidth; x++)
    		{
    			line(x * cellSize, 0, x * cellSize, windowHeight);
    		}
    		for (int y = 0; y <= gridHeight; y++)
    		{
    			line(0, y * cellSize, windowWidth, y * cellSize);
    		}
    
    		// 绘制存活细胞
    		for (int y = 0; y < gridHeight; y++)
    		{
    			for (int x = 0; x < gridWidth; x++)
    			{
    				if (currentGrid[y][x])
    				{
    					setfillcolor(HSVtoRGB(static_cast<float>(generation % 360), 0.8f, 0.9f));
    					// 绘制方块
    					solidrectangle(x * cellSize, y * cellSize,
    						(x + 1)* cellSize - 1, (y + 1)* cellSize - 1);
    				}
    			}
    		}
    
    		// 绘制UI信息
    		settextcolor(RGB(200, 220, 255));
    		settextstyle(16, 0, _T("Consolas"));
    
    		TCHAR info[256];
    		_stprintf_s(info, _T("Generation: %d  Population: %d  Speed: %dms  Status: %s"),
    			generation, population, speed, running ? _T("RUNNING"): _T("PAUSED"));
    		outtextxy(10, 10, info);
    
    		// 绘制帮助信息
    		settextcolor(RGB(180, 200, 230));
    		TCHAR help[] = _T("Space: Start/Pause  C: Clear  R: Random  +/-: Speed  ESC: Exit");
    		outtextxy(10, windowHeight - 30, help);
    
    		// 刷新双缓冲
    		FlushBatchDraw();
    	}
    
    	// 处理输入
    	void handleInput()
    	{
    		ExMessage msg;
    		while (peekmessage(&msg, EM_MOUSE | EM_KEY))
    		{
    			if (msg.message == WM_LBUTTONDOWN)
    			{
    				drawing = true;
    				int x = msg.x / cellSize;
    				int y = msg.y / cellSize;
    				if (x >= 0 && x < gridWidth && y >= 0 && y < gridHeight)
    				{
    					currentGrid[y][x] = !currentGrid[y][x];
    					population += currentGrid[y][x] ? 1 : -1;
    					draw(); // 立即绘制更新
    				}
    			}
    			else if (msg.message == WM_LBUTTONUP)
    			{
    				drawing = false;
    			}
    			else if (msg.message == WM_MOUSEMOVE && drawing)
    			{
    				int x = msg.x / cellSize;
    				int y = msg.y / cellSize;
    				if (x >= 0 && x < gridWidth && y >= 0 && y < gridHeight)
    				{
    					if (!currentGrid[y][x])
    					{
    						currentGrid[y][x] = true;
    						population++;
    						draw(); // 立即绘制更新
    					}
    				}
    			}
    			else if (msg.message == WM_KEYDOWN)
    			{
    				if (msg.vkcode == VK_SPACE)
    				{
    					running = !running; // 空格键开始/暂停
    					draw(); // 立即绘制更新
    				}
    				else if (msg.vkcode == 'C')
    				{
    					clearGrid(); // 清空网格
    					draw(); // 立即绘制更新
    				}
    				else if (msg.vkcode == 'R')
    				{
    					randomGrid(); // 随机生成网格
    					draw(); // 立即绘制更新
    				}
    				else if (msg.vkcode == VK_ADD && speed > 10)
    				{
    					speed -= 10; // 增加速度
    					draw(); // 立即绘制更新
    				}
    				else if (msg.vkcode == VK_SUBTRACT)
    				{
    					speed += 10; // 降低速度
    					draw(); // 立即绘制更新
    				}
    				else if (msg.vkcode == VK_ESCAPE)
    				{
    					exit(0); // 退出程序
    				}
    			}
    		}
    	}
    
    	// 清空网格
    	void clearGrid()
    	{
    		for (auto& row : currentGrid)
    		{
    			for (size_t i = 0; i < row.size(); i++)
    			{
    				row[i] = false;
    			}
    		}
    		generation = 0;
    		population = 0;
    		running = false;
    	}
    
    	// 随机生成网格
    	void randomGrid()
    	{
    		population = 0;
    		for (auto& row : currentGrid)
    		{
    			for (size_t i = 0; i < row.size(); i++)
    			{
    				row[i] = (rand()% 10)== 0; // 10%的概率为活细胞
    				if (row[i])population++;
    			}
    		}
    		generation = 0;
    		running = false;
    	}
    
    	// 运行游戏
    	void run()
    	{
    		randomGrid();
    		draw(); // 初始绘制
    
    		while (true)
    		{
    			handleInput();
    			update();
    			if (running)
    			{
    				draw();
    			}
    			this_thread::sleep_for(chrono::milliseconds(speed));
    		}
    	}
    };
    
    int main()
    {
    	// 创建游戏实例
    	GameOfLife game;
    	game.run();
    	return 0;
    }

[生命游戏](/t/%E7%94%9F%E5%91%BD%E6%B8%B8%E6%88%8F)[元胞自动机](/t/%E5%85%83%E8%83%9E%E8%87%AA%E5%8A%A8%E6%9C%BA)

![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/tofu/a/game-of-life\))

[取消回复](javascript:void\(0\);)

![の拽着糖纸不放手](//thirdqq.qlogo.cn/ek_qqapp/AQJ2yPgYMN5XlsR60GvWzCn8LibeGRLJibiatfib7Yq6WXc3mBRV17MQGFrgmpF5FjOVgusuC0VmZWFRIrZqwQEaSSnWYhqeKbS0CDMcxu4NtvOyTicf3zWs/0)

[の拽着糖纸不放手](/tofu/)

  * [__](/tofu/ "主页")
  * [__](https://i.codebus.cn/user/cd7bcf4c-14eb-e3e7-7a90-3f5b58406f38 "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=2890851110 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=2890851110&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=cd7bcf4c-14eb-e3e7-7a90-3f5b58406f38 "添加好友")



搜索

#### 标签云

  * [小游戏](/tofu/t/%E5%B0%8F%E6%B8%B8%E6%88%8F)
  * [围棋](/tofu/t/%E5%9B%B4%E6%A3%8B)
  * [生命游戏](/tofu/t/%E7%94%9F%E5%91%BD%E6%B8%B8%E6%88%8F)
  * [绘图](/tofu/t/%E7%BB%98%E5%9B%BE)
  * [游戏](/tofu/t/%E6%B8%B8%E6%88%8F)
  * [浪漫](/tofu/t/%E6%B5%AA%E6%BC%AB)
  * [元胞自动机](/tofu/t/%E5%85%83%E8%83%9E%E8%87%AA%E5%8A%A8%E6%9C%BA)
  * [表白](/tofu/t/%E8%A1%A8%E7%99%BD)



广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
