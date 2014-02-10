======================================
Build minimal Debian wheezy for Aakash
======================================


Hardware requirements
----------------------

#. sdcard of 1 GB or above

#. Aakash-2 tablet (Allwinner A13 tablet)

#. A USB hub, keyboard and mouse (optional)

A similar `compatible board <https://github.com/androportal/OLINUXINO>`_ (optional)


Setting up software toolchain
-----------------------------

#. Install cross compiler *gcc-arm-linux-gnueabihf* (on Ubuntu or its derivatives).
   For Debian include this repo ``http://emdebian.org/~thibg/repo/``


#. Other required programs ::

	apt-get install build-essential dpkg-dev kernel-wedge make automake 
    checkinstall git u-boot-tools debootstrap qemu-user-static minicom

#. You may also need *adb* (Android debug bridge) to extract ``script.bin``
   from Android installation of your Aakash tablet. The ``script.bin`` file is
   located in ``nanda`` partition of internal NAND flash. You can mount ``/dev/block/nanda``
   partition inside Android and pull required file to your local filesystem. 
   This file will be used later. 

Steps to build U-boot
---------------------


COMPILING UBOOT
^^^^^^^^^^^^^^^

1. Clone the repository by ::
    
        git clone -b sunxi https://github.com/androportal/uboot-allwinner.git


#. ``cd`` into ``uboot-allwinner`` ::

        cd uboot-allwinner

#.  To compile U-boot ::

        make a13_olinuxino CROSS_COMPILE=arm-linux-gnueabihf-


#.  After successful compilation ``u-boot.bin`` will be available at root of the
    directory and ``sunxi-spl.bin`` will be in ``spl`` directory
    


Transferring U-boot to sdcard
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**SDCARD layout**

=========      =========   =====================                                 
 Start            size          usage                                 
=========      =========   =====================                                 
0                 8KB         Unused                                           
8                24KB       Initial SPL loader                                            
32              512KB       u-boot                                       
544             128KB       environment                                             
672             352KB       reserved                                           
1024              --        free for partitions                           
=========      =========   =====================

|

1. Insert ``sdcard`` in card reader or MMC reader available in laptops/netbooks


#. Backup all your sdcard data, and issue the following commands carefully, replace 
   *X* in /dev/sdX with suitable letter(a lower case alphabet) alloted for your
   sdcard, remember *X* will never be *a* if you have a hard disk installed, so keep
   in mind it must be something like ``/dev/sdb`` or ``/dev/sdc`` etc. Please check
   twice and if you are still unsure then do not perform these steps ::

        sudo fdisk -u=sectors /dev/sdX

#. First clear the previous u-boot and its configurations, if any::

		sudo dd if=/dev/zero of=/dev/sdX bs=1024 seek=544 count=128

#. Now from same ``uboot-allwinner`` directory issue these commands, again replace 
   X with suitable value, now to write ``sunxi-spl.bin`` to sdcard issue::

        sudo dd if=spl/sunxi-spl.bin of=/dev/sdX bs=1024 seek=8

   Similarly to burn ``u-boot.bin`` issue ::

        sudo dd if=u-boot.bin of=/dev/sdX bs=1024 seek=32

At this point we have a bootable sdcard ready. Get kernel and rootfs to make a usable
Linux for your tablet.

|
    
Steps to build kernel
---------------------

Compiling kernel
^^^^^^^^^^^^^^^^


1. Clone the repository by ::
    
        git clone https://github.com/androportal/linux-sunxi.git


#. ``cd`` into ``linux-sunxi`` ::

        cd linux-sunxi


#. Checkout to desired branch, it this case ``sunxi-3.0``::

        git checkout sunxi-3.0

#. Compile the a13_configuration(optional, not required if you are using our config file) ::

        make ARCH=arm a13_defconfig


#. Download the latest *.config* file from `here <https://raw.github.com/androportal/linux-sunxi/sunxi-3.0/.config>`_ 
   and keep it in the root of the kernel source code directory


#. A13-OLinuXino customization can be done using(optional) ::

        make ARCH=arm menuconfig


#. To compile kernel::

        make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- uImage

#. To make kernel modules ::
        
        make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=out modules


#. To install modules in right path ::

        make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=out modules_install


#.  After successful compilation ``uImage`` will be available at ``arch/arm/boot/uImage``.
    Kernel modules are available at ``out/lib/modules/3.0.76+/`` 
    


Copy kernel & modules to sdcard
-------------------------------

1. Insert ``sdcard`` in card reader or MMC reader available in laptops/netbooks


#. Backup all your sdcard data, and issue the following commands carefully, replace 
   *X* in /dev/sdX with suitable letter(a lower case alphabet) alloted for your
   sdcard, remember *X* will never be *a* if you have a hard disk installed, so keep
   in mind it must be something like ``/dev/sdb`` or /``dev/sdc`` etc. Please check
   twice and if you are still unsure leave this README right now otherwise you may 
   end up with non bootable machine::

        sudo fdisk -u=sectors /dev/sdX


#. Then create partition for kernel and filesystem in your sdcard, from 
   inside ``fdisk`` prompt type ``p`` to list all partitions ::

        Command (m for help): p

#. Now press ``d`` to delete your partition. If you have multiple partitions use partition number
   to delete each partition as shown by fdisk utility::
        
        Command (m for help): d

#. Type ``n`` for new partion(hit enter) and then mention ``p`` for primary partition, hit 
   enter to continue with default starting sector(2048), enter again for last sector and  
   mention ``+16M``, this partition will be later formatted with vfat for kernel::

        Command (m for help): n
        Partition type:
           p   primary (0 primary, 0 extended, 4 free)
           e   extended
        Select (default p): p
        Partition number (1-4, default 1): 
        Using default value 1
        First sector (2048-7744511, default 2048): 
        Using default value 2048
        Last sector, +sectors or +size{K,M,G} (2048-7744511, default 7744511): +16M
 
#. The sdcard is assumed to be 4gb size, similarly create 2nd primary partition
   using remaining disk space which will serve as filesystem::

        Command (m for help): n
        Partition type:
           p   primary (1 primary, 0 extended, 3 free)
           e   extended
        Select (default p): p
        Partition number (1-4, default 2): 
        Using default value 2
        First sector (34816-7744511, default 34816): 
        Using default value 34816
        Last sector, +sectors or +size{K,M,G} (34816-7744511, default 7744511): 
        Using default value 7744511

#. (Optional) If you intend to use ``swap`` partion, leave atleast ~300MB in 
   previous step and create a 3rd primary partition.

#. Now as we have created 2 primary partitions now let's write(w) the changes 
   to disk. For that issue::

        Command (m for help): w

#. As partition is completed, we need to format them respectively, the first 
   partition must be vfat so that uboot can read kernel::

        sudo mkfs.vfat /dev/sdX1

   For rootfs, create ext4 partition::

        sudo mkfs.ext4  /dev/sdX2
        
        
#. Now mount vfat partition to some mount point(directory)::

        sudo mount -t vfat /dev/sdX1 /mnt/boot


   copy kernel to ``/mnt/boot``, assuming that you are still in ``linux-sunxi`` directory::

        sudo cp arch/arm/boot/uImage /mnt/boot

   
   Also copy ``script.bin`` to same directory::

        sudo cp script.bin /mnt/boot

   
   Now, umount /mnt/boot, before that you may want to ``sync`` so that any 
   remaining buffers are written to disks::

        sudo sync

   
   Finally unmount::

        sudo umount /mnt/boot


At this point we have a sdcard ready with kernel. Burn U-boot and copy rootfs to make a usable
Linux for your tablet. 


Step to make file system
------------------------

#. Make required directories ::

	mkdir -p debian-on-aakash/rootfs && cd debian-on-aakash

#. Download required minimal packages for armhf Wheezy ::

	sudo debootstrap --verbose --arch armhf --variant=minbase --foreign stable
    rootfs http://ftp.debian.org/debian

#. Copy ``qemu-arm-static`` to run ARM chroot on x86 ::

	sudo cp /usr/bin/qemu-arm-static rootfs/usr/bin/


#. Now download this simple script `ch-mount.sh
   <https://github.com/psachin/bash_scripts/blob/master/ch-mount.sh>`_ to perform
   chroot easily. Please read this script to get any further help ::

	sudo bash ch-mount.sh -m rootfs/

#. **Inside chroot, 1st time** ::

	/debootstrap/debootstrap --second-stage 

#. Set source lists ::

	cat <<END > /etc/apt/sources.list

   And enter the following ::

    deb http://ftp.us.debian.org/debian stable main contrib
	deb http://ftp.debian.org/debian/ wheezy-updates main contrib
	END

#. Now update the repositories ::

	apt-get update

#. Now exit from **chroot** ::

	exit

#. Now unmount proc, dev, and sys using same script::

	sudo bash ch-mount.sh -u rootfs

#. Now again chroot ::

	sudo bash ch-mount.sh -m rootfs

#. **Inside chroot 2nd time** ::

	export LANG=C

#. Install other essential packages :: 

	apt-get install apt-utils dialog

#. Install languages :: 
	
	apt-get install locales

#. Choose ``en_US.UTF-8`` when prompted by the output ::

	dpkg-reconfigure locales

#. Set the default language ::

	export LANG=en_US.UTF-8

#. Install ``xorg``, ``lxde-core`` and other supporting packages ::

	apt-get install dhcp3-client udev netbase ifupdown iproute openssh-server 
    iputils-ping wget net-tools ntpdate vim.tiny less nano bash-completion ssh 
    ethtool florence alsa-utils hal wicd netsurf lxde-core xorg 

#. Install light display manager ::

	apt-get install lightdm --no-install-recommends

#. Create a user and give permissions ::

	adduser aakash && addgroup aakash adm && addgroup aakash sudo && addgroup aakash audio

#. Enable auto-login for user ``aakash`` by changing ``autologin-user=aakash`` 
   in ``/etc/lightdm/lightdm.conf``

#. Create necessary directories for kernel modules and firmwares ::

	mkdir /lib/modules  /lib/firmware/rtlwifi

#. Set the hostname ::

	echo debian > /etc/hostname

#. Also in ``/etc/hosts file`` ::
		
	echo '127.0.1.1       debian' >> /etc/hosts	

#. Download rtl8192cufw.bin from this
   `page <http://mirrors.arizona.edu/raspbmc/downloads/bin/lib/wifi/rtlwifi/>`_,
   and copy  ``rtl8192cufw.bin`` to ``/lib/firmware/rtlwifi`` directory

#. Insert given modules after boot ::

	echo 8192cu  >> /etc/modules
	echo ft5x_ts >> /etc/modules

#. Exit the chroot ::

	exit

#. Unmount proc, dev, and sys using ch-mount script ::

	sudo bash ch-mount.sh -u rootfs


