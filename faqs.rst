==========================
Frequently asked questions
==========================

#. How to get this device ? 


   We appreciate your interest, if you are a student from India you
   will get it soon. Already more than 20k devices have been shipped
   to different colleges, please don't raise any questions regarding
   availability


#. Can I run unity desktop ?


   In ``Ubuntu 12.04`` builds you can actually install
   ``unity-2d-panel`` but it will be very less resposive compared to
   the custom lxde version which comes default with this device. The
   team is considering Ubuntu 12.10 because of many stability issues
   in lxde, in that case ``unity-2d`` will not be available.

#. Will it run from internal NAND flash ? How it will affect my stock
   android ?


   It may run from internal NAND flash, but for now we are
   concentrating only on sdcard version. The Linux boots directly from
   sdcard without touching any android filesystem from NAND

#. I don't find **script.bin** file in `linux-sunxi/arch/arm/boot/`
   directory after kernel compilation ?

   
   Actually **script.bin** has nothing to do with kernel compilation. It
   is not a kernel generated binary file. This file is device
   specific, you may find this file in `/dev/block/nanda` of the
   device.

   You can extract this file using by mountind **nanda** block
   somewhere in sdcard. To extract, please follow this procedure
   
   * first, enter the android shell using `adb shell` and create a
     directory `nanda` in internal SDcard ::

       adb shell
       mkdir /mnt/sdcard/nanda

   * Now mount `/dev/block/nanda` to `/mnt/sdcard/nanda` using ::

       mount -t vfat /dev/block/nanda /mnt/sdcard/nanda

   * Now open new terminal and pull **script.bin** from
     `/mnt/sdcard/nanda` using ::

       adb pull /mnt/sdcard/nanda/script.bin

   * Go back to your android shell and unmount **nanda** block ::
       
       umount /mnt/sdcard/nanda

   Primarily as the extension says, it is a binary file but you can
   change it using **bin2fex** and then again **fex2bin**.  You can
   get this tool from https://github.com/linux-sunxi/sunxi-tools. More
   info about script.bin can be found http://linux-sunxi.org/Fex_Guide

#. I'm getting following error when I do ``adb devices``? ::
     
     List of devices attached
     ????????????    no permissions
   

   We have two work-arounds for that, 

   * login as root and repeat the command again ::

       adb devices

     if it still gives you error, then you have to kill the **adb**
     process using ::
   
       killall adb
   
   * Second is the recommended one. You should make your device to be
     identified by your system. Every device has it own unique `Vendor
     ID`. This ID should be added to you system **udev** rules. To do
     this type ::
       
       lsusb

     this will output something like ::
       
       Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
       Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
       Bus 001 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
       Bus 002 Device 002: ID 8087:0024 Intel Corp. Integrated Rate Matching Hub
       Bus 001 Device 004: ID 10f1:1a36 Importek 
       Bus 002 Device 007: ID 0cf3:3005 Atheros Communications, Inc. AR3011 Bluetooth
       Bus 002 Device 008: ID 19d2:1351 ZTE WCDMA Technologies MSM 

     where the last line is my android phone attached via USB. Notice
     the string **19d2:1351** wherein, the `19d2` is my vendor ID.

     Now create a file ``/etc/udev/rules.d/51-android.rules`` if it
     does not exist and copy below line to the file ::

       SUBSYSTEM=="usb", ATTR{idVendor}=="19d2", MODE="0666", GROUP="plugdev"

     replace ``192d`` with your device's vendor ID. Save and close the
     file. You need to have root access to create/edit this file. Now
     your system should be able to detect your android device as a
     normal user.

     For more info, please visit
     http://developer.android.com/tools/device.html

#. How to contribute ?

   Write to us, you can do lot of fancy stuff like building custom
   apps, fixing bugs, cross compiling, writing good documents, manuals
   etc


`Back to main page <https://github.com/androportal/linux-on-aakash/blob/master/README.rst>`_ 

Next topic: `TODO <https://github.com/androportal/linux-on-aakash/blob/master/todo.rst>`_
