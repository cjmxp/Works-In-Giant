# GM指令

姬旭 2018-09-17

## 概述

GM指令是游戏用于调试，GM管理等功能，在聊天窗口输入的以'//'开头的字符串。

> 正则文法：`[\s]*//[\s]*(\w+\s+)+`

## 位置

- GmCommand.h
- GmCommand.cpp
  
## 流程

- 输入内容先进入`GmCommand::Parse(const char*)`中
- 然后在`GmCommand::ProcessString(const char*, PARAMS&)` 中进行处理，去除多余的空格等内容，然后返回格式化好的字符串
- 然后根据`Register`和`Register1`注册的处理函数进行分发处理。

## 使用

如果是本地GM指令处理：我们添加一个`//add 1 2`这样的指令用来处理加法操作。

```c++
void DealGmAddCommand(const std::vector<std::string>& strlist)
{
    //这个宏用于检查参数数量是否符合函数的要求，
    //其中参数的是传入的参数。
    BEGIN_GM_COMMAND(2)
    
    //依次取出参数
    int param[2];
    int i = 0;
    for(; i < strlist.size(); ++i)
    {
        param[i] = atoi(strlist[i].c_str());
    }

    //进行操作
    int result;
    result = param[0] + param[1];
    char buf[30];
    sprintf_s(buf, "运算结果 %d", result);
    GetGameGuiManager()->AddClientSystemMessage(buf);

    //这个宏用于结束BEGIN_GM_COMMAND并在参数数量错误的时候提示用法。
    END_GM_COMMAND("//add 1 2")
}
```

然后在`GmCommand::Init()`中将函数和命令文本绑定。

```c++
Register1("Add", ::makefunctor<const std::vector<std::string>&, void>(&DealGmAddCommand));
```

销毁的操作由GmCommand自动进行处理。

