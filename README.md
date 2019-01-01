# How to Install Samba in FreeBSD

https://www.unixmen.com/how-to-install-samba-in-freebsd-9/

I will not give a definition of Samba because every Unix/Linux Administrator should know about it. step-by-step how-to article to setup your FreeBSD Samba server.
My server
    root@FreeBSD-unixmen:/root # uname -a
    FreeBSD FreeBSD-unixmen 9.1-RELEASE FreeBSD 9.1-RELEASE #0 r243826: Tue Dec 4 06:55:root@obrian.cse.buffalo.edu:/usr/obj/usr/src/sys/GENERIC i386

# Install Samba

You need to have ports installed. After you have it installed, you need to go to the Samba directory        
    root@FreeBSD-unixmen:/root # cd /usr/ports/net/samba36
    root@FreeBSD-unixmen:/root # make install clean

You can add extra components to your Samba server. If you want like Webind and Swat.
Now edit /etc/rc.conf so that Samba will start with every system boot. Add this line to the file and                   samba_enable="YES"

# Configure Samba
Edit /usr/local/etc/smb.conf:
    root@FreeBSD-unixmen:/root # vi /usr/local/etc/smb.conf
Specify where you will be able to connect:
    ; hosts allow = 192.168.1. 192.168.2. 127.
Remove the semi-colon and change the IP address so it matches your network. For example, in my hosts allow =          192.168.1. 192.168.0. 127. 10.1.1.
Now configure the share in the same configuration file:
    [unixmen]
    comment = Unixmen FreeBSD SAMBA
    path = /Unixmen
    valid users = pirat9
    public = no
    writable = yes
    Save and exit.
    Adding Samba user
Use command adduser to add unix user. In my situation pirat9:
    root@FreeBSD-unixmen:/root # adduser
    Username: pirat9
    Full name: test1 test2
    Uid (Leave empty for default):
    Login group [test]:
    Login group is test. Invite test into other groups? []:
    Login class [default]:
    Shell (sh csh tcsh bash rbash nologin) [sh]:
    Home directory [/home/test]:
    Home directory permissions (Leave empty for default):
    Use password-based authentication? [yes]:
    Use an empty password? (yes/no) [no]:
    Use a random password? (yes/no) [no]:
    Enter password:
    Enter password again:
    Lock out the account after creation? [no]:
    Username : pirat9
    Password : *****
    Full Name : test1 test2
    Uid : 1002
    Class :
    Groups : pirat9
    Home : /home/pirat9
    Home Mode :
    Shell : /bin/sh
    Locked : no
    OK? (yes/no): yes
    adduser: INFO: Successfully added (pirat9) to the user database.
    Add another user? (yes/no):
    Add the directory you want to share for Samba:
    root@FreeBSD-unixmen:/root # mkdir -p /Unixmen

Adding the Samba group and the user pirat9:
    root@FreeBSD-unixmen:/root # pw groupadd smbprivate -M pirat9

Change permission on the Samba folder:
    root@FreeBSD-unixmen:/root # chgrp smbprivate /Unixmen
    root@FreeBSD-unixmen:/root # chmod 770 /Unixmen
Now it’s time to set the password:
    root@FreeBSD-unixmen:/root # smbpasswd -a pirat9
    New SMB password:
    Retype new SMB password:
    Added user pirat9.
    Start the Samba server:
    root@FreeBSD-unixmen:/root # /usr/local/etc/rc.d/samba start
Removing stale Samba tdb files: . done
    Starting nmbd.
    Starting smbd.

Testing the server
Run this command to test the server:
    root@FreeBSD-unixmen:/root # smbclient -U pirat9 -L localhost

Enter pirat9's password:
    Domain=[MYGROUP] OS=[Unix] Server=[Samba 3.6.9]
    Sharename Type Comment
    --------- ---- -------
    unixmen Disk Unixmen FreeBSD SAMBA
    IPC$ IPC IPC Service (Samba Server)
    pirat9 Disk Home Directories
    Domain=[MYGROUP] OS=[Unix] Server=[Samba 3.6.9]
    Server Comment
    --------- -------

FREEBSD-UNIXMEN Samba Server
    Workgroup Master
    --------- -------
    MYGROUP FREEBSD-UNIXMEN

Now try to connect from a Windows OS machine and login with the Samba credentials:
    \\ip-to-your-freebsd-server
This is how it displays from Windows 7.
