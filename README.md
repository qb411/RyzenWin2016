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

## 2018/12/28 - Initial migration
Physical hardware swapout was complete in less than an hour. Boot up was slow/difficult.  Initial assessment was no bluescreens and hardware adjustment continued.   Removal of Realtek driver from old motherboard setup provided better network compatibility. 

_ _Outstanding issues at this time:_ _ 
- Server is not recognizing onboard Intel NIC, I211-AT NIC present on the motherboard does not have driver.  Follow this URL for adjusting the existing Win 10 driver.  
https://blog.workinghardinit.work/2017/06/19/installing-intel-i211-i217v-i218v-i219v-drivers-windows-server-2016-eufi-boot/
- Blue Screens continuing to occur.  Appears this is due to general driver unavailability for the OS.  Cannot use any AMD setup utilities for drivers due to non-support of Windows Server 2016. Possible solution mentioned in Byte Edit of installer:
https://www.reddit.com/r/Amd/comments/5yauiu/ryzen_master_does_not_run_on_windows_server_2016/

## 2018/12/28 - Update 1
Server continues to experience significant BSOD errors with added hardware.  Have setup only essential hardware (RAID Card/GPU) and so far this is most stable state. BSOD's do continue and occur frequently with the 520X-t2 NIC added.   Continue to research the driver compatibility issues. 
