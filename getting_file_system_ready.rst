========================
Step to make file system
========================

As of now we have uboot and kernel ready for Aakash, the next step is to run few apps, to do so we need Linux file system. One can use any 
distribuition from here after. We will use ubuntu 12.04 LTS. Just for the  record we have also tried Debian wheezy which also works well. 

The main war is between different desktop enviroments. With lot of testing
and real world examples we finally decided to go with LXDE. There is lot
of scope for customization with LXDE and moreover its almost 50% lighter & twice
as faster than any other desktop enviroment.

#. Insert sdcard again, download the core ubuntu 12.04.1 image from this `link <http://cdimage.ubuntu.com/ubuntu-core/releases/12.04/release/ubuntu-   core-12.04.1-core-armhf.tar.gz>`_ and save it in say /tmp directory. Extract the tar file in your sdcard's ext4 partition ::
	
	cd /media/<ext4 partition of sdcard>


   Now extract core ubuntu file system ::


	sudo tar -xvpzhf /tmp/ubuntu-core-12.04.1-core-armhf.tar.gz 



#. Copy the static qemu binary to mount arm fs in x86 architecture without invoking actual qemu emulator::

	sudo cp /usr/bin/qemu-arm-static /media/<ext4 partion of sdcard>/usr/bin/

#. Open ``/media/<ext4 partition of sdcard>/etc/apt/sources.list`` in text
   editor and add ``universe`` & ``multiverse``. A sample content
   is shown ::

	deb http://ports.ubuntu.com/ubuntu-ports/ precise main universe multiverse restricted
	deb-src http://ports.ubuntu.com/ubuntu-ports/ precise main  universe multiverse restricted

#. Now set up chroot environment ::

	sudo mount -t proc /proc /media/<ext4 partion of sdcard>/proc

	sudo mount -t sysfs /sys /media/<ext4 partion of sdcard>/sys

	sudo mount -o bind /dev /media/<ext4 partion of sdcard>/dev

	sudo mount -o bind /dev/pts /media/<ext4 partion of sdcard>/dev/pts

	
#. chroot into the file system ::

	
	sudo chroot /media/<ext4 partition of sdcard>/


#. The above two steps can be combined together as a script say,
   `ch-mount.sh <https://github.com/downloads/androportal/linux-on-aakash/ch-mount.sh>`_. From here after we will call this script to mount and un-mount fs. Please read this script to get any further help ::

	sudo ch-mount.sh -m /media/<ext4 partition of sdcard>/


#. Now we have a chroot environment with all ``proc, dev, sys, dev/pts`` mounted,
   so run update to fetch repository informations (as chroot has root prompt so
   no need to write sudo anymore) ::

	apt-get update

#. Now install english language pack to avoid locale related errors ::

         apt-get install language-pack-en-base

#. Now install minimal X environment to test our setup ::

	apt-get install vim.tiny sudo ssh net-tools ethtool \
         wireless-tools lxde lxappearence lxsession lxdm xinit xorg\
         python-gi-cairo onboard utouch rsyslog alsa-utils gnome-mplayer\
         
#. Add user and set permissions ::

	# adduser aakash

	# addgroup aakash adm
 	
	# addgroup aakash sudo

	# addgroup aakash audio

#. Open ``/etc/hostname`` file in vim.tiny editor and give a hostname, for example
   write ``aakash-arm``

#. Similarly open ``/etc/hosts`` and remove its content and add these two lines::

	127.0.0.1    localhost.localdomain localhost
	127.0.1.1    aakash-arm

#. Open ``/etc/modules`` file and add ``gt818_ts`` in a new line 

#. Open ``/etc/lxdm/lxdm.conf`` and modify it for autologin. Change the autologin
   section in the top of the file to this ::

	## uncomment and set autologin username to enable autologin
	autologin=aakash

#. Now open a new tab in your host Linux machine's terminal and copy
   ``/<path to linux kernel>/linux-sunxi/out/lib/modules/3.0.42+/`` to your arm
   ubuntu setup ::

   sudo cp -r /<path to linux kernel>/linux-sunxi/out/lib/modules/3.0.42+/   
              /media/<ext4 partition of sdcard>/lib/modules/


#. Download rtl8192cufw.bin from this
   `page <http://mirrors.arizona.edu/raspbmc/downloads/bin/lib/wifi/rtlwifi/>`_,
   and create a directory as ``/lib/firmware/rtlwifi`` in ubuntu chroot, copy
   ``rtl8192cufw.bin`` inside ``rtlwifi`` directory

#. By now we have basic elements set to give a trial run to our OS. Type
   ``exit`` in chroot environment to get back to Ubuntu x86 host prompt, now
   run un-mount script ::

	sudo ch-mount.sh -u /media/<ext4 partition of sdcard>/

   unmount both the partitions (fat32 and ext4) from your machine, confirm with
   ``mount`` command to check nothing from sdcard is mounted

#. Remove the sdcard and insert it in your tablet, power on to get ubuntu 12.04
   lxde desktop


`Back to main page <https://github.com/androportal/linux-on-aakash/blob/master/README.rst>`_ 

 
