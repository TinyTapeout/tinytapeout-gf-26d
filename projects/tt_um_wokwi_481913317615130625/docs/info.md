<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

This is a purely combinational 7-segment display driver built from OR gates. Each of the eight inputs selects one
digit: driving input `ui[N-1]` high lights the segments needed to display the digit N (1 to 8) on the display.

The outputs drive the segments directly and are active high (for a common-cathode display):

| Output | Segment |
| ------ | ------- |
| uo[0]  | a       |
| uo[1]  | b       |
| uo[2]  | c       |
| uo[3]  | d       |
| uo[4]  | e       |
| uo[5]  | f       |
| uo[6]  | g       |
| uo[7]  | unused (always 0, decimal point off) |

| Input high | Segments lit  | Digit shown |
| ---------- | ------------- | ----------- |
| ui[0]      | b c           | 1           |
| ui[1]      | a b d e g     | 2           |
| ui[2]      | a b c d g     | 3           |
| ui[3]      | b c f g       | 4           |
| ui[4]      | a c d f g     | 5           |
| ui[5]      | a c d e f g   | 6           |
| ui[6]      | a b c         | 7           |
| ui[7]      | a b c d e f g | 8           |

If more than one input is high at once, the display shows every segment used by any of the selected digits. With
all inputs low, the display is blank. The design does not use the clock or reset.

## How to test

Connect a common-cathode 7-segment display to `uo[0]`–`uo[6]` (segments a–g), or use the 7-segment display on
the Tiny Tapeout demo board. Set exactly one of the input switches `ui[0]`–`ui[7]` high and check that the display
shows the matching digit, 1 to 8. With all switches off, the display should be blank.

## External hardware

A common-cathode 7-segment display (such as the one on the Tiny Tapeout demo board) and eight switches or buttons on
the inputs.
