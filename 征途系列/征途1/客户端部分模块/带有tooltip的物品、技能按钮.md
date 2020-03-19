# 带有tooltip的物品、技能按钮

tooltip是当鼠标悬浮在按钮上方时，会给出该物品或技能的描述。

## 实现

- 首先在编辑器中添加按钮，选择底图\interface3.pak,35,21
- 在按钮下方添加一个图像容器作为物品/技能的边框，选择底图\interface3.pak,35,21，可以直接按原始大小，或者九宫格（推测为9slice的处理方法），LTRB均填写20
- `CGuiButton* p = GetButton(Id);`
- `#inlcude "FuncUtil.h"`
- `zt_util::SetObjectButton(p, ItemId)`
