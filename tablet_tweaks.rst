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




`Back to main page <https://github.com/androportal/linux-on-aakash/blob/master/README.rst>`_ 

 Next topic: `FAQs <https://github.com/androportal/linux-on-aakash/blob/master/known_issues.rst>`_