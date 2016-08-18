Testing mcelog

It is always a good idea to test an error handling mechanism before it is really needed. mcelog includes a test suite. The test suite relies on mce-inject which needs to be installed and in $PATH.

You also need the mce-inject kernel module configured (with CONFIG_X86_MCE_INJECT=y), compiled, installed and loaded:

modprobe mce-inject
On many systems you need to make sure the EDAC modules are not loaded, as these can prevent error report to mcelog.

Then you can run the mcelog test suite with

make test
This will inject different classes of errors and check that the mcelog triggers runs. There will be some kernel messages about page offlining attempts. The test will also lose a few pages of memory in your system (not significant)

Note this test will kill any running mcelog, which needs to be restarted manually afterwards.

Test coverage

make test only tests mcelog and large parts (but not all of) the kernel corrected machine check code at the software level. It does not exercise the actual error handling and correcting hardware or any BIOS components. To exercise these components you can:
Use APEI error injection if your BIOS supports it. See examples in mce-test below.
Use a known bad DIMM that throws errors in a test system
Test on a live cluster with enough machines. Enough transistors imply plenty of errors.
Use special error injecting DIMMs.
Use extreme measures like hair dryers (not recommended, use at your own risk)
mce-test

In addition an more in-depth test of the Linux kernel machine check facilities can be done with the mce-test test suite. mce-test supports testing uncorrected error handling, real error injection, handling of different soft offlining cases, and other tests.