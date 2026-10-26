# Prakriti Ayursensora
## Three Modules — Component Lists and How They Connect

The three modules do not plug into each other. Each one plugs into the Raspberry Pi. The Pi is the only shared piece, and it is what makes them run as one session.

```text
Aroma  ----USB--------\
Display --HDMI-------- Raspberry Pi 5 ---- phone (staff)
Music ---USB audio---/
```

Any one module can be switched on and tested by itself. For a real session, all three are connected to the Pi at the same time. There is no cable from the nebulizer to the projector, and no cable from the projector to the speaker.

---

## 1. Aroma

This module has its own small controller. The Pi tells that controller what to do over one USB cable.

| Qty | Component | What it is for |
|---:|---|---|
| 1 | ESP32 board | Receives commands from the Pi. Which GPIO does what is not frozen. |
| 1 | USB data cable | Pi to ESP32. This is the only link between aroma and the rest of the system |
| 3 | Ultrasonic nebulizer modules | One cup each in the concept. Fluid compatibility and the control pin are unresolved. |
| — | MOSFET switches and gate resistors | Not selected. A fourth MOSFET for the fan is not part of the design. |
| 1 | Fan for one shared outlet | Speed wiring is **UNRESOLVED — DO NOT FREEZE WIRING**. No datasheet is in the project. |
| 3 | Float switches | Intended to mark an empty cup. Not yet wired. |
| 1 | Leak sensor and drip tray | Intended to force mist off. Not yet wired. |
| 3 | Cups, tube, and connectors | Shared outlet. Carry-over between channels is not measured. |
| 1 | BME680 | Room temperature, humidity, and a gas signal. Not an oil-dose meter. |
| 1 | 0.96-inch OLED | Optional status text. Pin not assigned. |
| 1 each | Oil for Vata, Pitta, and Kapha | On hold until names and exposure are reviewed. |
| 1 | Separate aroma supply and an e-stop | The supply current is not calculated. The e-stop must cut nebulizer and aerosol-fan power by itself. It is not built. |

---

## 2. Display

No separate controller. The Pi plays the video.

| Qty | Component | What it is for |
|---:|---|---|
| 1 | Projector, or a TV if the projector is too dim in the room | Shows the nature scene |
| 1 | micro-HDMI to HDMI cable | Pi to the projector or TV. This is the only link |
| 1 | The projector’s or TV’s own power adapter | Mains. It does not take power from the Pi or the aroma supply |
| — | Scene video files on the Pi’s memory card | The actual pictures |

An LED strip is optional and is not required for this module to work.

---

## 3. Music

No separate controller. The Pi plays the audio.

| Qty | Component | What it is for |
|---:|---|---|
| 1 | USB audio adapter | The Pi 5 has no headphone socket |
| 1 | 3.5 mm audio cable | Adapter to the speaker |
| 1 | 10 W powered speaker with an AUX input | Plays the raga |
| 1 | The speaker’s own power adapter | Mains or USB. Not the aroma supply |
| — | Raga audio files on the Pi’s memory card | The actual music |

Headphones or a pillow speaker can share that same audio socket when the room should stay quiet.

---

## Shared parts

These are not a fourth module. They are what the three lists plug into.

| Qty | Component | Why it is shared |
|---:|---|---|
| 1 | Raspberry Pi 5 | Runs the session, the screen, and the sound, and sends mist commands |
| 1 | Official 27 W USB-C supply | Powers the Pi only |
| 1 | 128 GB memory card | Holds the system, the database, the videos, and the music |
| 1 | Phone or tablet | The buttons: start, pause, stop, and the log |

Three separate parts lists. **Four** power sources: the Pi supply, the aroma supply, the projector or TV adapter, and the speaker adapter. One computer in the middle joins the three modules. The aroma supply’s current is not calculated, and the aroma wiring is not frozen.
