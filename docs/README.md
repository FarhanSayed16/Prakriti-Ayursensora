# Prakriti Ayursensora — which file to open

These four files are the project. Read them in this order.

| File | What it is |
|---|---|
| [Problem statement and project understanding](Prakriti_Ayursensora_Problem_Statement_and_Project_Understanding.md) | What the project is, who it is for, the proposal’s mapping, the team, the phases, and what is still open |
| [System architecture and modules](Prakriti_Ayursensora_System_Architecture_and_Modules.md) | How the Pi, the ESP32, and the three modules connect, which software runs where, and how session data reaches the team |
| [Components and how they connect](Prakriti_Ayursensora_Three_Modules_Component_List.md) | The parts in each module, the exact cables, and the connection chart |
| [Final component list](Prakriti_Ayursensora_Final_Buy_List.md) | Every part under its module, then the current buy-now list, then the small-parts check |

Diagrams are in `architecture/`. Each `.puml` file is one picture.

| File | Picture |
|---|---|
| `01-system-context.puml` | Who uses the unit |
| `03-technical-stack.puml` | Software on the phone, the Pi, and the ESP32 |
| `04-session-data-flow.puml` | Enrol, confirm, run, export |
| `05-session-lifecycle.puml` | Idle, running, pause, fault, e-stop |
| `06-aroma-module.puml` | Aroma parts and wires |
| `07-display-module.puml` | Display parts and wires |
| `08-music-module.puml` | Music parts and wires |
| `09-three-modules-integrated.puml` | All three modules joined only at the Pi |

`docs/archive` holds the original deck, the component photos, and the earlier drafts. Those are kept for the record. They are not the plan.

`Prakriti_Ayursensora_Component_Finalization_Review.md` and `Prakriti_Ayursensora_Problems_and_Verdict_for_Cursor.md` are review notes. The architecture and the buy list are the current plan. Where a review and those two files disagree, follow the architecture and the buy list.
