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
 - suggested label: recovery
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
# for example: mount /dev/sda3 /mnt
cd /mnt

btrfs subvolume create @

ls | grep -v @ | xargs mv -t @

ls -a /mnt
# . .. @

btrfs subvolume create @home

mv /mnt/@/home/* /mnt/@home/

ls -a /mnt/@/home
# . ..

ls -a /mnt/@home
# . .. wmutschl

btrfs subvolume list /mnt
# ID 264 gen 339 top level 5 path @
# ID 265 gen 340 top level 5 path @home
```

Make sure `ls` gives you `@ @home`.

Unmount:
```
cd /

umount /mnt
```

## Edit the newly installed system

Each new line is a separate command to run.

```
mount -o defaults,subvol=@,compress=zstd:1,discard=async,noatime,commit=120 <path to root partition> /mnt
# mount -o defaults,subvol=@,compress=zstd:1,discard=async,noatime,commit=120 /dev/sda3 /mnt

for i in /dev/dev/pts/proc/sys/run; do sudo mount -B $i /mnt$i; done

sudo cp /etc/resolv.conf /mnt/etc/
```
1. **ssd**: use SSD specific options for optimal use on SSD and NVME. By default, pop os 22.04 recognizes your ssd.
2. **noatime**: prevent frequent disk writes by instructing the Linux kernel not to store the last access time of files and folders
3. **space_cache**: allows btrfs to store free space cache on the disk to make caching of a block group much quicker
4. **commit=120**: time interval in which data is written to the filesystem (value of 120 is taken from Manjaro’s minimal iso) (default=30 in fedora)
5. **compress=zstd:1**: allows to specify the compression algorithm which we want to use. btrfs provides lzo, zstd and zlib compression algorithms. Based on some Phoronix test cases, zstd seems to be the better performing candidate. zstd:1 is more lightweight while it compress lesser against zstd:9.

In case you are reinstalling over a previous Btrfs partition, the first command is likely to fail. To get it to work you'll need to add the `clear_cache` parameter, as in:

```
mount -o sudo mount -o defaults,subvol=@,compress=zstd:1,discard=async,noatime,commit=120,clear_cache <path to root partition> /mnt
# mount -o sudo mount -o defaults,subvol=@,compress=zstd:1,discard=async,noatime,commit=120,clear_cache /dev/sda3 /mnt

```

At this point the terminal might warn about `/etc/resolv.conf` being a duplicate of the target; you can safely ignore the warning.

```
chroot /mnt

nano /etc/fstab
```

Make sure you have one line starting with UUID for `/` and one for `/home`. The only difference between these two lines is that one uses the `subvol=@` parameter while the other uses `subvol=@home`. (Remove the `ssd` option if you are not using an SSD). Example:

```
UUID=18226258-bb30-4552-98c0-775ae3d74433  /  btrfs  defaults,subvol=@,compress=zstd:1,discard=async,noatime,commit=120  0  0

UUID=18226258-bb30-4552-98c0-775ae3d74433  /home  btrfs  defaults,subvol=@home,compress=zstd:1,discard=async,noatime,commit=120  0  0
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
```
```
cat /mnt/@/etc/kernelstub/configuration
# {
#   "default": {
#     "kernel_options": [
#       "quiet",
#       "splash"
#     ],
#     "esp_path": "/boot/efi",
#     "setup_loader": false,
#     "manage_mode": false,
#     "force_update": false,
#     "live_mode": false,
#     "config_rev":3
#   },
#   "user": {
#     "kernel_options": [
#       "quiet",
#       "loglevel=0",
#       "systemd.show_status=false",
#       "splash",
#       "rootflags=subvol=@"
#     ],
#     "esp_path": "/boot/efi",
#     "setup_loader": true,
#     "manage_mode": true,
#     "force_update": false,
#     "live_mode": false,
#     "config_rev":3
#   }
# }
```
Now Adjust configuration of systemd bootloader like below:

```
mount /dev/sda1 /mnt/@/boot/efi

cat /mnt/@/boot/efi/loader/entries/Pop_OS-current.conf
# title Pop!_OS
# linux /EFI/Pop_OS-UUID_of_data-root/vmlinuz.efi
# initrd /EFI/Pop_OS-UUID_of_data-root/initrd.img
# options root=UUID=UUID_of_data-root ro quiet loglevel=0 systemd.show_status=false splash rootflags=subvol=@

```
```
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
