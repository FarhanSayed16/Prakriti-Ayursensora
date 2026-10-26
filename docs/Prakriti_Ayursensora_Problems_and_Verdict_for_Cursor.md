# Prakriti Ayursensora — Existing Problems, Engineering Concerns, and Verdict

**Purpose:** This document records the problems and unresolved questions identified during the review of the Prakriti Ayursensora project documents and proposed Phase I buy list. Give it to Cursor as a review brief. Cursor should inspect the actual project files, verify each point, and report evidence-backed conclusions. This is **not** a final wiring design or a replacement BOM.

## 1. Overall verdict

**Verdict: Keep the high-level three-module concept, but do not freeze the hardware design, purchase the complete aroma subsystem, or use it around people yet.**

The overall concept is reasonable: a Raspberry Pi coordinates the experience, an ESP32 controls the aroma hardware, the display uses HDMI, and music uses a wired audio connection. However, several hardware interfaces, safety behaviours, fluid-delivery assumptions, and product choices remain unverified.

The **main blocker is the aroma subsystem**. Power sizing, fan control, emergency-stop behaviour, enclosure/mains arrangements, and sensory-exposure validation are also blocking items. A successful software demo does not prove electrical safety, inhalation safety, Ayurvedic/clinical validity, or therapeutic effectiveness.

## 2. Problems and unresolved engineering questions

### 2.1 Nebulizer model, fluid compatibility, and control interface — critical

- The proposed 5 V ultrasonic nebulizers are described in the documents as water-humidifier-style modules. Water operation does **not** prove that the module is compatible with essential oils or oil-water mixtures.
- Do not assume that essential oils can be added to the reservoir safely or that the materials, wick, transducer, seals, tubing, and output path tolerate them.
- The proposed control method must match the exact module. Some modules use a dedicated trigger/signal input; switching the module's 5 V supply with a MOSFET may not be the correct control method.
- Confirm the exact part number, datasheet, pinout, operating voltage/current, trigger polarity, start/stop behaviour, fluid restrictions, cleaning requirements, and manufacturer-approved use.
- Pulse duration is not the same as delivered mist quantity. Output can vary with liquid level, module condition, fluid properties, warm-up, and enclosure airflow.
- A 0.01 g scale can help measure mass change in controlled tests, but by itself it does not establish airborne aerosol concentration, inhaled dose, room distribution, or safe exposure.

**Required decision:** Identify and bench-validate the exact delivery technology using only the fluid and procedure approved for that module. Keep essential oils on hold until compatibility and human-use safety have been independently reviewed.

### 2.2 Shared tubing/plenum and scent cross-contamination — critical

- The proposed three reservoirs/channels appear to feed a shared fan/outlet or plenum.
- A shared path can retain a previous scent, allow residual liquid/condensation to collect, and cause cross-contamination between Vata, Pitta, and Kapha sessions.
- The current documents do not yet prove that the airflow path prevents backflow or that all wetted parts can be cleaned and dried properly.

**Required verification:** Establish the final fluid/air path, material compatibility, drainage, cleaning method, and a measurable carry-over test. Do not assume three separate reservoirs mean three isolated delivery channels.

### 2.3 MOSFET choice and module switching — critical

- The buy list suggests IRLZ44N-class logic-level MOSFETs and gate resistors/pull-downs, but the actual module interface and current draw must be known first.
- “Logic-level” does not automatically prove that a MOSFET is adequately enhanced at the ESP32's 3.3 V gate drive for the actual load.
- Verify the chosen MOSFET's on-resistance at the relevant gate voltage, current rating under the real thermal conditions, dissipation, wiring, transient behaviour, and gate resistor/pull-down values.
- Confirm whether each nebulizer should be switched at its supply input or controlled using its dedicated signal input. Do not finalize the schematic before this is confirmed.

### 2.4 Four-pin PWM fan versus the proposed driver design — critical

- The list specifies a **four-pin PWM fan** but also allocates one MOSFET channel to the fan in a way that may imply power switching.
- A standard four-wire PWM fan generally expects constant supply power and a separate PWM control input. Its PWM input's electrical requirements may differ from the nebulizer switching circuit.
- A 5 V fan's fourth-wire control interface must be checked against the exact fan datasheet and the ESP32 interface. Do not assume the GPIO can drive it directly or that low-side power switching is equivalent to PWM control.
- Verify fan supply current, startup current, tachometer wiring if used, PWM frequency/interface, and the fail-safe state.

**Required verification:** Select the exact fan and implement the control circuit specified by its datasheet; then test start-up, speed control, loss of controller, and power restoration.

### 2.5 ESP32 pins, boot states, and input circuitry — high

- The proposed pin mapping includes GPIOs that need careful treatment at boot or have special limitations. In particular, GPIOs used for input-only functions cannot drive outputs, and boot-strapping pins can affect startup if external circuits pull them to the wrong level.
- GPIO 34–39 on common ESP32-WROOM modules are input-only; GPIO 35/36 can be used for inputs but must not be treated as general-purpose output pins. Check the exact board and ESP32 variant.
- GPIO5 is a strapping pin on the classic ESP32 family, so a pause button or attached circuit must not prevent reliable boot. Confirm every assigned GPIO against the exact chip/board documentation.
- Inputs such as float switches, leak sensors, and buttons need suitable pull-ups/pull-downs, defined open/closed logic, noise handling, and fault detection. Do not rely on floating inputs.
- Confirm whether any sensor breakout's I²C or signal pins pull up to 5 V. ESP32 GPIOs are not 5 V tolerant; use suitable level shifting or a verified 3.3 V-compatible breakout where required.

**Required verification:** Produce one pin table showing board/chip, pin capabilities, boot constraints, direction, external bias, voltage domain, and safe state at reset.

### 2.6 Emergency stop and safe recovery — critical

- A physical latching emergency stop is proposed in the 5 V actuator supply. Its actual circuit, contact rating, wiring, and scope must be confirmed.
- The emergency stop must remove power from all intended hazardous aroma actuators independently of Raspberry Pi software, ESP32 firmware, and network/USB communication.
- A software stop command or a GPIO that merely reports the E-stop state is not a substitute for an independent power interruption path.
- The design must define what happens after the stop is pressed, after the USB cable is removed, after the ESP32 resets, after a brownout, and when power returns.
- Firmware must boot with all mist outputs disabled, require an explicit new start after a fault/E-stop, and never automatically resume the prior session after recovery.
- Confirm whether the fan should remain powered or be stopped in each fault condition. Do not assume all actuators should have identical shutdown behaviour without a reviewed safety analysis.

**Required verification:** Test E-stop and fault scenarios physically, under supervised bench conditions, and record the observed outputs. Do not use people as test subjects.

### 2.7 Power budget, supplies, grounding, and protection — critical

- The proposed enclosed 5 V, 6–7 A actuator supply is not yet validated by a documented load calculation. Do not size it from a guess.
- Calculate steady-state and startup current for all three nebulizers, fan, indicators, and any other load connected to the actuator rail. Include derating, cable losses, connector ratings, ventilation, and supply safety margins.
- The system appears to require separate supplies for the Pi, aroma actuator rail, display/projector or TV, and powered speaker. That is potentially **four power sources**, not simply “three separate power feeds.” Draw the actual power tree and distinguish mains feeds from low-voltage rails.
- The plan says mains enters the enclosure once, while the display and speaker are described as using their own adapters. Decide whether those adapters remain external or are safely mounted/managed outside the box; document how mains distribution is actually implemented.
- Define fuse locations and ratings, conductor sizes, terminal ratings, strain relief, cable glands, protective earth requirements for Class I devices, isolation, and separation of wet and mains/electronics areas. These must be chosen for the actual components, not filled in by guesswork.
- Share a ground reference between ESP32 and low-voltage drivers only as required by the selected driver topology. Never assume that grounding arrangements are interchangeable across isolated or mains-powered equipment.

**Required verification:** Create a power-tree diagram and a load/protection table before choosing the final supply, fuse, wire, or enclosure.

### 2.8 Liquid leak, overflow, and enclosure design — critical

- The design places liquid reservoirs and mist hardware near electronics. A wet/dry partition is proposed but has not been validated with a physical layout.
- Verify the float-switch orientation and logic, what a disconnected/faulty sensor does, leak-sensor coverage, tray capacity, overflow paths, tube retention, condensation, drainage, cleaning access, and the consequences of a sensor-wire break.
- The enclosure and 3D-printed brackets should not be finalized until the aroma bench establishes the real footprint, airflow, tubing route, maintenance access, and liquid containment.
- Breadboards and loose jumper wires are bench tools, not the final installation method for a liquid-adjacent powered unit.

### 2.9 BME680 and exposure claims — high

- The BME680 measures environmental parameters and gas-related sensor signals; it is **not a calibrated essential-oil aerosol concentration meter** and should not be treated as proof that an exposure is safe.
- Do not use BME680 readings as the sole safety interlock or as evidence of a safe inhaled dose.
- If retained, document exactly what it is intended to detect, how it is calibrated, its limitations, and what action its reading triggers. Safety-critical protections should not depend solely on this sensor.

### 2.10 Aroma timing and human-use safety — critical

- The proposed mist pulse budgets and maximum continuous run time are provisional engineering settings, not validated exposure limits.
- A timed pulse, a mass-loss measurement, and a room-level environmental reading do not independently establish safe exposure for users.
- Essential-oil selection, concentration, duration, room ventilation, user sensitivities, contraindications, and the intended user population need review by suitably qualified domain and safety professionals.
- Until there is an approved protocol and a validated delivery method, restrict testing to controlled bench tests with an approved test fluid. Do not describe the system as therapeutic or clinically effective based solely on a working demo.

### 2.11 Oil mapping and the project proposal — high

- The old component sheet's oil sets (including helichrysum, davana, tea tree, and “tridoshic”) do not clearly match the oils/mappings stated in the proposal documents.
- Do not purchase oils or hard-code mappings based on ambiguous slides, assumptions, or the names of the three doshas.
- Ask the project/domain lead to approve the exact oil, source/specification, intended mapping, and applicable safety constraints. Keep these mappings configurable and versioned rather than embedding unreviewed assumptions in code.

### 2.12 Prakriti questionnaire and scoring — high

- The questionnaire, scoring method, tie handling, dual-dosha/mixed outcomes, and any thresholds need one unambiguous approved specification.
- Ensure the actual questions, answer options, point weights, thresholds, and resulting profile mapping match the approved source. Do not let Cursor invent clinical/Ayurvedic logic to fill gaps.
- Keep the result framed as a project-defined wellness profile unless the assessment and interpretations have been appropriately validated. Do not imply diagnosis or medical advice.

### 2.13 Pi–ESP32 communication, command acknowledgements, and logs — high

- The intended connection is Raspberry Pi to ESP32 over a USB data/serial cable; the phone/tablet should use the Pi's local web interface rather than directly controlling the ESP32.
- Define the command schema, version, valid channel names, bounds, timeouts, acknowledgements, error responses, heartbeat, duplicate-command behaviour, and recovery procedure.
- Logging a command as “sent” is not the same as confirming that the ESP32 accepted it or that the physical output reached the intended state. Record the command, acknowledgement, reported state, fault state, and timestamps where available.
- USB loss, Pi crash, malformed messages, serial reconnection, watchdog expiry, and ESP32 reset must all lead to defined safe behaviour.
- The music/display can be coordinated over the local network if needed, but this should not create a dependency that can prevent the aroma controller from shutting down safely.

### 2.14 Display and projector/TV choice — medium

- Choose projector versus TV only after playing the actual scene in the intended room under realistic lighting and viewing conditions.
- Verify native resolution, brightness, throw distance, focus, aspect ratio, audio-routing expectations, ports, cooling/noise, mounting, and power requirements from the exact model's documentation.
- Treat claims such as “4K supported” separately from native display resolution. Do not use marketing input-resolution claims as proof of native 4K output.
- HDMI display and music playback should be tested separately from aroma hardware before integration.

### 2.15 BOM and purchase-order inconsistencies — high

- The old sheet total of ₹2,27,766 is explicitly not the cost of the connected Phase I unit because it includes items that are on hold or excluded. Do not reuse it as the Phase I project cost.
- The revised list still includes pending choices and quantities that must be tied to verified part numbers. Clearly label each item **BUY NOW**, **BENCH-TEST FIRST**, **WAIT FOR APPROVAL**, **REUSE**, or **OMIT**.
- Do not buy the spare nebulizer until the first exact model passes the agreed bench test. Do not order the final enclosure or printed brackets until the bench layout is confirmed.
- Confirm whether the listed cups, tubing, connectors, leak sensor, float switches, and reservoir material are compatible with the selected delivery method and cleaning procedure.
- The 150 Ω gate resistor and 10 kΩ pull-down values should be treated as proposed values pending the final circuit, not automatically accepted for all devices.
- Check all totals against the editable source sheet and recalculate the connected-unit BOM independently. Identify included taxes, shipping, spares, and items already owned.

### 2.16 Scope, validation, privacy, and claims — high

- Keep Phase I limited to a controlled, local prototype of the three coordinated modules unless the project owner explicitly approves broader scope.
- Distinguish a functional demonstration from user validation, safety validation, scientific validation, and clinical/therapeutic claims.
- Define consent, minimal data collection, storage duration, access controls, local-network security, export/deletion procedures, and whether identifiable user/profile data is retained.
- Music/raga selection and visual scene mapping should be approved by the project/domain lead; confirm media licensing before public demonstration or distribution.

## 3. Required stop/go gates

Do not proceed past a gate until its evidence is recorded.

1. **Part identification:** Exact nebulizer, fan, MOSFET/driver, sensors, and supplies have known model numbers and authoritative datasheets.
2. **Aroma bench test:** One channel operates with the manufacturer-approved test fluid and correct interface; output and failure behaviour are measured and recorded.
3. **Fluid/path decision:** Delivery technology, fluid compatibility, tubing, reservoir, shared/separate airflow path, carry-over, and cleaning are decided.
4. **Electrical design:** Pin map, fan control, MOSFET/driver circuit, power tree, load calculation, protection, grounding, and wet/dry layout are reviewed.
5. **Fail-safe test:** E-stop, sensor fault, leak/low-liquid condition, USB loss, heartbeat timeout, controller reset, and power restoration leave outputs in the defined safe state.
6. **Software contract:** Pi–ESP32 protocol, acknowledgements, logs, bounds, timeout rules, and recovery are specified and tested.
7. **Domain/safety approval:** Questionnaire/scoring, oil mapping, user-use protocol, exposure controls, and all public-facing claims are reviewed by qualified people.
8. **Final purchasing:** Only then finalize quantities, enclosure, projector/TV, and full purchase cost.

## 4. Priority order

1. Resolve the **nebulizer model, fluid compatibility, and control interface**.
2. Resolve the **fan control and exact ESP32 pin map**.
3. Calculate the **power tree, load, protection, and E-stop circuit**.
4. Validate **leak/float sensing and wet/dry enclosure design**.
5. Specify **safe firmware defaults and Pi–ESP32 protocol/recovery**.
6. Resolve **oil mapping, questionnaire scoring, and human-use safety approvals**.
7. Finalize the **BOM and display choice**.

## 5. Prompt for Cursor — verify, do not assume

> You are reviewing the Prakriti Ayursensora project. Read this entire document and inspect all relevant repository files, including the problem statement, proposal slides, architecture documents, component sheets, source code, ESP32 firmware, pin maps, wiring diagrams, and purchase list.
>
> Your task is to **verify each concern against the actual project artefacts and authoritative manufacturer documentation**. Do not simply rewrite this document, agree with it, or assume that a concern is proven. For each item, classify it as:
>
> - `CONFIRMED ISSUE` — evidence shows the current design is wrong or inconsistent.
> - `UNVERIFIED / BLOCKING` — necessary evidence/specification is missing.
> - `NOT AN ISSUE` — evidence supports the current design; explain why.
> - `PARTIALLY RESOLVED` — some evidence exists, but a specific gap remains.
>
> For every finding, provide: the exact file/section/code/pin/component involved; the evidence; the risk/impact; a recommended next action; and what must be verified before marking it resolved. Cite exact local file paths and line numbers where possible, and provide manufacturer-document links for product-specific claims. Clearly separate facts from inferences. Never invent a model number, rating, safe exposure limit, Ayurvedic mapping, or clinical claim.
>
> Give priority to the aroma module/fluid/control interface, four-wire fan interface, ESP32 pin/boot behaviour, MOSFET drive, supply sizing, mains and low-voltage power architecture, physical E-stop, sensor-fault behaviour, fluid containment/cross-contamination, and recovery after power/USB loss. Inspect the existing implementation to determine whether outputs default off and whether a previous session could resume automatically.
>
> Then produce a concise engineering review with (1) overall go/no-go verdict, (2) prioritised issue register, (3) verified pin and wiring table only where sufficient evidence exists, (4) power/load calculation with cited component ratings or a clear list of missing inputs, (5) failure-mode test matrix, and (6) BOM status table using `BUY NOW`, `BENCH-TEST FIRST`, `WAIT FOR APPROVAL`, `REUSE`, or `OMIT`.
>
> Do not alter the code, finalize a circuit, recommend human testing, or mark the system safe merely because the demo runs. If essential information is missing, list the exact question and the authoritative source needed to resolve it. Do not change the project scope without explaining the reason and asking for the relevant owner decision.

## 6. Final engineering position

The three-module architecture can remain the working concept, but the project is **not ready for a complete purchase freeze or human-use deployment**. The first priority is to prove that the selected aroma hardware can be controlled exactly as intended with a manufacturer-approved fluid and known electrical interface. Power design and independent fail-safe shutdown must then be verified. All unresolved points should remain visible as blockers until supported by evidence.
