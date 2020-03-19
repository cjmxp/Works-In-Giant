[TOC]
# 动作Action
概括：动作不是之前印象中的cocos2d的动作，不太一样。是偏向TaskAction,GotoAction,CallNpcAction,ClickNpcDlgItemAction,DelayAction,WaitGuardNpcAction,EndAction,ReliveAction这样比较抽象的动作。
主要重写函数：
```C++:n
void Action::OnEnter();
void Action::OnExit();
void Action::Update();
```
## GotoAction
- 这个动作是在地图中，到达某一个指定的点。API使用为GotoAction(const char* mapName,stPointI pos,int range)，可以理解为是寻路动作。
- 这些Action都是需要AddMessages的。
## OnTransferAndVisit
- 走到指定地点，然后访问指定（附近）的NPC。
- 访问NPC调用的函数在MainCharacter_other.cpp里面。GetScene()->GetMainChatacter()->CallNpc(npc)。参数是指针。（废话）
## DelayAction
- 延时执行动作。

>更多的具体执行动作，请到具体的代码处查看。