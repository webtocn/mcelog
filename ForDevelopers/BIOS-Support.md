BIOS support for mcelog

This applies to mcelog running on Intel servers (Nehalem and beyond)
mcelog has the (socketid, channel, DIMM) information from the CPU and tries to translate that into a motherboard silkscreen label using SMBIOS. The label is then logged in the log file and in the accounting database in memory.

SMBIOS has no official way that works to do that translation, but on a Supermicro test system it was possible to do it by matching the non standard identifier. That is what mcelog is trying to do.

The SuperMicro identifier looks like:

Bank Locator: A1_NodeX_ChannelY_DimmZ

with X socketid, Y channel, Z DIMM and the A1 part being ignored. That's the format mcelog is looking for. If a BIOS provides the same information in some other format mcelog can also be easily changed to match that. If the information is not matched it will just print the untranslated data and a warning.

The goal of this is to allow a service technician to identify a potentially bad DIMM (note that a DIMM may not be necessarily bad because it has a few corrected errors). Using the untranslated data may be enough for that, or they may find it easier using a silkscreen label that is printed on the motherboard.

However if untranslated users will see the "failed to prefill DIMM database from DMI data" message in the log. Also the mcelog --client accounting will only show DIMMs as they get errors.

A BIOS that wants to avoid this has the following options:

Provide the same format as shown above and validate it (make sure the motherboards actually match it)
Provide some other format and modify mcelog to match it (maintainer is open to integrate patches to do so) Also needs validation in this case.
Changing the BIOS to shut up the message without validating the report is likely a bad idea because it risks bad DIMM replacements and increased service costs.