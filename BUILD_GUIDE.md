# Build Guide: Assembling Your Light Theremin

This guide walks through building one Light Theremin from parts to a working instrument. Follow the steps in order — later steps assume earlier ones are done and tested.

See [`wiring-diagram.svg`](wiring-diagram.svg) for the full pin reference. See [`PROJECT_OVERVIEW.md`](PROJECT_OVERVIEW.md) for what this thing does and why it's built this way, or [`README.md`](README.md) for the full technical spec.

## Safety first

- Soldering irons run at 300–370°C (570–700°F) — hot enough to burn instantly on contact. Always return the iron to its stand when you're not actively soldering, never set it down on the bench.
- Work in a ventilated area and avoid breathing solder fumes directly — a small fan pointed away from your face helps.
- Wear safety glasses when cutting or trimming component leads — clipped wire ends fly.
- Let a freshly soldered board cool before touching the joints.
- If you're unsure about a step, ask before powering anything on. A wiring mistake is easy to fix; a fried Nano is not.

## What you'll need

**Tools**
- Soldering iron + stand + solder
- Wire strippers
- Flush/side cutters
- A small breadboard and jumper wires (for testing before you commit to solder)
- A multimeter (helpful, not strictly required)
- Small Phillips screwdriver (if your buttons need mounting)

**Parts per unit**
| Part | Qty | Notes |
|---|---|---|
| Arduino Nano (clone) | 1 | |
| LDR (photoresistor) | 1 | |
| 10kΩ resistor | 1 | pairs with the LDR as a voltage divider |
| Momentary push buttons | 5 | play, octave, key/mode, calibration, quantization |
| Piezo buzzer (passive) | 1 | |
| 3.5mm audio jack | 1 | |
| Small capacitor (1–10µF) | 1 | DC-blocking, for the audio jack |
| Resistor (~1kΩ) + capacitor (~10nF) | 1 each | RC low-pass filter, cleans up the jack output |
| Stripboard | 1 piece | sized to fit your layout |
| Hookup wire | — | |
| USB cable | 1 | power only |

## Step 0: Prototype on a breadboard first

Before soldering anything permanently, build the full circuit on a breadboard and confirm it works. This is standard practice for a reason: it's far easier to fix a wiring mistake by pulling a jumper wire than by desoldering stripboard. Don't skip this step even if you're confident.

## Step 1: Wire the light sensor

Connect the LDR and the 10kΩ resistor in series between 5V and GND, with the midpoint (the junction between the two) going to pin **A0**. This is called a voltage divider — as light hitting the LDR changes its resistance, the voltage at that midpoint changes, and that's what the Nano reads.

## Step 2: Wire the buttons

Each button connects a digital pin to GND when pressed (we'll configure the pin's internal pull-up resistor in software, so you don't need an external resistor here).

| Button | Pin |
|---|---|
| Play | D2 |
| Octave | D3 |
| Key / mode | D4 |
| Calibration | D5 |
| Quantization toggle | D6 |

## Step 3: Wire the audio output

1. Connect the piezo buzzer directly between **D9** and GND.
2. For the audio jack: run a wire from D9 through the small blocking capacitor, then to the jack's tip and ring (tied together, since this is mono), with the jack's sleeve going to GND.
3. Add the RC filter (1kΩ resistor into a 10nF capacitor to ground) between D9 and the jack for a cleaner line-out signal. The buzzer alone doesn't need this — its sound is rough either way, so the extra filtering isn't worth the board space there.

**Do not use pins 9 or 10 for anything else.** They're reserved for audio output.

## Step 4: Confirm the onboard LED

No wiring needed here — the Nano's built-in LED on pin D13 is used for calibration feedback. Just confirm you know which LED on the board that is before moving on.

## Step 5: Wire USB power

Nothing to build here beyond making sure your Nano's USB port is accessible once everything else is soldered down — you'll be plugging and unplugging it during testing and calibration, so don't bury it under other components.

## Step 6: Double-check against the wiring diagram

Before you solder anything to stripboard, lay your breadboard build next to [`wiring-diagram.svg`](wiring-diagram.svg) and check every connection off one at a time. This is the point to catch mistakes — they're much cheaper to fix now.

## Step 7: Solder to stripboard

Transfer the confirmed circuit to stripboard. Work in the same order as the steps above (sensor, then buttons, then audio) so you can test incrementally rather than discovering a problem only after everything is soldered.

## Step 8: Install the firmware

1. Install the [Arduino IDE](https://www.arduino.cc/en/software) if you don't have it.
2. Install the **Mozzi** library (Library Manager → search "Mozzi").
3. Open the class firmware sketch (ask your instructor where this lives in the repo — it isn't finalized yet as of this guide).
4. Under **Tools → Board**, select Arduino Nano. Under **Tools → Processor**, most cheap clones need **ATmega328P (Old Bootloader)** — if upload fails with a timeout, this is almost always why.
5. Select the correct port under **Tools → Port**, then click Upload.

## Step 9: Test every function

Go through each of these in order — don't skip ahead if one fails, since later features often depend on earlier ones working correctly:

1. Power on — onboard LED should light briefly.
2. Press Calibration, follow the beep prompts (high beep = show it bright light, double beep = show it your darkest available light).
3. Hold Play and wave your hand over the LDR — pitch should change smoothly.
4. Press Octave — range should shift up or down.
5. Press Key/mode — the scale should audibly change.
6. Toggle Quantization on — notes should snap to the scale instead of gliding freely.
7. Plug headphones or a speaker into the jack — sound should come through there too.

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| Upload fails / times out | Wrong processor selected — try "ATmega328P (Old Bootloader)" |
| No sound at all | Check D9 connections; confirm the buzzer isn't wired backwards (piezo buzzers are often polarity-sensitive) |
| Sound is present but distorted/crackly | Check the blocking capacitor is actually in the signal path, not just near it |
| Calibration beeps happen but pitch doesn't respond to light | Recheck the LDR/resistor divider wiring at A0 |
| Buttons don't respond, or respond constantly | Confirm pull-up configuration in firmware matches how the button is wired (to GND, not 5V) |
| Device works on breadboard but not after soldering | Look for a cold solder joint or a solder bridge between adjacent stripboard strips |
