# Amazon Kindle Fire 2 (otter2) LineageOS install guide
This is all public info but it's old and getting hard to find. I just compiled it all into one place.

You are 100% responsible for whatever happens to your device when following this but it should work fine.

### STEP 0 - Setup

You are going to need working ADB, fastboot, and USB cable.

The kindle needs to be rooted with KingRoot or similar. You also need to enable developer settings and ADB.

### STEP 1 - Backup

Backup your stock partitions to your own HD for later if you need disaster recovery:
Code:

    adb pull /dev/block/mmcblk0boot0
    adb pull /dev/block/platform/omap/omap_hsmmc.1/by-name/boot
    adb pull /dev/block/platform/omap/omap_hsmmc.1/by-name/recovery
    adb pull /dev/block/platform/omap/omap_hsmmc.1/by-name/system # This will take a few minutes

Take those files and put them somewhere safe (or don't up to you).

### STEP 2 - Get files

Download the following files from this branch:
* [otter2-freedom-boot-10.4.6.img](https://github.com/jsheradin/otter2_misc/raw/master/otter2-freedom-boot-10.4.6.img) md5sum: 80f9b9ba80910de361463560f86f315a
* [otter2-twrp-2.6.3.0-recovery.img](https://github.com/jsheradin/otter2_misc/raw/master/otter2-twrp-2.7.0.0-recovery.img) md5sum: a66a8cbab92fd5daaa85f6303e9cc0c3
* [otter2-u-boot-prod-10.2.4.bin](https://github.com/jsheradin/otter2_misc/raw/master/otter2-u-boot-prod-10.2.4.bin) md5sum: 5b03a7b428325de8d360ec201a745498
* [stack](https://github.com/jsheradin/otter2_misc/raw/master/stack) md5sum: 3cee2b7f3233fc3a1e10373677b8c1a9

Get a ROM and Gapps pair from here:

https://github.com/jsheradin/android_device_amazon_otter2/releases

### STEP 3 - Stack override

Install the stack override in /system:

ADB commands entered while booted up into STOCK rooted Amazon Kindle OS -- May need to enable ADB debugging in Settings.

    adb push stack /sdcard/
    adb shell su -c "dd if=/sdcard/stack of=/dev/block/platform/omap/omap_hsmmc.1/by-name/system bs=6519488 seek=1"

### Step 4 - Enter fastboot

#### Method 1

You should be able to run:

    adb shell "su -c 'reboot bootloader'"

Fastboot screen should then show up (traffic light).

#### Method 2
Make, borrow, or buy a "fastboot cable" with the following pinout

![fastboot](https://raw.githubusercontent.com/jsheradin/otter2_misc/master/fastbootcable.jpg)

Connect fastboot cable, and reboot the device.

Fastboot screen should then show up (traffic light).

### STEP 5 - Flash bootloader and recovery

*DONT RE-FLASH FREEDOM-BOOT.IMG LATER AFTER FLASHING CM/LineageOS -- IT'S FOR STOCK AMAZON OS ONLY / FIRST INSTALL ONLY*

Code:

    fastboot -i 0x1949 flash bootloader otter2-u-boot-prod-10.2.4.bin
    fastboot -i 0x1949 flash boot otter2-freedom-boot-10.4.6.img
    fastboot -i 0x1949 flash recovery otter2-twrp-2.7.0.0-recovery.img
    fastboot -i 0x1949 reboot

You should see the bootmenu with a BLUE logo.

### Step 6 - Install LineageOS and Gapps

During the blue logo screen press the power button to cycle through options and select "Recovery" to enter TWRP.

In TWRP recovery:

* Wipe > Format Data
* Wipe > Advanced Wipe > Select everything
* Wipe > Factory reset

On computer:

    adb push /path/to/lineage-XX-XXXXXXXX-UNOFFICIAL-otter2.zip /sdcard/
    adb push /path/to/open_gapps-arm-4.4-XXXX-XXXXXXXX.zip /sdcard/

In TWRP recovery:

* Install > /sdcard/lineage-XX-XXXXXXXX-UNOFFICIAL-otter2.zip
* Install > /sdcard/open_gapps-arm-4.4-XXXX-XXXXXXXX.zip
* Reboot (First boot will probably take a few minutes)

Tada! You should now have LineageOS on your otter2.

### Returning to stock:
** FIND THE FILES WE BACKED UP IN STEP #1 OR DOWNLOAD NEW ONES FROM THE RECOVERY THREAD HERE: **
http://forum.xda-developers.com/show....php?t=2035047

Once you have these files (we'll name them boot.img/recovery.img and system.img for the purpose of these instructions, but they could be different on your system).

Plug in your fastboot cable and reboot so that you see the fastboot screen and enter the following commands:
Code:

    fastboot -i 0x1949 flash boot boot.img
    fastboot -i 0x1949 flash recovery recovery.img
    fastboot -i 0x1949 flash system system.img # This one will take a few minutes
    fastboot -i 0x1949 reboot

Enjoy

THANKS TO:

    https://forum.xda-developers.com/showthread.php?t=2100963
    fattire for finding the original bootloader exploit
    TESTERS: powerpoint45 and FMKilo
    Hashcode for making the original guide and device tree
