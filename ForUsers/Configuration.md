
# Configuration

###  mcelog.conf reference
Mcelog配置通过修改配置文件/etc/mcelog.conf  

- **通用格式**

```
 optionname = value
```
除非删除某条配置，否则不允许使用空白值。   

一般所有的命令行操作在这里不能使用，查看man mcelog或者mcelog --help获取操作列表。例如：允许 --no-syslog 使用如下方式
```
 no-syslog = yes (or no to disable)
```
when the option has a argument
```
logfile = /tmp/logfile
```

- 下面的这些选项不能再命令行指定

设置cpu类型当mcelog解码事件时。
```
 cpu = type
```
有效的参数对于type请查看`mcelog --help`。如果这个参数被设置错误，那么decoded的输出也将会错误。By default when this parameter is not set mcelog uses the CPU it is running on on very new kernels the mcelog events reported by the kernel also carry the CPU type which is used too when available and not overriden.

 运行daemon模式：
```
 daemon = yes
```

 By default mcelog just processes the currently pending events and exits. In daemon mode it will keep running as a daemon in the background and poll the kernel for events and then decode them.

 Filter out known broken events by default.
```
 filter = yes
```
 Don't log memory errors individually. They still get accounted if that is enabled.
```
 filter-memory-errors = yes
```
 Output in undecoded raw format to be easier machine readable (default is decoded).
```
 raw = yes
```
 Set cpu mhz to decode uptime from time stamp counter (output unreliable, not needed on new kernels which report the event time directly. A lot of systems don't have a linear time stamp clock and the output is wrong then. Normally mcelog tries to figure out if it the TSC is reliable and only uses the current frequency then. Setting a frequency forces timestamp decoding. This setting is obsolete with modern kernels which report the time directly.
```
 cpumhz = 1800.00
```
 Log output options Log decoded machine checks in syslog (default stdout or syslog for daemon)
```
 syslog = yes
```
 Log decoded machine checks in syslog with error level
```
 syslog-error = yes
```
 Never log anything to syslog
```
 no-syslog = yes
```
 Append log output to logfile instead of stdout. Only when no syslog logging is active
```
 logfile = filename
```
 Use smbios information to decode dimms (needs root). This function is not recommended to use right now and generally not needed. The exception is memdb prepopulation, which is configured separately below.
```
 dmi = no
```
 When in daemon mode run as this user after set up. Note that the triggers will run as this user too. Setting this to non root will mean that triggers cannot take some corrective action, like offlining objects.
```
 run-credentials-user = root
```
 Group to run as daemon with default to the group of the run-credentials-user
```
 run-credentials-group = nobody
```

 The [server] config section

 User allowed to access client socket. when set to * match any root is always allowed to access. default: root only
```
 client-user = root
```
 group allowed to access mcelog When no group is configured any group matches (but still user checking). when set to * match any
```
 client-group = root
```
 Path to the unix socket for client<->server communication. When no socket-path is configured the server will not start
```
 socket-path = /var/run/mcelog-client
```
 When mcelog starts it checks if a server is already running. This configures the timeout for this check.
```
 initial-ping-timeout = 2
```
 The [dimm] config section

 Is the in memory dimm error tracking enabled? Only works on systems with integrated memory controller and which are supported. Only takes effect in daemon mode.
```
 dimm-tracking-enabled = yes
```
 Use DMI information from the BIOS to prepopulate DIMM database. Note this might not work with all BIOS and requires mcelog to run as root. Alternative is to let mcelog create DIMM objects on demand.
```
 dmi-prepopulate = yes
```
 Execute these triggers when the rate of corrected or uncorrected Errors per DIMM exceeds the threshold. Note when the hardware does not report DIMMs this might also be per channel. The default of 10/24h is reasonable for server quality DDR3 DIMMs as of 2009/10.
```
 uc-error-trigger = dimm-error-trigger
 uc-error-threshold = 1 / 24h
 ce-error-trigger = dimm-error-trigger
 ce-error-threshold = 10 / 24h
```
  

 The [socket] config section

 Enable memory error accounting per socket.
```
 socket-tracking-enabled = yes
```
 Threshold and trigger for uncorrected memory errors on a socket.

 mem-uc-error-trigger = socket-memory-error-trigger
```
 mem-uc-error-threshold = 100 / 24h
```
 Trigger script for corrected memory errors on a socket.
```
 mem-ce-error-trigger = socket-memory-error-trigger
```
 Threshold on when to trigger a correct error for the socket.
```
 mem-ce-error-threshold = 100 / 24h
```
 log socket error threshold explicitely?
```
 mem-ce-error-log = yes
```
 Trigger script for uncorrected bus error events
```
 bus-uc-threshold-trigger = bus-error-trigger
```
 Trigger script for uncorrected iomca erors
```
 iomca-threshold-trigger = iomca-error-trigger
```
 Trigger script for other uncategorized errors
```
 unknown-threshold-trigger = unknown-error-trigger
```
 The [cache] config section

 Processing of cache error thresholds reported by intel cpus.
```
 cache-threshold-trigger = cache-error-trigger
```
 Should cache threshold events be logged explicitely?
```
 cache-threshold-log = yes
```

 The [page] config section

 Memory error accouting per 4k memory page. Threshold for the correct memory errors trigger script.
```
 memory-ce-threshold = 10 / 24h
```
 Trigger script for corrected errors.
```
 memory-ce-trigger = page-error-trigger
```
 Should page threshold events be logged explicitely?
```
 memory-ce-log = yes
```
 Specify the internal action in mcelog to exceeding a page error threshold this is done in addition to executing the trigger script if available

 memory-ce-action = off|account|soft|hard|soft-then-hard
```
 memory-ce-action = soft
```
 off

 no action

 account

 only account errors

 soft

 try to soft-offline page without killing any processes

 This requires an uptodate kernel. Might not be successfull.

 hard

 try to hard-offline page by killing processes

 Requires an uptodate kernel. Might not be successfull.

 soft-then-hard

 First try to soft offline, then try hard offlining

The [trigger] config section

Maximum number of running triggers
```
children-max = 2
```
execute triggers in this directory
```
directory = /etc/mcelog

```


# Triggers

In addition to global counters mcelog also maintains thresholds using a _leaky-bucket_ algorithm. When the number of errors in a specific time window exceeds a pre-configured threshold a _trigger_ will be executed. Triggers are usually shell scripts in the _/etc/mcelog_ directory but can be also other internal actions. Thresholds and triggers can be configured in [mcelog.conf](http://www.mcelog.org/config.html)

Trigger will run as the user configured for mcelog in _mcelog.conf_, by default root The default trigger action can be overridden by specifying a different trigger script in the configuration file. Actions in addition to the default trigger (like notifying an administrator) can be put into the respective _/etc/mcelog/*.local_ script which is executed after the default action. This allows updating the default scripts without overriding local actions. All trigger actions are also logged to syslog.

The [error flow](http://www.mcelog.org/error-flow.png) gives an overview over the various triggers (note some are missing)<a name="dimm" style="color: rgb(170, 87, 62); text-decoration: underline;"></a>

## The DIMM and socket memory error triggers

The _/etc/mcelog/dimm-error-trigger_ and _/etc/mcelog/socket-memory-error-trigger_ scripts are executed when a DIMM or a CPU socket exceeds a configured corrected or uncorrected memory error threshold. The thresholds are configured in the [mcelog.conf](http://www.mcelog.org/config.html) [_[dimm]_](http://www.mcelog.org/config.html#dimm) and [_[socket]_](http://www.mcelog.org/config.html#socket) sections. The default triggers log a warning message in the system log. The triggers are only executed when mcelog runs as a daemon.Arguments are passed as environment variables

| THRESHOLD | human readable threshold status |
| MESSAGE | Human readable consolidated error message |
| TOTALCOUNT | total corrected oruncorrected count of errors for current DIMM depending on what triggered the event |
| LOCATION | Consolidated location as a single string |
| DMI_LOCATION | DIMM location from DMI/SMBIOS if available |
| DMI_NAME | DIMM identifier from DMI/SMBIOS if available |
| DIMM | DIMM number reported by hardware |
| CHANNEL | Channel number reported by hardware |
| SOCKETID | Socket ID of CPU that includes the memory controller with the DIMM |
| CECOUNT | Total corrected error count for DIMM |
| UCCOUNT | Total uncorrected error count for DIMM |
| LASTEVENT | Time stamp of event that triggered threshold (in time_t format, seconds) |
| THRESHOLD_COUNT | Total umber of events in current threshold time period of specific type |

After the default action local actions in _/etc/mcelog/dimm-error-trigger.local_ or respective _/etc/mcelog/socket-memory-error-trigger.local_ are executed.

## The page error trigger

The _/etc/mcelog/page-error-trigger_ script is executed by mcelog in daemon mode when a page in memory exceeds a pre-configured corrected or uncorrected error threshold. mcelog internally also implements offlining the page through the kernel. This is configured through the [_[page]_](http://www.mcelog.org/config.html#page) section of [_mcelog.conf_](http://www.mcelog.org/config.html).

The environment arguments are the same as for the [dimm-error-trigger](http://www.mcelog.org/triggers.html#dimm) script

After the default action local actions in _/etc/mcelog/page-error-trigger.loccal_ are executed.<a name="cache-error-trigger" style="color: rgb(170, 87, 62); text-decoration: underline;"></a>

## The cache error trigger

_/etc/mcelog/cache-error-trigger_ shell script is called for [cache error handling](http://www.mcelog.org/cache.html) in daemon mode when a CPU reports excessive corrected cache errors. This could be a indication for future uncorrected errors.

This trigger is configured through the [cache](http://www.mcelog.org/config.html#cache) section in the configuration file. The threshold is defined by the CPU. The default trigger offlines the affected CPU cores, unless it is the last core running. For more details see [extended cache error handling](http://www.mcelog.org/cache.html).

Arguments are passed as environment variables

| MESSAGE | Human readable error message |
| CPU | Linux CPU number that triggered the error |
| LEVEL | Cache level affected by error |
| TYPE | Cache type affected by error (Data,Instruction,Generic) |
| AFFECTED_CPUS | List of CPUs sharing the affected cache |
| SOCKETID | Socket ID of affected CPU |

After the default action local actions in _/etc/mcelog/cache-error-trigger.local_ are executed.<a name="bus" style="color: rgb(170, 87, 62); text-decoration: underline;"></a>

The **bus-uc-threshold-trigger** runs on uncorrected errors on a IO bus. It is configured through the **bus-uc-threshold-trigger** and **bus-uc-threshold-trigger-threshold** options in _/etc/mcelog.conf_. By default it logs a message with the error location to the system log. After the default action local actions in_/etc/mcelog/bus-uc-error-trigger.local_ are executed.

Arguments are passed as environment variables

| MESSAGE | Human readable consolidated error message. |
| LOCATION | Consolidated location as a single string |
| SOCKETID | Socket ID of CPU that includes the memory controller with the DIMM |
| LEVEL | Interconnect level |
| PARTICIPATION | Processor Participation (Originator, Responder or Observer) |
| REQUEST | Request type (read, write, prefetch, etc.) |
| ORIGIN | Memory or IO |
| TIMEOUT | The request timed out or not |

<a name="iomca" style="color: rgb(170, 87, 62); text-decoration: underline;"></a>The **iomca-error-trigger** runs when a socket receives bus or interconnect errors. It is configured through the **iomca-error-trigger** and **iomca-error-trigger-threshold** options in _/etc/mcelog.conf_. By default it logs a message with the error location to the system log. After the default action local actions in_/etc/mcelog/iomca-error-trigger.local_ are executed.

Arguments are passed as environment variables

| MESSAGE | Human readable consolidated error message |
| LOCATION | Consolidated location as a single string |
| SOCKETID | Socket ID of CPU that includes the memory controller with the DIMM |
| CPU | Linux CPU number that triggered the error |
| SET | PCI segment number |
| BUS | PCI bus number |
| DEVICE | PCI device number |
| FUNCTION | PCI function number |

The **unknown-error-trigger** runs on any errors not otherwise categorized. It is configured through the **unknown-error-trigger** and **unknown-error-trigger-threshold** options in _/etc/mcelog.conf_. By default it logs a message to the system log. After the default action local actions in _/etc/mcelog/unknown-error-trigger.local_ are executed.

Arguments are passed as environment variables

| MESSAGE | Human readable consolidated error message |
| LOCATION | Consolidated location as a single string |
| SOCKETID | Socket ID of CPU that includes the memory controller with the DIMM |
| CPU | Linux CPU number that triggered the error |
| STATUS | IA32_MCi_STATUS register value |
| ADDR | IA32_MCi_ADDR register value |
| MISC | IA32_MCi_MISC register value |
| MCGSTATUS | IA32_MCG_STATUS register value |
| MCGCAP | IA32_MCG_CAP register value |

 

 

 

 

 