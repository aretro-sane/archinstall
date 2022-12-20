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
### Load desired keymap and set font size
I am using standard keyboard, so I don't need to load keymap, but if needed, load yours using ```loadkeys```. Set font size if needed too, using ```setfont``` command.
### Connecting to the internet
I am going to connect to a wireless network, if it's different for you check other methods.
- Type ```iwctl``` and hit enter.
- In the new ```[iwd]``` prompt, search for the name of the wireless adaptor using ```device list```.
- 
- Get fastest pacman mirrors using reflector. I use ```reflector --latest 20 --protocol https --sort rate --save /etc/pacman.d/mirrorlist```.
- Refresh the servers with ```pacman -Syy```.
- Now we need to partition the disk. I am installing arch on SSD, so it is ```/dev/nvme0n1```. It may be different for you.
- The EFI partition, created by default by Windows, is small. We are gonna create a /boot Linux extended boot partition to mitigate the problem.
- 
