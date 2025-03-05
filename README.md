# Running OpenVMS on an HP ZX2000 

**Disclaimer: OpenVMS DOES NOT officially support ZX2000 platform.**

However, the following statements appear to be true about ZX2000:
> The zx6000 is identical to the rx2600 except for some firmware, and the card
cage (that provides an AGP - which will not work as we never completed the
support work for it). I'm not sure we ever had an rx6000, but I'm pretty
confident that it works.

>The rx2000 is the same basic electronics as the rx2600 with a single CPU and
an AGP bus - we do have a couple of these (I have one).

Roughly, whereas a rx2000 is an entry-level server platform, the zx2000 is a
workstation, sharing a large part of electronics, that is CPU, chipset, couple
of other controllers. The difference would mainly be the card cage and some
firmware.

That said, following is a list of modifications and additions to a zx2000 suffice
to run an OpenVMS v8.4 system.
 
# Hardware specifications

* **Optical Drive: OptiArc DVD-RW AD-5170A**
* Graphics card: ATI 7000 AGP
* Disk controller: LSI21320-IS PCI-X SCSI U320 - 2pc.

## Notes on choosing the hardware:

The zx2000 only supports a very limited range of extension cards, so the above
specs should be followed "ad verbum". 

### Optical drive 
That is especially true about the optical drive, as OpenVMS itself is very picky,
requiring a 512 bytes block size, whilst a 1K size is much more common, (read
almost every modern CD/DVD-ROM has a 1K block). So specific models should be 
carefully choosen.
Plextor drives and some HP models support 512 bytes blocks (apparently
those rebranded for OEM). Otherwise "DQ - Medium Not Ready" errors would
prohibit installation.

Please keep in mind that the original models may well be rebranded: for example
OptiArc becomes Sony or HP. As a rule, the model code (AD-5170A) is usually
retained throughout the re-branding.

Also, see below the IDE channel notes.

### Graphics card
OpenVMS would not normally support AGP, but given the ATI 7000 should be OK with
HPE OpenVMS 8.4. Not so with newer VMS Software versions though. 

### Disk controller
Disk controllers are also very special: These given would be single channel,
hence two of these to separate the slower devices. As long as they're PCI-X
and the chipset is an LSI-1030, you're fine.

Please see below regarding the firmware.

# Installation process

## Pre-requisites

### The console
The OpenVMS installation is ALWAYS done through the serial ports, regardless of
the BMC settings. That is, unless the serial port is used, installation would
abort, complaining it cannot continue without a serial console.

### The installation media
The ISO image should be written in RAW mode to the optical media, that is, the
image is in fact a raw dump of an ODS-2 filesystem, not the ISO-9660 as you
might guess from the file extension. 

### The IDE/ATA channel
Secondly, the ATA/IDE path for OpenVMS is very unreliable. That is, it only
supports a handful of IDE controllers and all others are prone to errors.
The CF cards are also not well supported.

The zx2000 has two IDE channels labeled "OPTICAL" and "HDD". Make sure you plug the optical drive into the master port on the "HDD" channel. Also I had loads of issues with any form of DVD/RW combo drive.

That is, you **MUST** have the optical drive connected to IDE channel 1
("HDD", NOT "OPTICAL") using the HDD cable set as Master. Any other setting
will fail. 

This leaves you with only the SCSI drives as target installation disks.

### The SCSI firmware

You'll have to install the Itanium firmware for the SCSI controllers.
LSI did provide some here: http://www.lsi.com/storage_home/products_home/host_bus_adapters/scsi_hbas/lsi21320r.

I have also saved them [here](scsi-hba-firmware), just in case, provided you
follow their [license](license.txt).

# General notes

In case you get "DQ - Medium Not Ready" errors during installation, it most
likely means your optical drive does not support 512 byte blocks.

If you need to debug the process, try running:
```
fsX:\efi\boot\bootia64 -flags 30000,0
```

Or if you'd rather go with the "conversational boot":
```
fsX:\efi\boot\bootia64 -flags 0,1
```
where X is the optical media drive.

# Acknowledgements

Special thanks to Brendan Horan for his blog notes here: https://www.horan.hk/blog/ia64-build/. Saved locally [here](doc/Itanium%202%2C%20I%27ve%20got%20one%20by%20Brendan%20Horan.pdf).

comp.os.vms news group (NARKIVE) talk on zx2000 saved locally [here](doc/OpenVMS%20on%20zx2000%20or%20zx6000.pdf).
