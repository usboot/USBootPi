# USBootPi v1.1 for H3

一、简介：

USBootPi是使用ARM开发板把TF卡模拟成U盘，并且支持直接挂载ISO文件来模拟USB光驱，主要用于操作系统安装和启动。

最新版本下载地址：https://github.com/usboot/USBootPi/releases

二、安装：

1、Windows上使用Win32 Disk Imager把镜像文件写到TF卡上。

2、把TF卡插到ARM开发板上，使用USB线连接PC和板子上的OTG，此时板子已经启动。

3、第一次启动，PC端会识别出一块USB硬盘（就是TF卡），Windows下使用磁盘管理工具把剩余空间创建新分区并格式化（推荐使用exFAT格式）。

4、分区完毕后，安全卸载USB设备，拔掉USB线即可完成安装。

三、使用：

1、使用USB线连接PC和板子上的OTG，PC端会识别三个U盘，其中一个是BOOT盘，用来存放内核文件和配置文件。另外一个是DISK盘（安装时新创建的分区），用来存放ISO和各种磁盘镜像文件。还有一个是用于挂载DISK盘上的镜像文件。

2、usbootpi.cfg配置格式说明（注意必须是UNIX换行）：

file=xxx.iso #指定DISK盘上的镜像文件

cdrom=1 #设置按照ISO文件格式挂载

ro=1 #设置按照只读模式挂载镜像文件

disk=/dev/mmcblk0p2 #指定DISK盘所在的设备（此设置只在BOOT盘和第一个DISK盘有效）

disk_ro=1 #设置DISK盘为只读模式（此设置只在BOOT盘和第一个DISK盘有效）

disk2=/dev/mmcblk0p1 #指定DISK2盘所在的设备（默认是BOOT盘，此设置只在BOOT盘有效）

disk2_ro=1 #设置DISK2盘为只读模式（此设置只在BOOT盘有效）

3、usbootpi.cfg使用模式说明（逻辑稍微复杂一点）：

出于安全考虑，BOOT盘初始为只读模式。想要修改配置文件，一是可以使用读卡器对BOOT盘上的文件进行修改，还可以把配置文件复制到DISK盘进行修改。位于DISK盘的配置文件其中disk2开头的参数无效。

通过修改disk参数配置第二个DISK盘（比如U盘设备/dev/sda1，注意这里不是DISK2盘），第二个DISK盘还可以有配置文件，但配置文件中的disk开头的参数都无效。

参数file,cdrom,ro是后面的配置文件覆盖前面的配置文件；而disk开头的参数则是前面的配置文件覆盖后面的配置文件。如果把DISK盘设置为只读，可以通过修改BOOT盘里的配置把DISK盘的只读去掉。

4、把镜像文件复制到DISK盘上，然后修改DISK盘上的usbootpi.cfg文件指定挂载镜像文件名及其相关配置即可（注意要安全卸载USB设备）。

5、插入要安装系统的PC上，选择启动方式，就可以看到模拟的USB光驱或磁盘了。

6、DISK盘也可以做成启动U盘，这样就又多了一种启动方式。还可以使用插入U盘做DISK盘，这样比TF卡的速度能快一些。


四、注意事项：

1、DISK盘格式化成FAT32，就不能挂载大于4GB的ISO文件（比如Win10 x64的ISO文件）。建议格式化成exFAT格式（NTFS格式可能会导致文件系统Log没有写入正确而无法按照读写模式挂载文件镜像）。如果只在Linux下使用，也可以格式化成ext等格式。

2、复制镜像文件到DISK盘时，由于TF的写入速度较慢，需要等一会儿再断电才能保证数据都写入到TF卡上了。

五、原理说明：

1、基本原理就是使用Linux内核的USB Gadget的Mass Storage功能。但实际测试中，原有的代码不支持大ISO文件。目前已经对遇到的问题进行了改进，可以使用Win10 x64的ISO文件进行安装。

2、由于系统内核启动需要一点时间（已经做了一些优化），因此插入USB线时，需要等待10秒左右PC可以识别USB设备。

六、版本历史：

v1.1 支持exFAT格式；改进配置文件格式和使用方法；进一步优化内核文件大小；BOOT盘初始为只读模式。

v1.0 初始版本，支持NanoPi M1。
