# Working Session 1 — AI Product Strategy & AI-Native PM Workflows

**Wednesday, June 17 · 7:30–10:30 PM IST**

---

## Tonight's Scenario

You joined TalentScope as their first PM two weeks ago. The CEO has handed you three folders and said: "The engineering team needs a product brief by Friday. We have user research from March, beta feedback from the last eight weeks, and the engineers are waiting. Figure out what we build for v1."

Tonight you use Claude to do the entire product analysis — synthesize the research, identify the gaps, write the PRD sections — and then you build a tool that automates this workflow.

If you haven't already, read the full TalentScope case study before we start — [case-study-talentscope.md](case-study-talentscope.md). It's the running scenario for tonight and across the Build Labs.

---

## What You're Building Tonight

By 10:30 PM you will have:
1. A complete product brief for TalentScope v1 (written live using Claude Code with real data)
2. A running **PRD Generator** web app that any PM can use for their own AI features

```
Tonight's through-line:
Research transcripts + beta feedback
        │  (Claude Code workflow)
        ▼
Opportunity memo → AI-native PRD sections → Decision memo
        │  (web app you build)
        ▼
PRD Generator live at http://localhost:3000
```

---

## Setting the Stage

**Have these ready by 7:30 PM:**
- Terminal open, Claude Code ready
- The cohort repo cloned:
```bash
git clone https://github.com/thecrux-ai/aipm-01 ~/thecrux-pmtrack/aipm-01
```
- A real AI feature from your own product in mind

> **You don't need an Anthropic API key for tonight** — Session 1 runs entirely through Claude Code (`claude -p`). You'll need an API key with credits for **Build Lab 1**, when you switch to the Anthropic API and deploy. Get one from console.anthropic.com before next week.

---

## Tonight's Schedule

We run one of two schedules depending on how fast the cohort moves — the instructors call it live. **Regular Pace** is the default. If we're flying through the material, we switch to **Accelerated Pace** — same agenda, brisker clip — and spend the time we save on the Bonus Labs in the room instead of leaving them entirely as homework. The timestamps on each section below follow Regular Pace.

### Regular Pace (default)

| Time | Block | What happens |
|---|---|---|
| 7:30 – 7:45 | Kickoff & Welcome | Course structure, expectations, case study |
| 7:45 – 8:00 | Core teaching Pt 1 | AI product strategy — the decisions most PMs skip |
| 8:00 – 8:20 | Core teaching Pt 2 | What breaks when you spec AI features the traditional way |
| 8:20 – 8:45 | PM Workflow 1 + 2 | Research synthesis → product brief (Claude Code, live) |
| 8:45 – 9:10 | PM Workflow 3 + 4 + discussion | AI-native PRD writing + cross-functional review board + TalentScope decisions |
| 9:10 – 9:20 | Break | |
| 9:20 – 10:05 | Build | PRD Generator web app |
| 10:05 – 10:30 | Build Lab 1 brief + Capstone + close | Build Lab brief, capstone options, form capstone teams |

Bonus Labs A (PM Skills) and B (PM Brain) are take-home this week — run them on your own or at office hours.

### Accelerated Pace (if we're moving fast)

Same agenda as Regular Pace — including the manual PM Workflows — just at a brisker clip. The time we save compresses into a block at the end for the Bonus Labs. We get through as much of Labs A and B as we can in the room; whatever's left stays as homework.

| Time | Block | What happens |
|---|---|---|
| 7:30 – 7:45 | Kickoff & Welcome | Course structure, expectations, case study |
| 7:45 – 8:10 | Core teaching Pt 1 + 2 | AI product strategy + what breaks with traditional specs |
| 8:10 – 8:30 | PM Workflow 1 + 2 | Research synthesis → product brief (Claude Code, live) |
| 8:30 – 8:50 | PM Workflow 3 + 4 + discussion | AI-native PRD writing + cross-functional review board + TalentScope decisions |
| 8:50 – 8:55 | Break | |
| 8:55 – 9:30 | Build | PRD Generator web app |
| 9:30 – 9:40 | Build Lab 1 brief + Capstone + close | Build Lab brief, capstone options, form capstone teams |
| 9:40 – 10:30 | Bonus Labs A + B | PM Skills, then PM Brain — highest-value exercises first |

---

## 7:30 — Kickoff & Welcome (15 min)

This is Session 1. We set the frame for the whole program.

**How this course works.** Instructors walk through:
- The arc: four Build Labs for hands-on practice, running in parallel with your Capstone — the real product you build alongside them and demo live on July 18
- What's expected each week — you ship working tools, not slides
- The TalentScope case study — the running scenario you'll use tonight; the Build Labs practice on it, and you can also choose it for your capstone

> Capstone options and team formation come at the **close** tonight (see "Looking Ahead"), once you've seen what a Build Lab actually involves.

---

## 7:45 — Core Teaching Pt 1: AI Product Strategy (15 min)

### The decision most PMs are not making deliberately

Before you spec a single AI feature, there's a question that should already be answered: what is AI's role in your product? Not "which features have AI in them" — but what strategic role does AI play?

Three archetypes. Pick the one that is true for your company right now:

**Archetype 1: AI as a feature**
AI does one or two things in your product. The product would largely work without it, but AI makes it faster or better.
- Your roadmap is: build the product, then add AI capabilities
- Competitive risk: anyone can add the same API calls you added
- When it makes sense: you're early-stage, proving the core loop first
- *Examples: Swiggy's AI-estimated delivery time. LinkedIn's AI-assisted profile suggestions. TalentScope today — the resume screener is one feature in an otherwise familiar HR platform.*

**Archetype 2: AI as infrastructure**
Every surface in your product is powered by AI. You're not adding AI features — you're building on top of an AI layer.
- Your roadmap is: the AI layer first, product surfaces second
- Competitive risk: harder to copy, but the AI layer is complex to maintain
- When it makes sense: the product's value *is* the intelligence layer
- *Examples: Zomato's restaurant ranking — every screen a user sees is shaped by AI, not just one feature. Netflix's recommendation engine. A mature TalentScope, where AI governs every step of the hiring pipeline, not just first-round screening.*

**Archetype 3: AI as the product**
The model is what you're selling. There is no separation between the AI and the product.
- Your roadmap is: model quality, model reliability, model cost
- Competitive risk: highest differentiation potential, hardest to build
- When it makes sense: you're building something that didn't exist before AI
- *Examples: Claude, ChatGPT, Gemini. GitHub Copilot — the code generation is not a feature of an IDE, it is the product. Perplexity. Harvey for legal work.*

**Why this matters right now**

The archetype determines three things: what you hire for, what you measure, and how you prioritise when trade-offs come up. A team that thinks they're building Archetype 1 but actually needs to build Archetype 2 will rebuild their architecture in 18 months. We've seen this happen.

### The build vs. buy question

For most product teams, "AI" means calling an API. That's the right default. But there are four triggers that should make you question it:

| Trigger | What it signals | Strategic question |
|---|---|---|
| The API's failure modes are unacceptable for your use case | The model does something you can't spec around | Can you fine-tune, or do you need a different foundation entirely? |
| Your prompt is so long it's eating your margin | Cost per call exceeds unit economics at scale | Can you compress the context or distill into a smaller model? |
| Your competitors have access to the same model | API parity = no moat | What do you have that they don't — workflow, proprietary data, distribution? |
| Vendor lock-in is a business risk | One vendor controls your cost structure and roadmap | What's your contingency if pricing doubles next year? |

For TalentScope tonight: they're calling the Anthropic API. That's correct for their stage — the screener is differentiating, but the failure cost is still low (no enterprise customers yet), so build-on-API is right. The question they should be asking now is: what will they have in 18 months that makes their screening better than a competitor who starts building with the same API today?

The answer is not the model. It's the data — every hiring outcome they log, every HITL override, every eval result. That's the moat they're building, whether they know it or not.

---

## 8:00 — Core Teaching Pt 2: What Breaks With Traditional Specs (20 min)

### The fundamental problem

A traditional PRD for a deterministic feature says: "Button submits the form. If the user doesn't fill in required fields, show an error. If the API fails, show a retry message."

The failure modes are enumerable. The outputs are deterministic. Testing is binary.

An AI feature is none of that. The same input produces different outputs. The failure modes are invisible — not error messages, but wrong answers that look like right answers. There is no unit test that proves an LLM feature is correct.

**The four things that break:**

| What traditional PMs write | Why it fails for AI |
|---|---|
| "The model summarises the ticket accurately" | Accurately according to what standard? Measured how? |
| Acceptance criteria that pass/fail at implementation | AI features need eval criteria that measure quality at runtime, not build time |
| No failure mode section | For AI, failures are silent — the model gives a wrong answer confidently |
| No eval plan | Someone has to own quality measurement or it never gets built |

### The four new sections every AI-native PRD needs

These sit alongside (not replacing) your existing background, goals, and requirements:

**1. Input/Output Contract** — What exactly goes in, what exactly comes out, and what happens with bad inputs.

**2. Quality Criteria** — Specific, measurable thresholds. Not "accurate" — "≥ 85% agreement with expert reviewers on a blind test of 50 cases."

**3. Failure Modes + Fallbacks** — For each failure mode: what triggers it, what the user specifically sees, what gets logged, who gets alerted.

**4. Eval Plan** — Who writes the test cases, what categories they cover, when the suite runs, what the pass/fail threshold is.

This is the PM's job. Engineering can build the system. Only you know what "good enough to ship" means.

---

## 8:20 — PM Workflow 1: Research Synthesis (15 min)

### The scenario

It's Day 1 as TalentScope PM. You have three user research transcripts in the repo. Your first task: produce an opportunity memo the engineering team can act on.

This is 45 minutes of careful reading and analysis done manually. Or it's 8 minutes with Claude Code.

### Step 1: Navigate to the session materials

```bash
cd ~/thecrux-pmtrack/aipm-01
ls talentscope-data/
claude
```

This is the TalentScope case-study folder. For tonight you'll use `user-research-transcripts.md` and `beta-feedback.md`; the candidate and job-description files are for Build Lab 1.

### Step 2: Run the research synthesis workflow

Paste this into Claude Code:

```
Read the file talentscope-data/user-research-transcripts.md

This is user research for TalentScope — an AI-powered hiring assistant for Indian mid-market companies.

After reading it, produce an Opportunity Memo with these sections:

1. HEADLINE FINDING
One sentence. The single most important thing these three interviews told us.

2. SHARED PAIN POINTS
What do all three HR managers complain about? List 3-4 problems that appear in multiple interviews, with a supporting quote from at least two different participants for each.

3. WHAT THEY WANT FROM AI (and what they don't)
Two lists:
- What they explicitly said they want
- What they explicitly said they don't want (or would stop them trusting the product)

4. HIDDEN REQUIREMENTS
Three requirements that aren't stated explicitly but are embedded in what they said. These are the things that will kill adoption if we miss them.

5. THE MVP SCOPE QUESTION
Based only on this research, what is the smallest version of TalentScope's screening feature that these three customers would trust enough to use in a real hiring process? What is explicitly out of scope for that MVP?

6. OPEN QUESTIONS
Three questions this research doesn't answer that we need to answer before we can finalise the v1 spec.

Format as a document I can share in a Slack channel. Use headers. Keep it under 600 words. Be direct — no preamble, no "great research" filler.
```

Read what Claude produces. Does it match your reading of the transcripts? Is the headline finding the thing you'd have written?

### Step 3: Push on the hidden requirements

The first output will probably be good but safe. Push harder:

```
The hidden requirements section is too obvious — these look like stated requirements, not hidden ones.

Hidden requirements are things the customers didn't say directly but which are clearly load-bearing. For example: Rajesh said "I need to be able to explain to a candidate why they weren't shortlisted." That implies the system must be auditable and explainable per-candidate — that's a hidden architecture requirement he didn't name.

Rewrite the hidden requirements section with 3 requirements like that — each one derived from a specific thing someone said, with the inference spelled out.
```

### Step 4: Save the output

```
Create a directory called my_work and save the opportunity memo to my_work/opportunity-memo.md
```

> **💡 Learning Point — Synthesis is no longer the bottleneck**
>
> **Traditional workflow:** A PM reads all three transcripts manually — 45+ minutes of careful reading — then synthesises patterns from memory and notes. Quality depends on reading stamina and what the PM happens to notice on a given day.
>
> **AI-Native workflow:** Claude reads everything in seconds and produces a structured first draft. The PM's job shifts from *doing* the synthesis to *interrogating* it — is the headline finding actually the headline? Are the "hidden requirements" genuinely hidden, or just restated?
>
> **What's different:** Your judgment now shows up in Step 3 — the follow-up prompt that pushes back on a too-safe first draft — not in the original reading. If you accept the first output as-is, you've outsourced the labor *and* the judgment.

---

## 8:35 — PM Workflow 2: Beta Feedback to Product Brief (10 min)

### The scenario

You also have 10 beta support tickets and NPS verbatims. The CEO wants to know: what are we fixing for GA, and what are we deferring? This is a product prioritisation call disguised as a support ticket review.

### Step 1: Run the beta analysis workflow

Paste this into Claude Code:

```
Read the file talentscope-data/beta-feedback.md

This is TalentScope's beta period support tickets and customer feedback. Analyse it and produce:

1. TOP 3 PRODUCT GAPS
The three problems appearing most frequently and most critically in the feedback. For each:
- Name the gap
- Count how many distinct customers mentioned it
- Quote the most telling verbatim
- Classify it: (a) blocks adoption for this customer, (b) reduces quality but doesn't block, or (c) nice-to-have

2. TRUST FAILURE SCENARIOS
Based on the feedback: what specific events would cause a customer to stop using TalentScope and go back to manual screening? Be specific — not "bad recommendations" but the exact scenario that breaks trust.

3. GA PRIORITISATION
Given the top 3 gaps, which one should we fix before GA and which two do we defer? Make a recommendation with a 2-sentence rationale for each.

4. GOVERNANCE RED FLAGS
Identify any issues in this feedback that are not just product gaps but potential legal or compliance risks — things that could expose TalentScope to liability if not addressed.

5. WHAT CUSTOMERS ARE ACTUALLY EVALUATING US ON
The NPS verbatims reveal what customers actually care about vs. what they said they cared about in discovery. What's the delta? What surprised you?

Format as a product brief. Bullet points where appropriate. Under 500 words.
```

### Step 2: Challenge the prioritisation

Before pasting, replace `[gap 1]` and `[gap 2]` below with the actual gap names from Claude's GA Prioritisation in Step 1 — `[gap 1]` is the one it recommended fixing, `[gap 2]` is one of the ones it deferred.

```
You recommended fixing [gap 1] before GA. 

Play devil's advocate: make the strongest case for fixing [gap 2] instead. What would have to be true about our customer base or business model for that to be the right call?
```

### Step 3: Combine both analyses

```
Read my_work/opportunity-memo.md and the beta analysis you just produced.

Write a single PRODUCT BRIEF (under 400 words) that a CTO would read to understand:
- What TalentScope v1 is building (one paragraph)
- The 3 most important decisions we've made about scope (bulleted)
- The 2 biggest risks to the v1 launch plan
- What we're explicitly not building in v1 and why

Save it as my_work/product-brief.md
```

> **💡 Learning Point — Pattern-finding vs. trade-off judgment**
>
> **Traditional workflow:** A PM (or support lead) triages tickets manually. Prioritisation tends to follow whoever escalated loudest or most recently — recency bias disguised as urgency.
>
> **AI-Native workflow:** Claude surfaces frequency-weighted patterns and trust-failure scenarios across all 10 tickets at once, with verbatims attached. But the GA prioritisation call is still yours — and Step 2 forces you to stress-test it by arguing the opposite case.
>
> **What's different:** AI is excellent at finding patterns across volume that a human would miss or under-weight. It's not good at making the trade-off call alone — it will confidently recommend a prioritisation without knowing your business model's actual constraints. The PM's judgment now lives in the prompts that challenge the first answer, not in the original analysis.

---

## 8:45 — PM Workflow 3: Writing AI-native PRD Sections with Claude (15 min)

> **🗣️ Discussion Topic — PRDs: State of the Union**
>
> **1. What goes into it, and who is it really for?**
> Think of your last PRD's table of contents — what's always there, and what's dead weight nobody reads? Which of your stakeholders (engineering, design, data science, leadership, legal/compliance, GTM, support) use the PRD and for what?
>
> **2. Where does your PRD break down today?**
> Where does your PRD go stale fastest? What's the biggest gap between "what it said" and "what shipped" — especially for any AI feature you've already shipped? And what's been hardest about the process itself — the writing, getting stakeholder alignment, or keeping it updated as things change?
>
> We'll come back to this discussion after writing TalentScope's AI-native PRD and see what actually changed.
.
### The scenario

Based on the product brief you produced earlier, let's write the actual AI-native PRD section for TalentScope's resume screener — the one the engineering team will build from. Remember, these sit alongside the existing sections (background, goals, requirements).



Go back to the first terminal tab — the one running Claude Code in `~/thecrux-pmtrack` (not the `prd-generator` tab from the Build block).

**Missing `opportunity-memo.md` or `product-brief.md`?** Flag an instructor — they'll get you a copy so you're not blocked.

Paste this:

```
Read my_work/product-brief.md and my_work/opportunity-memo.md

Based on everything we know about TalentScope's screening product from these documents, write the AI-native PRD section for the resume screening feature.

Use this exact structure:

## AI-Native PRD: Resume Screening Feature

### Input / Output Contract
[Input spec with format, constraints, bad input handling]

### Quality Criteria
[3 criteria with measurable thresholds — cite specific numbers from the research where possible. Where we don't have data, mark as NEEDS DATA with a description of what experiment would produce the data.]

### Failure Modes
[5 failure modes specific to resume screening in the Indian hiring context — not generic AI failure modes. Each must name: trigger, user experience, what's logged, escalation path.]

### Eval Plan
[Test categories that specifically cover the failure modes surfaced in the beta feedback — at minimum: the context misread (a software "QA" resume scored as manufacturing "QA"), the ignored hard criterion (geography requirement not enforced), the undefendable rejection reason ("insufficient relevant experience" with no specific gap cited), and the college-tier bias (Tier 1 colleges over-represented despite a substance-over-institution instruction). Name who owns the suite and when it runs.]

After writing the PRD, identify: which of the five failure modes in our spec would the traditional PM have missed? Why?

Save the output as my_work/prd-section.md
```
. 
> **🗣️ Discussion Topic — TalentScope decisions (10 min)**
> 
> Compare what Claude produced with what you'd have written from memory. Three questions:
> 1. **Quality criteria:** Rajesh (Sundaram Finance) said "I need to be able to explain to a candidate why they weren't shortlisted." Which quality criterion in our PRD covers this? If it's missing, what would you add?
> 2. **Failure modes:** The beta had a ticket where the screener misread "QA in manufacturing" as "QA in software." Which failure mode in our PRD would have caught this? Would a traditional PRD have caught it?
> 3. **Eval plan:** Pooja (Marico) raised a college bias concern. Is that covered in our eval plan? If not, what test cases would you add?

. 

> **💡 Learning Point — Traditional PRD vs. AI-Native PRD**
> | | Traditional PRD | AI-Native PRD |
> |---|---|---|
> | Problem | User pain point | User pain point + what AI helps decide |
> | Requirements | Features | Capabilities — human vs. AI responsibility split |
> | User flow | Deterministic steps | Human ↔ AI collaboration loop |
> | Architecture | Services / APIs | Models, tools, memory, agents |
> | **Acceptance ✦** | Pass / fail tests | **Input/Output Contract** — inputs, outputs, bad-input handling |
> | **Quality bar ✦** | "Accurate" (subjective) | **Quality Criteria** — numeric thresholds, no adjectives |
> | **Failure ✦** | Error messages | **Failure Modes + Fallbacks** — silent failures named explicitly |
> | **Operations ✦** | Often omitted | **Eval Plan** — named owner, cadence, pass threshold |
> | Metrics | Adoption, retention | + quality + cost + latency |
> | Risks | Technical risks | + hallucinations, trust, safety, cost overrun |
> *✦ = the four new sections updated here*
>
> **Traditional PRDs** (for deterministic features) define what the software should do; **AI-native PRDs** (for probabilistic capabilities) define what outcomes the AI should achieve, what context it needs, how its quality will be evaluated, and where humans should remain in control.
> 
> **Traditional PMs** ask, "What should we build?" **AI-native PMs** ask, "What should the AI be able to do, how will we know it's doing it well, and when should humans stay in control?"
>
> Claude can automate much of the PRD creation process, the **PM's value shifts to problem framing (what to build, why it matters, defining success), exercising judgement (decisions & trade-offs) & stakeholder buy-in**.
> 
.

---

## 9:00 — PM Workflow 4: PRD Review Board with Cross-Functional Stakeholders (10 min)

You've written the PRD. In a real org, that draft now goes to a room — engineering, data science, legal, GTM, leadership — and each person reads it through a different lens. Getting that sign-off is usually the slowest, most political part of the job. It's also the part the handbook flagged as where the PM's value lives: **stakeholder buy-in**.

This workflow runs that room before you've booked it. Claude role-plays five senior stakeholders, each reading the same PRD with their own mandate and a verdict they own. You walk out with a consolidated list of what must change before GA — and the conflicts only you can resolve.

> **🗣️ Discussion Topic — Who actually has to say yes?**
>
> Think about the last spec you shipped. Who could have blocked it but didn't get a real read until late? Which department's concern surfaced *after* you'd committed to a date? A review board is only useful if the cast is right — too few and you miss the veto, too many and nobody owns anything.

### The scenario

Your PRD section (`prd-section.md`) is written. Before it goes to the real review, you want to know where it'll get torn apart — while it's still cheap to fix. The TalentScope room has five people who matter: Meera Pillai (CEO), Aditya Shah (CTO), a Data Science lead, Legal & Compliance, and a GTM lead carrying the Sundaram Finance contract that closes end of July.

Go back to the first terminal tab — the one running Claude Code in `~/thecrux-pmtrack` (the same tab you used for Workflow 3, not the `prd-generator` build tab).

Paste this:

```
Read my_work/prd-section.md, my_work/product-brief.md, and my_work/opportunity-memo.md

Convene a PRD review board for the resume screening feature. Role-play these five
TalentScope stakeholders, each reading the same PRD through their own lens. Do NOT
write generic feedback — every concern must cite something specific in the PRD or
the source documents.

The board:

1. Aditya Shah, CTO — owns engineering sign-off. Cares about: build cost, model
   latency at hiring-season volume, integration with the existing ATS. Will block
   if the Input/Output Contract or Eval Plan implies effort nobody has scoped.

2. Data Science Lead — owns the quality bar. Cares about: whether the Quality
   Criteria thresholds are actually measurable, whether the eval set covers the
   college-bias concern Pooja (Marico) raised, and whether NEEDS DATA items have a
   real experiment behind them.

3. Legal & Compliance — owns the compliance gate. Cares about: DPDP, the rule that
   there are NO automated rejections, and candidate explainability (Rajesh at
   Sundaram Finance: "I need to explain to a candidate why they weren't shortlisted").
   Will block if a human is not demonstrably in the loop on every reject.

4. GTM Lead — owns launch-readiness against customer commitments. Cares about: the
   end-July Sundaram Finance deadline, what Sales can honestly promise, and which
   PRD scope is at risk of slipping the date.

5. Meera Pillai, CEO — owns the final go/no-go. Cares about strategic fit and
   breaks ties between the others. Speaks last.

For EACH stakeholder, output:
- Verdict: APPROVE / APPROVE WITH CONDITIONS / BLOCK
- Top 1–2 concerns, each pointing at a specific line or section of the PRD
- The exact condition(s) that would turn their verdict to APPROVE

Then synthesize:
- MUST-FIX BEFORE GA: the consolidated, de-duplicated list of conditions
- OPEN CONFLICTS: where two stakeholders want incompatible things (e.g. GTM's date
  vs. Data Science's eval rigor) — state the trade-off, do NOT resolve it. These are
  the PM's calls.
- Which single concern would a PM reviewing this PRD alone most likely have missed,
  and why?

Save the output as my_work/prd-review-board.md
```

> **🗣️ Discussion Topic — Resolve the conflicts (5 min)**
>
> Read the OPEN CONFLICTS section. Pick the sharpest one — almost certainly **GTM's July deadline vs. Data Science's eval rigor**. Three questions:
> 1. Who's right? What would you cut or phase to ship something defensible by end of July without breaking the no-automated-rejection rule?
> 2. Did the board surface a condition you hadn't thought of? Which one, and from which stakeholder?
> 3. Is there a "yes" here that's actually fake — a stakeholder who approved but whose real concern wasn't addressed?

> **💡 Learning Point — A review board catches misalignment, not bad decisions**
>
> | | Without a review board | With an AI review board |
> |---|---|---|
> | When concerns surface | Late — in the real meeting, after you've committed | Early — while the PRD is still cheap to change |
> | Coverage | Whatever the loudest reviewer noticed | Five lenses, simultaneously, every section read |
> | The PM's job | Defend the draft | Adjudicate the conflicts the board exposed |
>
> **Traditional workflow:** the PRD goes to a real cross-functional review, where concerns surface one department at a time over days — and the conflicts get resolved by whoever's most senior in the room, not by the clearest trade-off.
>
> **AI-Native workflow:** Claude runs all five lenses in one pass and hands you a structured map of what must change and where the stakeholders genuinely disagree. But note what it does **not** do — a review board tells you whether your PRD is *aligned and complete*, not whether the underlying bet is *right*. The OPEN CONFLICTS are deliberately left unresolved. Picking the trade-off — date vs. rigor, scope vs. compliance — is still the PM's call, and it's the part that doesn't automate.

---

## 9:10 — Break (10 minutes)

---

## 9:20 — Build: PRD Generator Web App (45 min)

Now let's build a tool that turns the AI-native PRD framework into something any PM can use in 5 minutes.

### What you're building

A Node.js web app that runs at `http://localhost:3000`. A PM fills in 4 fields (feature name, description, company context, user problem), clicks Generate, and gets a structured AI-native PRD with all four new sections.

### Step 1: Set up the project (5 min)

> **Two tabs, two jobs.** In this build you'll paste prompts into **Claude Code** (to build the app) and run commands like `node server.js` in a plain **shell**. Claude Code takes over a tab once you launch it, so keep them separate: one tab for Claude Code, one tab for your shell. Anything in a `bash` block runs in the shell tab; anything labeled "paste into Claude Code" goes to Claude Code.

Open a new terminal tab — this is your **build tab**. First, confirm the Claude CLI is installed, since the app calls it at runtime:

```bash
which claude
# You should see a path like /usr/local/bin/claude
# If you see "claude not found", stop — Claude CLI is not on PATH. Ask for help before continuing.
```

Then create the project and launch Claude Code in this same tab:

```bash
cd ~/thecrux-pmtrack
mkdir prd-generator && cd prd-generator
claude
```

Paste this into Claude Code:

```
Create a Node.js project for a PRD generator web app.

Create these files:

package.json with dependencies: express, cors

.gitignore excluding: node_modules

server.js with:
- Express app on port 3000
- CORS enabled  
- Serves static files from /public
- POST /api/generate-prd — placeholder that returns { status: "ok" }
- POST /api/save-prd — placeholder that returns { status: "ok" }

/public/index.html — empty placeholder

Run npm install after creating package.json.
Tell me when done with a list of files created.
```

When Claude Code says it's done, verify the server starts. Open a **second terminal tab** — your shell tab — and run it there (leave Claude Code running in the build tab):

```bash
cd ~/thecrux-pmtrack/prd-generator
node server.js
# You should see: Server running on port 3000
```

Press `Ctrl+C` to stop it. Keep this shell tab open — you'll restart the server here in Step 3.

### Step 2: Build the PRD generation route (15 min)

> **💡 The prompt is the product.** Everything Claude Code wires up in this step — how Node launches the CLI, how it handles timeouts, how it strips code fences — is plumbing. Claude can write that from a one-line requirement. The part that matters, the part *you* own, is the prompt: it encodes the AI-native PRD framework you learned earlier as a reusable system instruction. Read the PERSONA, CONTRACT, and CONSTRAINTS below as the actual deliverable — the rest is implementation detail you're delegating.

Paste this into Claude Code:

```
Build the POST /api/generate-prd route in server.js. It takes four fields in the request body: featureName, featureDescription, companyContext (one sentence about the company), userProblem (one sentence: the pain point this solves).

Wire it up to Claude — these are requirements, you implement the details:
- Call the local `claude` CLI in headless mode as a subprocess, passing the prompt on stdin. No API key — it uses the Claude session already logged in on this machine.
- Use a generous timeout (~120s) and handle every failure so the route never hangs or crashes: CLI not found / not on PATH, timeout, non-zero exit, empty output, and output that isn't valid JSON. On any of these respond with status 500 and a clear error message.
- Log the raw stdout and stderr to the console so we can see what Claude returned.
- Claude may wrap its reply in ```json code fences — strip them before parsing.
- On success, parse the JSON and respond { success: true, prd }.

The prompt is the product — build it from these three parts, in this exact order:

PERSONA + TASK:
You are a senior AI product manager. You write AI-native PRDs that include the four sections traditional PRDs miss: input/output contract, quality criteria, failure modes and fallbacks, and an eval plan. When given a feature brief, you produce a structured PRD section in valid JSON — no markdown, no code fences, just the JSON object.
Write the AI-native PRD section for this feature:
Feature name: ${featureName}
Description: ${featureDescription}
Company context: ${companyContext}
User problem: ${userProblem}

CONTRACT — return exactly this JSON structure:
{
  "featureName": "string",
  "inputOutputContract": {
    "inputs": [
      { "name": "string", "format": "string", "constraints": "string", "required": true }
    ],
    "outputs": [
      { "name": "string", "format": "string", "alwaysPresent": ["list of fields guaranteed in every output"] }
    ],
    "badInputHandling": [
      { "scenario": "string", "systemBehavior": "string", "userMessage": "string" }
    ]
  },
  "qualityCriteria": [
    {
      "criterion": "string — one specific, measurable quality property",
      "threshold": "string — numeric or observable threshold, no adjectives",
      "measurementMethod": "string — exactly how you would measure this",
      "launchBlocker": true
    }
  ],
  "failureModes": [
    {
      "name": "string — a short name for this failure mode",
      "trigger": "string — exact input condition or model behavior",
      "userExperience": "string — exact message or behavior the user sees (not 'handle gracefully')",
      "logged": "string — what event is logged",
      "escalation": "string — who is notified and what they do"
    }
  ],
  "evalPlan": {
    "owner": "string — role responsible for writing and maintaining the test suite",
    "testCategories": ["list of input categories the suite must cover"],
    "adversarialCases": ["3 specific inputs designed to break this feature"],
    "cadence": "string — when the suite runs",
    "passThreshold": "string — specific pass/fail threshold",
    "failureAction": "string — what happens when the suite fails"
  }
}
CONSTRAINTS:
- Exactly 2 inputs, 1 output with at least 3 guaranteed fields, 2 bad input scenarios
- Exactly 3 quality criteria
- Exactly 5 failure modes
- All thresholds must use numbers or observable properties — no adjectives like "high quality" or "accurate"
```

That CONSTRAINTS block is the framework, operationalised — "exactly 5 failure modes," "no adjectives" are the rules that turn a vague PRD into a measurable one. You'll tune it in Step 4 and watch the output change.

Don't test the route standalone yet — there's no UI calling it. Step 3 builds the interface, and that browser run is your first real test of the whole app. If the route has a bug, the server console you just wired up will show it there.

### Step 3: Build the web interface (20 min)

Back in the build tab, paste this into Claude Code:

```
Build public/index.html — the complete PRD generator interface.

DESIGN:
- Clean, professional, no frameworks — pure HTML, CSS, JavaScript
- Colors: white background, #111827 text, #2563EB blue for primary actions, #F9FAFB light grey for result cards
- Font: Inter from Google Fonts
- Max width 760px, centered, 24px horizontal padding
- All sections have 1px solid #E5E7EB borders, 16px border-radius, 24px padding

HEADER:
"AI-Native PRD Generator" in 28px bold
Subtitle: "Generate quality criteria, failure modes, and eval plans for AI features — in under 60 seconds."
Small text below: "Based on the AI-native PRD framework. Powered by Claude."

FORM:
Four fields:

1. Feature Name — text input, placeholder: "e.g. Resume Screener, Smart Reply, Risk Classifier"

2. Feature Description — textarea (5 rows)
   placeholder: "What does this AI feature do? Who uses it? What does it produce as output? (2-3 sentences)"

3. Company + Product Context — text input
   placeholder: "e.g. TalentScope is a Pune-based HR-tech startup building AI-powered hiring tools for Indian mid-market companies."

4. User Problem — text input
   placeholder: "e.g. HR managers spend 60-70% of their time on manual resume screening with no systematic way to do it at scale."

"Generate AI-native PRD" button — full width, blue, 48px height, 16px font weight 600

STATES:
- Default: form visible, no results
- Loading: button says "Generating…" and is disabled, spinner animation below the button
- Error: red banner with error message below button  
- Success: results section appears below the form; a "Generate Another" link at the bottom resets the form

RESULTS — four collapsible sections, each starting expanded:

Section 1 "Input / Output Contract"
  Subsection "Inputs": table with columns Name | Format | Constraints | Required
  Subsection "Outputs": card showing the output name and a chip list of guaranteed fields
  Subsection "Bad Input Handling": numbered list, each item shows Scenario / System Behavior / User Message

Section 2 "Quality Criteria"
  Three cards in a grid (2-col on wide screens, 1-col narrow)
  Each card: criterion name as title, then Threshold / Measurement Method as labelled rows
  Cards with launchBlocker: true get a red "Launch Blocker" badge

Section 3 "Failure Modes"
  Five cards in an accordion (click to expand/collapse)
  Each card header: failure mode name + a severity badge
  Expanded: Trigger / User Experience / Logged / Escalation as labelled rows

Section 4 "Eval Plan"
  Top row: Owner and Cadence side by side
  Pass Threshold and Failure Action side by side
  Test Categories: chip list
  Adversarial Cases: numbered list with red left border

Bottom of results:
- "Copy as Markdown" button — copies the full PRD as formatted markdown to clipboard
- "Generate Another" button — scrolls back to form and clears results

JAVASCRIPT:
On submit:
  1. Validate all four fields non-empty — show inline error if any are empty
  2. Set loading state
  3. POST to /api/generate-prd
  4. Parse response — if success render results, if error show banner
  5. Scroll results into view on success

Copy as Markdown:
  Build a markdown string from the PRD JSON that formats all four sections cleanly.
  Use navigator.clipboard.writeText. On success flash the button text to "Copied!" for 2 seconds.

No libraries. Vanilla JS. No page reload.
```

This takes Claude Code 4–6 minutes. While it runs, open a browser ready at `http://localhost:3000`.

Back in your shell tab, restart the server — this is the first real run of the whole app:

```bash
node server.js
```

Open `http://localhost:3000` and fill in the TalentScope Resume Screener:

- **Feature Name:** Resume Screener
- **Feature Description:** An AI that reads a job description and a candidate resume and produces a structured hiring recommendation — recommend, hold, or reject — with specific evidence from the resume supporting the decision.
- **Company + Product Context:** TalentScope is a Pune HR-tech startup building AI-powered hiring tools for Indian mid-market companies with 200–2000 employees.
- **User Problem:** HR managers spend 60–70% of their time on first-round resume screening for 300–500 applications per role, with no systematic way to do it at scale.

Click Generate. In 15–30 seconds you should see the PRD rendered as cards and tables — readable, not raw JSON. This is the moment to actually *read* what the prompt produced: are the five failure modes specific? Do the quality criteria use numbers?

**If the button does nothing or shows an error:** the route is the likely culprit, not the page. Look at the server console — it logs Claude's raw stdout and stderr — then tell Claude Code: "The /api/generate-prd route is failing. Here's the server console output: [paste]. Fix it."

### Step 4: Iterate on quality (5 min)

The prompt is the lever — tighten the CONSTRAINTS block and the output changes. Let's prove it.

In the browser, generate a PRD for a feature from your own product and read the Quality Criteria section. Even with the "no adjectives" rule, models drift — you'll often still catch soft language like "high accuracy" or "consistent outputs," or a threshold with no number behind it.

Tighten the constraint. In the **build tab**, tell Claude Code:

```
In server.js, find the CONSTRAINTS section of the PRD prompt — the line about thresholds using numbers and no adjectives. Make it stricter, replacing that line with:

"Every threshold must include a number (percentage, count, or time value) — no adjectives allowed. If no benchmark data exists, write the threshold as [NEEDS DATA: description of what data would set this threshold]."
```

Then restart the server so the change takes effect — in your **shell tab**, `Ctrl+C` to stop it, then:

```bash
node server.js
```

Regenerate the same PRD in the browser and compare. The vague criteria should now be either hard numbers or explicit `[NEEDS DATA: …]` flags. That's the loop: the prompt is the product, and tightening one constraint — not touching the app's logic — is how an AI-native PM raises the quality bar.

---

## 10:05 — Build Lab 1 Brief, Capstone & Close (25 min)

**Read the full handbook: [build-lab-1.md](build-lab-1.md)**

This week you ship a working AI-powered tool with a **live URL.**

**Everyone builds the same thing:** the TalentScope resume screener — takes a job description and a resume, returns a structured hiring recommendation. One product, one path, so the whole cohort works from the same data. (Building for your own product is the *capstone* — you can start that any time from week 1.)

**The big shift from tonight:** Session 1 ran on `claude -p` (Path A), which only works on your machine. Build Lab 1 switches to the **Anthropic API (Path B)** and deploys to Vercel — that's the only path that gives you a live URL.

**Before you close tonight, decide:**
- What is the input/output contract for the screener? (The contract is in `build-lab-1.md`.)
- What's the one failure mode you most want to catch?

**Sharing:** When you're done, post your live Vercel URL in the cohort WhatsApp group, along with the one failure mode that surprised you most.

---

## Looking Ahead: Capstone Options & Teams

Now that you've seen what a Build Lab involves, we set up the capstone. The capstone runs **in parallel** with the Build Labs — the Labs are your hands-on practice, the capstone is the real product you build alongside them and demo live on July 18 (8 minutes, running code, not slides). You'll work it in **teams of 2–3.** Three direction options:

1. **Your own product** — spec and build for a real AI feature on your team's roadmap
2. **TalentScope** — make the running case study your capstone and take it further than the Labs do
3. **One of 10 provided case studies** — Indian companies, each with a clear AI product opportunity, a set of PM decisions, and a hard governance question

**The 10 case studies:**

| # | Company | Capstone opportunity |
|---|---|---|
| 1 | Swiggy | Restaurant Turnaround |
| 2 | Zomato | Hyperlocal Supply Intelligence |
| 3 | CRED | Credit Deterioration Early Warning |
| 4 | Razorpay | Integration Health Monitor |
| 5 | ClearTax | SMB Compliance Risk Audit |
| 6 | Urban Company | Service Quality Recovery |
| 7 | Meesho | New Seller Activation |
| 8 | Groww | Investment Goal Recovery |
| 9 | Practo | Post-Discharge Recovery Monitoring |
| 10 | IndiaMART | B2B Deal Facilitation |

Full details for each: [capstone-problems.md](capstone-problems.md)

**Form your teams now — live, with a shared Google Sheet:**

> **Capstone team sign-up sheet:** _(instructor drops the link in the WhatsApp group)_
>
> 1. Open the sheet. It has numbered team rows, each with three seats.
> 2. Type your name into any open seat. Already have a partner? Grab a row together. Solo? Claim a seat — teammates will land next to you.
> 3. Add your team's capstone direction in the right-hand column once you've picked one.
>
> Instructors sweep any unseated names into the emptiest rows so nobody's left solo. The sheet stays open all program — teams refine their direction as the Build Labs progress.

**This week's homework:** Lock your direction with your team — don't wait until Build Lab 4. Pick based on how confidently you can make PM decisions, not on technical complexity — see "How to Choose" in the case studies doc. Post your team's choice (and a one-line reason) in the cohort WhatsApp group before Session 2.

---

## Checkpoint

**Core (both schedules):**
- [ ] `opportunity-memo.md` exists in `my_work/`
- [ ] `product-brief.md` exists in `my_work/`
- [ ] `prd-section.md` exists in `my_work/`
- [ ] PRD Generator is running at `http://localhost:3000`
- [ ] You generated a PRD for a real feature from your product
- [ ] You know what you're building for Build Lab 1
- [ ] You chose your Capstone project

**Bonus Labs (in the room on Accelerated Pace, otherwise homework this week):**
- [ ] PM Skills installed and at least one command verified (Bonus Lab A)
- [ ] PM Brain installed and bootstrapped for TalentScope (Bonus Lab B)

---

## Bonus Labs

Two optional labs extend tonight's work — **Bonus Lab A (PM Skills)** and **Bonus Lab B (PM Brain)**. On Accelerated Pace we run them in the room; otherwise they're homework this week.

**→ [bonus-labs.md](bonus-labs.md)**
