# Hetzner rescue system

Sign on the target system via SSH using the rescue system password
from the Hetzner https://robot.your-server.de/server web console.

    installimage

The latest option at the time of this writing was: `CentOS-70-64-minimal`

## Hetzner Online AG - installimage - standardconfig

Reserve most free space for a LVM storage pool that will be set up later.

* The rescue system supports only MSDOS partition tables,
  so maximum size for a single partition is 2T.

* Calculate your system volume group size so that
  the remaining space is within the above limit.

Delete the default partitioning plan and replace it with:

### Example 1:

    ## the following free space to allocate (in GiB):
    # RAID  1: ~2794
    ## PARTITIONS / FILESYSTEMS
    
    PART /boot ext2 512M
    PART lvm vg_sys 768G
    PART lvm vg_pool all
    LV vg_sys lv_sys_root /     ext4  8G
    LV vg_sys lv_sys_swap swap  swap 16G
    LV vg_sys lv_sys_tmp  /tmp  ext4  5G
    LV vg_sys lv_sys_home /home ext4 20G
    LV vg_sys lv_sys_var  /var  ext4  4G

### Example 2:

In the system pool, allow at least enough space to make a full snapshot
of its largest logical volume and a few gigabytes extra.

    ## An example with the recommended minimum system pool size (80 GiB)
    # RAID  1: ~1863
    ## PARTITIONS / FILESYSTEMS
    
    PART /boot ext2 512M
    PART lvm vg_sys 80G
    PART lvm vg_pool all
    LV vg_sys lv_sys_root /     ext4  8G
    LV vg_sys lv_sys_swap swap  swap 16G
    LV vg_sys lv_sys_tmp  /tmp  ext4  5G
    LV vg_sys lv_sys_home /home ext4 20G
    LV vg_sys lv_sys_var  /var  ext4  4G

Save and exit.

## Installation report

```
                Hetzner Online AG - installimage

  Your server will be installed now, this will take some minutes
             You can abort at any time with CTRL+C ...

         :  Reading configuration                           done 
         :  Loading image file variables                    done 
         :  Loading centos specific functions               done 
   1/16  :  Deleting partitions                             done 
   2/16  :  Test partition size                             done 
   3/16  :  Creating partitions and /etc/fstab              done 
   4/16  :  Creating software RAID level 1                  done 
   5/16  :  Creating LVM volumes                            done 
   6/16  :  Formatting partitions
         :    formatting /dev/md0 with ext2                 done 
         :    formatting /dev/vg_sys/lv_sys_root with ext4  done 
         :    formatting /dev/vg_sys/lv_sys_swap with swap  done 
         :    formatting /dev/vg_sys/lv_sys_tmp with ext4   done 
         :    formatting /dev/vg_sys/lv_sys_home with ext4  done 
         :    formatting /dev/vg_sys/lv_sys_var with ext4   done 
   7/16  :  Mounting partitions                             done 
         :  Importing public key for image validation       done 
   8/16  :  Validating image before starting extraction     done 
   9/16  :  Extracting image (local)                        done 
  10/16  :  Setting up network for eth0                     done 
  11/16  :  Executing additional commands
         :    Generating new SSH keys                       done 
         :    Generating mdadm config                       done 
         :    Generating ramdisk                            done 
         :    Generating ntp config                         done 
         :    Setting hostname                              done 
  12/16  :  Setting up miscellaneous files                  done 
  13/16  :  Setting root password                           done 
  14/16  :  Installing bootloader grub                      done 
  15/16  :  Running some centos specific functions          done 
  16/16  :  Clearing log files                              done 

                  INSTALLATION COMPLETE
   You can now reboot and log in to your new system with
  the same password as you logged in to the rescue system.

root@rescue ~ #
```
