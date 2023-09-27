## Some random commands

**This file contains random commands and notes for any purpose.**

***

##### Partition Management, BTRFS

List what you have mounted: `sudo mount -av`

Check the swap partitions or files: `sudo swapon`

Shows btrfs system: `sudo btrfs filesystem show /`

Shows btrfs subvols: `sudo btrfs subvolume list /`

Remove or add a timeout for systemd boot loader: `sudo -i` `nano /boot/efi/loader/loader.conf` add to the end: `timeout 3`

***

##### File Manager, Nautilus

Show hidden files in the file manager: `Ctrl + H`

Type to the search bar of the file manager: `Ctrl + L`

New file from right click context menu: Put a template to `/home/username/Templates/`. You can easily generate a file from right click context menu from the template you put here.

Manipulate images from right click menu: 

*** 

##### DPKG Packages about Gnome and Nautilus

nautilus-kdeconnect:                  KDE Connect integration for Nautilus
nautilus-nextcloud:                   Nextcloud integration for Nautilus
gnome-shell-extension-gsconnect:      KDE Connect implementation for GNOME Shell
gnome-sushi:                          sushi is a quick previewer for nautilus

***

##### OBS Settings

**Sources:** XSHM for XORG, PipeWire for Wayland.


