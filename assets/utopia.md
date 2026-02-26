[![](/Content/images/codebus-white.svg)](/)

# [主题雄心](/utopia/)

一个以utopia为蓝本的及时战略游戏

  * [管理](/console/posts)



##  复刻古早即时战略游戏 utopia [![金牌收录](/content/images/gold.png)](?medal=3 "金牌收录")

__2025-11-22 ~ 2025-11-26 __[YzY-huohuo](/utopia/) [ __(0)](https://codebus.cn/utopia/utopia#comment)

# 什么是 Utopia？

正如大多数百科所介绍的那样，Utopia 被认为是第一款即时战略游戏。  
本项目会沿用其机制，并对其进行改进，以更好地适应现代需求。  
当前版本为 4.3.5。

# 如何去玩？

## 目标

游戏开始时，玩家将获得 100 资金和 100 资源。

你需要合理分配资源，建造建筑与船只，最终目标是摧毁所有敌方建筑。

## 如何建造建筑

1.先点击地图右侧平原地区直至出现红框。

2.点击右侧建筑栏里你需要的建筑（当金钱不足时取消建造）。

当你看到动画时说明建造完成。

建筑作用：

民用工厂：消耗 200 可用人口在每 1000 游戏刻产生金钱。如图所示：![](/f/a/0/0/760/logo.png)

社区：提供 500 住房。如图所示：![](/f/a/0/0/760/234.png)

农场：提供 500 食物。如图所示：![](/f/a/0/0/760/lo1go.png)

船厂（须在海边建造）：当再次点击时可建造船。如图所示：![](/f/a/0/0/760/33.png)

运河：可将我方领土的平原变为河。如图所示：![](/f/a/0/0/760/444.png)

军用工厂：消耗 200 可用人口在每 1000 游戏刻产生资源。如图所示：![](/f/a/0/0/760/3.png) 

学校：提升效率。如图所示：![](/f/a/0/0/760/4.png)

碉堡：防御船只。如图所示：![](/f/a/0/0/760/log3o.png)

医院：减少污染。如图所示：![](/f/a/0/0/760/67.png)

核弹发射井：发射核弹。如图所示：![](/f/a/0/0/760/lo0go.png)

## 如何建造船只

1.点击地图上船厂（看到下方出现船只图案即为成功）。

2.点击下方想建造的船。

当看见有船出现在船厂旁即为成功。

战舰种类：

战列舰：攻击建筑。如图所示：![](/f/a/0/0/760/ss.png)

工程船：改变周围 3*3 地形。如图所示：![](/f/a/0/0/760/4567.png)

## 如何操控船只

### 移动

1.点击船只。

2.点击你想去的位置。

### 攻击

1.点击船只。

2.点击你想攻击的位置。

（不能攻击船 设定攻击目标后不会主动移动）

## 如何移动

将鼠标放到地图边缘即可。

## 特殊按键

集群攻击：

如图所示：![](/f/a/0/0/760/op7.png)

用法：点击后再单击要攻击建筑即可实现群攻。

# 设置：

在游戏开始后可以看见：![](/f/a/0/0/760/op.png)

点击后即可改变音乐和退出游戏。

# 游戏二次创作:

## 建筑

1.打开“mod\\\build”。

2.按顺序创建文件夹。

3.编写配置文件（可参考其他文件）和图像。

4.打开代码改变“loading::building_loading();”。

## 船

1.打开“mod\\\ship”。

2.按顺序创建文件夹。

3.编写配置文件（可参考其他文件）和图像。

4.打开代码改变“loading::building_loading();”。

## CG

1.打开“CG”。

2.按顺序创建文件夹。

3.编写配置文件（可写可不写）和图像（共 5 帧）

4.打开代码改变“loading::CG_loading();”。

5.编写触发位置。

## 音乐

1.打开“music”。

2.按顺序创建文件夹。

3.放音乐文件和图像。

4.打开代码改变“loading::music_loading();”。

## 参考类型：
    
    
    struct player
    {
    	double all_money = 200;
    	long long all_pollution = 0;
    	double all_xl = 1;
    	long long all_people = 0;
    	long long food = 0;
    	long long worker = 0;
    	int oillike = 0;
    };
    struct music
    {
    	IMAGE p;
    	wstring path;
    };
    struct building_template
     {
    	IMAGE map_picture[10];
    	IMAGE logo;
    	int dh_return = 1;
    	int add_food = 0;
    	int add_money = 0;
    	int cost_money = 0;
    	int add_oillike = 0;
    	int add_pollution = 0;
    	int cost_can_use_people = 0;
    	int add_people = 0;
    	double add_xl = 0;
    	int kind = 0;
    };
    struct building_canuse
     {
    	int kindof_building = 0;
    	int x = -1;
    	int y = -1;
    	int outx = -1;
    	int outy = -1;
    	int Degree = 100;
    };
    struct ship_template
    {
    	IMAGE logo;
    	IMAGE map_picture[10];
    	int dh_return = 1;
    	int walk_long = 0;
    	int arm_long = 0;
    	bool swim = true;
    	int cost_money = 0;
    
    };
    struct ship_canuse
    {
    	int kind = 0;
    	int x = -1;
    	int y = -1;
    	int dx = -1;
    	int dy = -1;
    	int Degree = 50;
    	std::vector<std::pair<int, int>> path;
    	int path_index = -1;
    	bool war = false;
    	int war_object = 0;
    };
    struct shoot
    {
    	int x1;
    	int x2;
    	int y1;
    	int y2;
    	int duration = 10;
    };
    class display_module
    {
    public:
    	int x1;
    	int x2;
    	int y1;
    	int y2;
    	int weight;
    	int height;
    	void how_weight(int weight_x1, int weight_x2)
    	{
    		x1 = min(weight_x1, weight_x2);
    		x2 = max(weight_x1, weight_x2);
    		weight = x2 - x1;
    	}
    	void how_height(int height_y1, int height_y2)
    	{
    		y1 = min(height_y1, height_y2);
    		y2 = max(height_y1, height_y2);
    		height = y2 - y1;
    	}
    };
    struct wb
    {
    	int x;
    	int y;
    };
    struct CG
    {
    	int total = 5;
    	IMAGE cgp[6];
    };
    struct cg_player
    {
    	int x;
    	int y;
    	int kind;
    	int now;
    	void set_player(int cx, int cy, int k)
    	{
    		x = cx;
    		y = cy;
    		kind = k;
    		now = 1;
    	}
    };

# 游戏界面

网络游戏尚不开放。

![](/f/a/0/0/760/strat4.png)

![](/f/a/0/0/760/2025-11-23_231638.png)

# 环境 vc2026 easyx25.9.10。

# 下载

源码太长 1800 来行且包含配置文件，遂下载观看。

项目：[项目下载](/f/a/0/0/760/utopia.zip)

[游戏](/t/%E6%B8%B8%E6%88%8F)[游戏开发](/t/%E6%B8%B8%E6%88%8F%E5%BC%80%E5%8F%91)

![Saving the comment](/Content/images/blog/ajax-loader.gif)

### 添加评论

[](https://i.codebus.cn/account/login?&returnUrl=https://codebus.cn/utopia/a/utopia\))

[取消回复](javascript:void\(0\);)

![YzY-huohuo](//thirdqq.qlogo.cn/ek_qqapp/AQTYn14nPpu8LjWgJjUIgvibhI111WaYHmunU4q5eicLdselrIkGmHFficaasQkvcw7pxnhIExQL0hmuhdJDDXrLj0r3jZMfQWmPVRYMwvm7Zo0fvP7TH0/0)

[YzY-huohuo](/utopia/)

  * [__](/utopia/ "主页")
  * [__](https://i.codebus.cn/user/b45d84d1-2484-e73f-886f-b571104d6a92 "用户信息")
  * [ __](tencent://QQInterLive/?Cmd=2&Uin=15666721599 "添加 QQ 好友.") [ __](mqqwpa://im/chat?chat_type=wpa&uin=15666721599&version=1&src_type=web&web_src=https://codebus.cn)
  * [__](https://i.codebus.cn/friend/add?fid=b45d84d1-2484-e73f-886f-b571104d6a92 "添加好友")



搜索

#### 标签云

  * [游戏](/utopia/t/%E6%B8%B8%E6%88%8F)
  * [游戏开发](/utopia/t/%E6%B8%B8%E6%88%8F%E5%BC%80%E5%8F%91)



广告

Copyright © 2026 [意在](https://easyx.tech) 投诉举报：admin@easyx.cn

![](https://cdn.easyx.cn/mpsbeian.png) [冀公网安备13010402003013](https://beian.mps.gov.cn/#/query/webSearch?code=13010402003013) [冀ICP备18009530号-3](http://beian.miit.gov.cn)

链接已经复制到剪贴板，可以直接粘贴给需要分享的好友。 
