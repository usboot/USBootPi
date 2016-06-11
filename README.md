# USBootPi v1.3 for H3

* **Introduction**

USBootPi uses the ARM single board to emulate the USB flash disk and also emulate the USB cdrom by mounting the ISO file. It can be used for the OS installation and booting.

Download the latest version: https://github.com/usboot/USBootPi/releases

* **Installation**

1. Write the image file into the MicroSD card by Win32 Disk Imager on the Windows or dd on Linux.
2. Insert the MicroSD card into the ARM single board and connect PC and OTG on the board with a USB cable. At the moment the board has been started.
3. At the first time, PC recognizes a USB hard disk (just be the MicroSD card). Create a new partition with the blank space and format it with the "DISK" label(exFAT is recommended, the cluster size is 64KB) by Disk Management on Windows.
4. Unmount the USB device after the formatting and unplug the USB cable to complete this installation.

* **Usage**

1. After connecting PC and OTG on the board with a USB cable, PC recognizes three USB disks. The disk labeled "BOOT" is used to store the kernel files and the configuration files. The disk labeled "DISK" is used to store the ISO and other image files. The third disk is used to mount the image file on the disk labeled "DISK".
2. usbootpi.cfg configuration format (MUST be UNIX line endings):

  * file=xxx.iso # the image file on the disk labeled "DISK"
  * cdrom=1 # mount the image file as the ISO file
  * ro=1 # mount the image file as read-only
  * disk=/dev/mmcblk0p2 # the device of the disk labeled "DISK" (Available on the disk labeled "BOOT" and the first disk labeled "DISK")
  * disk_ro=1 # DISK disk set to read-only mode (Available on the disk labeled "BOOT" and the first disk labeled "DISK")
  * disk2=/dev/mmcblk0p1 # the device of the disk2 (The default value is the device of the disk labeled "BOOT", Available on the disk labeled "BOOT")
  * disk2_ro=1 # mount the disk2 as read-only (Available on the disk labeled "BOOT")

3. usbootpi.cfg usage pattern(a little more complicated logic):

  * For security reasons, the disk labeled "BOOT" is read-only initially. You can use the card reader to modify the files on the disk labeled "BOOT" and can also copy usbootpi.cfg into the disk labeled "DISK" and modify it, but the settings of the begin with "disk2" are invalid. 
  * If you modify the "disk" setting(such as /dev/sda1, cannot be the disk2), usbootpi.cfg is also available on the second disk labeled "DISK", but the settings of the begin with "disk" are invalid.
  * The "file", "cdrom", "ro" settings of the following configuration file overwrite the previous configuration files. The settings of the begin with "disk" of the previous configuration file overwrite the following configuration files. If the "disk_ro" setting on the disk labeled "DISK" is 1, you can modify the "disk_ro" setting on the disk labeled "BOOT" to be 0 for removing the read-only mode of the disk labeled "DISK".

4. Copy the image files into the disk labeled "DISK" and then modify usbootpi.cfg on the disk labeled "DISK" to specify the mounting image file(NEED unmount USB devices safely).
5. Insert the PC to be installed OS and power on the PC. You can select the emulated USB flash drive or cdrom as the booting device.
6. The disk labeled "DISK" can also be made as the bootable USB flash disk. You can also use the USB flash disk inserted on the board as the disk labeled "disk". Usually the USB flash disk is faster than the MicroSD card.
7. RAID0 usage: When the "disk" setting has the multiple devices(e.g., disk=/dev/sda /dev/sdb, available only on the disk labeled "BOOT"), the multiple devices are assambled as one RAID0 device and then the real r/w speeds are improved. But now the maximum r/w speeds of the OTG are 24MB/s reads and 17MB/s writes.

* **Notes**

1. The exFAT filesystem is recommended for the disk labeled "DISK". The NTFS filesystem may cause that the disk labeled "DISK" cannot be mounted as read-write because the $LogFile is not written correctly. The FAT32 filesystem does not support the above 4GB ISO file(such as the Win10 x64 ISO file). If you use it only under Linux, you can also format with ext or other filesystems.
2. Since it is slower to write the MicroSD card, you need to wait a while after coping the big file into the disk labeled "DISK". 

* **Principle**

1. USBootPi uses USB Gadget for Mass Storage function in the Linux kernel. However, the original code does not support the large ISO file. Now all issues have been fixed and improved, you can use the Win10 x64 ISO file to install.
2. Since the USBootPi kernel boot will take some time (I have made some optimization), you insert the USB cable and then wait less 10 seconds, PC can recognize the USB disks.

* **History**

- v1.3
  - Supported RAID0.

- v1.2
  - Used armbian to compile the new u-boot and kernel version.
  - Reduced the size of the kernel image again.
  - The boot speed is about 2 seconds faster than v1.1.

- v1.1
  - Supported exFAT format.
  - Improved configuration file format and usage.
  - Optimize the kernel file size.
  - Set the disk labeled "BOOT" to be read-only initially.

- v1.0
  - initial release, support NanoPi M1.
