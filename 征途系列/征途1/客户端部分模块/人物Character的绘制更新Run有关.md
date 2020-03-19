# 人物Character的绘制更新Run有关
1. 主要更新函数
```C++
void CMainCharacter::Run(long time_elasped)
{
    // 记录函数使用时间等信息(不过这个东西为什么不用VS自带的性能查看器查看)
    FUCNTION_INFO(CMainCharacterRun);
    
    // 特殊处理，有一个披风和经验丹的特殊处理
    
    // 检查是否需要启动远程跟随(后面有不少是这样处理的)
    static long crgtLastTime = 0;
    if(crgtLastTime > 2000)
    {
        crgtLastTime = 0;
        CheckRestartFollowTeamLeader();
    }
    else 
    {
        crgtLastTime += time_elasped;
    }
    m_followLeaderTrigger.Update(time_elasped / 1000.0f);           // 如果有涉及到定时器TimeTrigger，要在Run中Update。单位是毫秒
    
    // 获取语音管理器，管理器->OnRun(time_elasped)
    
    // 10000毫秒检查一次是否死亡，发送stClientCallRelive消息
    
    // 吃鸡中，2s自动舔包和拾取物品
    
    // 采集时玩家时间到了，会发消息取消状态，stClearStateMapScreenUserCmd
    
    // 自动加血
    
    // 如果在龙舟中，小地图摆到中间位置
    
    // ....还有等等等
    
    // 很长的一串复活框的处理
    
    // 包含基类的run函数
}
```
