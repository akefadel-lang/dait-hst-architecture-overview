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

DAIT + HST is an experimental defensive runtime architecture.

At a high level:

```text
DAIT
→ analysis/runtime side

HST
→ validation, timing, control, and protection support layer

NeuraSignal
→ lightweight visible telemetry satellite
→ sends allow-listed technical telemetry to DAIT
```

NeuraSignal is intentionally limited. It does **not** include AI, adaptive models, or local risk decisions. It only sends constrained technical telemetry through a controlled validation path.

---

## High-Level Architecture

```text
NeuraSignal Node/UI
→ HTTP Receiver
→ Network Customs / Pre-Gateway
→ Report Contract Validator
→ Report Lane / Queue / Replay-cache
→ DAIT Analysis Intake
```

Core principle:

```text
Known source does not mean trusted data.
Network-originated data is treated as foreign until it passes validation.
```

---

## Current Implemented Areas

```text
DAIT/HST core validation
OS boundary adapter
Timing risk engine
Payload window validation
Config loader
Gateway/HST-style validation chain
NeuraSignal telemetry node
NeuraSignal UI
NeuraSignal report contract
NeuraSignal network customs / ingress layer
NeuraSignal receiver server
NeuraSignal report lane / queue / replay-cache
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

Run the NeuraSignal receiver locally:

```powershell
$env:NEURASIGNAL_SHARED_SECRET="test-secret"
$env:NEURASIGNAL_RECEIVER_HOST="127.0.0.1"
$env:NEURASIGNAL_RECEIVER_PORT="8080"
py -m dataiatlas.neurasignal_receiver_server
```

Run the NeuraSignal UI in another PowerShell window:

```powershell
$env:NEURASIGNAL_CORE_URL="http://127.0.0.1:8080/neurasignal/report"
$env:NEURASIGNAL_NODE_ID="test-node"
$env:NEURASIGNAL_SHARED_SECRET="test-secret"
py -m neurasignal.neurasignal_ui
```

Note: `py -m neurasignal.neurasignal_ui` is the recommended startup method because it preserves Python import paths correctly.

---

## Test Summary

Current summarized test checkpoints:

```text
OS Boundary Adapter v2: PASS 6/6
Timing Risk Engine: PASS 5/5
NeuraSignal Report Contract: PASS 9/9
NeuraSignal Network Customs: PASS 11/11
NeuraSignal Receiver Server: PASS 8/8
NeuraSignal Report Lane: PASS 8/8
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

NeuraSignal’s design principle is visible, consent-based, limited telemetry:

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
DAIT UI: NeuraSignal node status panel
NeuraSignal LAN tests with another machine
node provisioning / consent-based installation flow
Report Lane persistence
evidence/throttle/rejected retention policy
restricted return lane
double quarantine integration
DAIT analysis intake integration
hardware capability probe
optional AI/GPU acceleration readiness
```

AI/GPU acceleration is kept as an optional assist layer. It must not bypass the Gateway, HST, Network Customs, Report Contract, Replay-cache, quarantine, or restricted return paths.

---

## Repository Note

This public-facing repository/README is designed to describe the architecture at a safe level without exposing sensitive implementation details, secrets, exact internal keys, private environment-specific data, or production security claims.

---

## Summary

DAIT + HST has evolved from a raw-data prototype toward a layered defensive runtime and telemetry-validation system.

At the current stage, the bridge between NeuraSignal and DAIT includes:

```text
node
UI
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
