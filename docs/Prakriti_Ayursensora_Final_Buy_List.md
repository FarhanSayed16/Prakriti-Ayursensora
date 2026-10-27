# Prakriti Ayursensora
## Final component list

Read this file in two passes.

1. **Module by module.** Every part sits under the shared core, aroma, display, or music. That is the full set for the project.
2. **Buy now.** One shopping list, taken only from the rows marked `BUY NOW`.

Connection drawings stay in `Prakriti_Ayursensora_Three_Modules_Component_List.md`.

The three modules do not cable to each other. Each one meets the Raspberry Pi. The phone talks only to the Pi.

```text
Aroma   ----USB serial----\
Display --micro-HDMI------ Raspberry Pi 5 ---- phone
Music ---USB audio--------/
```

| Decision | Meaning |
|---|---|
| `BUY NOW` | On the shopping list at the end of this file. |
| `BENCH-TEST FIRST` | One piece, and only after the condition in the row is true. |
| `WAIT FOR APPROVAL` | Named so it is not forgotten. Do not order. |
| `NOT REQUIRED` | Checked. Leave it out. |
| `OMIT` | Was on the old sheet. Leave it out of this project. |

Nothing here approves the unit for use around people.

---

## Shared core

This is not a fourth module. It is the computer the three modules plug into.

The Pi stores the profile, builds the session, plays picture and sound, and sends mist commands. Its official 27 W supply is power source 1. That supply also feeds the ESP32 chip through the USB data cable. It does not feed a nebulizer.

| Qty | Component | Specification | Used for | Connects to | Decision |
|---:|---|---|---|---|---|
| 1 | Raspberry Pi 5 | 4 GB. Use an 8 GB board only if that board is already on hand. | Session, database, video, audio, commands | 27 W supply, microSD, RTC battery, USB, HDMI | `BUY NOW` |
| 1 | Official Raspberry Pi 27 W USB-C supply | 5 V / 5 A, USB Power Delivery, local mains plug. The USB-C lead is in the box. | Pi power, and ESP32 chip power via the data cable | Wall socket → Pi power port | `BUY NOW` |
| 1 | Raspberry Pi 5 active cooler | Official clip-on cooler | Cooling during playback | Clips onto the Pi | `BUY NOW` |
| 1 | microSD card | 128 GB, high-endurance | OS, database, scene videos, raga audio | Pi microSD slot | `BUY NOW` |
| 1 | USB microSD reader | Only if the laptop has no card slot | Writing the OS image | Laptop USB → card | `BUY NOW` |
| 1 | Official Raspberry Pi RTC battery | Rechargeable ML2020, pre-fitted 2-pin JST plug. A CR2032 is the wrong cell. | Clock through a power cut | Pi 5 RTC connector | `BUY NOW` |
| 1 | Phone or tablet | Already owned. A normal browser. | Start, pause, stop, log | Pi hotspot over Wi-Fi | Already owned |
| 1 | Laptop | Already owned | Flash the microSD card with Raspberry Pi Imager | USB card reader | Already owned |
| 1 | Spare 128 GB microSD | A cloned image, after the first card boots | Spare boot card | Pi microSD slot | `WAIT FOR APPROVAL` |

---

## Module 1 — Aroma

The only module with its own controller. The Pi sends a mist plan over one USB data cable. The ESP32 is the board that will act on that plan. Which GPIO does the acting is not assigned.

Nebulizers and the fan that pushes mist take power from a separate aroma supply, power source 2, through a physical stop. That supply’s current is not calculated. A 5 V, 6–7 A figure is not a selected part. Releasing the stop does not start mist.

```text
Pi USB ---- data + ESP32 chip power ---- ESP32
                                           |
                                           | control wire: not chosen
                                           v
Aroma supply -- stop switch -- nebulizer, and later the aerosol fan
```

| Qty | Component | Specification | Used for | Connects to | Decision |
|---:|---|---|---|---|---|
| 1 | ESP32 development board | Headers already soldered. Keep the exact listing and its schematic. | Receives mist commands | Pi, by the data cable | `BUY NOW` |
| 1 | USB data cable | Match the board socket, USB-C or micro-USB. Data, not charge-only. | Commands, and power for the ESP32 chip only | Pi USB-A → ESP32 | `BUY NOW` |
| 1 | Ultrasonic nebulizer | Order one only after the seller’s model matches a manufacturer page and its allowed fluid. ACEBOTT QB062/QB078 is a lead, not proof of the old sheet line. No spare yet. | First water-only mist check | Its documented power pins and control pin | `BENCH-TEST FIRST` |
| 1 | Current-limited 5 V bench supply | Limit taken from that module’s specification | Power and current measurement for one module | Wall → supply → stop switch → module | `BENCH-TEST FIRST` |
| 1 | Stop switch | DC contact rating must cover the measured current. Latching, normally closed. | Physical break in that module’s supply | In series with the module supply | `BENCH-TEST FIRST` |
| — | Distilled water | Only if the module’s instructions allow water | Test fluid. Not an essential oil. | In the test cup | `BENCH-TEST FIRST` |
| 2 | Further nebulizers | Same model as the unit that passed the bench | Pitta and Kapha. The first unit is the third channel. | Same interface as the first module | `WAIT FOR APPROVAL` |
| 1 | Spare nebulizer | A fourth unit | Replacement after the first unit has passed | — | `WAIT FOR APPROVAL` |
| 1 | Fan or blower | A named model and its datasheet. Motor kept out of condensate. | Pushes mist from the outlet. Speed wiring is not frozen. | Aroma supply, after the stop | `WAIT FOR APPROVAL` |
| 3 | Float switches | One per cup, after the cup exists | Empty-cup signal. An open wire means empty. | ESP32 inputs. Pins not assigned. | `WAIT FOR APPROVAL` |
| 1 | Leak sensor and drip tray | After the wet layout exists | Spill signal | ESP32. Pin not assigned. | `WAIT FOR APPROVAL` |
| 3 | Cups, tube, fittings | Diameter not chosen | Fluid and mist path. Carry-over on a shared outlet is untested. | Nebulizer → outlet | `WAIT FOR APPROVAL` |
| 1 | BME680 breakout | 3.3 V I²C | Room temperature, humidity, gas signal. Not an oil-dose meter. | ESP32 I²C, out of the droplets | `WAIT FOR APPROVAL` |
| 1 | 0.96-inch I²C OLED | Status text | Idle, run, or fault on the box | Same I²C bus | `WAIT FOR APPROVAL` |
| 1 | Enclosed aroma supply | Amperage from measured loads, including startup current | Power source 2 | Wall → stop → nebulizers and aerosol fan | `WAIT FOR APPROVAL` |
| 1 | Finished stop | DC rating for that current, and a reviewed re-arm | Cuts actuator power with no help from the Pi | In the aroma supply output | `WAIT FOR APPROVAL` |
| 1 | Pause button | A pin that is not a boot-strapping pin on that exact board | Pause at the box. GPIO 5 is withdrawn. | ESP32. Pin not assigned. | `WAIT FOR APPROVAL` |
| — | Oils and dropper bottles | Signed names and an exposure review | The scent | Into the cups after that review | `WAIT FOR APPROVAL` |
| 1 | Spare ESP32 | Same board as the first | Spare controller | — | `WAIT FOR APPROVAL` |

People stay out of the mist during the bench. Mass lost by the cup is not an inhaled dose. GPIO 18, GPIO 5, GPIO 2, and GPIO 13 are not assigned.

---

## Module 2 — Display

No second computer. The Pi plays the video. The screen has its own mains adapter, power source 3.

```text
Session on the Pi → mpv → HDMI port 0 → micro-HDMI cable → projector or TV
Projector or TV ← its own adapter ← wall
```

| Qty | Component | Specification | Used for | Connects to | Decision |
|---:|---|---|---|---|---|
| 1 | micro-HDMI to HDMI cable | Pi HDMI port 0. Length chosen when the screen position is known. | Picture | Pi → screen | `BUY NOW` |
| 1 | HDMI screen for setup | Any TV or monitor already owned | First boot, before a projector is chosen | The same HDMI cable | Already owned |
| 1 | Projector or TV, plus a mount | After a light test in the real room. Beem 470 is only a candidate. | The nature scene in the room | HDMI cable, and its own mains adapter | `WAIT FOR APPROVAL` |
| — | Scene video files | MP4 loops on the Pi card | The pictures themselves | `/var/lib/ayur/media/scenes/` | Files, not a purchase |

An LED strip is not part of this module.

---

## Module 3 — Music

No second computer. The Pi 5 has no headphone socket, so sound leaves through USB. The speaker has its own adapter, power source 4.

```text
Session on the Pi → mpv → USB audio adapter → 3.5 mm cable → speaker AUX
Speaker ← its own adapter ← wall
```

| Qty | Component | Specification | Used for | Connects to | Decision |
|---:|---|---|---|---|---|
| 1 | USB audio adapter | USB Audio Class, so Linux can select it | Sound output | Pi USB port | `BUY NOW` |
| 1 | 3.5 mm stereo cable | Male-to-male, unless the speaker jack needs another plug | Link to the speaker | Adapter → speaker AUX | `BUY NOW` |
| 1 | Powered speaker | About 10 W, AUX input, its own adapter | Bench playback. Not yet a study-room speaker. | 3.5 mm in, and its adapter to the wall | `BUY NOW` |
| — | Raga audio files | On the Pi card. Names still unsigned. | The music itself | `/var/lib/ayur/media/ragas/` | Files, not a purchase |
| 1 | Headphones | After the shared-room volume decision | Quiet playback on the same jack | In place of the speaker | `WAIT FOR APPROVAL` |

---

## Tools

Used while the three modules are built. They are not a module and they do not stay in the finished wiring.

| Qty | Component | Specification | Used for | Connects to | Decision |
|---:|---|---|---|---|---|
| 1 | Solderless breadboard and one jumper set | Low-voltage signals only | Later ESP32 checks | ESP32 header, after a pin is chosen | `BUY NOW` |
| 1 | Multimeter | DC volts and DC current | Bring-up, then the nebulizer current | Across the point being measured | `BUY NOW` |
| 1 | Soldering iron and solder | Skip if a kit is already owned | Headers, if a board arrives without them | — | `BUY NOW` |
| 1 | 0.01 g scale | Borrow first | Mass lost by the test cup. Not an inhaled dose. | Under the cup | `WAIT FOR APPROVAL` |
| 1 | Enclosure, glands, fused mains inlet | After the wet and dry layout is measured | The room box. Until then each adapter keeps its own mains lead. | Mechanical | `WAIT FOR APPROVAL` |

---

## Buy now

This is the only order to place today. It is the `BUY NOW` rows from the modules above. If a row says “already owned” or “skip if owned,” do not buy a second one.

| Qty | Component | Module | Connects to |
|---:|---|---|---|
| 1 | Raspberry Pi 5, 4 GB | Core | Supply, card, cooler, RTC battery |
| 1 | Official Raspberry Pi 27 W USB-C supply, local mains plug | Core | Wall → Pi |
| 1 | Raspberry Pi 5 active cooler | Core | On the Pi |
| 1 | 128 GB high-endurance microSD | Core | Pi card slot |
| 1 | USB microSD reader, if the laptop has no slot | Core | Laptop → card |
| 1 | Official Raspberry Pi RTC battery, ML2020 with JST plug | Core | Pi RTC connector |
| 1 | ESP32 development board, headers fitted | Aroma | Pi, by the data cable |
| 1 | USB data cable matched to that ESP32 | Aroma | Pi USB-A → ESP32 |
| 1 | micro-HDMI to HDMI cable | Display | Pi HDMI0 → a screen you already have |
| 1 | USB audio adapter | Music | Pi USB port |
| 1 | 3.5 mm stereo cable | Music | Adapter → speaker |
| 1 | Powered AUX speaker, about 10 W | Music | 3.5 mm, and its own adapter |
| 1 | Breadboard and one jumper set | Tools | Bench only |
| 1 | Multimeter | Tools | Bench only |
| 1 | Soldering iron and solder, if you do not own one | Tools | Bench only |

That is **15 purchased items** when none of them are already owned. The phone, the laptop, and an existing HDMI screen are used and not bought.

Leave every `BENCH-TEST FIRST` and `WAIT FOR APPROVAL` row until the condition in that row is true.

---

## Small-parts check

These do not appear as module lines because they are not extra purchases. Each one was checked against the four power sources and the unfrozen aroma circuit.

### Batteries

| Part | Qty | Verdict | Why |
|---|---:|---|---|
| Official Pi RTC battery, ML2020 with JST plug | 1 | `BUY NOW` | The only battery in the project. It holds the clock when mains drops. Listed under the shared core. |
| CR2032 or any other primary coin cell | 0 | `NOT REQUIRED` | The Pi 5 RTC circuit charges the cell. A primary cell is the wrong part. |
| 18650 cells | 0 | `NOT REQUIRED` | The unit runs from mains. |
| Power bank | 0 | `NOT REQUIRED` | It is not the Pi’s specified supply, and it is not the aroma supply. |
| Battery inside the ESP32 | 0 | `NOT REQUIRED` | The ESP32 chip is powered from the Pi USB cable. |
| Battery inside the speaker | 0 | `NOT REQUIRED` | The speaker uses its own adapter. |

### Converters

| Part | Qty | Verdict | Why |
|---|---:|---|---|
| Buck converter, any voltage | 0 | `NOT REQUIRED` | There is no 12 V rail. The Pi, the screen, and the speaker each have their own adapter. |
| Boost converter | 0 | `NOT REQUIRED` | No Phase-1 part needs a higher voltage than the supply that feeds it. |
| Extra 3.3 V regulator board | 0 | `NOT REQUIRED` | The ESP32 development board already makes 3.3 V from USB 5 V. |
| 74AHCT125 level shifter | 0 | `NOT REQUIRED` | Only an optional 5 V LED strip would need it. That strip is not in this build. |
| USB audio adapter | 1 | `BUY NOW` | A sound output, not a power converter. Listed under music. |

### Resistors

| Part | Qty | Verdict | Why |
|---|---:|---|---|
| 150 Ω gate resistors | 0 | `NOT REQUIRED` yet | No MOSFET and no load have been chosen. |
| 10 kΩ gate pull-downs | 0 | `NOT REQUIRED` yet | Those values are not accepted. |
| 330 Ω LED-strip resistor | 0 | `NOT REQUIRED` | The LED strip is not in this build. |
| I²C pull-up resistors | 0 | `NOT REQUIRED` yet | Buy them only if a later BME680 or OLED board does not already include them. |
| Pause-button pull-up | 0 | `NOT REQUIRED` yet | The pause pin is not chosen. |
| Resistor assortment | 0 | `NOT REQUIRED` | An unsorted kit is not a design. |

### Transistors and MOSFETs

| Part | Qty | Verdict | Why |
|---|---:|---|---|
| IRLZ44N, or any other MOSFET | 0 | `NOT REQUIRED` yet | The nebulizer may have its own trigger pin. A MOSFET is added only if that datasheet says the supply pin must be switched. On-resistance at 3.3 V is not in the IRLZ44N specification. |
| BJT transistor | 0 | `NOT REQUIRED` | No stage in this unit uses one. |
| Fan MOSFET | 0 | `NOT REQUIRED` | A MOSFET in the fan supply is not a 4-wire speed input. The emergency cut is the stop switch. |
| Relay or L298N | 0 | `OMIT` | Left off the unit. |

### Connectors, wire, and fixings

| Part | Qty | Verdict | Why |
|---|---:|---|---|
| USB-C lead on the Pi supply | 1 | Included with the official supply | Do not buy a second Pi power cable. |
| USB data cable to the ESP32 | 1 | `BUY NOW` | Listed under aroma. Charge-only cables fail this job. |
| micro-HDMI cable | 1 | `BUY NOW` | Listed under display. |
| 3.5 mm cable | 1 | `BUY NOW` | Listed under music. |
| RTC JST plug | 1 | Included with the official RTC battery | Do not buy a loose connector. |
| Breadboard jumper wires | 1 set | `BUY NOW` | Listed under tools. |
| 2.54 mm header strip | 1 | Only if the ESP32 arrives without headers | Confirm before a separate purchase. |
| Nebulizer connector | 0 | `WAIT FOR APPROVAL` | Pitch and pin count come from the module that is actually bought. |
| Aroma hook-up wire, ferrules, fuse, terminal block | 0 | `WAIT FOR APPROVAL` | Gauge and fuse rating follow the measured current. |
| Cable glands and an IEC inlet | 0 | `WAIT FOR APPROVAL` | Only when one box holds the adapters. |
| Pi case or M2.5 standoffs | 0 | `NOT REQUIRED` for the first build | The board can sit on a dry insulating surface. |

---

## Left out of the project

| Item | Decision |
|---|---|
| Arduino Uno, L298N, relay boards | `OMIT` |
| Two 5-inch ESP32 touch boards, fingerprint reader, camera, 7-inch touch screen | `OMIT` |
| Heart-rate and skin-conductance sensors | `OMIT` |
| Two full LED reels, extra OLEDs, a second BME680 | `OMIT` |
| Network switch | `OMIT` |
| Custom PCB | `OMIT` |
| Treadmill | `OMIT` |

The student coordinator is a person, not a component. The old sheet total of ₹2,27,766 is not the cost of the buy-now list.

---

## Order of work

1. Buy the **Buy now** table.
2. Install the current Raspberry Pi OS 64-bit. Play one picture. Play one sound. Open one page from the phone.
3. Identify the nebulizer. Then buy the aroma rows marked `BENCH-TEST FIRST` and run one water-only bench with the stop switch in the supply.
4. Buy a `WAIT FOR APPROVAL` row only when the condition in that row is true.
