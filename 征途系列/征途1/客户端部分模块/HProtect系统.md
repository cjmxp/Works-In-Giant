# HProtect系统
主要是使用HP的SDK，C++导入相关的库代码为#pragma comment(lib,"HProtect");__declsper(dllexprot)表示从库中导出相关的函数，数据，成员等。
在HProtocterSDK.h头文件中，有很多的__declsper(dllexport) VOID WINAPI HPSetHwnd(HWND hWnd);这种导出函数，可以用宏定义代替这个开头。