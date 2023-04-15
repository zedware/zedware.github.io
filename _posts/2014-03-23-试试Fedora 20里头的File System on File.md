---
layout: post
title: 试试Fedora 20里头的File System on File
original: https://blog.csdn.net/zedware/article/details/21884243
tags: [Linux, FSoF, File System]
---
                
Create a file to host the fs.  Can also use dd here instead of truncate.
    $ truncate -s 128M bigfile
    $ du -csh bigfile
    $ stat bigfile


Create a fs on the file. There will be some warnings saying it is not a block device.
    $ mke2fs -t ext4 bigfile
    $ stat bigfile 


Find the first unused loop device, then create it.
    $ losetup -f
    $ sudo losetup /dev/loop0 bigfile


Finnally, we can mount the fs.
    $ sudo mkdir /mnt/fsof
    $ sudo mount -t ext4 /dev/loop0 /mnt/fsof
    $ mount | grep loop0
    /dev/loop0 on /mnt/fsof type ext4 (rw,relatime,seclabel,data=ordered)


Now, let us do some experiments on the fs on a file.
    $ sudo dd iflag=fullblock if=/dev/random of=largefile count=4096 bs=4096
    The performance is not so good, be patient please.
    
    $ sudo fallocate -p -o 4096 -l 8192 largefile 
    Puch a hole, and we can find the blocks used is reduced using: 
    $ du -c largefile
    Or
    $ stat largefile


Be careful to the unit of the different blocks, it may be a sector(512b), a fs block(1024b or 4096b).

===
How to Check Filesystem Block Size on Linux?
Example 1:
# tune2fs -l /dev/sda1 | grep -i ‘block size’
Block size: 4096
Example 2:
# dumpe2fs -h /dev/sda1 |grep “Block size:”
Block size: 4096
Example 3:
# blockdev –getbsz /dev/sda1
4096
Example 4:
# echo “abc” >test.txt
# du -h test.txt
4.0K test
            
