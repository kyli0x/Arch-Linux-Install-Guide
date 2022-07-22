# Arch Linux x86_64 Installation Guide:

## Copying ISO onto USB
```
dd bs=4M if=/path/to/<file.iso> of=/dev/sd<X> conv=fsync oflag=direct status=progress
```


# Installation

Checking internet:
```shell
root@archiso# rfkill list
root@archiso# rfkill unblock <wifi> # if hard blocked
root@archiso# iwctl
[iwd]# device list
[iwd]# station <DEVICE> scan
[iwd]# station <DEVICE> get-networks
[iwd]# station <DEVICE> connect SSID
[iwd]# exit

root@archiso# ping 8.8.8.8
```

## Partitioning

Verify Drives:
```shell
root@archiso# lsblk
```

Delete & List Partitions:
```shell
root@archiso# fdisk /dev/sda
  - d
  - 3
  - d
  - 2
  - d
  - 1
  - p   # list partitions
```

Create Partitions:
```shell
  - n   # new partition
  - (enter)
  - (enter)
  - +1G
  - -Y

  - n
  - (enter)
  - (enter)
  - +90G
  - Y

  - n
  - (enter)
  - (enter)
  - (enter)
  - Y

  - w   # wipe partitions
```

Verify New Partitions:
```shell
root@archiso# lsblk
```

Make filesystem:
```shell
root@archiso# mkfs.ext4 /dev/sda3
root@archiso# mkfs.ext4 /dev/sda2
root@archiso# mkfs.fat -F32 /dev/sda1
```

Mount partitions:
```shell
root@archiso# mount /dev/sda2 /mnt
root@archiso# mkdir /mnt/home
root@archiso# mkdir /mnt/boot
root@archiso# mount /dev/sda1 /mnt/boot
root@archiso# mount /dev/sda3 /mnt/home
```

Verify Drives:
```shell
root@archiso# lsblk
```

Installing Base & Personal Packages:
```shell
root@archiso# pacstrap /mnt base base-devel linux linux-firmware linux-headers networkmanager vim
```

Generating filesystem
```shell
root@archiso# genfstab -U /mnt >> /mnt/etc/fstab
```

Moving into Arch System:
```shell
root@archiso# arch-chroot /mnt
```

Enabling Internet:
```shell
sh# systemctl enable NetworkManager
```

Setting Timezone:
```shell
sh# ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
sh# hwclock --systohc
sh# vim /etc/locale.gen   # uncomment en_US.UTF-8 pt_BR.UTF-8
sh# locale-gen
sh# vim /etc/locale.conf  # LANG=en_US.UTF-8 LANG=pt_BR.UTF-8 LC_TIME=pt_BR.UTF-8
```

Setting Hostname and Hosts:
```shell
sh# vim /etc/hostname # <PC_NAME>
sh# vim /etc/hosts    # 127.0.0.1 localhost (\n) ::1 localhost (/n) 127.0.1.1 <PC_NAME>
```

Installing Grub:
```shell
sh# pacman -S grub efibootmgr
sh# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
sh# grub-mkconfig -o /boot/grub/grub.cfg
```

Setting Root Password:
```shell
sh# passwd  # set password
```

Reboot

## Post Install:

Turning on NTP:
```shell
$ timedatectl set-ntp true
```

Adding User Account & Giving Sudo Priv:
```shell
$ useradd -m -g wheel <USERNAME>
$ passwd
$ vim /etc/sudoers  # uncomment: root ALL=(ALL) ALL -and- %whell ALL=(ALL) ALL
```
Connecting to wifi using NetworkManager:
```shell
nmcli dev status
nmcli radio wifi
nmcli radio wifi on # if disabled
nmcli dev wifi list
nmcli dev wifi connect <network-ssid> password "<network-password>"
```
Installing Personal Base Programs:
```shell
$ pacman -S xorg xorg-server xorg-xinit xdg-utils xorg-xmag nitrogen picom firefox git python-yaml playerctl w3m feh okular ranger jsoncpp zathura zathura-pdf-poppler yajl zsh noto-fonts-cjk nodejs npm lxappearance pulseaudio neovim polybar-dwm-module gsfonts```

Git clone zsh plugins into ~/.config/zsh/plugins/
```shell
git clone https://github.com/zsh-users/zsh-autosuggestions.git
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
```

Additional Programs:
```shell
jdk11-openjdk ghidra radare2 iaito wireshark net-tools nmap traceroute whois numlockx p7zip zip unzip lsof gucharmap tldr mcfly flameshot wmname btop acpi acpi_call tlp
```

Setting default browser:
```shell 
xdg-settings set default-web-browser firefox.desktop
```

Setting git defaults
```shell
git config --global user.name "<username>"
git config --global user.email "<user_email>"
git config --global init.defaultBranch main # default branch to main (for rust-lang)
```
Disable Touchpad:
```shell
xinput list
xinput set-prop ## "Device Enabled" 0 # use ID number found in xinput list
```
