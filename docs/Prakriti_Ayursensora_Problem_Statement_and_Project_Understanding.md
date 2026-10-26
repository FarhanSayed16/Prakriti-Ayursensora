# Prakriti Ayursensora
## Problem Statement and Project Understanding

**Document status:** Working baseline  
**Prepared from:** the KJSIM proposal deck (*AI-Enhanced Personalized Healing Environments Using Ayurvedic Principles*), the registered-design slide, the Phase-I component sheet (items 1–47), and the existing Prakriti questionnaire  
**This file is:** the agreed description of the problem, the idea, the people, the phases, and the corrections that the build should follow  
**This file is not:** the wiring plan or the purchase order. Module parts, connections, software, and the path of the data are in `Prakriti_Ayursensora_System_Architecture_and_Modules.md`.

Three kinds of statement appear below. They are labelled so a later reader can see what is already in the proposal and what still needs a named person to confirm.

| Label | Meaning |
|---|---|
| **From the proposal** | Stated in the deck, the component sheet, or the questionnaire |
| **Working enhancement** | A correction this baseline adopts so the project can be built and studied without mixing ideas |
| **Open** | Still needs Dr. Vinayak, the clinical team, or the project owner |

---

## 1. The project in one page

**Prakriti Ayursensora** is a personalized room environment built on Ayurvedic constitution. A person’s Prakriti is captured, reviewed, and stored as three scores — Vata, Pitta, and Kapha. From those scores the system runs one session of three outputs together:

1. **Aroma** — a controlled mist of a selected essential oil.
2. **Display** — nature scenery on a projector or screen.
3. **Music** — a selected Indian classical raga.

The first setting in the proposal is a deluxe or premium hospital room. The same unit is also proposed for a doctor’s cabin, a wellness centre, a spa or hotel, and a senior executive’s cabin.

The full programme is a five-year research project of Somaiya Vidyavihar (KJSIM, with SSBAS and KJSSE), aimed at studying whether a constitution-matched environment improves stress, anxiety, sleep, and recovery. The first engineering job is smaller and stricter: make one unit that accepts a human-confirmed profile, including a mixed constitution, runs the three outputs as one session, can be stopped immediately, and writes down exactly what was delivered.

**Working enhancement.** Until a study produces evidence, this is a wellness research prototype. “Healing”, “treatment”, and “AI diagnosis” are programme language. They are not descriptions of what the first unit does.

---

## 2. Problem statement

### 2.1 The situation

Hospital stays and high-pressure work raise stress, disturb sleep, and leave people in rooms whose light, sound, and smell are generic. Scent, nature imagery, and music are already used informally. They are usually chosen for the room, not for the person. They are rarely run together. They are rarely recorded, so nobody can later say what the person actually received.

### 2.2 The Ayurvedic premise the project uses

Ayurveda describes a person’s constitution, **Prakriti**, as set early in life and stable. It is expressed through three doshas:

| Dosha | Proposal’s energy label | Elements on the proposal slide |
|---|---|---|
| **Vata** (Vataj) | Kinetic | Air and ether |
| **Pitta** (Pittaj) | Thermal | Fire and a little water |
| **Kapha** (Kaphaj) | Potential | Earth and water |

Most people are a combination, not a pure type. Classical practice recognises seven constitutions: three single (Vata, Pitta, Kapha), three dual (Vata–Pitta, Pitta–Kapha, Vata–Kapha), and one balanced across all three (Sama). Prakriti is read from physical, physiological, and psychological traits. A trained practitioner’s observation is the reference. A questionnaire is an aid to that reading.

The person’s **present condition** is a different idea. Stress, poor sleep, heat, heaviness, and illness describe the current state. In Ayurveda that current imbalance is **Vikriti**. The proposal’s outcomes — stress, anxiety, depression, sleep, recovery — are current-state outcomes.

### 2.3 What is missing

No system in the proposal’s settings does all of the following:

1. Captures Prakriti in a standard way and keeps the three scores, including a dual or balanced result.
2. Turns that profile into one shared plan for aroma, scenery, and music.
3. Runs the three outputs together, under a person’s control, with a hard stop.
4. Logs the profile version and everything that actually played, so a later study can test the result.

### 2.4 The engineering problem

Design and build a modular unit that:

- takes a reviewed Prakriti profile,
- applies one expert-approved mapping from that profile to oils, scenes, and ragas,
- coordinates aroma, display, and music as a single session,
- lets staff start, pause, and stop the session, including an immediate stop,
- fails safe if power or communication is lost,
- and records the session so the run can be repeated and audited.

The first version receives the profile from a questionnaire plus a clinician’s confirmation. Automatic assessment from a camera, a fingerprint, or a heart-rate sensor is a later research aim.

### 2.5 One-sentence problem statement

**Build a safe, logged, three-channel sensory unit that turns a clinician-confirmed Prakriti profile — single, dual, or balanced — into one coordinated session of aroma, nature scenery, and music.**

### 2.6 Research problem, kept separate from the build

**From the proposal.** The programme asks whether a dosha-matched environment reduces stress, anxiety, and depression and supports sleep, recovery, emotional regulation, and satisfaction (“360° well-being”).

**Working enhancement.** That question is answered by a study, not by a working demo. Phase II in the deck compares personalized rooms with standard care. A relaxing scent, a nature film, or music can help on its own. A study that wants to show that *matching the constitution* is what matters needs a comparison in which the room is pleasant but not matched to the person. Outcome scales named by the clinical lead are **PSS** (stress), **HADS** (anxiety and depression), and **PSQI** (sleep). Those scales measure the study. They do not drive the device.

---

## 3. What the system does

```text
ENROL
  Questionnaire  +  clinician review  →  Prakriti scores [Vata, Pitta, Kapha]
  Short “how are you now” check       →  current-state note (separate from Prakriti)

PLAN
  Approved mapping + scores + time of day  →  one session plan
  (which oil channel, which scenes, which ragas, how long, how strong)

RUN
  Aroma module   — mist on the planned channels
  Display module — nature scenes
  Music module   — ragas, within a volume limit
  Staff can pause or stop. A hardware stop cuts aroma even if software hangs.

RECORD
  Profile version, plan, start, stop, faults, and what each module actually did
```

The three modules share one plan and one session clock. They can also be tested one at a time. Aroma is the safety-critical module: if it faults, aroma goes off. Display and music may continue.

**Working enhancement.** The plan is chosen from Prakriti (the stable profile) and may be softened or intensified using the current-state check and the time of day. Sensors do not rewrite the Prakriti label.

---

## 4. Sensory mapping in the proposal

The deck repeats this mapping on the modules slide and on the methodology slide. Spellings below are corrected for the document; the deck writes Medows, Rosemery, Jasmin, and Sandal Wood.

| Profile | Aroma | Display | Music |
|---|---|---|---|
| **Vata** | Sandalwood, cinnamon, frankincense | Lush forest | Bhimpalasi, Bageshree, Yaman, Kafi |
| **Pitta** | Rose, jasmine, lavender, mint | Mountains and water bodies | Miyan ki Todi, Malkauns, Marwa, Durga |
| **Kapha** | Rosemary, eucalyptus, thyme, peppermint | Flowing water, meadows | Bhairav, Jaunpuri, Hamsadhwani, Desh |

This table is a **proposal**, not a frozen prescription. It goes into software only after Dr. Vinayak signs one version of it.

### 4.1 Contradictions already inside the source material

These must be resolved in that signed table. They should not be averaged in code.

1. **Speaker notes disagree with the slide on oils.** The notes say “calming lavender for Vata, cooling peppermint for Pitta, invigorating eucalyptus for Kapha.” The slide puts lavender with Pitta and peppermint with Kapha.
2. **Speaker notes swap the elements.** The slide is the one to follow: Vata = air + ether, Pitta = fire + a little water, Kapha = earth + water. The notes reverse Vata and Pitta.
3. **The component-sheet oils are a different set.** The sheet lists Helichrysum (tagged Vata/Pitta), Davana (tagged Vata/Kapha), tea tree (tagged Pitta/Kapha), and a Tridoshic / Sama balancing oil. None of these names appear in the deck mapping. The pilot oil set is **open**.
4. **Mint and peppermint** are assigned to opposite doshas on the slide. The expert decides whether that split stands.
5. **Raga and time of day.** Several listed ragas belong to a traditional time window (for example Bhairav in the morning, Marwa at twilight). A ward runs across the day and the night. The expert decides whether playback follows those windows.
6. **The Hindi “Raga Chikitsa” page** at the end of the deck is a Sapta Swar Academy social-media graphic. It is not the project mapping. It names Bhairavi as well as Bhairav, and it makes hormone claims the project should not repeat. Bhairav and Bhairavi are different ragas.

### 4.2 How a mixed profile uses the mapping

**Working enhancement.** Prakriti is stored as a score vector, not as a single word.

```text
answers + clinician review  →  [Vata, Pitta, Kapha], summing to 100%
                            →  a label: single, dual, or Sama
                            →  blend weights the three modules actually use
```

| Result | How the session uses it |
|---|---|
| **Single** | One dosha’s oil channel, scene set, and raga pool |
| **Dual** | The two leading doshas share the session in proportion to their scores. Aroma time-slices the two channels. Scenes alternate. Ragas are drawn from both pools. |
| **Sama** | All three doshas share the session |

The cut-offs that decide single, dual, and Sama are **open**. Dr. Vinayak sets them. They live in configuration, so a change of threshold does not require a code change. A label with a very small gap between the top two scores is shown to the clinician as a boundary case.

A clinician’s confirmed label is the result the session uses. Self-report alone is marked **provisional**.

---

## 5. Who it is for

**From the proposal.**

| Setting | Person |
|---|---|
| Deluxe and premium hospital rooms | Inpatients in a healing room |
| Doctor’s cabins | Clinicians between patients |
| Wellness centres, spas, hotels | Guests |
| Corporate cabins | CEO, CFO, CTO, directors, senior executives |

The speaker notes add that the unit is meant to support wellness and lower stress for people working under pressure, as well as to support patients.

**Working enhancement.** The first supervised use is a single controlled site (Somaiya Ayurveda Centre with the technology labs), with staff operating the unit. A shared hospital room, a child, pregnancy, asthma, fragrance allergy, and similar situations wait for an exclusion list from the clinical lead before anyone is enrolled.

---

## 6. Programme the proposal already defines

### 6.1 Objectives written in the speaker notes

1. Real-time Prakriti assessment using biofeedback and wearable sensors.
2. Evaluate dosha-specific aromatherapy.
3. Design a personalized environment of music, aroma, and visuals.
4. Adapt that environment from real-time data such as heart-rate variability.
5. Assess emotional regulation, recovery, and satisfaction.

**Working enhancement of objective 1.** Prakriti is assessed by questionnaire and clinician, then stored. Heart rate, oxygen saturation, skin conductance, and air quality describe the **current state** and the room. They are logged in a later phase. They are not a real-time Prakriti test. Objective 3 is the first build. Objectives 1, 2, 4, and 5 are the research programme around it.

### 6.2 Three research phases on the methodology slide

| Phase | Name in the deck | Where | What the slide says |
|---|---|---|---|
| **I** | System development and validation | Somaiya Ayurveda Centre, with technology labs | Build and validate the system |
| **II** | Clinical deployment | K. J. Somaiya Hospital | Personalized healing rooms compared with standard care, minimum 100 participants |
| **III** | Outcome assessment | Follow-up after discharge | Continuous biofeedback; physiological, emotional, and satisfaction outcomes; statistical analysis |

### 6.3 Five-year timeline on the budget slide

| Year | Share shown | Heading |
|---|---|---|
| 1 | 20% | Foundational research |
| 2 | 45% | Prototype and small trials |
| 3 | 15% | Large clinical trials |
| 4 | 15% | Commercial feasibility |
| 5 | 5% | Policy advocacy and dissemination |

### 6.4 Two different budgets

The deck’s five-year envelope and the Phase-I shopping sheet are not the same budget.

**Five-year programme total: ₹3,00,00,000**

| Head | Amount (INR) |
|---|---:|
| Personnel | 90,00,000 |
| Equipment and infrastructure | 80,00,000 |
| Clinical trials and validation | 45,00,000 |
| Consumables and materials | 20,00,000 |
| Travel and collaboration | 15,00,000 |
| Dissemination and IP filing | 20,00,000 |
| Contingency and overheads | 30,00,000 |
| **Total** | **3,00,00,000** |

**Phase-I component sheet grand total: ₹2,27,766** (47 lines). Section 9 summarises that sheet. A line-by-line re-add should be done before anything is ordered. The treadmill (₹17,998) and the student-coordinator line (₹50,000) need a written reason if they stay inside a hardware phase.

---

## 7. People and institutions

**Submitted by:** Dr. Krantee M. Jamdaade, Dr. Harshali Y. Patil, Dr. Ninad D. Mehendale (KJSIM, SSBAS, KJSSE).

| Person | Role on the team slide | Owns in this baseline |
|---|---|---|
| **Krantee** | Preparation, administration, Ayurveda principles, timeline, ICSSR compliance, link between Somaiya Ayurveda Centre and K. J. Somaiya Hospital | Programme, sites, recruitment |
| **Ninad** | Technical and AI lead: Prakriti assessment engine, sensors, real-time feedback, prototype, control, adaptive logic | Later AI and sensing; technical supervision |
| **Harshali** | Integration across Ayurveda, AI, psychology, and clinical work; machine learning, analysis, dashboard | Data, dashboard, integration |
| **Seema** | Clinical trial design, inclusion and exclusion, ethics, consent, PSS, HADS, PSQI | Safety limits, consent, outcomes |
| **Om** | Public-health evaluation, outcome protocols, trial and publication standards | Evaluation design |
| **Vinayak** | Ayurveda and Prakriti: validate the model, map oils, ragas, and visuals, standardize the questionnaire, train the team | The mapping table, the questionnaire, and the dual-dosha rule |

**Institutional support shown:** Somaiya Ayurvihar and KG Mittal Ayurvedic Hospital, plus the hospital and Ayurveda centre named in the team roles.

**Intellectual property already claimed:** a registered design titled **PRAKRITI AYURSENSORA-AI NEBULIZER** (Designs Act, 2000; certificate shown in the deck, dated 7 March 2025). A design registration protects appearance. It does not protect the method, the scoring, or a medical claim.

The deck shows two physical ideas side by side: a wall unit with a small screen, a camera, a power button, and speaker grilles, and a blue cylindrical registered form. The build follows the three functions (aroma, display, music). It does not have to copy both shapes.

A commercial aroma diffuser priced at **₹2,999** is on the idea slide as a reference. The project’s distinction is the personalized, synchronized, logged session, and the study around it.

---

## 8. What already exists

| Asset | State |
|---|---|
| Proposal deck | Concept, mapping, phases, team, five-year budget |
| Registered design | Appearance of the “AI Nebulizer” |
| Early aroma bench | Shown on the current-status slide: relays, mist hardware, an enclosure, blue light, and a runner-up recognition. Schematic and code are still to be collected. |
| Questionnaire | A website flow, “Analyze My Prakruti”, about 55 items, three options each. Option order on the form is Pitta, then Vata, then Kapha. It returns a label. It does not yet return the three scores. |
| Component sheet | 47-line Phase-I list totalling ₹2,27,766. This is a proposed list, not proof that every part has been bought or tested. Handwritten marks on the first page tick the Raspberry Pi 5, the Arduino, and the L298N, and note the three modules. The handwritten dates are not clear enough to treat as deadlines. |
| Scoring rules, oil list, session software, power design | Not settled |

The questionnaire covers skin, hair, eyes, lips, teeth, physique, frame, temperature, appetite, thirst, sweat, sleep, weight, speech, gait, decisions, emotion, memory, temperament, climate, taste, relationships, dreams, and self-description. Several topics repeat (hair, eyes, appetite, liquids, frame). Several items ask the person to report traits a clinician would normally observe (veins, tongue, teeth, chest, frame). Items on faith, ego, and praise may describe mental temperament rather than dosha. Hindi and Marathi versions are required for the Mumbai sites and are not confirmed.

**Working enhancement for the questionnaire**

- Ask about lifelong traits, so a current illness does not fill in the constitution.
- Randomize the order of the three options; store the real mapping on the server.
- Allow “both apply” and “not sure”.
- Move observable body items onto a clinician sheet.
- Return `[Vata, Pitta, Kapha]`, the label, and the rule version.
- Mark a self-report result as provisional until a clinician confirms it.

---

## 9. Hardware the proposal lists, and what it is for

Grouped from the 47-line sheet. Quantities are as printed.

| Group | What is listed | Role in the idea |
|---|---|---|
| **Control** | Raspberry Pi 5, 8 GB (1); Arduino Uno R4 WiFi (1); L298N motor driver (1); ESP32 with 5-inch touchscreen (2); ESP32 dev board (1); 32 GB microSD (2) | Profile, media, and actuation. Five controllers are listed for one unit. |
| **Aroma** | ACEBOTT 2.4 MHz ultrasonic nebulizer (3); 80 mm 5 V fan (3); 4-channel relay (2); oils in Section 4.1 (10 bottles each); 30 ml dropper bottles (9) | Three mist channels, one per dosha, which fits dual-dosha blending |
| **Identity and UI** | AS608 fingerprint (1); Arducam IMX219 camera (1); 7-inch HDMI touchscreen (1) | The idea slide also says the camera can be replaced by a mobile app, and that a fingerprint only loads traits already captured in the app |
| **Power** | 5 V / 10 A SMPS (1); 25,000 mAh 100 W power bank (1); four 18650 cells; two 5 V / 3 A buck modules | Mains plus a backup path. Needs a real load calculation before it is built. |
| **Picture and sound** | Portronics Beem 470 projector (1); 10 W Bluetooth/AUX speaker (1); two 5 m WS2812B LED reels; three 0.96-inch OLED displays | Scenery, music, optional colour light, tiny status screens |
| **Sensing** | MAX30100 (1); MAX30102 (2); GSR (2); BME680 (2) | Heart rate, oxygen saturation, skin conductance, air quality. Later-phase logging, once the open-loop session works. |
| **Build** | Acrylic enclosure 300 × 200 × 150 mm, 3D-printed mounts, ABS box, Pi cooling, wire, breadboard, PCB, tools, cables, 8-port switch | Prototype housing |
| **Other lines** | 300 consent forms, stationery, software and cloud (₹5,000), transport, publication, contingency (₹28,145), treadmill (₹17,998), student coordinator (₹50,000) | Administration. The treadmill is unrelated to the three modules unless a separate protocol needs it. |

**Working enhancement.** Phase I uses the smallest set that can run a logged three-channel session: one Pi as the brain (profile, plan, video, audio, log), one microcontroller for the mist channels and their safety limits, a staff interface in the browser, one air-quality sensor, a certified mains supply, and a hardware stop. Camera, fingerprint, the extra controllers, the LED reels at full length, and battery packs wait until the open-loop session is reliable. The two 5 m LED reels cannot share a 5 V / 10 A supply at full brightness. That power path is redesigned, not copied from the sheet.

The ultrasonic discs are made for water. Essential oils can clog them, and standing water in a ward needs an infection-control method. A bench test with the approved pilot oils decides whether this hardware stays, before three channels are enclosed. Electronics stay in a dry compartment, separate from the mist path.

---

## 10. What Phase I must achieve

Phase I in the deck is “system development and validation” at the Somaiya Ayurveda Centre with the technology labs. For the build, that means the following and only the following.

1. A person can be enrolled under a code, with consent recorded.
2. The questionnaire and the clinician sheet produce a confirmed profile: scores, a single / dual / Sama label, and the version of the rules.
3. One action starts a session. Aroma, scenery, and music follow that profile and each other.
4. Staff can pause and stop. A hardware stop cuts the mist even if the computer fails.
5. Loss of power or loss of the link turns actuation off. The unit does not restart a mist by itself.
6. The log can show, afterwards, which profile was used, which oil, scene, and track ran, and why the session ended.
7. Each module can be demonstrated on its own, then together.
8. Setup, limits, and known gaps are written down.

A finished Phase I unit is ready for an ethics and safety review. It is ready for a patient study only after that review (the deck’s Phase II).

### Acceptance, in plain checks

| Check | Pass looks like |
|---|---|
| Profile | The same answers and the same rule version always give the same scores and label |
| Mixed type | An approved dual example and an approved Sama example each produce the right blend |
| Three channels | One plan starts aroma, picture, and music; each can also run alone |
| Stop | Pause, software stop, and hardware stop all end mist inside an agreed time |
| Failure | Unplugging the controller or the supply leaves mist off, including after reboot |
| Record | The log matches what the hardware did |
| Privacy | The device stores a participant code. Raw answers do not need to sit on the microcontroller. |
| Claims | The demo script describes a personalized session. It does not claim a cure or a diagnosis. |

---

## 11. Enhancements this baseline adopts

These are the corrections to the original write-up and to the deck. They are part of the project understanding from here on. Items marked **open** still need the named owner; the direction is already set.

| # | Enhancement | Direction |
|---|---|---|
| 1 | **Separate Prakriti and Vikriti** | Prakriti is the stable profile from the form and the clinician. The current check and, later, sensors describe state. The deck’s phrase “real-time Prakriti from sensors” is retired. |
| 2 | **Seven constitutions, stored as scores** | `[Vata, Pitta, Kapha]` is the record. Single, dual, and Sama labels are derived. The session blends from the weights. |
| 3 | **One signed mapping** | Dr. Vinayak freezes oils, scenes, ragas, time-of-day rules, and the dual-dosha cut-offs before they are encoded. The slide, the speaker notes, the oil sheet, and the Hindi graphic are inputs to that meeting, not four competing tables. |
| 4 | **Rules before AI** | Phase I uses a versioned, explainable scoring method. A model is in scope only after there is a consented, clinician-labelled set large enough to test. Ninad’s AI objective remains the later programme. |
| 5 | **Clinician confirms** | Self-report is provisional. The session runs on the confirmed profile. |
| 6 | **Open loop first** | The first unit does not adapt itself from heart rate or a camera. Sensors, if added, log the room and the person. Adaptation is Phase III in the deck. |
| 7 | **Identity without biometrics** | A participant code is enough for Phase I. Fingerprint and facial video are optional later, and only with a specific consent. The deck already allows a mobile app in place of the camera. |
| 8 | **One brain, one actuator** | The Pi coordinates and plays media. One microcontroller drives mist, fan, and local safety. Extra boards on the sheet are not given overlapping jobs. |
| 9 | **Aroma is gated by a bench test** | Water-based ultrasonic mist is the starting method because a bench unit already exists. It is kept only if dose, clogging, noise, leaks, and hygiene pass. A waterless nebulizer is the fallback. |
| 10 | **Safety is in the first build** | Immediate stop, default-off at boot, mist only while the fan runs, dry electronics, daily cleaning of the liquid path, and an exclusion list owned by Seema. |
| 11 | **Study arm reserved in the data model** | Each session can be marked personalized, generic, or mismatched, so Phase II can test matching itself. Blinding and assignment belong to the clinical team. |
| 12 | **Local data** | Participant code, consent scope, rule version, and session events stay on the unit. Export is de-identified. India’s DPDP Act, 2023, and the hospital’s ethics process govern collection. |
| 13 | **Content has a licence** | Scenes and raga recordings are owned, commissioned, or properly licensed. The Hindi infographic is not a content source. |
| 14 | **Two budgets stay distinct** | ₹2.28 lakh is the Phase-I sheet. ₹3 crore is the five-year programme. Hardware for the first unit is ordered against a revised list after the mapping and the aroma bench test, not by buying all 47 lines. |

---

## 12. Open items, in the order they block the build

### Dr. Vinayak — blocks the mapping and the scores

1. The single mapping table: oil, scene, and raga for each dosha, including the contradictions in Section 4.1.
2. The pilot oil set for three channels (one known oil per channel), and whether the bottles already on the sheet are used, replaced, or held.
3. The numeric rule for single, dual, and Sama, and whether a small third score is ignored.
4. Which published Prakriti method the 55 items should follow, and which items move to the clinician or come out (including faith, ego, and praise).
5. Whether time of day and season change the plan.

### Dr. Seema and Dr. Krantee — block any use with people

6. Ethics status, consent text, and who may switch the unit on.
7. Who is excluded from aroma exposure.
8. Maximum scent duration, strength, and night-time volume and brightness.
9. Whether Phase II adds a generic (unmatched) comparison arm.
10. The first site and the real dates. The handwritten dates on the component sheet are not readable.

### Technical leads — block software and parts

11. Is the questionnaire website’s code available, and who maintains it?
12. Was the existing aroma bench run with real oils, and where are its schematic and code?
13. Is a participant code acceptable for Phase I?
14. Who produces the scene films and the raga recordings, and under what licence?
15. Who approves the first small purchase for the aroma bench, and what is the ceiling?

---

## 13. Glossary

| Term | Meaning in this project |
|---|---|
| **Prakriti** | Stable constitution, recorded as Vata, Pitta, and Kapha scores |
| **Vikriti** | Current imbalance or state. Separate from Prakriti. |
| **Dosha** | Vata, Pitta, or Kapha |
| **Dual (dvandvaja)** | Two doshas lead, under the expert’s rule |
| **Sama** | All three doshas are close enough to share the session |
| **Session plan** | The one instruction set for aroma, display, and music |
| **Mapping table** | The signed list from dosha to oil, scene, and raga |
| **PSS, HADS, PSQI** | Study questionnaires for stress, anxiety/depression, and sleep |
| **Open loop** | The session follows the plan and does not change itself from sensors |
| **Fail-safe** | On fault or power loss, mist and actuators go off and stay off |

---

## 14. Source note

| Source | Used for |
|---|---|
| Deck PDF and PPTX, including speaker notes | Title, idea, users, elements, three-module mapping, objectives, methodology, team, design slide |
| Methodology slide | Phase I–III sites and the “360° well-being” aim |
| Budget and status slide | Five-year split, ₹3 crore heads, Somaiya Ayurvihar, KG Mittal Ayurvedic Hospital, early prototype photo |
| Design-certificate slide | Registered name PRAKRITI AYURSENSORA-AI NEBULIZER, 7 March 2025 |
| Component photographs, items 1–47 | Phase-I list and total ₹2,27,766, including the oil names and dosha tags printed on that sheet |
| Questionnaire already reviewed | About 55 three-option items and the gaps in Section 8 |
| Closing Hindi graphic | Recorded only so it is not mistaken for the project’s raga map |

The working set is this file, the system architecture, the three-module list, and the buy list. Earlier drafts and the original deck are in `docs/archive`. Where an archived draft disagrees with this file, this file is the baseline.
