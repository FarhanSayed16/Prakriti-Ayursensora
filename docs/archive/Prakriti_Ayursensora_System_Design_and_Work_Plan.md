# Prakriti Ayursensora — System Design & Work Plan (v1.0, from scratch)

*Nothing is built or ordered yet, so this document **decides** the architecture, the components, the Prakriti calculation, and the work split.*
*Companion to: `Prakriti_Ayursensora_Project_Understanding.md` (problem understanding + flaws found).*

> **Legend.** **[DECISION]** = my recommended choice (change it if you disagree). **[EXPERT]** = must be set by the Ayurveda expert (Dr. Vinayak). **[CLINICAL]** = must be set by the clinical team (Dr. Seema). **[CALIBRATE]** = a number I give only as a starting value; the bench test or data replaces it. Prices are **indicative estimates** (±25%) and must be re-quoted before ordering.

---

## 0. Decisions at a glance (ranked by how much they unblock)

| # | Decision | Recommended default | Why |
|---|---|---|---|
| D1 | **What the system calculates** | Prakriti = **score vector [V,P,K]** from questionnaire + clinician observation; **clinician confirms** final type | Matches dual-dosha need; keeps the human as ground truth in Phase 1 |
| D2 | **Add a second input: Vikriti/state** | Short "how are you now" check at session start | Prakriti is fixed; stress is a current state |
| D3 | **Architecture** | **Raspberry Pi = brain** (profile engine, orchestrator, media), **one ESP32 = real-time aroma/LED actuator**, linked by USB serial; MQTT between software services | Fewer parts, deterministic, works without hospital Wi-Fi |
| D4 | **Aroma technology** | **3 independent ultrasonic mist channels (one per dosha)**, blended by *time-slicing*; bench-test a waterless cold-air nebulizer as fallback (Gate G1) | You already have a working prototype; per-dosha channels give clean dual-dosha blending |
| D5 | **UI** | **Web app on the Pi** (staff phone/tablet), **no onboard touchscreen** in Phase 1 | Saves ₹6k+, avoids multi-display pain; matches the mobile-app idea in the deck |
| D6 | **Drop for Phase 1** | Camera/emotion AI, fingerprint, Arduino Uno R4, L298N, relays, 2× ESP32 touch boards, 18650 pack, power bank, buck converters, network switch, treadmill | Redundant, risky, or unrelated (see §10.3) |
| D7 | **Sensors** | Phase 1: **1× BME680** only. Phase 2: pulse/HRV/GSR for logging, then adaptation | Open-loop personalization first; closed loop only after it works |
| D8 | **Power** | Official Pi 5 PSU + **separate 5V rail** for actuators + hardware e-stop; **no battery backup** in Phase 1 (fail-safe = everything off) | Simplest safe design |
| D9 | **Order in waves** | **Wave 0 bench kit (~₹4–6k) now**, Wave 1 after Gates G0+G1, Wave 2 later | Avoids repeating the "wrong oils/parts" problem |
| D10 | **Study support built in** | Orchestrator has an **`arm` field** (personalized / generic / mismatched) | Lets the clinical trial test whether Prakriti-matching matters |

---

## 1. Problem statement and solution

### 1.1 Problem statement
Patients in hospital rooms, and professionals in high-stress cabins, experience stress, anxiety, and poor sleep. Ambient aids (scent, nature visuals, music) are normally **generic** and **uncontrolled**, so their benefit cannot be measured or tailored. Ayurveda holds that people differ by constitution (**Prakriti**: Vata, Pitta, Kapha, usually a combination) and by their current imbalance (**Vikriti**), and that environments can be chosen to balance them. No system exists that:

1. **captures** a person's Prakriti in a standardized, auditable way,
2. **translates** it (including dual/tri combinations) into **synchronized** aroma, visual, and music settings, and
3. **logs** exactly what was delivered, so the effect can be studied clinically.

### 1.2 Solution statement
**Prakriti Ayursensora** is a ward-deployable unit and software platform with four parts:

| Part | Purpose |
|---|---|
| **Core: Prakriti Engine + Orchestrator** | Collects data (questionnaire, clinician observation, current-state check), computes the [V,P,K] profile, and produces a *session plan* |
| **S1 Aroma system** | Delivers a blended scent using one mist channel per dosha |
| **S2 Display system** | Projects dosha-matched nature scenes (+ optional ambient LED light) |
| **S3 Music system** | Plays dosha-matched ragas, filtered by time of day, at safe volume |

All modules follow **one shared session plan**, are individually testable, and are logged for research.

### 1.3 What success means (Phase 1)
- A clinician can enrol a person, get a **confirmed Prakriti profile** (single, dual, or sama) with scores.
- One button starts a **60–90 min session** in which aroma, scene, and music follow that profile and each other.
- Every session is **logged** (profile used, what ran, for how long, environment readings).
- The unit is **safe and repeatable** on a bench and ready for ethics/safety review.

### 1.4 Non-goals (Phase 1)
Diagnosing disease; real-time "Prakriti from sensors"; facial-emotion AI; cloud dependence; mains-power battery backup; clinical claims of treatment.

---

## 2. Architecture

### 2.1 Block diagram

```
   STAFF DEVICE (phone/tablet)                                  PATIENT ROOM
┌───────────────────────────────┐                  ┌───────────────────────────────────────┐
│ Web app: intake, questionnaire│    Wi-Fi/LAN     │  AYURSENSORA UNIT                     │
│ clinician review, session ctrl│◄────────────────►│  ┌──────────────────────────────┐     │
│ dashboard, export             │                  │  │ Raspberry Pi 5               │     │
└───────────────────────────────┘                  │  │ • FastAPI (profile, session) │     │
                                                   │  │ • Orchestrator (state machine)│    │
                                                   │  │ • MQTT broker (Mosquitto)    │     │
                                                   │  │ • Media player (video+audio) │     │
                                                   │  │ • SQLite DB + logger         │     │
                                                   │  └──┬───────────┬─────────┬─────┘     │
                                                   │     │USB serial │HDMI     │USB audio  │
                                                   │  ┌──▼────────┐  │         │           │
                                                   │  │ ESP32     │  │         │           │
                                                   │  │ actuator  │  │         │           │
                                                   │  │ MOSFETs → │  │         │           │
                                                   │  │ 3 mist ch │  │         │           │
                                                   │  │ fan, LED, │  │         │           │
                                                   │  │ BME680,   │  │         │           │
                                                   │  │ safety I/O│  │         │           │
                                                   │  └───────────┘  │         │           │
                                                   └─────────────────┼─────────┼───────────┘
                                                         S1 AROMA    ▼ S2 DISPLAY  ▼ S3 MUSIC
                                                                  Projector/TV   Speaker/headphones
```

### 2.2 Responsibilities

| Block | Runs on | Owns | Does **not** do |
|---|---|---|---|
| Profile service | Pi (FastAPI) | Questionnaire, scoring, clinician review, Prakriti/Vikriti records | Control hardware |
| Orchestrator | Pi (Python service) | Session state machine, builds the session plan, safety gating, logging | Decode video, drive pins |
| Aroma bridge + firmware | Pi ↔ ESP32 | Pulse scheduling safety, PWM, interlocks, sensor read | Decide *what* to blend |
| Media player | Pi (mpv) | Play scenes and ragas per plan, crossfade, volume cap | Decide *what* to play |
| Web app | Browser | Staff UI | Business logic |

### 2.3 Software stack **[DECISION]**
- **OS:** Raspberry Pi OS (64-bit, Bookworm), services under `systemd`, hardware watchdog on.
- **Backend:** Python + **FastAPI**, **SQLite (WAL mode)** (move to PostgreSQL only if you go multi-unit/cloud), SQLAlchemy/SQLModel.
- **Messaging:** **Mosquitto** (MQTT) on the Pi; JSON payloads.
- **Media:** **mpv** (controlled over its JSON IPC socket); **ffmpeg** to normalize loudness and encode content.
- **Firmware:** ESP32 with **Arduino framework + PlatformIO** (C++), JSON-lines over USB serial.
- **Frontend (ranked):** (1) server-rendered pages (FastAPI + Jinja/HTMX) for questionnaire + staff console: fastest, works on any device, easy translations; (2) Flutter later for a polished dashboard/mobile app if you want it.
- **Existing website:** adopt it only if its stack is easy to extend; otherwise treat it as the **content source** (questions, options) and rebuild scoring in the new service. *(I have not seen its code.)*

---

## 3. Core: the Prakriti Engine (how Prakriti is actually found)

### 3.1 Principle
- **No sensor can measure Prakriti.** Ground truth in Ayurveda is a **trained physician's assessment** (history + observation + examination). The system **estimates** and **records**; the clinician **confirms**.
- Classical framework to follow **[EXPERT to confirm]:** *Dashavidha Pariksha* (ten-fold examination, which starts with Prakriti and Vikriti) and *Trividha/Ashtavidha Pariksha* (inquiry, observation, touch; plus pulse, tongue, voice, etc.). Prakriti is described in Charaka Samhita (Vimana Sthana 8). *Chapter references from memory: verify.*
- Also note **Manasika Prakriti** (mental constitution: Sattva/Rajas/Tamas). Several "psychological" questionnaire items (ego, patience, faith, gratitude) arguably describe **mental guna**, not dosha. Ask the expert whether to keep them in the dosha score or score them separately.

### 3.2 Three data sources

| Source | Who fills | When | Used for |
|---|---|---|---|
| **A. Prakriti questionnaire v2** (self-report) | Patient (assisted if needed) | Enrolment, once | Initial V/P/K score |
| **B. Clinician observation sheet** | Ayurveda physician/trained staff | Enrolment, once | Corrects/overrides A; **gold standard** |
| **C. Current-state (Vikriti) check** | Patient/nurse | **Every session** | Intensity/variant choice; not Prakriti |

**Questionnaire v2 rules (fixes from the understanding doc)**
1. Wording: *"Throughout most of your life, **before** the current illness…"* (so acute illness doesn't pollute Prakriti answers).
2. **Randomize option order** per respondent; store the mapping server-side.
3. Allow **"both apply"** (split vote) and **"not sure"** (no vote).
4. Remove near-duplicate items (hair ×4 → 2; eyes ×2 → 1) and fix non-discriminating options.
5. Physical items that are **observable** (veins, tongue, teeth, chest, frame) move to the **clinician sheet** (B).
6. Languages: English, Hindi, Marathi, with back-translation check.
7. Domain tags on every item: `physical`, `physiological`, `psychological` (and `manasika` if the expert separates it).

**Clinician sheet (B) fields** **[EXPERT]:** body frame, skin (texture/temperature), hair, eyes, tongue, voice, pulse impression, appetite/digestion, sleep pattern, and the clinician's own **V/P/K percentage estimate** (sums to 100) plus **final Prakriti label** and confidence.

**Current-state check (C)** (≤2 minutes) **[EXPERT + CLINICAL]:** sleep last night, appetite today, bowel, anxiety/agitation now (0–10), body heat/cold, heaviness/lethargy, pain. Produces a **Vikriti vector** `[Vv, Vp, Vk]`. Optional scales for the study (PSS, HADS, PSQI) are recorded separately as **outcomes**, not used by the engine.

### 3.3 Scoring math

**Step 1: item votes.** Each answered item *i* gives a vote vector `e_i` over (V,P,K): a normal pick = (1,0,0)-style; "both apply" = e.g. (0.5,0.5,0); "not sure" = skipped.

**Step 2: weighted domain scores.**

```
S_dom = Σ(w_i · e_i) / Σ(w_i)        for items i in each domain
S_self = Σ_dom (W_dom · S_dom)       W_dom set by the expert (default: equal per item, i.e. proportional to item count)
```
Item weights `w_i` default to 1; set a duplicate item's weight to 0.5 or 0 if the expert says so.

**Step 3: combine with the clinician's estimate.**
```
S_final = α · S_clin + (1 − α) · S_self      default α = 0.7 [EXPERT/CALIBRATE]
```
If the clinician enters a **final label**, that label **wins**; the scores still drive blend weights. If there's no clinician input yet, the system shows **"Provisional (self-report only)"**.

**Step 4: classify** (sort `s1 ≥ s2 ≥ s3`): **[CALIBRATE] starting values, to be tuned on real data**

```
Sama (tri):  s1 − s3 ≤ 0.12
Single:      else if s1 − s2 ≥ 0.15
Dual (s1+s2): otherwise      (primary = s1, secondary = s2)
```

**Step 5: blend weights for modules.**
```
Single: w = (1.0 on that dosha)
Dual:   w1 = s1/(s1+s2), w2 = s2/(s1+s2), third = 0   → round to nearest 5%
Sama:   w = (1/3, 1/3, 1/3)
Rule:   any weight < 20% is dropped and the rest renormalized (avoids token doses)
```

**Step 6: confidence flags** (shown to the clinician):
- `low_margin` if `s1 − s2 < 0.05` (type is on a boundary),
- `many_skipped` if > 20% items unanswered,
- `self_vs_clin_mismatch` if the self-report top dosha ≠ clinician's,
- `unstable` if the label changes when 10% of items are randomly dropped in >20% of 200 resamples (cheap Monte-Carlo stability check).

### 3.4 Worked example

55 answered items: V = 21, P = 24, K = 10 → `S = [V 0.382, P 0.436, K 0.182]`.
Sorted: P 0.436, V 0.382, K 0.182. Sama? `0.436 − 0.182 = 0.254 > 0.12` → no. Single? `0.436 − 0.382 = 0.054 < 0.15` → no. ⇒ **Dual: Pitta-Vata**, primary Pitta, flag `low_margin`.
Blend: `w_P = 0.436/0.818 = 0.533 → 55%`, `w_V = 45%`, `w_K = 0`.
Session plan: aroma channel P gets ~55% of the mist-on budget and V ~45%; scenes alternate Pitta (mountains + water) and Vata (lush forest) in a 55:45 time share; ragas drawn from both pools, time-of-day filtered.

### 3.5 Validation plan (this is how "exact calculation" becomes trustworthy)
1. **Collect n ≈ 300** (matches the 300 consent/forms already budgeted): each participant gets A (self) + B (clinician) and, ideally, **two independent clinicians** for B on a subset (inter-rater agreement).
2. Compare engine label vs clinician label: **weighted Cohen's κ**, confusion matrix across all 7 types, and mean absolute error of the V/P/K percentages.
3. **Tune** `W_dom`, `α`, thresholds on a training split; **report on a held-out split**.
4. Optional (only after step 3 plateaus): regularized multinomial logistic regression or gradient boosting on item votes → score vector; keep the rule engine as the explainable fallback.
5. **Freeze a scoring version** (`scoring_v1.0`) and store it with every result so old sessions stay reproducible.

### 3.6 Data model (SQLite)

| Table | Key fields |
|---|---|
| `patients` | `patient_code` (pseudonym), `age_band`, `language`, `consent_id`, `created_at` (**no name/phone**; hospital keeps the identity map) |
| `consents` | `version`, `signed_at`, `scope` (data, aroma, media), `withdrawn_at` |
| `questionnaire_responses` | `patient_id`, `form_version`, `item_id`, `shown_order`, `answer`, `answered_at` |
| `clinician_assessments` | `patient_id`, `assessor_id`, `v/p/k_pct`, `final_label`, `confidence`, `notes` |
| `prakriti_results` | `patient_id`, `scoring_version`, `s_self`, `s_clin`, `s_final`, `label`, `blend_w`, `flags`, `confirmed_by` |
| `vikriti_checks` | `session_id`, answers, `vikriti_vec` |
| `sessions` | `patient_id`, `arm`, `plan_json`, `start/end`, `stop_reason`, `operator_id` |
| `events` | `session_id`, `ts`, `module`, `type` (e.g. `mist_pulse`, `scene_start`, `track_start`), `payload` |
| `env_readings` | `session_id`, `ts`, `temp`, `rh`, `gas_res` |
| `outcomes` | `patient_id`, `timepoint`, `scale` (PSS/HADS/PSQI), `score` |
| `content_catalog` | `content_id`, `type` (scene/track), `dosha_tags`, `time_tags`, `version`, `license`, `loudness/brightness` |
| `oil_catalog` | `oil_id`, `name`, `botanical`, `dosha_tag`, `cautions[]`, `batch`, `coa_ref` |
| `audit_log` | who did what and when |

---

## 4. S1: Aroma system

### 4.1 Function
Deliver a controlled, **blended** scent: one mist channel per dosha; blend by **time-sliced pulses** so dual/sama profiles are just different time shares.

### 4.2 Hardware design
```
 [Cup V] ─disc→ ┐
 [Cup P] ─disc→ ├─ silicone tubes → [Mixing plenum] → [Fan] → outlet
 [Cup K] ─disc→ ┘      (WET ZONE)                           ▲
                                                             │ 5V PWM
 ─────────── barrier (sealed) ─────────────────────────────────────────
 ESP32 → 3× MOSFET (mist V/P/K) + 1× MOSFET (fan PWM) + BME680 + float switches + leak sensor
                                   (DRY ZONE)
```
- **Per-channel cup (≈100 ml)**: removable, pre-filled by staff/pharmacy and **labelled with oil, batch, date; replace every 24 h** (hygiene). Float switch in each cup **cuts the disc if the level is low** (dry-running kills piezo discs).
- **Wet/dry separation:** electronics in a sealed compartment above/behind; **drip tray + leak sensor** under the wet zone.
- **Mist path:** short silicone tubes (≈8 mm ID) into a small plenum; one **5V fan** pushes mist out; run the fan *before and after* each pulse to clear tubes.
- **Oils:** only the **expert-frozen pilot set** (see §10). Suggest **1–2 oils per dosha** so each channel is a single, known oil; the "blend" happens in the room.

### 4.3 Technology gate G1 (bench test before buying Wave 1)
Test three options with identical protocol (**Bench Test BT-1**):

| Option | Idea | Pros | Cons |
|---|---|---|---|
| **A1 (default)** | Ultrasonic disc, oil floating in water | Quiet, cheap, you have a working prototype | Oil dose imprecise; residue; water hygiene |
| **A2** | A1 with an oil solubilizer in the water | More uniform | **Inhaled surfactant safety must be cleared clinically** |
| **B** | Waterless cold-air (Venturi) glass nebulizer + small air pump | No standing water; dose by pulse time | Louder, higher oil use and aerosol concentration, parts harder to source |

**Pass criteria (targets) [CALIBRATE with Dr. Seema]:** repeatable mass loss per pulse (e.g. ±15%), no clogging over a 72-hour soak, noise ≤ ward limit, scent perceptible but not strong at bed distance, VOC trend visible on the BME680, no leaks. **Pick A1 unless it fails; B is the fallback.**

### 4.4 Pulse scheduler (runs in the orchestrator; firmware enforces limits)
```
Cycle T = 15 min [CALIBRATE]; mist-on budget B per cycle = (intensity level L1/L2/L3 → e.g. 30/60/90 s) [CALIBRATE]
channel_time_i = w_i · B
split each channel_time into pulses ≤ 12 s; interleave channels (primary first)
fan: on 5 s before each pulse, off 20 s after
```
Intensity level (L1–L3) is set by the nurse per patient; default L1 for the first session.

### 4.5 Firmware safety rules (hard-coded in ESP32, not overridable by software)
1. Max **continuous** mist-on per channel: 20 s; max total mist-on per hour: set by **[CLINICAL]**.
2. **Mist only if the fan is running.**
3. **Dead-man heartbeat:** no valid heartbeat from the Pi for 10 s → all outputs off.
4. Low-water (float) or leak detected → channel/all off + error event.
5. Temperature or humidity out of range (BME680) → pause.
6. **Hardware e-stop** physically breaks the 5V feed to the mist/fan MOSFET rail (works even if the MCU hangs).
7. On boot: everything **off** until the Pi sends an explicit start.

### 4.6 Proposed ESP32 pin plan (WROOM-32 38-pin; avoids strapping/flash pins)

| Function | GPIO | Note |
|---|---|---|
| Mist V / P / K MOSFET gates | 25 / 26 / 27 | active-high, 10 kΩ pull-down |
| Fan PWM MOSFET gate | 18 | LEDC ~25 kHz |
| WS2812B data (via 74AHCT125) | 13 | optional |
| I²C SDA / SCL (BME680, OLED) | 21 / 22 | |
| Float switches V / P / K | 32 / 33 / 35 | 35 is input-only: add external pull-up |
| Leak sensor (analog) | 36 | |
| E-stop state sense / pause button | 4 / 5 | for logging; hardware cut is independent |
| Status LED | 2 | |

### 4.7 Deliverables & acceptance (S1)
- Firmware with command set: `mist(ch, ms)`, `fan(pct)`, `led(...)`, `status`, `stop`, heartbeat; unit-tested with a serial simulator.
- Wet/dry enclosure prototype passes a **leak test** and **24 h soak**.
- BT-1 report (technology pick + calibrated numbers).

---

## 5. S2: Display system

### 5.1 Function
Play dosha-matched, slow, calm nature scenes, with an optional ambient light layer; blend by **time share** for dual profiles.

### 5.2 Components **[DECISION]**
- **Output:** Pi 5 micro-HDMI → **projector** (Portronics Beem 470 as candidate) or **TV/monitor** (fallback). **Test in a real, lit ward before buying**: consumer 720p/1080p LED projectors wash out in ambient light; if it fails, a 32–43" TV on the wall is more reliable.
- **Player:** `mpv --vo=drm` (no desktop session needed), fullscreen, looped playlists, crossfades via scene fade-in/out. **Pi 5 has hardware HEVC decode but no H.264 hardware decode**: 1080p30 H.264 plays fine in software on the 4 cores; test CPU load and temperature over 8 h.
- **Ambient LED (optional):** **1 m WS2812B, 60 LED/m** (not two 5 m reels), brightness capped at ~25% in firmware (worst case 60 LEDs × 60 mA = 3.6 A; at 25% ≈ 0.9 A). Colour palette per dosha **[EXPERT]**. Add 330 Ω data resistor, 1000 µF cap, level shifter.

### 5.3 Content spec
| Item | Spec |
|---|---|
| Format | H.264 or H.265 MP4, 1920×1080, 24/30 fps, no hard cuts, slow camera or static, no text/logos |
| Length | 10–20 min seamless loops; **≥4 scenes per dosha** so a 90 min session doesn't repeat |
| Scene set (from deck) | Kapha: flowing water, meadows; Pitta: mountains + water bodies; Vata: lush forest **[EXPERT to confirm; see understanding doc §4.3]** |
| Metadata | `dosha_tags`, `time_tags` (day/evening/night), brightness level, motion level, `license` |
| Night variants | Dimmer/warmer grading; **no bright flashes** |
| Sourcing (ranked) | (1) Own/partner shoot (full rights, controlled); (2) commissioned stock with clinical-use licence; (3) CC0/free-licence libraries (check terms). Keep **an immutable, versioned content library** for the study |

### 5.4 Deliverables & acceptance (S2)
- `display-service`: subscribes to `ayur/display/cmd`, publishes status, resumes after reboot.
- 24 h soak: no dropped-frame spikes, CPU temperature in safe range.
- Brightness/lux at bed distance measured and logged (cheap lux meter).

---

## 6. S3: Music system

### 6.1 Function
Play raga-based music matched to the profile, **filtered by time of day**, at a clinically safe, capped volume; blend dual profiles by weighted selection.

### 6.2 Components **[DECISION]**
- **Audio out:** **Pi 5 has no 3.5 mm jack.** Use a **USB audio adapter** → powered speaker (the 10 W unit from the old BOM works) **and** a 3.5 mm splitter for **patient headphones/pillow speaker** option. Bluetooth is *not* recommended for the ward (pairing/reconnect failures).
- **Player:** mpv (shared with display service or separate instance), **8–10 s crossfade**, loudness-normalized tracks, hard software volume ceiling.
- **Binaural beats:** only if **headphones** are used (needs stereo isolation); treat as an optional experiment, not core.

### 6.3 Content spec
| Item | Spec |
|---|---|
| Ragas (from deck) | Kapha: Bhairav, Jaunpuri, Hamsadhwani, Desh; Pitta: Miyan ki Todi, Malkauns, Marwa, Durga; Vata: Bhimpalasi, Bageshree, Yaman, Kafi **[EXPERT]** |
| Form | Slow, sparse instrumental (e.g. flute, sitar, sarod, santoor, veena); minimal percussion, no abrupt dynamics; 20–30 min per piece |
| Time tags | Each raga tagged with its **prahara** (time window) from the expert; the player only picks tracks valid at the current time (or the expert-approved exceptions) |
| Loudness | Normalize with `ffmpeg loudnorm` to one target (e.g. −23 LUFS) **[CALIBRATE]**; software cap and a **night cap** **[CLINICAL]** |
| Rights | Own recordings or commissioned/licensed; **check public-performance licensing** (e.g. IPRS/PPL) for hospital playback |
| Evidence note | The Hindi "Raga Chikitsa" infographic is **not** a source for the mapping; use the expert's table |

### 6.4 Selection algorithm
```
pool = tracks where (dosha ∈ profile with w>0) AND (time_tag matches now) AND NOT recently_played
choose dosha d with probability w_d, then a random track from d's pool
crossfade; log track_id, start, end
```

### 6.5 Deliverables & acceptance (S3)
- `music-service`: plan-driven playlist, crossfade, volume ceiling, resume after reboot.
- Measured SPL at pillow position within the clinical limit (cheap SPL meter).

---

## 7. Integration

### 7.1 Session state machine (orchestrator)

```
IDLE → INTAKE (patient + consent) → PROFILE_READY (clinician-confirmed) → PRECHECK
      → RAMP_IN (5–10 min) → RUNNING → WIND_DOWN → ENDED
                  ↘ PAUSED (nurse / e-stop) ↗        ↘ FAULT (any module) → SAFE_OFF
```
- **PRECHECK:** cups present and labelled, water level OK, no leak, BME680 alive, media library OK, patient contraindication flags OK.
- **Fault policy:** a failed module **does not stop the others** (e.g., aroma fault → music and display continue), but **aroma faults always force aroma off**.

### 7.2 Session plan (the shared contract)

```json
{
  "session_id": "S-2026-000123",
  "patient_code": "P-0457",
  "arm": "personalized",
  "profile": { "type": "dual", "primary": "P", "secondary": "V",
               "weights": { "V": 0.45, "P": 0.55, "K": 0.0 },
               "scoring_version": "scoring_v1.0",
               "vikriti": { "V": 0.2, "P": 0.5, "K": 0.3 } },
  "context": { "start": "2026-11-10T19:30:00+05:30", "time_bucket": "evening", "season": "autumn" },
  "intensity": "L1",
  "duration_min": 60,
  "contraindications": ["avoid:peppermint"],
  "aroma":   { "cycle_min": 15, "budget_s": 30, "channels": { "P": 0.55, "V": 0.45 } },
  "display": { "segments": [ {"t": 0,  "scene_pool": "P"}, {"t": 20, "scene_pool": "V"} ] },
  "music":   { "pools": { "P": 0.55, "V": 0.45 }, "volume_cap": 0.35, "crossfade_s": 8 }
}
```

### 7.3 MQTT topics

| Topic | Direction | Payload |
|---|---|---|
| `ayur/session/start` / `stop` / `pause` | Orchestrator → all | session plan / reason |
| `ayur/aroma/cmd` / `status` | Orchestrator ↔ aroma bridge | commands, firmware state, faults |
| `ayur/display/cmd` / `status` | Orchestrator ↔ display service | scene, state |
| `ayur/music/cmd` / `status` | Orchestrator ↔ music service | track, volume, state |
| `ayur/env/reading` | Aroma bridge → logger | temp, RH, gas_res |
| `ayur/safety/event` | Any → orchestrator | estop, leak, lowwater, overheat |

**Serial link (Pi ↔ ESP32):** newline-delimited JSON, with sequence numbers, acks, and a 1 Hz heartbeat. Example: `{"seq":42,"cmd":"mist","ch":"P","ms":8000}` → `{"ack":42,"ok":true}`.

### 7.4 Synchronization
Modules are **loosely coupled**: they share a **session clock** and a **plan**, not tight timing. The orchestrator aligns *narrative* events (e.g., scene change and raga change at the same segment boundary); aroma pulses run on their own cycle.

### 7.5 Study support
`arm ∈ {personalized, generic, mismatched}`; the orchestrator selects the profile used for delivery accordingly (generic = a fixed expert-approved relaxing set; mismatched = a deliberately different dosha). Assignment is stored in `sessions.arm`; blinding is the research team's job.

---

## 8. Power, enclosure, safety

### 8.1 Power
| Domain | Supply | Feeds |
|---|---|---|
| A | **Official Raspberry Pi 5 27 W USB-C PSU** | Pi 5 (+ USB audio, USB serial to ESP32) |
| B | **5 V / ~6–7 A enclosed SMPS** (certified, e.g. Mean-Well-class), fused | ESP32, mist modules, fan, LED strip (capped), BME680, OLED |
| C | Own mains adapters | Projector/TV, speaker |

- Mains enters through a **fused IEC inlet with switch**; mains wiring is **fully separated** from low-voltage by insulated barrier; metal parts earthed.
- **No** relays for DC loads, **no** 18650 pack, **no** power-bank UPS in Phase 1.
- Build a **power budget sheet** (every load, worst case) and have a **qualified electrician / hospital biomedical engineer review it** (Gate G2).
- Power cut = all outputs off; unit boots to IDLE; a session never auto-resumes aroma without a nurse's confirmation.

### 8.2 Enclosure
- Two zones (wet/dry), sealed cable glands, wipeable surfaces, vents only on the dry side, filtered air path; **no open flame/heating elements**.
- Material: laser-cut acrylic is fine for prototype; for the ward, prefer **3D-printed PETG** parts or sheet ABS that tolerate cleaning agents. Size ≈ 300 × 200 × 150 mm as a starting envelope **[CALIBRATE after bench layout]**.
- Controls on the unit: big **PAUSE** and **E-STOP**, a small status OLED, and nothing else.

### 8.3 Clinical/hygiene safety **[CLINICAL]**
Exclusion criteria (infants/young children, asthma/COPD, pregnancy, epilepsy, allergies, shared rooms), consent, daily cup swap and cleaning SOP, incident log, physician-controlled on/off.

### 8.4 Cybersecurity and privacy
Local-only by default; HTTPS on the local network; role-based logins (clinician/technician/admin); audit log; pseudonymous patient codes; encrypt the data partition; export only de-identified CSV; comply with **DPDP Act 2023** (confirm current rules with the hospital's legal team).

---

## 9. Work division

### 9.1 Workstreams

| WS | Name | Scope | Main skills | Suggested owner* |
|---|---|---|---|---|
| **WS-A** | S1 Aroma | Bench test BT-1, firmware, wet/dry design, calibration | Embedded, mechanical | You (hardware lead) |
| **WS-B** | S2 Display | Player, scene library, projector/TV test, LED layer | Linux, video | You (software) + content support |
| **WS-C** | S3 Music | Player, track library, rights, SPL | Audio, Linux | You (software) + expert for content |
| **WS-D** | Core software | Profile engine, questionnaire v2, FastAPI, orchestrator, DB, web UI, dashboard | Backend, frontend | You + Harshali (ML/analysis) |
| **WS-E** | Prakriti knowledge & data | Mapping table, thresholds, clinician sheet, translations, data collection | Ayurveda | Dr. Vinayak (+ Dr. Krantee) |
| **WS-F** | Clinical & ethics | Exclusion criteria, consent, safety limits, study arms, outcome scales | Clinical | Dr. Seema (+ Om for evaluation) |
| **WS-G** | Integration, power & enclosure | Power budget, wiring, enclosure, soak and safety tests | Hardware | You + a technician/electrician |

*\*Placeholders based on the roles in the deck; confirm availability. You are the single integrator.*

### 9.2 Interfaces between workstreams (what each must give the others)
- **WS-E → WS-D/A/B/C:** frozen mapping table (dosha → oils, scenes, ragas, time tags, thresholds).
- **WS-D → all modules:** the session plan JSON and MQTT contract (§7).
- **WS-A → WS-D:** firmware command set and its safety limits.
- **WS-F → WS-D/A:** contraindication list and exposure limits.
- **WS-B/C → WS-D:** content catalog with metadata.

### 9.3 Gates

| Gate | Condition | Unblocks |
|---|---|---|
| **G0** | Mapping table + dual rule + pilot oil set frozen by the expert | Wave 1 order, content production |
| **G1** | Aroma technology picked from bench test BT-1 | Enclosure design, final aroma parts |
| **G2** | Power/safety design reviewed | Building the mains-powered unit |
| **G3** | Integrated bench demo (3 modules from one plan) | Enclosure finalization, internal trial |
| **G4** | Ethics approval + clinical safety sign-off | Any use with patients |

### 9.4 Effort and timeline (rough; assumes you are the main builder)

| Track | Person-days (range) |
|---|---|
| WS-D Core software | 20–30 |
| WS-A Aroma (incl. bench test + firmware + wet/dry) | 15–25 |
| WS-B Display | 4–7 (+ content) |
| WS-C Music | 4–7 (+ content) |
| WS-G Integration/enclosure/tests | 10–15 |
| **Total build effort** | **≈ 55–85 person-days** |

Relative schedule (adjust to your real deadlines, which I couldn't read on the handwritten page):

| Weeks | Do |
|---|---|
| **0–1** | Decisions D1–D10; send expert the mapping questions; order **Wave 0 bench kit**; draft consent/ethics docs; start questionnaire v2 |
| **1–3** | BT-1 aroma bench test; core skeleton (FastAPI, MQTT, DB); scoring prototype; start paper/digital data collection (**long pole**, needs ethics) |
| **3–4** | **G0 + G1** → order **Wave 1**; content production begins |
| **4–7** | ESP32 firmware; media services; orchestrator; web UI; plan JSON end to end |
| **7–9** | Integrated bench demo (**G3**); enclosure build; power review (**G2**) |
| **9–11** | Calibration, soak tests, leak tests, content freeze |
| **11–14** | Safety documentation, clinician walkthrough, pilot-readiness (**G4** dependent) |

---

## 10. Component list v2 (decided from scratch)

### 10.1 Wave 0, bench kit (order now, ≈ ₹4–6k)
| Qty | Item | Spec / note |
|---|---|---|
| 2 | Ultrasonic mist module | 5 V, piezo disc + driver; **record the datasheet current/frequency** (the old BOM calls it 2.4 MHz; confirm) |
| 1 | ESP32 DevKitC 38-pin | CP2102/CH340, USB |
| 3 | Logic-level MOSFET module | ≥ 2 A at 5 V, gate drive OK from 3.3 V |
| 2 | Float switch (small, vertical, PP) | Low-water cut |
| 1 | Water/leak sensor strip | analog |
| 1 | Silicone tubing 8 mm ID ×1 m + connectors | |
| 3 | 100 ml cups/containers (PP or glass) | candidate reservoirs |
| 1 | 5 V 4-pin PWM fan (80 mm) | or 5015 blower for BT-1 |
| 1 | Precision scale 0.01 g | **mass-per-pulse** dose measurement |
| 1 | Starter oils (2–3, pilot candidates) + distilled water | only what the expert approves for testing |
| 1 | Breadboard + jumpers + 5 V/3 A bench supply | |

### 10.2 Wave 1, core build (after G0 + G1) ≈ ₹50–65k
| Group | Item | Qty | Spec / reason | Est. ₹ |
|---|---|---|---|---|
| **Compute** | Raspberry Pi 5 **4 GB** (8 GB only if you want on-device ML later) | 1 | The old BOM's 8 GB at ₹19,618 looks high; re-quote | 7–12k |
| | Official Pi 5 27 W PSU | 1 | proper 5 V/5 A negotiation | 1.3k |
| | Pi 5 Active Cooler | 1 | replaces "heatsink + paste" | 0.5k |
| | microSD **128 GB high-endurance** | 2 | media library is ~20+ GB; the old 32 GB is too small. *Upgrade path: NVMe HAT + SSD* | 2k |
| | micro-HDMI → HDMI cable (1.5–2 m) | 1 | Pi 5 uses micro-HDMI | 0.4k |
| | USB stereo audio adapter | 1 | Pi 5 has no headphone jack | 0.4k |
| | Pi 5 RTC battery | 1 | timestamps survive power loss | 0.3k |
| **Aroma** | ESP32 DevKitC (+ spare) | 2 | one controller | 0.7k |
| | Ultrasonic mist modules (3 + 1 spare) | 4 | per G1 result | 3.4k |
| | MOSFET modules / discrete IRLZ44N set | 6 | 3 mist, 1 fan, 2 spare | 0.6k |
| | Float switches | 4 | 3 cups + spare | 0.6k |
| | Leak sensor + drip tray | 1 | | 0.5k |
| | 5 V 4-pin PWM fans (80 mm) | 2 | 1 + spare | 0.5k |
| | Silicone tubing, T/Y connectors, grommets | set | wet-zone plumbing | 0.8k |
| | Cups/cartridges (final design) | 6 | 3 in use + 3 spare for 24 h swap | 1.5k |
| | Pilot essential oils (expert set) with **batch COA** | per set | **do not buy until G0** | 3–6k |
| | Amber glass dropper bottles 30 ml | 9 | keep from old BOM | 0.1k |
| | Distilled water, labels, pipettes | set | | 0.5k |
| **Sensing / UI** | BME680 breakout | 1 | temp/RH/VOC log | 0.9k |
| | SSD1306 OLED 0.96" | 1 | status | 0.2k |
| | Pause + E-stop buttons (latching, NC) | 2 | | 0.6k |
| **Display** | Projector (Beem 470 candidate) **or** TV 32–43" | 1 | buy after ward-light test | 8k / 12–18k |
| | Wall/ceiling mount or tripod | 1 | | 1k |
| | WS2812B 60 LED/m, **1 m**, + resistor, cap, 74AHCT125 | 1 | optional layer | 1k |
| **Music** | Powered speaker 10 W (aux-in) | 1 | from old BOM | 0.5k |
| | 3.5 mm splitter + wired headphones | 2 | patient option | 0.8k |
| **Power** | 5 V 6–7 A enclosed SMPS | 1 | actuator rail | 0.8k |
| | Fused IEC inlet w/ switch + mains lead | 1 | | 0.5k |
| | Inline fuse holder + fuses, terminal blocks, ferrules | set | | 0.5k |
| **Mechanical** | Enclosure (acrylic/PETG) + 3D-printed cup holders/plenum | 1 | design after BT-1 | 4–8k |
| | Screws, standoffs, glands, heat-shrink, silicone wire, JST | set | | 1.5k |
| | Perfboard "controller carrier" (PCB later) | 2 | | 0.3k |
| **Test gear** | Lux meter, SPL meter (or calibrated app + mic), multimeter | 1 each | verify brightness, loudness, supply | 2–3k |
| **Content** | Scene library + raga recordings | — | **budget separately** (shoot/licence/musician fees) | TBD |

### 10.3 Old BOM v1 → what happens (items 1–47)

| v1 item | Action | Reason |
|---|---|---|
| 1 RPi5 8 GB | **Change** → 4 GB (re-quote) | 8 GB not needed without on-device ML |
| 2 Arduino Uno R4 WiFi | **Drop** | ESP32 does the job; redundant MCU |
| 3 L298N | **Drop** | 2 channels only, voltage drop, wrong for PWM fans → MOSFETs |
| 4 ESP32 5" touch ×2 | **Drop** | web UI replaces |
| 5 ESP32 dev | **Keep** (×2) | |
| 6 microSD 32 GB ×2 | **Change** → 128 GB high-endurance | capacity |
| 7 Nebulizer ×3 | **Keep (×4)** after BT-1 | |
| 8 Fan ×3 | **Change** → 1–2 PWM fans | one outlet |
| 9 Relays ×2 | **Drop** | DC loads → MOSFET |
| 10–13 Oils (4 types ×10) | **Hold** | replace with expert-frozen set |
| 14 Bottles | Keep | |
| 15 Fingerprint | **Defer** (Wave 2, optional) | consent/biometric load; use patient code |
| 16 Camera | **Drop** for Phase 1 | weak science, privacy |
| 17 7" touchscreen | **Drop** (optional later) | web UI |
| 18 SMPS 5 V/10 A | **Change** → Pi PSU + 5 V 6–7 A | see §8.1 |
| 19 Power bank, 20 18650, 21 bucks | **Drop** | safety, complexity |
| 22 Projector | **Candidate**, buy after ward test | |
| 23 Speaker | Keep | |
| 24 WS2812B 2 reels | **Change** → 1 m | current |
| 25 OLED ×3 | **Change** → 1 | I²C address limits |
| 26 MAX30100 | **Drop** | MAX30102 supersedes |
| 27 BME680 ×2 | **Change** → 1 | |
| 28 GSR, 29 MAX30102 | **Defer** (Wave 2) | open loop first |
| 30–33 Enclosure/3D/ABS/heatsinks | **Redesign** after BT-1 | |
| 34–35 Jumpers/breadboard | Keep (1 each) | |
| 36 PCB (₹2,500) | **Defer** to Phase 2 | prototype on perfboard |
| 37–38 Solder kit/cables | Check what you already own | |
| 39 Network switch | **Drop** → Pi hotspot or a small travel router | one device |
| 40–44 Forms/stationery/software/transport/dissemination | Keep | |
| 45 Contingency | Recompute on the new total | |
| 46 Treadmill | **Remove** unless justified | unrelated to the 3 modules |
| 47 Student coordinator ₹50,000 | **Move** to a personnel line, confirm | not hardware |

### 10.4 Wave 2 (only after the open-loop system works)
MAX30102 (1), GSR (1) for **logging**, or a **BLE chest strap** (e.g. Polar H10-class) for proper HRV; RFID reader + tags or fingerprint for ID; PCB spin; UPS HAT for graceful shutdown; second unit for a parallel pilot.

### 10.5 Budget view
Hardware **Wave 0 + Wave 1 ≈ ₹55–70k** (excl. content), compared with ≈ ₹1.3 lakh of hardware lines in the old sheet. Content creation/licensing and clinical costs are **separate** line items.

---

## 11. Test plan (acceptance)

| Test | Pass condition |
|---|---|
| **BT-1** Aroma bench | Repeatable mass per pulse, no clog in 72 h, no leak, noise within limit |
| **Firmware safety** | Heartbeat loss, low water, leak, over-temp, e-stop each force the right outputs off (simulate every case) |
| **Scoring unit tests** | Known answer sets give expected labels/weights; thresholds config-driven |
| **Scoring validation** | κ vs clinician labels reported (target set by the expert/PI) |
| **Plan → modules** | Same plan JSON drives all three; modules can be run individually |
| **Soak** | 72 h continuous mist/media cycle, no crash, CPU temp OK, DB intact |
| **Power-cut** | Hard power loss at random moments: unit returns to IDLE, DB uncorrupted, no mist on boot |
| **Ward environment** | Lux at bed, SPL at pillow, VOC trend, temperature/humidity in range |
| **Usability** | Nurse completes enrol → session start in ≤ 5 min after one demo |

---

## 12. Risks and open decisions

| # | Risk / open item | Mitigation / owner |
|---|---|---|
| 1 | Mapping table and thresholds not frozen | **G0**; ask Dr. Vinayak this week |
| 2 | Oils/aroma tech may fail bench test | G1 with fallback B; small Wave 0 purchase |
| 3 | Clinical safety limits undefined | Dr. Seema before any ward use (**G4**) |
| 4 | Ethics approval timing delays data collection | Start docs in week 0; collect questionnaire-only data first |
| 5 | Content rights/licensing | Decide own-shoot vs licence early; budget line |
| 6 | Single-builder bottleneck | Keep modules decoupled; offload content, translation, data entry |
| 7 | Hospital network restrictions | Pi hotspot / travel router; no cloud dependency |
| 8 | Unknown deadlines (handwritten dates unreadable) | Confirm; schedule above is relative |
| 9 | Existing website stack/code unknown | Share repo or export; decide adopt vs rebuild |
| 10 | Projector underperforms in lit wards | Test in ward; TV fallback |

**Questions I need from you:**
1. The real deadlines and the target ward/pilot site for the first demo.
2. Is the existing website's code accessible, and who maintains it?
3. Is **fingerprint** mandatory, or is a patient code acceptable?
4. Is the team fine with **dropping the camera** from Phase 1?
5. Budget ceiling for Wave 0/1 and who approves purchases.
6. Who will produce scene videos and raga recordings (own shoot, musician, licence)?

---

## Appendix A: Questionnaire v2 conversion checklist (from the current 55 items)

1. Tag each item `physical / physiological / psychological (/ manasika)`.
2. Merge: hair (Q3–6) → 2 items; eyes (Q7–8) → 1; appetite (Q25–26) → 1; liquids (Q30–31) → 1.
3. Rewrite non-discriminating options (e.g. Satisfaction).
4. Move observable-only items (veins, tongue, teeth, chest, hands, frame) to the **clinician sheet**.
5. Ask the expert about **Faith in God**, **Egoistic**, **Fond of praise**, **Biting nails** (culture/sensitivity; Manasika vs dosha).
6. Add "both apply" and "not sure".
7. Randomize option order; store the mapping.
8. Translate (Hindi, Marathi) and back-translate.
9. Pilot with 20 people for comprehension; revise; then freeze `form_v2`.

## Appendix B: Oil catalogue schema (for contraindication checks)
```json
{ "oil_id":"OIL-ROSE-01", "name":"Rose otto", "botanical":"Rosa damascena",
  "dosha_tag":["P"], "cautions":["asthma","pregnancy_first_trimester"],
  "batch":"B2026-114", "coa_ref":"COA-114.pdf" }
```
*(Cautions list is filled by the clinical team, not by me.)*

## Appendix C: Glossary
**Prakriti:** inborn constitution. **Vikriti:** current imbalance. **Dosha:** Vata (air+ether), Pitta (fire+water), Kapha (earth+water). **Dvandvaja:** dual-dosha type. **Sama/Sannipataja:** tri-dosha balanced type. **Raga / prahara:** melodic framework / its time-of-day window. **Dashavidha Pariksha:** ten-fold classical examination. **Manasika Prakriti:** mental constitution (Sattva/Rajas/Tamas).
