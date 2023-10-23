# Install Pop!_OS with **btrfs** and subvolumes (shortened)

**WARNING!:** This guide may not fully correct since I just forked it for later use for a template. Check [mutschler's youtube video](https://www.youtube.com/watch?v=i8HDHAX1RJc) for detailed guide to setup a btrfs sistem to Pop!_OS for version 22.04. For even more up to date guide, please check [mutschler's guide](https://mutschler.dev/linux/pop-os-btrfs-22-04/)

This is a guide to install Pop!_OS (version 22.04 at the time of writing this) with **btrfs** instead of **ext4** to allow the use of **subvolumes** and **timeshift** for continuous backup. 
__Warnings__:
- the guide assumes that the user is not using full-disk encryption
- it features (optional) zstd compression and other SSD optimizations, fitting best laptop users.

## Required partition layout
- the bootloader partition:
 - size: 1024MB
 - format: fat32
 - mount point: `/boot/efi`
 - suggested label: POP_boot
- the recovery partition:
 - size:4096MB
 - format: fat32
 - mount point: `/recovery`
 - suggested label: POP_Recovery
- the root partition:
  - size: +25 Gbs
  - format: btrfs
  - mount point: `/`
  - suggested label: POP_OS

## Get started

1. Install Pop!OS using the official installer from a Live environment as indicated by the _Required partition layout_ above.
2. Do not close the Pop!_OS installer. Instead fire up GNOME Terminal and acquire super-user rights:

```
sudo -i
  Password: ******
```
  
Display your current partitions:
```
lsblk -f
```

Write down the UUIDs of both the __booloader__ and __root__ partitions.

## Create the Btrfs subvolumes

Replace the contents between `< >` as fits (see previous section). Each new line is a separate command to run.

```
mount <path to root partition> /mnt

cd /mnt

sudo btrfs subvolume create @

ls | grep -v "@\|home" | xargs mv -t @

sudo btrfs subvolume create @home

mv ./home ./@home
```

Make sure `ls` gives you `@ @home`.

Unmount:
```
cd /

umount /mnt
```

## Edit the newly installed system

Each new line is a separate command to run. Remove the `ssd` option if you are not using an SSD.
```
mount -o sudo mount -o defaults,subvol=@,ssd,discard,noatime,space_cache,compress=zstd,commit=120 <path to root partition> /mnt

for i in /dev/dev/pts/proc/sys/run; do sudo mount -B $i /mnt$i; done

sudo cp /etc/resolv.conf /mnt/etc/
```

In case you are reinstalling over a previous Btrfs partition, the first command is likely to fail. To get it to work you'll need to add the `clear_cache` parameter, as in:

```
mount -o sudo mount -o <...other options>,clear_cache <path to root partition> /mnt
```

At this point the terminal might warn about `/etc/resolv.conf` being a duplicate of the target; you can safely ignore the warning.

```
chroot /mnt

nano /etc/fstab
```

Make sure you have one line starting with UUID for `/` and one for `/home`. The only difference between these two lines is that one uses the `subvol=@` parameter while the other uses `subvol=@home`. (Remove the `ssd` option if you are not using an SSD). Example:

```
UUID=18226258-bb30-4552-98c0-775ae3d74433  /  btrfs  defaults,subvol=@,ssd,noatime,space_cache,commit=120,compress=zstd  0  0

UUID=18226258-bb30-4552-98c0-775ae3d74433  /home  btrfs  defaults,subvol=@home,ssd,noatime,space_cache,commit=120,compress=zstd  0  0
```
Save the file with Ctrl + O and close with Ctrl + X. Make sure you get the following result from `mount -av`:

```
mount -av
  /boot/efi : successfully mounted
  ...
  / : ignored
  /home : successfully mounted
```

Add the kernel parameters persistently:

```
kernelstub -a "rootflags=subvol=@" -l -s

update-initramfs -c -k all
```
You can now exit the terminal with `exit` (you will need to enter it twice) and reboot to the newly installed system.

## Optional step: Defrag & rebalance data blocks

```
sudo btrfs filesystem defrag -v -r -f /

sudo btrfs filesystem defrag -v -r -f /home

sudo btrfs balance start -m /
```

resource: https://github.com/spxak1/weywot/blob/main/Pop_Btrfs_Subvolumes_with_Timeshift_Condensed.md
