* 参考资料：https://www.cnblogs.com/gladiatorplus/category/1850060.html
* syslinux：https://wiki.syslinux.org/wiki/index.php?title=SYSLINUX
* https://wiki.archlinux.org/title/Syslinux_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
* mbr分区表:https://www.cnblogs.com/yxqxx/p/8972301.html
* grub详解：https://www.cnblogs.com/f-ck-need-u/p/7094693.html
* 关于Linux系统下Grub启动流程的讨论总结：https://www.huaweicloud.com/articles/7c823ee9d42190f093aeffbc261dff67.html
* GRUB image files: https://www.gnu.org/software/grub/manual/grub/html_node/Images.html#Images
* grub2详解(翻译和整理官方手册)：https://www.cnblogs.com/f-ck-need-u/p/7094693.html
* 分区表：https://wiki.archlinux.org/title/Partitioning_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#GUID_分区表

# 一、deepin-boot-maker
## 1、刻录镜像
### 1.1 判断U盘分区是否有足够空间(进度0%-5%)
* 如果勾选了格式化，判断公式：```镜像大小 <= U盘分区总大小```
* 如果没勾选格式化，判断公式，```镜像大小 <= U盘分区剩余大小```

### 2.1 检查iso（进度5%-10%）
* 检查iso方法：```7z t ~/Downloads/uniontechos-desktop-20-professional-1040-amd64-20210708-2122.iso```。（感觉没啥卵用）

### 2.2 格式化磁盘分区（进度10%-15%）
* 格式化方法：```sudo mkfs.vfat  /dev/sdb1```

### 2.4 安装bootloader(进度15%-20%)
* ```sudo syslinux -i /dev/sdb1```
* ```sudo dd if=/usr/lib/SYSLINUX/mbr.bin of=/dev/sdb bs=440 count=1```
* ```sudo sfdisk /dev/sdb -A 1```
* ```sudo fsck -y /dev/sdb1```
* ```sudo fatlabel /dev/sdb1 UT000205```


### 2.5 解压镜像（进度20%-80%）
* 解压方法：```sudo 7z x -y ~/Downloads/uniontechos-desktop-20-professional-1040-amd64-20210708-2122.iso -o/media/uos/UT000205 -bsp2```

### 2.6 同步磁盘（进度80%-90%）
* 同步磁盘方法： ```sync```

### 2.7 condif Syslinux (进度90%-100%)
* ```rm -rf /media/uos/UT000205/syslinux```
* ```mv /media/uos/UT000205/isolinux /media/uos/UT000205/syslinux```
* ```rm -rf /media/uos/UT000205/syslinux/syslinux.cfg```
* ```cp /media/uos/UT000205/syslinux/isolinux.cfg /media/uos/UT000205/syslinux/syslinux.cfg```
* ```cp /usr/lib/syslinux/modules/bios/{gfxboot.c32,chain.c32,menu.c32,vesamenu.c32,libcom32.c32,libutil.c32} /media/uos/UT000205/syslinux```

### 2.8 卸载磁盘（进度101%）
* ```sudo udisksctl unmount -b /dev/sdb1```
* ```sudo udisksctl power-off -b /dev/sdb```



# 二、ventoy
## 1、制作步骤
### 1.1、U盘分区（划分两个分区）
* format_ventoy_disk_gpt
```
设置part1的起始扇区为2048
...
parted -a none --script $DISK \
    mklabel gpt \
    unit s \
    kpart Ventoy ntfs $part1_start_sector $part1_end_sector \
    mkpart VTOYEFI fat16 $part2_start_sector $part2_end_sector \
    $vt_set_efi_type \
    set 2 hidden on \
    quit
```
* format_ventoy_disk_gpt

### 1.2 格式化（vfat格式）第一个分区（用于存放镜像文件的那个分区）


### 1.3 写磁盘开头预留的2048个扇区
* 
```dd status=none conv=fsync if=./boot/boot.img of=$DISK bs=1 count=446```

### 1.4 写入core.img.xz

### 1.5 vtoy_gen_uuid

### 1.6 挂载第二个分区（part2）到 ./tmp_mnt

### 1.7 修改第二个分区

### 1.8 卸载第二个分区


# 三、WoeUSB
* 代码路径：https://github.com/WoeUSB/WoeUSB




