---
layout: post
title: "Seamless Linux VM"
---
OS X is a popular environment for Computer Science students, as is Windows to a lesser extent, but both fall short or make life unnecessarily hard when it comes to the lower level C programming required by the curriculum.
Surprises like line endings and [little deviations from POSIX](http://stackoverflow.com/questions/1413785/sem-init-on-os-x) can be a pain to track down.
For these classes, Linux is almost mandatory. For this there are three solutions:

## Dual Boot
The most straightforward and obvious solution is to install Linux.
Not always an option, though, as some computers have unsupported hardware that can cause no end of problems on their own.

And, of course, some people are afraid of making the switch.
I'm no fan of pushing uneasy people into this sort of deal, unless I'm confident they'll be able to get prompt help as needed.

## SSH
I've seen some people solve this problem by SSHing into the school's Linux machines and working from there.
That solution seems terribly unpleasant, considering the dated software selection and the dependence on a stable internet connection from a laptop.

## VM
Instead of installing to the system, installing [Ubuntu](http://www.ubuntu.com) inside [VirtualBox](https://www.virtualbox.org).
This yields a fully functional Linux desktop, constrained inside a window on the native OS X or Windows desktop.
While this provides the full functionality of an install with the security of reverting everything with a click of the mouse, running a full desktop in a full desktop unsurprisingly leads to fairly high resource consumption.

Also, there's the non trivial awkwardness of having to work inside a different environment where your regular window management workflow has no bearing.
VirtualBox offers a "Seamless Mode" setting where it hides the virtual machine and mixes the guest applications in with the host's, but this still requires the guest to render the applications.
This also didn't work with my [Windows 8 window management setup](/2013/11/09/keyboard-centered-workflow-on-windows-8/).

## VM SSH
The solution I came up with, back when I was using Windows for hardware reasons, is kinda cool: I ran a lightweight Linux installation in a VM with an SSH server, and connected to that.

#### Installing
Installation of the Linux guest is standard.
I installed [Arch Linux](https://www.archlinux.org), a super barebones distribution, though [Ubuntu Server](http://www.ubuntu.com/download/server) would do the trick too.
The important thing here is we're looking for essentially a headless system that doesn't even run an X server.

#### Port Forwarding
![port forwarding](/public/vmssh1.png)
Once the Linux guest is installed and running the ssh daemon, you forward one of your host ports to guest port 22, conventionally used for ssh.

#### Connect
![ssh](/public/vmssh2.png)
Next, ssh in:
```
ssh [user]@localhost -p [host port]
```
Pictured here is [Cygwin](https://www.cygwin.com), a set of UNIX tools for Windows, which I already had installed, otherwise [Cmder](http://bliker.github.io/cmder/) is probably easiest.

At this point, you have a native Windows application offering a full Linux terminal, package manager and all.
Since there's no full desktop, this ends up being rather lightweight -- my setup ran at under 100 Mb RAM.

But, it's running on its own virtual drive, which means files on the VM and files on the host are isolated.

#### Shared /home/
![shared home](/public/vmssh3.png)
Using VirtualBox's Shared Folders feature, I shared my Windows home directory with the guest, and within the guest used it as my home directory.
This means that the VM can now access all of my files, notably my workspace.
This also means shared dotfiles across host and guest for convenient preconfiguration.

As a caveat, this also means shared SSH keys.
I never figured out how to ssh in to the VM from the host.
