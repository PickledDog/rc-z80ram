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

## Standard vs Enhanced bus
While this card can be used on a Standard bus backplane (like the RC2014 Backplane-5 or Backplane-8), the RAM is basically unusable (since there's no way to page it out or fill it through bus control). If you intend to use it this way, omit the RAM chip, 4017, and all jumpers except JP3. If you're looking for an Enhanced backplane, I recommend Stephen C Cousins' [SC112](https://smallcomputercentral.wordpress.com/sc112-modular-backplane-rc2014/) (optionally with [SC113](https://smallcomputercentral.wordpress.com/sc113-modular-backplane-rc2014/)).

## Part selection
Bill Of Materials and part references are below. In order to make the 50-pin RC2014 Enhanced bus connector, you need to take a 2x40-pin right-angle header and pull out most of the pins from the top row (needle-nose pliers work well). I recommend using gold-plated header for this - I use these ones from [Pololu](https://www.pololu.com/product/2668) or [Sparkfun](https://www.sparkfun.com/products/12792). The jumper headers can be snapped from regular breakaway header, for which eBay is substantially cheaper; the Mouser listings are provided for convenience.

The specified parts are just the ones I used, and can be substituted as needed - Mouser links provided for convenience and reference. If you wish to socket the oscillator, I strongly recommend using a turned-pin oscillator socket, like [this one](https://www.mouser.com/ProductDetail/535-1108800). Not only do these already have the unused pins removed, they grip the oscillator much better (the limited friction of only 4 pins makes them tend to fall out of standard sockets).

| Reference | Value | Qty | Mouser link |
| --------- | ----- | --- | ----------- |
| C1-C4 | 100nF ceramic | 4 | [KEMET C315C104M5U5TA](https://www.mouser.com/ProductDetail/C315C104M5U5TA7303) |
| C5 | 1nf ceramic | 1 | [KEMET C315C102K1R5TA](https://www.mouser.com/ProductDetail/C315C102K1R5TA) |
| J1/2 | 2x40 right-angle header | 1 | [3M 2380-5121TG](https://www.mouser.com/ProductDetail/2380-5121TG) |
| JP1/2 | 2x3 header | 1 | [Amphenol 10129381-906002BLF](https://www.mouser.com/ProductDetail/10129381-906002BLF) |
| JP3 | 1x2 header | 1 | [Amphenol 10129378-902002BLF](https://www.mouser.com/ProductDetail/10129378-902002BLF) |
| JP4 | 1x3 header | 1 | [Amphenol 10129378-903002BLF](https://www.mouser.com/ProductDetail/10129378-903002BLF) |
| JP5 | 2x5 header | 1 | [Amphenol 10129381-910002BLF](https://www.mouser.com/ProductDetail/10129381-910002BLF) |
| | jumpers | 5 | [Harwin M7583-46](https://www.mouser.com/ProductDetail/M7583-46)
| RN1 | Bussed 10kΩ×4 resistor | 1 | [Bourns 4605X-AP1-103LF](https://www.mouser.com/ProductDetail/4605X-AP1-103LF) |
| U1 | 8MHz Z80 | 1 | [Zilog Z84C0008PEG](https://www.mouser.com/ProductDetail/Z84C0008PEG) |
| | socket | 1 | [Amphenol DILB40P-223TLF](https://www.mouser.com/ProductDetail/DILB40P-223TLF) |
| U2 | AS6C1008 SRAM | 1 | [Alliance AS6C1008-55PCN](https://www.mouser.com/ProductDetail/AS6C1008-55PCN) |
| | socket | 1 | [Amphenol DILB32P-223TLF](https://www.mouser.com/ProductDetail/DILB32P-223TLF) |
| U3 | DS1233-5 | 1 | [Dallas/Maxim DS1233-5+](https://www.mouser.com/ProductDetail/700-DS1233-5) |
| U4 | CD74HC4017 | 1 | [TI CD74HC4017E](https://www.mouser.com/ProductDetail/595-CD74HC4017E) |
| X1 | 7.3728MHz oscillator | 1 | [CTS MXO45HS-3C-7M3728](https://www.mouser.com/ProductDetail/MXO45HS-3C-7M3728) |