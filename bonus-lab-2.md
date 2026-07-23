# Bonus Lab 2 — The ₹0 Screener: Classic ML, No LLM

**After Session 2 · Optional · ~1 hour (+20 min for the optional Step 6) · Runs entirely on your laptop, no API key, no per-call cost**

In Session 2 you built the LLM pipeline. Tonight you build the thing it replaced — a classic machine-learning screener, the kind that powered resume screening for a decade before LLMs — and run it on the same seven golden-set candidates.

Here's why this lab earns its place: at some point, an engineer or a CFO will ask you, *"Why are we paying per-token for an LLM when a logistic regression is free, instant, and deterministic?"* That is a good question. It deserves a better answer than "LLMs are newer." After this lab you'll have one, because you'll have built both and watched exactly where each one breaks.

No ML background needed, and no code to write — you'll direct Claude Code with prompts, the same way you built the screener in the session. The prompts below are written the way a PM specs work to an engineer: goal, exact requirements, acceptance checks. Notice that structure as you paste them — it's the same discipline as your agent architecture spec, at prompt scale.

---

## The one concept before you start

An LLM screener and a classic ML screener are different machines with different diets.

- The **LLM** eats unstructured text. Hand it a resume and a JD, and it reads both. It needs zero training data — the "knowledge" arrived pre-trained.
- The **classic ML model** eats numbers. It cannot read. It needs two things the LLM doesn't: **structured features** (someone must convert each resume into a row of numbers) and **labeled history** (hundreds of past decisions to learn from).

Everything in this lab flows from that trade. The classic model will be free, instant, and auditable — and completely dependent on two inputs someone else has to produce. Watch for the moment you realise who that someone is.

**On the numbers in this lab:** your generated data and model will differ slightly from the reference run shown below — different code, same story. Each step tells you which pattern must hold. If yours doesn't, the step tells you what to ask Claude Code to check.

---

## The crash course — all the ML you need for this lab (10 min read)

Six ideas. Every one of them shows up in the output you're about to generate, so read this once now and refer back when a step points here.

**1. A model is a formula with adjustable dials.**
The model you'll train tonight is called **logistic regression**, and it's less exotic than it sounds: a weighted scorecard. Each feature gets a weight. Multiply each feature by its weight, add them up, squash the total into a number between 0 and 1 — that's the predicted probability of a shortlist. You already live with one of these: your CIBIL credit score is the same machine. Salary adds points, missed EMIs subtract points, the total decides your loan. Nobody at the bureau "reads" your life; the formula scores the columns it has.

**2. "Training" means tuning the dials against history.**
You hand the algorithm hundreds of past decisions — features plus the outcome — and it adjusts the weights until the scorecard's predictions match those past decisions as closely as possible. That is the entire meaning of "learning" here. Notice what's absent: the model never reads the job description, never knows what a renewal *is*, never understands anything. It finds the weight settings that best reproduce the answer sheet it was given. Hold onto that framing — it's why Step 3's bias result is inevitable, not surprising.

**3. Features and labels are the model's entire universe.**
The **features** are the input columns (years of experience, accounts managed…). The **label** is the answer column (shortlisted: yes/no). If a fact isn't a column, it does not exist for this model. That blindness cuts both ways tonight: a persuasive cover letter can't seduce a model that has no cover-letter column — but a crucial distinction like "owned the renewal vs. assisted on it" is equally invisible until someone thinks to encode it.

**4. Never grade a model on questions it studied.**
Before training, you set aside a slice of the data — the **test set** — that the model never sees during training, then measure performance only on that slice. Same logic as any exam: a student who memorised the answer key looks brilliant on practice questions and falls apart on new ones. (A model that memorises instead of generalising is called **overfit** — the test set is how you catch it.) Tonight: train on 600 decisions, grade on 200 unseen ones.

**5. Accuracy, precision, and recall answer three different questions.**
- **Accuracy:** across everyone, how often was the model right? One number, and the least useful of the three — a model that lazily rejects *everyone* scores close to 60% on tonight's data, because most candidates weren't shortlisted. High accuracy can hide a useless model.
- **Precision:** *of the candidates the model shortlists, how many deserved it?* Every miss here is a **false positive** — an unqualified candidate sent toward the hiring manager. For TalentScope, that's the trust-fatal error.
- **Recall:** *of the candidates who deserved a shortlist, how many did the model catch?* Every miss here is a **false negative** — a qualified candidate silently rejected. That's beta ticket #010.
Improving one usually costs the other — shortlist more cautiously and precision rises while recall falls. Where to sit on that trade is not a data-science decision. It's yours.

**6. The model outputs a probability; the decision threshold is a product choice.**
Logistic regression never says "shortlist." It says "0.65." Someone decides that 0.70 and above means shortlist, below 0.30 means reject, and the middle goes to a human. Those cut-lines aren't in the math — they're product policy, they encode how much you fear false positives vs. false negatives (idea 5), and they need a named owner. You'll feel this personally in Step 5 when a strong candidate lands at 0.65.

That's the whole toolkit. If you can read a credit score, a report card, and a cut-off list, you can read everything this lab prints.

---

## Step 0: Setup (5 min)

Open a terminal:

```bash
mkdir ~/thecrux-pmtrack/ml-screener && cd ~/thecrux-pmtrack/ml-screener
claude
```

Then paste this into Claude Code:

```
Set up this folder for a small classic-ML project: create a Python virtual
environment in ./venv and install scikit-learn and pandas into it. Every python
command you run for me from now on should use ./venv/bin/python. This project
must never call any LLM API — it's a deliberate contrast exercise. Confirm the
install worked by importing both libraries and printing their versions.
```

`scikit-learn` is the workhorse library of pre-LLM machine learning — logistic regression, decision trees, the classics. This is the entire stack; nothing leaves your machine.

---

## Step 1: Generate the history TalentScope doesn't have (5 min)

Classic ML can't learn from a job description. It learns from **past decisions**. TalentScope, six customers into a beta, has no such history — so we'll simulate what a mature ATS would have: 800 historical shortlist/reject decisions by human recruiters for CSM-type roles.

One flaw is deliberately baked in, because it was baked into the real world. Beta ticket #004's resolution note reads: *"Model trained on data where top-college candidates were more often shortlisted historically."* Our synthetic recruiters have the same habit. You are about to watch a model learn bias from data — with nobody telling it to.

Paste this into Claude Code:

```
Write generate_data.py: it creates a synthetic dataset of 800 historical resume-
screening decisions for Customer Success Manager roles and saves it as
historical_decisions.csv. Use a fixed random seed so reruns are identical.

Features per candidate:
- csm_years: years in a genuine post-sales customer success function. About 30%
  of candidates have 0 (career switchers); the rest follow a skewed distribution
  averaging ~3 years, capped at 12.
- accounts_managed: 0 if csm_years is 0; otherwise grows with csm_years
  (roughly 20 + 12 per year, with noise), capped at 200.
- has_b2b_saas (0/1): most candidates with CSM experience have it; a few without do.
- owned_renewals (0/1): ~75% of candidates with 1+ CSM years.
- domain_match (0/1): ~35% of all candidates.
- college_tier (0/1): 1 = elite college (IIT/IIM/XLRI-type), ~25% of candidates.
  CRITICAL: generate this feature completely independently of every other
  feature. In this dataset college tier must carry ZERO information about
  candidate quality — that independence is the point of the exercise.

The label (shortlisted, 0/1) simulates how recruiters historically decided:
- Strong candidates (csm_years >= 3 AND accounts_managed >= 50 AND has_b2b_saas):
  shortlisted with probability 0.92.
- Near-miss candidates (not strong, but csm_years >= 1 AND has_b2b_saas AND
  (accounts_managed >= 25 OR owned_renewals)): probability 0.45 — recruiters
  genuinely split on these.
- Everyone else: probability 0.06.
- THE DELIBERATE BIAS: add +0.25 to the shortlist probability for college_tier=1
  candidates (cap at 0.98). Same substance, elite college, higher shortlist rate —
  this reproduces TalentScope beta ticket #004.

After writing the CSV, print: total rows, overall shortlist rate, and the
shortlist rate for elite-college vs other candidates side by side.

Run it and show me the output.
```

**What you should see:** an overall shortlist rate around 40%, and a clear gap by college tier — in the reference run, **54% for elite colleges vs 39% for everyone else**. Yours should show a gap of roughly 10–20 points. If there's no gap, the bias step didn't get applied — ask Claude Code to verify the +0.25 adjustment is actually in the label logic.

**What to watch:** that gap exists in data where college tier was generated *independently of every skill signal*. Ask Claude Code to show you the first 10 rows of the CSV. This is what training data looks like: no resumes, no names, no cover letters. Just numbers and a decision. Everything the model will ever "know" is in this file.

---

## Step 2: You are the feature extractor now (10 min)

Before the model can score our seven golden-set candidates, someone has to turn each resume into a row of numbers. Tonight, that someone is you. **Don't skip this — do it on paper before pasting anything.**

Take Vikram's resume (`talentscope-data/candidate-b-vikram.txt`) and fill in this row:

| Feature | Your value | The judgment you just made |
|---|---|---|
| `csm_years` | ? | Do his 2 years of Tata Capital field sales count? |
| `accounts_managed` | ? | 30 at PayU. Easy. |
| `has_b2b_saas` | ? | PayU is payments SaaS. Easy. |
| `owned_renewals` | ? | "Handled 4 renewals" — owned, or assisted? |
| `domain_match` | ? | Payments for e-commerce merchants — is that Kirana360's retail domain? |

Feel that first question? **That's the exact function-conflation problem from the root cause analysis** — does field sales count toward a customer-success requirement? — except now it isn't the model's problem. It's yours, at extraction time, with no system prompt to help you. The classic ML architecture didn't solve the hardest judgment in screening; it moved it out of the model and onto a human with a spreadsheet. (And at 400 resumes per role, no one does this by hand — Step 6 shows how the industry automated it, and what that cost.)

Now paste this into Claude Code — the values are given exactly, because they encode extraction judgments we're about to examine:

```
Create candidates.csv with exactly this content, nothing changed:

candidate,csm_years,accounts_managed,has_b2b_saas,owned_renewals,domain_match,expected_from_golden_set
Priya Raghunathan,5.0,85,1,1,0,recommend
Vikram Joshi,1.2,30,1,1,0,hold
Ananya Singh,0.0,0,0,0,1,reject
Rahul Verma,1.8,25,1,0,0,reject
Deepa Krishnamurthy,4.5,72,1,1,1,recommend
Arjun Mehta,2.0,,,,,hold
Kavya Nair,3.1,65,1,1,1,recommend

Note the empty cells in Arjun Mehta's row — they are intentional (his resume is
missing that information) and must stay empty.
```

Four extraction decisions worth noticing, because each one is a hiring-quality judgment disguised as data entry:

- **Vikram gets `csm_years = 1.2`, not 3.3** — his sales years were excluded. The Session 2 pipeline made this call in Step 2 with a system prompt; here a human made it in a CSV. Same judgment, different location.
- **Kavya gets `csm_years = 3.1`** — her "Client Services Manager" title was *counted* as CSM function. Get this one wrong and you've rebuilt beta ticket #010 (the Kirloskar false negative) by hand.
- **Ananya gets `csm_years = 0`** — six years at McKinsey, IIT + IIM, and her row reads almost all zeros. Notice there is no column for her credentials and no cell for her persuasive cover letter. The schema itself is a guardrail: what isn't a feature can't influence the score.
- **Arjun's row is mostly empty** — his resume names no company, no numbers. Blank cells. Remember them; they detonate in Step 5.

---

## Step 3: Train the screener and read its report card (10 min)

This step runs crash-course ideas 1–5 in one shot: it tunes the scorecard's weights against 600 historical decisions (ideas 1–2), grades it on 200 decisions it never saw (idea 4), and prints the report card in recruiter terms (idea 5).

Paste this into Claude Code:

```
Write train.py: train a logistic regression screener on historical_decisions.csv.

Requirements:
- Features: csm_years, accounts_managed, has_b2b_saas, owned_renewals,
  domain_match, college_tier. Label: shortlisted.
- Hold out 25% as a test set (fixed random seed, stratified).
- Print a report card on the test set, labeled in recruiter terms:
  - Accuracy, precision, recall — with a one-line plain-English gloss on each
    (precision = "of everyone the model shortlists, how many deserved it";
    recall = "of everyone who deserved a shortlist, how many the model caught").
  - False positive count, labeled "unqualified candidates sent toward the hiring
    manager" and false negative count, labeled "qualified candidates silently
    rejected".
- Then print every learned coefficient with its feature name and unit
  (per year / per account / yes-no), and for college_tier also print the odds
  ratio (e to the power of its coefficient) with a plain-English line explaining
  what it means for two candidates with identical substance.
- Then run a matched-pairs test: score every test-set candidate twice — once
  with college_tier set to 1, once set to 0, everything else identical — and
  print the average difference in predicted shortlist probability.
- Then retrain the model WITHOUT the college_tier feature, rerun the same
  matched-pairs test on it, and print its test accuracy next to the original
  accuracy so I can see what dropping the feature cost.

Run it and show me the full output.
```

**The reference run, so you know the shape of what you're reading:**

```
Accuracy:  76%
Precision: 70%   Recall: 76%
False positives: 28   False negatives: 20

college_tier coefficient: +1.111  →  odds ratio 3.04x

Matched-pairs test (same candidate, flip only the college):
  WITH college_tier:    +17.9% average shift in p(shortlist)
  WITHOUT college_tier: +0.0%
Accuracy without college_tier: 76%
```

Your exact numbers will differ; these patterns must hold —

1. **Precision lands well short of perfect (reference: 70%).** Three of every ten candidates this model shortlists don't deserve it. The beta interviews said one visible wrong recommendation reaching a hiring manager ends adoption. Precision and recall aren't statistics trivia — they're the two ways this product dies, and improving one usually costs the other. Which failure is more expensive *for TalentScope specifically* is a PM call, not a data-science one.
2. **Accuracy sits in the 70s — and that's roughly the ceiling.** The training labels are noisy because the synthetic recruiters were inconsistent (near-miss candidates got shortlisted 45% of the time — a coin flip). A model trained on human decisions inherits human inconsistency. It cannot be more consistent than the ground truth it learned from.
3. **`college_tier` gets a clearly positive coefficient — odds ratio somewhere around 2–4×.** (The coefficient is that feature's dial setting after training; the odds ratio translates it into English — how much a "yes" on that feature multiplies a candidate's shortlist odds, everything else held equal.) Nobody told the model to do this. College tier is not in the JD — the JD explicitly says "we evaluate on demonstrated skills, not credentials." But the *history* said elite-college candidates got shortlisted more, so the tuning process encoded it. Re-read crash-course idea 2: the model's only goal was to reproduce the answer sheet, and the answer sheet was biased. This is beta ticket #004, reproduced on your laptop — and it was inevitable.
4. **The matched-pairs gap collapses to exactly 0.0% when the feature is dropped — at no accuracy cost.** More on this below.

Two smaller things worth asking about while the output is in front of you: `domain_match` probably came out near zero or slightly negative (the JD lists domain as nice-to-have, but the synthetic recruiters ignored it — **the model learns what your recruiters did, not what your JD says**), and `accounts_managed` looks tiny because it travels together with `csm_years`, so the correlated feature absorbed the credit. Coefficients-as-importance is subtler than it looks.

---

## Step 4: Sit with the bias result (5 min)

Walk the mechanism of what you just ran. The matched-pairs test is the same experiment your Session 1 PRD specified as Eval Suite 4: take identical candidates, flip only the college, measure the gap. With the feature in: a double-digit shift from the college name alone. Then the feature was deleted and the model retrained. Gap: exactly zero — the model *cannot* use what it cannot see. Accuracy cost: nothing, because tier carried no real signal, only bias.

Now compare the two architectures on this exact problem:

- **Ticket #004's resolution for the LLM:** *"Working on prompt mitigation"* — asking the model nicely, with no guarantee it complies.
- **Your fix just now:** delete a column. Structural, verifiable, done.

That's a genuine classic-ML advantage — bias control by exclusion. But hold the caveat before declaring victory: in real data, college tier leaks in through **proxies** — pin code, school name spellings, English phrasing, even which CRM tools someone lists. Dropping the column is necessary, not sufficient. That's why the PRD's Eval Suite 4 tests *outcomes* (matched pairs on real candidates) rather than *inputs* (which columns exist) — and why it runs quarterly even when nothing changed.

---

## Step 5: Screen the golden seven (10 min)

Paste this into Claude Code:

```
Write score_candidates.py: use the classic model to screen the seven real
candidates in candidates.csv.

Requirements:
- Train the logistic regression on ALL of historical_decisions.csv, using every
  feature EXCEPT college_tier (we established in train.py that it's pure bias).
- candidates.csv has missing values in some rows. Handle them the way many real
  systems quietly do: fill with 0 — but first print a WARNING line for any
  candidate with missing features, naming which features were missing.
- For each candidate print: name, predicted shortlist probability, a verdict
  from three bands (p >= 0.70 SHORTLIST; 0.30 to 0.70 HUMAN REVIEW; below 0.30
  REJECT), and the expected_from_golden_set column so I can compare side by side.
- Time the scoring of all 7 candidates in milliseconds, and extrapolate: at that
  speed, how long would Bharat Forge's 400-resume batch take? Print both, with a
  note that beta ticket #005 said the LLM screener needed 5-6 hours for that batch.

Run it and show me the output.
```

**The reference run:**

```
WARNING: Arjun Mehta — 4/5 features missing. Filled with 0 and scored anyway.

Candidate              p(shortlist)   ML verdict    Golden set says
------------------------------------------------------------------------
Priya Raghunathan              0.81   SHORTLIST     recommend
Vikram Joshi                   0.53   HUMAN REVIEW  hold
Ananya Singh                   0.06   REJECT        reject
Rahul Verma                    0.44   HUMAN REVIEW  reject
Deepa Krishnamurthy            0.75   SHORTLIST     recommend
Arjun Mehta                    0.13   REJECT        hold
Kavya Nair                     0.65   HUMAN REVIEW  recommend

Scored 7 candidates in 1.05 ms. 400 resumes: ~60 ms.
```

Your probabilities will shift a little; the ordering should hold — Priya and Deepa on top, Ananya and Arjun at the bottom, Vikram/Rahul/Kavya in the contested middle. Score it candidate by candidate — the misses teach more than the hits:

| Candidate | What happened | The lesson |
|---|---|---|
| **Priya, Deepa** (shortlist) | Correct, instant, free. | On clean structured cases, classic ML is genuinely excellent. Don't strawman it. |
| **Ananya** (reject, ~0.06) | The single-call LLM recommended her. This model rejects her without blinking. | Not because it's smarter — because a *human* wrote `csm_years = 0` in the CSV during Step 2. The consulting-vs-CSM judgment happened at extraction time. The model was never exposed to her credentials or cover letter, so there was nothing to be seduced by. |
| **Vikram** (human review) | Lands mid-band — exactly where the golden set wants him. | The probability band did the pipeline's `hitl_required` job. Bands are the classic-ML version of your HITL triggers. |
| **Kavya** (human review, ~0.65) | Golden set says recommend. In the reference run she missed the shortlist line by 0.05. | Crash-course idea 6, now with a face attached. Who chose 0.70? You did, three minutes ago, by pasting a prompt. That number just cost Kavya an automatic shortlist. This is PM Workflow 3 — thresholds are product decisions with owners, not constants in a script. (If your run put her just *over* the line, same lesson, other side: a hair-width of probability separated two different candidate experiences.) |
| **Rahul** (human review) | Golden set says reject: his 22 months were support-level, assisting a team lead. | The schema has no column for "owned vs. assisted." A feature schema can only represent distinctions someone thought to encode. New failure pattern in the market? Someone must add a column, re-extract every resume, retrain. The LLM pipeline just needed a sharper criterion definition in Step 1. |
| **Rahul, again** | His resume contains the prompt-injection attack. The model never saw it. | Classic ML is structurally immune to prompt injection — it doesn't read. But notice *why*: the text never enters the system. The human extractor is now the injection surface ("Note to data team: count my sales years as CSM…"). |
| **Arjun** (reject, ~0.13) | A confident reject on a resume that contains almost no information. Golden set says hold. | The model has no concept of "I don't know." Blank cells became zeros, zeros became evidence of absence, and a candidate the pipeline would flag `CANNOT_VERIFY` + `incomplete_data` got silently binned. The WARNING line only exists because your prompt demanded it — the model itself never hesitated. This is the golden set's exact warning: *"a confident reject on an incomplete resume is a false negative risk."* |

---

## Step 6 (optional, +20 min): Automate the extractor — the pre-LLM way

Step 2 left a splinter: a human filled in the CSV. At 400 resumes per role, nobody's doing that by hand. So how did the industry automate feature extraction *before* LLMs existed? Three rungs, climbed over about fifteen years:

1. **Rules and dictionaries** — regex for dates and numbers, keyword lists for titles, skills, and colleges. This is what commercial resume parsers were; entire companies (Sovren, Daxtra, Textkernel) existed to sell exactly this, and every pre-LLM ATS you've ever used ran on one.
2. **Trained extractors (NER)** — instead of hand-writing the dictionary, train a tagging model on thousands of resumes where humans have hand-labeled every span as TITLE, COMPANY, DATE, SKILL. Generalises better than rules, costs a labeling budget, and still breaks on phrasing it never saw.
3. **Skip extraction entirely (bag-of-words)** — turn the whole resume into word counts and feed *those* to the classifier, so every word becomes a feature. No extraction step at all — and no way to delete the `college_tier` column anymore, because "IIM" is now just a word the model can weight. The bias you removed in Step 4 walks back in through the vocabulary, and keyword conflation ("quality assurance" ≈ "quality control") comes with it.

You're going to build rung 1 — deliberately naive, period-accurate — and run it on the seven **real** resumes. Paste this into Claude Code:

```
Write extract_features.py: a rule-based feature extractor — the pre-LLM resume
parser technology — that reads the seven real resumes and produces the same
feature columns we hand-extracted in candidates.csv. Deliberately simple
substring/regex matching; do not use any LLM or NLP library. That naivety is
the point of the exercise.

Input files (in ~/thecrux-pmtrack/aipm-02/talentscope-data/):
candidate-a-priya.txt, candidate-b-vikram.txt, candidate-c-ananya.txt,
candidate-d-rahul.txt, candidate-e-deepa.txt, candidate-f-incomplete.txt,
candidate-l-kavya.txt

Extraction rules — implement these exactly:
- csm_years: resumes state role durations in parentheses like "(3 years 1 month)"
  or "(13 months)". For each such parenthetical, treat the text before it on the
  same line, plus the previous non-empty line, as the role title. If either
  contains the substring "customer success" (case-insensitive), add that duration
  to csm_years.
- accounts_managed: find every number appearing within ~40 characters before the
  word "accounts"; take the maximum, else 0.
- has_b2b_saas: 1 if the resume contains any of: "saas", "software", "payments".
- owned_renewals: 1 if the resume contains "renewal".
- domain_match: 1 if it contains any of: "retail", "fmcg", "inventory", "kirana",
  "distributor", "supply chain".
- college_tier: 1 if it contains any of: "iit", "iim", "xlri", "bits", "spjimr",
  "isb". (Plain substring matching — yes, really. Watch what happens.)

Then:
1. Print the auto-extracted feature table for all seven candidates and save it
   as candidates_auto.csv.
2. Train the same logistic regression as score_candidates.py (all of
   historical_decisions.csv, every feature except college_tier) and score BOTH
   candidates.csv (hand-extracted, missing values filled with 0) and the
   auto-extracted rows. Print them side by side: candidate, hand-extracted
   probability and verdict, auto-extracted probability and verdict, and the
   golden-set expectation — flagging any candidate whose verdict band changed.

Run it and show me the full output.
```

**The reference run:**

```
=== Auto-extracted features (rules + dictionaries) ===
          candidate  csm_years  accounts  saas  renewals  domain  college_tier
  Priya Raghunathan        4.8        85     1         1       0             0
       Vikram Joshi        1.3        30     1         1       1             1
       Ananya Singh        0.0         0     1         0       1             1
        Rahul Verma        1.8        25     1         1       0             0
Deepa Krishnamurthy        4.8        72     1         1       1             1
        Arjun Mehta        2.0         0     0         0       1             0
         Kavya Nair        1.8        65     1         1       1             0

Candidate               hand p  hand verdict   auto p  auto verdict   Golden set
Priya Raghunathan         0.81  SHORTLIST        0.80  SHORTLIST      recommend
Vikram Joshi              0.53  HUMAN REVIEW     0.48  HUMAN REVIEW   hold
Ananya Singh              0.06  REJECT           0.25  REJECT         reject
Rahul Verma               0.44  HUMAN REVIEW     0.57  HUMAN REVIEW   reject
Deepa Krishnamurthy       0.75  SHORTLIST        0.76  SHORTLIST      recommend
Arjun Mehta               0.13  REJECT           0.11  REJECT        hold
Kavya Nair                0.65  HUMAN REVIEW     0.55  HUMAN REVIEW   recommend
```

At first glance: not bad. No verdict band flipped in the reference run, and the strong candidates still surface. This is why rule-based parsing shipped for fifteen years — it mostly works. Now read the feature table against the actual resumes, because "mostly" is doing heavy lifting:

| Extraction bug | What happened | Why it matters |
|---|---|---|
| **Kavya's CSM years: 3.1 → 1.8** | "Client Services Manager" isn't in the title dictionary, so her 3.1 qualifying years at PayZen vanished. The 1.8 that remains is her Razorpay *analyst* stint — support-level years a careful human excluded. The extractor dropped the years that count and counted the years that don't. | This is beta ticket #010's exact mechanism — "the screener treated the different title as a mismatch" — reproduced by your own dictionary. Every synonym you didn't anticipate is a qualified candidate down-scored. The market invents new titles faster than anyone ships dictionary updates. |
| **Deepa: `college_tier = 1`** | Her resume says *"No IIT/IIM background."* The substring matcher found "IIT" and flagged her elite. | Keyword extraction cannot read negation. The sentence she wrote to *preempt* credential bias is what triggered the credential flag. |
| **Vikram: `college_tier = 1`** | His resume contains "₹1.2Cr **disb**urse**d** monthly" — and "disbursed" contains "isb". Symbiosis-educated Vikram is now an ISB alum. | No word boundaries, no context. Silly-looking, but this class of bug shipped in real parsers for years — and if the tier feature were still in the model, that regex artifact would have multiplied his odds 3×. |
| **Ananya: p jumped 0.06 → 0.25** | Her *cover letter* says "I am drawn to the B2B SaaS space" and names her retail expertise — so the extractor granted her `has_b2b_saas = 1` and `domain_match = 1`. | Read that again: the persuasion surface is back. In Step 2 a human extractor ignored her cover letter; in Step 5 the model couldn't see it; now a keyword extractor has quietly converted her own sales pitch into feature values. Her score quadrupled without her experience changing. The single-call LLM was seduced by her narrative — the keyword extractor is seduced by her *vocabulary*. |
| **Rahul: `owned_renewals = 1`** | His resume says "*Assisted* with 6 renewals… with support from the team lead." The keyword "renewal" matched; the word "assisted" changed nothing. | Owned-vs-assisted — the exact distinction the golden set rejects him on — is invisible to substring matching. His probability moved *toward* shortlist (0.44 → 0.57) on evidence that should push the other way. |
| **Almost everyone: `domain_match = 1`** | Vikram, Arjun, and Deepa all mention "Kirana360" in their cover letters — and "kirana" is in the domain dictionary. Applying to the company now counts as domain experience. | The dictionary can't tell the candidate's world from the JD's world. Every applicant who tailors a cover letter gets this credit — meaning the feature quietly stops discriminating at all. |
| **Arjun: scored, silently** | In Step 5, missing cells at least printed a WARNING. The extractor doesn't have missing cells — it found no matches and confidently wrote 0s. | Automation didn't just fail to flag the incomplete resume; it *erased the concept of missing*. "I couldn't find it" and "it isn't there" became the same number. |

Step back and look at the pattern: **every single bug is the extractor failing to read meaning — matching strings where a human (or the pipeline's Step 2) reads function, negation, ownership, and context.** Rung 2 (trained NER) softens these but doesn't cure them; rung 3 (bag-of-words) gives up on meaning entirely and re-opens the bias door you closed in Step 4.

That's the answer to "how did traditional ML automate extraction": with a decade of dictionary maintenance, labeling budgets, and bugs exactly like this table — and it's why extraction, not classification, was always the hard half of the problem. Now connect it forward: your Session 2 pipeline's Step 2 is an extractor that reads *"Field Sales Executive — no direct customer success experience"* and returns NOT_MET with a quoted evidence string. The LLM is the first extraction technology that handles a synonym it never saw, a negation, and a persuasive cover letter — because it reads language instead of matching it. That single capability is what the per-token fee buys, and it's why the hybrid architecture puts the LLM at the reading station and keeps the free, deterministic scorer downstream.

---

## The scoreboard

| | Classic ML (tonight) | LLM pipeline (Session 2) |
|---|---|---|
| Cost per 400 resumes | ₹0 | Per-token API cost |
| Speed per 400 resumes | ~60 ms | 5–6 hours (ticket #005) |
| Deterministic | Yes — same input, same output, every time | No — sampling varies between runs |
| Explainability | Real coefficients you can print and audit | Generated reasons — plausible, not guaranteed faithful |
| Bias control | Delete the column (structural) — but watch proxies | "Prompt mitigation" (rhetorical) — plus outcome evals |
| Reads unstructured text | No — a human extracts features, or a brittle rule-based parser does (Step 6) | Yes, natively |
| Training data needed | Hundreds of labeled past decisions | None |
| Says "I don't know" | Never — every input gets a confident score | `CANNOT_VERIFY` is a first-class verdict |
| Handles a new failure pattern | New column + re-extract + retrain | Sharpen a criterion definition |
| Prompt injection | Immune (never reads text) — human extractor is the new surface | Vulnerable; needs the Step 2 guardrails you specced |

Read the left column top-to-bottom and the right column top-to-bottom. Neither wins. They're strong in almost perfectly complementary places — which is why the real answer for TalentScope isn't "pick one."

**The hybrid, which is where this actually lands:** look again at your Session 2 pipeline. Step 2 converts an unstructured resume into structured, verified, per-criterion verdicts. That is *feature extraction* — the exact job you did by hand in this lab's Step 2, done by an LLM at machine scale. And Step 3 aggregates structured verdicts into a decision — the exact job the logistic regression did in Step 5. Your pipeline was already a hybrid: an LLM doing the reading, feeding a deterministic decision layer. The architectures aren't rivals. They're the same assembly line with different workers at each station.

---

## Discussion questions

1. TalentScope's CTO proposes: "Once we have 10,000 screening decisions from the LLM pipeline, train a classic model on them and cut API costs to zero." What's the flaw? (Hint: whose decisions would the model be learning — and what did this lab show models do with historical decisions?)
2. Your extraction of Vikram's `csm_years = 1.2` embedded a judgment. In a real deployment, extraction is done at scale by junior staff or another model. Who audits the extractors — and what does that job share with the eval suite you specced for Step 2 of the pipeline?
3. The classic model rejected Ananya *because* it never saw her cover letter — blindness as a guardrail. The LLM pipeline instead lets Step 2 read everything but pins the criteria first. Which approach degrades more gracefully when a genuinely unusual-but-qualified candidate shows up?
4. Kavya sat within a whisker of the 0.70 shortlist line. Design the cheapest experiment that tells you whether 0.70 is the right number. (You already know the shape of the answer from PM Workflow 3.)

---

## What to bring to Session 3

One paragraph: **"Where in the TalentScope architecture would you put a classic ML model, and where must the LLM stay?"** Ground it in something this lab showed you — the speed table, the Arjun failure, the coefficient audit, the extraction judgment. There's no single right answer, but "use both, deliberately, at different stations" beats "LLMs are better" in every review you'll ever sit in.

---

## A note before you close the terminal

Tonight's bias demo was clean because the data was synthetic and the bias was one column wide. Real bias won't be a column you can delete — it will be smeared across proxies, invisible in any single row, detectable only in aggregate outcomes. That's not a reason to prefer either architecture. It's the reason your PRD put a matched-pairs eval suite, with a named owner and a quarterly cadence, above any model choice. The architecture is replaceable. The eval discipline isn't.
