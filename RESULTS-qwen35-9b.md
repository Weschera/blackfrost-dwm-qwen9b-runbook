# DWM on Qwen3.5-9B — overnight results (Oct 10-11, 2026)

Autonomous overnight run: base pre-DWM eval → DWM apply → post eval → adaptive alpha re-run, single DGX Spark, no human in the loop. 3 alpha settings, 80-scene think-OFF bench (v7), grader c572d0b, 68 scenes paired per comparison.

## Dose-response (mean delta vs pre-DWM, thinkOFF)
| alpha | mean delta | verdict |
|---|---|---|
| 1.0 | -0.0193 | flat (noise) |
| 1.4 | -0.0735 | worse |
| 0.6 | gate-fail x2 | no candidate |

a1.4 per-scene: large swings both directions (RR-02/RO-04 0->1.0; SA-03/RO-03 1.0->0.0) — signal exists but is outweighed at full dose.

## a0.6 gate failure (reproducible 2/2)
STAGE-5 ctrl-04 (structured JSON, thinking-OFF): edited model emits "Thinking Process: ..." narration instead of pure JSON. Deterministic behavior drift, not noise. Pipeline halts by design.

## Fixes landed on main along the way
- d7309e4 device-placement in project_matrix / alpha_zero_identity (CPU dirs x cuda weights)
- e4c9752 bit-exact passthrough for non-target tensors in STAGE-7 (dtype homogenization)

## Verdict
NO-WIN at benchmark level (threshold +0.02): full-dose DWM is neutral-to-negative on Qwen3.5-9B for this bench; error grows with alpha. Next lever: per-layer / target-subset dosing rather than global alpha.

## Artifacts
- Eval reports: results/runs/*preDWM* / *postDWM* (1x Spark, thinkOFF, repeats=2)
- Failure evidence: workdir/round2-controls.json, FAILURE.json
