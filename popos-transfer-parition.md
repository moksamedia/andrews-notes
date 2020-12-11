Helpful Links
- http://www.robotthoughts.com/2018/12/22/moving-the-root-partition-to-a-new-disk-in-ubuntu-18-10-general-grub-chicanery/
- https://askubuntu.com/questions/1035510/is-grub-needed-when-copying-ubuntu-to-a-new-drive
- https://support.system76.com/articles/bootloader/

General Steps
1) Get UUID of root parition and PARTUUID of /boot/efi
2) Use rsync, dd, or cp to copy file from old partition to new one
3) Update etc/ftsab on new parition with UUID of root and PARTUUID of /boot/efi
4) Follow instructions from PopOS to repair boot for systemd-boot and EFI

### 1) Get UUID of root parition and PARTUUID of /boot/efi

The NEW /boot/efi partition
```bash
sudo blkid /dev/nvme0n1p1
/dev/nvme0n1p1: UUID="D5FD-09F4" TYPE="vfat" PARTUUID="59079acd-0f63-4452-bb00-f25e94cac419"
```

The NEW root partition
```bash
sudo blkid /dev/nvme0n1p3
/dev/nvme0n1p3: LABEL="PopOs" UUID="d011a62d-1039-4ad3-8c8a-b749a809e238" TYPE="ext4" PARTUUID="b667d818-f0fd-4d67-bb2d-0515945b0612"
```

Save these values for step 3

### 2) Use rsync, dd, or cp to copy file from old partition to new one

```bash
sudo rsync -axHAWXS --numeric-ids --info=progress2 / /mnt/newpop/
```

### 3) Update etc/ftsab on new parition with UUID of root and PARTUUID of /boot/efi


These values need to be updated to reflect the UUID and PARTUUID of the new drive and partition
/etc/fstab
```
# <file system> <mount point> <type> <options> <dump> <pass>

PARTUUID=59079acd-0f63-4452-bb00-f25e94cac419	/boot/efi	vfat	umask=0077	0	0
UUID=d011a62d-1039-4ad3-8c8a-b749a809e238	/	ext4	noatime,errors=remount-ro	0	0
```

### 4) Follow instructions from PopOS to repair boot for systemd-boot and EFI

https://support.system76.com/articles/bootloader/

systemd-boot
EFI Boot

Most computers sold after 2014 use UEFI mode. If boot, esp is listed under flags, the system is installed in UEFI mode. You can also use this command to see if the OS is installed in UEFI mode:

[ -d /sys/firmware/efi ] && echo "Installed in UEFI mode" || echo "Installed in Legacy mode"

Run these commands based on what type of disk you have:
For NVMe Drives:

```bash
sudo mount /dev/nvme0n1p3 /mnt
sudo mount /dev/nvme0n1p1 /mnt/boot/efi
for i in /dev /dev/pts /proc /sys /run; do sudo mount -B $i /mnt$i; done
sudo cp /etc/resolv.conf /mnt/etc/
sudo chroot /mnt
apt install --reinstall linux-generic linux-headers-generic
update-initramfs -c -k all
exit
sudo bootctl --path=/mnt/boot/efi install
```

### efibootmgr

List boot items
```bash
efibootmgr
efibootmgr -v
```

Delete boot item
```bash
 efibootmgr -b <item #> -B
```

### Recovery Partition

Great tutorial on adding recovery partition: https://baez.link/add-recovery-to-your-pop-_os

Another thread: https://pop-planet.info/forums/threads/missing-recovery-option-on-boot-menu.211/

popos-upgrade utility: https://github.com/pop-os/upgrade

### /boot/efi full

Use `du` to see what is using up all the space. Probably things in the `/EFI` subdir.

Look in `loader/loader.conf` and `loader/entries` to see what entries are being used and not being used.

Delete any unneeded entries and unused subdirectories in `/EFI`

