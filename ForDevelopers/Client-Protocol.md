mcelog client/server protocol

mcelog in daemon mode supports a simple client-server protocol. The server is normally queried from mcelog --client, but can be also queried by other agents.

Important: Before writing a query agent consider setting up triggers, that run custom scripts when specific error thresholds per hardware unit are crossed. For many problems these are the better answers.

Specification

The communication happens over a SOCK_STREAM AF_UNIX socket on the local host. The file name of the unix socket is configurable. The default is /var/run/mcelog-client. The client connects over the unix socket to the server.
The protocol is an text based protocol. The client sends a command. The server answers with text. The protocol is case sensitive. Each line is finished with unix newline.

The following grammar is in EBNF

Valid commands

Command	Semantics
ping	Server answers with pong
dump	Dump memory errors. Can be followed by modifiers.
pages	Dump errors per page
dump command

The dump command can have the following modifiers separated by spaces: bios to dump BIOS DMI information and all to dump even errors that did not cross a threshold.
	DUMP = "dump" ["bios"] ["all"] 
The reply format is a ASCII format that needs to be parsed by the client. The first line is "Memory errors". Then a list of object description follows.

	REPLY = "Memory errors\n" { OBJECT }
The first line in a object description describes the location as a string of IDENTIFIER value pairs. Then a list of error types follow. Each error type ends with a :. After each error type is a list of data values, consisting of a number and a string. Data values always start with a tab character. An empty line separates different objects
	OBJECT = IDENTIFIERS "\n" { ERRORTYPE "\n" }
	IDENTIFIERS = IDENTIFIER { IDENTIFIER }
	IDENTIFIER = ID number
	ID = [a-zA-Z_][a-zA-Z0-9_]+
	NUMBER = [0-9]+
	ERRORTYPE = error-type ":" "\n" { "\t" NUMBER error-description "\n" }
dump example output

Memory errors
SOCKET 0 CHANNEL any DIMM any
corrected memory errors:
	1 total
	1 in 24h
uncorrected memory errors:
	0 total
	0 in 24h

SOCKET 0 CHANNEL 0 DIMM any
corrected memory errors:
	1 total
	1 in 24h
uncorrected memory errors:
	0 total
	0 in 24h
pages command

The pages command dumps a list of errors per memory page. The first line is "Per page corrected memory statistics". The each line consists of a hex number, followed by :, and a bucket description. The bucket description is of the form: total X seen in "Y". X is the number of errors seen in the time period of the bucket, Y is the string description of the bucket configuration. Followed by an offline or online string. Empty lines should be ignored.
	PAGE-REPLY = description ":" "\n" { PAGE }
	description = [a-fA-Z 0-9]+
	PAGE = NUMBER ":" "total" NUMBER "seen" "in" "\"" description "\"" ["online" | "offline"]
pages example output

Per page corrected memory statistics:
2320000: total 1 seen "1 in 24h" online
3d8c000: total 1 seen "1 in 24h" online
75df000: total 1 seen "1 in 24h" online
7b15000: total 1 seen "1 in 24h" online
Access restrictions

mcelog enforces access from only specific user ids or groups. See the mcelog.conf [server] section for more details.