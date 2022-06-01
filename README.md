# Dell Wyze 3040

## Notes

xxx

## Hardware

* **CPU:** [Intel(R) Atom(TM) x5-Z8350 CPU @ 1.44GHz](https://ark.intel.com/content/www/us/en/ark/products/93361/intel-atom-x5z8350-processor-2m-cache-up-to-1-92-ghz.html)
* **Memory:** 2GB DDR3 1600 MHz Soldered
* **Audio:** Composite audio jack - unknown chipset
* **USB:** 
    * 3x USB 2.0
    * 1x USB 3.1
* **Video:** 
    * 2x DisplayPort 
    * Max Resolution 1920x1080
    * Intel Corporation Atom Processor x5-E8000 Integrated Graphics Controller
* **Ethernet:** 1x Ethernet 1 Gbps - Realtek RTL8111/8168/8411
* **Expansion:** M.2 E-Key
    * This is NOT a true E-Key slot, it is an *SDIO interface*, reference this `lspci` line:

            00:11.0 SD Host controller: Intel Corporation 
                    Atom/Celeron/Pentium Processor x5-E8000/J3xxx/N3xxx 
                    Series SDIO Controller (rev 36)
    * There is more SDIO wifi info in this [FreeBSD article](https://wiki.freebsd.org/SDIO).
    * I tried various 




## Operating Systems

### Linux

#### ThinLinux

Dell has their own linux distributino for the Wyze 3040 called ThinLinux.  You can download it from their [Support Site](https://www.dell.com/support/home/en-us/drivers/driversdetails?driverid=jrr5m).  The file is `2.2.0.00_3040_16GB_merlin.exe` which you can extract the `img` files from with 7zip, like: `7z x 2.2.0.00_3040_16GB_merlin.exe`.  I have not tested this and I probably won't.