# Prakriti Ayursensora — Component Finalization Review

**Review date:** 27 October 2026  
**Purpose:** Turn the current project documents into a practical, evidence-based component decision plan.  
**Status:** Architecture accepted in principle; aroma hardware and safety circuit are **not approved for final purchase or wiring**.

> This review is based on the six latest Markdown files, the original proposal PDF/PPTX, and the photographed 47-line component sheet. Manufacturer specifications are linked where verified. The actual repository source code, hardware prototype schematic, and exact seller listings for several components were not included in this upload set, so this is not a claim that hardware has been tested.

## 1. Executive verdict

Keep the three-module architecture:

- **Raspberry Pi 5:** the session coordinator, staff web interface, database/logging, video and audio playback.
- **ESP32:** the only controller intended to command the aroma subsystem and monitor its local safety inputs.
- **Display:** HDMI from the Pi to a projector or TV.
- **Music:** a wired USB audio adapter and 3.5 mm output to an AUX speaker or suitable headphones.
- **Phone/tablet:** browser connects to the Pi's local web interface; it does not command the ESP32 directly.

Do **not** freeze the aroma circuit or buy its full BOM. The unresolved issues are not only missing document fields: they determine whether the chosen atomizer can be controlled as required, what driver (if any) is necessary, the fan/airflow arrangement, the actuator supply rating, the emergency stop and the fluid-delivery method.

### What can be finalized now

The core-computer and wired-media architecture is reasonable. A Raspberry Pi 5 with 4 GB RAM is a sensible starting configuration for the described FastAPI/SQLite/mpv workflow; 8 GB is not needed merely for this Phase I workload. Keep the official 27 W Pi supply, active cooling, micro-HDMI cable, wired audio path, and one ESP32 for serial bring-up, subject to selecting exact SKUs and validating them on the chosen OS.

### What cannot be finalized from the current files

The files do not identify the exact nebulizer revision, named fan model, verified driver circuit, supply current, final GPIO map, completed E-stop implementation, measured aerosol behaviour, shared-path cleaning method, approved oils, or exposure limits. No firmware, wiring implementation, or bench-test record is shown in the submitted documentation.

**Current verdict:** `GO` for planning and independent Pi/media bring-up; `CONDITIONAL GO` for a single-module water-only bench test after the exact atomizer and its instructions are confirmed; `NO-GO` for the complete aroma assembly, essential oils, human exposure, and final enclosure until the gates in this document are closed.

---

## 2. New and confirmed corrections

### 2.1 Raspberry Pi 5 RTC battery: CR2032 is the wrong purchase instruction

The updated buy list says `CR2032 for the RTC`. Raspberry Pi's official Pi 5 RTC accessory is a **rechargeable ML2020 lithium-manganese coin cell with a two-pin JST-SH connector**. Raspberry Pi's documentation warns against using a primary, non-rechargeable lithium cell in this role because the RTC circuit includes a charger and a primary cell is not the intended part.

**Action:** Replace `CR2032` with `official Raspberry Pi Pi 5 RTC battery (ML2020, pre-fitted connector)`. The RTC still works without a backup battery, so this can be deferred if the prototype will always obtain reliable time from a network or if budget is tight.

Sources:
- https://www.raspberrypi.com/products/rtc-battery/
- https://github.com/raspberrypi/documentation/blob/master/documentation/asciidoc/computers/raspberry-pi/rtc.adoc

### 2.2 Operating system version: Bookworm is no longer the current default

The architecture names Raspberry Pi OS Bookworm (Debian 12). As of the review date, Raspberry Pi's official download page lists **Raspberry Pi OS 64-bit based on Debian Trixie (Debian 13)** as the latest release.

**Action:** Do not hard-code Bookworm solely because it appeared in the early architecture. Start with the current supported Raspberry Pi OS 64-bit image and test FastAPI, SQLite, mpv, audio selection, HDMI playback, serial permissions, services and shutdown/recovery on that image. If a dependency forces Bookworm, record the reason and support/update implications explicitly.

Source: https://www.raspberrypi.com/software/operating-systems/

### 2.3 Nebulizer: there is a plausible manufacturer reference, but the BOM does not identify the exact part

The paper component sheet states only `ACEBOTT, 2.4 MHz`. The official ACEBOTT documentation page for **QB062/QB078 Humidifier Module** describes a 5 V module, a falling-edge trigger, and a water-mist demonstration; its sample uses an ESP32 signal pin. That is evidence for a possible trigger-input approach **only if the bought module is confirmed to be the same model/revision**.

The source does not establish:

- that the item on the original sheet is exactly QB062 or QB078;
- that it supports essential oils or oil/water mixtures;
- its electrical current draw (including startup/peak);
- that an `ms: 8000` software command gives exactly 8 seconds of controlled output;
- the output mass, airborne concentration or inhaled dose;
- that the proposed cups, tubes, fan and shared outlet are compatible with its operation.

**Action:** Before buying even the first sample, obtain the seller link and model/revision, then match it to the manufacturer guide and written fluid restrictions. If it matches, a supervised water-only bench test may proceed according to the manufacturer's instructions. Do not infer oil compatibility from a water test.

Source: https://acebott.com/docs/qb062-qb078-humidifier-module/

### 2.4 The current proposed MOSFET choice is not approved

The source sheet's `IRLZ44N-class` is a proposal, not a selected driver. Infineon's IRLZ44N datasheet gives its on-resistance under specified gate voltages such as 4 V, 5 V and 10 V; it does not provide the design evidence needed to assume a 3.3 V ESP32 output will drive the selected load adequately in the final circuit.

For an atomizer with a dedicated logic trigger, a power MOSFET may not be necessary at all. For supply switching, choose a driver only after the exact load, current, control interface, safe default state, heat and fault behaviour are known. The gate resistor and pull-down values must follow that circuit rather than a generic shopping list.

**Action:** Do not buy six MOSFETs or a set of gate resistors yet. Revisit the driver only after the atomizer model and interface are confirmed.

Source: https://www.infineon.com/assets/row/public/documents/24/49/infineon-irlz44n-datasheet-en.pdf

### 2.5 Fan control and fan selection remain unresolved

The original proposal lists 5 V, 80 mm brushless fans; the revised buy list mentions a 4-pin PWM type. No model is named. For a standard four-wire PWM fan, supply, ground, tachometer and PWM control are separate signals; PWM on the supply line is not equivalent and may be unsafe for the fan. The exact control level/frequency must follow the selected fan's datasheet.

The larger unresolved point is mechanical: the documents show a fan moving aerosol from three cups into a shared outlet, but they do not demonstrate that the selected fan is appropriate for the wet/aerosol path or that condensation will not reach the fan motor/electronics.

**Action:** Do not buy an unspecified fan. First define the airflow arrangement and keep the fan/electronics as dry as practicable; then choose a named fan or blower from its pressure/airflow, voltage, PWM, current, noise, and material/environment specifications. Keep speed control separate from the emergency power cut.

Reference for four-wire PWM concepts: https://www.noctua.at/en/support/faqs/microcontroller-guide-pwm-setup-and-rpm-monitoring

### 2.6 Emergency stop: the documented requirement is stronger than the documented circuit

The E-stop is intended to remove power independently from the Pi, ESP32 and software. The files also require that releasing the E-stop, rebooting either board, or reconnecting USB must not restart mist automatically. A normally-closed switch in the actuator supply can interrupt power, but by itself it does not guarantee a deliberate re-arm after power is restored.

**Action:** The final circuit must have a reviewed safe-off/re-arm behaviour, appropriate DC contact rating, suitable branch protection and no signal-pin back-power path that can energize a disabled module. The controller must be able to keep output commands inactive, but software alone is not the independent emergency cut. Test the physical stop on the bench before adding more channels.

### 2.7 Power rating and the “four sources” count

Four power sources are represented in the files: (1) Pi supply, (2) aroma low-voltage supply, (3) display adapter, and (4) speaker adapter. The revised component list correctly states four. Keep that count everywhere. Decide whether the projector and speaker adapters remain external; do not assume that one custom mains inlet is needed for the prototype.

The proposed 5 V, 6–7 A aroma supply is not validated. Build a load table from each exact component's continuous and startup/peak current; include the fan and any optional loads, cable/connector ratings, supply derating and protection before choosing an enclosed supply. Do not extrapolate the full three-channel supply from a single module's voltage rating.

### 2.8 Shared outlet and BME680 limitations remain

- Three reservoirs feeding one plenum/outlet do not mean three isolated scent paths. Carry-over, condensation, backflow and cleaning must be tested; a separate outlet per channel is a possible design alternative if isolation cannot be demonstrated.
- The BME680 can log environmental measurements and a broad gas/VOC response. It is **not** a selective essential-oil sensor, a calibrated aerosol dose meter, or evidence that inhalation is safe.

Source: https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme680-ds001.pdf

### 2.9 The printed 47-line sheet total does not match its line-item arithmetic

Adding the 47 line totals visible in the uploaded component-sheet photos gives **₹2,27,851**, while the printed grand total is **₹2,27,766** — a difference of **₹85**. Because the photo is a scan, confirm against the original editable sheet before using either total. In any case, the old total is not the connected Phase I BOM because it includes items on hold or outside the three-module unit.

---

## 3. Component decision table

Statuses here are recommendations from this review. They are not proof of purchase, physical compatibility, or a completed safety assessment.

| Component | Current decision | Reason / condition to close |
|---|---|---|
| Raspberry Pi 5, 4 GB | **APPROVE FOR PHASE I** | Enough for the proposed local UI, SQLite and media orchestration as a starting point. Use 8 GB only if already owned or a tested workload requires it. |
| Official Raspberry Pi 5 27 W USB-C PSU | **APPROVE** | Use the model specified by Raspberry Pi; it powers the Pi, not the aroma actuators. |
| Raspberry Pi 5 active cooler | **APPROVE** | Sensible for sustained playback and an enclosure; verify mounting and enclosure airflow. |
| microSD, 128 GB high-endurance | **APPROVE ONE** | Use a reputable card; the spare can wait. Ensure media fits and backups are tested. |
| RTC battery | **CORRECT SPEC / OPTIONAL** | Official rechargeable ML2020 unit with JST-SH plug, not CR2032. Buy only if offline time retention is required. |
| ESP32 development board | **ONE FOR SERIAL BRING-UP ONLY** | Choose one exact SKU/revision and keep its schematic. Do not freeze GPIO assignments or buy a spare yet. |
| USB-A to USB-C data cable | **APPROVE AFTER BOARD CONNECTOR CHECK** | Must carry data, not power only. Match both connectors to the selected board. Do not power actuators from the Pi USB. |
| micro-HDMI to HDMI cable | **APPROVE** | Raspberry Pi 5 has micro-HDMI output. Choose length after physical placement. |
| USB stereo audio adapter | **APPROVE** | Choose a Linux-compatible USB Audio Class device; test ALSA device selection after OS installation. |
| AUX-powered speaker | **APPROVE FOR BENCH / DEMO** | 10 W is a starting category, not a clinical-room choice. Check noise, placement and measured sound level before shared-room use. |
| Wired headphones / pillow speaker | **WAIT** | Decide after assessing the actual room and acoustic/privacy requirements. |
| Projector versus TV | **WAIT FOR ROOM TEST** | Test the intended scene at the real screen size and actual ambient light. Beem 470 is only a candidate; do not rely solely on the advertised brightness/resolution. |
| One nebulizer candidate | **MODEL IDENTIFICATION FIRST, THEN ONE SAMPLE** | The original sheet does not identify an exact SKU. Match seller item to official documentation and fluid restrictions. No spare and no oils at this stage. |
| Nebulizers 2–4 | **WAIT** | Only after one unit's control, current, fluid compatibility and bench behaviour are documented. |
| Dedicated nebulizer control / MOSFETs | **WAIT** | The exact module may have a trigger input. Do not assume supply switching is required. Select driver only from the verified circuit and load. |
| 150 Ω / 10 kΩ resistors | **WAIT** | Values must be derived from the selected driver/interface. Do not order them as universal gate values. |
| Fan / blower | **WAIT FOR AIRFLOW DESIGN + DATASHEET** | Name the model, define the wet/dry airflow route, verify PWM interface, current, startup and environment. Do not order a generic 80 mm 4-wire fan as a proxy. |
| Current-limited 5 V bench supply | **BORROW OR BUY FOR ONE-MODULE TEST** | Set the limit according to the verified module specification. Do not substitute a guessed 6–7 A enclosed supply. |
| E-stop switch | **BENCH SAMPLE ONLY IF DC RATING IS KNOWN** | Select contact rating for actual DC current and fault path. A final installation also requires a deliberate re-arm design and reviewed protection. |
| Float switches | **WAIT FOR RESERVOIR GEOMETRY** | Orientation, switching logic, failure-on-disconnect behaviour and placement depend on cup design. |
| Leak sensor and drip tray | **WAIT FOR WET-SIDE LAYOUT** | Select coverage, tray size and drainage only after the physical layout is defined. |
| Cups, tubing, tees/Y-connectors | **WAIT** | Must fit module geometry and support drainage, cleaning and carry-over validation. Avoid locking an 8 mm tube diameter by assumption. |
| BME680 breakout | **DEFER / OPTIONAL** | Environmental log only; not an aroma safety interlock. Check breakout supply/I²C levels and keep it out of direct droplets. |
| OLED | **DEFER / OPTIONAL** | Not needed for the first bench test. Select after pin map and breakout voltage are known. |
| Dropper bottles and essential oils | **WAIT FOR SIGNED MAPPING + SAFETY REVIEW** | Proposal slide and speaker notes disagree on oils; old purchase sheet lists a different set. Buy no oils until a named expert approves one exact pilot set and exposure protocol. |
| 0.01 g scale | **OPTIONAL / BORROW FIRST** | Not required to establish the trigger pin or current for the first electrical test. Mass loss does not equal an inhaled dose or prove safety. |
| Breadboard and jumpers | **APPROVE FOR LOW-VOLTAGE BENCH ONLY** | Never use a breadboard for mains wiring or as the finished high-current actuator assembly. |
| Soldering kit / multimeter | **BUY ONLY IF NOT ALREADY AVAILABLE** | A multimeter is useful for bring-up; borrow lab tools where possible. |
| Final enclosure, IEC inlet, fuse holders, terminals, glands, perfboard/PCB | **WAIT** | Freeze only after the real wet-side layout and electrical/protection design exist. For the prototype, keep certified mains adapters external where practical; do not build an unreviewed mains enclosure. |
| Arduino Uno, L298N, relays, touch ESP32 boards, camera, fingerprint sensor, heart/skin sensors, 2×5 m LED reels, network switch, power bank/18650 pack, buck modules, treadmill | **OMIT FROM PHASE I** | Not required for the three-module open-loop prototype as scoped. Reconsider only with a separately approved requirement. |

---

## 4. Key system decisions that should stay open

### Decision A — What is the aroma output actually supposed to be?

The concept combines aroma/oils with a mist-like output, but the current candidate is a water-humidifier-style ultrasonic module. Before selecting the subsystem, obtain a written decision from the responsible Ayurveda/clinical leads and the module manufacturer on the intended delivery method and allowed fluid.

Possible paths to evaluate:

1. **Water-only engineering demonstrator:** use a verified water-humidifier module only for controlled bench demonstrations. Do not describe it as a validated essential-oil delivery system and do not use it around people.
2. **Oil-compatible commercial delivery technology:** select a device explicitly designed and documented for the exact intended oil/fluid, then verify whether it offers a safe external enable/control interface suitable for the ESP32. This option still requires a safety/exposure review before human use.

Do not compromise by placing oils into a water module that has not been approved for them.

### Decision B — Three independent aroma channels or one shared outlet?

If the product requirement is to time-share channels without scent carry-over, a common plenum may be unsuitable without a successful measured purge/cleaning procedure. Prototype the fluid/air path before choosing tubing, valves, fan, enclosure or 3D-printed brackets. If carry-over cannot be controlled, evaluate separate outlets or a different isolation strategy.

### Decision C — How exactly does the E-stop re-arm?

The safe state must survive a software crash, unplugged USB, ESP32 reset, power brownout and reapplication of power. The E-stop physically removes actuator power. Once released, the system must remain unarmed until an intentional reset/start sequence; a switch that simply restores 5 V does not prove this requirement. Include signal-pin back-power prevention and defined idle levels in the interface design.

### Decision D — What can the BME680 prove?

It can provide environmental logs. Do not use temperature, humidity, or the raw gas signal as a substitute for an aroma output limit or safety assessment. Decide whether the sensor is actually needed in the first demonstrator; its use is not required to prove the Pi–ESP32 control link.

### Decision E — What operating-system image is the baseline?

Use the current supported Raspberry Pi OS 64-bit image as the starting point and test every service/library on that image. The architecture should not declare Bookworm as the baseline without a dependency reason and a test result.

---

## 5. Recommended build order

1. **Freeze core-compute SKUs:** Pi 5 (4 GB is sufficient to start), official 27 W PSU, cooler, one microSD card, one exact ESP32 dev board, and matching data cable.
2. **Bring up the Pi without aroma hardware:** install the current supported Raspberry Pi OS 64-bit, verify HDMI, play one video, play one audio file through USB audio, run a local web page, record a test event in SQLite, and test reboot/shutdown.
3. **Identify the atomizer exactly:** record its model/revision, seller source, manufacturer page, voltage, control pin behaviour, current, approved fluid and operating instructions. If it cannot be positively identified, stop before purchase/test.
4. **Bench-test one unit only:** supervised, water only if manufacturer instructions permit it, with a current-limited supply, people away from aerosol, and no oils. Record voltage, steady/startup current, signal waveform/trigger behaviour, mist cycle timing, abnormal heating, leaks and mass change separately. Do not interpret mass loss as inhaled dose.
5. **Design/test the stop path:** use a known-rated DC switch or lab-rated switching arrangement. Verify stop cuts intended actuator power. Verify stop release, controller reset, USB disconnect and power restoration do not restart mist without a deliberate new start.
6. **Select the airflow hardware:** define outlet geometry and determine whether the fan stays dry. Select a named fan/blower from its datasheet, then add and test it separately.
7. **Calculate the final supply and protection:** use the exact selected loads, startup current, wiring/connector ratings, fuse/contact ratings and thermal/derating conditions. Only then choose an enclosed actuator supply and final terminals.
8. **Check multi-channel isolation:** only after one channel works, add channel 2 and 3, then test carry-over, backflow, drainage and repeatable cleaning.
9. **Freeze the wet/dry enclosure:** after the measured bench layout, not before.
10. **Only after expert sign-off:** purchase approved oils and define exposure/session limits. Human-use clearance is a separate safety/ethics gate, not the result of a successful demo.

---

## 6. Evidence needed to turn this into a final purchase order

The project files do not yet contain these exact product identifiers. Provide or obtain them before approving the respective line items:

1. **Atomizer:** exact seller URL/model/revision; manufacturer's datasheet or instructions; allowed fluid; trigger logic and electrical ratings.
2. **ESP32:** exact board purchase link or board photo showing both sides and module marking, plus schematic/pinout for that exact revision.
3. **Fan/blower:** exact model and datasheet, but only after airflow geometry is decided.
4. **Bench supply and E-stop:** current limit/rating, DC contact rating, and proposed wiring path.
5. **Fluid path:** reservoir/cup dimensions, tubing material/diameter, outlet geometry, planned cleaning and carry-over acceptance criteria.
6. **Oil plan:** named sign-off for one exact candidate per channel and an exposure/exclusion review before any use around people.
7. **Projection environment:** actual room lighting, intended screen size and throw distance; then compare the Beem 470 against a TV in that environment.

A product name or an online listing alone is not enough when the electrical/control variant can differ. Exact model and manufacturer documentation are the required evidence.

---

## 7. Project questions that are not component-shopping decisions

The following should remain open and must not be invented in firmware or inferred from a component selection:

- The signed mapping of oil, nature scenes and ragas for Vata, Pitta, Kapha, dual types and Sama.
- The conflict between the proposal slide and speaker notes for oil assignments (lavender and peppermint/mint are not consistently assigned).
- The difference between the proposal's listed oil set and the old component sheet's helichrysum/davana/tea-tree/Tridoshic set.
- The questionnaire scoring method, thresholds for single/dual/Sama and clinician confirmation workflow.
- Human-use exclusions, consent, maximum exposure/session timing, volume/brightness limits and ethics approval.
- Whether the current questionnaire code and the existing aroma prototype source code/schematic are available. The submitted documents say these have not been collected into the repository.

These decisions affect the system requirements but cannot be settled by choosing a MOSFET, fan, or power supply.

---

## 8. Final conclusion

The architecture is now sufficiently clear to begin work on the **Pi/media core** and to define a controlled bench test. It is **not** sufficiently specified to purchase the full aroma subsystem. The highest-priority next action is to confirm the exact nebulizer model and manufacturer instructions, not to pick the number of MOSFETs or the 5 V supply first.

Keep the purchase list as a staged list. Buy only the parts needed for independent computer/media bring-up and, after exact module confirmation, one controlled low-voltage bench test. Do not purchase three additional nebulizers, an unspecified fan, bulk driver components, oils, the final enclosure or a guessed high-current supply until their respective gates are closed.

This document is an engineering selection review, not a clinical approval, inhalation-safety certification, or completed electrical design.

## 9. Manufacturer and product references

1. Raspberry Pi RTC battery (ML2020, pre-fitted JST plug): https://www.raspberrypi.com/products/rtc-battery/
2. Raspberry Pi 5 RTC documentation (battery guidance): https://github.com/raspberrypi/documentation/blob/master/documentation/asciidoc/computers/raspberry-pi/rtc.adoc
3. Raspberry Pi OS downloads and current release: https://www.raspberrypi.com/software/operating-systems/
4. ACEBOTT QB062/QB078 Humidifier Module guide: https://acebott.com/docs/qb062-qb078-humidifier-module/
5. Infineon IRLZ44N datasheet: https://www.infineon.com/assets/row/public/documents/24/49/infineon-irlz44n-datasheet-en.pdf
6. Noctua guide to four-wire PWM fan control: https://www.noctua.at/en/support/faqs/microcontroller-guide-pwm-setup-and-rpm-monitoring
7. Bosch BME680 datasheet: https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme680-ds001.pdf
8. Portronics Beem 470 candidate product page: https://www.portronics.com/products/beem-470
