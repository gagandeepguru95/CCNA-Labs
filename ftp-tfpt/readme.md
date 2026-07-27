# TFTP and FTP IOS Upgrade Lab

## Overview
In this lab I used TFTP and FTP to upload a new IOS image to R1 and R2, then set both routers to boot from the new image. I also removed the old IOS files from flash once the upgrade was confirmed.

## Topology
- SRV1: TFTP and FTP server, holds the IOS image files
- R1: connects to SRV1, upgraded using TFTP
- R2: connects to SRV1 through R1, upgraded using FTP

## IP Addressing

| Device | Interface | IP Address |
|--------|-----------|------------|
| SRV1   | -         | 10.0.0.1   |
| R1     | -         | 10.0.0.x   |
| R2     | -         | 10.0.0.x   |

IP addressing and routing were already set up before starting the IOS upgrade steps, including a static route on R2 to reach the 10.0.0.0/24 network.

## R1: Upgrade with TFTP

Checked the current IOS version and the files already in flash:
```
enable
show version
show flash
```

Copied the new IOS file from SRV1 using TFTP:
```
copy tftp flash
10.0.0.1
<filename>
```

Set R1 to boot with the new file, saved config, then reloaded:
```
conf t
boot system flash <filename>
exit
write
reload
```

Verified the new version was running:
```
show version
```

Deleted the old IOS file from flash:
```
show flash
delete flash:<old_filename>
show flash
```

## R2: Upgrade with FTP

Checked the current version:
```
show version
```

Configured FTP credentials before copying, since FTP needs a username and password:
```
conf t
ip ftp username jeremy
ip ftp password ccna
exit
```

Copied the new IOS file from SRV1 using FTP:
```
copy ftp flash
10.0.0.1
<filename>
```

Set R2 to boot with the new file, saved config, then reloaded:
```
conf t
boot system flash <filename>
exit
write
reload
```

Verified the upgrade and removed the old file:
```
show version
show flash
delete flash:<old_filename>
show flash
```

## Notes and Observations
- FTP is much slower than TFTP in Packet Tracer. The transfer looked stuck at first, but it just needed time to finish.
- The `boot system flash` command only takes effect after saving the config with `write`, so I made sure to save before reloading.
- Deleting the old IOS file after confirming the new version works keeps flash storage clean.
