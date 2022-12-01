# Hardware

* **Power:** My device shows 5V @ 3A, I use [this power adapter](https://www.amazon.com/dp/B0877ZTXT2?ref=ppx_yo2ov_dt_b_product_details).
    * [Video Power Mod, Teardown, UEFI/BIOS Quirks](https://www.youtube.com/watch?v=6Ls7xn4qdlk).
* **CPU:** [Intel(R) Atom(TM) x5-Z8350 CPU @ 1.44GHz](https://ark.intel.com/content/www/us/en/ark/products/93361/intel-atom-x5z8350-processor-2m-cache-up-to-1-92-ghz.html)
* **Memory:** 2GB DDR3 1600 MHz Soldered
* **Drive:** 8GB EMMC SK Hynix H56C4HP4A - There is also a 16GB version of this board.
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