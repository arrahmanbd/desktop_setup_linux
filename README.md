

Arch Linux Installation Guide 🔥
=============================

✅ Table of Contents
-----------------

* [Connect to Network](#connect-to-network)
* [Easy Installation](#easy-installation)
* [Professional Installation](#professional-installation)
    * [Partitioning Schema](#partitioning-schema)
    * [System Installation with a Separated Home](#system-installation-with-a-separated-home)
    * [System Installation without a Separated Home](#system-installation-without-a-separated-home)
    * [Accessing Our System](#accessing-our-system)
    * [Localization](#localization)
    * [Root Setup](#root-setup)
    * [Install Bootloader](#install-bootloader)
* [Post-Installation Tasks](#post-installation-tasks)
* [Reboot and Enjoy](#reboot-and-enjoy)

Connect to Network
------------------

To connect to a network, use the `iwctl` tool to manage Wi-Fi connections:

    iwctl
    device list
    station wlan0 scan
    station wlan0 get-networks
    station wlan0 connect SSID
    # Replace SSID with your network's SSID. Enter the password when prompted.
    # Exit iwctl and verify the connection:
    ping google.com

Easy Installation
-----------------

For a simplified installation process, follow these steps:

    sudo pacman -Syu
    sudo pacman -S archinstall
    archinstall

Follow the on-screen instructions to complete the installation.

Professional Installation
-------------------------

For more control over the installation process, follow these steps:

### Partitioning Schema

If you plan to have a separate `/home` partition, use the following partitioning scheme:

| Partition | Size | Filesystem | Mount Point |
| --- | --- | --- | --- |
| 1   | 120-512MB | FAT32 | /boot/efi |
| 2   | 32-50GB | ext4 | /   |
| 3   | 120GB+ | ext4 | /home |
| 4   | 4GB | swap |     |

### System Installation with a Separated Home

Follow these commands for a system with a separate `/home` partition:

    mkfs.fat -F 32 /dev/sdb1  # EFI System Partition
    mkfs.ext4 /dev/sdb2       # Root Partition
    mkfs.ext4 /dev/sdb3       # Home Partition
    mkswap /dev/sdb4          # Swap Partition
    
    # Mount the partitions
    mount /dev/sdb2 /mnt               # Mount Root Partition
    mkdir -p /mnt/boot/efi            # Create directory for EFI partition
    mount /dev/sdb1 /mnt/boot/efi     # Mount EFI Partition
    mount /dev/sdb3 /mnt/home         # Mount Home Partition (if separate)
    swapon /dev/sdb4                  # Enable Swap Partition
    

### System Installation without a Separated Home

Follow these commands for a system without a separate `/home` partition:

    mkfs.fat -F 32 /dev/sdb1  # EFI System Partition
    mkfs.ext4 /dev/sdb2       # Root Partition
    mkswap /dev/sdb3          # Swap Partition
    
    # Mount the partitions
    mount /dev/sdb2 /mnt               # Mount Root Partition
    mkdir -p /mnt/boot/efi            # Create directory for EFI partition
    mount /dev/sdb1 /mnt/boot/efi     # Mount EFI Partition
    swapon /dev/sdb3                  # Enable Swap Partition
    

### Accessing Our System

Change root into the new system:

    arch-chroot /mnt

### Localization

Set the timezone and synchronize the hardware clock:

    ln -sf /usr/share/zoneinfo/United_States/New_York /etc/localtime
    hwclock --systohc

Generate the locale:

    nano /etc/locale.gen
    # Un-comment en_US.UTF-8 UTF-8
    locale-gen

Specify the system language:

    nano /etc/locale.conf
    # Add the following line:
    LANG=en_US.UTF-8

Set the hostname for your system:

    nano /etc/hostname
    # Add your hostname, e.g.:
    HackerOS

### Root Setup

Set the root password and create a user:

    passwd
    useradd -m -G wheel -s /bin/bash ghost  # Replace 'ghost' with your desired username
    passwd ghost

Edit the sudoers file to allow members of the `wheel` group to use `sudo`:

    EDITOR=nano visudo
    # Uncomment the following line:
    # %wheel ALL=(ALL:ALL) ALL

Switch to the new user:

    su ghost
    sudo pacman -Syu
    exit

### Install Bootloader

Re-enter the root environment and install the bootloader:

    arch-chroot /mnt
    grub-install /dev/sdb       # Install GRUB bootloader on the disk
    grub-mkconfig -o /boot/grub/grub.cfg  # Generate the GRUB configuration file

At this point, the installation is complete. You can proceed to install a desktop environment.

Post-Installation Tasks
-----------------------

### Install GUI Desktop Environment

Choose and install a desktop environment of your choice. For GNOME:

    sudo pacman -S xorg gnome

For KDE Plasma:

    sudo pacman -S xorg kde-plasma

### Enable Display Manager for GUI Login

Choose a display manager (GNOME Display Manager or SDDM) and enable it:

For GDM (GNOME Display Manager):

    sudo systemctl enable gdm

For SDDM (Simple Desktop Display Manager):

    sudo systemctl enable sddm

Reboot and Enjoy
----------------

Reboot the system:

    reboot

\### Troubleshooting GRUB Issues

If you encounter GRUB errors or OS Prober issues, check and update `/etc/default/grub`:

    nano /etc/default/grub
    # Uncomment the following line if needed:
    # GRUB_DISABLE_OS_PROBER=false

Reinstall GRUB with:

    grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB

Enjoy your new Arch Linux installation!

