IO errors in mcelog

Some newer Intel Xeon CPUs support reporting uncorrected data corruption errors through a new IOMCA machine check bank. This is typically data that gets corrupted on the PCI express links. These errors is reported by mcelog for uncorrected errors. For corrected errors and errors reported by the device PCI AER is used, which is reported through the normal kernel log (not in mcelog).

mcelog supports a single iomca trigger for all IOMCA errors and a bus error trigger for other bus errors. The triggers can be configured through the configuration file.

For more details please see this document