# Import example: a component TARA inside a machine TARA

Two projects in one folder:

| File | Role |
|---|---|
| [`SmokeDetectorCase.tara.json`](SmokeDetectorCase.tara.json) | **Component** — the smoke detector as the platform team analyses it: its DFD, threats, risks, measures, assets and assumptions of use |
| [`SmokeDetectorController.tara.json`](SmokeDetectorController.tara.json) | **Machine** — the controller the detector is built into; it imports the component instead of analysing it again |

Requires TARAflow **0.19 or later** (desktop app; the import reads the component from git).

Both projects are complete on their own: no errors or warnings in any tab, every threat decided, every risk rated and treated, and every implemented measure visible in the model (no DFD notifications). Whatever the machine reports after the import is the import's work for the machine team, not noise.

## What the import does

- The component's DFD becomes a read-only child page of a new multiprocess in the machine. Its external entities are mapped to elements of the machine (e.g. the component's *Smoke Detector Controller* to the machine's controller process, its *Debugger* to the machine's debugger).
- The component's threats, attack trees, measures, assets and assumptions of use come along — not regenerated. The machine rates what depends on its context: impact, context factors (e.g. window of opportunity), the assumptions of use per instance.
- The component has one attack tree on its calibration data (DA-002) with two AND combinations — *Open the housing* AND *Use the debug port to write the calibration* (or *… read it out*) — and two single steps over RS485. Each leaf is rated with the five attack-potential factors (ISO/IEC 18045). In the machine's report the tree appears under the imported component, every path per instance, every step with its factors. Rating the window of opportunity per step is the machine's decision in an ISO/SAE 21434 machine only; this example is an IEC 62443 / CRA pair, so the paths are shown, not rated.
- The same component can be built in more than once: each instance (prefix `SD1`, `SD2`, …) has its own ratings.
- The import is pinned to the component's **commit**. Both files lie in this repository, so the machine finds the component on any computer that has the repository checked out.

## Try it

1. Open `SmokeDetectorController.tara.json`.
2. In the DFD tab choose **Import component** and pick `SmokeDetectorCase.tara.json`. Map the two external entities — *Smoke Detector Controller* → **P-1 Alarm Processing Controller**, *Debugger* → **EE-1 Debugger** — and choose a prefix (`SD1`). Import it a second time as a **separate instance** (`SD2`).
3. What the machine has to do now, per instance:
   - the two assumptions of use (ASM-01 debug connector out of reach, ASM-02 the controller authenticates on the sensor link) are open — mark them fulfilled or not;
   - the impact of the imported assets (`SD1:DA-001` …) is the machine's to rate;
   - the machine-side threats on the new boundary flows (per interaction) wait for a decision.
4. Rate an imported risk, look at the component section of the multiprocess (details panel).

## Try an update (on a local branch)

Use a local branch, so this repository stays as it is:

```bash
git switch -c try-update
```

1. Change the component: edit a threat text, delete a boundary flow and draw it again between the same two elements with the same protocol, delete a threat the machine has rated. Save and commit.
2. In the machine, component section: **Check** — "newer state available" with the commit.
3. **Update…**: the dialog shows what changed, offers a pairing for the redrawn flow (unticked), and how many of the machine's decisions go to review. Apply.
4. The decisions to review are listed in the component section with **Confirm**. **Discard update** restores the state before the update until you save.

```bash
git switch -     # back; delete the branch when done: git branch -D try-update
```
