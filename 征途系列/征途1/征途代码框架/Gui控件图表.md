[TOC]
# Gui大全
征途使用到的UI控件总的如下：
*Button,Static,CheckBox,RadioButton,ComboBox,Slider,EditBox,ImeEditBox,ListBox,ScrollBar,Element,Image,Dialog,ItemCell,Table,Tab,Process,ScrollTab,MLTextBox,Tree,DataListView*等。
## GuiControl
- Control是一个基类，UI控件都继承，能通过GetControl(ID)查找到任意的UI控件。变量m_bDesignMode可以控制拖拽和改变大小。有传递消息的MsgProc。能够获取一下基本的信息，比如继承这个类的UI控件的高，宽，在游戏界面中的位置，设置打开的热键等等。
## GuiButton
- 使用非常广泛的按钮。
- 以下是常用的API：(还有继承而来的SetVisible，由于过于基础，不再添加)

函数名| 参数 | 返回类型 | 作用
------ | ----- | ----------| -----
SetEnabel | 无 | void | 设置是否响应

- 按钮主要的处理回调写在OnGuiEvent里面，对点击进行响应。
## GuiComboBox
- Gui中的组合框，组合框或者有鼠标上下拉动，滑动的，都是有HandleMouse等监听鼠标操作的。Win的库里面，竟然包含了MOUSEWHEEL,LBUTTONUP等的宏，很方便。
- 经常使用到的API：

 函数名  | 参数 | 返回类型 | 作用 
--------- | ---- | --------- | -------
RemoveAllItems | 无 | void | 清空所有单元
AddItem | const char* ,void* ,DWORD | void | 新建一个单元
GetSelectedData | 无 | void* | 返回你选择的内容的指针
SetCurItem | int | void | 设置当前的选择单元

## GuiScrollPanel
- 此UI包括了滑动面板，滚动条。ScrollPanel的Render函数，已经规避掉了很多可能会引起宕机的问题。无效的管理控件，移动滚动条，对应的控件也移动等。
- 一般来说，ScrollPanel也带有ScrollBar。
- 提到了挂钩，又忘了，挂钩的作用是Windows的消息处理机制，可以监听应用程序或者特定事件。可以在目标窗口处理函数之前处理这个消息。
- 经常使用到的API：（题外，有一个GetControlPanel可以用）

函数名| 参数 | 返回类型 | 作用
------ | ----- | ----------| -----
RemoveInValidControl | 无 | void | 移除无效的控件 
AddControl | DWORD dwFrom,DWORD dwTo | void | 连续添加控件（一个ID范围）
AddControlPre | DWORD dwID | void | 添加单个控件
GetScorllBar | 无 | CGuiScroellBar* | 返回滚动条 |

## GuiScrollBar
- 有必要跟上面的ScrollPanel联系起来，因为是绑定的。
- 在初始化的时候，经常先获取滑动条的最小值，GetRangeMin，然后在SetValue，不能一上来就设置为0。ScrollBar中滚动情况的监听是渲染处理。
- 经常使用的API：
    
函数名| 参数 | 返回类型 | 作用
------ | ----- | ----------| -----
SetValue | int | void | 设置滚动条的值（应该是位置）
GetRangeMax | 无 | int | 返回最大值（最小值同理）

## GuiItemCell（继承 CGuiImage）
- 这个Gui大致与GuiImage一样，主要是配合GuiTable一起使用，组合成为物品框。具有物品图片的偏移m_PtOffset，m_Pos,m_Size等属性。当鼠标移动到上面时是否需要变色。

## GuiTable（前置Gui：GuiItemCell）
- Table就是游戏中的格子，分为单个格子和二维格子。
- 格子有格子类型m_iTableType，还有有格子的二维数字（格子的序列号）和格子里面藏的物品（m_listItem）
- 常用的API：

函数名| 参数 | 返回类型 | 作用
------ | ----- | ----------| -----
AddTable | int id,int x,int y,int cellWidth,int cellHeight,int colCount,int rowCount,int showRowCount,bool bVirtual,CGuiTable **ppCreated | HRESULT(是否成功，一个封装的宏，没仔细看)  | 添加二维的格子
SetScale | stPointF | void | 设置缩放比
SetSize | int width,int height | void | 设置格子大小
PopItem | CGuiItemCell* | void | 弹出物品图片
PushItem | CGuiItemCell* | void | 压入物品图片
GetRectItem | const RECT* ,CGuiItemCell** ,int | int | 获得这个区域的物品，返回数量
AddItem | CGuiItemCell* | bool | 加入物品图片，内部调用pushItem

- 由于过于底层，有空可以再研究，会用即可，具体使用可以到具体的Dialog文件里面去学习。
- 如果想要学习，可以去参考寄存包裹，CommonStorageBag.cpp里面。
- 是这样的，每个格子都需要去command.h里面进行定义，然后在RoleItem.cpp里面的MoveTo进行你点击的位置的dwLocation进行判定，然后分项处理。在判定能够安全交换之后，会向服务器发送交换的命令Cmd，然后服务器返回，但是自己凭空创建的，服务器不给
反馈，所以这里暂时不去管它。

## GuiTab
- 选项卡，比较重要的一个UI，也属于复合UI。但是由于年代问题，UI也有他的不适应性，我们现在的选项卡内容，都是手动添加进去的，因为会有美观和老旧（按钮按下会有背景覆盖）。
- 数据结构：int m_nSelected选择的选项，m_nMouseOver鼠标移动到的选项，m_items选项单。
- 常用的API：
函数名| 参数 | 返回类型 | 作用
------ | ----- | ----------| -----
AddControl2Tab | CGuiTab* tab,int frame,int id | bool | 添加进选项框（继承自CGuiDialog）

## GuiListBox
- 列表框，就是简单的下拉，选择一个单元进行处理。每一个数据单元，都是GuiListBoxItem，也算一个单独的Gui类型。单元里面存储的东西可以是各种东西比如DWORD ID什么的。（额外解释一个IBitmaps，这是由一张或者多张图片组成的动画序列帧）
- 常用的API：

函数名| 参数 | 返回类型 | 作用
------ | ----- | ----------| -----
AddItem | const char* szText,void *pData,DWORD color | void | 添加一个单元
GetCurItem | 空 | int | 获得当前单元，返回单元编号
RemoveAllList | 空 | void | 清空当前的所有单元
GetItem | int | CGuiListBoxItem* | 根据序号返回对应单元（一般与上面的GetCurItem一起用）
GetSelectedItem | 空 | GuiListBoxItem* | 直接返回选择的单元（上面的一体化版本）


## GuiEditorBox
- 单行编辑框
- 常用的API：
函数名| 参数 | 返回类型 | 作用
------ | ----- | ----------| -----
ClearText | 无 | void | 清空字符串
SetText | LPCWSTR wszText,bool bSelected,DWORD* pData | void | 设置对应的文字

## QQPanel
- 这是历史遗留问题，现在已经不支持QQPanelUI控件，所以如果想要修改控件里面的内容，直接去.xml配置文件里面修改。



# GuiManager相关宏定义
|宏定义|描述|
|----|----|
|`Dlg_Action_0(DlgClass, dwDlgID, FuncName)`|0个参数的直接执行DlgClass内方法FuncName|
|`Dlg_Action_1(DlgClass, dwDlgID, FuncName, param1)`|1个参数的直接执行DlgClass内方法FuncName|
|`Dlg_Action_2(DlgClass, dwDlgID, FuncName, param1, param2)`|2个参数的直接执行DlgClass内方法FuncName|
- 后面还有一个三个参数的，依次类推。
- 这么多的参数，为什么不用不定长度模版函数，或者其他的方法。

# Gui后续补充内容
- **在使用GetControl获取控件时，如果同ID的控件有多个，是无法获取到你需要的那个控件的。所以控件的UI不能重复。**
- 有很多gui的细节处理，比如文字的颜色，大小等。在代码中是没有注释的，而且有的是使用继承部分的函数，比较难找。比如按钮的设置文字颜色就是用的setTextColor()
- 快捷键的处理AccelKey,思路是用一个static 数组来存放需要的所有快捷键。然后在初始化的时候把这些push_back进一个vector。检测的部分就是遍历这个vector，数字直接用ASCLL码判断,按键是否down，用Engine_GetState()->GetKeyState(VK_MENU)判定即可。
- 有快捷键处理，相应的也有鼠标的处理。鼠标的样子是我们的图标替代的。直接在Render部分替换。（这个问题我还真没考虑过，通过获取鼠标的位置，然后在鼠标位置绘制一个image替代覆盖）。**不管是鼠标还是键盘的处理，都是在game的调用MsgProc进行分配的。**鼠标的事件无非就是左右中建，然后设置相应的bool值。具体的其他处理，通过获取这个bool值，来具体处理。**windef和win32自带的HWND指针类型，句柄能在MsgProc检测到鼠标的移动，进入和离开Dialog等事件**
- 用location还是position，现在发现还是location更加准确。
- 这里有一个获取Gui子节点的一系列函数，为什么不用child命名。感觉这一系列多余，因为都没看到哪里使用过这些函数。
- 实际上，发现很多的基类函数都没有实际上引用。也有可能暂时还没用到，为了以后的扩展。
- **一定要想办法增长自身能力，一直写这种代码，能力是不会提高的。**
- **写代码一定要有注释。**
- image实际上可以由程序调整，就是image的种类，如果设置为1，则可以调整xml中的rect的后两个参数进行一定的缩放。