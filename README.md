# Dell Wyze 3040 

This is mainly about building and installing OpenWRT on the Wyse 3040. I have included as much additional information as possible.

I suspect this will work for any Intel Atom based processors, though I have not tested it.  I don't think the standard images are compiled with Atom support.  There's a special flag to toggle in `kernel_menuconfig` to enable Atom specifically.

## TOC

- [Dell Wyze 3040](#dell-wyze-3040)
  * [Notes](#notes)
  * [Hardware](#hardware)
  * [Boot Options](#boot-options)
  * [BIOS](#bios)
  * [Operating Systems](#operating-systems)
    + [OpenWRT](#openwrt)
      * [Building](#building)
      * [My Unofficial Builds](#my-unofficial-builds)
      * [Installing](#installing)
    + [ThinLinux](#thinlinux)
    + [Mint](#mint)
    + [antiX Linux](#antix-linux)
    + [VyOS](#vyos)
    + [Windows](#windows)
  * [Images](#images)
    + [Device](#device)
    + [OpenWRT](#openwrt-1)
    + [antiX](#antix)


## Notes

### Dell Pages

[dell pages](./dell_pages.md)

### Articles

[articles](./articles.md)

## Hardware

[hardware](./hardware.md)

## Boot Options

* F2 - Boots BIOS
* F12 - Boot Menu

## BIOS

### Updates

[bios update](./bios.md)

### Setup

* Enter BIOS with F12 
* System Configuration -> USB Configuration, then check **Enable USB Boot Support**
* Secure Boot -> Secure Boot Enaable, set to **Disabled**
* Power Management -> AC Recovery, set to **Power On**
* Save Settings

## Operating Systems

### OpenWRT

The current build of OpenWRT does not install on the Wyze.  From what I understand, this is due to the default build outputting to Serial.  I built my own version specifiying the Atom processor with no serial output and had some good luck.

#### Building

I'm not an expert on building OpenWRT, this is the first custom build I've done.  I may be missing some stuff or could be wrong about any amount of this.  Feel free to drop a comment/bug/pull request.

**Requirements:**

```
binutils bzip2 diff find flex gawk gcc-6+ getopt grep 
install libc-dev libz-dev make4.1+ perl python3.6+ rsync 
subversion unzip which
```
**Build:**

I used directions from [OpenWRT Build System Usage](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem).

```
git clone git@github.com:openwrt/openwrt.git
cd openwrt
# list tags
git tag
# I'm using the latest rc as of today
git checkout tags/v22.03.2

# Update/install the feeds
./scripts/feeds update -a
./scripts/feeds install -a

# Optional: Download my config
# wget "https://raw.githubusercontent.com/pjobson/dell-wyse-3040-openwrt/main/config/config" -O .config

# Otherwise make your own
# Make your config
make menuconfig -j$(nproc)

# Select options:
#   Target System -> x86
#   Subtarget -> x86_64
#   Target Profile -> Generic x86/64
#   Target Images -> (uncheck) Use Console Terminal (in addition to Serial)
#   Target Images -> (clear out) Serial port device
#   Target Images -> (uncheck) GZip images
#   LuCI -> Collections -> luci
#   Select whatever else you want/don't want
# Save/Exit

# Make your kernel config
make kernel_menuconfig -j$(nproc)

# Select Options:
#   Processor type and features -> Processor family -> Intel Atom
# Save/Exit

# Do the build, it'll take like an hour.
make defconfig download clean world -j $(nproc) V=sc

# You can find the builds in: openwrt/bin/
# This includes all the packages.
find . -name "openwrt-x86-64-generic-ext4-combined-efi.img"
```

#### My Unofficial Builds

* [openwrt.atom.v22.03.2.img.gz](https://github.com/pjobson/openwrt-dell-wyze-3040/raw/main/builds/openwrt.atom.v22.03.2.img.gz)

#### Installing

* You need two USB sticks, one you should put some linux distribution on, the other you should format.  I formatted mine to ext4, but it probably doesn't matter.
* Download the image from above or do your build.
* `gunzip openwrt.atom.v22.03.2.img.gz`
* Copy `openwrt.atom.v22.03.2.img` to your second USB stick.
* Put your Linux boot stick into one of the USB2.0 slots.  
* Put the stick with the OpenWRT image on it in the USB3.1 slot.
* Boot the unit hitting F12 to get to the boot menu, then select the linux boot stick.
* Open a terminal.
* Find your EMMC with: `sudo fdisk -l`
* Both of mine were: `/dev/mmcblk0`
* Find your img file, if you're using mint it'll be in media: `ls -laR /media/mint/`
* Write the image using `dd`.
* `sudo dd if=/path/to/openwrt.atom.build.img of=/dev/mmcblk0 bs=512 status=progress`
* Wait for awhile.
* Reboot the device removing all USB sticks. I just power cycle it, because I don't care.
* Setup your network as I desribe in this gist: [OpenWRT on x86_64 - First Boot](https://gist.github.com/pjobson/3584f36dadc8c349fac9abf1db22b5dc#first-boot)
* You can now open LUCI in your browser by going to whatever IP you set the unit to.
* Resize your partition.
    * Reboot back to your LiveUSB
    * Open gparted and resize the ext4 partition.
    * NOTE: DO NOT resize it to the full 8GB or it will not boot, keep slightly under 8GB.  I have no clue why.  I left 100MB at the end of my drive and it worked fine.

### ThinLinux

Dell has their own linux distributino for the Wyze 3040 called ThinLinux.  You can download it from their [Support Site](https://www.dell.com/support/home/en-us/drivers/driversdetails?driverid=jrr5m).  The file is `2.2.0.00_3040_16GB_merlin.exe` which you can extract the `img` files from with 7zip, like: `7z x 2.2.0.00_3040_16GB_merlin.exe`.  I have not tested this and I probably won't.

### Mint

[Linux Mint](https://linuxmint.com/) will boot off the USB stick and I have used the Live USB stick to mess around with the device.  The minimum system requirements for Mint are 2GB of RAM and 20GB of disk space.  The installer will crash shortly after selecting keyboard type.

### antiX Linux

[antiX](https://antixlinux.com/) is probably a better option as the minimum system requirements are 256MB RAM and 5GB HDD.  This seems to install and run fine, you will need to write the ISO to a USB with [live-usb-maker](https://github.com/MX-Linux/lum-qt-appimage/releases) as the default antiX distribution ISO is legacy boot only.

### VyOS

This person has done some work with VyOS. [https://blog.kroy.io/2020/01/17/the-baby-wyse-the-dell-3040/](https://blog.kroy.io/2020/01/17/the-baby-wyse-the-dell-3040/)

### Windows

Requirements are:

* 1GHz CPU
* 1GB RAM
* 16GB HDD

Unless you have the 16GB version of the 3040, it probably won't work.  There's an edition of Win10 called [Tiny10](https://archive.org/details/tiny-10_202105), which you may get lucky with.

## Images

[images](./images.md)