Can’t start X11 applications after “su” or “su -” to another user
=====================================================================


To get access to the X client applications such as system-config-date, xclock, vncviewer we need to export the DISPLAY settings of a remote host to the local server. This is commonly done using below commands.

	# ssh root@remotehost
	remotehost# export DISPLAY=x.x.x.x:y.y
	
Where x.x.x.x:y.y – is the display settings of the system from which you connected to the remote host.
You can also use the -X option with ssh to directly export the DISPLAY on the remote host.

	# ssh -X root@remotehost
	
But now if you try to switch to another user on the remote system and export the display again, you would get and error – “Error: Can’t open display:”.

	# ssh -X root@remotehost
	# su - [username]
	# export DISPLAY=x.x.x.x:y.y 
	# xclock
	Error: Can't open display: x.x.x.x:y.y
	X11 forwarding for sudo users
	
Just setting the DISPLAY is not enough. X authentication is based on cookies, so it’s necessary to set the cookie used by the user that initiated the connection. The following procedure allows a sudo user to use the ssh based X11 tunnel:

1. Connect the remote host using the -X option with ssh.

	# ssh -X root@remote-host
	
2. Now list the coockie set for the current user.

	# xauth list $DISPLAY
	node01.thegeekdiary.com/unix:10  MIT-MAGIC-COOKIE-1  dacbc5765ec54a1d7115a172147866aa
	# echo $DSIPLAY
	localhost:10.0
	
3. Switch to another user account using sudo. Add the cookie from the command output above to the sudo user.

	# sudo su - [user]
	# xauth add node01.thegeekdiary.com/unix:10  MIT-MAGIC-COOKIE-1  dacbc5765ec54a1d7115a172147866aa
	
4. Export the display from step 2 again for the sudo user. Try the command xclock to verify if the x client applications are working as expected.

	# export DISPLAY=localhost:10.0
	# xclock