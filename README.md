# Dell Wyze 3040

This is mainly about building and installing OpenWRT on the Wyse 3040. I have included as much additional information as possible.

## TOC

- [Dell Wyze 3040](#dell-wyze-3040)
  * [Notes](#notes)
    + [Dell Pages](#dell-pages)
    + [Articles](#articles)
  * [Hardware](#hardware)
  * [Boot Options](#boot-options)
  * [BIOS Updates](#bios-updates)
  * [BIOS Setup](#bios-setup)
  * [Operating Systems](#operating-systems)
    + [Linux](#linux)
      - [OpenWRT](#openwrt)
        * [Building](#building)
        * [My Unofficial Builds](#my-unofficial-builds)
      - [ThinLinux](#thinlinux)
      - [Mint](#mint)
      - [antiX Linux](#antix-linux)
      - [VyOS](#vyos)
  * [Images](#images)
    + [Device](#device)
    + [OpenWRT](#openwrt-1)
    + [antiX](#antix)


## Notes

### Dell Pages

* [Wyse 3040 Thin Client](https://www.dell.com/en-us/work/shop/wyse-endpoints-and-software/wyse-3040-thin-client/spd/wyse-3040-thin-client)
* [Wyse 3040 Disassembly and Reassembly](https://www.dell.com/support/manuals/en-us/wyse-3040-thin-client/3040_ug/disassembly-and-reassembly?guid=guid-2832a3ba-4312-4770-98e8-dd0261ca350c&lang=en-us)
* [Wyse 3040 Client User Guide](https://www.dell.com/support/manuals/en-us/wyse-3040-thin-client/3040_ug/system-specifications?guid=guid-b35dd1df-32f3-4c36-84a9-52d9a5c0810c&lang=en-us)

### Articles

* [Thin Clients Wyse 3040 (N10D): Hardware](https://www.parkytowers.me.uk/thin/wyse/3040/)
* [Thin Clients Wyse 3040 Disassembly](https://www.parkytowers.me.uk/thin/wyse/3040/disassembly.shtml)
* [A Baby WYSE, the 3040](https://blog.kroy.io/2020/01/17/the-baby-wyse-the-dell-3040/)
* [Install a New OS On a Dell Wyse 3040](https://qubitsandbytes.co.uk/install-a-new-os-on-a-dell-wyse-3040/)

## Hardware

* **Power:** My device shows 5V @ 3A, I use [this power adapter](https://www.amazon.com/dp/B0877ZTXT2?ref=ppx_yo2ov_dt_b_product_details).
    * [Video Power Mod, Teardown, UEFI/BIOS Quirks](https://www.youtube.com/watch?v=6Ls7xn4qdlk).
* **CPU:** [Intel(R) Atom(TM) x5-Z8350 CPU @ 1.44GHz](https://ark.intel.com/content/www/us/en/ark/products/93361/intel-atom-x5z8350-processor-2m-cache-up-to-1-92-ghz.html)
* **Memory:** 2GB DDR3 1600 MHz Soldered
* **Audio:** PulseAudio shows:
    * Intel HDMI/DP LPE Audio - snd_hdmi_lpe_audio
    * Dell Inc. - Wyse 3040 ThinClient -- CherryTrailCR - snd_soc_sst_cht_bsw_rt5672
* **USB:** 
    * 3x USB 2.0
    * 1x USB 3.1
* **Video:** 
    * 2x DisplayPort 
    * Max Resolution 1920x1080
    * Intel Corporation Atom Processor x5-E8000 Integrated Graphics Controller
* **Ethernet:** 1x Ethernet 1 Gbps - Realtek RTL8111/8168/8411
* **Expansion:** M.2 E-Key
    * This is NOT a true E-Key slot, it is an *SDIO interface*.
    * There is more SDIO wifi info in this [FreeBSD article](https://wiki.freebsd.org/SDIO).
    * I tried various E-Key wifi cards, none worked.
    * Reference this `lspci` line:

            00:11.0 SD Host controller: Intel Corporation 
                    Atom/Celeron/Pentium Processor x5-E8000/J3xxx/N3xxx 
                    Series SDIO Controller (rev 36)
* **lshw - Hardware** [lshw.txt](https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/notes/lshw.txt?token=GHSAT0AAAAAABTRQNXQO5Y66VO6U6DAEJSKYUW4K5A)
* **lspci - PCI** [lspci.txt](https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/notes/lspci.txt?token=GHSAT0AAAAAABTRQNXQLILXPEOHE2AN37DQYUW4LWA)
* **pacmd - Audio** [pacmd.txt](https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/notes/pacmd.txt?token=GHSAT0AAAAAABTRQNXQB4UFOPRH6KLZF7SUYUXAXAA)

## Boot Options

* F2 - Boots BIOS
* F12 - Boot Menu

## BIOS Updates

My unit would not let me update to 1.2.5 from 1.2.4, no clue why. It just showed `<invalid>` in the updater.

* Download from Dell: [https://www.dell.com/support/home/en-us/product-support/product/wyse-3040-thin-client/drivers](https://www.dell.com/support/home/en-us/product-support/product/wyse-3040-thin-client/drivers)
* Unzip it if it is zipped.
* Copy to a USB flash drive, FAT32 formatted.
* Boot the with F12 key.
* Follow instructions on screen.

## BIOS Setup

* Enter BIOS with F12 
* System Configuration -> USB Configuration, then check **Enable USB Boot Support**
* Secure Boot -> Secure Boot Enaable, set to **Disabled**
* Power Management -> AC Recovery, set to **Power On**
* Save Settings

## Operating Systems

### Linux

#### OpenWRT

The current build of OpenWRT does not install on the Wyze.  From what I understand, this is due to the default build outputting to Serial.  I built my own version specifiying the Atom processor with no serial output and had some good luck.

##### Building

I'm not an expert on building OpenWRT, this is the first custom build I've done.  I may be missing some stuff or could be wrong about any amount of this.  Feel free to drop a comment/bug/pull request.

**Requirements:**

```
binutils bzip2 diff find flex gawk gcc-6+ getopt grep 
install libc-dev libz-dev make4.1+ perl python3.6+ rsync 
subversion unzip which
```
**Install:**

I used directions from [OpenWRT Build System Usage](https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem).

```
git clone git@github.com:openwrt/openwrt.git
cd openwrt
# list tags
git tag
# I'm using the latest rc as of today
git checkout v22.03.0-rc3

# Update/install the feeds
./scripts/feeds update -a
./scripts/feeds install -a

# Download my config
wget "https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/config/config?token=GHSAT0AAAAAABTRQNXRKMMNAU3TGNBJGEREYUW7HAA" -O .config

# Otherwise make your own
# Make your config
make menuconfig
# Make your kernel config
make kernel_menuconfig

# Do the build, it'll take like an hour.
make world -j$(nproc) V=sc

# You can find the builds in: openwrt/bin/
# This includes all the packages.
find . -name "openwrt-x86-64-generic-ext4-combined-efi.img.gz"
```

Gunzip and copy the `openwrt-x86-64-generic-ext4-combined-efi.img.gz` and copy `openwrt-x86-64-generic-ext4-combined-efi.img` file to a USB stick, boot the device off a secondary drive with linux on it, as described below.

Open a terminal from your LiveUSB stick.

```
# Find your EMMC drive, mine is: /dev/mmcblk0
sudo fdisk -l

# Find the image
find /media/mint -name openwrt-x86-64-generic-ext4-combined-efi.img

# Write the image.
sudo dd if=./openwrt-x86-64-generic-ext4-combined-efi.img of=/dev/mmcblk0 bs=512 status=progress

# Use fdisk to show your partitions
sudo fdisk -l

# It'll probably show some partition errors, we will fix those now.
# For some reason the img file has the partitions in a weird order.
sudo gdisk /dev/mmcblk0
        s - sort partitions
        w - write table and exit
        Y - Do you want to correct this problem?
        Y - Do you want to proceed?

# Should return
# OK; writing new GUID partition table (GPT) to /dev/mmcblk0.
# The operation has completed successfully.

# fdisk should no longer show errors.
sudo fdisk -l 
```

From here, I used gparted to resize the system partitions, because I couldn't seem to get it to go properly in gdisk, I probably just don't know how to use gdisk properly.


##### My Unofficial Builds

* [openwrt.atom.build.20220531.img.gz](https://github.com/pjobson/openwrt-dell-wyze-3040/raw/main/builds/openwrt.atom.build.20220531.img.gz)
* [openwrt.atom.build.20220528.img.gz](https://github.com/pjobson/openwrt-dell-wyze-3040/raw/main/builds/openwrt.atom.build.20220528.img.gz)

**Installing:**

* You need two USB sticks, one you should put some linux distribution on, the other you should format.  I formatted mine to ext4, but it probably doesn't matter.
* Download a build.
* `gunzip openwrt.atom.build.img.*.gz`
* Copy openwrt.atom.build.########.img to your second USB stick.
* Put your Linux boot stick into one of the USB2.0 slots.  
* Put the stick with the OpenWRT image on it in the USB3.1 slot.
* Boot the unit hitting F12 to get to the boot menu, then select the linux boot stick.
* Open a terminal.
* Find your EMMC with: `sudo fdisk -l`
* Both of mine were: `/dev/mmcblk0`
* Find your img file, if you're using mint it'll be in media: `ls -laR /media/mint/`
* Write the image using `dd`.
* `sudo dd if=/path/to/openwrt.atom.build.img of=/dev/mmcblk0 bs=4096 status=progress`
* Wait for awhile.
* Reboot the device removing all USB sticks. I just power cycle it, because I don't care.
* Setup your network as I desribe in this gist: [OpenWRT on x86_64 - First Boot](https://gist.github.com/pjobson/3584f36dadc8c349fac9abf1db22b5dc#first-boot)
* You can now open LUCI in your browser by going to whatever IP you set the unit to.

#### ThinLinux

Dell has their own linux distributino for the Wyze 3040 called ThinLinux.  You can download it from their [Support Site](https://www.dell.com/support/home/en-us/drivers/driversdetails?driverid=jrr5m).  The file is `2.2.0.00_3040_16GB_merlin.exe` which you can extract the `img` files from with 7zip, like: `7z x 2.2.0.00_3040_16GB_merlin.exe`.  I have not tested this and I probably won't.

#### Mint

[Linux Mint](https://linuxmint.com/) will boot off the USB stick and I have used the Live USB stick to mess around with the device.  The minimum system requirements for Mint are 2GB of RAM and 20GB of disk space.  The installer will crash shortly after selecting keyboard type.

#### antiX Linux

[antiX](https://antixlinux.com/) is probably a better option as the minimum system requirements are 256MB RAM and 5GB HDD.  This seems to install and run fine, you will need to write the ISO to a USB with [live-usb-maker](https://github.com/MX-Linux/lum-qt-appimage/releases) as the default antiX distribution ISO is legacy boot only.

#### VyOS

This person has done some work with VyOS. [https://blog.kroy.io/2020/01/17/the-baby-wyse-the-dell-3040/](https://blog.kroy.io/2020/01/17/the-baby-wyse-the-dell-3040/)

## Images

### Device

![](https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/img/front.jpg)
![](https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/img/rear.jpg)
![](https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/img/angle.jpg)

### OpenWRT

![](https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/img/neofetch.png)
![](https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/img/luci_status.png)

### antiX

![](https://raw.githubusercontent.com/pjobson/openwrt-dell-wyze-3040/main/img/antix-wyse-3040.png)
