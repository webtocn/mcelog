Thermal events in mcelog

Intel CPUs have an thermal monitoring feature that detects overheating of the CPU. When the CPU reaches the TM1 threshold the CPU gets throttled, when it reaches TM2 it immediately shuts down the system. Throttling means the CPU starts skipping clocks to reduce temperature. This will lead to a much slower system.

When a TM1 event leads to throttling the kernel will report a thermal event to mcelog. The thermal event is logged to the log file. There is no trigger support.

Typical reasons for thermal events are failing fans, failing system cooling, or badly applied thermal paste between the CPU and the heatsink.