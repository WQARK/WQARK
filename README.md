# WQARK · 内核级 ARK 工具

Windows 内核级 Anti-RootKit 工具。直接读取内核内存与未公开结构，用于查看和处理系统中的隐藏对象、内核钩子、内核回调与内核 API 调用。

**支持系统**：Windows 10 1709 — Windows 11 26H1（build 28000）
**运行环境**：x64，管理员权限，驱动需自行签名
**当前版本**：1.0.0.10
**反馈群**：811651711　**反馈**：3992383998

---

## 功能

列表页统一支持点击列标题排序，行按微软签名 / 已签名 / 未签名着色，操作通过右键菜单触发。

### 进程

12 列：映像名称、进程ID、父进程ID、进程对象、应用层权限、映像路径、启动时间、会话ID、命令行、文件厂商、文件描述、备注。

进程列表由多种枚举方式交叉比对得出，结果不一致或枚举不完整的条目会标为隐藏色，并在备注列写明原因——隐藏进程由此暴露。

右键菜单：

| 操作 | 说明 |
|---|---|
| 查看进程模块 / 进程内存 / 进程线程 / 进程句柄 | 打开对应子窗口 |
| 挂起进程 / 恢复进程 / 结束进程 | 按当前挂起状态自动置灰 |
| 隐藏进程 | |
| 注入 → 远程线程注入(有模块) / 远程注入(内存加载) / HOOK注入(隐藏) | 内存加载方式不需要 DLL 落地 |
| 定位到进程文件 / 查看进程文件属性 | |
| 复制 → EPROCESS / 路径 / 名称 | |

**进程模块**：模块名、基地址、大小、模块路径、文件厂商、文件描述。支持拷贝模块内存（可选按 PE 还原后再 dump）、隐藏模块、卸载模块、反汇编、提取内存字符串。

**进程内存**：地址、大小、Protect、State、Type、模块名。支持拷贝内存、反汇编、提取内存字符串，以及修改保护属性（11 种 PAGE_* 属性）。

**进程线程**：线程ID、线程对象、Teb、优先级、线程入口、切换次数、状态、挂起计数、所在模块。支持结束、挂起、恢复、反汇编入口、校验模块签名、定位文件。既不在线程链表也不在 PspCid 表中的线程会被标记为隐藏。

**进程句柄**：权限、句柄类型、句柄名称、句柄索引、句柄对象、引用计数。支持关闭句柄与强制关闭句柄。

### 驱动模块

10 列：驱动名称、驱动基址、驱动大小、驱动入口、驱动对象、驱动路径、服务名、启动方式、加载顺序、文件厂商。

驱动列表合并三种枚举方式（驱动对象链表、DriverObject 遍历、可执行内存扫描）的结果，任一方式未能枚举到的条目标为隐藏色。脱离系统记录的隐藏驱动、断链驱动、内存加载（MAP）驱动都会出现在列表里，特征是无驱动路径、无服务名，或同一驱动对应多块内存区域。

右键菜单：

| 操作 | 说明 |
|---|---|
| 拷贝模块内存 | dump 驱动映像到文件，PE 时可选还原 |
| 拷贝内核内存 | 按地址与大小读取任意内核内存 |
| 查看IRP派遣 | 列出 28 个 IRP_MJ_* 派遣函数的地址与所属模块 |
| 卸载驱动 | 危险操作，会被系统拒绝的情况直接提示 |
| 反汇编 / 提取内存字符串 | 反汇编按整个映像大小 |
| 加载与卸载查看 | 打开驱动加载与卸载记录 |
| 定位到驱动文件 / 查看驱动文件属性 | 双击列表项同样打开文件属性 |
| 复制 → 驱动名 / 基址 / 路径 / 驱动对象 | |
| 启动方式 → 开机 / 系统 / 自动 / 手动 / 禁用 | 直接改写服务注册表的 Start 值 |
| 定位到注册表项 | 用 regedit 打开该驱动的服务键 |
| 导出所有到文件 | |

**加载与卸载记录**：上表为加载记录（驱动名称、文件生成时间、驱动STATUS、驱动文件哈希），下表为卸载记录（驱动名称、模块基址、模块尾、模块大小、卸载时间、文件哈希）。均可删除记录、修改哈希、导出。

### 钩子

五个子页签：SSDT / ShadowSSDT / OBJECT / Process / Driver。识别并恢复 Inline、IAT、EAT 三类钩子。

**SSDT / ShadowSSDT**：8 列——服务号、函数名称、现在函数地址、HOOK类型、原本函数地址、现在模块路径、现在机器码、原本机器码。默认只显示被挂钩的行，可切换为显示全部。支持恢复单条、恢复所有，并分别反汇编当前函数与原始函数。函数名来自内置的各版本服务号对照表。

**OBJECT**：OBJECT类型、函数名、当前函数地址、HOOK、原始函数地址、OBJECT地址、当前函数地址所在模块。对象回调标红，支持恢复单条与恢复所有。

**Process**：左侧进程列表，选中后「检测」扫描该进程的全部钩子；右侧列出函数、节区、模块偏移、挂钩地址、挂钩类型、挂钩当前机器码、挂钩原本机器码。恢复时按挂钩类型自动分派，也可分别清除 Inline / Iat / Eat 三类。

**Driver**：左侧驱动列表，选中后「检测」扫描该驱动的全部钩子；右侧列同上。支持恢复单条，或一次还原该驱动的 Inline / Eat / Iat 全部钩子。

### 内核层

| 子页签 | 列 | 操作 |
|---|---|---|
| 系统回调 | 回调入口、通知类型、模块路径、文件厂商、备注 | 移除、全部移除、反汇编入口、查看文件属性、定位文件、复制、导出 |
| DPC定时器 | 定时器对象、DPC、触发周期(s)、函数入口、模块路径、文件厂商 | 移除、反汇编入口、查看文件属性、定位文件、复制、导出 |
| IO定时器 | 定时器对象、设备对象、状态、函数入口、时钟链表头、模块路径、文件厂商 | 开启、停止、移除、反汇编入口、查看文件属性、定位文件、复制、导出 |
| 系统线程 | 线程ID、线程对象、优先级、线程入口、切换次数、线程状态、挂起计数、所属模块、文件厂商 | 结束、挂起、恢复、反汇编入口、查看文件属性、定位文件 |
| 工作队列线程 | 线程对象、Cid、函数入口、模块路径、文件厂商 | 挂起、恢复、反汇编入口、查看文件属性、定位文件 |
| 设备树 | 树形展开 驱动 → 设备 → 附加设备 | 删除附加设备，从下往上拆设备栈 |
| Nmi | CallBack、Context、ModulePath | 删除、反汇编、复制一行、导出所有 |

系统回调覆盖 12 种通知类型：`PsSetCreateProcessNotifyRoutine` / `Ex` / `Ex2`、`PsSetCreateThreadNotifyRoutine` / `Ex`、`PsSetLoadImageNotifyRoutine`、`CmRegisterCallback`、`IoRegisterShutdownNotification`、`IoRegisterLastChanceShutdownNotification`、`KeRegisterBugCheckCallback`、`KeRegisterBugCheckReasonCallback`、`IoRegisterFsRegistrationChange`。

IO 定时器与两个线程页的右键菜单会按当前状态置灰——已开启的不能重复开启，挂起计数非零的不能重复挂起。

### 监控

六个子页签。

**恶意代码**：列出内核中可疑代码的地址、大小、命中次数与命中类型（Nmi / Apc / Dpc）。每 5 秒自动刷新，同一地址命中次数累加。支持启动/关闭监控、反汇编、提取字符串、拷贝一行。

**进程**：记录进程创建/退出、线程创建/退出、模块加载，含当前进程、目标进程、详细信息、模块归属与风险标记。模块归属列解析线程起始地址所属模块（模块名+偏移）；开启注入检测后，跨进程且不在白名单内的线程标记为「疑似注入」。五个过滤器（进程 / 模块 / 线程 / 模块归属 / 注入检测）可逐个开关。

**驱动模块**：捕获内核中出现的驱动映像文件并缓存，列出驱动路径、基地址、映像大小、捕获文件大小与缓存状态（已缓存 / 文件过大 / 打开失败 / 读取失败 / 内存不足 / 未捕获）。无文件落地的驱动即使原文件已消失，映像内容仍在内核内存中。

**注册表**：完整记录注册表操作——索引、进程ID、进程名称、操作类型、注册表路径、键、值、结果。操作类型覆盖 11 组共 22 种 Pre/Post 事件：Open、Create、DeleteKey、DeleteValueKey、SetValue、SetInformationKey、RenameKey、EnumerateKey、EnumerateValueKey、QueryKey、QueryValueKey，每组可单独开关。信息过滤支持按进程ID、进程名称、注册表路径添加条件。

**网络**：记录网络操作——进程名、进程ID、动作、ip、值。动作覆盖 Connect / Listen / Send / Recv / Dns / Accept，可逐项开关。选中任意事件即可在下方查看完整内核调用栈（最多 32 帧，解析为模块名+偏移）与数据包内容。信息过滤支持按进程ID、进程名称、IPV4 添加条件。

**内核 API**：检测无文件落地威胁的核心。监控 22 个内核 API 的调用，按 9 个分组组织：

| 分组 | 数量 | API |
|---|---|---|
| 连续物理内存 | 3 | `MmAllocateContiguousMemory` / `…SpecifyCache` / `…SpecifyCacheNode` |
| MDL 页面申请 | 2 | `MmAllocatePagesForMdl` / `MmAllocatePagesForMdlEx` |
| MDL 映射 | 3 | `MmMapLockedPages` / `…SpecifyCache` / `…WithReservedMapping` |
| 物理地址映射 | 2 | `MmMapIoSpace` / `MmMapIoSpaceEx` |
| Section 映射 | 2 | `MmMapViewInSystemSpace` / `…Ex` |
| 预留映射地址 | 2 | `MmAllocateMappingAddress` / `…Ex` |
| MDL 保护 | 1 | `MmProtectMdlSystemAddress` |
| 池申请 | 3 | `ExAllocatePoolWithTag` / `ExAllocatePool2` / `ExAllocatePool3` |
| Process / Thread | 4 | `PsLookupProcessByProcessId` / `MmCopyVirtualMemory` / `PsCreateSystemThread` / `KeStackAttachProcess` |

另有 `NtAllocateVirtualMemory`，合计 23 个可配置项。

每条事件记录时间、内核 API、进程 PID、线程 TID、目标地址、返回值与栈深度。双击展开详情：完整调用栈（最多 16 帧）、API 原型、Arg0–Arg6 逐项参数与 IRQL。

设置对话框支持按 API 勾选监控范围（全选 / 全不选 / 逐个配置），为单个 API 配置事件规则（目标地址、关联地址、大小范围、Flags 掩码与匹配值、Pool Tag、直接调用者地址），以及通用条件（进程名、PID、是否采集调用栈、最小栈深度、最大缓存事件数）。监控基于 Inline Hook 实现。

> 内存加载的驱动、无文件落地的 shellcode，最终都要调这批 API。
> 它们不会在文件系统留痕，但会在这里留痕。

### 文件

**文件访问监控**：实时记录文件操作——序号、时间、进程ID、进程名、操作、路径、结果，操作覆盖创建、读取、写入、删除、重命名、属性修改。顶部按钮为开始监控、暂停/继续、停止监控、清空、导出、过滤设置。

过滤设置分三页：操作类型（勾选要监控的操作）、进程过滤（按 PID 或进程名添加，支持完整匹配与包含匹配）、路径过滤（按路径子串添加）。

**文件回调查询**：枚举 minifilter 驱动注册的全部回调——函数名称、函数地址、函数所在模块、文件厂商、过滤器地址、高度（Altitude）。覆盖 FilterUnload、InstanceSetup、InstanceQueryTeardown、InstanceTeardownStart、InstanceTeardownComplete、GenerateFileName、NormalizeNameComponent、NormalizeContextCleanup，以及各 `IRP_MJ_*` 的 PreOperation / PostOperation。双击行可直接反汇编回调函数。

### 网络

**WfpCallBack**：WFP 回调枚举——ID、版本、Classifyfn、Notifyfn、FlowDeletefn、标志、设备对象、文件路径、文件厂商。支持删除指定 Callout、定位驱动文件、查看文件属性，以及反汇编三个回调函数。

**WfpFilter**：WFP 过滤器枚举——CalloutKey、名称、描述、流量层级、FilterID、CallOutID、驱动路径。支持删除指定过滤器。

**网络连接**：遍历当前 TCP / UDP 连接（IPv4 与 IPv6）——连接类型、IP 版本、本地地址端口、目标地址端口、状态、进程ID、进程路径。状态覆盖 CLOSED / LISTEN / SYN_SENT / SYN_RCVD / ESTAB / FIN_WAIT1 / FIN_WAIT2 / CLOSE_WAIT / CLOSING / LAST_ACK / TIME_WAIT / DELETE_TCB。

### 关于与自动更新

关于页显示当前版本、最新版本与更新记录，并可在线检查、下载更新。

- 进入页面自动检查一次，也可手动「检查更新」
- 「立即更新」需二次确认，随后下载安装包
- 下载后校验 SHA256，通过才写出更新作业文件并启动 updater.exe，主程序自动退出
- 更新记录合并内置历史与服务端记录，按版本号降序，选中任一版本可查看该版本说明
- 服务端可通过 `mandatory` 或 `minSupportedVersion` 强制更新

检查与下载均在后台线程执行，不阻塞界面。

### 通用操作

| 操作 | 快捷键 |
|---|---|
| 刷新当前页 | F5 |
| 复制选中行 | Ctrl+C |
| 全选 | Ctrl+A |

状态栏分四格：状态提示、当前页名（主标签 > 子标签）、列表总数、选中数量。

---

## 截图

![进程完整信息](images/26-process-commandline.png)

![隐藏驱动与断链驱动](images/27-driver-hidden.png)

![SSDT 机器码对照](images/08-ssdt.png)

![对象钩子](images/33-object-hook.png)

![系统回调](images/32-system-callback.png)

![网络监控与调用栈](images/28-monitor-network-callstack.png)

![内存反汇编与十六进制](images/30-memory-disassembly.png)

![提取内存字符串](images/31-memory-strings.png)

---

## 更新日志

### 1.0.0.10 · 2026-09-16

- 修复从进程模块及内存列表打开反汇编时，大模块或大区域因读取长度超限而无法显示的问题。默认读取最多 0x1000 字节，不足一页使用原长度，读取长度仍可手动调整。

### 1.0.0.9 · 2026-09-15

- 补充、修正 25H2 / 26H1 内核适配，更新 SSDT / ShadowSSDT 名称映射。
- 修复 WFP 回调与过滤器枚举的兼容性及数据显示问题。
- 修复 26H1 内存池扫描导致的蓝屏问题。
- 改善驱动对象枚举的引用、锁与异常清理，保留断链驱动恢复及内存扫描。
- 修正进程列表异常标记、签名验证与内存反汇编读取相关问题，文件回调增加反汇编入口。

### 1.0.0.8 · 2026-09-13

- 静态链接 MFC，不再需要运行库 DLL。
- 启动时自动清理旧版本遗留 DLL。
- Release 版增加管理员权限声明。

### 1.0.0.2

- 添加内存加载远程线程注入、添加 HOOK 注入。

---

## 说明

- 适配针对已核验的系统版本与样本；未列出的功能及后续系统更新不作全量兼容承诺。
- 本工具为内核级程序，加载驱动前请自行完成签名。
- 若发现 bug 或有其他建议，欢迎通过上方反馈渠道联系。
