# Ubiquiti US-48 Board Photos

This repository contains a collection of board and component level
photos taken during the repair of a Unifi US-48 switch. No schematics
are available for the board or the BCM53344 processor, so I am posting
these photographs in the hope that they are helpful to others.

## Symptoms

The switch was purchased in an untested condition at a steep discount.
The seller reported that the switch would power on, but was otherwise
untested. Upon receiving the switch, the following was observed

1. Both fans would spin once connected to power, never stopping or
	 altering speed.
2. The blue status LED on the left side of the switch never
	 illuminated.
3. All 48 port LEDs briefly illuminated (as if part of a self test)
4. The switch was unresponsive and remained in this state until power
	 was removed.

## Diagnosis

The switch was disassembled, revealing a 4 pin UART connector on the
main board. This was attached to a separate PCB that provided console
access via the external RJ-45 connector. A RJ-45 to USB serial cable
was not available, so a console connection was made using a UART to USB serial
connector attached directly to the main board.

The console connection revealed a DDR memory test failure at boot
time. Several shmoo plots were generated as part of the self test
diagnostics. The shmoo plots seemed to indicate a complete failure of
the DDR memory module, and the total memory amount was incorrectly
reported at 4 GiB (as opposed to the expected 256 MiB).

The root problem was eventually traced to [two damaged pads](./lifted_pads.jpg)
under the CPU: `DML` and `DQU6` as identified in the memory IC
data sheet. Probing of the memory module's pads prior to
removing the CPU revealed that these connections were floating. As
such, it is improbable that the pads were lifted during CPU removal
unless their integrity was already compromised.

# Repair
Ultimately the switch was repaired using a replacement
DDR3 memory module and CPU. Reballing the CPU using direct heat
stencils presented problems with warping that ultimately proved too
difficult to resolve.

The lifted pads were repaired by scraping away solder mask to expose
enough of the traces such that a solder ball could flow onto the
exposed copper.


## Memory
The switch uses a `K4B2G1646Q-BCK0` 2 Gbit DDR3 memory module, for which
data sheets are available online.

## CPU
The switch uses two `BCM53344` series ARM processors. No data sheets
for these processors could be found online. Some reverse engineering
was possible by matching known pads of the memory module to pads of
the CPU.
