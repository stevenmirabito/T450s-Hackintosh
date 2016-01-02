Introduction
===========
This guide is intended to be a record of how I installed OS X El Capitan on my Lenovo ThinkPad T450s (model 20BXCTO1WW). I have tried to make this guide as self-sufficient as possible, linking to sources where required and providing the required software and tools in this repository.

My laptop's specs<sup id="specs">[1](#specs)</sup> (pre-Hackintosh):

 - **CPU:** Intel® Core™ i5-5200U @ 2.20GHz
 - **Chipset**: Intel® Broadwell-U Rev. 09
 - **RAM**: 4GB (soldered to the board) + [Crucial 8GB DDR3L-1600 SODIMM](http://www.amazon.com/gp/product/B006YG8X9Y)
 - **Graphics:** Intel HD Graphics 5500 (1920x1080 FHD IPS non-touch screen)
 - **Ethernet:** Intel® Ethernet Connection I218-V
 - **Audio:** Realtek® ALC3232 HD Audio
 - **WLAN:** Intel Dual Band Wireless-AC 7260 M.2/NGFF 802.11ac Wi-Fi + Bluetooth combo adapter
 - **Storage:** [MyDigitalSSD 256GB M.2/NGFF Super Boot Drive](http://www.amazon.com/gp/product/B00NY4VIPA) + stock 500GB SATA hard drive

Prerequisites
===========
Before you begin, please make sure that you have a 16GB or larger flash drive (the more flash drives the better, although they don't all have to be that big) and access to a real, working Mac with access to the Mac App Store. There are alternative methods that involve coaxing Snow Leopard to run on something you own or virtualizing a recent version of OS X in VirtualBox, but you will have the easiest time and run into the least amount of hassle if you just borrow a friend's Mac for a few hours.

> :exclamation:**Watch out!** Many experienced Hackintosh users and developers, as well as the author of this guide, will refuse to provide support if you do not create your USB installer exactly as described on a real Mac. If you absolutely must use something other than a clean DMG downloaded from the Mac App Store with the included utility to create your USB installer, don't expect to find help with installation issues.

If you run into issues and need help, there are numerous resources available. Check out the [El Capitan Laptop Support forum on tonymacx86](http://www.tonymacx86.com/el-capitan-laptop-support/) or the [OSx86 10.11 (El Capitan) forum on InsanelyMac](http://www.insanelymac.com/forum/forum/503-osx86-1011-el-capitan/) - you'll find that many of the sources referenced in this guide are from one of those two fantastic communities. Also, shoutout to [RehabMan](http://www.tonymacx86.com/members/rehabman/) ([GitHub](https://github.com/RehabMan)) - without his guides, resources, and years of dedication to the community, this project may not have been possible.

In addition to the above, you will also need the following tools and components (further explanation can be found in the relevant portions of this guide):

> :bulb:**Tip:** The links below are merely a suggestion and simply represent the hardware I actually bought. If you're willing to wait about a month for shipping from China, the programmer and test clips can be found on eBay for about half the price.

 - [SOIC8 SOP8 Flash Chip IC Test Clips](http://www.amazon.com/gp/product/B00V9QNAC4)
 - [CH341A Series USB EEPROM BIOS Programmer](http://www.amazon.com/gp/product/B013Q5P3ES)
 - [Broadcom® BCM94352Z M.2/NGFF 802.11ac Wi-Fi + Bluetooth combo adapter](https://partstore.com/Part/Lenovo/Lenovo/20200480/Refurbished.aspx)
	 - Can be found marketed under Lenovo part number 20200480 or Dell part number DW1560<sup id="bcm94352z">[2](#bcm94352z)</sup>

Step 1: BIOS Modification
======================
Like many laptop manufacturers, Lenovo has chosen to only expose the basic BIOS settings that a user would normally need to tweak - boot options, passwords, USB 3.0 support, fingerprint reader settings, etc. Unfortunately, due to a compatibility check Apple has included in their graphics driver for Broadwell chipsets (hereafter referred to as AppleIntelBDWGraphicsFramebuffer), you will get a kernel panic as soon as the driver loads if your dedicated video memory (hereafter referred to as DVMT pre-allocated memory) is not set to a value between 64MB & 128MB<sup id="dvmt">[3](#dvmt)</sup>. My ThinkPad's DVMT pre-alloc was set to 128MB, which is too large for OS X. It's worth checking to see if yours is set to a different value - if it is, you may be able to skip to step 2 (see the link in footnote 3 for more details on how to check). If it's not set to a value in that range, however, you must install a modified BIOS that unlocks access to that setting so you may change the value to something that is acceptable for OS X.

> :exclamation:**Watch out!** Proceeding past this point will absolutely, without a doubt, void your laptop's warranty. If you aren't comfortable with electronics, troubleshooting, and the possibility of bricking your laptop, do not proceed past this point. In addition, I cannot guarantee that you will be successful installing OS X based on this guide, nor do I take any responsibility for any damage caused as a result of following these instructions or using the software found in this repository.

Normally, flashing a modified BIOS is a relatively easy process. You just have to run some software, sometimes from a bootable USB drive with a version of DOS, that will write the new BIOS to the EEPROM chip in your computer. However, Lenovo has set Protected Range Registers in their BIOS, preventing the flash tool from accessing the chip. As a result, we have to resort to a more archaic approach: directly writing the modified BIOS to the chip.

> :ribbon: **Credit where credit is due:** This guide is largely based upon the work of a few brave pioneers such as [booger_sniffer5000](http://www.insanelymac.com/forum/user/102287-booger-sniffer5000/) at InsanelyMac<sup id="biosguide">[4](#biosguide)</sup> and [DotBowder](https://www.bios-mods.com/forum/User-DotBowder) at BIOS-Mods<sup id="flashtutorial">[5](#flashtutorial)</sup> who wrote extensive guides on how to perform this process. I've tailored this guide specifically to the T450s and included my own pictures and screenshots of the process.

*January 2, 2015 - Waiting on my flashing tools. To be continued...*

----------

1.<b id="specs">&nbsp;</b>[T450s Platform Specifications (Lenovo, page 66 in the PDF)](http://www.lenovo.com/psref/pdf/tabook_WE.pdf) [↩](#specs)

2.<b id="bcm94352z">&nbsp;</b>[Where can I get BCM94352Z? (tonymacx86)](http://www.tonymacx86.com/yosemite-laptop-support/160258-where-can-i-get-bcm94352z.html) [↩](#bcm94352z)

3.<b id="dvmt">&nbsp;</b>[\[GUIDE\] Intel HD Graphics 5500 on OS X Yosemite 10.10.3 (tonymacx86)](http://www.tonymacx86.com/yosemite-laptop-support/162062-guide-intel-hd-graphics-5500-os-x-yosemite-10-10-3-a.html) [↩](#dvmt)

4.<b id="biosguide">&nbsp;</b>[Lenovo ThinkPad T450S HD5500 Full Hardware Acceleration (InsanelyMac)](http://www.insanelymac.com/forum/topic/309493-lenovo-thinkpad-t450s-hd5500-full-hardware-acceleration/) [↩](#biosguide)

5.<b id="flashtutorial">&nbsp;</b>[Flashing your bios (BIOS-Mods)](https://www.bios-mods.com/forum/Thread-REQUEST-Lenovo-IdeaPad-S405-Whitelist-Removal?pid=74571#pid74571) [↩](#flashtutorial)
