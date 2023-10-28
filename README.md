# &mu;WWVB: a tiny wwvb station

&mu;WWVB is a low power 60 khz [WWVB] transmitter used
to automatically set the time on radio synchronized clocks and wristwatches where the regular [WWVB] signal isn’t available. The system acquires
the correct time via GPS.

![alt text](images/schematic.png)

![alt text](images/board.png)

## Anish Athalye's project

I started with this 2016 github project.

-  His github project https://github.com/anishathalye/micro-wwvb.git 
-  His [blog post][micro-wwvb-post].

Thanks to Anish for making it available. 

## My situation

I live in Forest Grove, Oregon.  The opposite coast where Anish had his problem. I'm not able to reliably receive [WWVB], and especially not inside my house.  

I started searching for information on WWVB signals and ran 
across Anish's project.  It was perfect for what I wanted to do.

Anish seemed to have access to a lot of equipment for building the physical part of the stand.    I don't have access so I'm just going to be using a simple plastic box as a case.

I haven't made my own PC board since 1974.  A lot of taping was involved and then an etching bath.  I was pleased to discover open source schematic and printed circuit board editors.  I was
also pleased to discover online services which would accept [gerber files](https://en.wikipedia.org/wiki/Gerber_format) and mail you a finished PCB.

## KiCad

For my open source schematic/PCB editor I chose [KiCad].  I had never used it before.  So there was a bit of a learning curve.

### autorouting
Install the Freerouting plugin.
1. Open KiCad Project Manager
1. Click on Plugin and Content Manager
1. Fine Freerouting
1. Click on install
1. Click on Apply Pending Changes.
1. You will have to have Java installed for it to work.


## PCB fabrication service

I used [Osh Park](https://oshpark.com) as my fabrication service.  They accept KiCad pcb files directly.  No need to export to gerber files.  The are the cheapest service I could fine.  Interestingly, they just happen to be local to me.

[WWVB]: https://www.nist.gov/pml/time-and-frequency-division/radio-stations/wwvb
[micro-wwvb-post]: https://www.anishathalye.com/2016/12/26/micro-wwvb/

[KiCad]: https://www.kicad.org/

## ATTiny44a-SSUR

AVR family of processors.  8 bit risc architecture.

14 pin Dual inline package
Surfuce mount device

Just notes an decoding the suffix.

4KiB flash
SS - 14S1 package
U - Matte tin
R - Tape and real
14S1 - 14-lead, 3.90 mm Narrow Body, Plastic Small Outline Package (SOIC)

## Software

The WWVB signal sends one bit of data for one second.  60 seconds are required to send all the bits for the time.
- 0: Power reduced for .2 seconds.
- 1: Power reduced for .5 seconds.
- mark: Power reduced for .8 seconds.

The 60 khz signal is generated using the ATTiny's counter1, and wave form generator.
The pulse width modulation output, PA5 (pin 8), is used to generate the 60khz signal.
The software divides a second into two parts.  The first part is the reduced power signal.  Reduced power in our case means no signal is sent.  The second part is full power signal.  Here a 50% duty cycle signal is sent.

Actually, a 20 khz signal is generated.  We depend on the third harmonic.  Apparently Anish found this more effective.

Timing for how long a signal is sent or not is accomplished using a library routine to delay a certain number of clock cycles.

During the full power signal, the blue led is turned on. The led is connected to PA7 (pin 6).