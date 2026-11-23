---
name: wave-ideal
description: Use when the owner asks whether anything else can be done so an area works "ideally" - even if it takes a grill, a refactor or a whole wave - or asks what else can be improved here; when just-closed work leaves a "works, but not quite" feeling; or before deciding whether an area deserves further investment.
---

# The gap to ideal

## Overview

The owner asks: *"Is there anything else worth doing here so it works ideally?"* or *"What
else can be improved here?"* This skill answers with a **judgment, not code**. It measures
the gap between how an area works now and how it should work. Every gap it reports has to
earn its cost, and the skill says **when** to close it, given the current queue.

Core principle: **ideal means nothing is left that will bite, not that nothing is left to
add.** A gap exists only when you can name the concrete failure it causes or will cause. A
gap without a failure is taste, and taste is dropped.

"Even if it takes a grill, a refactor or a big wave" **permits** a large answer. It does not
**require** one. "The ideal is reached here; anything further is added complexity" is a
complete, valid result.

## 1. Bound the subject and name its job

- **Subject:** one area, such as the link just closed, a feature, a module or a flow. Never
  the whole repository.
- **Job:** what the area guarantees: its contract to users or callers, its data invariants,
  and its behaviour at the edges. Write it in two or three sentences. The ideal is measured
  against this job, never against an imagined "better architecture".

## 2. Measure the gap from evidence

Read the code, tests, schema and history, not your memory of them. If the pass spans many
files, delegate the sweep to a subagent and keep only its conclusion. Walk the lenses, and
under each one ask: *what breaks here, and who notices?*

| Lens | Question |
|---|---|
| Correctness | Edge cases, races, partial failures, empty and null values |
| Invariants | Is the rule held by **structure** (constraint, type, guard) or by **convention** that the next change can silently break? |
| Falsifiability | Is there a test that goes red if the behaviour is removed? |
| Design | Duplication, logic in the wrong place, a leaking seam, two sources of truth |
| Operability | Is a failure visible when it happens? Does the state recover? |
| User experience | Does the person see the right thing, including on errors and empty states? |

Record each candidate with:

- **Evidence:** `file:line`, or a command and its output.
- **Failure:** a concrete scenario of the form input or state → wrong result.
- **Likelihood and blast radius:** how often it happens and what it touches (data, every
  user, one screen).
- **Cost:** a fix, a refactor, or a grill plus a wave.
- **Trajectory:** whether the fix gets more expensive over time (new callers, accumulating
  data, a hardening schema) or stays the same.

## 3. The balance filter

Drop a candidate when any of these holds:

- There is no concrete failure scenario, only "cleaner" or "more correct".
- It guards against something the current product scope rules out (YAGNI).
- The fix adds more moving parts than the failure costs.
- Planned work will soon replace the area, so polishing what is about to disappear is
  wasted.

**Net-simplicity test:** after the fix, is the system simpler to reason about? A refactor
that removes concepts, code or sources of truth almost always pays for itself. One that adds
a layer, an abstraction or a config knob must pay for itself with a named failure.

List dropped candidates with the reason they were dropped, so nobody raises them again.

## 4. A verdict per gap

| Verdict | When |
|---|---|
| **Do now** | Small, inside the current work, and the context is still hot |
| **Fix list** | The decision is clear and the size is medium: its own commit or link |
| **Needs a grill** | The decision is open with several options. Give the options and your recommendation |
| **Needs a wave** | A structural change. Sketch the slice: precondition, done-state, gates |
| **Accept knowingly** | Known debt. Record it with the **trigger** that reopens it |

## 5. When, relative to the current queue

First read the queue: open issues, plans, the running wave and the release boundary. The
ideal is judged against what is already in flight, never in a vacuum.

**Earlier than its priority suggests** when:
- the fix gets more expensive with time (data migrates, callers multiply, contracts drift);
- the gap lies on the path of planned work. In that case **fold it into** that work instead
  of doing it separately;
- the gap blocks the release or a current task.

**Later** when:
- the area is stable and nothing planned touches it;
- the current queue is worth more and the gap does not get more expensive;
- planned work will dissolve the gap anyway.

**Never** interrupt in-flight work for a non-blocking ideal.

State "when" as an anchor, not a date: "before link X", "fold into plan Y", "when Z
happens".

## 6. The reply

The order is fixed:

1. **One-sentence verdict:** "the ideal is reached", or "N gaps are worth the work; the most
   important is …".
2. **Gap table**, most important first: gap · failure · cost · verdict · when.
3. **Dropped:** one line per candidate, with its reason.
4. **One recommended next step.**

The skill implements nothing. Action starts only after the owner says yes: a grill goes
through `wave-grill`, and a wave goes through a slice and a plan.

## Common mistakes

| Mistake | Fix |
|---|---|
| A 15-item list "just in case" | Only gaps with a named failure. Everything else goes under Dropped |
| A rewrite proposed without a failure scenario | No failure, no gap |
| "Do now" for everything | Check the queue. "Now" needs a reason from section 5 |
| A wave because the owner allowed a wave | Permission is not an order. The failure sets the scale |
| Auditing from memory | Every gap carries `file:line` evidence |
| An empty reply because "it's all fine" | "Ideal reached" needs evidence too: which lenses were walked |
