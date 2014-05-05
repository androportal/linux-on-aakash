=======================
Aakash specific changes
=======================

#. To disable edge resize(top right corner) and top resize in openbox windows edit the file ``~/.config/openbox/lxde-rc.xml``, and look for
   this section and update the content of **action name** filed to **Focus** as shown ::
    
     ....	
	<context name="Top">
     <mousebind button="Left" action="Press">
       <action name="Focus"/>
       <action name="Raise"/>
       <action name="Unshade"/>
     </mousebind>
     <mousebind button="Left" action="Drag">
       <action name="Focus">
	<context name="Right">
     <mousebind button="Left" action="Press">
      <action name="Focus"/>
       <action name="Raise"/>
     </mousebind>
     <mousebind button="Left" action="Drag">
       <action name="Focus">
         <edge>right</edge>
         <context name="TRCorner">
     <mousebind button="Left" action="Press">
       <action name="Focus"/>
       <action name="Raise"/>
       <action name="Unshade"/>
     </mousebind>
     <mousebind button="Left" action="Drag">
       <action name="Close"/>
     ....

   Reconfigure openbox to see the effect ::

	openbox --reconfigure



#. To open all windows maximized except ``dialogs`` & ``onboard`` keyboard add this content in the same above file ::

	<applications>

	<application class="*">
	<maximized>yes</maximized>
	</application>

	<application type="dialog">
	<maximized>no</maximized>
	</application>

	<application name="onboard">
	<maximized>no</maximized>
	</application>

	</applications>

   In this case we have to reload ``openbox`` or reload ``lxdm`` ::

	sudo service lxdm restart


#. To increase the width of menu items edit gtk2.0 theme found in   
   ``/usr/share/themes/xyzTheme/gtk-2.0/gtkrc``. Example ::

    xthickness = 5
    ythickness = 9
    engine "clearlooks"
       {
               radius = 15.0
       }

    “clearlooks-menubar”
     xthickness = 10
     ythickness = 5


#. (Auto)Detect touch controllers for Aakash

   Till date, four different types of touch controllers works on
   Aakash. These are: Focal Tech(ft5x), Goodix(gt811), Gslx680, &
   Elan(ektf2k). In a conventional GNU/Linux system, the hardware is
   auto-detected at boot time. However due to ``script.bin``, which
   passes parameters to kernel about various hardware configuration,
   these modules are not auto-loaded at boot time. Every time SDcard
   is inserted, the relevant ``script.bin`` should be loaded so that
   exact touch driver is loaded. This is done by a very simple bash
   script which does this job at boot time.

   The script can be downloaded from `here
   <https://raw.githubusercontent.com/psachin/probe_tc/master/probe_tc.sh>`_. This
   script is called by the file ``/etc/rc.local`` at user runlevel ::

     #!/bin/sh -e
     #
     # rc.local
     #
     # This script is executed at the end of each multiuser runlevel.
     # Make sure that the script will "exit 0" on success or any other
     # value on error.
     #
     # In order to enable or disable this script just change the
     execution
     # bits.
     #
     # By default this script does nothing.

     # Added for Aakash, setting 777 permissions
     #chmod 777 /dev/ump
     #chmod 777 /dev/mali

     ...

     
     bash /opt/github/probe_tc/probe_tc.sh
     exit 0
     

   For Elan(ektf2k) driver to work properly, some changes has to be
   made in ``/usr/share/X11/xorg.conf.d/`` directory.

`Back to main page <https://github.com/androportal/linux-on-aakash/blob/master/README.rst>`_ 

 Next topic: `FAQs <https://github.com/androportal/linux-on-aakash/blob/master/known_issues.rst>`_
