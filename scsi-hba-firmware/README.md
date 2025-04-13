= Files explanation

- efimptfl.efi is the EFI flash utility that *downloads* the *.rom files to HBA's *BIOS*.
- lsimpt.rom is the *ROM* file per se.
- lsimpt.efi - boot services driver, to be run via the EFI *load* command.
- fwupdate.exe - Windows Firmware Update utility, i.e., it *patches* the BIOS or EFI image with a new *ROM* image that could be
subsequently flashed into the *BIOS*.