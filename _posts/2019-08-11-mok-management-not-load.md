---
layout: post
title: MoK Management Freeze/not load when installing nvidia driver
categories: ubuntu
tag: secureboot
---

* toc
{:toc}

The Mok Management Menu is actually interpreting the key I pressed, as I spam the Enter key I can get into Grub menu and boot successfully. It looks like just the menu does not render properly for some reason.

So here are the exact key sequences you need to perform to get the Mok enrolling working:

1. When it says `Press any key`, press `↵` once
1. Press `↓` and `↵`
1. Press `↓` and `↵`
1. Press `↓` and `↵`
1. enter the Mok password and `↵`
1. Press `↵`

## reference
- [install Nvidia drivers ubuntu 18.04 with secure boot](https://askubuntu.com/questions/1048135/install-nvidia-drivers-ubuntu-18-04-with-secure-boot)
- [https://askubuntu.com/questions/950395/mok-management-will-not-load-on-boot](https://askubuntu.com/questions/950395/mok-management-will-not-load-on-boot)
- [SecureBoot Ubuntu Wiki](https://wiki.ubuntu.com/UEFI/SecureBoot)