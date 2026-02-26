[![](/Content/images/codebus-white.svg)](/)

# [妙妙小豆子](/gary/)

长风破浪会有时，直挂云帆济沧海

  * [管理](/console/posts)



##  四邻 [![金牌收录](/content/images/gold.png)](?medal=3 "金牌收录")

__2025-4-13 ~ 2025-6-8 __[花毛茛](/gary/) [ __(0)](https://codebus.cn/gary/four-neighbors-game#comment)

# 程序简介

四邻游戏，是在国产操作系统某次作品征集中的优秀作品中看到的。玩法是数字拼图，每块拼图分割成上下左右四个部分，要求相邻两块拼图对应的上下或左右部分的数字颜色一致，边缘不考虑，最后完整拼接。在拼图较小时（四乘四），突破口在于有些拼图会出现无相对应位置拼图的情况，例如某拼图顶部是数字 1，而没有其他拼图的底部是数字 1，由此确定这块拼图位于顶部边缘。而拼图较大时，大部分时候都不会出现突破口，还会出现一些相似拼图，例如三个部分都一致的拼图，在实际游戏中会出现没法顺利解的情况，有时候解六乘六半个小时后才发现第二块或者第三块猜错了，会有点想放弃重开，原程序是没有考虑这个问题的。

本程序的按钮，1.新开：当前边长重新生成拼图。2.复原：还原当前拼图。3.提示：针对拼图较大时（五乘五、六乘六）完全没有头绪时，每点一次提示会帮助多还原一块拼图，点两到三次基本就够用了。4.边长：调难度，边长 2~6。5.颜色：调难度，颜色 2~10，建议不动，如果感觉六乘六 10 个数字比较简单，可以调小。

玩法拓展讨论，我原本还想添加旋转功能，更大程度增加难度，但玩过六乘六后放弃了，不加提示成功率就不高。我还想到可以加一些万能拼图来降低难度；加一些墙拼图来改变大拼图形状......

# 程序运行展示

![](/f/a/0/0/742/four-neighbors-game.gif)

# 完整源代码
    
    
    ////////////////////////////////////////
    // 程序：四邻
    // 作者：Gary
    // 编译环境：Visual C++ 2010，EasyX_20211109
    // 编写日期：2025.4.13
    
    # include <math.h>
    # include <graphics.h>
    # include <time.h>
    # include <string>
    
    static HWND hOut;						// 画布
    
    // 格子
    struct Node1
    {
    	int num;										// 初始位置
    	POINT pos[5];									// 点位置
    	int num_top, num_bottom, num_left, num_right;	// 四格位置的值
    	int num_type;									// 状态
    };
    
    Node1 box_start[12][6];
    
    // 按钮
    struct Node2
    {
    	int posx1, posy1, posx2, posy2;
    	LPTSTR text;
    };
    
    Node2 box_button[6];
    
    // 定义一个类
    class Gary
    {
    public:
    	void carry();							// 主进程
    	void initialization();					// 初始化
    	void move();							// 窗口主视角函数
    	void time_new();						// 时间更新函数
    	void exchange(int i, int j, int x, int y);	// 交换函数
    	void check(int i, int j, int x, int y);	// 检测函数
    	void draw();							// 绘制函数
    	void end();								// 判定函数
    	void solve();							// 复原函数
    	void tip();								// 提示函数
    	int exit_carry;							// 主循函数控制参数
    	int exit_move;							// 开始界面控制参数
    	int length_side;						// 难度
    	int num_click;							// 点击
    	int num_tip;							// 提示
    	int num_color;							// 颜色种类
    	int num_time;							// 游戏时间
    	ExMessage m;							// 鼠标
    	COLORREF color_num[16];					// 颜色
    	TCHAR s[10];							// 字符串
    	clock_t start_t1;						// 游戏开始系统时间参数
    	clock_t start_t2;						// 游戏进行系统时间参数
    };
    
    // 复原函数
    void Gary::solve()
    {
    	int i, j, x, y;
    	// 根据初始位置交换
    	for(i = 0; i < length_side * 2; i++)
    	{
    		for(j = 0; j < length_side; j++)
    		{
    			if(box_start[i][j].num != i * 10 + j)
    			{
    				// 搜寻
    				for(x = 0; x < length_side * 2; x++)
    				{
    					for(y = 0; y < length_side; y++)
    					{
    						if(box_start[x][y].num == i * 10 + j)
    						{
    							// 交换
    							exchange(i, j, x, y);
    						}
    					}
    				}
    			}
    		}
    	}
    	// 绘制
    	draw();
    }
    
    // 判定函数
    void Gary::end()
    {
    	int i, j, k;
    	k = 0;
    	// 左侧是否填满
    	for(i = length_side; i < length_side * 2; i++)
    	{
    		for(j = 0; j < length_side; j++)
    		{
    			if(box_start[i][j].num_type == 1)
    			{
    				k++;
    			}
    		}
    	}
    	// 填满则结束
    	if(k == length_side * length_side)
    	{
    		exit_move = 1;
    		draw();
    		MessageBox(hOut, _T("成功了"), _T("来自小豆子的提醒"), MB_OK);
    	}
    
    }
    
    // 时间更新
    void Gary::time_new()
    {
    	// 文字绘制
    	settextstyle(30, 0, _T("Consolas"));
    	settextcolor(BLACK);
    	// 时间更新
    	start_t2 = clock();
    	if(( start_t2 - start_t1 ) / 1000 != num_time)
    	{
    		num_time = ( start_t2 - start_t1 ) / 1000;
    		setbkcolor(WHITE);
    		_stprintf_s(s, _T("%0.1d"), num_time);
    		outtextxy(380, 415, s);
    		FlushBatchDraw();
    	}
    }
    
    // 交换函数
    void Gary::exchange(int i, int j, int x, int y)
    {
    	int k;
    	// 四邻的值
    	// 上
    	k = box_start[i][j].num_top;
    	box_start[i][j].num_top = box_start[x][y].num_top;
    	box_start[x][y].num_top = k;
    	// 下
    	k = box_start[i][j].num_bottom;
    	box_start[i][j].num_bottom = box_start[x][y].num_bottom;
    	box_start[x][y].num_bottom = k;
    	// 左
    	k = box_start[i][j].num_left;
    	box_start[i][j].num_left = box_start[x][y].num_left;
    	box_start[x][y].num_left = k;
    	// 右
    	k = box_start[i][j].num_right;
    	box_start[i][j].num_right = box_start[x][y].num_right;
    	box_start[x][y].num_right = k;
    	// 状态
    	k = box_start[i][j].num_type;
    	box_start[i][j].num_type = box_start[x][y].num_type;
    	box_start[x][y].num_type = k;
    	// 初始位置
    	k = box_start[i][j].num;
    	box_start[i][j].num = box_start[x][y].num;
    	box_start[x][y].num = k;
    }
    
    // 检测函数
    void Gary::check(int i, int j, int x, int y)
    {
    	if(
    		// 新二格检查
    		// 新二格是否在左侧
    		( x >= length_side && (
    			// 顶部检查
    			( y > 0 && box_start[x][y - 1].num_type != 0 && box_start[x][y].num_top != box_start[x][y - 1].num_bottom ) ||
    			// 底部
    			( y < length_side - 1 && box_start[x][y + 1].num_type != 0 && box_start[x][y].num_bottom != box_start[x][y + 1].num_top ) ||
    			// 左侧
    			( x > length_side && box_start[x - 1][y].num_type != 0 && box_start[x][y].num_left != box_start[x - 1][y].num_right ) ||
    			// 右侧
    			( x < length_side * 2 - 1 && box_start[x + 1][y].num_type != 0 && box_start[x][y].num_right != box_start[x + 1][y].num_left ) ) )
    		||
    		// 新一格检查		
    		// 新一格是否在左侧，且不为空，即原二格不为空
    		( i >= length_side && box_start[i][j].num_type == 1 && (
    			// 顶部检查
    			( j > 0 && box_start[i][j - 1].num_type != 0 && box_start[i][j].num_top != box_start[i][j - 1].num_bottom ) ||
    			// 底部
    			( j < length_side - 1 && box_start[i][j + 1].num_type != 0 && box_start[i][j].num_bottom != box_start[i][j + 1].num_top ) ||
    			// 左侧
    			( i > length_side && box_start[i - 1][j].num_type != 0 && box_start[i][j].num_left != box_start[i - 1][j].num_right ) ||
    			// 右侧
    			( i < length_side * 2 - 1 && box_start[i + 1][j].num_type != 0 && box_start[i][j].num_right != box_start[i + 1][j].num_left ) ) ))
    	{
    		// 换回来
    		exchange(i, j, x, y);
    	}
    }
    
    // 绘制函数
    void Gary::draw()
    {
    	int i, j;
    	POINT p[3];
    	// 字体
    	settextstyle(21 + 5 * int(pow(2.0, 5 - length_side)), 0, _T("Consolas"));
    	settextcolor(BLACK);
    	setlinecolor(BLACK);
    	// 绘制
    	for(j = 0; j < length_side; j++)
    	{
    		for(i = 0; i < length_side * 2; i++)
    		{
    			// 不是空格
    			if(box_start[i][j].num_type == 1)
    			{
    				// 顶部
    				setfillcolor(color_num[box_start[i][j].num_top]);
    				p[0] = box_start[i][j].pos[0]; p[1] = box_start[i][j].pos[1]; p[2] = box_start[i][j].pos[4];
    				fillpolygon(p, 3);
    				setbkcolor(color_num[box_start[i][j].num_top]);
    				_stprintf_s(s, _T("%0d"), box_start[i][j].num_top);
    				outtextxy(box_start[i][j].pos[0].x + 180 / length_side - ( length_side >= 4 ? 5 : 10 ),
    					box_start[i][j].pos[0].y + 180 / length_side - ( 7 * ( 10 - length_side ) + ( length_side == 2 ? 24 : 0 ) ), s);
    
    				// 底部
    				setfillcolor(color_num[box_start[i][j].num_bottom]);
    				p[0] = box_start[i][j].pos[2]; p[1] = box_start[i][j].pos[3]; p[2] = box_start[i][j].pos[4];
    				fillpolygon(p, 3);
    				setbkcolor(color_num[box_start[i][j].num_bottom]);
    				_stprintf_s(s, _T("%0d"), box_start[i][j].num_bottom);
    				outtextxy(box_start[i][j].pos[0].x + 180 / length_side - ( length_side >= 4 ? 5 : 10 ),
    					box_start[i][j].pos[0].y + 180 / length_side + ( 7 + ( 6 - length_side ) / 3 * 2 + ( length_side <= 3 ? 10 : 0 ) + ( length_side == 4 ? 5 : 0 ) ), s);
    
    				// 左侧
    				setfillcolor(color_num[box_start[i][j].num_left]);
    				p[0] = box_start[i][j].pos[0]; p[1] = box_start[i][j].pos[3]; p[2] = box_start[i][j].pos[4];
    				fillpolygon(p, 3);
    				setbkcolor(color_num[box_start[i][j].num_left]);
    				_stprintf_s(s, _T("%0d"), box_start[i][j].num_left);
    				outtextxy(box_start[i][j].pos[0].x + 180 / length_side - ( 5 * ( 11 - length_side ) + ( length_side == 3 ? 10 : 0 ) + ( length_side == 2 ? 25 : 0 ) ),
    					box_start[i][j].pos[0].y + 180 / length_side - ( ( length_side >= 5 ? ( 8 - length_side ) * 5 : ( 2 + int(pow(2.0, 4 - length_side)) ) * 5 ) ), s);
    				// 右侧
    				setfillcolor(color_num[box_start[i][j].num_right]);
    				p[0] = box_start[i][j].pos[1]; p[1] = box_start[i][j].pos[2]; p[2] = box_start[i][j].pos[4];
    				fillpolygon(p, 3);
    				setbkcolor(color_num[box_start[i][j].num_right]);
    				_stprintf_s(s, _T("%0d"), box_start[i][j].num_right);
    				outtextxy(box_start[i][j].pos[0].x + 180 / length_side + ( length_side <= 4 ? 10 * ( 6 - length_side ) : ( 11 - length_side ) * 3 ) + ( length_side == 4 ? 5 : 0 ),
    					box_start[i][j].pos[0].y + 180 / length_side - ( ( length_side >= 5 ? ( 8 - length_side ) * 5 : ( 2 + int(pow(2.0, 4 - length_side)) ) * 5 ) ), s);
    			}
    			// 空格子
    			else
    			{
    				setfillcolor(RGB(230, 230, 230));
    				setlinecolor(BLACK);
    				fillrectangle(box_start[i][j].pos[0].x, box_start[i][j].pos[0].y, box_start[i][j].pos[2].x, box_start[i][j].pos[2].y);
    			}
    		}
    	}
    
    	// 箭头
    	setfillcolor(RGB(230, 230, 230));
    	setlinecolor(WHITE);
    	p[0].x = 415; p[0].y = 20;
    	p[1].x = 415; p[1].y = 380;
    	p[2].x = 385; p[2].y = 200;
    	fillpolygon(p, 3);
    
    	// 按钮
    	settextstyle(25, 0, _T("Consolas"));
    	settextcolor(BLACK);
    	setbkcolor(RGB(220, 220, 220));
    	setfillcolor(RGB(220, 220, 220));
    	setlinecolor(WHITE);
    	for(i = 0; i < 6; i++)
    	{
    		// 边框
    		fillrectangle(box_button[i].posx1, box_button[i].posy1, box_button[i].posx2, box_button[i].posy2);
    		outtextxy(box_button[i].posx1 + 10, box_button[i].posy1 + 15, box_button[i].text);
    	}
    
    	// 已选中的格子
    	if(num_click != -1)
    	{
    		i = num_click % 15;
    		j = num_click / 15;
    		setlinecolor(WHITE);
    		setlinestyle(PS_SOLID, 6);
    		rectangle(box_start[i][j].pos[0].x, box_start[i][j].pos[0].y, box_start[i][j].pos[2].x, box_start[i][j].pos[2].y);
    		setlinestyle(PS_SOLID, 1);
    		setlinecolor(BLACK);
    		rectangle(box_start[i][j].pos[0].x + 3, box_start[i][j].pos[0].y + 3, box_start[i][j].pos[2].x - 3, box_start[i][j].pos[2].y - 3);
    	}
    	FlushBatchDraw();
    }
    
    // 提示函数
    void Gary::tip()
    {
    	int i, j, k, x, y, t;
    	// 提示数加一
    	num_tip++;
    	// 提示满了
    	if(num_tip == length_side * length_side)
    	{
    		MessageBox(hOut, _T("仅剩一块了"), _T("来自小豆子的提醒"), MB_OK);
    		num_tip--;
    		start_t1 = clock() - 1000 * num_time;
    	}
    	else
    	{
    		// 左移
    		for(i = length_side; i < length_side * 2; i++)
    		{
    			for(j = 0; j < length_side; j++)
    			{
    				// 右格不为空
    				if(box_start[i][j].num_type == 1)
    				{
    					for(x = 0; x < length_side; x++)
    					{
    						for(y = 0; y < length_side; y++)
    						{
    							// 左格为空
    							if(box_start[x][y].num_type == 0)
    							{
    								// 交换
    								exchange(i, j, x, y);
    							}
    						}
    					}
    				}
    			}
    		}
    		// 根据提示数右移
    		for(k = 0; k < num_tip; k++)
    		{
    			// zigzag 算法进行提示，3 次提示即可以确定一个角
    			t = k;
    			i = length_side;
    			j = length_side;
    			// 确定提示位置
    			while(i >= length_side || j >= length_side || box_start[i + length_side][j].num_type == 1)
    			{
    				for(i = 0; i < length_side * 2; i++)
    				{
    					if(i * ( i + 1 ) / 2 > t) { i--; break; }
    				}
    				j = t - i * ( i + 1 ) / 2;
    				i = i - j;
    				if(i >= length_side || j >= length_side || box_start[i + length_side][j].num_type == 1) { t++; }
    			}
    			// 搜寻
    			for(x = 0; x < length_side; x++)
    			{
    				for(y = 0; y < length_side; y++)
    				{
    					// 左格初始位置满足
    					if(box_start[x][y].num == i * 10 + j)
    					{
    						// 交换
    						exchange(i + length_side, j, x, y);
    					}
    				}
    			}
    		}
    	}
    	// 绘制
    	draw();
    }
    
    // 初始化函数
    void Gary::initialization()
    {
    	// 无选中
    	num_click = -1;
    	// 无提示
    	num_tip = 0;
    	// 无时间
    	num_time = -1;
    
    	setbkcolor(WHITE);
    	cleardevice();
    	srand((unsigned)time(NULL));
    	setlinecolor(BLACK);
    
    	// 颜色
    	color_num[0] = RGB(255, 255, 255);
    	color_num[1] = RGB(193, 125, 17);
    	color_num[2] = RGB(204, 0, 0);
    	color_num[3] = RGB(245, 121, 0);
    	color_num[4] = RGB(237, 212, 0);
    	color_num[5] = RGB(115, 210, 22);
    	color_num[6] = RGB(52, 101, 164);
    	color_num[7] = RGB(117, 80, 123);
    	color_num[8] = RGB(185, 185, 185);
    	color_num[9] = RGB(0, 245, 245);
    
    	int i, j, k;
    	// 格子
    	for(j = 0; j < length_side; j++)
    	{
    		// 左右两侧
    		for(i = 0; i < 2 * length_side; i++)
    		{
    			// 初始位置
    			box_start[i][j].num = i * 10 + j;
    			// 中心点
    			box_start[i][j].pos[0].x = 420 - 400 * ( i / length_side ) + ( 360 / length_side ) * ( i % length_side );
    			box_start[i][j].pos[0].y = 20 + ( 360 / length_side ) * j;
    			// 四个角
    			box_start[i][j].pos[1].x = box_start[i][j].pos[0].x + ( 360 / length_side );
    			box_start[i][j].pos[1].y = box_start[i][j].pos[0].y;
    			box_start[i][j].pos[2].x = box_start[i][j].pos[0].x + ( 360 / length_side );
    			box_start[i][j].pos[2].y = box_start[i][j].pos[0].y + ( 360 / length_side );
    			box_start[i][j].pos[3].x = box_start[i][j].pos[0].x;
    			box_start[i][j].pos[3].y = box_start[i][j].pos[0].y + ( 360 / length_side );
    			box_start[i][j].pos[4].x = box_start[i][j].pos[0].x + ( 180 / length_side );
    			box_start[i][j].pos[4].y = box_start[i][j].pos[0].y + ( 180 / length_side );
    
    			// 右侧随机生成
    			if(i < length_side)
    			{
    				// 顶部
    				if(j == 0) { box_start[i][j].num_top = rand() % num_color; }
    				else { box_start[i][j].num_top = box_start[i][j - 1].num_bottom; }
    				// 底部
    				box_start[i][j].num_bottom = rand() % num_color;
    				// 左侧
    				if(i == 0) { box_start[i][j].num_left = rand() % num_color; }
    				else { box_start[i][j].num_left = box_start[i - 1][j].num_right; }
    				// 右侧
    				box_start[i][j].num_right = rand() % num_color;
    				// 状态
    				box_start[i][j].num_type = 1;
    			}
    			// 左侧为空
    			else
    			{
    				box_start[i][j].num_type = 0;
    			}
    		}
    	}
    
    	// 打乱
    	for(k = 0; k < length_side * length_side; k++)
    	{
    		i = rand() % ( length_side * length_side );
    		exchange(k % length_side, k / length_side, i % length_side, i / length_side);
    	}
    
    	// 按钮
    	box_button[0].posx1 = 20; box_button[0].posy1 = 400; box_button[0].text = _T("新开");
    	box_button[0].posx2 = box_button[0].posx1 + 65; box_button[0].posy2 = box_button[0].posy1 + 60;
    
    	box_button[1].posx1 = 140; box_button[1].posy1 = 400; box_button[1].text = _T("复原");
    	box_button[1].posx2 = box_button[1].posx1 + 65; box_button[1].posy2 = box_button[1].posy1 + 60;
    
    	box_button[2].posx1 = 260; box_button[2].posy1 = 400; box_button[2].text = _T("提示");
    	box_button[2].posx2 = box_button[2].posx1 + 65; box_button[2].posy2 = box_button[2].posy1 + 60;
    
    	box_button[3].posx1 = 475; box_button[3].posy1 = 400; box_button[3].text = _T("边长");
    	box_button[3].posx2 = box_button[3].posx1 + 65; box_button[3].posy2 = box_button[3].posy1 + 60;
    
    	box_button[4].posx1 = 595; box_button[4].posy1 = 400; box_button[4].text = _T("颜色");
    	box_button[4].posx2 = box_button[4].posx1 + 65; box_button[4].posy2 = box_button[4].posy1 + 60;
    
    	box_button[5].posx1 = 715; box_button[5].posy1 = 400; box_button[5].text = _T("退出");
    	box_button[5].posx2 = box_button[5].posx1 + 65; box_button[5].posy2 = box_button[5].posy1 + 60;
    	// 绘制
    	draw();
    	// 时间重置
    	start_t1 = clock();
    	start_t2 = clock();
    
    }
    
    // 窗口主视角函数
    void Gary::move()
    {
    	int i, j, k, x, y;
    	// 主视角控量
    	exit_move = 0;
    	// 主视角
    	while(exit_move == 0)
    	{
    		// 时间
    		time_new();
    		if(peekmessage(&m, EM_MOUSE | EM_KEY))
    		{
    			// 左键单击判断
    			if(m.message == WM_LBUTTONDOWN)
    			{
    				i = ( m.x > 420 ? 1 : 0 ) * ( m.x - 420 ) / ( 360 / length_side ) + ( m.x < 380 ? 1 : 0 ) * ( ( m.x - 20 ) / ( 360 / length_side ) + length_side );
    				j = ( m.y - 20 ) / ( 360 / length_side );
    				// 点击格子
    				if(( ( m.x > 420 && m.x < 780 ) || ( m.x > 20 && m.x < 380 ) ) && m.y < 380 && m.y>20 && box_start[i][j].num_type == 1)
    				{
    					// 记录
    					num_click = i + j * 15;
    					// 绘制
    					draw();
    					// 二次点击
    					while(true)
    					{
    						time_new();
    						if(peekmessage(&m, EM_MOUSE | EM_KEY))
    						{
    							// 左键单击判断
    							if(m.message == WM_LBUTTONDOWN)
    							{
    								// 点击格子
    								if(( ( m.x > 420 && m.x < 780 ) || ( m.x > 20 && m.x < 380 ) ) && m.y < 380 && m.y>20)
    								{
    									x = ( m.x > 420 ? 1 : 0 ) * ( m.x - 420 ) / ( 360 / length_side ) + ( m.x < 380 ? 1 : 0 ) * ( ( m.x - 20 ) / ( 360 / length_side ) + length_side );
    									y = ( m.y - 20 ) / ( 360 / length_side );
    									break;
    								}
    								// 判断是否点击了按钮
    								else
    								{
    									for(k = 0; k < 6; k++)
    									{
    										// 矩形按钮
    										if(m.x > box_button[k].posx1 && m.y > box_button[k].posy1 && m.x < box_button[k].posx2 && m.y < box_button[k].posy2)
    										{
    											// 记录
    											num_click = -1;
    											// 绘制
    											draw();
    											// 返回
    											goto A;
    										}
    									}
    								}
    							}
    						}
    					}
    					// 记录
    					num_click = -1;
    					// 两次点击
    					if(i != x || j != y)
    					{
    						// 交换
    						exchange(i, j, x, y);
    						// 检查
    						check(i, j, x, y);
    						// 判定
    						end();
    					}
    					// 绘制
    					draw();
    				}
    				// 判断是否点击了按钮
    				else
    				{
    					for(k = 0; k < 6; k++)
    					{
    						// 矩形按钮
    						if(m.x > box_button[k].posx1 && m.y > box_button[k].posy1 && m.x < box_button[k].posx2 && m.y < box_button[k].posy2)
    						{
    							break;
    						}
    					}
    				A:
    					switch(k)
    					{
    						// 新开
    					case 0:exit_move = 1; break;
    						// 复原
    					case 1:solve(); break;
    						// 提示
    					case 2:tip(); break;
    						// 边长
    					case 3:
    					{
    						InputBox(s, 10, _T("输入边长(2 ~ 6)"));
    						_stscanf_s(s, _T("%d"), &i);
    						if(i >= 2 && i <= 6)
    						{
    							length_side = int(i);
    							exit_move = 1;
    						}
    						else
    						{
    							MessageBox(hOut, _T("输入错误，不在范围内"), _T("来自小豆子的提醒"), MB_OK);
    							start_t1 = clock() - 1000 * num_time;
    						}
    						break;
    					}
    					// 颜色
    					case 4:
    					{
    						InputBox(s, 10, _T("输入颜色(2 ~ 10)"));
    						_stscanf_s(s, _T("%d"), &i);
    						if(i >= 2 && i <= 10)
    						{
    							num_color = int(i);
    							exit_move = 1;
    						}
    						else
    						{
    							MessageBox(hOut, _T("输入错误，不在范围内"), _T("来自小豆子的提醒"), MB_OK);
    							start_t1 = clock() - 1000 * num_time;
    						}	
    						break;
    					}
    					// 退出
    					case 5:exit_move = 1; exit_carry = 1; break;
    					default:break;
    					}
    				}
    			}
    		}
    	}
    }
    
    // 主进程
    void Gary::carry()
    {
    	// 窗口定义
    	hOut = initgraph(800, 480);
    	SetWindowText(hOut, _T("四邻"));
    	// 进程控制
    	exit_carry = 0;
    	BeginBatchDraw();
    	// 初始难度设定，边长 4，颜色 10
    	length_side = 4;
    	num_color = 10;
    	while(exit_carry == 0)
    	{
    		initialization();
    		move();
    	}
    	EndBatchDraw();
    	closegraph();
    }
    
    // 主函数
    int main(void)
    {
    	Gary G;
    	G.carry();
    	return 0;
    }

# 参考资料

<https://www.bilibili.com/video/BV1JJ41177Wz/>

[游戏](/t/%E6%B8%B8%E6%88%8F)

![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/gary/a/four-neighbors-game\))

[取消回复](javascript:void\(0\);)

![花毛茛](//thirdqq.qlogo.cn/ek_qqapp/AQXpcnejhliaiaXxSszONt3TLmbPwYV6atiaOPkLqicVd1XRZou0hUYtGxOfWP9x24eGTDdZ8FlN/0)

[花毛茛](/gary/)

  * [__](/gary/ "主页")
  * [__](https://i.codebus.cn/user/1e510a86-45ce-5d02-167f-d7df8473f01a "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=983962708 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=983962708&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=1e510a86-45ce-5d02-167f-d7df8473f01a "添加好友")



搜索

#### 标签云

  * [绘图](/gary/t/%E7%BB%98%E5%9B%BE)
  * [物理模拟](/gary/t/%E7%89%A9%E7%90%86%E6%A8%A1%E6%8B%9F)
  * [图像处理](/gary/t/%E5%9B%BE%E5%83%8F%E5%A4%84%E7%90%86)
  * [图形学与图像学](/gary/t/%E5%9B%BE%E5%BD%A2%E5%AD%A6%E4%B8%8E%E5%9B%BE%E5%83%8F%E5%AD%A6)
  * [分形学](/gary/t/%E5%88%86%E5%BD%A2%E5%AD%A6)
  * [游戏](/gary/t/%E6%B8%B8%E6%88%8F)
  * [算法](/gary/t/%E7%AE%97%E6%B3%95)



广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
