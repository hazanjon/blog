---
layout: post
title:  "Hetzner - Setting Up LVM to use all disks"
date:   2014-04-27 18:00:00
categories: 
---

For good or bad I have decided to migrate my existing Hetzner dedicated server to an identical one at Hetzner that is &euro;11 cheaper a month.

Since I'm getting to start from scratch Im going to correct a few things I mis-setup last time, The first being to turn off the software RAID and add both disks to LVM for a single useable 5.5TB volume. I realise that most would recommend against this as it leaves me open to hardware failure, however this isnt a production server and the entire thing will be backed up to a RAID5 NAS.

Rambling asside the first job was to ssh into the Hetzner rescue system and install ProxMox.

{% terminal %}
$ installimage
{% endterminal %}

Select Virtualization from the list and then the version of ProxMox (I chose ProxMox - Wheezy)

![installimage main screen]({{!site_url}}/uploads/installimage-main.png)
![installimage main screen]({{!site_url}}/uploads/installimage-virtualization.png)

Once ProxMox has been selected you will need to edit the config script to turn off software raid and use LVM rather than the default partitions. Change your config script to look like this:

```
#
# Hetzner Online AG - installimage
#
# This file contains the configuration used to install this
# system via installimage script. Comments have been removed.
#
# More information about the installimage script and
# automatic installations can be found in our wiki:
#
# http://wiki.hetzner.de/index.php/Betriebssystem_Images_installieren
#

DRIVE1 /dev/sda
DRIVE2 /dev/sdb
SWRAID 0
SWRAIDLEVEL 1
BOOTLOADER grub
HOSTNAME Proxmox-VE.localdomain
PART /boot  ext3     512M
PART lvm    vg0       all
LV vg0   swap  swap      swap         8G
LV vg0   root   /        ext4         all
IMAGE /root/.oldroot/nfs/install/../images/Debian-70-wheezy-64-minimal.tar.gz
```

Press f10 to exit and save your changes. The install will then begin, once it finishes you will be returned to the command prompt, just reboot the server and reconenct via ssh.

Our next task is to setup the second physical disk and add it to the LVM

Setup the partition table using `gdisk`:

{% terminal %}
$ gdisk /dev/sdb

  Command (? for help): o #create a new empty GUID partition table (GPT)
  
  This option deletes all partitions and creates a new protective MBR.
  Proceed? (Y/N): Y

  Command (? for help): n #add a new partition
  Partition number (1-128, default 1): 1
  First sector (34-2047, default = 34) or {+-}size{KMGTP}:
  Last sector (34-2047, default = 2047) or {+-}size{KMGTP}:
  Current type is 'Linux filesystem'
  Hex code or GUID (L to show codes, Enter = 8300): 8e00 #This is Linux LVM

  Command (? for help): w #write table to disk and exit

  Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
  PARTITIONS!!

  Do you want to proceed? (Y/N): Y
{% endterminal %}

Now we just want to add the new space to LVM

{% terminal %}
$ pvcreate /dev/sdb1 #Add a new Physical Volume to LVM
$ vgextend vg0 /dev/sdb1 #Extend teh Volume Group onto the new Phiyscal Volume
$ lvextend -l +100%FREE /dev/vg0/root #Add the new free space to our root Logical Volume
$ resize2fs /dev/mapper/vg0-root #Extend the file system to the new space
{% endterminal %}

Success!
