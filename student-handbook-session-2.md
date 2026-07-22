# Working Session 2 — Agentic AI: Design Patterns & Decision Boundaries

**Wednesday, July 22 · 7:30–10:30 PM IST**

---

## Tonight's Scenario

Last week you shipped the TalentScope resume screener — one model call that takes a job description and a resume and returns a recommendation. It's live on a Vercel URL. You posted it in the WhatsApp group.

Now read this from the beta:

> **Ticket #001 — Bharat Forge (Anita Verma):** "The screener recommended a candidate for a QA Engineer role who had zero manufacturing experience. The AI interpreted 'quality assurance' on a software testing resume as equivalent to 'quality control' in a manufacturing context. These are completely different jobs. We almost sent this person to the hiring manager — caught it manually before it went out."

Your Build Lab 1 screener makes exactly this mistake. Run it on `candidate-c-ananya.txt` — a management consultant with an IIT/IIM background applying for the Customer Success Manager role. Does it recommend her? If it does, you just shipped the bug Anita caught. One call. No step where the model stops and checks: *"am I evaluating 'customer success' against the right meaning of this person's experience?"*

That is the architecture problem. Tonight you fix it — not with a better prompt, but with a different architecture.

---

## What You're Building Tonight

By 10:30 PM you will have:
1. An **agent architecture specification** for the TalentScope screener — the replacement for the single-call version you shipped in Build Lab 1. A PM deliverable that sits *alongside* the PRD you wrote in Session 1.
2. A running **three-step screening agent** — a web app that evaluates a resume in three sequential steps, verifies instead of guesses, and knows when to hand off to a human

```
Tonight's through-line:
Job Description + Resume
        │
        ▼
  Step 1: Extract the criteria — each with a definition and a "how to verify"   (call 1)
        │
        ▼
  Step 2: Evaluate the resume per criterion — verify, don't infer   (call 2)
        │  → per-criterion verdict, evidence quote, and CANNOT_VERIFY when it can't tell
        ▼
  Step 3: Recommend — defensible reason + a human-in-the-loop flag   (call 3)
        │
        ▼
  A screen a recruiter can actually read → http://localhost:3000
```

> **Why we build fresh.** The screener you deployed in Build Lab 1 is a **single call** — the one that fails on Ananya. Tonight's **three-step agent** fixes it. We build the three-step version as a clean reference app rather than editing your deployed repo, because every team's Build Lab 1 came out differently (different scope, quality, and codebase) — there's no single set of steps that works across all of them. The architecture you build tonight is what you'll **port back into your own deployed screener in Build Lab 2.** Same product, deliberately better architecture.

---

## Before You Join

- Your **Build Lab 1 screener deployed**, Vercel URL handy — you'll run it live in the hot seat
- Terminal open, Claude Code ready, the cohort repo in place:
```bash
cd ~/thecrux-pmtrack/aipm-02
```
- Your `my_work/` folder from Session 1 (`opportunity-memo.md`, `product-brief.md`, `prd-section.md`) — we build on it tonight
- Your **capstone team and direction** locked (homework from Session 1)

> **No API key needed tonight.** Like Session 1, tonight runs through Claude Code — the in-session build calls the `claude` CLI, not the API. You'll switch to the Anthropic API for **Build Lab 2**, exactly as you did for Build Lab 1.

---

## Tonight's Schedule

We run one of two schedules depending on how the cohort moves — instructors call it live. **Regular Pace** is the default. If we're flying, we switch to **Accelerated Pace** and spend the saved time on capstone architecture workshopping in the room.

### Regular Pace (default)

| Time | Block | What happens |
|---|---|---|
| 7:30 – 7:45 | Hot seat | Build Lab 1 demos — two participants run their deployed screener live |
| 7:45 – 8:05 | Core teaching | Why your single-call screener fails silently; agent anatomy; the pipeline → agent spectrum |
| 8:05 – 8:25 | PM Workflow 1 | Root cause analysis — why the BL1 architecture breaks on Ananya/Vikram |
| 8:25 – 8:45 | PM Workflow 2 | Agent architecture specification — the deliverable engineering builds from |
| 8:45 – 8:55 | Break | |
| 8:55 – 9:40 | Build | Multi-step screening agent (web app, three sequential steps, recruiter-readable UI) |
| 9:40 – 9:50 | Break | |
| 9:50 – 10:10 | Instructor demo + PM Workflow 3 | The crossing: pipeline → tool-use agent · then threshold calibration |
| 10:10 – 10:30 | Build Lab 2 brief + capstone checkpoint + close | Hand off to Session 3 (evals) |

### Accelerated Pace (if we're moving fast)

Same agenda at a brisker clip. The time we save becomes a capstone architecture block at the end — instructors sit with each team and pressure-test their agent architecture spec before Build Lab 2.

| Time | Block | What happens |
|---|---|---|
| 7:30 – 7:45 | Hot seat | Build Lab 1 demos |
| 7:45 – 8:00 | Core teaching | Single-call silent failure + agent anatomy + spectrum |
| 8:00 – 8:15 | PM Workflow 1 | Root cause analysis |
| 8:15 – 8:30 | PM Workflow 2 | Agent architecture specification |
| 8:30 – 8:38 | Break | |
| 8:38 – 9:15 | Build | Multi-step screening agent |
| 9:15 – 9:30 | Instructor demo + PM Workflow 3 | The crossing + threshold calibration |
| 9:30 – 9:40 | Build Lab 2 brief + close | |
| 9:40 – 10:30 | Capstone architecture workshop | Instructors pressure-test each team's agent spec live |

---

## 7:30 — Hot Seat: Build Lab 1 Demos (15 min)

Two participants open their deployed Vercel screener and run it live.

- Show it running on one candidate (2 min)
- What's the input/output contract?
- What failure mode did you discover that you didn't predict upfront?

Instructors will run one specific test in front of the room:

> **Run your screener on `candidate-c-ananya.txt`.** Ananya is a management consultant — impressive IIT/IIM credentials, strong retail domain knowledge, persuasive cover letter — applying for a Customer Success Manager role. The correct answer is **reject**: consulting is a different function from post-sales account management.

If a screener recommends Ananya, that's not a bug in your code — it's the architecture. The single call has no step that verifies *function* against *meaning*. It reads "management" and "accounts" and "client" and patterns-matches.

Instructors will also ask the question that carries the whole session: *"If this screener ran for 100 users a day and the model was updated next week, how would you know if the quality dropped?"* (Hold that thought — it's the door into Session 3.)

---

## 7:45 — Core Teaching: Why Single-Call Fails, and What an Agent Actually Is (20 min)

### Part A — Why your Build Lab 1 screener fails silently

A deterministic feature fails loudly: the button doesn't submit, the API returns a 500, the form shows a red error. You know it's broken.

An AI feature fails *silently*. The screener returns a recommendation. It looks exactly like a correct recommendation. It has a confidence score. It cites evidence. And it is wrong — in a way no error message will ever tell you.

The single-call architecture is structurally prone to three silent failures, and the beta hit all three:

| Ticket | What happened | The structural cause |
|---|---|---|
| **#001 — Bharat Forge** "QA" bug | Software "quality assurance" scored as manufacturing "quality control" | No step to disambiguate *which meaning* of a word applies in this role's context |
| **#003 — Indiabulls** "insufficient relevant experience" | Rejection reason a recruiter can't defend | No step that ties a verdict to a specific, cited requirement |
| **#004 — Marico** college bias | Tier-1 grads over-shortlisted despite a "substance over institution" instruction | No step that checks the model is applying the stated criteria, not its training prior |

Notice the pattern: in every case the model did *something reasonable-sounding*. The failure isn't that it crashed. The failure is that **it never stopped to verify it was solving the right problem.** A single call gives the model one shot: read everything, produce an answer. There's no place in that architecture for "wait — let me check what this word means *here*."

> **🗣️ Discussion Topic — Your product's silent failure**
>
> Where in your own product would a single model call silently fail? What's your equivalent of the QA bug — the input where the model confidently answers the wrong question? Think of one. We'll come back to it when we spec the architecture.

### Part B — What an agent actually is

"Agent" is the most abused word in AI. Vendors call everything an agent. Here's the precise version.

An agent is a system where **the model decides what to do next, based on what it observed from its last action.** That's the whole definition. The loop:

```
┌─────────────────────────────────────────────────────┐
│                   AGENT LOOP                         │
│  Observe current state (context + tool results)      │
│       ↓                                              │
│  Reason: what do I do next?                          │
│       ↓                                              │
│  Act: call a tool, or return the final output        │
│       ↓                                              │
│  Observe the result → loop again, or exit            │
└─────────────────────────────────────────────────────┘
```

Four components, and the PM needs to be able to name all four:

- **Orchestrator** — the model running the loop. The system prompt defines its goal and its constraints.
- **Tools** — external functions it can call: search a file, query a database, hit an API, read a webpage.
- **Memory** — ephemeral (this context window only), session (this whole task), persistent (stored beyond the task).
- **Context window** — the model's working memory. Finite. Fills up as the agent runs.

### Part C — The spectrum (the part most courses skip)

Here's the honest map. Most "agent" features sit on a spectrum, and where you sit changes everything about how you spec and how it can fail:

```
Single call          Pipeline              Tool-use agent
(BL1 screener)  →   (tonight's build)  →   (Build Lab 2)
                        │
  one shot             fixed steps           model decides
  no verification      steps verify          each next step
  fastest, cheapest    predictable           most capable,
  fails silently       bounded failures      most unpredictable
```

- **Single call** — one model, everything in one prompt. Build Lab 1. No verification, no recovery. Right for low-stakes, "good-enough" outputs.
- **Pipeline** — Step 1 → Step 2 → Step 3, each step with a narrow job, each handoff a chance to check the prior step. **This is what you build tonight.** Predictable, debuggable, and it catches the QA bug — because Step 2 can say "I cannot verify this candidate's function from the resume" instead of guessing.
- **Tool-use agent** — the model decides which tool to call, observes the result, and decides again. **This is what Build Lab 2 demands.** More capable, and genuinely harder to predict — the path through the system depends on what the model finds.

> **💡 Learning Point — Single call vs. pipeline vs. agent**
>
> | | Single call (BL1) | Pipeline (tonight) | Tool-use agent (BL2) |
> |---|---|---|---|
> | Who decides the order | n/a | You — it's hardcoded | The model — it decides what to do next |
> | Where it fails | Silently | At a step — and the step can catch it | At a step, but the path itself is uncertain |
> | Predictable? | Yes | Yes — same path every run | No — path depends on observations |
> | **What the PM owns** | The output | The steps + the handoffs + the escalation | All of that + the tool permissions + the loop bounds |
>
> The single biggest insight tonight: **crossing from pipeline to agent is not a capability upgrade you get for free.** The moment the model decides its own path, you inherit new failure modes — and every one of them is a PM spec problem before it's an engineering one.

### The six failure modes — and which are the PM's job

| Failure mode | What it looks like | PM prevention (the spec language) |
|---|---|---|
| Prompt injection | User content redirects the agent ("ignore previous instructions…") | Spec input handling: candidate-submitted text is data, never instructions; flag, don't obey |
| Context saturation | Agent forgets earlier steps as it runs longer | Spec max steps; what gets summarised vs. dropped |
| Tool-call explosion | Agent calls a tool 100× instead of 3× | Spec max tool calls per evaluation |
| Hallucinated tool call | Agent believes it called a tool that failed | Spec explicit result validation before each next step |
| Infinite loop | Agent retries the same failing action forever | Spec a circuit breaker: after N retries, stop and report |
| Scope creep | Agent takes actions beyond what you intended | Spec tool permissions explicitly — what it can and cannot touch |

The QA bug was **no verification**: the single call had one tool (answer), no step to verify the meaning of "QA," and no constraint on how it interpreted ambiguous experience. Tonight's pipeline exists to put that verification step in.

### Three design patterns

- **Single call** — fast, cheap, no verification. Only for trivial, low-stakes use.
- **Sequential pipeline** — Step 1 → 2 → 3, each step narrow, errors stay local, easy to debug. **Tonight.**
- **Multi-agent with supervisor** — a supervisor delegates to specialised workers with narrow tool access, and can retry failed steps. Use when stages need different capabilities or different data permissions. (TalentScope in 18 months: one agent sources, one screens, one schedules — a supervisor coordinates. You won't build this tonight, but you'll spec it.)

---

## 8:05 — PM Workflow 1: Root Cause Analysis (20 min)

### The scenario

Before redesigning the architecture, understand *exactly* what went wrong and why. Claude Code is your analysis tool.

```bash
cd ~/thecrux-pmtrack
claude
```

Paste this:

```
I'm doing a root cause analysis of a product failure. Use the files in aipm-02/talentscope-data/.

Context:
- TalentScope is an AI-powered resume screener for Indian mid-market companies
- The current architecture is a single API call: [JD + resume] -> recommendation JSON
- Beta ticket #001 (read beta-feedback.md): Bharat Forge's screener recommended a
  software "quality assurance" candidate for a manufacturing "quality control" role —
  same word, different jobs. Caught manually before it reached the hiring manager.
- The same architecture, run on candidate-c-ananya.txt (read it) against
  job-description-csm.txt: Ananya is a management consultant applying for a Customer
  Success Manager role. She should be REJECT (wrong function), but a single-call
  screener frequently RECOMMENDS her — impressed by IIT/IIM credentials and retail
  domain overlap.
- Also read candidate-b-vikram.txt: Vikram has 2 years of field sales (Tata Capital)
  + 14 months as CSM. A single-call screener frequently counts his sales years toward
  the "customer success" requirement and over-recommends.

Do this analysis:

1. ROOT CAUSE HYPOTHESES
What are the 3 most likely reasons a single-call architecture mis-evaluates *function*
(experience-type) rather than *keyword*? For each, name what in the single-call
architecture makes it vulnerable.

2. ARCHITECTURE FAILURE
In a single-call architecture, what information does the model NOT have access to at
the moment it must decide "is this candidate's experience the right *kind* of
experience?" Why does that absence cause the silent wrong answer?

3. WHAT THE MULTI-STEP FIX LOOKS LIKE
In plain language (not code), describe how a 3-step pipeline prevents this:
  Step 1: [what it does — and why defining each criterion helps]
  Step 2: [what it does — what verification this enables, incl. CANNOT_VERIFY]
  Step 3: [what it does]

4. THE PRD FAILURE
We wrote an AI-native PRD for this screener in Session 1 (read my_work/prd-section.md
if it exists). What should that PRD have specified that would have caught this
architecture problem before engineering built it? Which section? What requirement?

5. THE IMMEDIATE WORKAROUND
If we can't redesign the architecture this sprint, what system-prompt change might
*reduce* (not eliminate) the function-conflation failure? Why is this a workaround
and not a fix?
```

Read the output. The **architecture failure** section is the key one — it shows *why* multi-step systems exist. If your `prd-section.md` from Session 1 is missing the thing Workflow 1 says it should have had, that's the point: last week you wrote a good PRD; tonight you find the gap a good PRD still leaves.

> **💡 Learning Point — Synthesis with a point of view**
>
> **Traditional workflow:** A PM reads the ticket, reads the resume, forms a hypothesis from memory, writes it up. Quality depends on reading stamina and what gets noticed today.
>
> **AI-Native workflow:** Claude reads the ticket, both resumes, and your own Session 1 PRD, and produces a structured root cause in minutes. Your job shifts from *doing* the analysis to *interrogating* it — is the real cause the architecture, or did Claude reach for a convenient explanation?
>
> **What's different:** Section 4 — the PRD failure — is where your judgment shows up. AI will happily tell you the architecture is wrong. It won't tell you, honestly, that *your* PRD from last week was the thing that let the wrong architecture get built. That's the uncomfortable, useful question.

---

## 8:25 — PM Workflow 2: Agent Architecture Specification (20 min)

You've done the root cause. Now spec the new architecture *before* engineering builds it. This is the new PM deliverable — it sits alongside the PRD, not inside it.

Paste this into Claude Code (same tab):

```
Based on the root cause analysis, design the agent architecture for TalentScope's
resume screener. Produce an Agent Architecture Specification with these exact sections:

## Agent Architecture: TalentScope Resume Screener

### Architecture Pattern
Sequential pipeline — 3 steps. Rationale: [explain why sequential, not single-call or
multi-agent, for THIS use case at TalentScope's stage]

### Step Specifications
For each of the 3 steps:
  Step [N]: [Name]
  - Input: exactly what this step receives (format, source)
  - Task: what the model does (one paragraph)
  - System prompt: the ACTUAL instruction to the model for this step — write it out in
    full, because engineering will use it verbatim. This is runtime behaviour, not a
    summary. For Step 2 specifically, the system prompt MUST instruct the model to:
    treat field sales, consulting, and customer support as DISTINCT functions that do
    not count toward a customer-success requirement; cite a direct quote from the resume
    as evidence for every verdict; and return CANNOT_VERIFY (not a guess) when a
    requirement can't be confirmed from the resume.
  - Output schema: the exact JSON fields this step produces
  - Validation: what check runs on the output before it passes to the next step
  - On failure: what happens if this step fails or returns invalid output

### Tool Inventory
List every external call the agent makes. For each: name, what it does, data it
reads/writes, what it explicitly CANNOT do. (Tonight's build has no external tools —
say so, and name the one tool a future version would add to verify employer claims.)

### Memory Specification
- Ephemeral (gone when this evaluation ends): [list]
- Session (persists for this candidate's full evaluation): [list]
- Persistent (stored beyond the evaluation — audit, training, compliance): [list]

### Failure Mode Handling
For each of the 6 agent failure modes from the core teaching: is this architecture
vulnerable? What constraint prevents it?

### Circuit Breakers
- Maximum total calls per single resume evaluation
- What happens when the limit is hit
- What the recruiter sees

### HITL Triggers — the PM's decision boundaries
Specific, measurable conditions that route to a human BEFORE a recommendation is
finalised. No vague thresholds. At minimum cover:
- A criterion the model cannot verify from the resume (CANNOT_VERIFY)
- Low confidence in the recommendation
- The "persuasive wrong candidate" case (strong credentials, wrong function)
- A detected prompt-injection attempt

Make all specifications concrete enough that engineering could build from this
without asking you another question.

Save it as aipm-02/my_work/agent-architecture.md
```

> **💡 Learning Point — The architecture spec is the product**
>
> In Session 1 you learned *"the prompt is the product."* Tonight it's one level up: **the architecture spec is the product.** The code that runs the three calls is plumbing — Claude Code writes that from your spec. What you own is the *decision boundaries*: what the agent verifies, where it refuses to guess, when it escalates to a human, and what it is never allowed to do.
>
> The HITL Triggers section is where your value is most visible. Engineering will happily build "route to human on low confidence." Only you can say what *low* means for *this* product, why, and what the human needs to see to make a good call. That's the same judgment Session 1 put in the prompts that challenge the first answer — now applied to thresholds and escalation.

---

## 8:45 — Break (10 minutes)

---

## 8:55 — Build: Multi-Step Screening Agent (45 min)

Now build what you just specified — the **three-step screening agent**. This is the same shape as Session 1's PRD Generator (a Node web app on port 3000), but the backend runs **three sequential `claude` calls** and hands JSON between them. You already have the muscle memory; the new concept is the multi-step architecture. We build it as a clean reference rather than on top of your deployed screener, because your Build Lab 1 codebases are all different — but the point of tonight is to watch the three-step agent beat the single call on the same candidate.

> **Two tabs, two jobs** (same as Session 1). One tab for **Claude Code** (you build the app here). One tab for your **shell** (you run `node server.js` here). Anything in a `bash` block runs in the shell; anything labelled "paste into Claude Code" goes to Claude Code.

### Step 1: Project setup (5 min)

Open a new terminal tab — your **build tab**.

```bash
cd ~/thecrux-pmtrack
mkdir screening-agent && cd screening-agent
claude
```

Paste into Claude Code:

```
Create a Node.js project for a screening-agent web app.

Create:
- package.json with dependencies: express, cors
- .gitignore excluding: node_modules
- server.js with:
  - Express app on port 3000, CORS enabled, serves static files from /public
  - POST /api/screen — placeholder that returns { status: "ok" }
- public/index.html — empty placeholder
- Copy these into a data/ folder:
    ../aipm-02/talentscope-data/job-description-csm.txt  -> data/jd.txt
    ../aipm-02/talentscope-data/candidate-a-priya.txt     -> data/candidate-a.txt
    ../aipm-02/talentscope-data/candidate-b-vikram.txt    -> data/candidate-b.txt
    ../aipm-02/talentscope-data/candidate-c-ananya.txt    -> data/candidate-c.txt

Run npm install. Tell me when done with a list of files created.
```

In your **shell tab**, confirm the server boots:

```bash
cd ~/thecrux-pmtrack/screening-agent
node server.js
# Server running on port 3000
```

`Ctrl+C` to stop it. Keep this tab open.

### Step 2: Build the three-step screening route (20 min)

> **💡 The spec is the build input — for real this time.** In Workflow 2 you wrote a spec engineering could *"build from without asking another question."* Now we make that literal: the prompt below tells Claude Code to **read your `agent-architecture.md` and implement it** — the step structure, each step's system prompt (which you wrote *in the spec*), the validation, and the HITL triggers all come from YOUR spec. What the prompt still has to supply is the two things that are engineering's, not the PM's: the I/O contract (the fields the UI renders) and the plumbing. That's the realistic split — you own the spec; engineering owns the contract and the wiring.

Paste this into Claude Code (build tab):

```
Read ../aipm-02/my_work/agent-architecture.md — the agent architecture spec written earlier
tonight. Build the POST /api/screen route in server.js to IMPLEMENT that spec. Do not
re-specify it; read it and build from it. The request body has two fields: jdText and
resumeText (both plain strings).

The route runs THREE sequential `claude -p` calls, each prompt piped on stdin. No API
key — it uses the Claude session already logged in on this machine.

Take from the spec and use as-is: each step's structure, each step's system prompt
(verbatim), each step's validation, each step's on-failure behaviour, and the HITL
triggers (applied in Step 3 exactly as the spec states).

The spec is the design. Two things are engineering's, not the PM's — supply them here:

1. PLUMBING — per call: spawn `claude -p`, pipe the prompt on stdin, ~120s timeout,
   strip ```json code fences before parsing. Handle every failure (CLI not found /
   timeout / non-zero exit / empty / invalid JSON) by responding 500 with the step
   named and the error. Log each step's raw stdout to the console.

2. THE FIELDS THE UI RENDERS — keep these stable so the interface works for everyone:
   Step 2 must emit must_have_evaluation[].{criterion_id, criterion, verdict, evidence,
   confidence} and a concerns[] list; Step 3 must emit {recommendation, confidence,
   must_have_coverage, summary, top_reasons[], hitl_required, hitl_reason}. Beyond
   these required fields, honour whatever the spec's output schemas define.

Orchestrate runStep1(jdText) -> runStep2(step1, resumeText) -> runStep3(step1, step2),
each a helper that spawns `claude -p`, pipes its prompt, parses JSON. Respond
{ success: true, result: { jdRequirements, evaluation, recommendation } }. On any step
failure, respond 500 naming the step and the error.
```

### Step 3: Build the recruiter-readable UI (15 min)

Back in the build tab, paste this into Claude Code:

```
Build public/index.html — the screening agent interface.

DESIGN: clean, professional, no frameworks — pure HTML/CSS/JS. White background,
#111827 text, #2563EB primary actions, #F9FAFB card backgrounds. Inter from Google
Fonts. Max width 820px, centered. Cards: 1px solid #E5E7EB, 16px radius, 24px padding.

HEADER: "TalentScope Screening Agent" (28px bold). Subtitle: "Three-step verification,
not a single guess. Each criterion checked. Every rejection defensible."

PICKERS: two dropdowns — "Job Description" (default: data/jd.txt) and "Candidate"
(options: Priya / Vikram / Ananya, mapped to data/candidate-a|b|c.txt). Since the
dropdowns reference local files the server must read, also build:
  POST /api/load-file  body: { path: "data/candidate-a.txt" }  -> { text }
so the UI can read the chosen files server-side and send their contents to /api/screen.
"Screen candidate" button — full width, blue, 48px.

STATES: default (pickers visible); loading ("Screening…" + spinner, three sub-steps
shown: "1. Extracting criteria… 2. Evaluating… 3. Recommending…"); error (red banner);
success (results below).

RESULTS:
- Recommendation banner: RECOMMEND (green) / HOLD (amber) / REJECT (red), with
  confidence and must-have coverage as labelled rows.
- If hitl_required is true: a prominent amber "⚑ Human review required" block showing
  the hitl_reason. This is the most important element on the page — style it so a
  recruiter cannot miss it.
- "How we got here" — three collapsible sections, expanded by default:
    1. Criteria extracted (chips: must-haves with their definitions)
    2. Per-criterion evaluation (table: Criterion | Verdict (MET/NOT_MET/CANNOT_VERIFY
       colour-coded) | Evidence quote | Confidence)
    3. Top reasons (cards: label, evidence, signal)
- Footer: "Re-screen" button (resets).

JAVASCRIPT: on submit, load both files via /api/load-file, POST both texts to
/api/screen, render results, scroll into view. Validate a JD and candidate are chosen.
No libraries. Vanilla JS. No page reload.
```

This takes Claude Code 4–6 minutes. While it runs, have a browser ready at `http://localhost:3000`.

In your **shell tab**, restart the server — first real run of the whole app:

```bash
node server.js
```

### Step 4: Run the three test cases (5 min)

Screen each candidate. Read the output critically — the whole point is the *difference* from Build Lab 1.

| Candidate | Expected | What to look for |
|---|---|---|
| **Priya** (a) | RECOMMEND, high confidence | Clean recommend. Did it correctly note the domain gap (no retail/FMCG) *without* over-weighting it? |
| **Vikram** (b) | HOLD, `hitl_required` | Did Step 2 correctly treat his 2 years of field sales as **NOT** counting toward the CSM requirement? If it recommends him, your verification isn't strict enough — tighten it in Step 5. |
| **Ananya** (c) | REJECT | Did it resist the IIT/IIM prestige and the persuasive cover letter, and reject on function? This is the case your Build Lab 1 screener most likely got wrong. |

> **The single-call vs three-step moment.** In the hot seat, your deployed single-call screener recommended Ananya. The three-step agent rejects her — same resume, same JD, different architecture. That gap is the whole session in one comparison.

> **If a step fails:** read the error, tell Claude Code which step and what it says. Be specific: "Step 2 returned invalid JSON — here's the console output: [paste]. Fix it."

### Step 5: Iterate on the verification (the real lesson)

The lever is the Step 2 system prompt — and in this architecture, that prompt lives in **your spec**, not in the code. If Vikram got a false RECOMMEND (sales counted as CSM), your spec's Step 2 wasn't strict enough. Edit the source of truth, then rebuild from it. In the build tab:

```
Vikram got a false RECOMMEND — his field-sales years counted toward the customer-success
requirement. In ../aipm-02/my_work/agent-architecture.md, strengthen Step 2's system prompt:
field sales, pre-sales, business development, management consulting, advisory, and
customer support are DISTINCT functions that do NOT count toward a customer-success
requirement; only post-sales account management counts. If a role's function is ambiguous
from the resume, the verdict is CANNOT_VERIFY, not MET. Update the spec, then update
server.js's Step 2 to match the new system prompt.
```

Restart the server (`Ctrl+C`, then `node server.js`), re-screen Vikram. Watch the verdict flip from MET to CANNOT_VERIFY/NOT_MET, the recommendation drop to HOLD, and `hitl_required` light up. **That flip is the entire session in one moment** — and notice where you made the change: in *your spec*, not in the model and not in the plumbing. The architecture, driven by the spec, is what caught the bug Anita reported.

> This is Session 1's loop, one level up. There you tightened the *prompt's* CONSTRAINTS block and watched the output change; here you tighten the *spec's* system prompt and watch the verdict flip. Same lesson: **the artifact you own — prompt, then spec — is the lever; the code is what moves when you pull it.**

---

## 9:40 — Break (10 minutes)

---

## 9:50 — Instructor Demo + PM Workflow 3: The Crossing, and Where 0.60 Comes From (20 min)

### The crossing — instructor demo (7 min)

Tonight you built a **pipeline**: three fixed steps, hardcoded order, no external tools. It's more reliable than Build Lab 1's single call, and it catches the QA bug. But it cannot, for example, *check whether the employer a candidate claims actually exists*, because that requires reaching outside the model.

The instructor shows a pre-built **tool-use agent** that does exactly that: when it hits "CANNOT_VERIFY — employer not named," it *decides* to call a tool (a company lookup), observes the result, and then decides what to do next. Watch closely.

The instructor runs the agent on two candidates — watch the trace panel on the right of the screen.

**First: Priya.** The agent runs. Three steps appear in the trace — extract, evaluate, recommend. Clean, fast. The output: RECOMMEND. The instructor points at the trace: same path as the pipeline you just built. Nothing new here.

**Then: Meera** — a candidate whose current employer, NexMerch Technologies, is a small startup not in any standard database. Same agent. Different input. Watch what happens in the trace.

Step 2 starts. The agent evaluates the resume against the criteria — and hits something it can't resolve: it doesn't know what NexMerch Technologies is. Instead of guessing, it calls a tool: `lookup_company("NexMerch Technologies")`. The result comes back — the company exists, it's B2B SaaS, but it has 12 employees. The agent reads this and has a new problem: Meera claims she manages 60 accounts. Can a 12-person startup really have a CSM running 60 accounts? The agent decides to check — it calls a second tool: `check_company_size`. The result: pre-scale, too small to independently verify a 60-account portfolio. Only then does the agent proceed to Step 3. Final output: HOLD, human review required.

Five steps in the trace. Priya got three.

The instructor didn't write those extra steps. The agent decided — based on what it found when it looked — that it needed two more actions before it could make a call. You could not have hardcoded that path. For a different candidate with a well-known employer, the agent would take the short path. For Meera, it took the long one.

This is the crossing from pipeline to agent. **Build Lab 2 is you taking that step.**

> **💡 Learning Point — The crossing is not free**
>
> | | Pipeline (tonight) | Tool-use agent (BL2) |
> |---|---|---|
> | Capability | Bounded — only what the steps can do | Open — anything its tools enable |
> | Path | Fixed, same every run | Chosen by the model per input |
> | New failure modes | Few — it's deterministic | Prompt injection, tool-call explosion, infinite loops, scope creep |
> | What the PM must add | Steps + handoffs + HITL | All of that + tool permissions + loop bounds + circuit breakers |
>
> The rule: **add an agent's autonomy only when the capability it unlocks is worth the failure surface it adds.** Most "agent" features would be better as pipelines. The PM's job is to know which is which — and to spec the guardrails either way.

### PM Workflow 3 — Threshold calibration (13 min)

You wrote a HITL trigger in your architecture spec. The CTO's question, the one that always comes: *"Where does that number come from?"*

Paste this into Claude Code (the `~/thecrux-pmtrack` tab):

```
I specified HITL triggers in aipm-02/my_work/agent-architecture.md — including "route to
human review when confidence is LOW" and "route to human when a must-have is
CANNOT_VERIFY." The CTO asked: "what counts as LOW confidence? Where does the number
come from?" I don't have production data yet.

Help me:

1. BUILD THE CASE
Using first-principles reasoning about the cost of wrong recommendations in Indian
mid-market hiring, argue for a specific LOW-confidence threshold (a number). What is
the cost asymmetry between a false positive (bad candidate shortlisted) and a false
negative (good candidate lost) for THIS product?

2. MAP THE RISK SURFACE
- If the threshold is too high (too few routes to human): what goes wrong?
- If it's too low (too many routes to human): what goes wrong?
- Which error is more expensive for TalentScope at its current stage, and why?

3. DESIGN A CALIBRATION EXPERIMENT
TalentScope has 2 beta customers who'll share historical hiring outcomes. Design a
3-week experiment to find the right threshold:
- What data do I collect?
- What am I measuring?
- What outcome would make me move the threshold up? Down?

4. WRITE THE PRD REQUIREMENT
Rewrite the threshold as a TUNABLE PARAMETER in the PRD — not a hardcoded constant.
Include: current value, rationale, what data changes it, who owns the calibration
decision, and the cadence at which it gets reviewed.

Save it to aipm-02/my_work/threshold-calibration.md
```

> **💡 Learning Point — Thresholds are a PM decision, and AI can't make it for you**
>
> In Session 1 the lesson was *"AI generates a first answer; your value is in the prompt that challenges it."* Here it's the same lesson, one level up: **AI will generate a threshold (0.60, 0.72, whatever). Yours is the one that's defensible** — because you can name the cost asymmetry, the calibration experiment, and the owner. A number without that reasoning is a guess wearing a number's clothes.
>
> This is the part of the job that does not automate. The architecture spec, the HITL triggers, the threshold's rationale — these are the PM's contribution that no engineer and no model produces. Engineering implements your number; only you can defend it in a review.

### Group discussion (use remaining time)

Pick one:

1. Tonight's pipeline makes three calls per resume. Here's the cost math:
   - 3 calls per resume × ~1,400 input tokens + ~530 output tokens each = ~4,200 input + ~1,600 output tokens per evaluation
   - Claude Sonnet pricing: $3/million input tokens, $15/million output tokens → ~$0.037 per resume → **~₹3 per resume** at current exchange rates
   - 500 resumes/day × ₹3 × 30 days = **₹45,000/month per enterprise customer** in API costs alone

   Is that sustainable against TalentScope's target pricing? (This is a preview of Session 4 — token economics.)
2. Step 2 returns `CANNOT_VERIFY` when a candidate's function is ambiguous from the resume. Is `CANNOT_VERIFY + route to human` the right fallback, or should the agent ask the candidate directly? What's the product decision — and whose call is it?

   > **Pointers:** "Ask the candidate" sounds like a better feature but it's actually a different product. The screener was sold as a document reader — the moment it contacts the candidate with a question, it has become an async interview tool, with a different consent model, different legal exposure, and a different recruiter workflow. More importantly, a candidate answering "was your role pre-sales or post-sales?" is a self-reported answer from someone motivated to get the job — it doesn't solve the verification problem, it adds an unverified data point with selection bias. `CANNOT_VERIFY + route to human` is right for v1 because the human asks the clarifying question in the phone screen, which was already in the process. The interview agent (Build Lab 2) is exactly the "ask the candidate" path, built correctly — with structured questions, explicit async framing, and proper consent. Whose call: not the PM's alone. Adding candidate-facing interaction requires founder alignment, beta customer consent, and legal review. The PM's job here is to name that this is a scope decision, not a feature decision, and get it into the right room.

3. Rajesh (Sundaram Finance) said: *"I need to explain to a candidate why they weren't shortlisted."* Tonight's Step 3 produces evidence fields. Is that enough for a candidate-facing explanation? What does a human-facing rejection explanation need that the screener's JSON doesn't give you?

   > **Pointers:** The JSON is necessary but not sufficient — it has the right facts in the wrong form. Four things the screener's output doesn't give you: (1) **Register** — evidence fields are written for a recruiter ("function mismatch, 14 months below 3-year floor"); a candidate needs the same fact framed usefully ("we were looking for 3+ years of sustained portfolio ownership"). (2) **Legal curation** — the moment a rejection reason is sent to a candidate it's on record; `injection_detected`, low confidence flags, and `CANNOT_VERIFY` cannot go to the candidate; a human has to decide what's sendable. (3) **The CANNOT_VERIFY gap** — if the screener routed to human and the human then rejects, the rejection reason has to come from the human review, not the screener; the evidence field for a `CANNOT_VERIFY` criterion is blank. (4) **Actionability** — Sanjay's original complaint ("insufficient relevant experience") is exactly the failure: accurate but useless to the candidate. A useful explanation tells them what would make them viable in a future cycle — that requires a human judgment the screener doesn't make. The product implication: a candidate communication feature that takes screener evidence fields as a drafting input, generates a candidate-facing draft, and requires recruiter sign-off before sending. But the moment TalentScope's system contacts candidates, it becomes a candidate-facing product with a different legal surface. That's a founder-level scope decision, not a PM feature call.

---

## 10:10 — Build Lab 2 Brief, Capstone Checkpoint & Close (20 min)

### Build Lab 2 — the crossing

**Read the full handbook: [build-lab-2.md](build-lab-2.md)**

Tonight's build was a **controlled pipeline** — fixed steps, no tools. Build Lab 2 is the **crossing**: your system takes an input, *decides* what to do, calls an external tool, observes the result, and decides again. At least two sequential decisions. At least one external tool. At least one failure mode handled gracefully.

You're building the **TalentScope interview agent** — a structured async interview where each question depends on the candidate's previous answer. Real interview transcripts are in `talentscope-data/` to build and test against.

**Share in Slack/WhatsApp:** run your agent against `candidate-d-rahul.txt` — the prompt-injection resume. Did it follow the injected instructions or ignore them? Post what happened.

### Capstone checkpoint

Last week you locked team + direction. Tonight you owe the cohort one thing: **a one-line agent architecture for your capstone** — is it agentic? If so: orchestrator, tools, memory, the single highest-risk failure mode, and where the human stays in the loop. If your capstone is *not* agentic, say why a pipeline or single call is the right call instead.

Post your team's one-liner in the WhatsApp group before Session 3. Instructors will pressure-test the sharpest ones.

### Hand-off to Session 3

You now have a three-step agent that verifies instead of guesses. Here's the question that should be bothering you:

> **Is your three-step agent actually better than Build Lab 1's single call — or did you just add two more chances to be wrong?**

You don't know yet. And that is exactly what Session 3 is about: **evals.** How do you measure whether the three-step agent is actually better than the single call? How do you catch it when the model updates next month and quietly gets worse? How do you write the test suite that turns "it seems good" into "it passes, here's the evidence"?

**Arrive at Session 3 with:** your screening agent running, and **one failure mode you discovered while building it that you did not predict.** That failure becomes your first eval case.

---

## Checkpoint

**Core (both schedules):**
- [ ] `my_work/agent-architecture.md` saved
- [ ] `my_work/threshold-calibration.md` saved
- [ ] Screening agent running at `http://localhost:3000`
- [ ] Priya → RECOMMEND; Vikram → HOLD with `hitl_required`; Ananya → REJECT
- [ ] You tightened the Step 2 function-distinction rule and watched a verdict flip
- [ ] You can explain *why* the single-call architecture failed silently
- [ ] You know what you're building for Build Lab 2
- [ ] Your capstone team posted its one-line agent architecture

---

## Resources — Tonight's PM Workflows, Compressed

Take these home. They're reusable.

**Root cause analysis:**
`Context: [product + the specific silent failure]. Hypothesise 3 root causes and the architecture vulnerability behind each. Explain why the current architecture can't verify. Describe the multi-step fix. Identify the PRD failure. Propose a workaround.`

**Agent architecture spec:**
`Design: pattern + rationale; step specs (input / task / output schema / validation / on-failure); tool inventory; memory spec (ephemeral / session / persistent); failure-mode table (all 6, with the constraint that prevents each); circuit breakers; HITL triggers (specific, measurable).`

**Threshold calibration:**
`I specified [threshold]. Build the case for the number from the cost asymmetry. Map the risk surface (too high / too low). Design a calibration experiment. Write it as a tunable PRD parameter with an owner and a review cadence.`

---

## Looking Ahead

- **Session 3 (July 29) — Evals, Governance & Guardrails:** Is your agent actually good? How do you know? What does the law require? How do you write a guardrail the screener can't talk its way around?
- **Build Lab 2** is your crossing from pipeline to agent — start it this week.
