<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

Language of Light turns text into flashes of light that a phone camera or webcam can read back, a tiny one-way optical data link.

Each character is sent on the LIGHT output (`uo[0]`) as a "light frame", one symbol per bit, least significant bit first:

| Symbol | 0     | 1–8         | 9            | 10–11      | 12   |
| ------ | ----- | ----------- | ------------ | ---------- | ---- |
| LED    | on    | data bits   | even parity  | off (stop) | idle |

Text comes from one of four sources, selected with `ui[1:0]`:

| MODE | Name  | What is sent                                                                                       |
| ---- | ----- | -------------------------------------------------------------------------------------------------- |
| 0    | DEMO  | The built-in message `Hey, it's Pranay ` on repeat                                                |
| 1    | LIVE  | Characters typed on a PC, streamed over UART; each is echoed back once flashed (flow control)       |
| 2    | LOOP  | Up to 12 characters typed over UART, repeated forever. A newline (`\n`) starts a new message        |
| 3    | CAL   | `U` (0x55, alternating bits) forever, to help a receiver find the symbol rate                      |

The symbol rate is set by `ui[6:4]` (with a 10 MHz clock):

| SPEED | Symbol time | Good for                     |
| ----- | ----------- | ---------------------------- |
| 0     | 105 ms      | Phone camera at 30 fps       |
| 1     | 52 ms       | Steady 30–60 fps camera      |
| 2     | 26 ms       | 60 fps+ camera               |
| 3     | 13 ms       | High-speed camera            |
| 4     | 6.6 ms      | Photodiode receiver          |
| 5     | 1.6 ms      | Photodiode / IR receiver     |
| 6     | 410 µs      | Photodiode / IR receiver     |
| 7     | 102 µs      | Photodiode (≈9.8 kbit/s)     |

Extra outputs make receiving easier:

- **SYMBOL_CLOCK** (`uo[1]`) toggles at every symbol, so a two-LED receiver needs no timing knowledge.
- **BUSY** (`uo[2]`) is high during a frame; **FIRST_CHAR** (`uo[3]`) marks the first character of the message.
- **IR_38KHZ** (`uo[5]`) is the light signal modulated onto a ~37.9 kHz carrier, suitable for driving an IR LED (through a transistor) towards a TSOP-style IR receiver. Use SPEED 5 or 6 so each burst is long enough for the receiver.
- **WIDE mode** (`ui[2]` = 1) shows each whole byte at once on `uio[7:0]` for one symbol, followed by one dark symbol: 8 LEDs, about 6× faster.
- **INVERT** (`ui[7]`) flips the polarity of LIGHT and the wide outputs, for active-low LEDs.
- **UART_TX** (`uo[4]`) echoes every character after its light frame finishes, in all modes, at 115200 baud 8N1.

## How to test

1. Select the project on the demo board and set the clock to **10 MHz**.
2. With all inputs low except `ui[3]` (UART RX idles high), the chip is in DEMO mode at the slowest speed. Segment A of the 7-segment display (LIGHT) flashes the built-in message; the decimal point is a heartbeat.
3. Point a webcam at the LED and run `host/decode_camera.py` from the project repository (`pip install opencv-python numpy`). Drag a box around the LED; the decoded text appears in the terminal.
4. For live text, copy `host/lol_board.py` to the demo board and in the MicroPython REPL:

```python
from lol_board import *
start()
mode(LIVE, speed=0)
send("Hello")
mode(LOOP)
store("SOS")
```

The demo board bridges its USB serial to the chip's UART on `ui[3]` (RX) and `uo[4]` (TX), the standard Tiny Tapeout UART pins.

## External hardware

- None required: the demo board's 7-segment display shows LIGHT on segment A.
- Optional: a bright LED (with resistor) on `uo[0]` for longer range; 8 LEDs on the bidirectional Pmod for WIDE mode; an IR LED driven through a small NPN transistor on `uo[5]`.
- A webcam or phone camera, and a PC running `host/decode_camera.py`.
