# rc-z80ram
 CPU+RAM+Clock+Reset for RC2014

## Overview
This RC2014-compatible card provides a Z80 CPU, pageable RAM (fixed at the full 64K memory space, but disableable by the PAGE signal), and CLK and CLK2 signals. Reset supervision is provided.

## CPU
CPU is a regular Z80, with all signals brought out to the RC2014 Enhanced bus. Clock is always connected to the bus (and optionally generated locally - see below).

## Memory
RAM is provided by a single AS6C1008 128k x 8 SRAM chip. The chip may be optionally disabled (with JP1), and it may be configured to follow the bus PAGE signal (with JP2). This allows it to be paged out by compatible ROM cards or the BusRaider (currently untested). Only half of the chip is used; this part was picked because it is cheaper than 2x 62256 chips, and the extra chip select allows implementation of PAGEing without extra logic. Note that unlike the official RC2014 64K RAM card, PAGE disables *all* memory, not just the lower 32K (so this isn't compatible with the official RC2014 ROM card).

### Why flat 64K?
This RAM on this card is primarily intended to be used with J.B. Langston's z80ctrl board and Rob Dobson's BusRaider board - it's really nice to use these with the freedom of having memory anywhere in the Z80 address space. To use this card with other memory systems (like the official RC2014 RAM/ROM cards or the 512K ROM 512K RAM card), disable the RAM, since it can't bring any benefit to them.

## Clock
A fixed 7.3728MHz clock can be provided by the board to CLK (and the Z80), selectable with JP3. This oscillator can further be divided and provided to CLK2, using JP4 and JP5:

| Divisor | Clock     | Baud   |
| ------- | --------- | ------ |
|      /1 | 7.3728MHz | 115200 |
|      /2 | 3.6864MHz |  57600 |
|      /3 | 2.4576MHz |  38400 |
|      /4 | 1.8432MHz |  28800 |
|      /6 | 1.2288MHz |  19200 |
|      /8 | 0.9216MHz |  14400 |

The clocks only have a 50% duty cycle for /1 and /2 divisors - the others vary by divisor (so /3 is 33% duty, etc). This is tolerated by the Z80 SIO chip (which is the only device this board is intended to clock).

## Reset
No reset button is provided, but the included DS1233 reset supervisor will clean up reset pulses from other buttons and enforce a reset pulse during power-on. This is intended to be used with backplanes that provide a reset button and pullup resistor (which is most of them).

## Jumper settings
Refer to the back of the board for detailed jumper setting instructions.

## TODO
BOM, component references