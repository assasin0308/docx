# centos6.5 挂载数据盘

[TOC]

1. 首先查看磁盘使用情况

   ```scss
   df -h
   //显示如下
   Filesystem      Size  Used Avail Use% Mounted on
   /dev/xvda1       40G   31G  6.7G  83% /
   tmpfs           3.9G     0  3.9G   0% /dev/shm
   ```

2. 查看磁盘分区

   发现有一个500G的/dev/xvdb的`Disk identifier: 0x00000000`, 说明未挂载.

   ```scss
   fdisk -l
   Disk /dev/xvda: 42.9 GB, 42949672960 bytes
   255 heads, 63 sectors/track, 5221 cylinders
   Units = cylinders of 16065 * 512 = 8225280 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disk identifier: 0x00078f9c

       Device Boot      Start         End      Blocks   Id  System
   /dev/xvda1   *           1        5222    41940992   83  Linux

   Disk /dev/xvdb: 549.8 GB, 549755813888 bytes
   255 heads, 63 sectors/track, 66837 cylinders
   Units = cylinders of 16065 * 512 = 8225280 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disk identifier: 0x00000000
   ```

3. 分区

   切换到root账户；

   运行 **fdisk /dev/xvdb**，对数据盘进行分区。根据提示，依次输入 n，p，1，两次回车，wq，分区就开始了。

   n：代表new，新建分区

   p：代表primary，设为主分区

   1：代表分区数1个

   第一次回车：代表分区开始位置使用默认的从1号扇区开始。

   第二次回车，代表分区结束位置使用默认的最后一个扇区。

   (意思就是：把整块数据偏分为1个主分区，创建的分区分区名称就是xvdb1)

   wq，代表保存并执行以上分区。分区就开始了！

   再次使用fdisk -l就可以看到新的分区了，如下：

   ```scss
   Disk identifier: 0xda90ec5c

       Device Boot      Start         End      Blocks   Id  System
   /dev/xvdb1               1       66837   536868171   83  Linux
   ```

   分区成功, 接下来开始挂载, 否则是无法使用的.



4. 挂载

   linux的所有硬件都是用文件来表示的。挂载数据盘，其实就是指定数据盘的挂载点。也就是在配置文件中把数据盘指向某个文件夹，这就是挂载了。

   "mkdir /wwwroot"在根目录下创建wwwroot文件夹用来放网站文件。你也可以不用创建，使用现有的文件夹也可以的。

   ```scss
   mount /dev/xvdb1 /wwwroot
   ```

   提示mount: you must specify the filesystem type

   使用df -T -h 查看文件系统格式

   ```Scss
   df -T -h
   Filesystem     Type   Size  Used Avail Use% Mounted on
   /dev/xvda1     ext4    40G   31G  6.7G  83% /
   tmpfs          tmpfs  3.9G     0  3.9G   0% /dev/shm
   ```

   格式化该磁盘

   ```Scss
    mkfs.ext4 /dev/xvdb1
   ```

   格式化完成后尝试重新挂载, 挂载成功.

5. 硬盘开机自动挂载

   ```
   
   ```
