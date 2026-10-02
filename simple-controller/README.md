# Simple Controller examples

One small model — an operator, a controller with an update service, a configuration store, a USB service port and the device enclosure — and one example per aspect of TARAflow. Every example starts from `00-base.tara.json` and changes one thing.

The files are generated from the TARAflow repository (`src/tests/examples`) through the real services; the values under **Expected** are checked by a test. Regenerate after a change:

```
WRITE_EXAMPLES=<this folder> npx vitest run src/tests/examples
```

Requirement ids refer to `src/tests/requirements/README.md` in the TARAflow repository.

## Base model

| Element | Name | Notes |
|---|---|---|
| EE-1 | Operator | outside both boundaries |
| IF-1 | USB (service port) | on the enclosure edge, EL1 |
| P-1 | Controller | daemon, runs as service |
| P-2 | Update Service | daemon, runs as root |
| DS-1 | Config Store | flash, no encryption, no integrity protection |
| DF-1 | push setpoint [cmd] | EE-1 → P-1 via IF-1, UART over USB |
| DF-2 / DF-3 | write config / read config | P-1 ↔ DS-1 |
| DF-4 | push firmware [cmd] | P-2 → P-1, file |

Assets (from example 01 on): DA-001 Setpoint and configuration (transports DF-1..3, stores DS-1), DA-002 Firmware image (transports DF-4), PR-001 Heating control (is P-1). Impact criteria: the defaults plus safety.

## 01 Goals derived

File: `01-goals-derived.tara.json` · Requirements: SG-01, SG-07

Security goals derived from the DFD relations and the asset impact — no analyst decision yet. Threat generation creates only the STRIDE categories of active goals; elements without an asset (P-2, IF-1, ENC) keep the full technically possible set.

**Steps**

1. Assets: the goal column shows blue-outlined chips (suggested) with the level, e.g. "I · High".
2. Open DA-001 → tab Security goals: every card says Suggested and explains the level (relation + driving criterion).
3. Threats → Generate: a data flow gets only the categories its asset goals ask for (technically possible ∩ active goal).

**Expected** (checked by `simple-controller-examples.test.ts`):

| Asset | Goals (level · source) |
|---|---|
| DA-001 Setpoint and configuration | C Medium · suggested, I High · suggested, A High · suggested |
| DA-002 Firmware image | C High · suggested, I Critical · suggested |
| PR-001 Heating control | I Critical · suggested, A Critical · suggested |

Threats → Generate (per element) creates these STRIDE categories:

| Element | Categories |
|---|---|
| DF-1 | T, I, D |
| DF-2 | T, I, D |
| DF-3 | T, I, D |
| DF-4 | T, I |
| DS-1 | T, I, D |
| EE-1 | S, R |
| ENC | S, T, R, I, D, E |
| IF-1 | S, T, R, I, D, E |
| P-1 | T, D |
| P-2 | S, T, R, I, D, E |

Needs review: none.

## 02 Goals decided

File: `02-goals-decided.tara.json` · Requirements: SG-02, SG-04, SG-09

Three analyst decisions on DA-001: integrity adjusted, authorisation added, confidentiality excluded — each with a rationale. An exclusion is a decision and stays visible.

**Steps**

1. Assets: DA-001 shows I and AuthZ as blue filled chips with the pen icon, C greyed out and struck through.
2. Hover C: the tooltip carries the rationale.
3. Report → Assets: the security-goal table lists I (Adjusted), AuthZ (Added) and C (Excluded) with their rationales.

**Expected** (checked by `simple-controller-examples.test.ts`):

| Asset | Goals (level · source) |
|---|---|
| DA-001 Setpoint and configuration | C excluded, I Medium · manual, A High · suggested, AuthZ Medium · manual |
| DA-002 Firmware image | C High · suggested, I Critical · suggested |
| PR-001 Heating control | I Critical · suggested, A Critical · suggested |

Threats → Generate (per element) creates these STRIDE categories:

| Element | Categories |
|---|---|
| DF-1 | T, D |
| DF-2 | T, D |
| DF-3 | T, D |
| DF-4 | T, I |
| DS-1 | T, D |
| EE-1 | S, R |
| ENC | S, T, R, I, D, E |
| IF-1 | S, T, R, I, D, E |
| P-1 | T, D |
| P-2 | S, T, R, I, D, E |

Needs review: none.

## 03 Goals need review

File: `03-goals-need-review.tara.json` · Requirements: SG-03, SG-05, SG-11

Three typical review cases: a decision whose basis changed (DA-001 A), a deviation without rationale (DA-002 C), and an asset without impact assessment (DA-003).

**Steps**

1. Assets: the bar offers "Needs review only (3)"; switch it on — exactly DA-001, DA-002 and DA-003 remain.
2. DA-003 shows "I · ?" instead of a level: the minimum level is never shown as Low.
3. Open DA-001 → Security goals: the A card says Review — the suggested level rose after the decision.
4. The findings panel below the table lists the same cases.

**Expected** (checked by `simple-controller-examples.test.ts`):

| Asset | Goals (level · source) |
|---|---|
| DA-001 Setpoint and configuration | C Medium · suggested, I Critical · suggested, A Low · manual · review |
| DA-002 Firmware image | C Low · manual, I Critical · suggested |
| PR-001 Heating control | I Critical · suggested, A Critical · suggested |
| DA-003 Calibration data | C ? (assessment required) · suggested, I ? (assessment required) · suggested |

Threats → Generate (per element) creates these STRIDE categories:

| Element | Categories |
|---|---|
| DF-1 | T, I, D |
| DF-2 | T, I, D |
| DF-3 | T, I, D |
| DF-4 | T, I |
| DS-1 | T, I, D |
| EE-1 | S, R |
| ENC | S, T, R, I, D, E |
| IF-1 | S, T, R, I, D, E |
| P-1 | T, D |
| P-2 | S, T, R, I, D, E |

Needs review: DA-001, DA-002, DA-003.

## 04 Impact per goal

File: `04-goal-impact.tara.json` · Requirements: SG-06, SG-08

Impact per security goal: DA-001 lowers safety for the integrity goal (with rationale); DA-002 raises financial damage for confidentiality above the asset value — a conflict, reported as an error, the value is not changed.

**Steps**

1. Open DA-001 → Security goals → I: Safety Impact shows 1 (adjusted) next to the asset value 3.
2. Open DA-002 → Security goals → C: the card shows "Impact above asset value"; the findings panel reports the conflict.
3. Threats → Generate, Risks → Sync: a tampering risk on DF-1 takes its safety impact from the goal (1), not from the asset (3).

**Expected** (checked by `simple-controller-examples.test.ts`):

| Asset | Goals (level · source) |
|---|---|
| DA-001 Setpoint and configuration | C Medium · suggested, I High · suggested, A High · suggested |
| DA-002 Firmware image | C High · suggested, I Critical · suggested |
| PR-001 Heating control | I Critical · suggested, A Critical · suggested |

Threats → Generate (per element) creates these STRIDE categories:

| Element | Categories |
|---|---|
| DF-1 | T, I, D |
| DF-2 | T, I, D |
| DF-3 | T, I, D |
| DF-4 | T, I |
| DS-1 | T, I, D |
| EE-1 | S, R |
| ENC | S, T, R, I, D, E |
| IF-1 | S, T, R, I, D, E |
| P-1 | T, D |
| P-2 | S, T, R, I, D, E |

Needs review: DA-002.
