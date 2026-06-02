# DAIT + HST — Lab-Level Defensive Runtime Prototype

> **Status:** Lab PoC / advanced hobby prototype  
> **Language:** Python  
> **Production-ready:** No  
> **Purpose:** Defensive telemetry validation, runtime hardening, and architecture research

## Important Notice

This repository describes a **lab-level proof-of-concept and learning project**. It is not a production-ready security product and must not be used as a replacement for antivirus software, EDR, firewalls, operating system security controls, or professional security monitoring.

The project focuses on defensive research: how telemetry, validation chains, replay protection, isolated lanes, controlled ingress paths, and runtime hardening ideas can be combined into a safer software architecture.

---

## What is DAIT + HST?

**DAIT + HST** is the core defensive runtime and validation system.

At a high level:

```text
DAIT
→ core analysis/runtime side
→ receives validated telemetry
→ handles analysis, state, and reporting direction

HST
→ validation, timing, control, and protection support layer
→ supports defensive checks and runtime hardening concepts

DaitSignal
→ separate lightweight satellite telemetry component
→ sends allow-listed technical telemetry to DAIT+HST
→ does not include AI, adaptive modeling, or local threat decisions
```

DaitSignal is intentionally separate from DAIT+HST. It is not the “brain” of the system and does not make security decisions by itself. Its role is to collect limited technical telemetry and send it through a controlled ingress path.

---

## High-Level Architecture

```text
DaitSignal Node/UI
→ HTTP Receiver
→ Network Customs / Pre-Gateway
→ Report Contract Validator
→ Report Lane / Queue / Replay-cache
→ DAIT+HST Analysis Intake
```

Core principle:

```text
Known source does not mean trusted data.
Network-originated data is treated as foreign until it passes validation.
```

DaitSignal can be understood as a visible satellite node. DAIT+HST is the core system that receives, validates, queues, and later analyzes the data.

---

## Current Implemented Areas

```text
DAIT/HST core validation
OS boundary adapter
Timing risk engine
Payload window validation
Config loader
Gateway/HST-style validation chain
DaitSignal telemetry node
DaitSignal UI
DaitSignal report contract
DaitSignal network customs / ingress layer
DaitSignal receiver server
DaitSignal report lane / queue / replay-cache
HST bounded runtime noise matrix
```

---

## Privacy Boundary

The project is designed around limited, technical telemetry.

By default, the telemetry design avoids collecting:

```text
file paths
process names
usernames
browser history
clipboard
keystrokes
documents
messages
photos
GPS/location
email/contact data
```

Unavailable measurements are handled explicitly:

```json
{
  "thermal": null,
  "battery_level": null,
  "missing_signals": ["battery_level", "thermal"],
  "input_quality": 0.75
}
```

Rule:

```text
null does not mean zero
null does not mean normal
null does not mean safe
null means unavailable measurement
```

---

## Development Setup

Create a virtual environment:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

Install basic dependencies:

```powershell
py -m pip install --upgrade pip
py -m pip install requests psutil PyQt6
```

Run the DaitSignal receiver locally:

```powershell
$env:DAITSIGNAL_SHARED_SECRET="test-secret"
$env:DAITSIGNAL_RECEIVER_HOST="127.0.0.1"
$env:DAITSIGNAL_RECEIVER_PORT="8080"
py -m dataiatlas.daitsignal_receiver_server
```

Run the DaitSignal UI in another PowerShell window:

```powershell
$env:DAITSIGNAL_CORE_URL="http://127.0.0.1:8080/daitsignal/report"
$env:DAITSIGNAL_NODE_ID="test-node"
$env:DAITSIGNAL_SHARED_SECRET="test-secret"
py -m daitsignal.daitsignal_ui
```

Note: the current codebase may still contain older internal module names until the refactor is completed. The public-facing name is now **DaitSignal**.

---

## Test Summary

Current summarized test checkpoints:

```text
OS Boundary Adapter v2: PASS 6/6
Timing Risk Engine: PASS 5/5
DaitSignal Report Contract: PASS 9/9
DaitSignal Network Customs: PASS 11/11
DaitSignal Receiver Server: PASS 8/8
DaitSignal Report Lane: PASS 8/8
```

Additional local attack-simulation style tests have also been run, including L8/L9 defensive decision-surface scenarios. These are local simulations and do not represent full red-team validation.

---

## Replay Policy

Report Lane v1 uses a deliberately simple replay policy:

```text
same report_id    → rejected_lane
same report_nonce → evidence_lane
```

Rationale:

```text
report_id may repeat because of retry/duplicate behavior
nonce is a one-time proof value, so repetition is a stronger signal
```

A separate duplicate lane was intentionally not added in v1 to avoid unnecessary lane complexity.

---

## Naming Note

The satellite telemetry component was renamed from **NeuraSignal** to **DaitSignal**.

Reason:

```text
The previous name could imply neural, medical, or health-related technology.
The new name better reflects the component’s actual role as a lightweight
technical telemetry satellite for DAIT+HST.
```

---

## Known Limitations

This project currently does **not** provide:

```text
production-ready security guarantee
constant-time proof
hardware/cache/power/EM side-channel protection
kernel-level protection
exploit delivery resistance
privilege escalation resistance
full red-team compromise resistance
formal verification
enterprise deployment hardening
```

The tests are mostly local simulations and development-environment tests. They demonstrate expected module behavior but do not replace professional auditing, fuzzing, long-duration chaos testing, or production security review.

---

## Ethical Use

This project is intended for defensive research, learning, and experimentation with safe telemetry architecture.

It is not intended for offensive use, malware development, unauthorized monitoring, or bypassing security controls.

DaitSignal’s design principle is visible, consent-based, limited telemetry:

```text
no hiding
no user content
no process names
no file paths
no browser history
no keystrokes
```

---

## Next Steps

Planned / possible next topics:

```text
DAIT UI: DaitSignal node status panel
DaitSignal LAN tests with another machine
node provisioning / consent-based installation flow
Report Lane persistence
evidence/throttle/rejected retention policy
restricted return lane
double quarantine integration
DAIT+HST analysis intake integration
hardware capability probe
optional AI/GPU acceleration readiness
```

AI/GPU acceleration is kept as an optional assist layer. It must not bypass the Gateway, HST, Network Customs, Report Contract, Replay-cache, quarantine, or restricted return paths.

---

## Repository Note

This public-facing README describes the architecture at a safe level without exposing sensitive implementation details, secrets, exact internal keys, private environment-specific data, or production security claims.

---

## Summary

DAIT + HST has evolved from a raw-data prototype toward a layered defensive runtime and telemetry-validation system.

At the current stage, the bridge between DaitSignal and DAIT+HST includes:

```text
DaitSignal node
DaitSignal UI
receiver
network customs
report contract
report lane
replay-cache
live/backlog separation
evidence/rejected/throttled lanes
emergency disconnect
```

The project is still a lab-level PoC, but its structure, testing, and documentation have progressed significantly.
