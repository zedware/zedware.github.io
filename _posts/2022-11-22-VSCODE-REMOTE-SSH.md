---
layout: post
title: Visual Studio Code Remote SSH
tags: jekyll blog github-page
---

This is a sad story. Someone removed my home dir on the development rig,
and I have to start from the scratch. Creating a new home dir, git cloning
the repository are easy. But recover the .bash\_profile/.bashrc and cheetsheet
are troublesome. Finally, the job is almost done, but I found that VSCode
have to be reinstalled, too. That's because I use VSCode remote on Windows.

After struggling for about an hour, everything looks fine, and I start to
write down the steps. First thing first, every extensions in VSCode should
be reinstalled for VSCode remote, including C/C++, CMake, Hex Editor, 
IntelliCode, GitLens, etc. 

To make remote ssh work, see the following steps.

1.  Install OpenSSH Client from Microsoft.

    <https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui>

    We can install OpenSSH Client using the GUI or the PowerShell command:

    ```
    # Install the OpenSSH Client
    PowerShell> Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
    ```
    
1.  Verify the commands are there.

    ```
    C:\Users\zedware> which ssh
    /c/WINDOWS/System32/OpenSSH/ssh
    
    C:\Users\zedware> which ssh-keygen
    /c/WINDOWS/System32/OpenSSH/ssh-keygen
    ```

1.  Generate ssh key.

    ```
    C:\Users\zedware> ssh-keygen -t ed25519 -C "zedware@xxx.com"
    ```
    
    The files are under C:\\Users\\zedware\\.ssh\\.

1.  Edit .ssh\\config.

    ```
    # Read more about SSH config files: https://linux.die.net/man/5/ssh_config
    Host 192.168.1.100
        HostName 192.168.1.100
        User zedware
        IdentityFile C:\Users\zedware\.ssh\id_ed25519
    ```

    The IdentityFile should be the private key, instead of the public one. I
    happened to use the public key, and ssh or VSCode will complaining that
    the file is in invalid format.

1.  Copy id to remote server and try ssh into it.

    ```
    wsl$ ssh-copy-id -i /mnt/c/Users/zedware/.ssh/id_ed25519.pub zedware@192.168.1.100
    
    C:\Users\zedware> ssh -i C:\Users\zedware\.ssh\id_ed25519.pub zedware@192.168.1.100
    ```
    
    If there are any errors, ssh -v -v -v can print debug information.

1.  Config remote ssh in VSCode.

    ```
    Settings -> Remote - SSH
    ```

Happpy coding!

