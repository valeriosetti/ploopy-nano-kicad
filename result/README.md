# What the final board look like?

Well first of all here are (4 out of the 5) boards I received from JLCPCB

![](boards-from-jlcpcb.jpg)

Nothing to add here: they just look and work fine, as expected.

# Does the board fit into the 3D printed case?

Since I heavily copied the original design and placement the answer is yes, of course:

![](board-inside-case.jpg)

However, even though the board was OK, the 3D printed case was not at the beginning:

- the cut for the USB slot was for a microUSB, but I intentionally went for a typeC

- since I don't have a 3D printer, I had the case printed externally. It turned out that the board's holding parts were a bit too tall (so that the top part of the case was not closing properly)

Both of these problems were solved manually with the Dremel:

![](3d-case-with-arrows.jpg)

> The yellowish layer you see on the sensor is its original cover. I did not removed it yet because I'm still waiting for the trackball to arrive so until then I'll keep the protection on.

# Schematic fixes

Only a major fix was required compared to the original design:

- **do not mount** C13 and C14 since otherwise the oscillator does not start (verified with oscilloscope)

Then there is also another one, but this is due to the fact I was not able at all to find the ADNS-5050 component, so I replaced it with ADNS-5020 which is pin-to-pin compatible. In case you do the same then **do not install** also R3 and R4.

# How to build the FW?

You can easily download QMK firmware from its repository, checkout at the `master` branch and build it with:

```
util/docker_cmd.sh make clean && util/docker_cmd.sh qmk compile -kb ploopyco/trackball_nano/rev1_001 -km default:production
```

The `production` keyword was added because we're dealing with a completely new MCU which does not have a bootloader yet (well, not the one QMK expects) so we build both the FW and the bootloader at once.

> Yes, I used Docker. You can run the same commands without it, but perhaps you will need to install required packages.

# How to flash the board?

Luckily there is no need for an external fancy programmer since the board can be programmed with `avrdude` using a RaspberryPi:

![](program-through-rpi.jpg)
![](program-tp-zoom.jpg)

---
**WARNING**

In order not to uncorrectly power either the ploopy nano or the RPi, I strongly suggest to:

- disconnect the RPi from any power supply
- connect the RPi to the Ploopy
- connect power to the RPi and turn it on

---

Here is how I did the connection (but you can change it on the RPi side - check `avrdude` instructions for this):

| RPi pin # | wire color (RPi/Ploopy) | Ploopy TP
|-----------|-------------------------|----------
| 1         | violet/red              | 1
| 6         | blue/blue               | 6
| 12        | gray/violet             | 3
| 16        | green/orange            | 2
| 18        | yellow/yellow           | 4
| 32        | white/green             | 5

(I added also the wirecolors in the table above so that you can also double check with the pictures).

Once you got the connection done and RPi turned on, you can SSH into it and the issue the following command to program your board:

```
sudo avrdude -p atmega32u4 -C ~/avrdude_gpio.conf -c pi_1 -v -U flash:w:ploopyco_trackball_nano_rev1_001_default_production.hex:i -U lfuse:w:0x5E:m -U hfuse:w:0x99:m -U efuse:w:0xC3:m -U lock:w:0x3F:m
```

---
**WARNING**

Of course care should be taken also when powering down:

- turn off the RPi first
- disconnect all power cables from it
- disconnect Ploopy

---

## What are those `-U lfuse:w:0x5E:m -U hfuse:w:0x99:m -U efuse:w:0xC3:m` used for programming the MCU?

The ATMEGA32U4 has some internal fuses that are used to set some configuration in a non-volatile way (i.e. not lost by resetting the board). These configurations include the crystal oscillator and by default the microcontroller is set to use the internal RC one (check the datasheet for this):

- L-fuse = 0x52
- H-fuse = 0x99
- E-fuse = 0xFB

Internal RC oscillator is **not** OK for use with USB since it is not stable enough for that purpose and moreover the FW expects something at higher speed. That's why we set:

- L-fuse = 0x5E
- H-fuse = 0x99
- E-fuse = 0xC3

Of course these changes were also suggested in the Ploopy project's wiki:
<https://github.com/ploopyco/classic-trackball/wiki/Appendix-D%3A-Programming-QMK-on-Older-Ploopy-Devices#flash-the-bootloader>

and elsewhere, like:
<https://www.reddit.com/r/olkb/comments/8sxgzb/replace_pro_micro_bootloader_with_qmk_dfu/>)
