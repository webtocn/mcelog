Frequently asked questions

How do I report bugs in mcelog?
Here is this machine check output. Please tell me what it means
I have this corrected error message. Is my system broken?
I inject errors, but nothing happens
How do I get an overview of what errors happened on the system?
How do I enable memory error reporting on SLES11-SP1?
How do I decode fatal machine checks?
How do I "run through mcelog --ascii"?
How do I log fatal machine checks to disk?
On what systems does DMI DIMM decoding work?
I get "Cannot open /dev/mem for DMI decoding"
I get "failed to prefill DIMM database from DMI data"
How do I enable corrected memory error reporting on Intel Xeon 7500,6500,E7 series systems?
How does mcelog compare to EDAC?
I get "machine check events logged"?
I get "kernel hardware error no human readable mce decoding support on this cpu type"
Can you release mcelog?
I get a "only decoding architectural errors" message. Does mcelog log all errors?
mcelog does not start on newer AMD systems anymore
Can I configure mcelog to send an email on each hardware error
On SUSE systems I see "mcelog: SMTP server problem" messages
mcelog on my old Linux distribution (RHEL 4 or similar vintage) reports wrong CPUs?
How do I report bugs in mcelog?

Please send them to the maintainer (see contact ) There is currently no mcelog specific mailing list. This is for bugs in mcelog itself, not for asking what is wrong with your hardware.
Here is this machine check output. Please tell me what it means

You have to ask your hardware vendor. Linux and mcelog developers cannot do hardware support for you. A machine check is a hardware problem and not a software problem. Such questions will be ignored. An exception are crashes or problems in the actual error reporting. Please report those.

If you're doing over clocking or otherwise running your system out of spec: consider to stop doing so now.

I have this corrected error message. Is my system broken?

A low rate of corrected memory errors is expected and does not require replacing hardware or other action. Also over a long uptime the total number of corrected errors may also be quite high. That is expected too.
You only need to worry when you have a high number of corrected errors in a short time. This likely indicates some problem.

On a cluster of systems the low rate of all the systems combined may actually be a high rate on a central logging server. That is expected too.

I inject errors, but nothing happens

In many systems where EDAC is running it may intercept all errors before mcelog can see them. This was fixed recently with this patch . On older systems unload EDAC.
How do I get an overview of what errors happened on the system?

When mcelog runs as a daemon it will account all memory errors. You can see the current state of the daemon using mcelog --client This daemon accounting is only in memory and not saved to disk.
Alternatively you can take a look at the log in /var/log/mcelog See also the previous question on how to interpret the log.

How do I enable memory error reporting on SLES11-SP1?

mcelog ships with a daemon capable mcelog, but the init script is disabled by default. Enable it as root with
chkconfig mcelog on 
rcmcelog start

How do I decode fatal machine checks?

How do I "run through mcelog --ascii"?

First don't expect too much from decoding them. This often does not actually tell you what really went wrong. And see the next question first.
Save the panic message in a file. Run mcelog --ascii < file

If you don't have a logging console that logged the panic message (like a serial console with a logging terminal program or netconsole or a USB debug cable ) you may need to type it in first.

For using DMI DIMM decoding mcelog has to run as root on the same machine as what experienced the error. If you're using an older kernel that does not report the CPUID in the machine check panic you may need to specify the correct CPU (see the manpage ). See also the next question.

How do I log fatal machine checks to disk?

Modern Linux kernels (since 2.6.31) automatically do a warm reset after a machine check panic. Unless something goes wrong (like some platform mechanism forcing a power switch on reboot) the machine check will then be logged after the reboot.
In some cases server BIOS do clear and log machine checks during boot. In this case you may need to look for the machine check data in the BIOS log.

On what systems does DMI DIMM decoding work?

DMI DIMM decoding currently only works on Intel Xeon 55xx, 56xx, E5 (Romley) systems It also requires the DMI BIOS to report the DIMMs in a specific non-standardized format, which may not be available in all systems. The DIMMs will also be only reported when mcelog recognizes the CPU and the CPU supplies the necessary data.
I get "Cannot open /dev/mem for DMI decoding"

This usually happens when mcelog is not running as root. The only implication is that mcelog cannot decode DIMM entries using the BIOS DMI tables. Depending on your system this may lose you some information on where a reported DIMM is located on the motherboard.
I get "failed to prefill DIMM database from DMI data"

This is a harmless warning message. The DIMM database prefill relies on a specific non-standard format of the DIMMs in the DMI BIOS tables. If this format is not used by the BIOS mcelog will only discover DIMMs as they get their first error (iff the CPU reports DIMMs in machine check errors).
If you are a BIOS writer or system vendor seeing this message on your system please see the page on BIOS support .

How do I enable corrected memory error reporting on Intel Xeon 7500,6500,E7 series systems?

By default these systems only report corrected errors per socket. To get reporting per page (to enable bad page offlining ) you need to load the APEI ghes module and enable APEI memory error reporting in the BIOS:
modprobe ghes APEI is included since the 2.6.35 linux kernel, for 2.6.32 kernels (like SLES11-SP1 or RHEL6) a backport is available.

How does mcelog compare to EDAC?

Linux also has an alternative memory error reporting infrastructure called EDAC. It consists of separate drivers for specific platforms that use hardware facilities to do memory error counting and DIMM topology discovery. The basic model is quite different from mcelog and fully kernel based.
While EDAC supports basic memory error counting and some logging, it does not implement any of the advanced features in mcelog which need user space support.

The main advantage of EDAC over mcelog is that EDAC supports reporting memory errors on older systems with separate memory controller. mcelog only works on modern systems where the memory controller is integrated. In addition EDAC works on newer AMD systems.

I get "machine check events logged"?

Look into /var/log/mcelog for the decoded machine or query the running mcelog daemon with .
I get "kernel hardware error no human readable mce decoding support on this cpu type"

This is pretty much a bug in newer Linux kernels. They print this message on every corrected error, even though it's useless and also the decoding into the kernel log is not very useful because mcelog can aggregate the information much better. This is fixed with this patch
To apply to a kernel: download raw patch , cd kernel source, patch -p1 < patchfile, recompile.

Can you release mcelog?

mcelog is on a rolling release through the git tree. However if you need a specific version in the git tree, and a git sha identifier is not good enough, you can use the "vXXX" tags which are regularly incremented.
I get a "only decoding architectural errors" message. Does mcelog log all errors?

mcelog doesn't know your CPU. It won't be able to decode model specific errors, but it will log them all in a raw (hex) format. The important errors are usually architectural, but sometimes new architectural errors are added, and you may not see them decoded.
mcelog does not start on newer AMD systems anymore

AMD stopped supporting mcelog. If you want to use it please contact AMD. Update: support is being worked on now.
Can I configure mcelog to send an email on each hardware error

Yes you can. Just send the email in a trigger
But it's usually a bad idea to send an email on each event. The problem is that those can occur in bursts (for example when something is rapidly deteriorating) and then you may end up with hundreds or thousands of emails. Usually you only want notifications when a specific threshold is crossed once to avoid this problem.

On SUSE systems I see "mcelog: SMTP server problem" messages

This comes from a buggy patch that SUSE is applying to their version of mcelog. The patch adds an email mode (see also previous question), and is always incorrectly enabled. This problem is tracked in this opensuse bug
mcelog on my old Linux distribution (RHEL 4 or similar vintage) reports wrong CPUs?

Old Linux kernels reported the CPU APIC ID instead of the Linux visible CPU number. Depending on the platform they can differ and also be not zero based. There's unfortunately no fool proof way for mcelog to detect it. /proc/cpuinfo has a field for APIC IDs so it's possible to translate them back manually.
One should also add that many platforms don't have stable CPU numbers. They can change between boots.