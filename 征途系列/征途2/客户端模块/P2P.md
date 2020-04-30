# P2P
> 关键部分文件：`PatchDownload.cpp`下载资源,`PatchUpdateDlg.cpp`主界面，`SrvList.cpp`下载请求的地址,`ServerDialog.cpp`服务器列表地址，实际上是对应的不同服务器的下载地址;`OtherDialog.cpp`其他一些界面，如右上角修复按钮打开的界面;`ReportDialog.h`是用来报错的界面，用IE请求http;`PatchUpdate.cpp`整个p2p起始点。
>线程的启动应该是在`PatchUpdateDlg.cpp`里面的`unsigned int __stdcall ThreadProc(void * pParam)`
1. 启动流程：
    - (wimain.cpp)`AfxWinMain`->`AfxGetApp`拿到整个进程的`theApp`
    - `theApp`进行初始化`InitInstance`->`InitializeCef`（注意，Cef3这个是广告展示窗口使用chrome内核.apk部分，而且运维解不开这个pak包，需要在`CWinApp::InitInstance`前初始化，否则会报错）->`CWinApp::InitInstance`->`GdiplusStartup`->`dlg.DoModal`（模态对话框，焦点到界面。按道理，模态对话框应该是只能处理当前对话框，但是现在不是这样的）->创建主界面完成。（其中`m_pMainWnd`存储了窗口对象）
    - 创建完毕之后，会进行线程`PatchUpdateDlg.cpp`里的`ThreadProc`，进行下载区服列表。由ip地址，端口port和`srvlist1.xml`与`srvlist2.xml`里面进行。这个地址是写死的。

2. 2
3. 
4. `PatchUpdateDlg.cpp`里面有一个查杀病毒木马的函数。