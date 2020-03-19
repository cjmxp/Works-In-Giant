# 征途UI
## 基类
1. 对话框的基类继承CGuiDialogEx，方便使用相关的API
    - 能直接添加和查找接口，AddDialog<T>,FindDialog<T>。
    - 不需要在GameGuiManager里面创建对话框指针和创建窗口函数。当然也不用OnClose时清空指针。
    - 节省了不少代码量。
2. GuiDialogEx里面的OnCreate和OnClose都是要有的（不写可以直接用基类的），缺少就会出现无法开启和无法关闭的问题。缺少OnClose的情况下，好像有关闭按钮也不会出现（如果只自带的，就不出现）。如果是自定义的关闭按钮，还是会出现，因为最初的自带关闭按钮，因为后期想要更好看的北京，想要自定的关闭按钮，现在都不用这个设计了。

## 功能实现
1. 翻页的功能跟SelectPage有关。跟当初的一样即可。但是最好跟ID的页数有关系，这样方便很多，有点类似tag。
2. ListBox是列表盒，就是可以往下翻页的，有相关的API是GetFirstShowItem。
3. 要发送消息，可以写SEND_USER_CMD的宏。
4. 想要获得相关的UI控件，最好是直接获取相关的控件，不要用GetControl，好像会出问题。比如想要图片，直接GetImage就行。
5. 还有一个Tab，许多的UI控件。
6. 有时候还要进行遍历，因为你选择了很多的按钮。这里的文本是static，不是很懂。
7. GuiDialog里面有MsgProc在监听你的鼠标位置，就是聚焦的位置，进入到CGuiStatic里面的HandleMouse进行处理，这就是鼠标的处理方式。
8. 明天过来的话，看gui3v3GraphLocal的翻页部分，看上去比较的简单。
9. UI编辑器里面分组的话，你接下来添加的UI全在这个新组里面了。
10. 翻页的UI控件叫Tab。Tab控件需要拉宽自己，才能点到其他几个页面。
11. UI编辑器能直接测试你的内容。
12. 聊天界面的学习很不错
13. 一组最多10个，10个以上就会自动新建组。
14. int转char*有自己封装的类函数可以直接用。
15. 动态加载背景：stResourceLocation rl("data\\interface2.gl",173,69")这样就能拿到对应图包的图片。然后我们就能SetBackImage(rl)设置底图背景什么的。
16. 添加一些简单的屏幕中间黄字提示，右下角滚动提示，右下角弹出提示，弹出确认框提示，都可以简单参考《客户端参考手册》的第十章具体实现节。
17. 里面的一些网格都是美术图片画好的，然后程序计算坐标，一个个SetPosition上去。
18. 游戏界面下面的菜单部分，是一开始就生成的，所以生成之后，GuiDialog打断点，打开背包，不会进入断点。（21，22解释了出处）
19. 输入账号密码登录之后，进入的选择角色界面相关代码在CreateRole，CreateRoleManager里
20. GuiPackage就是我们界面下的背包，鼠标上的物体移动到附加包裹时，相关的函数在GuiPackage.cpp中的MoveItemToPackage。有一个文件是FloatItem叫浮动物体，暂时不知道什么作用。更加主要的，包裹中的物体是在RoleItem.cpp里面。移动包裹的函数在RoleItem.cpp中的MoveTo中，是先获得你点击的dstLocation位置，然后进行相关的判断。普通的包裹放到空格子，是在if(dst.dwLocation != OBJECTCELLTYPE_EQUIP)部分代码处理。自己画的格子，放上去可以参考AutoGame.cpp中的AutoEquipFillItem的交换物体最后部分代码。
21. 下面一行菜单，点了不是重新生成Dialog，不经过Dialog的构造函数，触发的事件在GameGuiManager里面的GetGameGuiManager()->AddItem()，这是第一次打开包裹，要分第一次和后面几次，因为位置会改变。说起来，不能理解为什么背包单独写在OnOffItemDialog里面。
22. 重要的一点，就是玩家下面的菜单栏一部分，跟广告牌的UI部分不一样，拥有单击2次，能够关闭再开启的功能，但是广告牌则不行。所以下面这一栏的按钮响应事件，全是OnOff开头的函数，比如玩家信息OnOffUserInfoDialog，玩家包裹OnOffItemDialog。**但是有一点奇怪的是，我本以为这些东西可以统一写在一个地方，但是详细信息在GuiMain里面定义，背包在GameGuiManager里面定义。而且在打开之前，代码需要检查好几种玩家状态，比如吃鸡模式中，不能打开玩家详细信息。海战什么的也不行。** 
23. 界面锁定器好像能解决重复打开问题（界面锁定器不是这个功能）。解决了，FindDialog判断一下就行，但是要记得Dialog的ID要和代码中的配套。
24. 发现GuiMain前面几个头文件文档还是写的不错的。


## 消息处理
1. MsgProc是主要处理函数，需要在继承的时候重写这个东西。不过这个东西好像GuiDialogEx里面没有写，这个消息是老的处理函数。特定的处理函数变成GetEvenHandler。所以我们需要自己注册感兴趣的事件进去。
2. Gui大的复合控件，都是ParseCmd里面进行消息的接受处理。（总的消息处理在Game）