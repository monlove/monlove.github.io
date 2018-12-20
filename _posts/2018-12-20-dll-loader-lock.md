---
layout: post
title:  "DLL Loader Lock引起的死锁"
categories: win32
tags: dll win32
author: admin
---

* content
{:toc}
### 参考：
[https://blog.csdn.net/CJF_iceKing/article/details/40043569](https://blog.csdn.net/CJF_iceKing/article/details/40043569)

[https://www.v2ex.com/t/519019#reply24](https://www.v2ex.com/t/519019#reply24)

[https://blog.csdn.net/norsd/article/details/50409585](https://blog.csdn.net/norsd/article/details/50409585)

### bug代码重现
``` Winodws核心编程书中的20.2.5节的一个死锁例子
BOOL WINAPI DllMain(HINSTANCE hInstDll, DWORD fdwReason, PVOID fImpLoad)
{
	HANDLE hThread;
	DWORD dwThreadId;
	switch (fdwReason){
		case DLL_PROCESS_ATTACH:
			// The DLL is being mapped into the process' address space
			hThread = CreateThread(NULL, 0, SomeFuction, NULL, 0, &dwThreadId);
			WaitForSingleObject(hThread, INFINITE);
			CloseHandle(hThread);
			break;
		case DLL_THREAD_ATTACH:
			// A thread is being created
			break;
		case DLL_THREAD_DETACH:
			// A thread is exiting cleanly
			break;
		case DLL_PROCESS_DETACH:
			// The DLL is being unmapped from the process' address space
			break;
	}
	return TRUE;

```
- 总结
   - DLL尽量不要在DllMain里写逻辑，可以通过对外接口
   - dll全局对象的初始化时间是不可控的（我是指代码监控不到生命周期）,在exe/elf 里我还知道他起码在_start 之后.init 里调用或者在_WinMainCRTStartup 之后 main 之前（大概）调用，但是dll里，它是在什么地方调用的？完全没头绪
   - 强烈建议不要使任何对象实例成为全局对象，用一个工厂方法去获取唯一实例都好得多：

```
CSomeClass* getGlobalInstance(){
static CSomeClass *instance = nullptr;
if(!instance) instance = new CSomeClass{};
return instance;
}
```



