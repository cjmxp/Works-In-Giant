# P2P
> 关键部分文件：`PatchDownload.cpp`下载资源,`PatchUpdateDlg.cpp`主界面，`SrvList.cpp`下载请求的地址,`ServerDialog.cpp`服务器列表地址，实际上是对应的不同服务器的下载地址;`OtherDialog.cpp`其他一些界面，如右上角修复按钮打开的界面;`ReportDialog.h`是用来报错的界面，用IE请求http;`PatchUpdate.cpp`整个p2p起始点。
>线程的启动应该是在`PatchUpdateDlg.cpp`里面的`unsigned int __stdcall ThreadProc(void * pParam)`
1. 启动流程：
    - (wimain.cpp)`AfxWinMain`->`AfxGetApp`拿到整个进程的`theApp`
    - `theApp`进行初始化`InitInstance`->`InitializeCef`（注意，Cef3这个是广告展示窗口使用chrome内核.apk部分，而且运维解不开这个pak包，需要在`CWinApp::InitInstance`前初始化，否则会报错）->`CWinApp::InitInstance`->`GdiplusStartup`->`dlg.DoModal`（模态对话框，焦点到界面。按道理，模态对话框应该是只能处理当前对话框，但是现在不是这样的）->创建主界面完成。（其中`m_pMainWnd`存储了窗口对象）
    - 创建完毕之后，会进行线程`PatchUpdateDlg.cpp`里的`ThreadProc`（OnInitDialog->Init->SrvflistProcess->开启线程），进行下载区服列表，也就是说下载是客户端主动请求的。由ip地址，端口port和`srvlist1.xml`与`srvlist2.xml`里面进行。这个地址是写死的，在`SrvList.cpp`开头。（srvlist是以dat后缀显示在客户端本地，解不开。。以前可以看）
    - 下载主要是通过`curl`库进行下载，然后读取的服务器列表是通过运维的`srvlist.xml`读取来的，客户端本地没有，保存在`stream`中。都是通过读取`LoadXMLFromStream`读取。
    - 下载成功后，读取了服务器列表。`ServerDialog`会进行服务器名的显示，新区的排序等。然后会保存你选择，上次选择的区的一些相关配置（登入服务器名，端口等）
    - 点击更新的区服后，会进行数据包的检查，更新之前检查是否能更新。在这之前，是有p2p判断自身是否需要更新的，会有一个步骤，但是现在干掉了，所以暂时不去管它。
    - 正式开始下载`BeginUpdate`，先删除`flist.xml`和`flist.xml`。然后会单起一个线程来加载`flist`，`LoadFlistProc`会检查`flist.xml`文件，发现已经删除（前面干的），然后给主窗口发送`LOADFLIST_COMPLETE`消息，开始进行真正的下载`mDownload->BeginDownload->DownloadProc`。最后一个函数是核心函数。与服务器建立好连接后，首先下载的`flist.xml`，服务器上的名字叫`srvflist.xml`多了一个f。这个文件`srvflist`下载完成之后,调用`LoadFlist`函数，将里面的内容保存到`mFiles`中，加载其中需要更新的文件的信息（大小和CRC校验码），加载完了之后就把`srvflist`删了。
    - 接下来会更新其他组件部分，如嘟嘟`CheckDuDuInstall`。会检查注册表中是否存在dudu，进而检查是否有dudu.exe，比较版本号，是否有在游戏中下载等。

2. 2
3. 
4. `PatchUpdateDlg.cpp`里面有一个查杀病毒木马的函数。