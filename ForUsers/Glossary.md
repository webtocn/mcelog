

## Glossary 术语

- **Machine Check**
硬件故障时在系统中产生日志信息

- **Machine Check Architecture (MCA)**
x86计算机允许软件统过硬件程序接口检测和处理硬件故障。
这是一个抽象的接口，允许操作系统逆向处理。详细的描述在Intel Architecture Software Developer Manual Volume 3 chapter 15。

- **Machine Check Exception (MCE)**
x86 CPU增加了完整的18例外来预测一个不可修正的硬件错误。操作系统拥有一个特殊的进程处理MCA寄存器中的信息。

- **Error Correcting Code (ECC)**
错误修正码，一段能够检测和修复错误的特殊的代码。常用的ECC码能够检测到2bit的错误和修正1bit的错误（有一些高级的编码能够处理更多）。阅读维基百科中的ECC部分。服务器的内存一般支持ECC。

- **Corrected error**
硬件错误能够被硬件自动修复(例如：使用ECC修复单bit故障)。这些错误不需要软件立刻解决，但是任然会报告、统计、预测和失败分析。

- **Uncorrected error**
硬件产生了一个不可修正的硬件错误。数据已经产生异常。这种错误要求软件及时反馈。

- **Predictive Failure Analysis (PFA)**
主要通过可修正错误的趋势来预测硬件以后的状态，自动任务步骤以避免一些离线。
mcelog工具自动的offlining对于内存、CPU缓存。此外用户可以进行配置。

- **IO-MCA**
在最新的Xeon系统中报告PCIE链路不可修正的错误。mcelog支持，具体看IO errors错误报告。

- **PCI AER（PCI-Express Advanced Error reporting）**
PCIE高级错误报告，在PCIE链路中用来报告错误。不支持mcelog工具，但是会产生内核日志。详细的内容可以查看OLS页或者IO-MCA。

- **RAS**
Reliability, Availability, Serviceability.
可靠性，实用性，可服务性。

- **DIMM**
Memory module.
内存模块

- **DMI (or SMBIOS)**
这是一个标准的BIOS向操作系统报告当前硬件配置方式。DMI信息会被dmidecode程序输出。mcelog使用这些信息当能够映射DIMMnumbers to silk screen labels。

- **APEI**
一个ACPI4的借口定义标准，他允许BIOS向操作系统报告错误。以前大家都知道的是WHEA ：硬件错误报告体系结构(Windows Hardware Error Architecture) 

- **EDAC**
一个可选的内存错误报告框架，具体请阅读FAQ部分。

# Trigger

除了全局计数器， mcelog 还维护使用 leaky-bucket 算法来约定阀值。当错误的数量在一个特定时间窗超过预先配置的阈值触发器将被执行。触发器通常 /etc/mcelog 目录中的 shell 脚本还可以其他内部操作。在 mcelog.conf 阈值和触发器可以配置

触发将作为用户配置为 mcelog mcelog 运行。配置 , 默认情况下根默认触发动作可以被指定一个不同的触发脚本在配置文件中。行动除了默认触发 ( 比如通知管理员 ) 可以放在各自的 /etc/mcelog/*. 本地脚本执行后默认操作。这允许更新默认脚本没有覆盖当地行动。所有触发动作也记录到系统日志。

  error flow 概述了各种触发器 ( 注意有些缺失 )
