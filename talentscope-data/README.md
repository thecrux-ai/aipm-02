# TalentScope Case-Study Data

Test data for Session 1 and Build Lab 1 — no need to write your own. More data files land here with later sessions.

## Files in this directory

| File | What it is |
|---|---|
| `user-research-transcripts.md` | Three user-research interviews with HR managers (Session 1 — research synthesis) |
| `beta-feedback.md` | Beta support tickets + NPS verbatims (Session 1 — beta analysis; also a PM Skills/PM Brain input) |
| `job-description-csm.txt` | Full job description for the Customer Success Manager role |
| `candidate-a-priya.txt` | Resume — Priya Raghunathan (expected: **recommend**) |
| `candidate-b-vikram.txt` | Resume — Vikram Joshi (expected: **hold**) |
| `candidate-c-ananya.txt` | Resume — Ananya Singh (expected: **reject**) |
| `candidate-d-rahul.txt` | Resume — Rahul Verma (expected: **reject** — contains prompt injection attempt) |
| `candidate-e-deepa.txt` | Resume — Deepa Krishnamurthy (fairness eval: strong candidate, state college background) |
| `candidate-f-incomplete.txt` | Resume — partial/incomplete submission (edge case: low data quality) |
| `expected-outputs.json` | Golden set: correct screening outputs for Candidates A–F (use as eval ground truth) |

## How to use

**Session 1 — Working Session**
Use `user-research-transcripts.md` for the research-synthesis workflow and `beta-feedback.md` for the beta-analysis workflow. Your generated outputs (opportunity memo, product brief, PRD section) go in your `my_work/` folder, not here.

**Build Lab 1 — Resume Screener**
Feed `job-description-csm.txt` + any candidate resume into your screener. Check the output against `expected-outputs.json`. All six candidates cover different decision paths.
