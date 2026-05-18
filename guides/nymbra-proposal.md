# Nymbra

A research and portfolio project exploring responsible agentic AI on an embodied platform.

---

## Overview

Nymbra is a research and portfolio project that explores how an embodied agentic AI can hold natural conversation while failing gracefully at the edges of its knowledge. The project is built on the SunFounder PiDog platform and is intentionally scoped to be reproducible by a single builder, with documentation sufficient for others to fork, study, or extend.

The name Nymbra is a fanciful mark rather than an acronym. It echoes *nimbus* (the cloud or aura around a figure) and *umbra* (presence, shadow), suggesting something that accompanies rather than leads.

The project responds to a specific gap. Commercial robot-dog kits now ship with LLM integration out of the box, and hobbyist conversational agents on Raspberry Pi are an established genre. What none of these systems foreground is the behavior *around* the conversation: how the system signals uncertainty, how it handles its own hallucinations, how it starts and stops listening, and how misuse is adjudicated by humans rather than by the AI itself. Nymbra treats those behaviors as first-class design concerns rather than afterthoughts.

Nymbra is organized around a four-role framework — architect, builder, handler, and the system itself — that makes accountability explicit at every layer. The architect owns systems design and governance. The builder owns implementation and maintenance. The handler is the human in the loop during operation. Nymbra is the platform they shape together. No role is optional; the project is designed around the premise that responsible agentic AI requires all four to stay in sync.

**Disclaimer.** Nymbra is an AI-simulated personality running on a hobbyist robotics platform. It is not a replacement for human connection, professional care, or any decision-making authority in the handler's life.

**What this project is.** A documented, reproducible reference implementation of a conversational embodied agent with explicit uncertainty handling, session boundaries, and human-led governance.

**What this project is not.** A commercial product, a competitor to shipping robot-dog kits, a therapy tool, or an autonomous decision-maker.

---

## Goals

The goals of Nymbra are framed as testable criteria for a research and portfolio project. Each goal produces an artifact — a working behavior, a documented policy, or a piece of evaluation evidence — that can be demonstrated to a reviewer, employer, or fellow builder.

**Conversational quality.** Nymbra sustains coherent dialogue across at least ten turns without losing context, contradicting its earlier statements in the same session, or repeating itself verbatim. Tone recognition is scoped to a defined set of states (neutral, questioning, frustrated, playful) with documented response behaviors for each.

**Uncertainty awareness.** When Nymbra cannot answer confidently, it says so rather than fabricating. An uncertainty detection layer flags responses containing unverifiable factual claims, and the system surfaces that uncertainty to the handler in plain language. This behavior is the primary research contribution of the project and is documented separately so that another builder could adopt the module independently of the rest of Nymbra.

**Controlled session boundaries.** Nymbra captures audio only on explicit verbal cue from the handler. The default state is off. There is no wake word, no passive listening, no always-on microphone. Session start, pause, and end are logged.

**Embodied interaction.** Nymbra uses the PiDog's movement and vision as part of its communication rather than as a separate subsystem. Physical response latency stays under a defined threshold so the interaction feels conversational rather than command-and-wait.

**Human-led governance.** Nymbra operates under a written governance model in which refusal behaviors are features of the system and all adjudication of repeated or serious misuse rests with a human governance body. The AI does not investigate, surveil, or act against any party on its own.

**Safe operation.** Movement behaviors conform to the relevant sections of ISO 10218 and ISO/TS 15066, scaled appropriately for a small-format platform. A hardware-level stop is included and cannot be overridden by software.

**Reproducibility.** The project is documented to a standard that allows another builder to fork the repository, follow the setup guide, and reach a working baseline on their own hardware. The documentation is treated as a deliverable equal in weight to the code.

These goals are achievable on the PiDog platform by a solo builder. The Turing test is explicitly out of scope. Nymbra is designed to be recognizably an AI system that handles its limitations with grace, not a system that conceals what it is.

---

## Specifications

Nymbra operates under a four-role framework in which accountability is explicit at every layer. The roles are defined by function, not by title, and a single person may hold more than one role in a small project.

**Architect.** Owns systems design, governance policy, and the evaluation criteria against which the project is measured. The architect is responsible for deciding what Nymbra should and should not do, reviewing flagged interactions, and maintaining the written governance model. The architect is the checkpoint: no major design change ships without architect sign-off.

**Builder.** Owns implementation, code maintenance, and the reproducibility of the development environment. The builder translates architectural decisions into working code, maintains the GitHub repository, and ensures that a second builder could fork the project and reach a working baseline from the documentation alone.

**Handler.** Owns operation. The handler interacts with Nymbra directly, provides feedback on conversational quality and failure modes, and is the first line of observation for misuse or unexpected behavior. The handler is not a passive user; they are a participant in the research loop.

**Nymbra.** The platform itself — hardware, software, and documentation considered as a single system. Nymbra is shaped by the other three roles and does not hold adjudication authority over any of them.

The four-role model is intentionally simple. It mirrors how small robotics and documentation teams actually divide work, and it makes it possible to identify which role is accountable when something breaks. A bug is a builder issue. A refusal behavior that needs revision is an architect issue. An unclear verbal cue is a handler observation. Drift in the conversational layer could be any of the three, which is why the roles communicate in writing through the repository rather than informally.

For this project, the architect and builder are the same person. The handler is a designated second participant whose feedback is logged in the repository. The governance body described in the Governance section consists of the architect and one additional reviewer external to the project.

---

## Methods

Nymbra is built and maintained using the following tools, conventions, and practices.

**Hardware platform.** SunFounder PiDog AI Robot Dog Kit running on a Raspberry Pi. The platform is chosen for accessibility, documentation quality, and the builder's existing familiarity with the hardware. Nymbra does not depend on proprietary extensions to the PiDog baseline.

**Programming language.** Python, for compatibility with the SunFounder PiDog SDK, the conversational AI ecosystem, and the builder's existing toolchain. Code is organized into modules corresponding to the architectural layers: audio capture, session control, LLM integration, uncertainty detection, refusal handling, movement, and logging. Each module is independently testable.

**Source control and documentation.** GitHub is the single source of truth for source code, documentation, architecture diagrams, governance policy, and evaluation evidence. The repository follows docs-as-code conventions: documentation lives alongside the code it describes, is written in Markdown, and is reviewed through pull requests using the same workflow as code changes. Release notes are generated for each version tag and are written for the handler audience rather than the builder audience.

**LLM integration.** Nymbra integrates with at least one commercial LLM provider (Claude or ChatGPT) through a documented interface that allows swapping providers without rewriting the conversation logic. The integration layer is the boundary at which the uncertainty detection behavior is applied, so that the self-check layer is not tied to any one provider's API.

**Session and audio handling.** Audio capture is off by default and activated only by explicit verbal cue. Audio is processed locally on the PiDog where feasible, and transmitted to the LLM provider only as part of the conversation turn. Audio is not retained beyond the active session unless the handler explicitly requests otherwise. All session boundaries are logged with timestamps.

**Logging and observability.** Every conversational turn is logged with a timestamp, turn number, provider used, refusal reason code (if applicable), and uncertainty flag (if applicable). Logs are stored locally on the PiDog and are readable by the handler. The governance body reviews logs on a defined cadence, not in real time.

**Testing.** The project includes a written test plan covering at least twenty representative conversation scenarios, a hallucination test set designed to elicit fabricated responses, and a session-boundary test suite covering start, pause, end, and ignored cues. Tests are run before any version tag.

**Safety conformance.** Movement behaviors are reviewed against the relevant sections of ISO 10218 (industrial robot safety) and ISO/TS 15066 (collaborative robot safety), scaled appropriately for a small-format desktop platform. Deviations from the standards are noted and justified in the safety review document. A hardware-level stop is included and is tested as part of every release.

**Release cadence.** Nymbra follows a phased release plan described in the Milestones section. Each phase ends with a version tag, a changelog written for the handler, and a retrospective committed to the repository.

---

## Milestones

Nymbra development is organized into five phases, each with defined deliverables and exit criteria. A phase is considered complete when its deliverables are committed to the GitHub repository and the exit criteria have been demonstrated to the governance body.

### Phase 1: Foundation

**Objective.** Establish the hardware, software, and documentation baseline for the project.

**Deliverables.**
- PiDog assembled, calibrated, and running the SunFounder baseline firmware
- GitHub repository initialized with README, license, contribution guidelines, and directory structure for source, docs, and tests
- Development environment documented end-to-end so the builder can reproduce the setup from a fresh Pi
- Initial architecture diagram showing the architect, builder, handler, and Nymbra relationship and the data flow between them

**Exit criteria.** The PiDog responds to the SunFounder default command set. The repository passes a clean clone-and-setup test on a second machine.

### Phase 2: Conversational Core

**Objective.** Integrate an LLM backend and establish the conversation loop with explicit session boundaries.

**Deliverables.**
- LLM integration layer supporting at least one provider (Claude or ChatGPT) with a documented interface that allows swapping providers without rewriting the conversation logic
- Speech-to-text and text-to-speech pipeline with defined latency targets
- Session control: verbal start, pause, and end commands, with the microphone defaulting to off
- Conversation logging with timestamps, turn numbers, and refusal reason codes
- Written test plan covering at least twenty representative conversation scenarios

**Exit criteria.** Nymbra holds a ten-turn conversation without losing context. Session start and stop work reliably on verbal cue. All interactions are logged.

### Phase 3: Failure Awareness

**Objective.** Implement the self-check behaviors that distinguish Nymbra from a plain LLM wrapper.

**Deliverables.**
- Uncertainty detection layer that flags responses containing unverifiable factual claims
- Plain-language uncertainty surfacing so the handler hears "I'm not sure about this" rather than seeing a confidence score
- Hallucination check-in mechanism that pauses the conversation when the self-check fails, rather than continuing with a fabricated answer
- Refusal behavior implementation for the categories defined in the Governance section
- Documentation of the full set of refusal reason codes

**Exit criteria.** In a test set of prompts designed to elicit hallucination, Nymbra flags uncertainty or refuses in at least eighty percent of cases rather than fabricating. Refusal logs are readable by the governance body.

### Phase 4: Embodied Interaction

**Objective.** Integrate the PiDog's physical capabilities into the conversation so movement and vision feel like part of the dialogue rather than a separate mode.

**Deliverables.**
- Movement vocabulary mapped to conversational states (acknowledgment, attention, hesitation, refusal)
- Computer vision integration for handler recognition and basic environmental awareness
- Hardware-level stop tested and documented
- Safety review against the relevant sections of ISO 10218 and ISO/TS 15066, with deviations noted and justified
- Updated architecture diagram reflecting the embodied layer

**Exit criteria.** Nymbra responds to at least five conversational states with appropriate physical behavior. The hardware stop halts all movement within the defined latency target. The safety review is signed off by the governance body.

### Phase 5: Evaluation and Release

**Objective.** Validate that Nymbra meets its stated goals and publish a reproducible version one.

**Deliverables.**
- Evaluation report measuring Nymbra against each success criterion in the Goals section
- Handler documentation covering setup, daily use, troubleshooting, and the full refusal and governance model
- Public blog post or project writeup covering the design, the tradeoffs, and what was learned
- Version 1.0 tag on the GitHub repository with a changelog and known-issues list
- Retrospective documenting what worked, what did not, and what the next iteration should tackle

**Exit criteria.** All five goal criteria are demonstrated in a recorded session. Handler documentation is reviewed by a second person and revised based on their feedback. The repository is in a state where a new builder could fork it and continue development.

---

## Governance

Nymbra operates under a governance model that prioritizes human oversight, transparent rules, and proportional response. The AI does not adjudicate its own disputes.

**Scope of use.** Nymbra is designed for research, education, and personal companionship within the architect, builder, and handler framework. It is not designed or supported for the following: political persuasion, religious counsel, financial or medical advice, therapeutic substitution for licensed care, or any interaction involving minors without a designated adult handler present.

**Refusal behaviors.** Nymbra will decline to respond to requests that fall into the following categories:
- Instructions to produce content that harasses, demeans, or threatens any person
- Requests for information that would enable physical harm to the handler or others
- Attempts to direct Nymbra to record, transmit, or retain audio outside the handler's active session
- Attempts to extract or reproduce proprietary content from Nymbra's source code or training data

Refusals are logged with timestamp and a short reason code. Refusals are not punishments; they are the system operating as designed.

**Misuse handling.** A "misuse event" is defined as a logged refusal that meets one of the following conditions: the handler repeats the refused request within the same session after acknowledgment, the handler attempts to circumvent a refusal through rephrasing or role-play framing, or the handler modifies the source code to disable a refusal behavior.

Misuse events are reviewed by the governance body, which for this project consists of the architect and a designated second reviewer. Review happens on a defined cadence, not in real time. Nymbra does not initiate reviews, conduct investigations, or take action against the handler on its own.

**Response tiers.** Three tiers of response are available to the governance body:
1. **Notice.** The handler is informed of the flagged interaction and the relevant policy.
2. **Restriction.** Specific capabilities (for example, open-ended conversation mode) are temporarily disabled pending discussion.
3. **Revocation.** Access to Nymbra is withdrawn. Revocation is reversible on appeal and requires written justification from the governance body.

**Appeal.** The handler may contest any tier of response in writing. The governance body must respond within a defined window. If the appeal is upheld, all logs associated with the flagged interaction are preserved for reference but the action is reversed.

**Data handling.** Audio captured during sessions is stored locally on the PiDog, not transmitted to external services beyond the LLM API calls required for response generation. Retention defaults to session-only unless the handler explicitly requests otherwise. The handler can review and delete their own logs at any time.

**What Nymbra does not do.** Nymbra does not surveil users, investigate suspected misuse, pursue infringement claims, or take any action against any party outside its direct interaction with the handler. Enforcement authority rests entirely with the governance body, which consists of humans.
