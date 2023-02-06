# ReSeed
ReSeed is an expansion card for the Commodore 16 and Plus/4 computers that allows interfacing the computer with a [MOS 6581 or 8580 Sound Interface Device](https://en.wikipedia.org/wiki/MOS_Technology_6581), the chip that is used to deliver audio on the Commodore 64.

It is based on [a design by Solder of Synergy](https://plus4world.powweb.com/hardware/Solders_SID_Card).

![Board](https://raw.githubusercontent.com/SukkoPera/ReSeed/master/img/render-top.png)

## Summary
Sounds from the C64 now for the Plus/4! Just plug the card into the Expansion Port! You can adjust the volume on the card. You can hear the sound over the Plus/4 audio or take it over a 3.5mm-Stereo-Cinch to a mixer or HiFi-Device. A 9-pin D-SUB connector allows connecting a 3<sup>rd</sup> (analog!) joystick or a proportional-mouse or (why not?) paddles!

*(Slight rewording of Solder's original description of the card.)*

## Design
ReSeed is derived from Solder's SIDcard, with a few enhancements.

### Modifications Introduced in V2
The focus for V2 was on reducing the output noise.

* New component values for the step-up voltage regulator: The values of some components were fine-tuned (again) in order to place the switching frequency of the step-up voltage converter way out of the audio band. These new values can also be retrofitted on a V1 board and will improve things dramatically (See [V1.1](https://github.com/SukkoPera/ReSeed/releases/tag/v1.1)).
* Alternative voltage step-up module: Cheap step-up voltage boost modules can now be used instead of the TL497-based circuit. Such modules, which can be easily sourced from Chinese portals, are much more modern and use a higher switching frequency, resulting in (near-)zero switching-induced noise. Using them also speeds up the assembly process, as many components can be skipped.
* Linear Regulator: If you are really paranoid about switching-induced noise, you can use the above alternatives as a first-stage voltage regulator and then make a second stage through a linear regulator, which should "eat" any remaining noise and provide the SID with very stable power.
* Separate grounds: A separate audio ground plane was introduced and connected to the signal ground plane through a single ferrite bead, preventing noise from spreading from the latter to the former. A second ferrite bead was inserted on the incoming +5V power rail in order to make it cleaner.
* Alternative BJT footprint: An alternative footprint for the output amplifier transistor was added, allowing for the mounting of the 2SC1815 BJT used in the original C64 audio circuit.
* SID clock delay circuit: This allows use of CD74HCT4520B chips in place of the CD4520B.
* Clock divider reset: Makes sure the SID clock always has the correct phase upon startup/reset.
* Short-circuit protection: In the original Solder design, a short-circuit can happen on the joystick port if it is written to. Series resistors were added in order to avoid this situation.

### Modifications Introduced in [V1](https://github.com/SukkoPera/ReSeed/releases/tag/v1)
* Supports both MOS 6581 and 8580 SIDs: this depends on mounting different components though, the board **cannot** be switched "on-the-fly", see [below](#Assembly).
* A volume control for the DigiFix was added and even a switch to completely bypass it.
* The output switch was removed: audio is automatically redirected to the Plus/4's EXT_AUDIO when nothing is connected to the output jack.
* The second stage of the amplifier was removed and the first stage modified so that it uses the same circuit and component values as the C64: the original card was known to introduce some distortion to the output signal as it was trying to amplify it, these days you can easily do better amplification outboard, so we chose to preserve sound fidelity.
* A decoupling capacitor was added close to every IC.
* The values for the step-up converter were recalculated in order to make the generated voltage less noisy and as ripple-free as possible.
* Last but not least, the design was documented and made open.

## Assembly
The provided BOM is suitable for the 8580 SID, but the board can also be used with a 6581 by changing the values of a few components.

### MOS 6581
* C1/C2 = 470pF
* C3/C4 = 1.8nF
* R3 = 1k
* R9 = 11k 1%: **CAREFUL!**
* SW1/RV2/R6 = Do not mount

### MOS 8580
* C1/C2 = 22nF
* C3/C4 = 2.2nF
* R3 = Do not mount
* R6 = 180k
* R9 = 7.87k 1%: **CAREFUL!**
* SW1 = Mount normally
* RV2 = 1M

**Be VERY CAREFUL with R9**: it is this component that decides whether the auxiliary supply voltage of the SID will be 9V or 12V. The 6581 requires 12V, but **if you provide an 8580 with that you are likely to destroy it**. In fact, the 8580 requires 9V, if you provide a 6581 with that it will probably output distorted sound or no sound at all, but SHOULDN'T get damaged. In any case, **I recommend measuring the voltage between pins 14 and 28 of the SID socket** before you actually plug your precious SID into the board. **Consider yourself warned**.

Also note that CN3 shorts AUDIO_IN to GND in order to avoid noise, when no input jack is inserted. If you don't like that, cut JP1 open. I guess this is a good idea (= do it!) if you are using a DigiBlaster add-on.

### Integrated Circuits
You should be careful when choosing some of the ICs to be installed on the card:
* U2 is a binary counter IC, **it MUST be of the "simple CMOS" type**, NOT a high-speed variant, i.e.: a [CD4520B](https://www.ti.com/product/CD4520B) is fine, a [CD74HCT4520](https://www.ti.com/product/CD74HCT4520) is NOT, for instance. If you can, look at the [pictures of the SIDcards originally manufactured by Solder](doc/) and try to get one of those exact chips.
* U4 is a GAL that is used to generate the SID *Chip Select* signal: this one should be as fast as possible (10-15 ns). I have used an [ATF16V8B-15PU](https://www.microchip.com/en-us/product/ATF16V8B) with good results. Be careful with second-hand pulls from China: they generally work but they are very often relabeled parts, so you might have a -15 chip that is actually a -25...

The above requirements are dictated by the simplistic design of the card, please see [this forum thread](https://plus4world.powweb.com/forum/45294) for more information.

### SID Replacements
Most SID replacements will not work correctly. This is because for technical reasons the clock that is provided to the SID has a lower frequency than what it gets on the C64 (~886 vs ~985 kHz). Most SID replacements do not use this clock signal at all and just assume C64 frequency (I guess), leading to incorrect data transfers. This is the case for the SwinSID firmware, for instance.

## Games & Software
You can get images of the disks Solder used to ship with his SIDcard [here](https://plus4world.powweb.com/software/Synergy_Sidcard_Software). These will help making sure that the card works as intended.

[Here is also a list](https://plus4world.powweb.com/effects/SID_Support) of a lot of C16/+4 games and software supporting the card.


## Releases
If you want to get this board produced, you are recommended to get [the latest release](https://github.com/SukkoPera/ReSeed/releases) rather than the current git version, as the latter might be under development and is not guaranteed to be working.

Every release is accompanied by its Bill Of Materials (BOM) file and any relevant notes about it, which you are recommended to read carefully.

## License
The ReSeed documentation, including the design itself, is copyright &copy; SukkoPera 2022 and is licensed under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).

This documentation is distributed *as is* and WITHOUT ANY EXPRESS OR IMPLIED WARRANTIES whatsoever with respect to its functionality, operability or use, including, without limitation, any implied warranties OF MERCHANTABILITY, SATISFACTORY QUALITY, FITNESS FOR A PARTICULAR PURPOSE or infringement. We expressly disclaim any liability whatsoever for any direct, indirect, consequential, incidental or special damages, including, without limitation, lost revenues, lost profits, losses resulting from business interruption or loss of data, regardless of the form of action or legal theory under which the liability may be asserted, even if advised of the possibility or likelihood of such damages.

## Support the Project
If you want to get some boards manufactured, you can get them from PCBWay through this link:

[![PCB from PCBWay](https://www.pcbway.com/project/img/images/frompcbway.png)](https://www.pcbway.com/project/shareproject/ReSeed_SIDcard_for_the_Commodore_16_Plus_4_c4967b6f.html)

You get my gratitude and cheap, professionally-made and good quality PCBs, I get some credit that will help with this and [other projects](https://www.pcbway.com/project/member/shareproject/?bmbid=41100). You won't even have to worry about the various PCB options, it's all pre-configured for you!

Also, if you still have to register, [you can use this link](https://www.pcbway.com/setinvite.aspx?inviteid=41100) to get some bonus initial credit (and yield me some more).

You can also buy me a coffee if you want:

<a href='https://ko-fi.com/L3L0U18L' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://az743702.vo.msecnd.net/cdn/kofi2.png?v=2' border='0' alt='Buy Me a Coffee at ko-fi.com' /></a>

## Thanks
- Solder, for designing the original card and sharing its schematics.
- [Kinmami](https://github.com/kinmami) for his invaluable support
- Luca, Chronos and all the folks at [Plus/4 World](https://plus4world.powweb.com/forum) for helping with the troubleshooting
- [thireo](https://github.com/thireo) for the [3D model of the headphone jack](https://github.com/thireo/kicad-library)
