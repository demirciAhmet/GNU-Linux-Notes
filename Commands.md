## Some random commands

This file contains random commands for any purpose.

List what you have mounted: `sudo mount -av`

Check the swap partitions or files: `sudo swapon`

Shows btrfs system: `sudo btrfs filesystem show /`

Shows btrfs subvols: `sudo btrfs subvolume list /`

Remove or add a timeout for systemd boot loader: `sudo -i` `nano /boot/efi/loader/loader.conf` `timeout 3`
