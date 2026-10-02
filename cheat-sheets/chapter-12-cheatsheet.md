# Chapter 12 Cheat Sheet — Exam Simulation & Test-Taking Strategy

## Exam Format
| Item | Value |
|---|---|
| Total questions | 45 |
| Total time | 90 minutes |
| Target pace | ~2 min/question |
| Passing basis | Single overall weighted score — **no per-domain threshold** |

## Domain Weights (memorize order)
| Domain | Weight | Approx. # Questions (of 45) |
|---|---|---|
| Databricks Machine Learning | 38% | ~17 |
| Model Development | 31% | ~14 |
| ML Workflows | 19% | ~9 |
| Model Deployment | 12% | ~5 |

**Rule of thumb:** Databricks ML + Model Development = 69% of your score. If time is short, protect these first.

## Pacing Checkpoints
| Question # | Expected elapsed time (2 min/Q) |
|---|---|
| 10 | 20 min |
| 20 | 40 min |
| 30 | 60 min |
| 45 | 90 min |

If you're past a checkpoint time without reaching that question number → **behind pace**: stop dwelling, flag, move faster.

## Flag-and-Move Protocol
1. Read scenario once, identify explicit constraints (cost, latency, scale, governance, compliance).
2. If not obvious in ~60–90 sec → pick best guess, flag, move on.
3. Never leave blank — no penalty for wrong answers, blank = guaranteed zero.
4. Use final 10–15 min buffer only for flagged questions.

## Elimination Strategy for Close Calls
- List every constraint stated in the scenario.
- Check each plausible answer against **all** constraints, not just the obvious one.
- Distractors usually satisfy only 2 of 3 constraints (e.g., cheaper but not scalable, or fast but not governed).
- Reject options that are technically valid but **outdated/deprecated** or **overkill** for stated scale.

## Terminology Traps to Drill
| Term | Meaning | Commonly Confused With |
|---|---|---|
| Data drift | Input feature distribution shifts | Concept drift |
| Concept drift | Input–output relationship shifts | Data drift |
| Model decay | General performance degradation (umbrella term) | Data/concept drift (specific causes) |

## Final-Week Checklist
- [ ] Re-review Databricks ML (38%) and Model Development (31%) notes
- [ ] Redo every previously missed practice question
- [ ] Flashcard drill on drift terminology, tracking vs. registry, deployment modes
- [ ] Complete one full 90-minute timed simulation, review pacing checkpoints
- [ ] Sleep and time-of-day plan for exam day — no new material within 24 hrs of exam
