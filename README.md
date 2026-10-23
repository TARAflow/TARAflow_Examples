# TARAflow Examples

Example projects for [TARAflow](https://github.com/TARAflow/taraflow), the open-source tool for threat analysis and risk assessment (TARA) of embedded, automotive and cyber-physical systems.

Open a project to see what a finished analysis looks like, how a setting changes the result, or what a feature expects from the model. Every example is a plain `.tara.json` project file. They range from a full reference project down to a single change on a five-element model.

More about TARAflow: [taraflow.github.io](https://taraflow.github.io) · [method](https://taraflow.github.io/method.html) · [releases](https://github.com/TARAflow/taraflow/releases)

---

## Getting started

1. Install TARAflow **v0.14.0-beta or later** from the [releases](https://github.com/TARAflow/taraflow/releases).
2. Clone this repository or download single files:

   ```bash
   git clone https://github.com/TARAflow/TARAflow_Examples.git
   ```

3. Open a `.tara.json` file in TARAflow.

Some files are stored in an older project schema. TARAflow asks before it converts them to the current schema and keeps a backup of the original next to it. To keep this repository clean, work on a copy or don't commit the converted files.

---

## Overview

| Project | System | Preset | What it shows |
|---|---|---|---|
| [`SmokeDetector.tara.json`](SmokeDetector.tara.json) | Industrial fire detector | Standard | Full reference project: security and safety together |
| [`SmokeDetector-leveled.tara.json`](SmokeDetector-leveled.tara.json) | The same fire detector | Standard | Leveled DFD: one top page and a child page for each multiprocess |
| [`Headlamp_ISO21434_Example.tara.json`](Headlamp_ISO21434_Example.tara.json) | Automotive headlamp system | ISO/SAE 21434 | ISO 21434 workflow with attack trees and attack feasibility |
| [`cnc-ref.tara.json`](cnc-ref.tara.json) | CNC machine | — | Larger model built from multiprocesses, many threats per interaction |
| [`Simple_Test_Project.tara.json`](Simple_Test_Project.tara.json) | Small embedded device | EN 50742 A | A compact project for a first look at every phase |
| [`simple-controller/`](simple-controller/) | Heating controller | Standard | One small model, one example per security-goal feature, with expected results |

---

## The projects

### Smoke Detector — reference project

`SmokeDetector.tara.json`

An industrial fire detector based on optical light scattering. A remote sensor head measures smoke and streams the values over a field-wired RS-485 sensor bus to the controller. The controller evaluates the alarm condition, signals alarms and faults to the fire alarm control panel and logs security events. Configuration and firmware updates run over WLAN.

The availability and integrity of detection and alarm signalling are safety-critical (EN 54-7, EN 54-2), so the project covers security and safety together.

- **DFD:** 12 processes, 6 data stores, 5 external entities, 7 interfaces, physical and chip boundaries.
- **Hazards:** 5 hazard items linked to the assets they endanger.
- **Assets:** 22 assets with asset-to-asset relations.
- **Threats and risks:** generated per element and per interaction, with their risks.
- **Tags:** CRA, IEC 62443.

### Smoke Detector — leveled DFD

`SmokeDetector-leveled.tara.json`

The same system split across three draw.io pages:

| Page | Content |
|---|---|
| Top | MP-1 Sensor Head and MP-2 Smoke Detector Controller, the external entities and boundaries |
| Sensor Head | Decomposes MP-1 |
| Smoke Detector Controller | Decomposes MP-2 |

The original element ids are kept, so threats, risks and assets refer to the same logical elements as in the single-page project. Elements shown on more than one page are context copies, linked in `dfd.pages`. Compare the two files to see what a decomposition changes:

- threat, risk and asset tables grouped per multiprocess
- flows analysed on the leaves
- one report section per page

### Headlamp — ISO/SAE 21434

`Headlamp_ISO21434_Example.tara.json`

An automotive headlamp system based on the worked example in ISO/SAE 21434 Annex H. It uses the `iso-21434` preset:

- **Impact:** SFOP damage-scenario impact (safety, financial, operational, privacy).
- **Threats:** STRIDE per element.
- **Attack trees:** 10, with attack-potential feasibility (ISO/IEC 18045).
- **Risk:** R = I × L.

### CNC reference case

`cnc-ref.tara.json`

A CNC machine modeled with multiprocesses, data stores and four trust boundaries. Attack trees are attached to selected threats. A good project to see how TARAflow handles many interactions.

### Simple Test Project

`Simple_Test_Project.tara.json`

A small embedded device: one process, one data store, two external entities, interfaces, a physical and a chip boundary. Threats, risks and two attack trees are already filled in, so every phase has content. Uses the EN 50742 A preset; tags: IEC 62443, EN 50742 A.

### Simple Controller — security goals step by step

[`simple-controller/`](simple-controller/)

A deliberately tiny model: an operator, a controller, an update service, a configuration store, a USB service port and the enclosure. `00-base` is the model, each further file changes one thing:

| File | Shows |
|---|---|
| `00-base` | The base model, no assets yet |
| `01-goals-derived` | Security goals suggested from the DFD relations and the asset impact |
| `02-goals-decided` | Analyst decisions: a goal adjusted, added and excluded, each with a rationale |
| `03-goals-need-review` | Review cases: changed basis, deviation without rationale, missing impact |
| `04-goal-impact` | Impact per security goal, including a reported conflict |

The [folder README](simple-controller/README.md) lists the steps to follow in the app and the expected result for each file. The files are generated from the TARAflow test suite, which checks these expectations.

---

## This repository as an audit repository

The repository is also used with TARAflow's Git-based audit workflow:

- `.gitattributes` keeps `.tara.json` files with LF line endings and a TARAflow diff driver.
- `.tara/allowed_signers` lists the keys allowed to sign commits.
- Commits starting with `[TARA]` come from the app's commit dialog.

To try the audit tab, open a project from your clone. The local hooks TARAflow installs (`.tara/hooks/`) are ignored by Git.

---

## Contributing

New examples are welcome, especially small ones that show one feature. Please:

- Keep each example as small as the point it makes.
- Save it with the current TARAflow release.
- Describe it in this README: what it shows, which preset it uses.

Issues and discussion take place in the [TARAflow repository](https://github.com/TARAflow/taraflow).

## License

GPL-3.0, like TARAflow — see [LICENSE](LICENSE).
