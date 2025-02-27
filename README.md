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
```

```bash
sudo gedit /mnt/nvme/etc/fstab
```
아래 내용 집어넣고 save

```
UUID=bd14adc2-04ff-4b84-bcb0-815102a019b3 / ext4 defaults 0 1
# UEFI 부팅을 사용하는 경우, 부트 파티션 유지
UUID=3FFC-5543 /boot/efi vfat defaults 0 1
```
``` 
orin@orin-desktop:~$ cat /mnt/nvme/etc/fstab
```
	
##### /etc/fstab: static file system information.



 ```bash
orin@orin-desktop:~$ sudo mount --bind /dev /mnt/nvme/dev
sudo mount --bind /proc /mnt/nvme/proc
sudo mount --bind /sys /mnt/nvme/sys
sudo mount /dev/mmcblk0p10 /mnt/nvme/boot/efi
sudo chroot /mnt/nvme
root@orin-desktop:/# update-initramfs -u -k all
exit
```
```bash
orin@orin-desktop:~$ sudo umount /mnt/nvme/boot/efi
```
```bash
sudo umount /mnt/nvme/dev
sudo umount /mnt/nvme/proc
sudo umount /mnt/nvme/sys
sudo umount /mnt/nvme
sudo reboot
```

```bash
orin@orin-desktop:~$ lsblk
```
![image](https://github.com/user-attachments/assets/4e20025b-c33b-442b-befa-3f77bd8f7114)

```bash
orin@orin-desktop:~$ sudo mkdir -p /mnt/data
```
[sudo] password for orin: 

```bash
orin@orin-desktop:~$ sudo mount /dev/nvme0n1 /mnt/data
```
![image](https://github.com/user-attachments/assets/c676da3f-469f-481b-93a7-a0d9ebeeb2a5)

```bash
orin@orin-desktop:~$ sudo parted /dev/nvme0n1 mkpart primary ext4 0% 100% 
```
Information: You may need to update /etc/fstab.

```bash
orin@orin-desktop:~$ sudo mkfs.ext4 /dev/nvme0n1p1
```
![image](https://github.com/user-attachments/assets/a29e8614-fa94-4607-90e1-fa959d437e63)

```bash
orin@orin-desktop:~$ sudo mount /dev/nvme0n1p1 /mnt/data
orin@orin-desktop:~$ df -h /mnt/data
```
![image](https://github.com/user-attachments/assets/7d5cd95e-adc5-4a48-9e94-348c3e4e6f7b)

설치 정상이므로 

# DeepStream 설치 디렉토리 준비

```bash
orin@orin-desktop:~$ mkdir -p /mnt/data/projects
orin@orin-desktop:~$ cd /mnt/data/projects

orin@orin-desktop:~$ sudo apt update
```

# DeepStream에 필요한 종속성 설치
```bash
orin@orin-desktop:~$ sudo apt install -y \
    libssl3 \
    libjansson4 \
    libjson-glib-1.0-0 \
    libgstreamer1.0-0 \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-plugins-ugly \
    gstreamer1.0-tools \
    gstreamer1.0-libav
```
다운로드한 후 # 다운로드 폴더에서 SSD로 파일 이동
deepstream_sdk_v7.1.0 다운로드 링크.
```bash
https://catalog.ngc.nvidia.com/orgs/nvidia/resources/deepstream/files
orin@orin-desktop:/mnt/data/projects$ cd
orin@orin-desktop:~$ cd Downloads
orin@orin-desktop:~/Downloads$ ls
```
deepstream_sdk_v7.1.0_jetson.tbz2

cd /mnt/data/projects # 압축 파일 해제 tar -xvf deepstream_sdk_v7.1.0_jetson.tbz2

```bash
orin@orin-desktop:/mnt/data/projects$ tar -xvf deepstream_sdk_v7.1.0_jetson.tbz2
opt/

orin@orin-desktop:/mnt/data/projects$ ls
```
deepstream_sdk_v7.1.0_jetson.tbz2  opt

![image](https://github.com/user-attachments/assets/4bbde509-0188-4aa4-94a6-e3dad2717f7c)

```bash
sudo apt-get install tree
```
![image](https://github.com/user-attachments/assets/52950219-ea0a-4647-b147-767f8e140469)

```bash
orin@orin-desktop:/mnt/data/projects$ cp -r /mnt/data/projects/opt/nvidia/deepstream/deepstream-7.1/samples/models /mnt/data/projects/test5

orin@orin-desktop:/mnt/data/projects$ cp -r /mnt/data/projects/opt/nvidia/deepstream/deepstream-7.1/samples/configs/deepstream-app/config_infer_primary.txt /mnt/data/projects/test5

orin@orin-desktop:/mnt/data/projects$ cp -r /mnt/data/projects/opt/nvidia/deepstream/deepstream-7.1/samples/configs/deepstream-app/config_infer_secondary_vehiclemake.txt /mnt/data/projects/test5

orin@orin-desktop:/mnt/data/projects$ cp -r /mnt/data/projects/opt/nvidia/deepstream/deepstream-7.1/samples/configs/deepstream-app/config_infer_secondary_vehicletypes.txt /mnt/data/projects/test5

orin@orin-desktop:/mnt/data/projects$ cp -r /mnt/data/projects/opt/nvidia/deepstream/deepstream-7.1/sources/apps/sample_apps/deepstream-test5/configs/test5_dec_infer-resnet_tracker_sgie_tiled_display_int8.txt /mnt/data/projects/test5
```
