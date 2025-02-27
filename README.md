# orin_NVMe-


```bash
orin@orin-desktop:~$ lsblk
```

######	 결과
	
![image](https://github.com/user-attachments/assets/df9ebaf8-8c62-4265-8f31-6dd29389ffa9)


	
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

	
``` bash
orin@orin-desktop:~$ sudo mkdir -p /mnt/nvme
orin@orin-desktop:~$ sudo mount /dev/nvme0n1 /mnt/nvme
orin@orin-desktop:~$ sudo rsync -axHAWXS --numeric-ids --info=progress2 / /mnt/nvme


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


