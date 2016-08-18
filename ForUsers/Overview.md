# MCELOG
mcelog记录和统计Machine Check(特别是内存、IO和CPU硬件错误)在现代x86 Linux系统中。

mcelog需要由32位x86Linux内核(2.6.30之后)和64位Linux内核(更早的2.6 版本之后)来记录Machine Check，且运行在所有需要错误处理的Linux系统上。

mcelog守护进程[以多种方式](http://www.mcelog.org/error-flow.png)统计内存和其他的一些错误。 `Mcelog --client` 可以用来查询运行的守护进程。当错误阈值超过了配置，这个守护进程也可以执行triggers。 在一定范围内他被用作自动的PFA算法工具，包括[bad page offlining](http://www.mcelog.org/badpageofflining.html)和自动的 [cache error handling](http://www.mcelog.org/cache.html)。用户也可以自行定义

所有错误都记录到 /var/log/mcelog 或 syslog 或者 其他日志 。

对于内存错误它支持现代x86系统的集成内存控制器 ;支持所有的x86系统的CPU错误。

传统的mcelog使用cron计划任务执行,但现在这种用法被弃用。现在运行它的方法是开机启动一个一直运行的进程。此外, 它可以在命令行decode fatal machine checks(当它开机自动运行时，就不用更新的操作系统了.)

安装信息和如何设置mcelog包(如果你是一个管理员)请参见  [README](http://www.mcelog.org/README.html) 。


