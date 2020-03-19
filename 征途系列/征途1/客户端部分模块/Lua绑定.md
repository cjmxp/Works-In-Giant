# Lua绑定
看到几行调用control绑定的lua函数的代码。
```C++
CGuiControl* pCtrl = this->GetControl(nID);
if(nullptr != pCtrl)
{
    std::string strLuaFunc = pCtrl->GetEventFuncInLua(nEvent);
    if(!strLuaFunc.Empty() && m_lua) // 这个m_lua是每个dialog都有的成员指针
    {
        if(m_lua->Call<bool,CGuiControl*, UINT>(strLuaFunc.c_str(),false,pCtrl,nID)
        {
            return true;
        }
    }
}
```
Call内部的实现是通过模版函数，调用luabind::call_function实现的。有好几个不同参数的模版可以调用。
本来想看lua内部的实现过程，但是比较繁杂，调用的层太多，就没有深究。