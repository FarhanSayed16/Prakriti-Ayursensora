# Prakriti Ayursensora
## Buy list

This is not an approved purchase order for the aroma subsystem. Each line has one status.

| Status | Meaning |
|---|---|
| `BUY NOW` | Useful without the unresolved aroma circuit. |
| `BENCH-TEST FIRST` | One unit, for a supervised water-only bench. Not a spare, and not a frozen design. |
| `WAIT FOR APPROVAL` | Held until a datasheet, a measurement, or a named review exists. |
| `OMIT` | Leave out of this phase. |

Nothing below is approval for use around people. No aroma bench test is recorded in this repository.

---

## Buy now

| Qty | Item | Why this quantity | Status |
|---:|---|---|---|
| 1 | Raspberry Pi 5, 4 GB or 8 GB | One brain. Either memory size runs this phase. | `BUY NOW` |
| 1 | Official Raspberry Pi 27 W USB-C supply | The Pi 5’s own specified supply. Source 1 of four. | `BUY NOW` |
| 1 | Raspberry Pi 5 active cooler | The Pi’s own cooling path. | `BUY NOW` |
| 1 | 128 GB high-endurance microSD | One boot card. A second card is not required to start. | `BUY NOW` |
| 1 | CR2032 for the RTC | One clock battery. | `BUY NOW` |
| 1 | USB-A to USB-C data cable | Pi to one ESP32. Power-only cables are not this cable. | `BUY NOW` |
| 1 | ESP32 development board | USB-serial bring-up only. GPIO is not frozen, so this board is not a wiring release. | `BUY NOW` |
| 1 | micro-HDMI to HDMI cable | Pi video output. | `BUY NOW` |
| 1 | USB audio adapter | Pi 5 has no audio jack. | `BUY NOW` |
| 1 | 3.5 mm audio cable | Adapter to the speaker. | `BUY NOW` |
| 1 | Powered speaker, about 10 W, with AUX | Playback check. Its adapter is source 4. | `BUY NOW` |
| 1 | Solderless breadboard and one jumper set | Bench only. | `BUY NOW` |
| 1 | Multimeter | Measure the bench current. Skip if one is already owned. | `BUY NOW` |
| 1 | Soldering kit | Skip if one is already owned. | `BUY NOW` |
| 1 | 0.01 g scale | Mass lost by the test setup. This is not an inhaled dose and not a safety proof. | `BUY NOW` |

---

## Bench-test first

| Qty | Item | Why this quantity | Status |
|---:|---|---|---|
| 1 | One ultrasonic nebulizer candidate | The old sheet named an ACEBOTT 2.4 MHz module and listed three. No datasheet is in the project. Buy one. Do not buy the spare until this one passes the water-only bench. | `BENCH-TEST FIRST` |
| 1 | Latching normally-closed stop switch | One switch to test a physical break in the actuator feed. Contact rating is still unknown, so this is a bench sample, not the finished safety part. | `BENCH-TEST FIRST` |
| — | Distilled water | Test fluid only if the module’s own instructions allow water. Not an essential oil. | `BENCH-TEST FIRST` |
| 1 | Current-limited 5 V bench supply | Enough for one module while its current is measured. This is not the guessed 6–7 A enclosed supply. | `BENCH-TEST FIRST` |

Do not buy a PWM fan as the solution. No fan model or datasheet is in the project. When a model is chosen, buy one under `BENCH-TEST FIRST` and keep its wiring marked **UNRESOLVED — DO NOT FREEZE WIRING** until that sheet is checked.

---

## Wait for approval

| Qty | Item | What has to exist first | Status |
|---:|---|---|---|
| 1 | Spare ESP32 | The first board talks over USB. | `WAIT FOR APPROVAL` |
| 1 | Spare microSD | The first card boots. | `WAIT FOR APPROVAL` |
| 3 | Further nebulizers | The first module’s control pin, current, and allowed fluid. | `WAIT FOR APPROVAL` |
| — | IRLZ44N-class MOSFETs, any quantity | Proof that the module is switched on its supply pin, plus the measured current and a gate drive that works at 3.3 V. Six pieces had no such basis. | `WAIT FOR APPROVAL` |
| — | 150 Ω and 10 kΩ resistors | A chosen MOSFET and a chosen load. Those values are not accepted. | `WAIT FOR APPROVAL` |
| — | PWM fans | A named model and its datasheet. GPIO 18 is not assigned. | `WAIT FOR APPROVAL` |
| — | Float switches, leak sensor, cups, tubing | A reservoir layout, and a carry-over and cleaning check for the shared outlet. | `WAIT FOR APPROVAL` |
| 1 | BME680 | An environmental log. It does not meter oil dose. | `WAIT FOR APPROVAL` |
| 1 | OLED | A status display after a pin is chosen. | `WAIT FOR APPROVAL` |
| — | Dropper bottles and essential oils | Signed oil names and an exposure review. The old sheet’s oil sets stay in this hold. | `WAIT FOR APPROVAL` |
| 1 | Pause button | A GPIO that is not a boot-strapping pin, on the exact board. GPIO 5 is withdrawn. | `WAIT FOR APPROVAL` |
| 1 | Enclosed aroma supply, including any 5 V 6–7 A unit | A current sum from manufacturer data, including startup or peak, for the parts actually selected. | `WAIT FOR APPROVAL` |
| 1 | Projector or TV, and its mount | A light check in the real room. Its adapter is source 3. | `WAIT FOR APPROVAL` |
| 1 | Headphones | A decision on shared-room volume. | `WAIT FOR APPROVAL` |
| — | IEC inlet, fuses, glands, perfboard, final enclosure | A finished mechanical layout after the bench. | `WAIT FOR APPROVAL` |

---

## Omit

| Item | Status |
|---|---|
| Arduino Uno, L298N, relay boards | `OMIT` |
| Two 5-inch ESP32 touch boards, fingerprint reader, camera, 7-inch touch screen | `OMIT` |
| Power bank, 18650 cells, buck converters | `OMIT` |
| Network switch | `OMIT` |
| Heart-rate and skin-conductance sensors | `OMIT` |
| Two full LED reels, extra OLEDs, a second BME680 | `OMIT` |
| A fan MOSFET bought as the speed-control method | `OMIT` |
| Custom PCB for this phase | `OMIT` |
| Treadmill | `OMIT` |

The student coordinator is a person, not a component.

The old sheet total of ₹2,27,766 includes omitted lines and items on hold. It is not the cost of this list.
