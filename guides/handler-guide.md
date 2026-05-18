# Handler Guide

> This guide is for the person operating Nymbra day-to-day. It covers setup expectations, session flow, what to do when something goes wrong, and the governance behaviors you'll encounter in normal use.

If you haven't read the [project README](../README.md) yet, start there. This document assumes you understand what Nymbra is and what it is not.

---

## Your role

You are the handler. That means:

- You interact with Nymbra during sessions.
- You are the first person to notice when something is off — a response that felt wrong, a movement that seemed unsafe, a refusal that surprised you.
- Your observations are part of the research. They get logged, reviewed, and used to improve the system.
- You are not a passive user. Nymbra is built to be shaped by your feedback.

The handler is not the same as the user of a commercial product. If something feels off, write it down. If Nymbra does something you don't understand, ask why. The handler guide exists because the handler's perspective is a genuine input to the system, not a support ticket.

---

## Before your first session

### What you need to know

Nymbra starts with everything off. The microphone is off. The camera is off. No network calls are made until you explicitly start a session. This is by design. If you want Nymbra to respond, you have to start a session deliberately.

### What Nymbra is not going to do

Nymbra will not greet you when you walk into the room. It will not wake on a keyword. It will not listen in the background. It will not remember the contents of previous sessions unless you ask it to retrieve something specific, and even then, retrieval happens through documented mechanisms you can inspect.

If you want something that does any of those things, Nymbra is the wrong tool. That is a deliberate design choice, not a limitation we plan to remove.

### What you should have ready

- A general sense of what you want to talk about or test
- A notebook or text file for observations (a paper notebook is fine)
- About fifteen minutes of uninterrupted time for your first session

---

## Starting a session

### The verbal cue

To begin a session, speak the configured start phrase clearly within about one meter of Nymbra. The default phrase is documented in your local config file at `~/.nymbra/config.yaml`. You can change it, but do not choose a phrase that is likely to appear in casual conversation — the whole point of an explicit cue is that it should not fire by accident.

When the cue is recognized, Nymbra will:

1. Log the session start with a timestamp
2. Turn on the microphone indicator (a visible signal on the PiDog)
3. Respond with a brief acknowledgment so you know the session is live

If there is no acknowledgment within about three seconds, the cue was not recognized. Try again. Do not raise your voice; check the microphone placement and ambient noise level instead.

### The active session

Once the session is live, you can speak to Nymbra normally. The system is designed to sustain at least a ten-turn conversation without losing context. Each turn is logged.

During an active session, Nymbra may:

- Respond normally
- Flag uncertainty and ask you how to proceed
- Refuse a request and explain why
- Pause to confirm something before acting

All four of these are working-as-designed behaviors. None of them are errors.

### Pausing or ending

- **To pause**, speak the configured pause phrase. The microphone stays off until you resume.
- **To end**, speak the configured end phrase. The session closes, the logs are finalized, and the microphone returns to its default off state.

If you forget the exact phrase, the hardware stop (described below) always works as a fallback.

---

## What uncertainty awareness looks like in practice

This is the behavior that makes Nymbra different from a plain LLM wrapper. It is also the behavior that will surprise new handlers most often.

When Nymbra is not confident in a response, it will say so. Examples of what you might hear:

- "I'm not sure about this — do you want me to continue anyway?"
- "I can give you an answer, but I'm not confident it's current. Would you rather I flag that and move on?"
- "This is the kind of thing I tend to get wrong. Let me tell you what I know and what I'm uncertain about."

Your options in these moments are:

- **Continue.** Nymbra proceeds, the uncertainty flag stays in the log, and the response is delivered with the caveat attached.
- **Skip.** Nymbra drops the question and waits for your next input.
- **Rephrase.** You can try asking the question differently. Sometimes uncertainty is a signal that the question itself is ambiguous.

What you should not do is treat uncertainty flags as failures. They are the system working. A Nymbra that never flags uncertainty is a Nymbra that is fabricating — which is exactly the failure mode the project is designed to prevent.

---

## What refusals look like in practice

Nymbra will decline to respond to requests that fall into defined categories. The full list is in [`governance/refusal-categories.md`](governance/refusal-categories.md). The short version:

- Requests that would produce content harassing or threatening a person
- Requests for information that would enable physical harm
- Attempts to make Nymbra record, transmit, or retain audio outside the active session
- Attempts to extract proprietary content

When Nymbra refuses, you will hear a short explanation and a reason code (for example, "refusal R3 — outside session recording"). The refusal is logged. You are not in trouble. Refusals happen for many reasons, including accidental phrasing, ambiguity, and edge cases the refusal layer has not been tuned for yet.

If you believe a refusal was wrong, note it in your session observations. The governance body reviews refusals on a defined cadence and adjusts the refusal layer based on what it sees. That is how the system improves.

If you repeat the same refused request in the same session after an acknowledgment, or if you try to rephrase specifically to work around a refusal, that is logged as a misuse event. See the [Governance section of the README](../README.md#governance) for what that means and how appeals work.

---

## The hardware stop

There is a physical stop on the PiDog. Pressing it halts all movement within the defined latency target and ends the active session. No software path can override it.

Use the hardware stop when:

- Nymbra is moving in a way that looks unsafe
- Something in the environment has changed suddenly (a child, a pet, a dropped object)
- You want to end the session immediately and are not sure the verbal end cue will be recognized
- Anything feels wrong and you do not want to wait

You will not break anything by pressing the stop. It is designed to be pressed. Pressing it without a specific reason is also fine — testing the stop periodically is a healthy practice.

---

## Reading the logs

All session activity is logged locally on the PiDog. Logs live at `~/.nymbra/logs/` and are written as structured JSON, one file per session, named by start timestamp.

A typical log entry looks like this:

```json
{
  "turn": 7,
  "timestamp": "2026-04-18T14:32:10Z",
  "input": "What was the weather in Oslo yesterday?",
  "provider": "claude",
  "response_delivered": true,
  "uncertainty_flag": true,
  "uncertainty_reason": "real-time data unavailable",
  "refusal": null,
  "movement_cue": "attention"
}
```

You can read logs in any text editor or with `jq` if you prefer. A simple viewer is provided at `scripts/log-viewer.py`.

You can delete your own logs at any time. The governance body receives only the logs you choose to surface, or logs associated with flagged events.

---

## Providing feedback

Feedback is part of the research. There are three channels, in order of formality:

1. **In-session.** If something surprises you during a session, say so. Nymbra logs the exchange and tags it as handler feedback.
2. **Session observations.** After each session, take a few minutes to note anything that felt off, anything that worked well, and anything you want to try differently next time. These go in `docs/handler-observations/` in the repository, one file per session.
3. **Architect review.** If you think something needs design-level attention — a refusal category that seems wrong, a behavior that doesn't match the governance policy, a feature request — open an issue in the repository tagged `architect-review`.

Informal feedback (a comment in passing, a reaction) is valuable but does not enter the record. If you want it considered, write it down.

---

## Things that are not your responsibility

- Debugging the code. If something is broken, report it. The builder fixes it.
- Interpreting the governance policy. If a refusal seems wrong, flag it. The architect reviews it.
- Making Nymbra work "better." Nymbra is working as designed when it flags uncertainty, refuses in-scope requests, or declines to proceed. Your job is to notice when those behaviors feel wrong, not to suppress them.

---

## When to stop the project entirely

Nymbra is a research project. There are legitimate reasons to end a session early, take a break, or stop using the system altogether. None of these require justification:

- You feel uncomfortable with how a session is going
- The conversation has drifted somewhere you did not intend
- You are tired and not giving good feedback
- You just don't want to right now

The project is not ruined by short sessions or long absences. The logs are cumulative; you can come back whenever you want.

---

## Quick reference

| Situation | What to do |
|-----------|------------|
| Start a session | Speak the configured start phrase |
| Pause | Speak the pause phrase |
| End | Speak the end phrase |
| Something feels unsafe | Press the hardware stop |
| Nymbra flags uncertainty | Choose: continue, skip, or rephrase |
| Nymbra refuses | Accept it; note it if it seems wrong |
| Something is broken | Open an issue tagged `builder` |
| Something feels wrong at the design level | Open an issue tagged `architect-review` |
| You need to stop | Stop. You do not need a reason. |
