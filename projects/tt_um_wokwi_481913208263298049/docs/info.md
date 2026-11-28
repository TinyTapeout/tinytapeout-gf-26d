## How it works

This combinational circuit displays the initials **L**, **Y**, and **J** on a
common-cathode seven-segment display. Two digital inputs select the letter;
there is no clock or stored state. The outputs `uo[0]` through `uo[6]` drive
segments A through G, and `uo[7]` is the decimal point (always off).

| `ui[1]` | `ui[2]` | Display |
|:-------:|:-------:|:--------|
| 0 | 0 | L |
| 0 | 1 | Y |
| 1 | 0 | J |
| 1 | 1 | Blank |

The letters are approximations made from the seven available segments. NOT,
AND, and OR gates decode the two input bits into the segment signals.

## How to test

Open the [Wokwi simulation](https://wokwi.com/projects/481913208263298049)
and start it. Set the switches connected to `IN1` and `IN2` to `00`, `01`,
and `10` to see L, Y, and J respectively. Set both to `1` to blank the display.
The same inputs appear as `ui[1]` and `ui[2]` on the Tiny Tapeout board.

## External hardware

The Wokwi simulation includes a common-cathode seven-segment display. No
additional hardware is needed for the online simulation.
