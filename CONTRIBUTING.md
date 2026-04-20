# Contributing to Nymbra

Thank you for considering a contribution. Before you start, please read this document in full. Nymbra is a research and portfolio project with specific design principles, and contributions that conflict with those principles are unlikely to be accepted — not because the contribution is bad, but because the project is scoped deliberately.

---

## What Nymbra is looking for

Nymbra welcomes contributions that:

- Improve the **uncertainty detection** layer or propose new patterns for surfacing uncertainty to handlers
- Strengthen the **refusal behavior** categories with additional edge cases, documentation, or test coverage
- Improve **reproducibility** — clearer setup instructions, platform compatibility notes, container definitions
- Expand **test coverage**, especially the hallucination test set and session-boundary tests
- Improve **handler-facing documentation** based on observed confusion or missing information
- Extend the **safety review** with additional standards, hazard analyses, or failure-mode documentation
- Fix **bugs** with clear reproduction steps

## What Nymbra is not looking for

The following are out of scope. Please do not open pull requests or issues for these:

- **Always-on listening, wake words, or passive audio capture.** The default-off microphone is a core design commitment, not a limitation to be removed.
- **Autonomous action against users.** Nymbra does not investigate, surveil, or enforce. Features that give the AI unilateral authority over the handler are out of scope.
- **Commercial productization features** — subscription handling, user accounts, telemetry, analytics. Nymbra is not a product.
- **Therapy, companionship, or wellness framing** in documentation or interfaces. Nymbra is a research platform, not a support tool.
- **Turing-test-style deception features** — anything that makes Nymbra harder to recognize as an AI.
- **Integration with third-party services beyond LLM providers** without prior architect review.

If you are not sure whether your idea is in scope, open a discussion issue before writing code.

---

## Before you contribute

1. **Read the README.** It describes what the project is and what it is not.
2. **Read the Handler Guide.** Even if you are contributing code, understanding how the system is meant to be used will help you write better contributions.
3. **Read the Governance section.** Nymbra has a written governance model. Contributions that bypass, weaken, or contradict the model will be declined.

---

## How to contribute

### Small changes (typo fixes, clear bug fixes, documentation improvements)

- Fork the repository
- Create a branch named `fix/short-description` or `docs/short-description`
- Make the change
- Open a pull request with a clear description of what and why

These can skip the discussion step. Small means "the intent is obvious and the diff is under about fifty lines."

### Larger changes (new behaviors, refactors, module additions)

- **Start with an issue.** Open an issue describing what you want to change and why. Tag it `proposal`.
- **Wait for architect review.** The architect will respond with one of: approved, needs-discussion, out-of-scope.
- **Once approved**, fork, branch, and implement. Reference the issue number in the pull request.

Skipping the issue step for large changes is not prohibited, but pull requests without prior discussion are more likely to be declined or sent back for rework.

### Governance-adjacent changes

Changes to any of the following require architect review before implementation:

- Refusal behavior categories or reason codes
- Session boundary logic (start, pause, end, logging)
- Uncertainty detection thresholds or surfacing language
- Safety review documentation
- The governance policy itself

These are the parts of the project where the design matters more than the code. Please do not surprise the architect with a pull request that changes them.

---

## Code conventions

### Language and style

- Python 3.11+
- Follow PEP 8, with exceptions documented in `pyproject.toml`
- Type hints on all public functions and class methods
- Docstrings on all public functions, classes, and modules

### Testing

- New behaviors need new tests
- Tests live in `tests/` mirroring the `src/nymbra/` structure
- The hallucination test set is a deliberate artifact; additions are welcome and are reviewed for diversity of failure modes, not just coverage

### Logging

- Every conversational behavior writes a log entry
- Log entries are structured JSON
- Do not add logging that captures content beyond what the existing schema documents
- Do not remove or downgrade existing log entries without architect review

### Documentation

- Documentation lives alongside the code it describes
- Written in Markdown
- Reviewed through pull requests using the same workflow as code
- If you change a behavior, update the handler guide in the same pull request if the change is handler-visible

---

## Commit messages

- First line: short summary, 72 characters or fewer, imperative mood ("Add refusal reason code R7")
- Blank line
- Body: what and why, not how
- Reference issue numbers where applicable

Commit messages are part of the project record. Write them as if the person reading them has no context — because six months from now, neither will you.

---

## Pull request expectations

A good pull request:

- Does one thing
- Has tests
- Updates documentation in the same PR, not a follow-up
- References an issue if one exists
- Passes CI

A pull request will be reviewed within a reasonable window, but reviews are not always fast. This is a solo-maintained project with active phase work. If you have not heard back in two weeks, feel free to ping the issue.

---

## Code of conduct

Be kind. Assume good faith. Explain your reasoning when disagreeing. Do not attribute disagreement to malice, incompetence, or bad values.

The maintainer reserves the right to close issues, decline pull requests, or block contributors whose behavior is hostile, harassing, or persistently in conflict with the project's stated scope.

---

## A note on scope

Most open-source projects grow by accepting contributions that expand their feature set. Nymbra is deliberately the opposite kind of project — it grows by becoming better at a narrow set of behaviors, not by adding new ones. If your contribution is declined as out-of-scope, it is not a statement about the quality of your work. It is a statement about what this project is for.

If you have an idea that is out of scope here but could stand on its own, consider forking. Nymbra is designed to be reproducible specifically so that others can build adjacent projects without needing to fit everything into this one.

---

## Questions

Open a discussion issue tagged `question`. Do not email the maintainer directly for project-related questions — the repository is the record of the project, and decisions that happen in email cannot be referenced later.
