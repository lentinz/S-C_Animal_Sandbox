# Strength & Conditioning Virtual Sandbox: RHINO

A browser sandbox for strength and conditioning education. Students plan three 4-week training blocks for a rhino, with a baseline test and a retest after each block, and watch the rhino's performance change over a 12-week run. The rhino has three tasks: push a boulder up a mound, pull a stump out of the ground, and toss a tire over a river. Each task needs a different kind of training, so the right program depends on the goal.

**Anchor principle: specificity.** Training must match the quality being built.
**Running theme: Goldilocks.** Too little stimulus gives no adaptation. Too much causes breakdown. The skill is finding the zone and moving it as the animal adapts.

Built with Claude (Anthropic). The model is directional, not exact, and it may contain errors. It is not a clinical tool.

## Status

| Version | State |
| --- | --- |
| 3 (idealized base model) | Final. This is the current build |
| 4 | Planned: audit fixes, About page, learning objectives, sandbox and guided modes, end-of-debrief questions, PDF report |
| 5 | Planned: realistic model (budget, outside stress, missed sessions, rhino traits, noise, group roles) |
| 1 and 2 | Earlier builds, kept for reference |

The v3 audit is in `RHINO-v3-Audit-Report.docx` (and `.md`).

## Try it

Open `dist/rhino-v3-base.html` in any browser. It is one self-contained file with no server and no network use, so it also works from a course site or an LMS upload. Run history is kept in the browser's local storage (key `rhino-v3-base-runs`).

## How a run works

1. Take the baseline test.
2. Plan a block: load, reps, sets and rest for each task, plus sessions per week, fueling, recovery and an optional deload week.
3. Press Train. The rhino demonstrates one set of each task. Then a week-by-week summary shows how energy and soreness changed across the block.
4. A block-complete screen points to the diary. The student then chooses to run the retest.
5. Adjust the plan and repeat for three blocks. After the last retest the debrief opens.

If the rhino is injured or overtrained, the student decides to continue, modify the plan, or rest 1 to 4 weeks.

## The model in brief

- **Three lifts**, each shown as percent of the way to a goal (50% at baseline, 100% at the goal). Force is measured in Rhino Units (RUs); every max starts at 100 RUs.
- **Goals** are 126, 126 and 122 RUs for push, pull and toss. The rhino has a fixed adaptive capacity of 63 RUs across all three lifts, so two goals can be reached and three cannot. The best pair margins found by search are about 2.7 to 3.0 RUs; the best attempt at all three falls about 1.4 short.
- **Weekly gain** is a best-case rate multiplied by modifiers: intensity and reps, dose and frequency, rest, fueling and recovery, novelty (accommodation), headroom, and readiness.
- **Gain sources** shown in the debrief: nervous system adaptations, muscle size, work capacity (push only) and carryover from other lifts.
- **Toss speed of effort** is a 0 to 100 slider. The speed the rhino actually produces depends on load as well, and is never shown. It feeds rep quality and toss gains.
- **Two fatigue pools:** peripheral (volume, short rests, low fueling) and central (heavy loads, failed sets, all-out toss speed). Each lift weighs them differently. Fatigue hides strength at test time, and a deload clears it. A deload helps little after an easy block and a lot after hard ones.
- **Tissue stress** rises with sudden jumps in workload, very high weekly load, high fatigue and failed sets. At 100 the rhino is injured. Overtraining triggers when peripheral fatigue stays high.
- **Hidden from the student during a run:** the fatigue pools, tissue stress and the speed value. The debrief reveals them.
- The engine is deterministic: the same plan and decisions always give the same result.

The full design is in `Version-2-Build-Spec.md` (sections 12 to 21 cover version 3) and `Animal-Training-Sandbox-Design-Outline.md`.

## Files

| Path | Purpose |
| --- | --- |
| `dist/rhino-v3-base.html` | Standalone v3 page (open this) |
| `dist/rhino-v3-artifact.html` | The same page as a fragment, for publishing as a Claude artifact |
| `src/engine-v3.js` | The model: every rule and number |
| `src/v3/page.html`, `app.js`, `debrief.js` | Page, planner and animation, debrief |
| `build_v3.py` | Inlines the engine and scripts into the two files in `dist/` |
| `tests/v3/` | Tests and search tools (see `tests/README.md`) |
| `Version-2-Build-Spec.md`, `Animal-Training-Sandbox-Design-Outline.md` | Design documents (`.docx` copies alongside) |
| `RHINO-v3-Audit-Report.md` | The v3 audit: findings, evidence table, v4 plan |
| `src/engine-v2.js`, `src/v2/`, `build_v2.py`, `dist/rhino-v2-*.html`, `tests/*.js` | Version 2 |
| `src/model.js`, `src/page.html`, `src/try.js`, `dist/rhino-prototype.html`, `dist/artifact.html` | Version 1 prototype |

## Build and test

Run from the project folder. Node 18 or newer and Python 3 are needed.

```
python3 build_v3.py                          # rebuild dist/ after editing src/
node tests/v3/acceptance.js                  # 80 engine checks, about 30 seconds
node tests/v3/baseline-snapshot.js check     # confirms the engine still matches the frozen v3 results
node tests/v3/playthrough.js normal          # headless run of the page; also: injury, phone, dark
```

The playthrough needs Playwright (`npm install playwright`, then `npx playwright install chromium`). It saves screenshots to `tests/v3/shots/`, which is not tracked in git.

Change the engine in `src/engine-v3.js`, then rebuild. Do not edit the files in `dist/`.

## Tuning

Every number lives in `CFG` at the top of `src/engine-v3.js`. After changing a rate or threshold, re-run `node tests/v3/frontier.js` and the acceptance checks, because the goal margins and score bands are set from them. Reference values:

| Item | Value |
| --- | --- |
| Sound balanced plans score | 84 to 92 out of 100 |
| Lopsided but safe plans score | 70 to 81 |
| Best plan found by search | 97 |
| Deload effect (test-sum change), moderate plan | block 1: -0.9, block 2: +0.2, block 3: +1.6 |
| Deload effect, hard plan | block 1: +16.5, block 2: +30.8, block 3: +31.4 |

`tests/v3/baseline-v3.json` freezes the v3 results. The realistic version must match it exactly with realism switched off.

## Known limits

- Real phone touch, Safari, screen readers and web fonts are untested. Many small controls are under 44 px (see the audit, C4).
- The "every pair of goals is reachable" result comes from a heuristic search, so margins are a lower bound.
- The model simplifies several things on purpose (fixed capacity, workload-spike injury rule, repetitions-at-percent chart). The audit lists them for the About page.

## Characters (planned)

| Animal | Quality | Possible course module | Signature obstacle | Status |
| --- | --- | --- | --- | --- |
| Rhino | Maximal strength, pull strength and explosive power | Muscle performance | Plateau, taught through progressive overload and periodization | Built (v3) |
| Tiger | Muscle growth (hypertrophy) | Muscle performance | Overuse injury from too much volume | Planned |
| Sled dog | Endurance | Aerobic training | Overreaching or burnout, or a stall from one steady pace | Planned |
| Gazelle | Jump, speed and agility | Integration modules | Skill practice sets how much of the physical ceiling is used | Planned |
| Stretchable monkey | Mobility and flexibility | Flexibility and mobility | Strain from too much stretch without control | Possible |
| Mountain goat | Balance | Balance | Falls as the ledge terrain gets harder | Possible |

Posture does not fit the trainable-quality model and stays a regular module.

### Adding a new animal

1. Name the quality and the single best-fit training pattern.
2. List the inputs the student controls.
3. Define the modifiers that reward the best-fit pattern and penalize the others.
4. Define the goals and the test for each.
5. Choose the signature obstacle, its hidden trigger, and its visible symptom.
6. Draw the animal with one or two parts that change as the quality improves.
7. Test the model against five plans before building the page: ideal, the same plan three times, the wrong quality, an overreach, and a big jump in load.

When a second animal exists, split the shared structure (inputs, weekly loop, meters, tests, scoring, findings) from the per-character content (configuration and art).

## Open questions

- Where do the learning objectives come from, and how many questions per debrief?
- How does the sandbox connect to graded work, if at all?
- Should the sled dog use longer blocks?
