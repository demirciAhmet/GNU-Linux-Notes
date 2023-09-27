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

Anything you put in `/home/username/Templates/` can be easily generated. You can easily generate from the template you put there.
