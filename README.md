# Piezo Preamp v2 with Baxandall Tone Control and Battery Indicator

[Newer version (v3) available here](https://github.com/arxwtf/2024_PiezoPreamp3/).

A simple preamp to be used for e.g. guitar piezo (contact) microphones. 

Created in KiCAD v7 and LTSpice.

![Rendered PCB](render.png?raw=true "Rendered PCB")


## Main circuit blocks

### Charge amplifier for piezo 

Basically an integrator circuit. Provides low-impedance input. The gain is adjustable and depends on the input piezo capacitance. In my measurements, piezo discs have following capacitances:

- small disc: ~10nF
- medium disc: ~20nF
- large disc: ~40nF

But I've also seen mentions of piezo discs with <10nF capacitance. Connecting multiple piezo discs in parallel increases the capacitance, so the maximum piezo capacitance can be in order of ~100nF or so.

The gain of the charge amp is then set by the ration of piezo capacitance to charge amp feedback capacitance. The **gain slide switch** is used to select from these feedback capacitances:

- 1nF
- 5.7nF
- 11nF
- 23nF

Input is protected by ESD bidirectional diode.

OPA145 is the opamp of choice because of these characteristics:
- JFET input stage, which means low input bias and offset current - critical for a charge amplifier
- low quiescent current (0.7mA max) for battery operation
- acceptable input voltage noise density (7nV/rtHz)
- good current noise density (0.8fA/rtHz)
- sufficient slew rate for audio (20V/us)
- sufficient GBW (5.5MHz)
- single supply voltage range 4.5V - 36V
- output voltage swing (V- +0.3, V+ -0.3)

Opamp also unfortunately seems to have bias current cancellation, which might introduce more current noise - this has to be determined by measurement. The same opamp is also used for Baxandall tone control section. Two single OPA145 components are used instead of dual OPA2145 package because it's easier to route, minimizes crosstalk and improves power dissipation (less heat). This increases the price, of course.

### High-pass filter

The charge amp is followed by a simple first-order high-pass filter with switchable cutoff frequency:
- 0Hz 
- 75Hz
- 120Hz
- 235Hz

The cutoff frequency is selectable by **LPF slide switch**.

### Baxandall tone control

Bass and treble can be adjusted independently by **Bass pot** and **Treble pot** in the ranges of +-10dB. The center frequency is switchable by **Center slide switch**:

- 350Hz
- 600Hz
- 780Hz
- 1kHz

Output audio is routed through **Volume pot** and protected by ESD bidirectional diode.

### Power-on LED and battery indicator
The power-on LED can work in two modes, switchable by on-board jumper header
- always on (jumper is off)
- "battery indication mode" (jumper is on)

**Battery indication mode (BI)** causes the LED to flash briefly when the circuit is switched on (by inserting mono plug to output jack). The longer the flash, the lower the battery voltage (remaining capacity). Under certain threshold, the LED will stay on permanentnly, signalling the need for replacement. This **BI threshold** is adjustable by on-board trimmer pot and needs to be determined by voltmeter measurement.

The **power-on LED intensity** is adjustable by an on-board trimmer pot as well.

### Power supply
Designed for 9V batteries or DC pedal power supplies, but should theoretically be able to work properly with up to 18V DC.

Power enters the PCB by means of Molex PicoBlade connector on board.

Contains ESD protection (bidirectional diode), MOSFET reverse polarity protection (with Zener gate protection and CLR) and input pi filter.

## Simulation: /ltspice folder
LTSpice simulations (transient, AC, noise, stability) and some graphs are available in `ltspice/` folder.

## /library folder
Folder containing all 3rd symbols, footprints and 3D models used in the project. Other than that, only standard KiCAD symbols are used (opamps, ).

## /resources folder
Contains some additional resources, inspiration and schematics used during development.

## /schematics folder
Contains schematic sheets plotted from KiCAD, individually as .png and in one file as .pdf.

## Known problems
- the slide switches are quite small! They might be hard to operate, especially when the PCB is inside an enclosure.
- the mounting holes are too close to the edges of the PCB. Mounting pegs' bases might need to be trimmed, depending on the enclosure - or a different mounting accessories need to be used.
- fixed in v2.3: ~the enclosure I chose (TEKO 1/A.1) has a inner width (34mm) that seems to be about 1mm shorter than ideal. This will create PCB mounting problems. Future revisions of PCB should address this, or a different enclosure must be used (e.g. TEKO 2/A.1)~
- battery spring (used only for grounding) is too close to the PCB edge. This creates unnecessary force between the PCB and the enclosure wall. The spring can be shortened, or not used at all.
