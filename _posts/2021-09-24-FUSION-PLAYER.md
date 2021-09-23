---
layout: post
title: VMware Fusion Player for macOS
---

VMware Workstation is powerful for desktop virtualization. And the macOS version is now free for personal usage. Followed is the steps to register, download and install.

1.  Register and activate an account on vmware.com
2.  Download Fusion Player.
3.  Search for Fusion Player personal license page and register for it.
4.  Install the Fusion Player with the license.
5.  Download Ubuntu 20.04 LTS.
6.  Install Ubuntu in Fusion Player.
    -   Choose UEFI, NVMe, proper disk size
    -   Prefer the manual mode over the express mode
    -   Choose the Minimal installation
7.  Network
    -   If NAT not work, change to Bridge
8.  Install VMware Tools
    -   Select it in the menu to mount it
    -   Copy the tar.gz to /tmp
    -   Extract it and run the perl script
    -   Configure shared folders, they will be mounted as /mnt/hgfs.
    -   And it says open-vm-tools is better
        -   sudo apt install open-vm-tools
        -   sudo apt install open-vm-tools-desktop
        -   It may not work as expected :-(
    -   Can also visit Computer in File Explorer using user/passwd if SMB is turned on in macOS.
9.  Install development tools
    -   vscode
    -   git
    -   gcc, gdb, g++
    -   clang-format
    -   cmake etc.

Thanks VMware Inc. for the great software and the free license.

