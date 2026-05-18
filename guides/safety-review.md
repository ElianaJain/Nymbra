# Safety Review — ISO 10218 / ISO/TS 15066

**Document type:** Phase 3 safety review
**Status:** Draft — requires sign-off from architect and external reviewer before Phase 3 exit
**Standards referenced:** ISO 10218-1:2011, ISO 10218-2:2011, ISO/TS 15066:2016
**Platform:** Nymbra Anthropos — humanoid research platform
**Form factor note:** These standards address industrial and collaborative robot applications. Nymbra Anthropos is a small-format humanoid research platform, not an industrial system. All deviations from the standards are noted and justified.

---

## Scope and applicability

ISO 10218 defines safety requirements for industrial robots. ISO/TS 15066 extends those requirements to collaborative robot operation — robots that share a workspace with humans and may operate in physical proximity to them.

Nymbra Anthropos is not an industrial robot. It is a small-format humanoid research platform with a payload capacity and force output substantially lower than any industrial system addressed by ISO 10218. However, these standards provide the most comprehensive available framework for thinking about robot safety in human environments, and the principles they establish — hazard identification, risk reduction, protective measures, and verification — apply regardless of scale.

This review applies the standards' principles to the Nymbra Anthropos platform, notes where the platform falls outside the standards' intended scope, and identifies the safety requirements that apply at the platform's actual scale and operational context.

---

## Hazard identification

The following hazards are identified for the Nymbra Anthropos platform in its research operational context. Each hazard is rated by severity and likelihood under normal operation.

| Hazard ID | Description | Severity | Likelihood | Notes |
|---|---|---|---|---|
| H-01 | Unexpected movement injuring handler or bystander | High | Low under controlled conditions | Primary physical hazard |
| H-02 | Joint overextension causing structural failure and projectile hazard | Medium | Low if ROM limits enforced | Mitigated by J-constraints |
| H-03 | Actuator overheating causing surface burn | Low | Low | Depends on actuator selection |
| H-04 | Platform falling and striking handler or bystander | High | Medium during gait development | Bipedal balance is an active risk |
| H-05 | Unauthorized command causing unexpected movement | High | Low if CR-2 implemented | Cybersecurity-adjacent |
| H-06 | Pinch or crush injury at joint interfaces | Medium | Low under controlled conditions | Standard robotic hazard |
| H-07 | Electrical hazard from power system | Medium | Low with correct power design | Standard Pi/actuator risk |
| H-08 | Software failure causing uncontrolled movement | High | Medium during development | Mitigated by hardware stop |

---

## ISO 10218-1 — Robot manufacturer requirements

**Applicability:** ISO 10218-1 addresses robot manufacturers. Nymbra Anthropos is a research platform, not a commercial product. The platform is designed and built by the same party that operates it. The requirements below are applied in the spirit of the standard — as design-time safety requirements — rather than as compliance obligations.

### Section 5.4 — Design for safety

**Requirement:** The robot shall be designed to minimize hazards throughout its lifecycle.

**Platform application:** Joint ROM limits (J-constraints) and load ratings (load distribution spec) are the primary design-time safety measures. ROM limits prevent overextension. Load ratings prevent structural failure. Both are documented and traceable to osteological evidence.

**Status:** Addressed in Phase 2 specifications. Verification required before Phase 3 exit.

### Section 5.5 — Control system safety

**Requirement:** The control system shall be designed to prevent hazardous motion resulting from control system failure.

**Platform application:** The command validation layer (CR-2) is the primary software safety measure. The hardware stop is the backup. The hardware stop must be independent of all software, including the command validation layer.

**Deviation:** Full control system safety per ISO 10218-1 requires hardware interlocks, safety-rated controllers, and fault detection that are beyond the scope of a hobbyist research platform. The hardware stop and command validation layer provide proportionate mitigation for the platform's actual risk level.

**Status:** CR-7 (hardware stop independence) addresses this requirement. Verification required before Phase 3 exit.

### Section 5.6 — Speed and force limiting

**Requirement:** The robot shall be capable of limiting speed and force to safe levels.

**Platform application:** Speed limits are enforced through actuator configuration. Force limits are implemented through the load exceedance refusal (R5). Both must be verified before Phase 3 exit.

**Status:** R5 refusal behavior addresses force limiting. Speed limit configuration is a builder implementation requirement for Phase 3.

### Section 5.7 — Enabling device / emergency stop

**Requirement:** The robot shall have an emergency stop that overrides all other controls.

**Platform application:** The hardware stop satisfies this requirement. It must be: immediately accessible to the handler at all times during operation, physically independent of all software, tested before each operational session, capable of halting all movement within the latency target defined in the Phase 1 milestones.

**Status:** Implemented. Verification procedure defined in handler guide.

---

## ISO 10218-2 — Integrator requirements

**Applicability:** ISO 10218-2 addresses system integrators who deploy robots in specific applications. The governance policy and operational scenario authorization log fulfill the integrator role for Nymbra Anthropos.

### Section 5.4 — Risk assessment

**Requirement:** A risk assessment shall be conducted for the intended application.

**Platform application:** This safety review document is the risk assessment. The hazard table above identifies the primary hazards. Risk reduction measures follow.

### Section 5.7 — Safety functions

**Requirement:** Safety functions shall be implemented and verified for each identified hazard.

**Platform application:**

| Hazard | Primary mitigation | Verification method |
|---|---|---|
| H-01 | Session control, handler proximity rules, R6 refusal | Pre-session checklist, observed operation |
| H-02 | J-constraint ROM limits, R1 refusal | Actuator configuration review, movement test |
| H-03 | Actuator thermal monitoring | Temperature monitoring implementation |
| H-04 | Handler supervision, controlled environment, hardware stop | Operational scenario authorization |
| H-05 | CR-2 command validation, R4 refusal | Code review, penetration test (lightweight) |
| H-06 | Handler awareness training, minimum clearance rules | Handler guide review |
| H-07 | Correct power system design, standard electrical safety | Power system design review |
| H-08 | Hardware stop (CR-7), command validation (CR-2) | Hardware stop test, code review |

---

## ISO/TS 15066 — Collaborative operation

**Applicability:** ISO/TS 15066 addresses robots operating in direct collaboration with humans — shared workspace, possible physical contact. Nymbra Anthropos is not designed for deliberate physical contact with humans other than the designated handler, but it operates in proximity to humans and the standard's principles apply.

### Section 5.4 — Power and force limiting (PFL)

**Requirement:** In collaborative operation, the robot shall limit contact forces to values that do not cause pain or injury.

**ISO/TS 15066 Annex A** provides biomechanically-derived pain threshold values for contact forces at different body regions. These values are derived from human tissue tolerance research.

**Platform application:** The platform's actuator force output must be below the pain threshold values for the body regions most likely to experience contact during the research operation. The most relevant values from Annex A for this context are:

| Body region | Transient contact force limit | Quasi-static force limit |
|---|---|---|
| Hand / finger | 140 N | 70 N |
| Forearm | 160 N | 75 N |
| Upper arm | 150 N | 35 N |
| Thigh | 220 N | 105 N |
| Lower leg | 220 N | 105 N |

**Deviation and justification:** Nymbra Anthropos is not a collaborative robot in the ISO/TS 15066 sense — it is not designed for contact tasks. The platform's operational scenario authorization process prohibits physical contact with persons other than the designated handler. The force limits above are provided as reference values for the handler's awareness, not as compliance targets for contact tasks.

Where accidental contact is possible — the handler adjusting the platform, or unexpected falls — the actuator force output should be verified against these limits as a design-time safety check.

**Status:** Actuator force output verification is a Phase 3 builder requirement. The platform must not be operated in proximity to unprotected persons until this verification is complete.

### Section 5.5 — Speed and separation monitoring (SSM)

**Requirement:** In collaborative operation, the robot shall monitor the distance between itself and humans and reduce speed as separation decreases.

**Platform application:** The R6 refusal behavior implements a simplified version of SSM — the platform declines to move into a zone within 0.3 meters of a detected person. Full ISO/TS 15066 SSM requires real-time distance monitoring and graduated speed reduction, which is beyond the current platform specification.

**Deviation and justification:** R6 provides a fixed exclusion zone rather than a graduated response. This is a simplified implementation appropriate for a research platform operating under direct supervision. The operational scenarios authorized for Phase 3 operation are low-speed research scenarios where the fixed exclusion zone provides adequate protection.

Full SSM implementation is deferred to a future phase if the platform's operational scope expands beyond direct-supervision research scenarios.

---

## Hardware stop verification procedure

The hardware stop must be verified before each operational session using the following procedure:

1. With the platform in a stable standing or supported position, activate the hardware stop
2. Confirm that all actuators halt within the specified latency target
3. Attempt to send a test command to any actuator while the stop is active
4. Confirm that the test command is rejected and does not produce movement
5. Deactivate the hardware stop and confirm that normal operation resumes
6. Log the verification result in the session pre-check record

If the hardware stop fails any step of this procedure, the session must not proceed. The builder must investigate and resolve the failure before the next attempt.

---

## Safety requirements summary

The following requirements must be satisfied before Phase 3 is considered complete. Each is traceable to a standard section or hazard above.

| Requirement ID | Description | Source | Status |
|---|---|---|---|
| SR-01 | Joint ROM limits implemented and verified | ISO 10218-1 §5.4, H-02 | Specified in Phase 2 — verify |
| SR-02 | Hardware stop independently operational | ISO 10218-1 §5.7, H-08 | CR-7 — verify per session |
| SR-03 | Command validation layer operational | ISO 10218-1 §5.5, H-05 | CR-2 — verify in code review |
| SR-04 | Actuator force output below ISO/TS 15066 Annex A limits | ISO/TS 15066 §5.4 | Builder requirement — verify |
| SR-05 | Speed limits configured on all actuators | ISO 10218-1 §5.6 | Builder requirement — verify |
| SR-06 | R6 proximity refusal operational | ISO/TS 15066 §5.5 | Requires proximity sensor |
| SR-07 | Handler completed safety awareness review | ISO 10218-2 §5.7 | Handler guide sign-off |
| SR-08 | Operational scenarios authorized before operation | ISO 10218-2 §5.4 | Governance policy — ongoing |
| SR-09 | Maintenance log current before each session | H-01, H-04 | Maintenance intervals spec |
| SR-10 | Power system design reviewed for electrical safety | H-07 | Builder requirement |

---

## Deviations from standards — consolidated summary

| Deviation | Justification | Residual risk |
|---|---|---|
| Not using safety-rated controller hardware | Research platform, not industrial system. Hardware stop provides proportionate mitigation. | Low — mitigated by hardware stop and supervised operation |
| Full SSM not implemented | Research platform under direct supervision. Fixed exclusion zone (R6) provides adequate protection for authorized scenarios. | Low — mitigated by supervision and scenario authorization |
| Force limits used as reference, not compliance targets | Platform not designed for contact tasks. Contact scenarios are outside authorized operation. | Low — mitigated by operational scope restrictions |
| Risk assessment conducted by project team, not independent body | Research platform. External reviewer sign-off on this document provides proportionate independent review. | Low — mitigated by external reviewer requirement |

---

## Sign-off

This safety review is considered complete when:

- [ ] All SR-01 through SR-10 requirements are verified and logged
- [ ] The external reviewer has reviewed this document and provided written sign-off
- [ ] The architect has reviewed the consolidated deviations and confirmed that residual risks are acceptable for the authorized operational scenarios
- [ ] The handler has reviewed the handler guide and confirmed understanding of the safety requirements

Sign-off date: ________________
Architect: ________________
External reviewer: ________________
