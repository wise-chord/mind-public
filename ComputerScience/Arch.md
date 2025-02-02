#### References
[arch wiki: Installation guide](https://wiki.archlinux.org/title/Installation_guide)
# 1 Installation and Configuration
## 1.1 Installation
### 1.1.1 Download installing image and boot live environment
[download image](https://archlinux.org/download/)
> [!warning]
> An image that is too old may cause errors in the pacman package manager
### 1.1.2 Connect to the internet
#### 1.1.2.1 plug in network cable
#### 1.1.2.2 WiFi: iwctl
> [!important]
> enter iwctl
> `# iwctl`
> 
> show WiFi devices
> `[iwd]# device list`
> 
> x1 ∈ your devices
> `[iwd]# station x1 get-networks`
> x1 ∈ your devices, x2 ∈ your network
> `[iwd]# station x1 connect x2`
> 
> exit iwd
> `[iwd]# exit`
### 1.1.3 Update packages and install tmux 
> [!tip]
> Although it may possibly no impact, highly recommend to update packages and install tmux before the below steps to prevent unexpected situations
>
> If you want to use parallel download, please execute `vim /etc/pacman.conf` and uncomment the line of parallel download (and modify the number of parallel)
>`# ParallelDownload = 5` → `ParallelDownload = 32`
>
> If you are in Chinese, Japanese or Korean, it is highly recommended to add archlinuxcn source, although the source is named "cn", it packs a lot of software packages commonly used by Chinese, Japanese and Korean people, and aur package managers. To use the source, please add below code in the end of the file: 
> ```
> [archlinuxcn]
> Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
> ```
> and execute `pacman -S archlinuxcn-keyring`

> [!important]
> 
> update mirrors, x1 ∈ your country or area
> `# reflector --sort rate (--country x1) --save /etc/pacman.d/mirrorlist`
> 
> update packages and install tmux
> `# pacman -Syyu tmux`
> 
> enter tmux, and then you could use (Ctrl+B) + "/% to divide tty screen
> `# tmux`
### 1.1.4 Partition the disks
#### 1.1.4.1 view current partitions
> [!important]
> show your disks, or you could ls /dev
> `# lsblk`
> 
> x1 ∈ your disks, you could also use fdisk if you want
> `# gdisk /dev/x1`
> 
> print partition table
> `Command (? for help):p`
#### 1.1.4.2 if you do NOT have EFI partition, create a new EFI partition
> [!important]
> x2~x6 depends on your original partitions
> ```
> Command (? for help):n
> Partition nember (1-128, default x2):
> First sector (x3, default = x4) or (+-)size(KMGIP):
> last sector (x5, default = x6) or (+-)size(KMGIP):
> Current type is 8300 (Linux filesystem)
> Hex code or GUID (L to show codes, Enter =8300):ef00
> Changed type of partition to 'EFI system partition'
> ```
#### 1.1.4.3 create your root partition
> [!important]
> x7~x11 depends on your original partitions
> ```
> Command (? for help):n
> Partition nember (1-128, default x7):
> First sector (x8, default = x9) or (+-)size(KMGIP):
> last sector (x10, default = x11) or (+-)size(KMGIP):
> Current type is 8300 (Linux filesystem)
> Hex code or GUID (L to show codes, Enter =8300):
> ```
#### 1.1.4.4 confirm and exit
> [!important]
> print partition table after above operation
> `Command (? for help):p`
> 
> (exit without saving, if you find any problems
> `Command (? for help):q`
> )
> 
> write and exit, if you do NOT find any problems
> ```
> Command (? for help):w
> 
> Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING PARTITIONS!!
> 
> Do you want to proceed? (Y/N): Y
> OK; writing new GUID partition table (GPT) to /dev/x1.
> The operation has completed successfully.
> ```
#### 1.1.4.5 make filesystem for the new partition
> [!important]
> ONLY if your EFI partition is just created in 1.1.4.2, x12 is your new EFI partition
> `# mkfs.vfat /dev/x12`
> 
> mkfs for root partition, use `mkfs.ext4` if you want to use ext4, x13 is your root partition
> `# mkfs.btrfs /dev/x13`
### 1.1.5 Mount the partition
> [!important]
> x1 is your root partition, x2 is your EFI partiiton
> ```
> # mount /dev/x1 /mnt
> # mount /dev/x2 /mnt/boot/efi --mkdir
> ```
### 1.1.6 Install essential packages
> [!important]
> the base command (you need add name of packages you choose after it)
> `pacstrap -K /mnt`
> 
> base packages , ucode and firmware (you need install all of this)
> `bsae base-devel linux-firmware amd-ucode intel-ucode`
> 
> kernal (you could choose to install one or more of below, but not none of them)
> * linux: `linux linux-headers`
> * linux-zen: `linux-zen linux-zen-headers`
> * linux-lts: `linux-lts linux-lts-headers`
> 
> console text editor (you need to install one of them, depends on your habits)
> * vim: `vim`
> * nano: `nano`
> 
> some important tools (recommend to install all of this)
> * grub: `grub efibootmgr`
> * btrfs tool: `btrfs-progs`
> * network manager: `networkmanager`
> * sound tool: `alsa-utils pulseaudio pulseaudio-buletooth`
> * process monitoring: `htop`
> * system info display: `neofetch`
> * git: `git`
> 
> input method and fonts (if you need)
> * japanese input method and fonts: `fcitx5 fcitx5-configtool mozc fcitx5-mozc noto-fonts-cjk adobe-source-han-sans-jp-fonts ttf-unifont`
> * chinese input method and fonts: `fcitx5 fcitx5-configtool fcitx5-chinese-addons noto-fonts-cjk adobe-source-han-sans-cn-fonts ttf-unifont`
> 
> graphics drivers
> * intel: `mesa vulkan-intel`
> * amd: `mesa` and some drivers depend on your gpu (you could visit [arch wiki](https://wiki.archlinux.org) later)
> * nvidia: you are using nvidia, and i'm sure you can find drivers yourself
> 
> if you want to use other shell, please search and install it yourself
### 1.1.7 Generate filesystem table
> [!important]
> ```
> # genfstab -U /mnt >> /mnt/etc/fstab
> ```
### 1.1.8 Change root and configure arch
> [!important]
> change root
> `# arch-chroot /mnt`
> 
> set host name, x1 is the host name you want
> `# echo "x1" >> /etc/hostname`
> 
> set ip mapping, execute `vim /etc/hosts` or `nano /etc/hosts` and write below text in the end of the file:
> ```
> 127.0.0.1       localhost  
> ::1             localhost  
> 127.0.1.1       x1.localdomain  x1
> ```
> 
> configure time, x2 is your time zone
> `# timedatectl set-timezone x2`
> set ntp
> `# timedatectl set-ntp true`
> 
> set network manager auto start
> `systemctl enable NetworkManager`
> 
> create standard user, x3 is the user name you want
> `# useradd -m -G wheel x3`
> 
> modify password for root
> `# passwd`
> modify password for standard user
> `# passwd x3`
> 
> enable sudo, execute `vim /etc/sudoers` or `nano /etc/sudoers` and uncomment the wheel group
> `# %wheel ALL=(ALL:ALL) ALL` → `%wheel ALL=(ALL:ALL) ALL`
### 1.1.9 Configure grub and finish
> [!important]
> if your disk have NOT configure grub in other systems, x1 is your disk with arch
> `# grub-install /dev/x1`
> 
> if you need to modify some configurations, execute `vim /etc/default/grub` or `nano /etc/default/grub`
> 
> generate configuration file, if your grub is original, you can modify it directly
> `# grub-mkconfig -o /boot/grub/grub.cfg`
> 
> umount partitions
> ```
> # umount /mnt/boot/efi
> # umount /mnt
> ```
> 
> finish and reboot
> `# reboot`
## 1.2 Desktop Environment
> [!tip]
> Before you start installing desktop environment, please check if xorg and wayland are ready
> if not, execute `# pacman -S wayland xorg`
### 1.2.1 KDE
> [!important]
> installation
> `# pacman -S plasma kde-applications sddm`
> 
> sddm auto start
> `# systemctl enable sddm`
### 1.2.2 Cosmic DE
> [!important]
> installation
> `# pacman -S cosmic`
> 
> sddm auto start
> `# systemctl enable sddm`
### 1.2.3 GNOME