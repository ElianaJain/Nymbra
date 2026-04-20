# Nymbra

A conversational AI robot dog that knows when it doesn't know.

Nymbra is a research and portfolio project exploring how an embodied agentic AI can hold natural conversation while failing gracefully at the edges of its knowledge. It runs on the SunFounder PiDog platform and treats uncertainty handling, session boundaries, and human-led governance as first-class design concerns.
Status: Phase 1 — Foundation • License: MIT • Platform: SunFounder PiDog + Raspberry Pi

# What Nymbra is
A documented, reproducible reference implementation of a conversational embodied agent with:

Uncertainty awareness — flags unverifiable claims instead of fabricating
Controlled session boundaries — microphone off by default, activated only by explicit verbal cue
Human-led governance — the AI does not adjudicate its own disputes
Embodied interaction — movement and vision as part of the conversation, not a separate mode
Safety conformance — reviewed against ISO 10218 and ISO/TS 15066, scaled for the platform
Reproducibility — documented to a standard that lets another builder fork and rebuild

# What Nymbra is not
A commercial product. A competitor to shipping robot-dog kits. A therapy tool. An autonomous decision-maker. A replacement for human connection.

# On intent
Nymbra is a research project about how an AI system can fail gracefully. The design principles that shape it: default-off behavior, uncertainty awareness, human-led governance, and explicit scope limits are documented throughout this repository and are the reason the project exists.
You are free under the license to use this code however you choose. If your use honors those principles, you are building on the work as it was meant to be built on. If your use contradicts them, you are welcome to fork, but please consider publishing under a different name. Nymbra is a small mark with specific meaning, and that meaning is part of what the project is for.

# Why this project exists
Commercial robot-dog kits now ship with LLM integration out of the box, and hobbyist conversational agents on Raspberry Pi are an established genre. What none of these systems foreground is the behavior around the conversation: how the system signals uncertainty, how it handles its own hallucinations, how it starts and stops listening, and how misuse is adjudicated by humans rather than by the AI itself.
Nymbra is an attempt to build that behavior as the primary design concern rather than as an afterthought and to document it well enough that another builder could adopt the same patterns on their own platform.

# The four-role framework
Nymbra is organized around four roles with explicit accountability:
RoleOwnsAccountable forArchitectSystems design, governance policy, evaluation criteriaDeciding what Nymbra should and should not doBuilderImplementation, code maintenance, environment reproducibilityTranslating design into working codeHandlerOperation, feedback, first-line observationInteracting with Nymbra and surfacing issuesNymbraThe platform itself: hardware, software, and documentationOperating within the boundaries the other roles define
For this project, the architect and builder are the same person. The handler is a designated second participant whose feedback is logged in the repository.

# Quick start

Phase 1 foundation work is underway. Setup instructions below will be finalized at the Phase 1 exit gate.

# Hardware

SunFounder PiDog AI Robot Dog Kit
Raspberry Pi (5 recommended; 4B supported)
MicroSD card (32 GB or larger)
USB-C power supply appropriate to the Pi

# Software prerequisites

Raspberry Pi OS (64-bit)
Python 3.11+
SunFounder PiDog SDK
Git

# Install
bashgit clone https://github.com/[username]/nymbra.git
cd nymbra
./scripts/setup.sh
First run
bashpython -m nymbra.main
Nymbra starts with the microphone off. Activate a session with the configured verbal cue (see docs/handler-guide.md).

# Project structure
nymbra/
├── docs/                    # Documentation (docs-as-code)
│   ├── architecture/        # Diagrams and design decisions
│   ├── governance/          # Written governance policy
│   ├── handler-guide.md     # Daily-use documentation
│   └── safety-review.md     # ISO conformance notes
├── src/nymbra/
│   ├── audio/               # Capture, STT, TTS
│   ├── session/             # Start, pause, end, logging
│   ├── llm/                 # Provider integration layer
│   ├── uncertainty/         # Self-check behaviors
│   ├── refusal/             # Governance-driven decline behaviors
│   ├── movement/            # PiDog motion vocabulary
│   └── logging/             # Structured interaction logs
├── tests/
│   ├── conversation/        # Multi-turn scenario tests
│   ├── hallucination/       # Fabrication-elicitation test set
│   └── session/             # Boundary behavior tests
└── scripts/                 # Setup, calibration, utilities
Each module in src/nymbra/ is independently testable and has its own README describing its public interface and the design decisions behind it.

# Roadmap
Nymbra is developed in five phases, each with defined deliverables and exit criteria.

 Phase 1 — Foundation. Hardware baseline, repository structure, development environment documented.
 Phase 2 — Conversational Core. LLM integration, STT/TTS pipeline, verbal session control, logging.
 Phase 3 — Failure Awareness. Uncertainty detection, refusal behaviors, governance-aligned decline patterns.
 Phase 4 — Embodied Interaction. Movement vocabulary, vision integration, safety review.
 Phase 5 — Evaluation and Release. Evaluation report, handler documentation, version 1.0 tag.

Full phase details are in docs/milestones.md.

# Design principles
A short list of commitments that shape every decision in this project:

Default off. The microphone, the camera, and any outbound network call default to disabled. Every activation is explicit.
Honesty over smoothness. Nymbra says "I'm not sure" instead of fabricating. This is the primary research contribution.
Humans adjudicate. Refusal behaviors are features of the system. Decisions about repeat or serious misuse are made by a human governance body, not by Nymbra.
Documentation is a deliverable. The project is not done when the code works. It is done when another builder can reproduce it from the documentation alone.
Scope is a safety feature. Nymbra is a hobbyist research platform. It is not a therapist, not an assistant for high-stakes decisions, and not a substitute for human connection.


# Governance
Nymbra operates under a written governance model summarized below. Full policy is in docs/governance/.

Refusal behaviors are defined and logged with timestamped reason codes
Misuse events are reviewed by the governance body, not by Nymbra itself
Three-tier response model: notice, restriction, revocation
All handler actions are appealable in writing
Nymbra does not surveil, investigate, or take action against any party on its own


# Acknowledgments

SunFounder — for the PiDog platform and accessible robotics documentation
Raspberry Pi Foundation — for the hardware that makes projects like this viable
The robotics safety standards community — for ISO 10218 and ISO/TS 15066, which shaped the safety review approach


# A note on the name
Nymbra is a fanciful mark, not an acronym. It echoes nimbus (the cloud or aura around a figure) and umbra (presence, shadow), suggesting something that accompanies rather than leads.

# Contact
Built by Eliana - technical writer, documentation practitioner, and someone who thinks small, well-documented robots can teach us something about how to build larger systems responsibly.

Blog: [https://elianajain.hashnode.dev/]

GitHub: [https://github.com/ElianaJain]

LinkedIn: [https://www.linkedin.com/in/elianajain]
