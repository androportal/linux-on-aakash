====================
STEPS TO BUILD UBOOT 
====================


COMPILING UBOOT
---------------

1. Clone the repository by ::
    
        git clone -b sunxi https://github.com/androportal/uboot-allwinner.git


#. ``cd`` into ``uboot-allwinner`` ::

        cd uboot-allwinner

#.  To compile uboot download and install ``Sourcery CodeBench Lite Edition``, then issue::

        make a13_olinuxino CROSS_COMPILE=arm-none-linux-gnueabi-


#.  After successful compilation ``u-boot.bin`` will be available at root of the
    directory and ``sunxi-spl.bin`` will be in ``spl`` directory
    


UBOOT INSTALLATION ON SDCARD
----------------------------

1. Insert ``sdcard`` in card reader or MMC reader available in laptops/netbooks


#. Backup all your sdcard data, and issue the following commands carefully, replace 
   *X* in /dev/sdX with suitable letter(a lower case alphabet) alloted for your
   sdcard, remember *X* will never be *a* if you have a hard disk installed, so keep
   in mind it must be something like ``/dev/sdb`` or /``dev/sdc`` etc. Please check
   twice and if you are still unsure leave this README right now otherwise you may 
   end up with non bootable machine::


        sudo fdisk -u=sectors /dev/sdX



#. Now from same ``uboot-allwinner`` directory issue these commands, again replace 
   X with suitable value, now to burn ``sunxi-spl.bin`` to sdcard issue::

        sudo dd if=spl/sunxi-spl.bin of=/dev/sdX bs=1024 seek=8

   Similarly to burn ``u-boot.in`` issue ::

        sudo dd if=u-boot.bin of=/dev/sdX bs=1024 seek=32

At this point we have a bootable sdcard readly. Get kernel and rootfs to make a usuable
Linux for your tablet. 



    