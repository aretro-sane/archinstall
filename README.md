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
- In the new ```[iwd]``` prompt, find out the name of the wireless adaptor using ```device list```. Most probably there will be only one, named ```wlan0```.
- To scan for network, run ```station wlan0 scan```. Now to get the list of all available networks, type ```station wlan0 get-networks``` and hit Enter.
- To connect to a network from the list, run ```station wlan0 connect <name of the network you want to connect to>```. Then type in the password and hit Enter.
- Now type ```exit``` to exit from the ```[iwd]``` prompt. You should be connected to the network, just ```ping``` and check.
### Setting up Network synchronized time and pacman mirrors
- Type ```timedatectl set-ntp true``` and hit enter.
- Refresh the servers with ```pacman -Syy```.
- To get fastest pacman mirrors using reflector, run ```reflector --latest 20 --protocol https --sort rate --save /etc/pacman.d/mirrorlist```.
- Again refresh the servers with ```pacman -Syy```.
### Partitioning and Formatting the disk
The EFI partition, created by default by Windows, is small. We are gonna create a ```/boot``` Linux extended boot partition to mitigate the problem. Also, I am creating a swap partition. Although I have 16GB RAM, I had use for a swap partition previously when compiling large OSS projects. The swap partition size will be 16GB too. If you feel it's excessive, you may reduce it, or may use a swapfile. I am also not creating separate ```/home``` partition, as I have all javascript, rust, haskell, go in my home folder, and there are large docker containers in my root folder. Though I am sure I will not run out of space, I have some difficulty deciding how much space to dedicate to each partition, so I am just not bothering to create unnecessary partitions and fragment my disk.
- I am installing arch on SSD, so the disk is ```/dev/nvme0n1```. It may be different for you. Run ```cfdisk /dev/nvme0n1``` 
- There should already be a ```/efi``` partition created by Windows. No need to create that. But we need a ```/boot``` partiton in that free space.
- Move down to the free space previously created, select new to create a new partition, and type in 500M in Partition Size. A new partition of 500MiB will be created.
- Now select the Type option to change the type of this partition. Move up/down to select the type as ```Linux Extended boot```. Verify that the Partition UUID dispalyed below is ```BC13C2FF-59E6-4262-A352-B275FD6F7172```. This would be the ```/boot``` partition.
- Select the remaining free space, and hit enter for the Default Partition Size, as we are not gonna create any more partitions.
- Again, select the Type option, to change the type to 'Linux LVM'. Check the Partition UUID shown below is ```E6D6D379-F507-44C2-A23C-238F2A3DF928```.
- Select the Write option, hit Enter, type in yes, hit Enter. The partitions will now be written. Now select the Quit option to quit ```cfdisk```.
Now since we have completed partitioning the disk, we are gonna start formatting the disk. Although we can use any filesystem for ```/boot``` as per archwiki, historically, only VFAT filesystem was recognized. So, to be safe, I am gonna format the ```/boot``` partition as a FAT32 filesystem.
- Run ```mkfs.fat -F32 <partition for /boot>``` and hit Enter.
- Type ```cryptsetup luksFormat <partition for LVM>```. Type in YES, and put in a password you will not forget :). Verify it, and then wait for some time.
- Now we need to open the encrypted partition. Type ```cryptsetup open <partition for LVM> cryptlvm```. You can choose any other name than ```cryptlvm```. Enter the passphrase.
- Create a physical volume using ```pvcreate /dev/mapper/cryptlvm```. Switch ```cryptlvm``` with the name you chose earlier.
- Now create a volume group using ```vgcreate vg /dev/mapper/cryptlvm```. You can choose other name than ```vg```.
- 
