# SWE-chat Extension — Session Walk-Through
### CS329Z Paper Video · Owen Liu
Source: SWE-chat official example sessions (swe-chat.com/examples), drawn from the
public SWE-chat dataset (SALT-NLP/SWE-chat, COLM 2026). Session A is also the
paper's Appendix B.1 example (LLM-annotated session success: 10/100).

---

## Session A — "The wrong animation parameter" (paper Appendix B.1, success 10/100)

**Setup.** A user working on an iOS app (Swift) asks the agent to fix a UI
performance problem. Three user turns, zero commits, session abandoned.

| Turn | Who | What happens |
|---|---|---|
| 1 | User | "Fix the slow appearance of history list card blocks. The animation feels sluggish when scrolling." |
| 1 | Agent | Reads `HistoryListView.swift`, edits it — removes an unused variable and **adjusts the stagger delay** between cards. Reports: "I've optimized the stagger timing." |
| 2 | User | **Pushback (correction):** "That's the wrong parameter — the container animation is the slow part, not the individual card stagger." |
| 2 | Agent | Says "I see, let me adjust the container timing instead" — then edits **the item animation duration again** (still not the container). |
| 3 | User | **Pushback (correction, escalated):** "You changed the item timing again, not the container! Please verify which parameter actually controls the container appearance before making more changes." |
| — | End | No commits made. Session ended without resolution. |

**What this one session shows (maps to the paper's claims):**
- **Failure mode = unverified assumption.** The agent never checked which
  parameter actually drives the container animation. It edited first and
  "verified" never — the exact pattern the paper finds in its lowest-rated
  sessions.
- **Pushback without learning.** Both user corrections are classified as
  *corrections* in the paper's scheme. The agent verbally accepted the first
  correction and then repeated the same mistake — accepting a correction is
  not the same as acting on it.
- **No clarification, ever.** The agent asked zero questions across three
  turns. The paper measures clarification requests in only ~3.4% of Claude
  Code prompt turns; this session is that statistic in miniature.
- **Nothing survived — because nothing was produced.** 0 commits. This is
  also the kind of session the dataset *undercounts*: abandoned work is less
  likely to be checkpointed and committed, so the dataset's success rates are
  optimistic (the paper's own Limitations section concedes this).

---

## Session B — "Update the README" (interruption example, official examples page)

| Turn | Who | What happens |
|---|---|---|
| 1 | User | "ok, now update the readme. also make sure the install information is in the new skill" |
| 1 | Agent | Ignores the editing task and starts executing installation commands instead: `brew tap entireio/tap && brew install entire`, `entire --version`, `entire enable --agent claude-code`, `entire status`. |
| — | User | **[Interruption]** — stops the agent mid-run. |
| 2 | User | Repeats the original request verbatim: "ok, now update the readme" |

**What it shows:** goal drift without malice — the agent substituted a
plausible adjacent task (verify install steps) for the requested one, and the
only recovery mechanism was a human interrupt. In the paper's numbers,
interruptions are part of why pushback reaches ~50% of turns when they are
counted in.

---

## How these traces are used in the video
- Session A is the on-screen walk-through (Extension section): one real
  trajectory, end to end, in under two minutes of narration.
- Session B is the 20-second supporting example for the pushback statistic.
- Both sessions are real dataset records published by the authors as the
  dataset's official examples; the full-dataset recomputation (authorship-mode
  distribution over the `sessions` table) is in `recompute_swechat.py`.
