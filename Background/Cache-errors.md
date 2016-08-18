Enhanced cache error reporting

Modern Intel CPUs have support for enhanced cache error reporting to report on the health of the CPU caches for corrected machine checks. To quote the Intel Architecture Software Developer's Manual (Volume 3, 15.4):
A processor that supports enhanced cache error reporting contains hardware that tracks the operating status of certain caches and provides an indicator of their "health". The hardware reports a "green" status when the number of lines that incur repeated corrections is at or below a pre-defined threshold, and a "yellow" status when the number of affected lines exceeds the threshold. Yellow status means that the cache reporting the event is operating correctly, but you should schedule the system for servicing within a few weeks.
When mcelog sees a 'yellow' state on a cache it attempts to offline the cores owning that cache through the cache-error-trigger This will prevent the cache from being used further This allows the system to continue operating, but with reduced computing capability. Programs running on those CPUs will be migrated to other CPUs.
Automatic core offlining can be configured using the options in the [cache] section of /etc/mcelog.conf.

The feature is enabled by default when the mcelog daemon is correctly installed and will work transparently in the background.