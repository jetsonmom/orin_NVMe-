# orin_NVMe-


``` bash
orin@orin-desktop:~$ lsblk

```


<pre>결과
	
NAME         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0          7:0    0    16M  1 loop 
loop1          7:1    0  38.7M  1 loop /snap/snapd/23546
loop2          7:2    0  68.8M  1 loop /snap/core22/1752
loop3          7:3    0     4K  1 loop /snap/bare/5
loop4          7:4    0  64.7M  1 loop /snap/cups/1069
loop5          7:5    0  91.7M  1 loop /snap/gtk-common-themes/1535
loop6          7:6    0 175.3M  1 loop /snap/chromium/3046
loop7          7:7    0 493.5M  1 loop /snap/gnome-42-2204/201
mmcblk0      179:0    0  59.7G  0 disk 
├─mmcblk0p1  179:1    0  58.2G  0 part /
├─mmcblk0p2  179:2    0   128M  0 part 
├─mmcblk0p3  179:3    0   768K  0 part 
├─mmcblk0p4  179:4    0  31.6M  0 part 
├─mmcblk0p5  179:5    0   128M  0 part 
├─mmcblk0p6  179:6    0   768K  0 part 
├─mmcblk0p7  179:7    0  31.6M  0 part 
├─mmcblk0p8  179:8    0    80M  0 part 
├─mmcblk0p9  179:9    0   512K  0 part 
├─mmcblk0p10 179:10   0    64M  0 part /boot/efi
├─mmcblk0p11 179:11   0    80M  0 part 
├─mmcblk0p12 179:12   0   512K  0 part 
├─mmcblk0p13 179:13   0    64M  0 part 
├─mmcblk0p14 179:14   0   400M  0 part 
└─mmcblk0p15 179:15   0 479.5M  0 part 
zram0        252:0    0   635M  0 disk [SWAP]
zram1        252:1    0   635M  0 disk [SWAP]
zram2        252:2    0   635M  0 disk [SWAP]
zram3        252:3    0   635M  0 disk [SWAP]
zram4        252:4    0   635M  0 disk [SWAP]
zram5        252:5    0   635M  0 disk [SWAP]
nvme0n1      259:0    0 465.8G  0 disk 
<pre>
	
```bash
sudo mkfs.ext4 /dev/nvme0n1
```

[sudo] password for orin: 
mke2fs 1.46.5 (30-Dec-2021)
Discarding device blocks: done                            
Creating filesystem with 122096646 4k blocks and 30531584 inodes
Filesystem UUID: 0c108b50-05b8-496d-9f8f-27f10d294751
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
	102400000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done     
<pre>
	
``` bash
orin@orin-desktop:~$ sudo mkdir -p /mnt/nvme
orin@orin-desktop:~$ sudo mount /dev/nvme0n1 /mnt/nvme
orin@orin-desktop:~$ sudo rsync -axHAWXS --numeric-ids --info=progress2 / /mnt/nvme

```
	``` bash
sudo gedit /mnt/nvme/etc/fstab
orin@orin-desktop:~$ cat /mnt/nvme/etc/fstab
```
	<pre>
# /etc/fstab: static file system information.
#
# <file system> <mount point> <type> <options> <dump> <pass>
# 기존 eMMC 루트 파일 시스템 주석 처리
# /dev/root            /                     ext4           defaults                                     0 1
# NVMe를 루트 파일 시스템으로 설정
UUID=bd14adc2-04ff-4b84-bcb0-815102a019b3 / ext4 defaults 0 1
# UEFI 부팅을 사용하는 경우, 부트 파티션 유지
UUID=3FFC-5543 /boot/efi vfat defaults 0 1
<pre>
	
orin@orin-desktop:~$ sudo mount --bind /dev /mnt/nvme/dev

<pre>
sudo mount --bind /proc /mnt/nvme/proc
sudo mount --bind /sys /mnt/nvme/sys
sudo mount /dev/mmcblk0p10 /mnt/nvme/boot/efi
sudo chroot /mnt/nvme
root@orin-desktop:/# update-initramfs -u -k all
exit
<pre>

orin@orin-desktop:~$ sudo umount /mnt/nvme/boot/efi
<pre>
	``` bash
sudo umount /mnt/nvme/dev
sudo umount /mnt/nvme/proc
sudo umount /mnt/nvme/sys
sudo umount /mnt/nvme
sudo reboot
```


