# Prakriti Ayursensora — Project Understanding (Phase 0)

*AI-enhanced personalized healing environment using Ayurvedic principles*
*Prepared for: Farhan (project owner, build lead) · Source: KJSIM/Somaiya deck (PDF + PPTX incl. speaker notes), 3 BOM photos, existing questionnaire website*

> **Scope of this document:** problem understanding → idea → what exists → expected outcome → flaws/risks → open questions.
> The detailed *planning* phase (architecture, schedule, task breakdown) comes next, after the questions in §10 are answered.

---

## 0. TL;DR

1. **What it is:** a room-level "healing environment" box for deluxe hospital wards (also doctor cabins, spas, executive cabins). Given a person's **Prakriti** (Ayurvedic constitution: Vata / Pitta / Kapha, or a mix), it runs **three synchronized modules**: **aroma** (nebulized oils), **display** (projected nature scenes), **music** (dosha-matched ragas). Goal: reduce stress, anxiety, depression and improve sleep/recovery.
2. **Phase 1 reality:** Prakriti is decided **by humans from forms/questionnaire** (the website already exists). The hardware only needs to *consume a Prakriti profile* and run the three modules. AI is *not* required to decide Prakriti in Phase 1.
3. **Your key design problem:** support **dual (and tri) Prakriti**. Classical Ayurveda has 7 types, so the system should work on a **weighted vector [V, P, K]**, not a single label. §5 defines this.
4. **Biggest issues found (ranked):**
   1. Prakriti is a *fixed constitution*; stress/anxiety is a *current state* (Vikriti). The deck mixes the two ("real-time Prakriti assessment from sensors").
   2. The **oils in the BOM don't match the oils in the clinical mapping** (zero overlap), and the deck contradicts itself on lavender/peppermint.
   3. **Power design is broken** (5V/10A SMPS vs. 600 WS2812B LEDs; 3A buck for a Pi 5; battery pack without a BMS).
   4. **Ultrasonic mist makers + essential oils** is a known failure mode (clogging, no emulsion, hygiene) and has patient-safety cautions.
   5. **Study design can't prove "Prakriti-matching works"** (needs a non-personalized control arm).
5. **Recommended first move:** get the Ayurveda expert (Dr. Vinayak) to freeze one **mapping table** (dosha → oils, scenes, ragas, time-of-day) and the **dual-dosha rule**, *then* lock hardware. Everything else depends on that table.

---

## 1. What I reviewed

| Source | What I took from it |
|---|---|
| Deck (PDF/PPTX, 15 slides) | Idea, target segments, Tri-dosha basics, 3-module mapping, design registration, BOM, methodology (3 phases), budget (₹3 crore, 5 yr), team roles, **speaker notes** |
| BOM photos (3 pages, items 1–47) | Components, qty, cost; grand total ₹2,27,766 (Phase-I) |
| Website text dump | 55-question Prakriti questionnaire, 3 options each |
| Last PDF page | Hindi "Raga Chikitsa" table (Sapta Swar Academy social-media graphic) |

Notes / gaps:
- The PPTX content matches the PDF; the PPTX adds speaker notes that matter (see §8).
- Handwriting at the top of BOM page 1: "① Aroma ② Display ③ Music" and what looks like a timeline ("… Dec → Feb → Dec …"). **Illegible to me; please confirm the deadlines.** Items 1–3 (RPi5, Arduino, L298N) have tick marks, so I assume those are ordered/approved. Please confirm.
- I have not seen the website's code or stack, or the earlier prototype's schematic/code.

---

## 2. Problem statement (my understanding)

**Context.** Hospital stays and high-stress work environments raise stress, anxiety and poor sleep. Ambient interventions (scent, visuals, music) are used informally; they are usually generic.

**Problem.** There is no system that **personalizes the ambient environment to an individual's Ayurvedic constitution** and delivers all three sensory channels together, in a way that can be tested clinically.

**Proposed solution.** A device ("Prakriti Ayursensora – AI Nebulizer"; design registered) that:
1. Identifies the person (fingerprint or app) and loads their **Prakriti profile**.
2. Drives **aroma + display + music** matched to that profile.
3. (Longer term) reads physiological signals (HR, SpO₂, skin conductance, air quality) and **adapts** the environment.

**Who it's for.** Hospital deluxe rooms, doctor cabins, wellness centres, spas/hotels, CEO/CFO/CTO/directors.

**Stated objectives (from speaker notes).** (a) real-time Prakriti assessment with biofeedback/wearables, (b) test efficacy of dosha-specific aromatherapy, (c) personalized environment with music + aroma + visuals, (d) dynamic adaptation using real-time data such as HRV, (e) assess emotional regulation, recovery, satisfaction.

**Your mandate.** Own the whole thing and build the three IoT modules. Phase 1 input = Prakriti decided manually by the team from forms.

---

## 3. The idea in system form

```
   INPUT                    BRAIN                       OUTPUT MODULES
┌───────────────┐      ┌─────────────────┐      ┌──────────────────────────┐
│ Questionnaire │      │ Prakriti profile│      │ 1. AROMA                 │
│ (web/app)     │─────►│ vector [V,P,K]  │─────►│  3 nebulizers + fans     │
│ Team override │      │ + context       │      │ 2. DISPLAY               │
│ Fingerprint/  │      │ (time, season,  │      │  Pi → projector (scenes) │
│ patient ID    │      │  state)         │      │ 3. MUSIC                 │
└───────────────┘      │ Scheduler/mixer │      │  Pi → speaker/headphones │
                       │ (Raspberry Pi 5)│      │ + LED chromotherapy      │
┌───────────────┐      │                 │      └──────────────────────────┘
│ Sensors (later)│────►│ Logger + API    │                │
│ HR, SpO₂, GSR, │     └────────┬────────┘                ▼
│ VOC/temp/RH    │              │                 Session logs → dashboard
└───────────────┘              ▼
                         Actuator controller (Arduino/ESP32): relays, fans, LEDs
```

**Division of labour I'd assume:** Raspberry Pi = brain, content playback (video/audio), UI, logging. Microcontroller = real-time actuation (relays, PWM, LEDs, sensor polling).

---

## 4. Ayurvedic foundation (what you need to know to build this)

### 4.1 Core concepts
- **Tridosha:** Vata (Air + Ether, "kinetic"), Pitta (Fire + a little Water, "thermal"), Kapha (Earth + Water, "potential/structural"). Deck slide matches this.
- **Prakriti:** constitution, set at conception (determined by parental Prakriti and other factors per Charaka Samhita), considered **stable for life**.
- **Vikriti:** the **current** dosha imbalance (stress, illness, season, diet). Treatment is aimed at Vikriti, guided by Prakriti.
- **7 constitution types:** Vata, Pitta, Kapha (single); Vata-Pitta, Pitta-Kapha, Vata-Kapha (dual, *dvandvaja*); Sama/Sannipataja (all three balanced, *tridoshaja*). This is exactly what you described: people are usually a combination.
- **Therapeutic principle:** *like increases like; opposites balance* (samanya–vishesha). Each dosha has qualities (gunas):

| Dosha | Qualities | Tends to be aggravated by | Balanced by |
|---|---|---|---|
| Vata | dry, light, cold, rough, mobile, subtle | cold, dryness, irregularity, noise | warm, moist, heavy, grounding, regular |
| Pitta | hot, sharp, oily, liquid | heat, intensity, pressure | cool, soothing, calm |
| Kapha | heavy, slow, cool, oily, static, dense | cold, damp, inactivity, heaviness | warm, light, dry, stimulating |

- **Context layers worth modelling later (design hooks, ask Dr. Vinayak):** *dinacharya* (dosha by time of day: Kapha morning/evening ~6–10, Pitta midday/midnight ~10–2, Vata ~2–6) and *ritucharya* (season). These also line up with the **raga prahara (time-of-day) tradition** in Indian classical music.

### 4.2 Texts and traditions in play
- **Ayurveda:** Charaka Samhita (Prakriti: Vimana & Sharira Sthana), Sushruta Samhita, Ashtanga Hridaya. *Chapter references are from my memory; verify with Dr. Vinayak before citing in a paper.*
- **Music:** raga–time theory (Natya Shastra / Sangita Ratnakara tradition). Raga time-of-day rules are real and matter for a 24×7 ward.
- **Aroma:** classical equivalents are *dhoopana* (medicated fumigation) and *dravya* like chandana, agaru, guggulu. The deck's essential-oil approach is a modern adaptation.

### 4.3 Mapping in the deck (as given) and points to confirm with the expert
| Dosha | Aroma | Display | Music (ragas) |
|---|---|---|---|
| Kapha | Rosemary, Eucalyptus, Thyme, Peppermint | Flowing water, meadows | Bhairav, Jaunpuri, Hamsadhwani, Desh |
| Pitta | Rose, Jasmine, Lavender, Mint | Mountains + water bodies | Miyan ki Todi, Malkauns, Marwa, Durga |
| Vata | Sandalwood, Cinnamon, Frankincense | Lush forest | Bhimpalasi, Bageshree, Yaman, Kafi |

Questions for the expert (I am **not** asserting these are wrong; they are things I can't verify and a reviewer will ask):
- **Kapha → "flowing water"** uses the water element (Kapha's own); is the logic "mobile/flowing" or should it be drier/warmer/brighter scenes?
- **Sandalwood** is classically considered cooling (often listed for Pitta). Is Vata placement intended?
- **Mint (Pitta) and Peppermint (Kapha)** overlap; same plant family, opposite doshas.
- **Marwa** (a tense, twilight raga) for Pitta relaxation, and **Bhairav** (a morning raga) for Kapha at night: how does time-of-day gating work?
- The deck's **speaker note** says "calming lavender for Vata, cooling peppermint for Pitta, invigorating eucalyptus for Kapha", which **contradicts the slide** (lavender = Pitta, peppermint = Kapha).

---

## 5. Dual-Prakriti design (your specific requirement)

### 5.1 Principle
Never store Prakriti as a single label. Store **scores** and derive a label from them.

```
raw answers  →  score vector  S = [V, P, K]  (sum = 100%)
             →  type label    (single / dual / sama) + order (primary, secondary)
             →  blend weights W = [wV, wP, wK] used by all three modules
```

### 5.2 Scoring and classification (placeholders; thresholds are the expert's call)
1. `S_d = (answers choosing dosha d) / (answered questions)`.
2. Sort descending: `s1 ≥ s2 ≥ s3`.
3. Classify:
   - **Single** if `s1 − s2 ≥ δ₁`
   - **Dual** (s1 + s2) if `s1 − s2 < δ₁` and `s2 − s3 ≥ δ₂`
   - **Sama (tri)** if `s1 − s3 < δ₂`
4. Blend weights for a dual type = top two renormalized: `w1 = s1/(s1+s2)`, `w2 = s2/(s1+s2)`; third = 0. For Sama, equal weights.
5. Keep **δ₁, δ₂ as config**, so Dr. Vinayak can tune them without code changes. *(Initial values to be provided by the Ayurveda expert; I deliberately don't invent them.)*

### 5.3 How each module consumes the weights
| Module | Single | Dual (e.g. Vata 60 : Pitta 40) |
|---|---|---|
| **Aroma** | One channel active | **Time-sliced blending:** in each cycle (e.g. 10 min) channel V runs 60% of on-time, P 40%. One nebulizer per dosha = the "3 nebulizers" in the BOM |
| **Display** | One scene set | Playlist alternates/composes scenes in 60:40 (e.g. forest + mountains/water) |
| **Music** | One raga pool | Weighted-random pick across both pools, **filtered by time-of-day**, no abrupt switches |
| **LED (chromotherapy)** | One palette | Blended palette |

### 5.4 Prakriti ≠ current state
For stress/anxiety outcomes, add a second input, **Vikriti/state**, even if crude in Phase 1 (e.g. a short "how do you feel now" check on the touchscreen + later sensors). The environment is then `f(Prakriti, state, time-of-day)`. This is the most important conceptual fix (see §8, #1).

---

## 6. What is available (inventory)

### 6.1 Prior work
- **Existing hardware prototype** (relays + nebulizer, acrylic box with blue LED) that won **Runner-up at Prahalpa'25** (hardware project). Ask for its schematic and code: it is the fastest route to a working aroma channel.
- **Design registered** for the "Nebulizer" (Design Act 2000; protects *appearance only*, not function).
- **Questionnaire website** (login, 55 questions, "Analyze My Prakruti").
- **Institutional support:** Somaiya Ayurveda Centre, KG Mittal Ayurvedic Hospital / K. J. Somaiya Hospital.
- **Team:** Krantee (PI/admin, Ayurveda), Ninad (technical & AI lead), Harshali (integration, ML, dashboard), Seema (clinical trial & psychometrics: PSS, HADS, PSQI), Om (public health/evaluation), Vinayak (**Ayurveda & Prakriti expert**, owns the mapping and questionnaire).

### 6.2 BOM by module (47 items)
| Group | Items (qty) | Role |
|---|---|---|
| **Compute/control** | Raspberry Pi 5 8GB (1); Arduino Uno R4 WiFi (1); ESP32 5" touch display boards (2); ESP32 dev board (1); microSD (2); L298N (1) | Brain, actuation, wireless bridge |
| **Aroma** | Ultrasonic nebulizer modules (3); 80mm 5V fans (3); 4-ch relay modules (2); essential oils: Helichrysum, Davana, Tea Tree, "Tridoshic" (10 each); glass bottles (9) | Module 1 |
| **Display** | Portronics Beem 470 projector (1); 7" HDMI touchscreen (1); OLED 0.96" (3) | Module 2 + UI |
| **Music** | 10W Bluetooth/aux speaker (1) | Module 3 |
| **Ambient light** | WS2812B 5 m strips (2 reels) | Chromotherapy |
| **User input/ID** | AS608 fingerprint (1); Arducam IMX219 camera (1) | ID; expression analysis |
| **Sensing** | MAX30100 (1); MAX30102 (2); GSR (2); BME680 (2) | HR/SpO₂, stress, air quality/VOC |
| **Power** | 5V/10A SMPS; 25,000 mAh power bank; 4×18650; 2× 5V 3A buck | Mains + backup |
| **Mechanical/consumables** | Acrylic enclosure, 3D prints (10), ABS box, heatsinks, PCBs, wires, breadboards, solder kit, cables, 8-port switch | Build |
| **Admin/other** | Consent/forms (300), stationery, software/cloud, transport, publication, contingency, treadmill, student coordinator | Non-hardware |

Phase-I total on sheet: **₹2,27,766**.

---

## 7. Expected outcomes

**Programme-level (from the deck)**
- Reduced **stress, anxiety, depression**; improved sleep and "360° well-being".
- Measured with **PSS (stress), HADS (anxiety/depression), PSQI (sleep)**, plus physiological and satisfaction outcomes.
- 3 phases: **I** system development & validation (Somaiya Ayurveda Centre) → **II** clinical deployment, ≥100 participants, personalized rooms vs standard care (K. J. Somaiya Hospital) → **III** continuous biofeedback + post-discharge follow-up, statistical analysis.
- 5-year plan, ₹3 crore envelope; Phase-I budget is the ₹2.28 lakh BOM.

**Engineering deliverables I'd define for Phase I (for your confirmation)**
1. A working unit that, given a profile (single/dual/sama), runs aroma + display + music in sync.
2. Profile intake from the questionnaire (team confirms or overrides) and patient identification.
3. Session logging (what ran, for how long, any sensor data).
4. Safe, repeatable operation in a ward setting (power, hygiene, fail-safes).
5. A basic dashboard for the team.

---

## 8. Flaws, risks and issues (ranked)

### CRITICAL

**C1. Prakriti vs Vikriti is conflated.**
The deck lists "real-time Prakriti assessment using biofeedback and wearable sensors". Prakriti is a lifelong constitution, so HR/GSR/HRV cannot "measure" it in real time; they measure **current state**. The outcomes (stress, anxiety) are also state-based. *Fix:* Prakriti (from forms) sets the *baseline mapping*; sensors/state set *intensity and adaptation*. Reword objectives accordingly or a reviewer will flag it.

**C2. The oils you bought are not the oils in the mapping.**
Mapping needs: rosemary, eucalyptus, thyme, peppermint, rose, jasmine, lavender, mint, sandalwood, cinnamon, frankincense. BOM has: Helichrysum, Davana, Tea Tree, "Tridoshic blend". **No overlap.** Also: the BOM text labels the Tridoshic oil as "for Pitta dosha patients: cooling" while its name says Sama Prakriti. *Fix:* expert freezes a **pilot oil set** (ideally 1–2 per dosha, so 3 channels) and the BOM is corrected before ordering more.

**C3. Power system doesn't work as specified.**
- WS2812B: 60 LEDs/m × 5 m = 300 LEDs per reel, up to ~60 mA each at full white ≈ **18 A per reel (36 A for two)**. The 5V/10A SMPS cannot feed that plus the Pi. *Fix:* one reel, brightness capped (~25%), power injection, separate LED supply.
- **Pi 5** wants a proper 5V/5A (27W USB-C PD) supply; a **3A buck** converter is undersized, and powering via GPIO bypasses protection.
- **4×18650 without a BMS** and a power bank "pass-through UPS" are a safety issue in a hospital (many power banks don't charge and discharge simultaneously). The "12V→5V" buck has no 12V source defined. *Fix:* pick **one** backup strategy (or drop battery backup in Phase 1), use a certified PSU, no raw Li-ion pack.
- No power budget exists; **build one first**.

**C4. Ultrasonic mist maker + essential oils; patient safety.**
- Piezo discs are designed for **water**; undiluted oils don't atomize well, clog, and can degrade parts. Practical routes: water + oil with a suitable emulsifier, or a true **nebulizing (cold-air) diffuser**. Test on the bench before committing three channels.
- **Hygiene:** standing water in a ward = microbial growth risk; needs cleaning/drain protocol and infection-control sign-off.
- **Clinical cautions:** common aromatherapy cautions apply to infants/young children (e.g. peppermint, eucalyptus), asthma/COPD, pregnancy, epilepsy, allergies, shared rooms. Need exclusion criteria, patient consent, and a physician-controlled on/off. *The clinical team (Seema) must own this list.*
- **Electronics + mist** in one box (the prototype photo shows relays beside the mist): condensation/short-circuit risk. Separate wet/dry zones.

**C5. Study design can't prove the core claim.**
Phase II compares "personalized rooms vs standard care". Any relaxing scent/music/visual may help (and expectancy/placebo effects are strong), so that comparison can't show that **Prakriti-matching** matters. *Fix:* add a **non-personalized (generic relaxing) arm** and/or a mismatched arm. Evidence context: music has reasonable support for anxiety reduction in medical settings; aromatherapy evidence is mixed; **raga–dosha matching is, as far as I know, unvalidated**. The system is a way to test a hypothesis, so word claims accordingly.

### HIGH

**H1. Questionnaire quality (55 questions).**
- **Order bias:** every question lists options in the same order (Pitta, Vata, Kapha). Randomize the display order (store mapping server-side).
- **Over-weighting:** 4 hair questions + 2 eye questions (≈11% of the test on hair/eyes). **33 physical/physiological vs 22 psychological** items; weighting is implicit.
- **Non-discriminating options:** e.g. "Satisfaction: *Seldom* satisfied / *Rarely* satisfied / Content" (first two nearly identical).
- **Self-report of observable traits** (veins, tongue, teeth, chest, frame) is unreliable; classical assessment is by a trained Vaidya observing the person. Consider an **observer section** for clinicians.
- **Forced single choice** can't represent "both apply", which is exactly what dual Prakriti needs. Consider allowing two picks or a 5-point scale per item.
- **"Faith in God"** as a dosha discriminator is culturally sensitive in a hospital; ask the expert whether it stays.
- Needs **Hindi/Marathi** versions for Mumbai patients.
- Ask which published/validated Prakriti instrument the expert is basing this on; validate yours against clinician judgement (agreement/kappa) before relying on it.

**H2. Hardware is over-specified and has electrical conflicts.**
- **5 compute devices** (Pi 5, Uno R4 WiFi, 2× ESP32-display, ESP32 dev). The Pi 5 already has Wi-Fi/BT. The Uno R4 WiFi contains its own ESP32 chip. *Recommend Pi 5 + one microcontroller.*
- **L298N has 2 channels, you have 3 fans**; it also drops ~2 V and **3-pin fans can't be PWM-controlled** through the sense wire. Use logic-level **MOSFETs** (or 4-pin fans).
- **Relays (8 channels) for DC loads** is overkill; MOSFET/SSR boards are quieter and last longer. Keep relays only where isolation is needed.
- **I²C address clashes:** MAX30100 and MAX30102 both use **0x57**; three SSD1306 OLEDs only have **2 addresses** (0x3C/0x3D). Use an I²C mux (TCA9548A) or drop duplicates.
- **MAX30100 + MAX30102:** redundant; MAX30102 supersedes. Finger PPG gives noisy HRV; if HRV is truly needed consider a BLE chest strap that outputs RR intervals.
- **Arducam IMX219** listing targets Jetson/CM modules; the **Pi 5 needs the 22-pin CSI adapter cable**. Check.
- **WS2812B** is 5V logic; ESP32 is 3.3V (needs a level shifter, e.g. 74AHCT125).
- **Display redundancy:** 7" touchscreen + 2× 5" ESP32 screens + 3 OLEDs + projector.

**H2b. Raspberry Pi 5 is overkill if the camera is dropped,** but fine as a media player + UI + logger. Keep it.

**H3. Privacy, consent and data protection.**
Camera (facial expression), fingerprint, health scores and physiological data are **sensitive personal data**. Needs ethics-committee approval, informed consent, minimal data, **local storage + encryption**, retention limits, and compliance with India's **DPDP Act, 2023** (check current rules with the hospital's legal team). Fingerprint for ID is heavier than needed; a **patient-ID QR/RFID wristband or bed/patient number** is simpler and less sensitive for a pilot.

**H4. Camera-based emotion → "Prakriti tuning" is the weakest link scientifically.**
Facial expression estimates emotion, not constitution, and generalizes poorly across lighting and populations. The deck itself says *"camera can be replaced by Mobile App"*. **Recommend dropping the camera in Phase 1.**

**H5. Content licensing & audio practicalities.**
- Raga recordings and nature videos are copyrighted; use commissioned/own recordings or properly licensed/CC content.
- **Binaural beats** (listed in the BOM text) need headphones; the 10W speaker can't deliver them. For ward use consider pillow speaker/headphones; a shared room needs consideration for neighbours.
- 24×7 ward: **night volume/brightness limits**, patient/nurse override, and a hard "stop" path.

**H6. Naming/regulatory.** "Nebulizer" in a clinical setting normally means a **drug-delivery inhalation device**, which is regulated as a medical device. Under ethics approval a research prototype is typically fine, but the name and any "healing/treatment" claims can invite scrutiny. Ask the PI/regulatory contact.

### MEDIUM

**M1. Budget sheet inconsistencies**
- Line items as I read them sum to **₹2,27,911**; sheet total is **₹2,27,766** (₹145 off; may be my misreading of a photo).
- "Misc & unforeseen (~12%)" = ₹28,145 is **~14%** of the other lines.
- Non-hardware lines inside a hardware BOM: **Treadmill ₹17,998** (no link to any of the 3 modules) and **₹50,000 student coordinator** (together ~30% of the budget). Confirm they are intended here.
- SD card: image shows **512 GB**, spec and price say **32 GB**.
- Nebulizer row shows two prices (₹722 and ₹850).
- 3D-printed parts: 10 × ₹1,200 = ₹12,000 looks high for brackets/shrouds.

**M2. Timeline unknown.** Handwritten dates unreadable; clinical phases need ethics approval lead time. Confirm hard deadlines.

**M3. Novelty/value check.** The reference product on slide 2 is a ₹2,999 commercial aroma diffuser. The defensible novelty is **personalization + synchronization + adaptation + clinical evidence**, not the diffusion itself.

**M4. Hospital integration.** Mains wiring, EMI, infection control, Wi-Fi in wards, and who maintains/cleans units. Not mentioned anywhere.

**M5. Reliability.** Mist, humidity and 24×7 duty on a Pi + SD card (corruption risk on power cut). Plan read-only root FS/UPS-safe shutdown and a watchdog.

### LOW
- Speaker note on slide 4 **swaps the elements** (says Vata = water+fire, Pitta = air+ether); the slide itself is correct. Fix before the deck circulates.
- Slide numbering/typos ("Medows", "Rosemery", "Prakruti/Prakriti" spelling variants). Standardize terms in UI strings.
- Hindi raga-therapy infographic is **not an evidence source** (e.g. "releases serotonin/dopamine" claims are unsourced), and it recommends *Bhairavi*, not the *Bhairav* in your mapping. Don't mix them.

---

## 9. Ranked recommendations (what I'd do about it)

1. **Freeze the mapping table** with Dr. Vinayak: dosha → pilot oil set, scene set, raga set, time-of-day rules, dual-dosha thresholds (δ₁, δ₂). *Blocks everything else.*
2. **Add a Vikriti/state input** to the concept (even a 3-question mood check in Phase 1).
3. **Bench-test the aroma channel** (nebulizer + diluted oil, fan, run-time, clogging, leaks) before building the other modules. Reuse the Prahalpa prototype.
4. **Redo the power budget** and correct the PSU/LED/buck/battery choices; decide on backup or none.
5. **Simplify compute:** Pi 5 + one microcontroller; drop the redundant ESP32 screens and Arduino-or-ESP32 duplication; drop the camera in Phase 1.
6. **Fix the questionnaire** (randomize options, de-weight hair/eyes, allow dual answers, add Hindi/Marathi, observer section) and have clinicians validate it.
7. **Get clinical/ethics inputs early:** exclusion criteria for aroma, consent flow, data policy, and a control arm in the study design.
8. **Reconcile the BOM/budget** (items in M1) and re-order only what the frozen design needs.

---

## 10. Open questions (in order of how much they block)

**For Dr. Vinayak (Ayurveda/Prakriti)**
1. Final mapping table, including the contradictions in §4.3.
2. Dual/sama thresholds and how to blend (60:40 proportional vs "primary dominates").
3. Which validated Prakriti instrument the questionnaire should follow; is "Faith in God" meant to stay?
4. Does the system also address Vikriti, and how should season/time-of-day influence choices?

**For Ninad / Harshali (technical leads)**
5. Which oils and nebulizer type are actually approved; was the Prahalpa prototype tested with real oils?
6. Is camera/emotion analysis required for Phase I, or can it be deferred?
7. Website stack, database, auth, and who owns it; can I get the code/API access?
8. Is fingerprint mandatory, or is patient ID (QR/RFID/bed no.) acceptable?
9. Why is a treadmill in the budget, and is the ₹50,000 coordinator part of this hardware phase?

**For Seema / Krantee (clinical/admin)**
10. Ethics status, consent form, exclusion criteria for aroma exposure.
11. Control-arm design for Phase II.
12. Deadlines (the handwritten Dec/Feb dates) and the pilot site/ward for Phase I.

**For you to decide**
13. Language(s) for the UI; who sets/changes Prakriti in the UI (team only vs team + patient).
14. Whether Phase I deliverable is a **bench demo** or a **ward-ready** unit. This sets the amount of safety/enclosure work.

---

## Appendix A — Questionnaire facts

- **55 questions**, 3 options each, always in the order **Pitta → Vata → Kapha** (e.g. skin: reddish/fair = Pitta, dark/dusky = Vata, pale/glowing = Kapha).
- **Domains:** Q1–33 physical/physiological, Q34–55 psychological/behavioural.
- Repeated topics: hair (Q3–6), eyes (Q7–8), appetite (Q25–26), liquids (Q30–31), frame (Q19–20).
- Output today: a single "Analyze My Prakruti" result. Needs to return the **score vector** (§5), not just a label.

## Appendix B — Quick power sketch (to be done properly in planning)

| Load | Rough worst case (5 V) | Note |
|---|---|---|
| Raspberry Pi 5 | up to ~5 A (27 W PSU recommended) | official PSU, not GPIO power |
| 7" touchscreen | ~1 A | |
| 3 nebulizer modules | check datasheet (few W each) | not all on continuously |
| 3 fans | 3 × 0.25 A | |
| MCU + sensors + OLED | < 1 A | |
| WS2812B, 1 reel | **18 A at full white** | cap brightness, own supply |
| Projector | own mains adapter | not on the 5 V rail |
| Speaker | own battery/USB | |

## Appendix C — Likely next step: planning phase outline
Once §10 items 1–8 are answered: (1) lock architecture and BOM delta, (2) power & enclosure design, (3) aroma bench validation, (4) profile engine + questionnaire v2, (5) content library (scenes/ragas, licensed), (6) integration + logging + dashboard, (7) safety checklist & pilot protocol, (8) schedule against your deadlines.
