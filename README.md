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

Part 1: BIOS Modification
======================
Like many laptop manufacturers, Lenovo has chosen to only expose the basic BIOS settings that a user would normally need to tweak - boot options, passwords, USB 3.0 support, fingerprint reader settings, etc. Unfortunately, due to a compatibility check Apple has included in their graphics driver for Broadwell chipsets (hereafter referred to as AppleIntelBDWGraphicsFramebuffer), you will get a kernel panic as soon as the driver loads if your dedicated video memory (hereafter referred to as DVMT pre-allocated memory) is not set to a value between 64MB & 128MB<sup id="dvmt">[3](#dvmt)</sup>. My ThinkPad's DVMT pre-alloc was set to 32MB, which is too small for OS X. It's worth checking to see if yours is set to a different value - if it is, you may be able to skip to part 2 (see the link in footnote 3 for more details on how to check). If it's not set to a value in that range, however, you must install a modified BIOS that unlocks access to that setting so you may change the value to something that is acceptable for OS X.

> :exclamation:**Watch out!** Proceeding past this point will absolutely, without a doubt, void your laptop's warranty. If you aren't comfortable with electronics, troubleshooting, and the possibility of bricking your laptop, do not proceed past this point. In addition, I cannot guarantee that you will be successful installing OS X based on this guide, nor do I take any responsibility for any damage caused as a result of following these instructions or using the software found in this repository.

Normally, flashing a modified BIOS is a relatively easy process. You just have to run some software, sometimes from a bootable USB drive with a version of DOS, that will write the new BIOS to the EEPROM chip in your computer. However, Lenovo has set Protected Range Registers in their BIOS, preventing the flash tool from accessing the chip. As a result, we have to resort to a more archaic approach: directly writing the modified BIOS to the chip.

> :ribbon: **Credit where credit is due:** This guide is largely based upon the work of a few brave pioneers such as [booger_sniffer5000](http://www.insanelymac.com/forum/user/102287-booger-sniffer5000/) at InsanelyMac<sup id="biosguide">[4](#biosguide)</sup> and [DotBowder](https://www.bios-mods.com/forum/User-DotBowder) at BIOS-Mods<sup id="flashtutorial">[5](#flashtutorial)</sup> who wrote extensive guides on how to perform this process. I've tailored this guide specifically to the T450s and included my own pictures and screenshots of the process.

1. Remove the bottom cover of your ThinkPad by unscrewing the X screws indicated in the picture below. The screws may not completely come out of their sockets; this is expected and designed to help you keep track of the tiny screws. Starting at the back in the external battery compartment, gently pry up the bottom casing with a plastic opening tool or a stiff plastic card. There is a bit of adhesive that attaches the heatsink fan assembly to the bottom casing, but it easily separates with a small tug.

	[picture]
	
2. Locate the internal battery connector, and unplug it. Remove the supplemental RAM DIMM, if installed. Lift the black plastic film under the RAM slot, and carefully start peeling it back until you can see the BIOS chip. Use some adhesive tape to hold the film out of the way.

	[picture]
	
3. On your CH341A programmer, lift the ZIF lever to open the programming ports. Take the small adapter board included with your test clips and place it in the appropriate slots for SPI 25 on the programmer, following the diagram silkscreened onto the circuit board (pin 1 was toward the middle of the block on mine). Attach the plug end of the test clips to the adapter board. The small arrow on the plug, under the pink wire, indicates the orientation it should be plugged in, relative to pin 1.

	[picture]
	
4. Carefully attach the test clips to the newly-exposed BIOS chip, making sure to align the contacts with the traces leading to the chip. The contact with the pink wire should be connected to pin 1, which is indicated on the chip by a small recessed circle in the epoxy.
5. Obtain another computer running a *nix-based operating system. This can be the Mac you borrowed to create your USB installer, or it could be another machine running off of an Ubuntu live USB. I haven't tested this process on a Mac, but it should work the same way. There is also a proprietary, Chinese Windows application for performing this process, but I had a lot of problems with getting it properly write and erase, and do not recommend it.
6. Install Git and the libusb development libraries. For Ubuntu, run this command in Terminal:
	```
	sudo apt-get install git libusb-1.0.0-dev
	```

7. Clone the ch341prog repo, change directories, and compile:
	```
	git clone https://github.com/setarcos/ch341prog.git
	cd ch341prog
	make
	```
8. Download the modded BIOS image from this repo:
	```
	wget https://raw.githubusercontent.com/stevenmirabito/T450s-Hackintosh/master/bios/t450s_mod.bin
	```
	
9. Plug the assembled CH341A programmer into your computer's USB port. The status LED should illuminate. If the light dims after a moment and you cannot communicate with the chip in the next few steps, check all of your connections and ensure that everything is connected in the correct orientation. The programmer powers itself down if it detects that it is not connected correctly.
10. Enter the following command into the terminal to check the connection between the BIOS chip and the programmer, and ensure that it reports a manufacturer ID of `ef`, memory type of `4018`, capacity of `18`, and chip capacity of `16777216` (this may vary depending on your specific machine, but the capacity values should be the same):
	```
	./ch341prog -i
	```
	
11. Back up your current BIOS:
> :exclamation:**Watch out!** Do NOT skip this extremely important step. If anything goes wrong, you should be able to restore your original BIOS with this backup and be back right where you were.

	```
	./ch341prog -r backup.bin
	```
	
12. Erase the BIOS chip:
	```
	./ch341prog -e
	```

13. Read back the BIOS to ensure it erased properly:
	```
	./ch341prog -r blank.bin
	xxd blank.bin blank.hex
	nano blank.hex
	```
The file will open in a text editor. Scroll through it a bit and make sure that the file is composed entirely of `FF` values, indicating that it's blank. If it's not, the chip was not erased properly. Check your connections and try again.

14. Write the modded bios to the chip:
	```
	./ch341prog -w t450s_mod.bin
	```
	
15. Read back the BIOS to ensure that it was written properly:
	```
	./ch341prog -r verify.bin
	xxd verify.bin verify.hex
	xxd t450s_mod.bin t450s_mod.hex
	diff t450s_mod.hex verify.hex
	```
If the last command doesn't print anything out, the two files match and the modded BIOS was successfully written. If not, check your connections and return to step 12.

16. Disconnect the clip from the BIOS chip, then disconnect the CMOS battery and wait 30 seconds. Reassemble the laptop.
17. Power on the laptop. If all goes well, it will turn on and beep 10 times in 2 groups of 5 (simply a warning that the firmware has been modified), then continue to BIOS setup with the new Advanced menu unlocked! :smile:

Now that we have an unlocked BIOS, we can configure the necessary settings for OS X. To make sure that we're on the same page, go to the `Restart` tab and select `Load Setup Defaults` to load the default settings. Once that's been applied, you need to change the following settings:

```
Under Advanced -> System Agent (SA) Configuration -> Graphics Configuration:
	DVMT Pre-Allocated -> 128MB
    DVMT Total Gfx Mem -> 256MB

Under Config -> Display:
	Total Graphics Memory -> 256MB

Under Startup:
	UEFI/Legacy Boot -> UEFI Only
    CSM Support -> Yes
```

Part 2: Prepare Installation Media
============================
Grab that Mac you borrowed, open the App Store, and search for `El Capitan`, then download the installer. It's fairly large, so it may take a little time to download. In the meantime, let's get Clover set up to boot the installation media.

1. Plug in your 16GB or larger USB drive, then open up Terminal and enter the following command:
	```
	diskutil list
	```

2. Look for your USB drive in the list (probably one or two partitions totalling the size of the drive, marked "external, physical") and note the disk number.
3. Enter the following command to repartition the drive, replacing `diskX` with the disk number from step 2:
> :exclamation:**Watch out!** This will permanently erase any data on the external drive. Make sure you get the disk number correct or you'll wipe the Mac's hard drive instead!

	```
	diskutil partitionDisk /dev/diskX 1 GPT HFS+J "install_osx" R
	```
	
4. Download the latest version of the Clover installer from (v3330 or newer is required):
http://sourceforge.net/projects/cloverefiboot/

*To be continued...*

----------

1.<b id="specs">&nbsp;</b>[T450s Platform Specifications (Lenovo, page 66 in the PDF)](http://www.lenovo.com/psref/pdf/tabook_WE.pdf) [↩](#specs)

2.<b id="bcm94352z">&nbsp;</b>[Where can I get BCM94352Z? (tonymacx86)](http://www.tonymacx86.com/yosemite-laptop-support/160258-where-can-i-get-bcm94352z.html) [↩](#bcm94352z)

3.<b id="dvmt">&nbsp;</b>[\[GUIDE\] Intel HD Graphics 5500 on OS X Yosemite 10.10.3 (tonymacx86)](http://www.tonymacx86.com/yosemite-laptop-support/162062-guide-intel-hd-graphics-5500-os-x-yosemite-10-10-3-a.html) [↩](#dvmt)

4.<b id="biosguide">&nbsp;</b>[Lenovo ThinkPad T450S HD5500 Full Hardware Acceleration (InsanelyMac)](http://www.insanelymac.com/forum/topic/309493-lenovo-thinkpad-t450s-hd5500-full-hardware-acceleration/) [↩](#biosguide)

5.<b id="flashtutorial">&nbsp;</b>[Flashing your bios (BIOS-Mods)](https://www.bios-mods.com/forum/Thread-REQUEST-Lenovo-IdeaPad-S405-Whitelist-Removal?pid=74571#pid74571) [↩](#flashtutorial)
