[TOC]
# 周末BOSS副本
## 前言
涉及**寻路模块，BOSS详情界面，普通界面，复活框，右中上方悬浮按钮，地图显示NPC，分辨率，九宫格**
## 功能描述
- 增加BOSS类型，新增一个副本地图。
## 涉及代码段
- `ParseProperty7UserCnd.cpp`,`RoleItemManager.cpp`,`BossInfoNoticeDlg.cpp`,`GuiReliverDlg.cpp`,`GameScene.cpp`,`GuiWeekendBossInfoDlg.cpp`,`GuiUseWeekendBossKeyDlg.cpp`,`MainRightTopBtnMgr.cpp`,`GuiWeekendBossStartDlg.cpp`等
## 界面细节
- 一共大概有三个界面，后来在某一次日会中提到策划中有三个有能力修改xml文件，lp,ll，（还有个谁来着。。。）
- 如果控件在界面的有效范围外，会有焦点不在该界面上，无法点击按钮的问题，**注意看界面的红线响应区域**
## 地图细节
- 出地图的问题，就是一个倒计时只在魔王殿地图显示，还特地把当时的地图信息存储起来，因为不确定是否会经常更改（后来觉得没有必要），然后在`GameScene`里面的`InitMap`里面进行判断是否在地图中，发现都是直接判断的地图名称和当前的名称。顺带一提，strncmp不是很习惯这个返回值。
- 还有一个小地图上显示boss的图标，需要在`GuiLargeMap.cpp`里面进行处理`ShowSpecialMoveNpcInLargeNap(float fElapsedTime)`。（这一点让我想起了为什么当时内网小地图不显示完整的图像）
## 代码细节
- 如果需求将来有扩展的可能性，尽可能去设计可拓展的代码，但是不要用注释去隐式拓展，没有用的注释直接删掉。用version去查看改动。
## 学习部分
1. 右中上方处的按钮的一系列配置，包括按钮的周边特效，配置的时间，`OnRender`等，一些加入的文字多是来源于图片，而且最好是图片（不吹毛求疵的话，程序动态创字也行）。代码段在`RightNoticeButton.h`,`MainRightTopBtnMgr.cpp`
2. 上方的BOSS按钮里面的数据存储在`BossInfoNoticeDlg.cpp`内，如果要增加boss类型，需要在这里面加类型。注意建立的部分属于`panel`,注意控件的修改方式。
3. 复活框部分在`GuiReliveDlg.cpp`部分，逻辑应该是服务器发送死亡消息（实际上不完全），然后客户端分析死亡情况，选择添加复活框，**注意，这些比较老的窗口，都是在GameGuiManager里面有对应的指针，通过函数AddXXXXDlg()来创建。**后来发现是服务器会固定发送的玩家的状态中有字段`m_byKillBy`，在`maincharacter_other.cpp`的`Run`中会每帧检测玩家的这个状态，进而分析是否弹出复活框。**具体的指令处理在maincharacter_other.cpp**的`HandleCommand`里面的`RELIVE_USERCMD`的`MAINUSERDEATH_RELIVE_USERCMD_PARA`。死亡也是一个对象，死亡的处理在`m_DieInfo`管理器（怎么不是Mgr后缀结尾）里面进行。（管理死亡时间，播放动画，跳转地图什么的）
4. 还涉及到了寻路模块，核心函数是`GetClientActionManager()->Goto(当前地图，目标地图，目标位置x坐标，目标位置y坐标)`。