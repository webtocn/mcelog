# 简介

Mcelog是一款对现代的X86 Linux系统中[Machine Checks](http://www.mcelog.org/glossary.html#machine-check)错误（特别是内存、IO和CPU硬件错误）进行记录和统计的工具软件。

Mcelog能在32位的X86 Linux内核(2.6.30版本之后)和64位Linux内核(2.6版本之后）上记录Machine Check错误，也应该在所有需要Error Handling的Linux系统上运行。

Mcelog进程能够以[多种方式](http://www.mcelog.org/error-flow.png)来统计内存和其他的一些错误。 `Mcelog --client` 可以用来查询运行中的后台Mcelog进程。当错误量超过了配置的阈值时，这个进程也可以执行触发器（[triggers](http://www.mcelog.org/triggers.html)）任务。 在一定范围内他被用作自动[Predictive Failure Analysis](http://www.mcelog.org/glossary.html#pfa)算法工具，包括[bad page offlining](http://www.mcelog.org/badpageofflining.html)和自动 [cache error handling](http://www.mcelog.org/cache.html)。用户也可以自行定义[触发Action](http://www.mcelog.org/triggers.html)。

所有错误都会记录到 /var/log/mcelog日志文件 或 由syslog管理 或者 其他journal系统管理 。

Mcelog支持集成内存控制器的X86系统的内存错误管理;支持所有x86系统的CPU错误管理。

传统的Mcelog工具使用cron计划任务方式运行,但现在这种用法被弃用了。现在运行它的方法是将其作为一个开机启动的进程一直在系统中运行。此外, 它可以在命令行解析严重的machine checks错误(目前系统在重启时都会自动检测的)。

对于安装信息和如何配置mcelog(如果你是一个管理员)请参见  [README](http://www.mcelog.org/README.html) 。

---------
# Overview

mcelog logs and accounts [machine checks](http://www.mcelog.org/glossary.html#machine-check) (in particular [memory](http://www.mcelog.org/memory.html), IO, and CPU hardware errors) on modern x86 Linux systems.

mcelog is required by both 32bit x86 Linux kernels (since 2.6.30) and 64bit Linux kernels (since early 2.6 kernel releases) to log machine checks and should run on all Linux systems that need error handling.

The mcelog daemon accounts [memory](http://www.mcelog.org/memory.html) and some other errors errors in [various ways](http://www.mcelog.org/error-flow.png). `mcelog --client` can be used to query a running daemon. The daemon can also execute [triggers](http://www.mcelog.org/triggers.html) when configurable error thresholds are exceeded. This is used to implement a range of automatic [predictive failure analysis](http://www.mcelog.org/glossary.html#pfa) algorithms: including [bad page offlining](http://www.mcelog.org/badpageofflining.html) and automatic [cache error handling](http://www.mcelog.org/cache.html). User defined [actions](http://www.mcelog.org/triggers.html) can be also [configured](http://www.mcelog.org/config.html).

All errors are logged to _/var/log/mcelog_ or syslog or the journal.

For memory errors it supports modern x86 systems with integrated memory controllers; for CPU errors all modern x86 systems are supported.

Traditionally mcelog was run as a cronjob, but this usage is deprecated now. The modern way to run it is to start it at boot up time and run it always as a daemon. In addition it can be used to decode fatal machine checks on the command line (but this is also usually not needed anymore on modern kernels which log those after reboot automatically)

For installation information and how to set up a mcelog package (if you're a distributor) please see the [README](http://www.mcelog.org/README.html).