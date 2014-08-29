---
layout: post
title: Installing Software On Azure
date: 2014-08-08
draft: true
---

#Stratagies for installing your favorite software on an Azure VM

I've found that when I'm deploying a system in Azure, I want an easy way to access the installation files as I create different VMs for dev, test, and production. Here's one approach that I've used to avoid downloading the same applications over and over again.

## Option 1 - Create a VM in the cloud to download the files and store them on a detachable data disk. Use RDP to copy and paste files into folders on the new VM. Copy/Paste of files can be very slow if you have a lot of data to copy. Option 2 is the recommended approach for this.

## Option 2 - Create a VHD locally and then upload to the cloud
1. On your local computer, locate all the software installation assets that you will need in Azure
2. Create a VHD file using Disk Management in the Microsoft Management Console
  a. Start a command window and type mmc
  b. File->Add/Remove Snap-In
  c. Add Disk Management and click OK
  d. Right Click on Disk Management in the Console Root and select "Create VHD"
  e. Follow the instructions to create a VHD file, bring it online and format it.
3. Now you should have a new drive attached to your computer. Copy all of the files that you will need to set up your VM.
4. Eject the vhd
5. Install AzCopy from [http://aka.ms/AzCopy](http://aka.ms/AzCopy)
6. change directory to the install location for AzCopy
7. Run the following command
  AzCopy d:\vhds <storage account upload location> /DestKey:<key> <vhdname>
