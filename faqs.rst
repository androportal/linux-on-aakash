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

#. How to contribute ?

   Write to us, you can do lot of fancy stuff like building custom
   apps, fixing bugs, cross compiling, writing good documents, manuals
   etc


`Back to main page <https://github.com/androportal/linux-on-aakash/blob/master/README.rst>`_ 

Next topic: `TODO <https://github.com/androportal/linux-on-aakash/blob/master/todo.rst>`_
