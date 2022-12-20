# archinstall
Archlinux installation with LVM on LUKS, Dual Booting with Windows 11

## Prerequisites
- I am assuming Windows 11 was preinstalled, as is the case in most of todays laptops.
- Disable Secure Boot and Fast Boot in BIOS.
- Reserve some free space from Disk Management.
- Download latest archlinux iso, prepare installation media using Rufus, with GPT.
- Paste the following to a Powershell terminal with Admin privilege to enable ntp in Windows:
 ```reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\TimeZoneInformation" /v RealTimeIsUniversal /d 1 /t REG_QWORD /f```

## Installation
- 
- The EFI partition, created by default by Windows, is small. We are gonna create a /boot Linux extended boot partition to mitigate the problem.
- 
