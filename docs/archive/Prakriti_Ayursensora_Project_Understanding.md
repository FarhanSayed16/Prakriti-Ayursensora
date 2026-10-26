# Prakriti Ayursensora
## Problem Statement, Project Understanding, Requirements, and Initial System Definition

> **Document status:** Initial project understanding and requirements baseline  
> **Project:** Prakriti Ayursensora — Smart Aroma & Healing Environment System  
> **Institutional context:** K. J. Somaiya Institute of Management, with interdisciplinary involvement referenced in the project presentation  
> **Phase covered:** Initial prototype using manually collected Prakriti questionnaire data  
> **Important:** This document separates information stated in the supplied project material from proposed engineering decisions and items that still require confirmation.

---

## 1. Executive Summary

Prakriti Ayursensora is a proposed personalized sensory-environment system inspired by Ayurvedic concepts of Prakriti and the three doshas: Vata, Pitta, and Kapha. The project presentation describes a system that coordinates three sensory outputs:

1. **Aroma therapy** — release a selected fragrance or essential oil.
2. **Visual environment** — display nature-related scenery through a projector or display.
3. **Music melody** — play selected music, including Indian classical ragas associated with dosha categories in the presentation.

The intended environments include premium hospital rooms, doctors' cabins, wellness centres, spas, hotels, and corporate settings. The broader proposal also discusses AI-based assessment, physiological feedback, clinical evaluation, and adaptive personalization.

For the initial phase described by the project owner, participant traits will be collected manually using a questionnaire. The system will then determine a Prakriti profile and use it to configure the three output modules. The project owner is also considering mixed or dual-dosha profiles rather than forcing every participant into a single category.

The immediate engineering challenge is to turn the concept into a reliable, coordinated, and safe IoT prototype. This requires an agreed assessment method, explicit profile-to-output rules, a controller architecture, module communication, power design, session control, error handling, and verification.

**This system should initially be described as an Ayurvedic-principle-inspired wellness prototype, not as a validated diagnostic or treatment device.** Therapeutic benefits and classification accuracy must not be claimed unless demonstrated through appropriate validation.

---

## 2. Source Material Reviewed

This initial understanding is based on the supplied materials:

- **Aroma healing project PDF:** “AI-Enhanced Personalized Healing Environments Using Ayurvedic Principles,” titled *Prakriti Ayursensora — Smart Aroma & Healing Environment System*.
- **Nebulizer project presentation:** includes the concept, target users, dosha framework, proposed sensory mappings, methodology, institutional context, and master component list.
- **Photographs of the component budget/list:** hardware and consumable entries numbered 1–47, including controllers, aroma components, sensors, output devices, enclosure items, consumables, and overheads.
- **Existing website questionnaire:** questions about physical characteristics, habits, behaviour, emotions, and preferences, with three answer options for most questions.

This document does not assume that every listed component has been purchased, assembled, tested, or approved for the final design. The exact current state of each component should be inventoried separately.

---

## 3. Problem Statement

### 3.1 Draft problem statement

People in hospital rooms, wellness centres, spas, hotels, and demanding work environments may benefit from a more personalized and comfortable sensory environment. A conventional room generally provides limited coordination between environmental visuals, fragrance, and music based on an individual's preferences or profile.

Prakriti Ayursensora proposes to develop an integrated system inspired by Ayurvedic Prakriti concepts. It will use participant information collected through a questionnaire to generate a profile and configure three coordinated sensory modules: aroma diffusion, visual scenery, and music playback.

The engineering problem is to design and implement a safe, modular, and maintainable IoT system that can receive a participant profile, apply a transparent and expert-reviewed mapping policy, coordinate the three outputs, allow users to control or stop a session, and record relevant operating status. The initial version will rely on manually collected questionnaire responses; advanced physiological adaptation and AI-based inference are future possibilities rather than assumed initial capabilities.

The project must also address uncertainty in the questionnaire's scoring method, the handling of mixed dosha profiles, essential-oil compatibility and exposure, electrical safety, data privacy, and validation of any wellness claims.

### 3.2 One-sentence problem statement

**Design and prototype an IoT-enabled personalized sensory environment that translates a participant's expert-reviewed Prakriti profile into coordinated aroma, nature-visual, and music outputs, with user control, safety limits, and measurable operational verification.**

### 3.3 Problem boundaries

The first phase should aim to demonstrate that the complete technical workflow functions correctly. It should not claim that the device diagnoses a medical condition, independently determines a scientifically proven constitution, or treats stress, anxiety, depression, or another disease.

---

## 4. Project Vision and Objectives

### 4.1 Vision

To create a modular smart wellness environment that combines an Ayurvedic-inspired participant profile with coordinated sensory outputs, while leaving room for later research into biofeedback, personalization, and clinical outcomes.

### 4.2 Initial objectives

1. Capture participant responses through the existing questionnaire or an agreed data-import process.
2. Establish an explicit, auditable rule for converting responses into Vata, Pitta, and Kapha scores.
3. Support a mixed or dual-dosha result if the project's Ayurveda expert approves the classification rules.
4. Convert the profile into a structured session configuration.
5. Control aroma-dispensing hardware with defined limits and a reliable stop mechanism.
6. Play the selected nature scene through the projector or other approved display path.
7. Play a selected track through the audio system.
8. Coordinate the modules so they operate as one session.
9. Report device state, faults, and session completion.
10. Provide a clear user interface for starting, stopping, and, where appropriate, adjusting a session.
11. Keep participant data and session records private and access-controlled.
12. Define test cases and evidence for functional reliability before any broader evaluation.

### 4.3 Longer-term objectives stated or implied by the presentation

The presentation also discusses:

- AI-assisted Prakriti assessment.
- Biofeedback and wearable-sensor integration.
- Dynamic adaptation using physiological measurements such as heart-rate variability.
- Assessment of emotional regulation, recovery, and participant satisfaction.
- Clinical deployment and outcome evaluation.
- Potential use in healthcare, wellness, and corporate environments.

These are broader research and development objectives. They should be placed on a later roadmap and not treated as completed capabilities of the first prototype.

---

## 5. Conceptual Foundation: Prakriti and Tri-Dosha

The supplied presentation uses the Ayurvedic concept of Prakriti and the categories Vata, Pitta, and Kapha. It relates these categories to physical, physiological, and psychological characteristics and proposes different sensory experiences for each category.

### 5.1 Terminology used in the supplied presentation

The presentation uses spellings such as **Vataj**, **Pittaj**, and **Kaphaj** in several places. This document uses **Vata**, **Pitta**, and **Kapha** for readability, while retaining the original terms when describing the presentation's mappings.

### 5.2 Proposed sensory mappings from the presentation

The following mappings are transcribed from the project presentation. They are project proposals, not independently verified clinical prescriptions.

| Profile label in presentation | Visual environment | Aroma examples | Music/raga examples |
|---|---|---|---|
| Vataj / Vata | Lush forest | Sandalwood, cinnamon, frankincense | Bhimpalasi, Bageshree, Yaman, Kafi |
| Pittaj / Pitta | Mountains and water bodies | Rose, jasmine, lavender, mint | Miyan ki Todi, Malkauns, Marwa, Durga |
| Kaphaj / Kapha | Flowing water and meadows | Rosemary, eucalyptus, thyme, peppermint | Bhairav, Jaunpuri, Hamsadhwani, Desh |

**Important source limitation:** the supplied presentation's diagram and extracted text do not consistently express the elemental associations. The elemental framework should therefore be checked against an agreed Ayurvedic reference and reviewed by the project's qualified Ayurveda expert before it is encoded in software. Do not silently use the component mapping table as a substitute for that review.

### 5.3 How to represent a mixed profile

The project owner intends to support people whose profile is a combination of two doshas. The system should not automatically force every participant into one label.

A suitable data representation could preserve all three scores, for example:

- Vata score: 0.48
- Pitta score: 0.39
- Kapha score: 0.13
- Possible profile label: Vata–Pitta dominant, **only if an approved classification rule permits this result**

These values are illustrative only and are not derived from the existing questionnaire.

Before implementation, the project team must define:
- The answer-to-dosha mapping for every question.
- Whether questions have equal or different weights.
- How unanswered or invalid answers are handled.
- The minimum data required to produce a result.
- The thresholds for single-dosha, dual-dosha, and balanced classifications.
- How the Ayurveda expert will review and approve the output.
- Whether a participant can review or correct their submitted information.

A transparent rule-based method is the recommended starting point. A machine-learning model should only be considered when an adequately sized, appropriately consented, and reliably labelled dataset is available and a defensible evaluation method has been agreed.

---

## 6. Existing Questionnaire: Current Understanding and Gaps

The website questionnaire presents three choices for most questions. The supplied content covers the following areas.

| Domain | Examples from the existing questionnaire |
|---|---|
| Skin and appearance | Skin colour and texture, hair type and texture, hair fall, eyes, lips, teeth |
| Body structure | Physique, veins, hands, joints, chest, body-frame length and breadth |
| Body function and habits | Temperature, appetite, hunger, thirst, liquids, sweating, sleep, weight change |
| Behaviour and communication | Speaking style, voice, walk, activity, decisions, patience |
| Psychological characteristics | Emotion, intellect, grasping, concentration, memory, temperament |
| Lifestyle and preferences | Climate, tastes, food preference, relationships, likings, comfort and challenges |
| Other self-reported traits | Dreams, self-control, gratefulness, faith, response to praise, nail biting, ego |

### 6.1 What the questionnaire currently establishes

It establishes a way to collect self-reported or participant-provided characteristics. It does not, by itself, establish that the resulting classification is accurate, clinically meaningful, or reproducible.

### 6.2 Required review

The questionnaire should be reviewed for:

- Expert-confirmed mapping of each answer to a dosha.
- Ambiguous or overlapping options.
- Repeated or potentially redundant questions.
- Cultural, language, and accessibility considerations.
- Questions that may be sensitive, subjective, or unsuitable for a given setting.
- A clear explanation to participants of why the information is collected.
- Consent, secure storage, retention, and deletion procedures.
- A consistent scoring and result-generation method.
- Validation against assessments conducted by qualified practitioners, if the project intends to evaluate classification performance.

Do not label the output as an AI prediction unless an actual model is implemented and evaluated. For the initial phase, “questionnaire-based Prakriti assessment” is a more accurate description.

---

## 7. System Scope

### 7.1 In scope for Phase I

- Questionnaire response intake.
- Participant/session identifier.
- Rule-based assessment engine, subject to expert approval.
- Support for approved single- or mixed-profile categories.
- Profile-to-output configuration.
- Aroma module control.
- Nature-scene playback.
- Music playback.
- Inter-module communication.
- Start, stop, and emergency-stop behaviour.
- Local operating status and error handling.
- Basic session logging.
- Hardware integration and functional testing.
- Documentation of setup, configuration, limitations, and maintenance.

### 7.2 Not assumed to be in scope for Phase I

- Automated Prakriti inference from facial images or fingerprints.
- Diagnosing a medical or psychological condition.
- Automatically changing treatment or prescribing oils.
- Claiming that pulse, SpO₂, or another single sensor determines Prakriti.
- Clinical proof that a particular combination treats stress, anxiety, depression, or another condition.
- Fully autonomous adaptation based on physiological feedback.
- Production-ready deployment across hospital wards.

These may be considered later only after feasibility, safety, ethics, validation, and scope have been reviewed.

---

## 8. Proposed High-Level Architecture

The following is a proposed architecture for discussion, not a description of an already completed implementation.

```text
                   PARTICIPANT / OPERATOR
                            |
                            v
                Questionnaire / Session UI
                            |
                            v
                Participant Profile Storage
                            |
                            v
                Prakriti Assessment Engine
                - Input validation
                - Approved scoring rules
                - Profile classification
                            |
                            v
                Session Configuration Builder
                - Aroma selection
                - Visual scene selection
                - Music selection
                - Duration / safety constraints
                            |
                            v
                  CENTRAL COORDINATION
                 Raspberry Pi 5 (proposed)
                    /       |       \
                   /        |        \
                  v         v         v
             Aroma MCU   Display /   Audio
             (ESP32)     Projector   Playback
                  |         |         |
                  v         v         v
             Nebulizer   Nature      Speaker
             / Fan /     scene
             Relay
                    \       |       /
                     \      |      /
                      v     v     v
                  Status / Fault Monitoring
                            |
                            v
                     Session Log / UI
```

### 8.1 Controller responsibilities: proposal

**Raspberry Pi 5**
- Coordinate the session.
- Receive the approved profile/configuration.
- Manage the display playback path.
- Coordinate music playback if the selected audio interface supports it.
- Maintain session state and application logs.
- Provide an integration point for future assessment software.

**ESP32**
- Control local actuators through suitable driver circuits.
- Read any approved local sensors.
- Enforce local operating limits and timeouts.
- Return status and fault information.
- Fail safely if central communication is lost.

**Arduino Uno R4 WiFi**
- Retain only if a specific, documented requirement justifies an additional controller.
- Avoid duplicate control logic when the Raspberry Pi and ESP32 can fulfil the same requirements.

**Display and audio devices**
- The projector requires a compatible playback source. An ESP32 alone should not be assumed to render and play full landscape video.
- The 0.96-inch OLED modules are suitable for small status information, not full scenery.
- The speaker needs a defined audio source and a tested connection path.

The final controller allocation must be confirmed after checking the actual board variants, interfaces, available ports, power requirements, and software compatibility.

---

## 9. Hardware Inventory: Components Identified in the Supplied List

The photographed master list contains items numbered 1–47. The table below summarises the identifiable items by function. It is not a purchase verification or final bill of materials.

### 9.1 Main processing and control

| Component | Listed quantity | Intended role / notes |
|---|---:|---|
| Raspberry Pi 5, 8 GB RAM | 1 | Main processing hub |
| Arduino Uno R4 WiFi | 1 | Microcontroller; exact assigned responsibilities need confirmation |
| L298N motor driver module | 1 | Listed under control; actual need and load compatibility should be checked |
| ESP32 Wi-Fi + BLE module with 5-inch touchscreen | 2 | Wireless interface / local UI; exact board model needs confirmation |
| ESP32 development board, 38-pin CP2102, Type-C | 1 | Wireless development/control |
| SanDisk Extreme PRO microSDXC, 32 GB | 2 | Storage |

### 9.2 Aroma diffusion system

| Component | Listed quantity | Intended role / notes |
|---|---:|---|
| Ultrasonic nebulizer module, ACEBOTT, 2.4 MHz | 3 | Aroma/mist generation; fluid compatibility must be confirmed |
| 5 V DC brushless fan, 80 mm | 3 | Mist dispersal |
| 5 V four-channel opto-isolated relay module | 2 | Switching; verify coil/logic requirements and actual load ratings |
| Helichrysum essential oil, 15 ml | 10 | Listed aroma consumable |
| Davana essential oil | 10 | Listed aroma consumable |
| Tea tree essential oil, 15 ml | 10 | Listed aroma consumable |
| Tridoshic balancing oil | 10 | Listed aroma consumable; composition and suitability need review |
| Glass essential-oil bottles, 30 ml | 9 | Storage |

The list suggests three nebulizers, three fans, and multiple oil types, but it does not fully specify whether each nebulizer is dedicated to a separate oil, how the oils are routed, or how cleaning and cross-contamination are handled.

### 9.3 Biometric and user input

| Component | Listed quantity | Intended role / notes |
|---|---:|---|
| AS608 optical fingerprint scanner | 1 | Participant identification; optional for Phase I |
| Arducam IMX219 wide-angle camera module | 1 | Expression analysis is suggested; optional and privacy-sensitive |
| 7-inch capacitive touchscreen, HDMI + USB | 1 | User interface; connection path needs confirmation |

Fingerprint and facial-image collection should not be enabled by default. A session ID or QR code may be sufficient for the first prototype.

### 9.4 Power system

| Component | Listed quantity | Intended role / notes |
|---|---:|---|
| 5 V / 10 A SMPS | 1 | Mains-powered supply |
| Stuffcool Giga Max 25,000 mAh, 100 W power bank | 1 | Backup/portable power |
| 18650 Li-ion cells, 3.7 V, 3000 mAh | 4 | Battery cells; configuration and protection are unspecified |
| 5 V, 3 A buck-down modules | 2 | Voltage regulation |

The power list should be redesigned from a load calculation rather than assembled as-is. A Pi, projector, touchscreen, fans, and other peripherals may have different voltage/current needs. Battery charging, cell protection, fusing, thermal limits, isolation, and grounding require explicit engineering.

### 9.5 Visual and audio output

| Component | Listed quantity | Intended role / notes |
|---|---:|---|
| Portronics Beam 470 smart LED projector, 1080p | 1 | Main scenery output |
| Bluetooth / AUX speaker, 10 W | 1 | Audio output |
| WS2812B addressable RGB LED strip, 5 V, 5 m | 2 | Ambient lighting |
| OLED display module, 0.96-inch SSD1306, I²C | 3 | Small status display |

The projector playback source, audio routing, file formats, local storage, and connection recovery behaviour must be decided during detailed design.

### 9.6 Sensing and feedback

| Component | Listed quantity | Intended role / notes |
|---|---:|---|
| MAX30100 pulse oximeter and heart-rate module | 1 | Physiological measurement |
| BME680 air-quality sensor | 2 | Environmental sensing |
| GSR / skin-conductance sensor | 2 | Physiological signal |
| MAX30102 heart-rate + SpO₂ module | 2 | Physiological measurement |

The two MAX30100/MAX30102 entries overlap in purpose. The project team should decide whether both are required and what validated use each serves. BME680 and GSR readings should not be interpreted as proof of Prakriti or treatment efficacy without a defined, validated method.

### 9.7 Enclosure, assembly, and consumables

| Component | Listed quantity | Intended role / notes |
|---|---:|---|
| Custom laser-cut acrylic enclosure, 300 × 200 × 150 mm | 1 | Ward/device housing |
| 3D-printed brackets, mounts, fan shroud | 10 | Mechanical assembly |
| ABS project box for Arduino + relay sub-unit | 1 | Sub-enclosure |
| Heat sinks + thermal paste for Pi 5 cooling | 2 | Thermal management |
| Jumper wires, assorted 40-piece sets | 3 | Prototyping |
| Full-size 830-point breadboard | 2 | Prototyping |
| Custom 2-layer PCB, 10 × 10 cm, 5 pcs | 1 | Electronics assembly |
| Soldering kit | 1 | Assembly tools |
| USB cables: Type-C, Micro-USB, HDMI | 1 listed set | Cabling |
| 8-port 100 Mbps network switch | 1 | Networking |
| Data collection and consent forms, 300 copies | 300 | Documentation |
| Stationery, labels, admin material | 1 listed set | Documentation |

Additional budget entries include software licences/cloud API services, transport/logistics, publication/conference charges, contingency, a treadmill, and a student-coordinator scheme. These are listed in the supplied budget material but are not all direct components of the three-module IoT prototype. Their project justification and scope should be confirmed separately.

---

## 10. Proposed Software Responsibilities

The software should be divided into understandable modules so that changes to the questionnaire do not require rewriting the hardware control code.

### 10.1 Questionnaire and profile service

Responsibilities:
- Accept and validate responses.
- Associate responses with a participant/session identifier.
- Apply approved scoring rules.
- Store the result and the version of the rules used.
- Handle incomplete submissions.
- Allow an authorised reviewer to inspect the result where required.

### 10.2 Assessment engine

Responsibilities:
- Convert valid answers into Vata, Pitta, and Kapha scores.
- Apply expert-approved weights and classification thresholds.
- Support approved mixed-profile results.
- Return scores, a profile label, confidence/quality indicators only if justified, and reasons for incomplete results.
- Avoid generating unsupported medical conclusions.

### 10.3 Session configuration service

Responsibilities:
- Map the approved profile to permitted aroma, scene, and audio identifiers.
- Validate that all selected assets and devices are available.
- Apply session duration and other safety limits.
- Store a record of the configuration used for a session.

### 10.4 Device-control layer

Responsibilities:
- Send commands to the local controller.
- Validate command format and permitted values.
- Track acknowledgements and device state.
- Use timeouts and safe failure behaviour.
- Report faults and support session termination.

### 10.5 Operator interface

Responsibilities:
- Show participant/session identity in a privacy-conscious manner.
- Display the approved profile and selected outputs.
- Let an authorised user start or stop a session.
- Show which modules are ready, active, or in error.
- Provide a clear emergency-stop mechanism.
- Avoid displaying unnecessary sensitive participant information.

### 10.6 Logging

Log operational facts such as:
- Session ID.
- Assessment-rule version.
- Configuration ID.
- Start and stop timestamps.
- Module acknowledgements.
- Faults and recovery actions.
- Whether the session ended normally or was stopped.

Do not collect more personal data than necessary. Physiological data, if collected, should have a documented purpose, access policy, retention period, and consent basis.

---

## 11. Data Model: Illustrative Starting Point

A simple structured format can help separate a person's assessment from the hardware commands. The following example is illustrative and does not encode a validated Ayurvedic scoring method.

```json
{
  "participant_id": "participant_demo_001",
  "assessment": {
    "method": "questionnaire",
    "rule_version": "pending-expert-approval",
    "scores": {
      "vata": null,
      "pitta": null,
      "kapha": null
    },
    "profile_label": null,
    "review_status": "pending"
  },
  "session": {
    "session_id": "session_demo_001",
    "aroma_profile_id": null,
    "visual_scene_id": null,
    "music_track_id": null,
    "duration_seconds": null,
    "status": "not_started"
  }
}
```

The `null` values are deliberate placeholders. They must not be treated as zero scores or as an actual assessment result.

A separate device command could contain a session ID and approved asset IDs rather than raw questionnaire responses. This reduces the amount of personal information that needs to reach the microcontroller.

---

## 12. Communication and Integration Design

The project needs a defined protocol between the central coordinator and local controller(s). The final protocol should be selected after confirming the hardware and whether the system must operate entirely on a local network.

### 12.1 Candidate approaches

- **USB serial:** useful for an early bench prototype connecting a Raspberry Pi to an ESP32.
- **Local Wi-Fi with HTTP or WebSocket:** useful if devices need to communicate over a local network and provide status updates.
- **MQTT:** useful for a multi-device system with clear topics, acknowledgements, retained state where appropriate, and reconnection behaviour.

These are alternatives, not a requirement to use all three. For an early prototype, USB serial may be simpler. MQTT or another network protocol can be introduced if the final deployment needs distributed communication.

### 12.2 Example command concept

A command might conceptually contain:

```json
{
  "session_id": "session_demo_001",
  "command": "START_SESSION",
  "aroma_profile_id": "approved_aroma_profile",
  "visual_scene_id": "forest_scene_01",
  "music_track_id": "approved_track_01",
  "duration_seconds": 600
}
```

This is an illustrative message structure, not a final protocol. The controller should reject unknown commands, unsupported asset IDs, invalid durations, and unsafe operating states.

### 12.3 Required behaviours

- The central controller can tell whether each module is ready.
- Commands have a clear acknowledgement or failure response.
- Repeated commands do not accidentally trigger repeated dispensing.
- A disconnected device enters a safe state.
- A stop request takes priority over normal operation.
- The system records whether the requested session actually started and stopped.
- The session does not automatically resume after a power interruption without an explicit, safe recovery rule.

---

## 13. Safety, Privacy, and Ethical Considerations

This project is intended for possible use in healthcare and wellness environments. Safety and privacy should be requirements from the beginning.

### 13.1 Aroma safety

- Confirm the nebulizer's approved fluid type and material compatibility.
- Obtain clinical and institutional review before use around patients.
- Define permitted oils, concentrations if applicable, exposure duration, ventilation, cleaning, and maintenance.
- Consider allergies, asthma, fragrance sensitivity, and other relevant contraindications through an approved screening process.
- Provide an immediate stop control.
- Prevent unintended dispensing at startup, during a crash, or after a communication failure.
- Prevent cross-contamination between fragrance channels.
- Do not describe an oil as medically appropriate for an individual solely because of a dosha label.

### 13.2 Electrical and mechanical safety

- Calculate load and current requirements for each power rail.
- Use appropriately rated supplies, wiring, protection, connectors, and drivers.
- Isolate mains-powered circuitry from low-voltage user-accessible circuitry as required by applicable standards.
- Protect batteries against overcurrent, overcharge, undervoltage, and overheating.
- Provide ventilation and thermal management.
- Secure the projector, enclosure, and moving or hot components.
- Test all stop and failure states before any user-facing demonstration.

### 13.3 Privacy and consent

- Explain what data is collected and why.
- Use a participant/session ID where possible.
- Collect biometric, facial, and physiological data only if necessary and approved.
- Restrict access to identifiable records.
- Define retention and deletion procedures.
- Avoid transmitting raw questionnaire data to devices that do not need it.
- Obtain the required institutional approvals before collecting research data from patients or conducting clinical evaluation.

### 13.4 Claims and intended use

The prototype may demonstrate profile-based sensory configuration and reliable device control. Claims that it reduces stress, anxiety, depression, or recovery time require appropriate evidence and study design. It should not replace clinical assessment or treatment.

---

## 14. Functional Requirements

The following requirements are proposed and should be assigned IDs in the project tracker.

| ID | Requirement | Initial acceptance evidence |
|---|---|---|
| FR-01 | Accept a valid questionnaire submission | A test submission is stored and retrievable |
| FR-02 | Reject or flag incomplete/invalid input | Invalid submissions produce a clear error |
| FR-03 | Generate a profile using the approved rules | A set of test cases produces expected outputs |
| FR-04 | Preserve the three dosha scores | Stored result contains all required scores |
| FR-05 | Support mixed profiles if approved | Expert-approved mixed-profile test cases pass |
| FR-06 | Generate an output configuration | Valid profile maps to approved asset IDs |
| FR-07 | Start the aroma module safely | Correct channel activates within defined limits |
| FR-08 | Stop aroma output on request | Stop request disables output within the specified response time |
| FR-09 | Play the selected nature scene | Correct scene is displayed through the selected playback path |
| FR-10 | Play the selected music | Correct track plays through the selected audio path |
| FR-11 | Coordinate the three modules | Module states and session IDs remain consistent |
| FR-12 | Report module faults | Disconnection or error is visible to the operator |
| FR-13 | Handle communication loss safely | Actuators enter the defined safe state |
| FR-14 | Record session events | Start, stop, and fault events are logged |
| FR-15 | Prevent unauthorised access to participant data | Access-control tests pass |

Numerical limits, timing thresholds, and clinical acceptance criteria should be agreed and documented rather than guessed.

---

## 15. Non-Functional Requirements

### Reliability
The system should behave predictably across repeated sessions, recover cleanly from restart, and avoid uncontrolled actuator operation.

### Safety
Aroma output and electrical hardware must have clearly defined safe states, limits, and stop behaviour.

### Modularity
The aroma, visual, and audio modules should be testable independently and replaceable without redesigning the entire application.

### Maintainability
Configuration mappings, media assets, device settings, and scoring rules should be versioned and documented.

### Usability
The operator should be able to identify the participant/session, see the selected outputs, start a session, and stop it without navigating complicated technical controls.

### Privacy
Only necessary data should be collected, and access to identifiable information should be restricted.

### Observability
The system should expose module status, errors, command acknowledgements, and session history.

### Extensibility
The architecture should leave room for later sensor integration and adaptive behaviour without making these dependencies mandatory for Phase I.

---

## 16. Testing and Validation Plan

Testing should proceed from small, isolated checks to full-system operation.

### Stage 1 — Requirements and bench checks
- Verify the exact model and electrical specifications of each component.
- Check all power rails and current requirements.
- Confirm the projector's supported inputs and playback method.
- Confirm nebulizer compatibility with the intended liquids.
- Confirm controller interfaces and firmware compatibility.

### Stage 2 — Independent module testing
- Test each nebulizer and fan separately.
- Verify relay/driver logic and safe default states.
- Test scene playback without the aroma module.
- Test audio playback without the other modules.
- Test the UI and session controls.
- Verify sensor readings against appropriate references if sensors are retained.

### Stage 3 — Assessment-engine testing
- Create test cases with known expected scores approved by the Ayurveda expert.
- Test incomplete, contradictory, and invalid answers.
- Test single, dual, and balanced outcomes where defined.
- Confirm that changes to a scoring-rule version are traceable.
- Do not use invented sample scores as evidence of clinical accuracy.

### Stage 4 — Integration testing
- Run the complete profile-to-output workflow.
- Confirm that each device receives the correct configuration.
- Test stop requests during normal operation.
- Disconnect network/serial communication during a session.
- Restart the Pi or controller and confirm safe recovery.
- Verify that logs match the actual hardware behaviour.

### Stage 5 — Controlled usability and safety evaluation
- Conduct supervised tests under the appropriate institutional process.
- Assess usability, comfort, reliability, and adverse reactions using approved procedures.
- Keep engineering acceptance tests separate from clinical outcome studies.
- Do not claim therapeutic efficacy based solely on a working demonstration.

---

## 17. Initial Risks and Mitigations

| Risk | Potential impact | Proposed mitigation |
|---|---|---|
| Inconsistent dosha rules | Unreliable or disputed classification | Obtain expert approval and version the rules |
| Unsupported dual-dosha logic | Arbitrary output selection | Define thresholds and mixed-profile policy before coding |
| Incompatible nebulizer/fluid combination | Hardware damage or unsafe exposure | Confirm specifications and obtain safety review |
| Excessive or unintended aroma output | Discomfort or health risk | Use local limits, timeouts, stop controls, and supervision |
| Controller responsibilities overlap | Integration complexity | Assign one clear owner for each function |
| Power supply undersized or poorly protected | Resets, overheating, or damage | Calculate loads and design protected power rails |
| Projector cannot be controlled as expected | Display module fails to integrate | Test the exact input/playback path early |
| Network or controller failure | Inconsistent session or uncontrolled output | Local fail-safe logic and explicit recovery behaviour |
| Sensitive data exposed | Privacy harm | Minimise data and apply access controls |
| Wellness benefits overstated | Misleading claims | Separate functional demonstration from clinical validation |
| Too many features in the first phase | Delays and integration failure | Deliver a narrow, end-to-end prototype first |

---

## 18. Recommended Development Phases

### Phase 0 — Requirement clarification
- Confirm the exact project objective and first-phase boundary.
- Review the questionnaire with the Ayurveda expert.
- Approve the scoring method and dual-dosha policy.
- Confirm which hardware has actually been procured and tested.
- Document intended use, safety requirements, and institutional constraints.

**Deliverable:** approved requirements and decision log.

### Phase 1 — Basic hardware proof of concept
- Establish the Pi and ESP32 communication path.
- Test one aroma channel with a safe, approved test setup.
- Play one nature scene through the projector.
- Play one audio track through the speaker.
- Implement a global stop mechanism.

**Deliverable:** each module can be operated and stopped reliably.

### Phase 2 — End-to-end profile-driven session
- Accept a questionnaire result.
- Generate a configuration using approved rules.
- Send the configuration to the three modules.
- Record session status and faults.
- Demonstrate a complete session using test participants/data approved for development.

**Deliverable:** a working profile-to-sensory-output prototype.

### Phase 3 — Multi-profile support and reliability
- Add approved single- and dual-dosha rules.
- Add operator controls and module readiness indicators.
- Test restarts, communication failures, invalid inputs, and stop behaviour.
- Document maintenance and setup procedures.

**Deliverable:** repeatable integrated prototype with documented limitations.

### Phase 4 — Optional sensing and adaptive features
- Review whether physiological and environmental sensors serve a defined purpose.
- Establish data-quality requirements and validation plans.
- Add feedback logic only after the intended interpretation and safety boundaries are approved.

**Deliverable:** an evaluated extension, not an assumed requirement for the initial build.

### Phase 5 — Research and clinical evaluation
- Obtain applicable institutional/ethics approvals.
- Establish a study protocol, outcomes, consent process, and analysis plan.
- Evaluate usability and any proposed wellness outcomes using appropriate methods.

**Deliverable:** research evidence with clearly stated limitations.

---

## 19. Key Decisions Required Before Detailed Implementation

These questions should be resolved with the project supervisor and relevant domain experts.

### Ayurveda and assessment
1. Who is the final authority for approving the questionnaire and scoring model?
2. Is the existing website's scoring logic implemented, and can its code be reviewed?
3. How should dual-dosha profiles be calculated and labelled?
4. Should the system report scores only, a profile label, or both?
5. What reference material will govern the traditional concepts and sensory mappings?

### Hardware and control
6. Which components have already been purchased and tested?
7. What exact models are the two ESP32 touchscreen boards?
8. What is the intended role of the Arduino Uno R4 WiFi?
9. Which device will render and play the projector's video?
10. How will audio playback be initiated and controlled?
11. Are the three nebulizers designed for the intended fragrance materials?
12. What are the power ratings and interfaces of every output device?

### Software and data
13. Where will questionnaire responses and profile records be stored?
14. Is the existing website code available, and what technology stack does it use?
15. Will the device run entirely on a local network or depend on internet access?
16. What session information must be retained, and for how long?
17. Is fingerprint or camera-based identification genuinely required?

### Safety and evaluation
18. What fragrance operating limits and stop requirements will be approved?
19. What institutional permissions are needed before any patient-facing trial?
20. What evidence will be required to declare the first phase complete?

These are unresolved requirements, not reasons to stop progress. They can be tracked and resolved in a structured sequence.

---

## 20. Initial Definition of Done

The first phase should be considered technically demonstrated only when all applicable points below are satisfied:

- [ ] The approved questionnaire can be submitted and validated.
- [ ] The approved assessment rules produce reproducible results on test cases.
- [ ] Mixed-profile handling is implemented if approved.
- [ ] A profile can be converted into a valid session configuration.
- [ ] The aroma module operates within defined limits and stops on request.
- [ ] The correct visual scene can be played.
- [ ] The correct music can be played.
- [ ] All three outputs can be coordinated in one session.
- [ ] Faults and communication failures are handled safely.
- [ ] Session events are logged.
- [ ] Participant data is handled according to the agreed privacy policy.
- [ ] The hardware wiring, software setup, limitations, and maintenance process are documented.
- [ ] Demonstration evidence is collected without making unsupported medical claims.

---

## 21. Glossary

| Term | Meaning in this project |
|---|---|
| Prakriti | Ayurvedic concept of an individual's constitution |
| Dosha | One of the three Ayurvedic categories used by the project: Vata, Pitta, Kapha |
| Dual-dosha profile | A result representing two dominant categories under an approved classification rule |
| Sensory output | Aroma, visual scenery, or music |
| IoT controller | Embedded device responsible for controlling hardware and communicating status |
| Assessment engine | Software that converts questionnaire responses into an approved profile |
| Session configuration | The set of output selections and operating limits for one session |
| Biofeedback | Use of measured physiological signals to inform feedback or adaptation |
| Fail-safe state | A defined state that avoids hazardous or uncontrolled operation after a fault |
| Functional validation | Testing that the system performs its specified functions |
| Clinical validation | Evaluation of claims about health or clinical outcomes using an appropriate study design |

---

## 22. Final Project Understanding

Prakriti Ayursensora is a proposed Ayurvedic-principle-inspired personalized wellness environment. The initial system will receive manually collected questionnaire information, apply an expert-reviewed assessment method, and use the resulting profile to configure three coordinated outputs: aroma diffusion, nature-scene display, and music playback.

The hardware list provides many of the building blocks, but the final architecture, controller assignments, communication protocol, power design, fragrance delivery method, and safety limits remain to be confirmed. The existing questionnaire provides an input mechanism but does not, on its own, establish a validated classification method. Mixed-dosha support must be defined explicitly rather than improvised in the code.

The most practical development path is to build and test each module independently, integrate one complete profile-driven session, verify safe stopping and fault recovery, and only then expand the system with additional profiles, sensors, or adaptive logic.

**The immediate priority is not to add more AI or more hardware. It is to agree on the assessment rules, define the system boundaries, and demonstrate a safe end-to-end workflow from questionnaire input to all three sensory outputs.**

---

## Appendix A — Source-Based Versus Proposed Information

### Taken from the supplied project material
- Project name and general concept.
- The three sensory modules.
- Intended settings and user groups.
- The Vata/Pitta/Kapha terminology and proposed sensory mappings.
- The questionnaire topics and three-option response structure.
- The listed hardware, sensors, power components, enclosures, and supporting materials.
- The presentation's longer-term interest in AI, biofeedback, and clinical outcome evaluation.

### Proposed in this document
- The initial-phase problem statement and scope.
- The rule-based-first implementation approach.
- The high-level architecture and controller responsibilities.
- The sample data/message structures.
- Functional and non-functional requirements.
- Safety and privacy requirements.
- Test plan, risks, development phases, and definition of done.

### Requires confirmation
- The exact elemental associations and their approved source.
- The questionnaire's existing scoring implementation.
- Validated scoring weights and classification thresholds.
- Dual-dosha decision rules.
- Purchased/available hardware and exact board variants.
- Nebulizer/fluid compatibility and safe operating limits.
- Final power architecture and communication protocol.
- Institutional approvals and clinical evaluation design.

## Appendix B — Suggested Next Project Documents

After this baseline is reviewed, create these separate documents:

1. **System Architecture and Data Flow** — controller roles, communication, session lifecycle, and failure states.
2. **Hardware Wiring and Power Design** — verified pin mapping, load calculations, driver selection, protection, and grounding.
3. **Questionnaire and Assessment Specification** — answer mapping, scoring, mixed-profile rules, and expert approval.
4. **Software/API Specification** — data schema, session configuration, device commands, acknowledgements, and logs.
5. **Test Plan and Validation Report** — module tests, integration tests, safety checks, results, and limitations.
6. **Implementation Tracker** — task owner, dependencies, status, test evidence, and unresolved decisions.
