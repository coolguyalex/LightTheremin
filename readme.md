# Light Theremin

A USB-powered, light-controlled instrument built on a cheap Arduino Nano clone. A photoresistor (LDR) maps ambient light to pitch — wave a hand over it, use a flashlight, or play it in a dark room — while a small set of buttons handle octave, scale, calibration, and quantization.

Unlike a traditional continuous-pitch theremin, this instrument borrows the "fixed notch" idea from handpans and hang drums: pitch can be quantized to a pre-defined scale so it's easier to play something that sounds intentional, while still allowing full continuous pitch when quantization is switched off.

> **Status:** hardware design finalized on paper, firmware in development. Currently built on bare stripboard — no enclosure yet.

## Features

- **Light-to-pitch mapping** — logarithmic (not linear), so pitch steps feel even across the light range rather than bunched up at one end
- **Quantization toggle** — snap to notes in the current scale, or play fully continuous pitch
- **Key/mode selector** — cycles through a set of pre-defined scales
- **Octave shift**
- **Calibration mode** — sets the LDR's usable min/max brightness for the room you're in (feedback via beep: single high beep = show it your brightest light, double beep = show it your darkest)
- **ADSR envelope** via [Mozzi](https://github.com/sensorium/Mozzi) — real attack/decay/sustain/release shaping, not just on/off square-wave beeping
- **Dual audio output** — onboard piezo buzzer plus a 3.5mm audio jack (line out), fed from the same audio pin
- **USB powered** — no battery

## Hardware

| Part | Notes |
|---|---|
| Arduino Nano (clone, CH340) | Main controller |
| LDR (photoresistor) | Wired as a voltage divider |
| Fixed resistor (~10kΩ) | Divider partner for the LDR |
| Piezo buzzer (passive) | Primary audio output |
| 3.5mm audio jack | Line out; DC-blocking cap in series off the same audio pin as the buzzer |
| 5x momentary buttons | Play, octave, key/mode, calibration, quantization toggle |
| Stripboard | No enclosure currently |
| USB cable | Power only |

### Suggested pinout

| Function | Pin |
|---|---|
| LDR (analog in) | A0 |
| Play button | D2 |
| Octave button | D3 |
| Key / mode button | D4 |
| Calibration button | D5 |
| Quantization toggle | D6 |
| Audio out (Mozzi) | D9 |
| Status LED | D13 (onboard) |

> Mozzi reserves pins 9 and 10 for audio output (Timer1) in STANDARD/STANDARD_PLUS mode — avoid using them for anything else.

## Software

- [Arduino IDE](https://www.arduino.cc/en/software)
- [Mozzi](https://github.com/sensorium/Mozzi) — audio synthesis library (oscillators, ADSR envelopes, non-blocking analog reads)

## How it works

### Calibration

Pressing the calibration button puts the device in calibration mode. The device beeps once (high pitch) to prompt exposing the LDR to the brightest available light, then beeps twice to prompt the darkest. The resulting min/max readings are used to scale all subsequent light readings into a usable pitch range. Calibration values are intended to be saved to EEPROM so they survive a power cycle (the device is USB-powered with no battery backup, so nothing persists in RAM alone).

### Scales & quantization

Each key/mode is a fixed array of allowed frequencies (or MIDI note numbers converted via `440 * 2^(n/12)`). The key/mode button cycles through the available arrays. When quantization is on, the raw light-to-pitch value snaps to the nearest entry in the current array. When off, pitch is continuous across the calibrated range.

### Controls

| Button | Function |
|---|---|
| Play | Produces sound (shaped by the ADSR envelope) while held |
| Octave | Shifts the current scale up/down by an octave |
| Key / mode | Cycles through pre-defined scales |
| Calibration | Enters calibration mode |
| Quantization | Toggles snapping to scale notes vs. continuous pitch |

## Roadmap

- [ ] Firmware: calibration + EEPROM persistence
- [ ] Firmware: scale/quantization arrays
- [ ] Firmware: ADSR envelope tuning via Mozzi
- [ ] Enclosure design
- [ ] Assembly/build guide with wiring diagram

## License

MIT — see [LICENSE](LICENSE) (or replace with your preferred license).
