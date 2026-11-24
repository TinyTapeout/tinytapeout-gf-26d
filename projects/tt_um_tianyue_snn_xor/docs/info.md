<!---

This file is used to generate your project datasheet. Please fill in the information below and delete any unused
sections.

You can also include images in this folder and reference them in the markdown. Each image must be less than
512 kb in size, and the combined size of all images must be less than 1 MB.
-->

## How it works

This project implements a **2 → 8 → 1 Spiking Neural Network (SNN)** that solves the non-linear **XOR** problem using a **0-DSP (multiplier-free)** Leaky Integrate-and-Fire (LIF) neuron architecture.

- **Leak Decay (`Beta = 0.5`)**: Implemented purely with an arithmetic right shift (`mem >>> 1`), avoiding multipliers/DSP blocks and mapping cleanly to SKY130 standard cells.
- **Synaptic Integration**: Since input and hidden activations are binary spikes (`0` or `1`), weighted accumulation simplifies to conditional additions (`MUX-Adder`) with pre-trained signed integer weights (`SCALE = 64`, `THRESHOLD = 64`).
- **Hard Reset**: When a neuron's membrane potential reaches or exceeds the threshold (`64`), it emits a 1-cycle spike and resets its membrane potential to `0` on the next clock edge.

## How to test

1. Apply reset (`rst_n = 0` for a few clock cycles, then `rst_n = 1`).
2. Drive the two binary inputs on `ui_in[1:0]`:
   - `2'b00` (`[0, 0]`): `uo_out[0]` remains `0` (no output spikes).
   - `2'b01` (`[0, 1]`): `uo_out[0]` fires periodic spikes.
   - `2'b10` (`[1, 0]`): `uo_out[0]` fires continuous spikes.
   - `2'b11` (`[1, 1]`): `uo_out[0]` remains `0` (no output spikes).
3. Monitor `uo_out[0]` for the XOR output spike train and `uio_out[7:0]` for the 8 hidden-layer LIF neuron spikes.

## External hardware

None required (optional switches on `ui_in[1:0]` and LEDs on `uo_out[0]` / `uio_out[7:0]`).
