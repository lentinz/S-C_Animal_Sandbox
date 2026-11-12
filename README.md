# Animal Training Sandbox

A browser sandbox for strength and conditioning education. Students pick an animal, set training variables, and watch the animal's body and performance change over a 12-week run. Each animal targets a different adaptation, so the right program depends on the goal.

**Anchor principle: specificity.** Training must match the quality being built. Running the rhino's program on the sled dog should visibly fail.
**Running theme: Goldilocks.** Too little stimulus gives no adaptation. Too much causes breakdown. The skill is finding the zone and moving it as the animal adapts.

Status: prototype. The rhino base model (version 3) is built. Everything else is planned.

## Version 3 (rhino base model, current)

Version 3 is built as `dist/rhino-v3-base.html` (the standalone file) and `dist/rhino-v3-artifact.html` (the page fragment). Version 2 is kept as it was.

- **Progress, not rungs:** each lift is shown as percent of the way to one goal. Every lift starts at 50% at baseline and reaches 100% at the goal. The boulder, stump and tire sit where that percent puts them. A PR marker (flag, ghost stump, ghost tire) shows only when the rhino slips below its personal best. The score uses the last test.
- **Planner:** load 20 to 100% (slider plus a typed number, following the NSCA training load chart for reps), reps, sets, and rest per exercise (0.5 to 5 min), shared sessions, fueling, recovery (was sleep) and a deload box. Block 2 and 3 keep the same absolute load, so the percentage can look lower. A note says so.
- **Demonstration set:** after Train, the student watches one set of each trained exercise. Rep dots show quality from 5 to 1 and a missed rep. Skip is available.
- **Soreness and energy:** both on a 1 to 5 scale in the diary and the stage footer. The flat-and-slow chip and the bar speed and mood readouts are gone.
- **Return from rest:** at the mid-rest check-in and when a rest ends inside a block, the student can resume as planned, resume with a modified plan or keep resting.
- **Confirmations:** train, each decision, resuming early, restarting and clearing the plan ask first. There is no back button.
- **Help:** a "?" beside Push, Pull and Toss opens the short description.
- **Debrief:** PR versus final per lift, principles at work (up to four, with a reflective question), quality of work by block, what-if replay, charts, run history (key `rhino-v3-base-runs`).
- Hands-on mode is removed.

Files: `src/engine-v3.js`, `src/v3/page.html`, `src/v3/app.js`, `src/v3/debrief.js`. Build with `python3 build_v3.py`. Tests: `node tests/v3/acceptance.js` (62 checks) and `node tests/v3/playthrough.js normal|injury|phone|dark` (headless page runs, screenshots in `tests/v3/shots`).

Still untested: real phone touch on the sliders and number box, Safari, keyboard-only use, and web fonts (the sandbox blocks them).

## Version 2 (rhino base model)

Version 2 is built as `dist/rhino-v2-base.html`. It replaces the single strength index with three lifts and three goals.

- **Three indices:** push, pull and toss (explosiveness), each starting at 100. Percentages rebase to the new tested max at every retest.
- **Three goals:** push the giant boulder to the top of the mound, pull the giant stump out of the ground, toss the tractor tire over the river. No plan can reach all three. Two is the most.
- **Planner:** load, reps and sets per lift, with shared sessions, rest, fueling and sleep. Block 1 starts blank.
- **Events:** injuries and overtraining ask for a decision (continue, modify or rest 1 to 4 weeks), with a mid-rest check-in.
- **Hands-on mode:** students push, pull and toss at the tests and at one session per block. It never changes the result, and it can be turned off.
- **Debrief:** score, findings, charts, a what-if replay and run comparison.

Files: `src/engine-v2.js` (engine), `src/v2/page.html`, `src/v2/app.js`, `src/v2/debrief.js`. Build with `python3 build_v2.py`. Engine tests are in `tests/` (see `tests/README.md`).

Still to build: the realistic model (budget, outside stress, missed sessions, rhino traits, noisy signals).

The sections below describe the overall concept and the version 1 prototype.

## Characters

| Animal | Quality | Possible course module | Signature obstacle | Status |
| --- | --- | --- | --- | --- |
| Rhino | Maximal strength | Muscle performance | Plateau, taught through progressive overload and periodization | Prototype built |
| Tiger | Muscle growth (hypertrophy) | Muscle performance | Overuse injury from too much volume | Planned |
| Sled dog | Endurance | Aerobic training | Overreaching or burnout, or a stall from one steady pace | Planned |
| Gazelle | Jump, speed and agility | Integration modules | Skill practice sets how much of the physical ceiling is used | Planned, built last |
| Stretchable monkey | Mobility and flexibility | Flexibility and mobility | Strain from too much stretch without control | Possible |
| Mountain goat | Balance | Balance | Falls as the ledge terrain gets harder | Possible |

Posture does not fit the trainable-quality model and stays a regular module.

## Run structure (all characters)

- 12 weeks in three 4-week blocks. The sled dog may need longer blocks.
- Baseline test, train a block, retest, adjust, repeat.
- One plan per block. Students may change it at each retest.
- A full run takes 10 to 15 minutes, and students can restart and compare strategies.
- Obstacle ladder: tests get harder, and passing one unlocks the next.

## Student inputs (shared)

| Input | Rhino range | Notes |
| --- | --- | --- |
| Load | 50 to 130% of starting max | Set against the starting max, so the same load gets relatively lighter as the animal strengthens |
| Reps per set | 1 to 12 | |
| Sets per session | 1 to 8 | |
| Sessions per week | 1 to 6 | |
| Rest between sets | 1 to 5 min | |
| Fueling | Low, adequate, high | Low fueling hurts more at high volume |
| Sleep | Poor, adequate, good | |

Other animals will reuse these inputs and add their own, such as pace and distance for the sled dog or skill practice for the gazelle.

## The shared engine

The engine is directional, not exact. It rewards sound principles and punishes classic mistakes. It is deterministic: the same plan always gives the same result, so students can compare strategies fairly.

Weekly gain is a best-case rate multiplied by modifiers. Each modifier is between 0 and about 1.

| Modifier | Principle it teaches |
| --- | --- |
| Intensity and reps | Specificity |
| Dose (hard sets) and frequency | Volume with diminishing returns |
| Rest | Quality of heavy sets |
| Fueling and sleep | Recovery turns stimulus into adaptation |
| Novelty | Progressive overload, plateau, periodization |
| Headroom | Gains shrink as the animal gets closer to its ceiling |
| Readiness | Fatigue suppresses gains |

Two hidden meters run underneath.

- **Fatigue (0 to 100):** builds with volume, effort, short rest and poor sleep. Above 60 the animal looks flat and slow, and test results slip.
- **Tissue stress (0 to 100):** rises with sudden jumps in workload, very high weekly load, high fatigue and failed sets. At 100 the animal is injured and loses 2 weeks of training. The injury is recoverable, so the run can finish.

Students never see the meters during a run. They see symptoms only after the problem occurs, then get a full explanation in the debrief, where the meters are revealed.

## Debrief and score

- Score out of 100: obstacle reached, efficiency (strength gained per hard set), and staying healthy.
- Plain-language findings that name the week and cause of any injury, fatigue crash, plateau or recovery problem.
- Charts for strength by week, the hidden meters, and which factors held back gains.
- A run history table so students can compare strategies across attempts.

## Files

| File | Purpose |
| --- | --- |
| `rhino-prototype.html` | Standalone rhino prototype. Open in any browser, or host it on a course site. |
| `README.md` | This file. |
| `src/model.js` | The rhino model on its own, with every rule and number in one place. |

### Planned split

The rhino is currently one self-contained file. Once a second animal exists, split the structure and the look into two files so characters can share them:

- `case-engine.js`: the shared structure. Inputs, weekly simulation loop, hidden meters, tests, scoring and findings. Each character supplies a config with its inputs, modifiers, obstacle ladder and symptom rules.
- `case-design.css` plus a per-character art module: tokens, layout, charts and each animal's drawing.

## Adding a new animal

1. Name the quality and the single best-fit training pattern.
2. List the inputs the student controls.
3. Define the modifiers that reward the best-fit pattern and penalize the others.
4. Define the obstacle ladder and its thresholds.
5. Choose the signature obstacle, its hidden trigger, and its visible symptom.
6. Draw the animal with one or two parts that change as the quality improves.
7. Test the model against five plans before building the page: ideal, the same plan three times, the wrong quality, an overreach, and a big jump in load.

## Tuning the rhino

Every number lives in `src/model.js`. The thresholds most worth adjusting after testing with students:

- `obstacles[].need`: how hard each rung is. The ideal plan currently just reaches the giant boulder.
- `G0`: the best-case weekly gain.
- The tissue-stress thresholds in `stepWeek`: how forgiving the rhino is about jumps in workload.
- The novelty rate (0.085 per repeated week): how quickly an unchanged plan plateaus.

Reference outcomes from the sample strategies used to tune the model:

| Strategy | Highest obstacle | Notes |
| --- | --- | --- |
| Heavy, low reps, ramped load, good recovery | Giant boulder | Score about 100 |
| Same, but slower ramp | Fallen tree | |
| Default plan repeated | Boulder | Gains fade each block |
| Heavy plan repeated unchanged | Fallen tree | Clear plateau |
| High reps, short rest | Small rock | Close to the boulder, but not there |
| Big jump in volume with poor sleep | Boulder | Two injuries |
| Extreme volume, low fuel, poor sleep | Nothing cleared | Injured repeatedly |

## Open questions

- Should the sled dog use longer blocks?
- How does the sandbox connect to graded work, if at all? This is still undecided.
- Should an instructor view show the hidden meters live, for lecture demos?
