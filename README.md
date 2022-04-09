# Arch

# Installation UFI

```bash
ping google.com # se o ping for um sucesso continue

loadkeys=br-abnt2
timedatectl set-ntp true

fdisk -l
fdisk /dev/x

# "d" para deletar partições
# "o" para reconstruir o sistema dos
# "n" para nova partição
# atribua um espaço de no mínimo 512M para a partição EFI, tente colocar até 64G
# ou 40% do montante de espaço na sua segunda partição que será a "/" e o restante 
# coloque na próxima partição equivalente a "/home"
# "t" para setar o tipo FAT para a partição EFI
# "w" para salvar

# formatando partições
mkfs.ext4 /dev/x2
mkfs.ext4 /dev/x3
mkfs.vfat /dev/x1

# Montando partições
mount /dev/x2 /mnt
mkdir /mnt/home
mount /dev/x3 /mnt/hom

# make swap
# Linux divides its physical RAM (random access memory) into chucks of memory 
# called pages. Swapping is the process whereby a page of memory is copied to 
# the preconfigured space on the hard disk, called swap space, to free up that page 
# of memory. The combined sizes of the physical memory and the swap space is the 
# amount of virtual memory available.
dd if=/dev/zero of=/mnt/swapfile bs=1M count=4096
mkswap /mnt/swapfile
swapon /mnt/swapfile

# mount partition FAT
mkdir -p /mnt/boot/EFI
mount /dev/x1 /mnt/boot/EFI

# basic features
pacstrap /mnt base linux linux-firmware networkmanager grub efibootmgr vim

# mapeando e automatizando as próximas montagens de partições
genfstab -U /mnt >> /mnt/etc/fstab

# "/mnt" -> "/" (root directory)
arch-chroot /mnt

# set localtime
ln -sf /usr/share/zoneinfo/America/Recife /etc/localtime

hwclock --systohc

vim /etc/locale.gen # retire o "#" do início de pt_BR.UTF-8
locale-gen
vim /etc/locale.conf # LANG=pt_BR.UFT-8
vim /etc/vconsole.conf # KEYMAP=br-abnt2
vim /etc/hostname # Arch-user
vim /etc/hosts 
# 127.0.0.1    localhost
# ::1          localhost

# small bash script to initialize correctally Linux Startup Process
mkinitcpio -P

#set root password
passwd # ******

# GRUB Install
grub-install --target=x86_64-efi --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg

# Enable NetworkManager to have access to internet after reboot
systemctl enable NetworkManager

exit
reboot
```

## After Arch Linux installation

```bash
pacman -S sudo xfce4 xfce4-goodies base-devel konsole lightdm lightdm-gtk-greeter
	lightdm-webkit2-greeter papirus-icon-theme ufw git xorg 

vim /etc/lightdm/lightdm.conf # set where "#greeter-session=example-gtk-gnome"
# to "greeter-session=lightdm-webkit2-greeter" 
sudo systemctl enable lightdm -f

ufw start && systemctl enable ufw
systemctl enable lightdm

useradd -m x
passwd x
gpasswd -a x wheel

vim /etc/sudoers # remove "#" from wheel All:All

reboot
```

## Wi-fi During Installation

```bash
ping google.com # erro
iwctl
	device list # wifi hardware
	station X scan # scan wifi hardware
	station X get-networks # list of wifi
	station X connect network-name # will prompt password
	station X show # if see connect is because it works
	exit
```

## Wi-fi after Installation

```bash
ping google.com # erro
# use networkmanager cli for wi-fi connections
nmcli dev status # Will show your connection if you are connected to some
nmcli radio wifi on # Will enable wifi conf if it is disable
nmcli dev wifi list # list of wifi
nmcli --ask dev wifi connect X # to connect on X
ping google.com # test

# for saved connections
nmcli con down X # disconnect to X
nmcli con up X # connect to X
```
