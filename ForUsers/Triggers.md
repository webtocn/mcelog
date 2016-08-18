Triggers

In addition to global counters mcelog also maintains thresholds using a leaky-bucket algorithm. When the number of errors in a specific time window exceeds a pre-configured threshold a trigger will be executed. Triggers are usually shell scripts in the /etc/mcelog directory but can be also other internal actions. Thresholds and triggers can be configured in mcelog.conf
Trigger will run as the user configured for mcelog in mcelog.conf, by default root The default trigger action can be overridden by specifying a different trigger script in the configuration file. Actions in addition to the default trigger (like notifying an administrator) can be put into the respective /etc/mcelog/*.local script which is executed after the default action. This allows updating the default scripts without overriding local actions. All trigger actions are also logged to syslog.

The error flow gives an overview over the various triggers (note some are missing)

The DIMM and socket memory error triggers

The /etc/mcelog/dimm-error-trigger and /etc/mcelog/socket-memory-error-trigger scripts are executed when a DIMM or a CPU socket exceeds a configured corrected or uncorrected memory error threshold. The thresholds are configured in the mcelog.conf [dimm] and [socket] sections. The default triggers log a warning message in the system log. The triggers are only executed when mcelog runs as a daemon.
Arguments are passed as environment variables
THRESHOLD	human readable threshold status
MESSAGE	Human readable consolidated error message
TOTALCOUNT	total corrected oruncorrected count of errors for current DIMM depending on what triggered the event
LOCATION	Consolidated location as a single string
DMI_LOCATION	DIMM location from DMI/SMBIOS if available
DMI_NAME	DIMM identifier from DMI/SMBIOS if available
DIMM	DIMM number reported by hardware
CHANNEL	Channel number reported by hardware
SOCKETID	Socket ID of CPU that includes the memory controller with the DIMM
CECOUNT	Total corrected error count for DIMM
UCCOUNT	Total uncorrected error count for DIMM
LASTEVENT	Time stamp of event that triggered threshold (in time_t format, seconds)
THRESHOLD_COUNT	Total umber of events in current threshold time period of specific type
After the default action local actions in /etc/mcelog/dimm-error-trigger.local or respective /etc/mcelog/socket-memory-error-trigger.local are executed.

The page error trigger

The /etc/mcelog/page-error-trigger script is executed by mcelog in daemon mode when a page in memory exceeds a pre-configured corrected or uncorrected error threshold. mcelog internally also implements offlining the page through the kernel. This is configured through the [page] section of mcelog.conf.
The environment arguments are the same as for the dimm-error-trigger script

After the default action local actions in /etc/mcelog/page-error-trigger.loccal are executed.

The cache error trigger

/etc/mcelog/cache-error-trigger shell script is called for cache error handling in daemon mode when a CPU reports excessive corrected cache errors. This could be a indication for future uncorrected errors.
This trigger is configured through the cache section in the configuration file. The threshold is defined by the CPU. The default trigger offlines the affected CPU cores, unless it is the last core running. For more details see extended cache error handling.

Arguments are passed as environment variables
MESSAGE	Human readable error message
CPU	Linux CPU number that triggered the error
LEVEL	Cache level affected by error
TYPE	Cache type affected by error (Data,Instruction,Generic)
AFFECTED_CPUS	List of CPUs sharing the affected cache
SOCKETID	Socket ID of affected CPU
After the default action local actions in /etc/mcelog/cache-error-trigger.local are executed.

The bus-uc-threshold-trigger runs on uncorrected errors on a IO bus. It is configured through the bus-uc-threshold-trigger and bus-uc-threshold-trigger-threshold options in /etc/mcelog.conf. By default it logs a message with the error location to the system log. After the default action local actions in /etc/mcelog/bus-uc-error-trigger.local are executed.

Arguments are passed as environment variables
MESSAGE	Human readable consolidated error message.
LOCATION	Consolidated location as a single string
SOCKETID	Socket ID of CPU that includes the memory controller with the DIMM
LEVEL	Interconnect level
PARTICIPATION	Processor Participation (Originator, Responder or Observer)
REQUEST	Request type (read, write, prefetch, etc.)
ORIGIN	Memory or IO
TIMEOUT	The request timed out or not
The iomca-error-trigger runs when a socket receives bus or interconnect errors. It is configured through the iomca-error-trigger and iomca-error-trigger-threshold options in /etc/mcelog.conf. By default it logs a message with the error location to the system log. After the default action local actions in /etc/mcelog/iomca-error-trigger.local are executed.

Arguments are passed as environment variables
MESSAGE	Human readable consolidated error message
LOCATION	Consolidated location as a single string
SOCKETID	Socket ID of CPU that includes the memory controller with the DIMM
CPU	Linux CPU number that triggered the error
SET	PCI segment number
BUS	PCI bus number
DEVICE	PCI device number
FUNCTION	PCI function number
The unknown-error-trigger runs on any errors not otherwise categorized. It is configured through the unknown-error-trigger and unknown-error-trigger-threshold options in /etc/mcelog.conf. By default it logs a message to the system log. After the default action local actions in /etc/mcelog/unknown-error-trigger.local are executed.

Arguments are passed as environment variables
MESSAGE	Human readable consolidated error message
LOCATION	Consolidated location as a single string
SOCKETID	Socket ID of CPU that includes the memory controller with the DIMM
CPU	Linux CPU number that triggered the error
STATUS	IA32_MCi_STATUS register value
ADDR	IA32_MCi_ADDR register value
MISC	IA32_MCi_MISC register value
MCGSTATUS	IA32_MCG_STATUS register value
MCGCAP	IA32_MCG_CAP register value
