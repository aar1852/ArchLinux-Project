# Arch Linux Project

Anastasia Reed

CYB 3353 - System Administration

University of Tulsa

Professor Justin Miller


## Get an ISO and verify the signature
1. Go to: https://archlinux.org/download/
2. Scroll down to the United States
    - Pick one, ping it to see if it times out.
    - Select the one that doesn’t.
3.	A variety of files will be available, choose: archlinux-xxxx.xx.xx-x86_64.iso
4. Compare the SHA256 hash:
   - Provided: b72dd6ffef7507f8b7cddd7c69966841650ba0f82c29a318cb2d182eb3fcb1db
   - Generated: B72DD6FFEF7507F8B7CDDD7C69966841650BA0F82C29A318CB2D182EB3FCB1DB

## Install VMWare Workstation Pro
1.	Go to Broadcom support portal: https://support.broadcom.com/
2.	Register/Login.
3.	Navigate to Software, VMWare Cloud, My Downloads, VMWare Workstation Pro.
4.	Find the VMWare Workstation Pro for Personal Use that works for you (Linux or Windows).
5.	Download.
6.  Compare the SHA256 hash:
    - Provided:2c3a40993a450dc9a059563d07664fc0fb85ae398a57d22b1b4bf0e602417bf7
    - Generated:2c3a40993a450dc9a059563d07664fc0fb85ae398a57d22b1b4bf0e602417bf7
7.	Run the installer (exe file).
8.	Pick Typical or Custom.
9.	Follow the on-screen directions.
10.	Restart the host machine (e.g. Your laptop or desktop).

## Create a new VM
1.	Open VMWare Workstation.
2.	Select “Create a New Virtual Machine”.
3.	Select Typical.
4.	Select Installer disc image file (iso) and add the file path to your Arch Linux ISO download.
5.	Select `Other Linux 5.x kernel 64-bit` as the operating system.
6.	Choose your Virtual machine name and location.
7.	Allocate 20GB to disk size and change memory to 2048 MB by selecting “Customize Hardware”.
8.	Click Finish.
9.	Without booting up the new VM, go to Edit, Virtual Machine Settings, Options, Advanced, change the selection to UEFI under Firmware Type.

## Arch Linux installation
1.	Power on the VM.
2.	Select Arch Linux install medium.
3.	Verify that you are in EFI mode using:

  	`# ls /sys/firmware/efi/efivars`
5.	There should be an internet connection already but check by pinging something:

  	`# ping -c 4 www.google.com`
7.	Set the system clock:

  	`# timedatectl set-ntp true`
9.	Disk partitions:
    - To see current disk layout:

        `# lsblk`
    - To create a new partition:
        1.	Enter command:

            `# cfdisk`

        2.	Select: gpt
        3. Select: New
        4.	Enter the amount of space you would like (e.g 500M for sda1, 18.5G for sda2, and 1G for sda3).
        5.	Make sure the partitions are the correct type (e.g. sda3 Size Type is Linux Swap).
        6.	Select Write, then yes for each partition created.
        7.	Select Quit.
    - Verify partitions with:

        `# lsblk`
    - If swap partition created:

        `# mkswap /dev/sda3`

        `# swapon /dev/sda3`
    - Create root file system:

        `# mkfs.ext4 /dev/sda2`
    - Create EFI file system:

        `# mkfs.fat -F 32 /dev/sda1`
    - Mount root partition:

        `# mount /dev/sda2 /mnt`
    - Create boot directory where we will mount the EFI partition:

        `# mkdir /mnt/boot`
    - Mount EFI partition:

        `# mount /dev/sda1 /mnt/boot`
10.	IF you want to change your mirror list:
    1.	Open mirror list:

   	    `# nano /etc/pacman.d/mirrorlist`
    2.	Look up Successfully Syncing mirrors
    3.	Sort list as desired
    4.	Choose the one you want
    5.	Add to mirror list.
       - Ex: Server = http://mirror.YOURCHOICE.com/$repo/os/$arch
12.	Install the base package and nano:

   	`# pacstrap /mnt base linux linux-firmware nano`

## Configuring the system:
1.	Generate an fstab file (when system boots it will know where to mount the partitions):

  	`# genfstab -U /mnt >> /mnt/etc/fstab`
2.	Chroot into the new system:

  	`# arch-chroot /mnt`
3.	Set the appropriate timezone:

  	`# ln -sf /usr/share/zoneinfo/Region/City /etc/localtime`

  	- Ex: # ls -sf /usr/share/zoneinfo/America/Chicago /etc/localtime
4.	`# hwclock --systohc`
5.	Localization:
    1. Generate locales:

  	    `# locale-gen`
  	
    2. Edit the locale.gen file, uncomment any locale that you need to use by removing the # before it:

  	    `# nano /etc/locale.gen`
       - Ex: Uncomment en_US.UTF-8 UTF-8
  	
    3. To finalize change:

  	    `# locale-gen`
  	
    4. Create locale.conf file and set language up using preferred editor:

  	    `# nano /etc/locale.conf`
  	
    5. Add to file: LANG=en_US.UTF-8
7.	Edit hostname to your chosen hostname:

  	`# nano /etc/hostname`

  	1.	Add chosen hostname to file.
        - Ex: archVM
8.	Edit hosts file with your chose hostname:

  	`# nano /etc/hosts`

  	- Ex:	127.0.0.1	localhost
          ::1		    localhost
          127.0.1.1	archVM.localdomain	archVM
10. Install and enable:

    `# pacman -S dhcpcd`

    `# systemctl enable dhcpcd.service`
12.	Create root password:

   	`# passwd`
13.	Install a boot loader:

   	`# pacman -S grub efibootmgr`
14.	Install the bootloader to the EFI partition:

   	`# grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB`
15.	Generate the main grub configuration file:

   	`# grub-mkconfig -o /boot/grub/grub.cfg`
16.	Exit:

   	`# exit`
17.	Unmount partitions:

   	`# umount -R /mnt`
18.	Reboot:

   	`# reboot`

## On Reboot:
1.	Install xorg:

  	`# pacman -S xorg`
3.	Install gnome:

  	`# pacman -S gnome`

  	Enable: `# systemctl enable gdm.service`

  	Start: `# systemctl start gdm.service`
5.	Install sudo package:

  	`# pacman -S sudo`

  	`EDITOR=nano visudo`
7.	Uncomment % from wheel ALL=(ALL) ALL
8.	Create 3 users and assign them to the wheel group:

  	`# useradd -m -G wheel -s /bin/bash username`

  	Set passwords:

  	`# echo ‘username:password’ | chpasswd`

  	Users have to change password on first login:

  	`# chage -d 0 username`
10.	Install Fish shell and ssh:

   	`# pacman -S fish openssh`
12.	Enable/Start ssh:

   	`# systemctl enable sshd`

   	`# systemctl start sshd`
14.	Colors in fish shell:

   	`# set fish_color_user brmagenta`

   	`# set fish_color_command yellow`

   	`# set fish_color_error red`
16.	Aliasing in config file:

   	`# /home/.config/fish/config.fish`
18.	Reboot:

   	`# reboot`

