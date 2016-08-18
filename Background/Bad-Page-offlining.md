
# Bad page offlining
DIMM中有一种很常见的内存错误single “strck bit”,这一bit被卡在一个特殊的状态，不能被重写了。同一DIIM的同一channel中的其他bits确不受影响。
ECC DIMM错误能够被修正：这种错误不会立刻产生严重的问题，但是当附近其他的bit 再次产生get corrupted错误时，就会升级成不可修复的2bit错误。 此外，当内存再次读取这部分内容时，那一个卡住的bit将再次产生连续规则的修正错误报告。  
解决这类
Handling these reports takes some time and may drown error thresholds for other purposes. It does not actually tell anything new.

The best strategy is to simply stop using the bit. The only entity which has reasonable fine control over that is the operating system. It manages memory by pages (typically 4K of size) and it's possible to offline the page containing the stuck bit.

When running in daemon mode mcelog keeps track of corrected memory errors per 4K pages and maintains error counters for each page. This is controlled using the [[page]](http://www.mcelog.org/config.html#page) section in mcelog.conf mcelog defaults to page tracking enabled by default (if the CPU supports it) with offlining of a specific page when a threshold of 10 errors per 24 hours is crossed.

Linux starting with 2.6.33 (and in some 2.6.32 kernels with backports) have a page soft-offlining capability. That is the contents of the page are copied somewhere else (or dropped if not needed) and the original page is removed from the normal operating system memory management and not used anymore.

The capability is called soft-offlining because it never kills or otherwise affects any application, in contrast to the "hard-offlining" that is done when an uncorrected recoverable data error happens.

One caveat is that offlining doesn't work for all pages, only pages in specific states. However in common workloads the majority of memory can be soft-offlined.

- Hardware

Bad page offlining works on CPUs that provide an physical address on corrected memory machine check errors. This is generally CPUs with integrated memory controller and ECC memory support. On Intel Xeon 75xx, 65xx, E7 (Westmere) series CPUs a special [driver](ftp://ftp.kernel.org/pub/linux/kernel/people/ak/apei/) has to be loaded for this and the BIOS has to enable the "firmware first" functionality.