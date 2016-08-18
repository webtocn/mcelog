# Memory Error
服务器中内存错误是最常见的。hey also scale with the amount of memory: the more memory the more errors. In addition large clusters of computers with tens or hundreds (or sometimes thousands) of active machines increase the total error rate of the system.

系统中的ECC内存错误通畅能够被检测到且能够自动修正。
当系统中一个可修正的错误或soft error发生时，不用太在意。实际上一个系统长时间的运行着，预测的soft error比例将会被记录。硬件平台使用错误修正码和冗余来解决soft error。这就是为什么他们被叫可修正错误。不幸的是一个不可修正的错误（硬件），数据异常，soft error不直接要求软件反应。 soft errors do not directly require software reaction. Also since there is an expected soft error rate for each system some soft errors are expected to occur. A small number of soft errors in a given time frame is generally not a problem.

内存错误扮演者重要的角色在定位哪个组件引起的该问题时，mcelog 进程进程预测内存错误在不通的buckets：
每个 DIMM (if available)
每个 Channel
每个 memory controller
每个 Socket (= physical CPU package)
每个 Page. This is used to automatically  offline bad pages

这种情形在daemon方式运行时能够被查询到，使用如下方式：
```
mcelog --client
```
对于更多详细内容请阅读LinuxKongress 2010 mcelog版面或者其他的相关文献
