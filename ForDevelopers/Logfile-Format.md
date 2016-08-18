mcelog logfile format

mcelog outputs its output to a log file. This can be through syslog, or a direct file, or through some other syslog mechanism such as the journal. The following documents the log file format for automated parsers. mcelog supports both outputting and also reading and annotating this format (with --ascii).

Before writing a parser for the mcelog logfile, consider using the higher-level mechanisms: Triggers and the client protocol

This description only describes the "pure" mcelog output. syslog and other mechanism may add additional time stamps, typically at the beginning of the line. The mcelog output format is a super-set of the machine check format the kernel or Xen outputs on a machine check panic.

The log file output has not been originally designed to be machine readable. That is why it requires partial parsing, with some parts being ignored.

The log file is a collection of records. Each record describes one (corrected or uncorrected) error or event. The records are lines of ASCII text.

Each line is a identifier value pair, with the identifier being upper case and separated by white space. Some lines may contain multiple such pairs. Some identifiers also contain more words after them. Unrecognized text should be ignored. Most identifiers are optional. numbers are in C format, either decimal or hexadecimal starting with 0x.
Many fields refer to register values. See the Intel SDM Volume 3, Chapter 15 and Chapter 16, for details on the registers.

A record always starts with a CPU identifier, followed optionally by other fields. When a CPU identifier is seen

Valid identifier/value pairs

Identifier	Description
CPU number [bank-nr]	Starts a record. Describes the CPU number, optionally followed by the bank number. Required.Can be followed by STATUS or BANK identifier-pairs.
CPU cpu-number: Machine Check Exception mcg-status-nr Bank bank-nr: status-nr	Starts a record. Alternative form of CPU that is output by the kernel, never by mcelog.
Describes the CPU number, mcg-status, bank, status fields in one line
BANK number	Describe the machine check bank. 
May be on the same line as CPU.
STATUS number	Describe the machine check bank. 
May be on the same line as CPU.
MCGSTATUS number	The mcgstatus field 
Will be decoded below.	
RIP number	The program counter
May be followed by !INEXACT! if the exact program counter cannot be determined
RIP segment:number {symbol}	The program counter in segment/offset format, with an kernel symbol
May be followed by !INEXACT! if the exact program counter cannot be determined
TSC number	The CPU time stamp counter at the time of the event
ADDR number	The physical memory address of the error.
MISC number	The MCi_MISC register. Values are model specific. 
Will be decoded below.
PROCESSOR vendor:cpuid	The CPU vendor. Vendor identifier (1 = Intel, 2 = AMD) and the CPUID 1.EAX identifer.
MCGCAP number	The MCGCAP register. 
May be decoded below.
APICID number	The APIC ID of the logical processor where the error occurred
SOCKETID number	Describe the physical APIC-ID of the socket the error occurred on.
CPUID Vendor vendor Family family family Model model	Describe the CPUID version. Decoded version of PROCESSOR
TIME time [decodedtime]	Time when the error occurred, in time_t format. Optionally followed by human readable ctime. Decoded version of TSC
Register decoding adds additional lines after specific identifiers. These are not described here.

DIMMs are accounted in the memory database, and should be queried with mcelog --client or client protocol.
Example

Undecoded:
CPU 0 2
STATUS 1234
TSC 3062652eaab
RIP 3f:<5678>
MCGSTATUS 0x123
With annotations:

Hardware event. This is not a software error.
CPU 0 BANK 1 TSC 3062652eaab 
RIP !INEXACT! 3f:5678
TIME 123456789 Thu Nov 29 13:33:09 1973
MCG status:
MCi status:
Machine check not valid
Corrected error
MCA: corrected filtering (some unreported errors in same region)
Unknown Error 234
STATUS 1234 MCGSTATUS 0
MCGCAP abc APICID f SOCKETID 1 
CPUID Vendor Intel Family 6 Model 15