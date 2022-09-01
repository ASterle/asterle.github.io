---
layout: post
title: Flashing a Bricked Bios Chip!
date: 2022-09-01T00:00:00.000Z
published: true
---

# Intro

A little more than a year ago my trusty Sapphire Nitro RX 480 died while playing Mothergunship. Considering the GPU shortage was at its peak at the time, I was forced to mothball my gaming PC and put it into a long sleep.

Recently I purchased a new GPU and while I was hooking it up, I decided to also do some more maintenance on my PC which included a BIOS update on my X370 Taichi from version P6.62 to P7.00 and then P7.10.

To explain a bit: P6.62 BIOS was an unofficial "beta" that added support for Zen 3 processors, while P7.00 was a bridge bios to upgrade to P7.10 which had official support for Zen 3 *"Vermeer"* CPUs.

So I went to [ASRocks official website](https://www.asrock.com/mb/amd/x370%20taichi/index.asp#BIOS) and downloaded P7.00 and P7.10, put them on a USB stick, rebooted the PC and started flashing the BIOS with P7.00.

### The problems start

After it flashed successfully I was prompted to reboot the PC. So I did and after that... nothing. The computer would get stuck in a boot loop and that's it. Less than a day after I revived my PC with a new GPU, it was once again dead and unusable. The problem as you may have noticed was that P7.00 did not support *"Vermeer"* CPUs. So I couldn't use my CPU to boot and then flash to P7.10 to fix everything.

I did not have my old Ryzen 1600X, which was supported by the BIOS version, close at hand, but I could borrow my friends Ryzen 1400 and then hopefully use it to flash to P7.10 🤞.

I removed the side panel from the PC, removed the cooler and replaced the CPU. I didn't mount the CPU heatsink back as this was just temporary and I didn't want to mess around with the thermal paste. And the lack of a heatsink shouldn't be a problem, after all I flashed from P2.60 to P6.62 (multiple flashes due to bridge BIOS versions) on my 1600X without a mounted cooler (I forgot to flash the bios and removed the heatsink too early) and there were no problems.

Unfortunately this time something went wrong. I wasn't paying too much attention to the flashing process as I was cleaning thermal paste from my cooler, but at around 70% done, the PC turned off. And again it wouldn't post. Not on my 5800X nor my friends 1400...

# I bricked the BIOS chip, what do I do now?

Alright so at this point I knew I bricked my chip. None of the CPUs will work with it and I started researching potential fixes. I found three:

1. Buy a new motherboard. I was using an X370 for a Zen 3 CPU so I might as well buy a motherboard that is of a more appropriate age for my CPU :)
2. Buy a [new BIOS chip for my motherboard](https://www.ebay.com/itm/401347336292). Then desolder the old one and resolder the new chip onto the motherboard.
3. Buy a [BIOS chip programmer (CH341a)](https://www.amazon.de/-/en/Youmile-Programmer-CH341A-Adapter-Programming/dp/B09HGSY75G) and use it to flash the chip that is still on my motherboard.

In the end I decided on option 3. Buying a motherboard is expensive and I would do that as a last resort only. Buying a new chip was the cheapest option but I could damage the pads on the motherboard while desoldering, which meant I couldn't solder the new chip back on the motherboard (at least not with my pleb electronics skills), effectively destroying it.

So I decided using the CH341a programmer to flash the chip again is the best way to go as it would also be usable in the future. Everything with the shipping to Slovenia included cost me about 30€.

### Researching flashing BIOS chips externally

One of the more useful resources I used was [Overclock.net](https://www.overclock.net). Specifically the [ASRock X370 Taichi overclocking thread](https://www.overclock.net/threads/asrock-x370-taichi-overclocking-thread.1627407/). It had tons of information on the motherboard, and the combined knowledge of the users there was incredibly helpful for me.

Useful posts:
- [X370 Taichi SPI interface header](https://www.overclock.net/threads/asrock-x370-taichi-overclocking-thread.1627407/post-28845017) Apparently the motherboard has a connector for flashing the BIOS. I did not use it since the clip is more multipurpose and should work on all motherboards with a SOP8 style BIOS chip.
- [X370 Taichi SPI interface pinout](https://www.overclock.net/threads/spi-flash-header-pinout-for-use-with-8-pin-spi-flashing-tools-such-as-ch341a.1643534/) SPI flash header pinout for ASRock motherboards. Useful if you decide to use the SPI connector to flash the chip instead of the clip.
- [BIOS Chip voltages](https://www.overclock.net/threads/asrock-x370-taichi-overclocking-thread.1627407/post-28935166) a post explaining that you might need to use a 1.8V hat with the programmer (if the BIOS chip on your motherboard is using 1.8V to run.)
- [Don't have to remove the CPU](https://www.overclock.net/threads/asrock-x370-taichi-overclocking-thread.1627407/post-28935168) Super helpful post that explains that you just have to turn off the power for the PC and then flash, nothing else needed

Some YouTube videos with more information on using the CH341a

- [Modding BIOS files](https://youtu.be/duObbQLFHwI?t=2245) A long watch but this has helped me realize that the BIOS sometimes keeps extra information about your PC. Some OEM BIOS chips keep the Windows product key that came with the device and some have a MAC address written there etc. I did not need to use any BIOS file modding in my case.
- [External BIOS flashing](https://www.youtube.com/watch?v=lmYXiE2fQ6E) Basic guide for flashing the BIOS chip with CH341a, goes over the correct pin layout etc.

So with all the information I found on the internet, I started checking out my BIOS chip. I found it's location on the motherboard (thankfully it wasn't covered by the GPU...) and tried to see if there are any markings on it so I can identify it. Mine was covered with a sticker labeled P2.60 (which is the BIOS version it shipped with), so I removed it and then took a photo of the chip to try and identify it.

![BIOS chip details]({{ site.baseurl }}/images/2022-09-01-flashing-bricked-bios-chip/bios_chip.jpg)

Taking a photo and zooming in on the chip makes the text on it readable. Here we can see that my BIOS chip is the [Macronix mx25u12873f](https://www.mxic.com.tw/en-us/products/NOR-Flash/Serial-NOR-Flash/pages/spec.aspx?p=mx25u12873f&m=serial+nor+flash&n=pm2348). A quick Google search and we can find the chips product page. Also take note of the black triangle pointing to the lower left leg, it marks pin 1.

The 25 in its name tells us it's a 25 series chip, and the datasheet tells us its operating voltage is 1.65V-2V. In other words we need a programmer that supports 1.8V chips. The CH341a programmer I bought does not support 1.8V natively, so I bought it in a pack with a 1.8V hat. **Do not try to program it with 3.3V or 5V programmer. You can fry your BIOS chip!!**

Ok, so I know the chip voltage, I know its pin 1 location and I know that it is a 25 series EEPROM. This should be enough for me to attempt to connect the programmer and attempt to read it's contents.

# Flashing the chip

It was finally time to assemble the programmer and connect it to the BIOS chip. First I turned off the PSU power switch. Then I plugged the 1.8V hat into the programmer keeping in mind the correct pin orientation.

![CH341a pin orientation]({{ site.baseurl }}/images/2022-09-01-flashing-bricked-bios-chip/ch341a.jpg)
Once fully assembled the programmer looked like this.

![CH341a with hats]({{ site.baseurl }}/images/2022-09-01-flashing-bricked-bios-chip/ch341a_with_hats.jpg)
The red cable on the clip indicates pin 1. So I aligned it with the black triangle on the motherboard silkscreen and put the clip on the chip.

![Clip attached to chip]({{ site.baseurl }}/images/2022-09-01-flashing-bricked-bios-chip/clip_attached_to_chip.jpg)
Getting a good connection with these clips can be finicky so try and get a good contact with the pins.

Then it was time to plug in the CH341a programmer into a PC. I used a Linux Manjaro live usb which had **flashrom** included (most linux distros should have it preinstalled), opened a terminal and ran the command `sudo flashrom --programmer ch341a_spi` to see if it can identify the chip (which would be a good sign).

![Ghetto programmer setup]({{ site.baseurl }}/images/2022-09-01-flashing-bricked-bios-chip/ghetto_programmer_setup.jpg)

Unfortunately `No EEPROM/flash device found`. Twice... I moved the clips after each failed attempt and tried to get a good connection and third time's the charm!! Success, flashrom successfully identified the chip! 

![Connecting to the chip]({{ site.baseurl }}/images/2022-09-01-flashing-bricked-bios-chip/connecting_chip.png)
The next thing I did was to do a couple of test reads, where I did a SHA512 checksum on every result. And I was lucky (unlucky?) to actually have an unstable connection so I can demonstrate why you should do multiple reads and a checksum of every read.  Note that each read took around 2 minutes and 20 seconds. So CH341a seems to be a pretty slow programmer.

`sudo flashrom --programmer ch341a_spi -r 1.bin`

`sha512sum 1.bin`

The first read was fine, but the second had a different checksum... and then the third read failed to identify the chip. Apparently my clips slipped a bit and the pins lost contact. So I reseated the clip and tried again. This time another 2 reads had the same checksum as the first one so I was assured that these are the chip contents and that my connection is secure!

![Finished reading the existing data]({{ site.baseurl }}/images/2022-09-01-flashing-bricked-bios-chip/finished_reading.png)
Now at this point half of the BIOS chip had P7.00  contents and the other half had P7.10 written to it. I decided to just flash it directly with the P7.10 so I downloaded the BIOS file and started to write to the chip:

`sudo flashrom --programmer ch341a_spi -w X370TC7.10`

Unfortunately I lost the screenshot I took of the result but when writing, flashrom first reads the data, writes it and then verifies the results. Taking around 7 minutes in total. Everything seemed fine so I removed the clip, turned the power switch on the PSU on and started the PC.

And it worked :D. The PC booted like nothing was ever broken.
