Further reading

Papers and presentations

mcelog - memory error handling in user space at Linux Kongress 2010 ( paper, slides). Introduction to memory errors on modern systems and a description how the mcelog daemon handles and avoids them. Includes an overview of modern mcelog.
Unified error handling -- A worthy goal? at Linux Plumbers 2009. Towards an improved error reporting infrastructure for Linux.
Ongoing evolution of Linux x86 machine check handling at LinuxCon 2009. Introduction to platform hardware errors on modern x86 machines (including detailed flows) and recent improvements to the Linux x86 machine check handling, with a focus on memory errors. Includes an overview of MCA recovery and a description of the Linux 2.6.32+ application memory error handling interface.
Machine check handling on Linux ( paper, slides) for Linux Kongress 2004. This is an early paper about the first version of mcelog. Many details described in the old paper are outdated by now.
Studies about memory errors

A good study on memory errors from the University of Rochester. "A Realistic Evaluation of Memory Hardware Errors and Software System Susceptibility", Li, Huang, Shen, Chu: Usenix Annual Tech Conference 2010
The famous google memory error study. "DRAM Errors in the Wild: A Large-Scale Field Study", Schroeder, Pinheiro, Weber, SIGMETRICS, 2009. Note: there are various indications that the google numbers are significantly higher than in typical servers. It is not recommended to use them for planning purposes.
A classic study on the benefits of automatic bad page offlining: "Assessment of the Effect of Memory Page Retirement on Systems RAS against Hardware Faults", Tang, Arruthers, Totari, Shapiro: Proceedings of the 2006 International Conference on Dependable Systems and Networks.
A newer study that gets to the same conclusion. Automatic page offlining is a good idea: "Cosmic rays don't strike twice: understanding the nature of DRAM errors and the implications for system design", Hwang, Stefanovici, Schroeder, ASPLOS 2012 (non-paywalled PDF).
Intel RAS

Intel whitepaper on RAS in recent server processors.
The Intel Software Developer's manual describes the low level register interface of the x86 machine check architecture Machine checks are described in Volume 3A: System Programming Guide.
Related software

The mce-inject injector tool and the mce-test test suite can be used to test machine check. This is in addition to the mcelog test suite included with the source (make test).
Linux EDAC project on sourceforge. EDAC is an alternative approach at reporting memory errors. See also the EDAC discussion in the FAQ.
Other

Wikipedia entries for DDR2-SDRAM, DDR3-SDRAM, DDR3-SDRAM, DDR4-SDRAM, ECC memory.
Freecode/Freshmeat mcelog page