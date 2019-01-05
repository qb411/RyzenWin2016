# RyzenWin2016
This is a repo for tracking my efforts to make Ryzen compatible with Windows Server 2016 to use the Hyper-V capabilities.

## Pre-Migrated Hardware Description
Server was a pre-existing Windows Server 2016 OS.  Specs for the pre-migration state was as follows:
1. Intel i5-2400s recovered CPU (1151 LGA)
2. Asus Z-77A nmotherboard
3. 32GB DDR 3 Patriot memory
4. IBM m1015 ServeRAID, updated to LSI firmware (https://www.servethehome.com/ibm-m1015-part-1-started-lsi-92208i/)
5. Intel Gigabit CT Desktop (1x PCIe)
6. Intel 520x t2 (10/1GBE 8x PCIe)
7. CoolerMaster Hyper T2 CPU cooler
8. Rosewill RSV 4000 Server Case

**Storage**
- 2-Toshiba 2TB 7200RPM HDD
- 2-Toshiba 3TB 7200RPM HDD
- 2-Western Digital 1TB 7200RPM HDD
- 1-Western Digital 500gb 7200RPM (OS)
- 2-OCZ 240GB SSD (VM array)

Additional complications from Hyper-V VM's were present as well, with 3 VM's built on Intel hardware configs, additionally drives are  logically organized in a Storage Spaces array within the primary hardware.  

## Post-Migration Hardware Description
Server will be migrated with existing software OS to new hardware platform, ideally Ryzen 7 1700x.
1. Ryzen 7 1700x
2. x470 Aorus Ultra Gaming 
3. 16GB DDR4 G.Skill RipJaw (PC 2400)
4. Discrete GPU needed (Zotac Nvidia 750 low profile and AMD 5450 available for PCI-E graphics)

Other parts will remain the same ideally during the failover.  Hard Drives are managed by Storage Spaces and software raid/jbod so it should make migration issues minimal. 

## Important Notes/Lessons Learned
1. Ensure you enable SVM (AKA AMD Virtualization) setting in BIOS.
2. Driver compatibility is a major concern.  PCI drivers were absolutely necesary to solve BSOD's, onboard NIC's (211, 217, 218, 219) must be hacked for Windows Server 2016, or find proper replacement hardware.
3. All software RAID/JBOD, Storage Space Pools etc followed the sytem without issue in the OS.  
4. Hyper-V VM's had immediate problems due to the prior Intel Hardware provisioning.  Create new VM configs and point them at the VHD's to get functionality back. 

## WorkLog

### 2018/12/28 - Initial migration
Physical hardware swapout was complete in less than an hour. Boot up was slow/difficult.  Initial assessment was no bluescreens and hardware adjustment continued.   Removal of Realtek driver from old motherboard setup provided better network compatibility. 

_Outstanding issues at this time:_ 
- Server is not recognizing onboard Intel NIC, I211-AT NIC present on the motherboard does not have driver.  Follow this URL for adjusting the existing Win 10 driver.  
https://blog.workinghardinit.work/2017/06/19/installing-intel-i211-i217v-i218v-i219v-drivers-windows-server-2016-eufi-boot/
- Blue Screens continuing to occur.  Appears this is due to general driver unavailability for the OS.  Cannot use any AMD setup utilities for drivers due to non-support of Windows Server 2016. Possible solution mentioned in Byte Edit of installer:
https://www.reddit.com/r/Amd/comments/5yauiu/ryzen_master_does_not_run_on_windows_server_2016/
- Download all drivers for the motherboard from Gigabyte's site: https://www.gigabyte.com/Motherboard/X470-AORUS-ULTRA-GAMING-rev-10#support-dl-driver

### 2018/12/28 - Update 1
Server continues to experience significant BSOD errors with added hardware.  Have setup only essential hardware (RAID Card/GPU) and so far this is most stable state. BSOD's do continue and occur frequently with the 520X-t2 NIC added.   Continue to research the driver compatibility issues. 

### 2018/12/28 - Update 2
Verified that above NIC driver hack does work for i211-at network card.  I've been able to now install the win10 drivers successfully with the onboard NIC of the motherboard.   Frustrating that this is clearly capable of working and merely a Intel limitation. 

Extracted mb_driver_chipset_b450_w10_18.10.20.02 and looked in the following path:
**..\mb_driver_chipset_b450_w10_18.10.20.02\WHQL_win10\Packages\Drivers**

Used the above path with Device Manager to discover drivers for all unidentified hardware in device manager.  PCI drivers appear to have resolved all initial BSOD issues that were experienced.

### 2018/12/28 - Update 3
So far the major BSOD's have been gone since updating the PCI drivers in Update 2.   In addition I've now loaded the appropriate Nvidia Drivers for the Zotac 750 GPU) and added the Intel X520-T2 dual port NIC.   This has now proved perfectly functional and the dual port NIC was immediately recognized.  I've also been able to migrate the VM's in Hyper-V through creating new VM configs.   It turns out that the old VM's were built on Intel and the configs will not work with a AMD system.  However to expedite recovery, just create a new VM and point to the old VHD.  Once created it will bring up without issue. 

So far I cannot use alot of the specific Ryzen software to provide oversight/view on the hardware state.  So far I'm not worried as this is a fairly low-utilization system right now.  However the overall unawareness of the hardware is a problem.  However it is a good point to be aware.  Nvidia drivers loaded without issue, and have been stable again since the PCI drivers were loaded. 

### 2019/01/04 - Update 4
No major complications save for one with the VM's.  At this time there is a random reboot issue that seems to occur due to the Plex server which is run within a VM.  It would appear the issue is related to some various issues with Dynamic memory within Hyper-V.  To resolve i've set static memory assignments to the VM's which has reduced the overall issues with the VM reboots.  However there is still some instability on a week to week basis with the underlying physical host.  It does appear to be driver related at this time.  It seems to highlight some instability with the drivers for PCI/Memory controller.  
