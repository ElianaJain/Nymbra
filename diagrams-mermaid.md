# Nymbra Architecture Diagrams — Mermaid Versions

These are text-based alternatives to the SVG diagrams in this directory. They render automatically on GitHub and in most Markdown viewers. Use these if you prefer editing diagrams in text, or embed them directly in other Markdown files.

---

## Context Diagram (Four-Role Framework)

```mermaid
graph TB
    subgraph Governance["Project Governance"]
        direction TB
        A["<b>Architect</b><br/><i>Head</i><br/>Systems design<br/>Governance policy<br/>Evaluation criteria"]
        B["<b>Builder</b><br/><i>Hand</i><br/>Implementation<br/>Repository<br/>Environment"]
        H["<b>Handler</b><br/><i>Heart</i><br/>Operation<br/>Feedback<br/>First-line observation"]
        N["<b>Nymbra</b><br/><i>Platform</i><br/>Hardware<br/>Software<br/>Documentation"]

        A -->|design decisions| B
        B -.->|implementation| A
        A -->|governance policy| H
        H -.->|flagged events| A
        B -->|builds / maintains| N
        N -.->|system state| B
        H -->|interacts with| N
        N -.->|responds| H
    end

    classDef role fill:#ffffff,stroke:#2b2b2b,stroke-width:2px,color:#1f2937
    classDef platform fill:#1f2937,stroke:#1f2937,stroke-width:2px,color:#fafaf7
    class A,B,H role
    class N platform
```

---

## Component Diagram (Conversation Turn Data Flow)

```mermaid
flowchart TB
    HV[/"Handler voice<br/><i>Explicit verbal cue</i>"/]
    SC["<b>Session Control</b><br/>Start / pause / end"]
    AC["<b>Audio Capture</b><br/>STT, buffer, timestamp"]
    LLM["<b>LLM Integration</b><br/>Provider-agnostic interface<br/>Claude / ChatGPT / other"]
    UD{{"<b>Uncertainty Detection</b><br/><i>RESEARCH CORE</i><br/>Flags unverifiable claims"}}
    RH["<b>Refusal Handling</b><br/>Governance-defined decline<br/>Logs reason codes"]
    DG{"<b>Uncertain or refused?</b>"}
    CR["<b>Check-in Response</b><br/>'I'm not sure about this'<br/>Pauses, asks handler"]
    RS["<b>Response Synthesis</b><br/>TTS + movement cue<br/>Embodied delivery"]
    LOG[("<b>Logging</b><br/><i>Sidecar</i><br/>Turn, timestamp, flags<br/>Local storage only")]

    HV -->|verbal cue| SC
    SC -->|session active| AC
    AC -->|transcript| LLM
    LLM -->|response| UD
    UD -->|check| RH
    RH --> DG
    DG -->|yes| CR
    DG -->|no| RS

    SC -.-> LOG
    AC -.-> LOG
    LLM -.-> LOG
    UD -.-> LOG
    RH -.-> LOG
    CR -.-> LOG
    RS -.-> LOG

    classDef standard fill:#ffffff,stroke:#2b2b2b,stroke-width:1.5px,color:#1f2937
    classDef accent fill:#fef3e7,stroke:#92400e,stroke-width:2px,color:#1f2937
    classDef external fill:#f3f4f6,stroke:#6b7280,stroke-width:1.5px,color:#1f2937
    classDef sidecar fill:#e5e7eb,stroke:#6b7280,stroke-width:1.5px,color:#1f2937
    class SC,AC,LLM,RH,RS standard
    class UD,CR accent
    class HV external
    class LOG sidecar
```

---

## Which version to use

Both the SVG and Mermaid versions describe the same architecture. Choose based on where the diagram needs to live:

- **SVG** — when embedding in documentation, presentations, or when visual refinement matters (portfolio pages, blog posts, slide decks). Edit the source directly or regenerate from Claude.
- **Mermaid** — when the diagram lives in Markdown, needs to be diffable in pull requests, or when multiple contributors will edit it over time. Renders automatically on GitHub.

For this project, the SVGs are the canonical versions (they live at `context.svg` and `components.svg` in this directory). The Mermaid versions exist as backups and for easy embedding in other Markdown files across the repository.
