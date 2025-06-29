这是一个免费的内核工具,请自行对驱动进行签名。若发现bug或有其他建议请联系我

支持1709系统-24H2系统

反馈群:811651711 反馈:3992383998

1.0.0.2:添加内存加载远程线程注入  添加HOOK注入

已支持功能:

1.进程:
遍历进程 遍历隐藏进程 挂起/恢复进程 结束进程 注入进程(支持被权限保护的进程注入) 进程校验
遍历线程 遍历隐藏线程 挂起/恢复线程 结束线程
遍历模块 拷贝模块内存 隐藏模块 卸载模块 提取扫描字符串
遍历内存 修改保护属性  提取内存字符串
遍历句柄 关闭句柄

2.驱动
遍历驱动 遍历隐藏驱动 遍历内存驱动(支持多块内存区域) 拷贝驱动模块 拷贝内核内存 查看IRP函数 提取驱动字符串 驱动加载与卸载痕迹 驱动校验

3.钩子
遍历SSDT,Inline,Iat钩子  恢复Inline,Iat钩子
遍历ShaowSSDT,Inline,Iat钩子 恢复Inline,Iat钩子
遍历Object对象钩子 恢复Object对象钩子
遍历进程钩子Inline,Iat,Eat钩子 恢复Inline,Iat,Eat钩子
遍历驱动钩子Inline,Iat,Eat钩子 恢复Inline,Iat,Eat钩子

4.内核层
系统回调遍历(进程,线程,模块,注册表,关机回调,蓝屏回调,文件回调) 系统回调删除(进程,线程,模块,注册表,关机回调,蓝屏回调,文件回调)
DPC定时器遍历 DPC定时器删除
IO定时器开启 IO定时器停止 IO定时器删除  IO定时器校验
系统线程遍历 系统线程挂起 系统线程恢复 系统线程结束 系统线程校验
工作线程遍历 工作线程挂起
设备树遍历 Attach设备删除
扫描内核内存 
Nmi遍历  Nmi删除

5.监控
监控内核ShellCode运行(模糊定位运行中无模块驱动)
监控进程创建  线程创建行为 监控注入操作
监控驱动创建
监控注册表行为
监控网络行为
监控文件行为

6.文件
遍历MiniFilter回调

7.网络
遍历WfpCallBack
遍历WfpFilter
遍历连接中的网络


![1进程扫描](https://github.com/user-attachments/assets/bed055b8-4549-42e7-bf88-c65e38b34a9d)

![2进程字符串扫描](https://github.com/user-attachments/assets/6629443b-9271-46cb-9589-9459093b167b)

![3进程注入](https://github.com/user-attachments/assets/05c234fe-7d7a-4af0-b267-5dc0b611d04d)

![4驱动扫描](https://github.com/user-attachments/assets/db6a0971-abab-497d-a895-ae4c1f582863)

![5提取驱动字符串](https://github.com/user-attachments/assets/de8673b2-c1c6-4b19-9169-d7eb404c81d3)

![6驱动加载与卸载查看](https://github.com/user-attachments/assets/b82e25ca-9d45-4515-9405-39753a544513)

![7扫驱动内存字符串](https://github.com/user-attachments/assets/bb54f67c-59fb-467f-a6a5-bc2a672f54c1)

![8SSDT](https://github.com/user-attachments/assets/02e98b2c-7b57-454b-a6df-af14b9af3d08)

![9shaowSSDT](https://github.com/user-attachments/assets/540803f0-7d52-4745-8f13-3b3823bbb8b1)

![10ObjectHook](https://github.com/user-attachments/assets/375606d0-27f1-407d-b6ba-991b99124289)

![11进程钩子扫描](https://github.com/user-attachments/assets/2b55e63c-8e5f-4bd3-aa5d-bccb6e5d0b6a)

![12驱动钩子扫描](https://github.com/user-attachments/assets/afeff2db-4422-4449-a087-6668aa0fa429)

![13系统回调](https://github.com/user-attachments/assets/47430404-efce-42a0-aae5-e27cfbb2d52d)

![14设备树](https://github.com/user-attachments/assets/f3a7eb81-6263-4c3e-8d65-f07908fd468d)

![15Nmi扫描](https://github.com/user-attachments/assets/44358f48-9988-4eca-b8e7-d1c1e66a4177)

![16内存加载模糊定位](https://github.com/user-attachments/assets/86d62172-d860-44d0-99b1-ce668be872e9)

![17监控进程线程模块信息](https://github.com/user-attachments/assets/2229fcbe-3072-48ea-95d0-4eb3742c850e)


![18监控驱动加载](https://github.com/user-attachments/assets/8c7a443b-8b27-4a21-b833-5b3459f8b665)

![19注册表监控](https://github.com/user-attachments/assets/02984ce5-e3e6-4631-b633-cee8db612d55)


![20监控网络](https://github.com/user-attachments/assets/035fe513-4e9b-43ed-9eb2-5d2f7e6bd730)

![21监控文件操作](https://github.com/user-attachments/assets/a6976b9f-b6bc-4294-b4ff-4360fa478c5a)

![22便利minfilter](https://github.com/user-attachments/assets/00eced3f-f7da-4fb2-9dab-d14e5906b90d)


![23遍历wfp](https://github.com/user-attachments/assets/d86ef3af-3160-4ecc-a218-4d5bb6fffbac)



![24遍历wfpfilter](https://github.com/user-attachments/assets/4a15da46-6a63-4da0-bc6a-1968f2b66296)

![25监控连接中的网络](https://github.com/user-attachments/assets/2b3771e8-3feb-46ec-a2d4-2262865df8d0)
























