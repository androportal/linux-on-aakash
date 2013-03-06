========================
Step to make file system
========================

As of now we have uboot and kernel ready for Aakash, the next step is to run few apps, to do so we need Linux file system. One can use any 
distribuition from here after. We will use ubuntu 12.10. Just for the  record we have also tried Debian wheezy which also works well. 

The main war is between desktop enviroments. With lot of testing
and real world statistics we finally decided to go with LXDE. There is lot
of scope for customization with LXDE and moreover its almost 50% lighter & twice
as faster than any other desktop enviroment.

#. Insert sdcard again, download the core ubuntu 12.10 image from this `link <http://cdimage.ubuntu.com/ubuntu-core/releases/12.10/release/ubuntu-core-12.10-core-armhf.tar.gz>`_ and save it in say /tmp directory. Extract the tar file in your sdcard's ext4 partition ::
	
	cd /media/<ext4 partition of sdcard>


   Now extract core ubuntu file system ::


	sudo tar -xvpzhf /tmp/ubuntu-core-12.10-core-armhf.tar.gz 



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


#. The above two steps can be combined together using a simple bash
   `ch-mount.sh
   <https://github.com/psachin/bash_scripts/blob/master/ch-mount.sh>`_. From
   here onwards we will use this script to mount and un-mount
   fs. Please read this script to get any further help ::

	sudo ch-mount.sh -m /media/<ext4 partition of sdcard>/

#. Now we have a chroot environment with all ``proc, dev, sys, dev/pts`` mounted,
   so run update to fetch repository informations (as chroot has root prompt so
   no need to write sudo anymore) ::

	apt-get update

#. Now install english language pack to avoid locale related errors ::

         apt-get install language-pack-en-base

#. Now install minimal X environment to test our setup ::

	apt-get install vim.tiny sudo ssh net-tools ethtool wireless-tools lxde \    
         xfce4-power-manager xinit xorg network-manager iputils-ping \
         python-gi-cairo onboard rsyslog alsa-utils gnome-mplayer \

#. Few optional recommended packages for system utilities(lxtask, lxproxy and htop) ::

	apt-get install bash-completion lxtask htop \ 
	python-central python-gobject-2 python-gtk2 synaptic\


   Download `lxproxy <http://zevenos.com/files/lxproxy_0.1ubuntu3_all.deb>`_ and install using dpkg::

	dpkg -i lxproxy_0.1ubuntu3_all.deb


#. Few more optional packages for office suite, arduino development and pcb designing (testing purpuse)::

	apt-get install libreoffice python-gnome2 python-gconf python-serial\
	python-gtksourceview2 gcc-avr  binutils-avr  avr-libc avrdude python-xdg

   Download  `gnoduiono <https://launchpad.net/~pmjdebruijn/+archive/gnoduino-release/+files/gnoduino_0.4.0-0pmjdebruijn4%7Eprecise_all.deb>`_ and install using dpkg::

	dpkg -i gnoduino_0.4.0-0pmjdebruijn4~precise_all.deb
	
         
#. Add user and set permissions ::

	# adduser aakash

	# addgroup aakash adm
 	
	# addgroup aakash sudo

	# addgroup aakash audio

#. Open ``/etc/hostname`` file in vim.tiny editor and give a hostname, for example
   write ``aakash-arm``

#. Similarly open ``/etc/hosts`` and remove its content and add these two lines::

	127.0.0.1    localhost
	127.0.1.1    aakash-arm

#. Open ``/etc/modules`` file and append these two lines ::

	gt811_ts
	rtlwifi

#. Add these lines in ``/etc/rc.local`` to activate swap at boot time::

	# Added for Aakash, assuming the last partition will be swap in all future builds
	mkswap /dev/mmcblk0p3
	swapon /dev/mmcblk0p3


#. To allow non root user to edit networking, change this file ::

	/usr/share/polkit-1/actions/org.freedesktop.NetworkManager.policy

	look for this section:
	<action id="org.freedesktop.NetworkManager.settings.modify.sys tem">
	
	change <allow_active> to 'yes':
	<defaults>
	<allow_inactive>no</allow_inactive>
	<allow_active>yes</allow_active>
	</defaults>
	</action>


#. Open ``/etc/lxdm/lxdm.conf`` and modify it for autologin. Change the autologin
   section in the top of the file to this ::

	## uncomment and set autologin username to enable autologin
	autologin=aakash

#. Now open a new tab in your host Linux machine's terminal and copy
   ``/<path to linux kernel>/linux-sunxi/out/lib/modules/3.0.42+/`` to your arm
   ubuntu setup ::

   sudo cp -r /<path to linux kernel>/linux-sunxi/out/lib/modules/3.0.42+/ /media/<ext4 partition of sdcard>/lib/modules/


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

#. Remove the sdcard and insert it in your tablet, power on to get ubuntu 12.10 lxde desktop


`Back to main page <https://github.com/androportal/linux-on-aakash/blob/master/README.rst>`_ 

 
