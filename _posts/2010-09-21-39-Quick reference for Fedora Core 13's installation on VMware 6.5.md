---
layout: post
title: Quick reference for Fedora Core 13's installation on VMware 6.5
original: https://blog.csdn.net/zedware/article/details/5899759
---
     记性没有以前那么好了，什么都能记住。想起来读书时的一句话“好记性不如烂笔头”。还是将遇到的小问题都记录下来吧，免得日后又来折腾。

Quick reference for Fedora Core 13's installation on VMware 6.5
 
[zedware@gmail.com](mailto:zedware@gmail.com)
 
2010/9/20 17:09:40 VMware Tools hints
2010/9/20 13:51:44 fc13
2009-10-3 16:43:31 fc11
 
Basic
1. make default user autologon.
1) fc10/fc11
Setting up Fedora 10 to log into our account automatically is very easy.
You simply need to append a few lines to the file called custom.conf
located in the /etc/gdm/ directory. Now, open the above mentioned file
in a text-editor like gedit or kedit, and append these lines:
[daemon]
TimedLoginEnable=true
TimedLogin=<Your-Username>
TimedLoginDelay=0
2) fc13
[http://haohetao.javaeye.com/blog/676608](http://haohetao.javaeye.com/blog/676608):
vim /etc/gdm/gdm.schemas
<schema>
<key>daemon/AutomaticLoginEnable</key>
<signature>b</signature>
<default>false</default>  设为 true.如 <default> true </default>
</schema>
<schema>
<key>daemon/AutomaticLogin</key>
<signature>s</signature>
<default></default>  设置用户名，如 <default>your-user-name</defaults>
</schema>`
如果想让root也能登录
1.vi /etc/pam.d/gdm
2.注释其中的一行 auth required pam_succeed_if.so user != root quiet
3.然后 vi /etc/pam.d/gdm-password
4.注释其中的一行 auth required pam_succeed_if.so user != root quiet
这一步不用也可以，不过只能自动登录root, 不能手工登录
2. $ sudo vi /etc/sudoers
VMware Tools
1. add: #define VMW_HAVE_EPOLL  1
2. not all of the functions are ok. At least we can Copy/Paste
   between the host and the guest now.
LVM stuff - system-config-lvm is better than CLI.
1. create a new hard disk using VMware's menu "VM".
2. run lvs to display the volume group names.
   # lvs
3. # pvcreate /dev/sdc1      #(or /dev/sdb)
4. # vgextend VolGroup /dev/sdc1    #(Volume name differs in fc13)
5. # lvextend VolGroup/lv_root /dev/sdc1
6. # lvs
7. # resize2fs -p /dev/VolGroup/lv_root
8. # df
*. ref:[http://sxzx.360doc.com/content/090413/11/36491_3114684.html](http://sxzx.360doc.com/content/090413/11/36491_3114684.html)
I had to add more swap for 11gR2 (fc13).
1. # lvs
2. # pvcreate /dev/sdc
3. # vgextend vg_xxxx /dev/sdc
4. # lvextend vg_xxxx /dev/sdc
5. # lvs
6. # mkswap /dev/mapper/...
*. swap does not need resize2fs.
Yum stuff
1. $ sudo yum install samba samba-client system-config-samba
   Note:
   1) samba service's name is smb.
   2) service smb start
2. $ sudo yum install system-config-lvm
How to share files
1. VM->Settings->Share folders. Automount to /mnt/hgfs in guest OS.
2. $ smbclient //host/c$ -U x
3. Use ssh/ftp client to visit guest OS.
 
