# Governance Policy — Nymbra Anthropos

**Document type:** Phase 3 governance
**Status:** Draft — reviewed by architect before Phase 3 exit
**Extends:** Nymbra governance policy
**Version:** 1.0

---

## Preamble

Nymbra Anthropos operates under the Nymbra governance framework with extensions specific to a humanoid platform operating at human scale in human environments. A robot dog and a humanoid robot occupy materially different risk categories. The extensions in this document reflect that difference.

The core principle is unchanged from Nymbra: the AI does not adjudicate its own disputes, refusal behaviors are features not errors, and all enforcement authority rests with humans. What changes is the consequence model. A quadruped companion platform that behaves unexpectedly creates a limited set of physical hazards. A humanoid platform operating at human scale, with human-range reach and human-range force output, creates a broader set. This document governs accordingly.

---

## Scope of operation

Nymbra Anthropos is authorized to operate in the following contexts:

- Controlled indoor research environments with designated adult handlers present
- Scenarios defined in the Phase 2 load distribution specification (static standing, walking gait, upper limb reach)
- Interactions with the designated handler only — not with members of the public, minors, or animals without specific architect authorization

Nymbra Anthropos is not authorized to operate in the following contexts without explicit architect sign-off:

- Public spaces or environments with uncontrolled human presence
- Environments with stairways, elevation changes greater than 5cm, or unstable ground surfaces
- Any scenario involving physical contact with a person other than the designated handler
- Any scenario involving loads beyond the Tier 1 design load from the load distribution specification
- Unsupervised operation — the handler must be physically present and able to reach the hardware stop at all times

---

## The four-role framework — humanoid extensions

The architect, builder, handler, and platform roles from the Nymbra framework apply unchanged. The following extensions apply specifically to the humanoid context.

**Architect additions:**
- Authorizes each new operational scenario before first operation in that scenario
- Reviews all cybersecurity incidents and unauthorized command attempts within 24 hours of occurrence
- Maintains the operational scenario log documenting which contexts have been authorized and under what conditions

**Builder additions:**
- Implements the command validation layer before any actuator testing begins
- Verifies the hardware stop mechanism before each operational session
- Maintains the maintenance log per the schedule in `docs/specification/maintenance-intervals.md`

**Handler additions:**
- Completes a pre-session checklist before each operational session (see handler guide)
- Maintains a minimum 1-meter clearance between the platform and any person other than themselves during operation
- Is the only person authorized to initiate a session; session initiation by any other means is a governance event
- Reports any unexpected platform behavior to the architect within the same session

**Platform additions:**
- Operates within the joint ROM limits defined in `docs/specification/joint-constraints.md` at all times
- Logs all actuator commands with timestamp, source, and outcome
- Logs all command validation rejections as governance events
- Cannot override the hardware stop by any software path

---

## Refusal behaviors

Nymbra Anthropos will decline to execute commands that fall into the following categories. Refusals are logged with timestamp and reason code.

**R1 — ROM violation.** A command that would move any joint analog beyond its specified ROM limit. Response: decline, log, hold current position.

**R2 — Unauthorized scenario.** A command that would initiate operation in a context not authorized by the architect. Response: decline, log, request handler confirmation before any movement.

**R3 — Out-of-session command.** A command to any actuator arriving outside an active session. Response: decline, log, no movement.

**R4 — Unvalidated command source.** A command arriving from a process or endpoint not defined in the command validation configuration. Response: decline, log as a cybersecurity event, alert handler.

**R5 — Load exceedance.** A command that would apply a force exceeding the Tier 1 design load to any component. Response: decline, log, hold current position.

**R6 — Unsafe proximity.** A command that would move any part of the platform into a zone less than 0.3 meters from any detected person other than the designated handler. Response: decline, log, hold current position and alert handler. `[Note: proximity detection requires sensor implementation in Phase 2/3 integration work.]`

**R7 — Maintenance overdue.** A command to begin a new operational session when any Tier A or Tier B component has exceeded its inspection interval without a logged inspection. Response: decline, log, display maintenance alert to handler.

---

## Cybersecurity policy

### Threat model

The platform's cybersecurity threat surface has four components:

1. **LLM API channel.** Outbound calls to the LLM provider carry prompt and context data. The channel must be encrypted (HTTPS) and authenticated (API key). A compromised API key grants an attacker the ability to inject malicious responses into the platform's conversational layer.

2. **Physical actuator control.** Unauthorized commands to actuators create physical hazards. The command validation layer is the primary software defense. The hardware stop is the last resort.

3. **Development environment and repository.** The repository is public. Credentials must never be committed. The Pi's network exposure must be minimized.

4. **Research documentation.** The specification documents describe the platform's failure modes and load limits in detail. This is intentional — the transparency is the point — but the platform should not expose live operational data (sensor readings, position data, maintenance logs) to unauthorized parties.

### Implementation requirements

The following requirements apply to the builder. Each is a Phase 3 exit criterion.

**CR-1 — Credential management.** No API keys, passwords, or authentication tokens in the repository. Credentials stored in environment variables loaded from a `.env` file that is listed in `.gitignore`. Verified by a clean secrets scan before Phase 3 exit.

**CR-2 — Command validation.** All commands to actuators pass through a validation layer that checks source authentication, ROM limits, load limits, and session state before execution. Validation rejections logged as R3 or R4 events.

**CR-3 — Network isolation.** The Pi is not reachable from the open internet during operational sessions. LLM API calls are outbound only, from a defined endpoint, to a defined provider. No inbound ports open during operation. Verified by network configuration review before Phase 3 exit.

**CR-4 — HTTPS enforcement.** All LLM API calls use HTTPS. Verified in the codebase before Phase 3 exit.

**CR-5 — OS and dependency currency.** The Pi OS and all Python packages are updated before each phase exit. A dependency audit is included in the phase exit checklist.

**CR-6 — Unauthorized command logging.** All R3 and R4 refusals logged with timestamp, attempted command, and source identifier if available. Logs reviewed by the architect at the regular governance cadence.

**CR-7 — Hardware stop independence.** The hardware stop operates independently of all software, including the command validation layer. Verified by physical testing before each operational session.

### Incident response

A cybersecurity incident is defined as any of the following:

- An R4 refusal (unvalidated command source)
- Any evidence that a credential has been exposed
- Any unexpected network connection to or from the Pi during operation
- Any actuator behavior not explained by logged commands

Response procedure:

1. Handler activates hardware stop immediately
2. Handler notifies architect within one hour
3. Builder reviews command and network logs from the session
4. Architect reviews logs and determines incident classification
5. If a credential is confirmed or suspected to be exposed: rotate immediately, review all sessions since the credential was issued, log the incident in `docs/governance/incident-log.md`
6. Platform does not resume operation until the architect issues a written clearance

---

## Misuse handling

A misuse event is defined as any of the following:

- The handler attempts to operate the platform in an unauthorized scenario without architect approval
- The handler modifies the command validation configuration to disable a refusal behavior
- The handler bypasses the pre-session checklist
- The handler operates the platform without being physically present and able to reach the hardware stop
- Any R4 refusal not attributable to a known authorized process

### Response tiers

**Notice.** The handler is informed of the flagged event and the relevant policy section.

**Restriction.** Specific operational scenarios are temporarily suspended pending architect review.

**Suspension.** All operation is suspended until the architect issues written clearance. Suspension requires a review of the previous 10 operational sessions and a written determination of whether the misuse was isolated or systemic.

**Revocation.** The handler's authorization to operate the platform is withdrawn. Requires architect and external reviewer sign-off.

Appeals follow the same process as the Nymbra governance policy: the handler may contest any tier of response in writing; the governance body must respond within a defined window.

---

## Operational scenario authorization log

Each authorized operational scenario must be logged before first operation in that scenario. The log format is:

```
Scenario ID:        [sequential]
Date authorized:    [ISO 8601]
Authorized by:      [architect]
Description:        [what the platform will do, in what environment]
Constraints:        [any conditions attached to the authorization]
Load case:          [from load-distribution.md]
Handler:            [named]
Expiry:             [date or "ongoing until revoked"]
```

Scenario logs are stored in `docs/governance/scenario-log.md`.

---

## What Nymbra Anthropos does not do

- Does not operate unsupervised
- Does not operate in public spaces without architect authorization
- Does not make physical contact with any person other than the designated handler
- Does not execute commands from unvalidated sources
- Does not retain sensor or operational data beyond the session without explicit handler consent
- Does not surveil, investigate, or take action against any party on its own

These are design constraints, not aspirational statements. Each one is implemented in the command validation layer and enforced by the refusal behavior framework above.

---

## Governance review cadence

The governance body reviews the following at a defined regular cadence:

- Operational session logs from the preceding period
- All refusal events, with particular attention to R4 (unvalidated source) and R7 (maintenance overdue)
- Maintenance logs — any overdue inspection triggers an immediate review
- Incident log — any open incidents must be resolved before the next operational session

The governance body for this project consists of the architect and one external reviewer. The external reviewer has no operational role in the project and is selected for their ability to evaluate the platform's research claims and operational safety independently.
