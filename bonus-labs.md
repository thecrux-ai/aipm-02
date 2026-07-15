# Session 1 — Bonus Labs

Two optional labs that go beyond tonight's core build. On **Accelerated Pace** we run them in the room; otherwise they're homework this week. Both build directly on the TalentScope work from the session.

- **Bonus Lab A — PM Skills Marketplace:** run six pre-built PM workflows (discovery, feedback, competitive, OKRs, pre-mortem, North Star) as Claude Code slash commands.
- **Bonus Lab B — PM Second Brain:** stand up a markdown "second brain" for TalentScope and exercise all six of its commands.

---

## Bonus Lab A: PM Skills Marketplace

Session 1's PM Workflows used raw Claude prompts. PM Skills gives you 68 pre-built skills and 42 chained workflows — structured frameworks from Teresa Torres, Marty Cagan, and Alberto Savoia baked into slash commands. You're about to run six of them on TalentScope.

Each exercise names the plugin it draws from, what to paste, and what to look for in the output.

### Install PM Skills (2 min)

PM Skills ships as a Claude Code **plugin marketplace** ([github.com/phuryn/pm-skills](https://github.com/phuryn/pm-skills)). Add the marketplace, then install the six plugins these labs use — they're slash commands you run inside Claude Code, not prompts:

```
/plugin marketplace add phuryn/pm-skills
```

```
/plugin install pm-product-discovery@pm-skills
/plugin install pm-product-strategy@pm-skills
/plugin install pm-market-research@pm-skills
/plugin install pm-data-analytics@pm-skills
/plugin install pm-execution@pm-skills
/plugin install pm-marketing-growth@pm-skills
```

Prefer a menu? Run `/plugin` on its own to browse the marketplace and install with the arrow keys. Either way, confirm it worked by typing `/pm-product-discovery:discover` — the discovery workflow should now be available.

### What's in each plugin — and how to see every skill

68 skills and 42 chained workflows is a lot. Here's what the six plugins you installed cover, and the commands you'll actually run in these labs:

| Plugin | What it covers | You'll use here |
|---|---|---|
| `pm-product-discovery` | Ideation, experiments, assumption testing, feature prioritization, interview synthesis | `/pm-product-discovery:discover` |
| `pm-product-strategy` | Vision, strategy canvas, value props, lean & business-model canvas, SWOT, PESTLE, Ansoff, Porter's Five Forces, monetization | — |
| `pm-market-research` | User personas, market segmentation, sentiment analysis, competitive analysis | `/pm-market-research:analyze-feedback`, `/pm-market-research:competitive-analysis` |
| `pm-data-analytics` | SQL query generation, cohort analysis, retention patterns | — |
| `pm-execution` | PRDs, OKRs, roadmaps, sprints, pre-mortems, stakeholder maps, user stories, prioritization | `/pm-execution:plan-okrs`, `/pm-execution:pre-mortem` |
| `pm-marketing-growth` | Marketing ideas, value-prop statements, North Star metrics, naming, positioning | `/pm-marketing-growth:north-star` |

**To see only the PM Skills commands:** type `/pm-` in Claude Code. Plugin commands are namespaced by plugin (e.g. `/pm-product-discovery:discover`), so the `pm-` prefix filters the slash menu down to just these six plugins — each command listed with a one-line description of what it does. To browse one plugin's full contents, run `/plugin`, open the plugin, and view the commands and skills it provides. Full reference: [github.com/phuryn/pm-skills](https://github.com/phuryn/pm-skills).

---

### Exercise 1: Full discovery on a TalentScope opportunity (10 min)

**Plugin:** `pm-product-discovery` — chains brainstorm-ideas → identify-assumptions → prioritize-assumptions → brainstorm-experiments automatically.

```
/pm-product-discovery:discover Language-aware resume screening for Indian regional languages

TalentScope is an AI-powered hiring assistant for Indian mid-market companies. We currently only support English resumes reliably. HR teams in South India regularly receive resumes in Tamil, Kannada, and Telugu. Tamil and Hindi resumes fail silently in our beta — Tamil is rejected immediately, Hindi produces garbled output. 6 of our 6 beta customers are affected at some scale. Multi-language support is listed as post-GA.
```

**What to watch:** The command runs four steps without you prompting between them. At the "prioritize assumptions" step, notice which assumption it classifies as highest impact × highest uncertainty — that's the one experiment you should run before committing to a build. The command will suggest one.

**Discussion:** The riskiest assumption is almost certainly "translation quality is good enough for screening." What's the cheapest 2-week experiment to test that without building anything?

---

### Exercise 2: Analyze the beta feedback (8 min)

**Plugin:** `pm-market-research` — `/pm-market-research:analyze-feedback` (feature-request + sentiment analysis).

```
/pm-market-research:analyze-feedback

Read the file talentscope-data/beta-feedback.md

This is TalentScope's 8-week beta feedback from 6 enterprise customers across manufacturing (Bharat Forge, Kirloskar Electric), financial services (Sundaram Finance, Indiabulls Housing Finance), and FMCG (Marico).

Analyze for:
- Sentiment by industry segment (manufacturing vs NBFC vs FMCG)
- Feature request themes ranked by frequency and severity
- NPS signal: what separates the promoters from the detractors in this data?
- Which feedback is strategic (affects positioning or trust) vs tactical (affects the backlog)?
```

**What to watch:** Compare the theme ranking the skill produces against your own read of the beta feedback. Does it catch the same top-3 issues? Where does structured analysis beat your intuition, and where did you spot something the framework missed?

---

### Exercise 3: Competitive landscape (8 min)

**Plugin:** `pm-market-research` — `/pm-market-research:competitive-analysis`.

```
/pm-market-research:competitive-analysis

Product: TalentScope — AI-powered resume screening and structured interview tool for Indian mid-market companies (200–2,000 employees). Currently in beta, targeting GA in 8 weeks.

Competitors to map:
- Darwinbox (enterprise HR platform with AI screening features, large Indian enterprise focus)
- HireQuotient (AI recruiting platform, US-founded)
- TurboHire (AI hiring platform, India-focused, mid-market)
- iSmartRecruit (ATS with AI screening, India)
- HR teams using ChatGPT directly as a DIY alternative

Frame this for a Relationship Manager hiring decision at an NBFC choosing between options.
```

**What to watch:** The "differentiation opportunities" section is where TalentScope's roadmap should be aimed. Does TalentScope's current GA plan exploit the gaps the analysis surfaces? Where are competitors systematically weakest?

---

### Exercise 4: Draft Q3 OKRs (8 min)

**Plugin:** `pm-execution` — `/pm-execution:plan-okrs`.

```
/pm-execution:plan-okrs

Team: TalentScope Product (2 co-founders + 2 engineers)
Quarter: Q3 2026 (July–September)
Company objective: Reach first 10 paying customers and prove the product can be trusted in real enterprise hiring processes.

Context for the PM team:
- 6 beta customers, mixed NPS (2 promoters, 2 passives, 2 detractors)
- 7 open P1 bugs still unresolved
- Top beta issues: college-tier bias, geography criterion handling, multilingual failure, confidence score confusion
- GA target: end of August
- Core constraint: one visible wrong recommendation reaching a hiring manager kills adoption for that account

Draft 3 Objectives with 3–4 KRs each. Every KR must have a number.
```

**What to watch:** Hold each KR to the test you'd apply yourself — is it measurable, outcome-based, and does it have a known baseline? Which KRs pass, and which ones would still be red?

---

### Exercise 5: Pre-mortem the GA launch (8 min)

**Plugin:** `pm-execution` — `/pm-execution:pre-mortem`.

```
/pm-execution:pre-mortem

Plan: TalentScope GA launch, target date August 31.

What we're committing to:
- Product: AI resume screener, structured interview agent, hiring recommendation memo
- Quality: resolve 7 P1 bugs, ship fairness guardrail, redesign confidence score UI, PDF + Excel export
- Sales: convert 4/6 beta customers to paid + close 6 net new
- Governance: HITL review on all recommendations, DPDP-compliant, no automated rejections

Classify risks as:
- Tigers (will almost certainly happen, material impact)
- Paper Tigers (feel scary, unlikely to actually bite)
- Elephants (everyone knows about it, nobody is saying it out loud)
```

**What to watch:** The Elephants are the highest-value output — the risks a team knows about but avoids naming. TalentScope's likely elephant: the college bias fix requires a fairness eval suite that nobody has scoped. Does the pre-mortem surface it? If not, it's itself an elephant.

---

### Exercise 6: Define TalentScope's North Star (5 min)

**Plugin:** `pm-marketing-growth` — `/pm-marketing-growth:north-star`.

```
/pm-marketing-growth:north-star

Company: TalentScope — AI-powered hiring assistant for Indian mid-market companies.

The product does three things: resume screening, structured AI interview, hiring recommendation memo.

The customer's job: get a trustworthy shortlist for an open role faster than manual screening, where every recommendation is defensible to a hiring manager.

Current stage: pre-revenue, 6 beta customers, targeting GA.

Help define the North Star Metric, the input metrics that drive it, and classify which business game we're playing.
```

**What to watch:** A good North Star for TalentScope should capture *value delivered to the recruiter*, not usage volume. "Resumes screened" is easy to game. "Recommendations accepted by hiring managers without override" is harder to hit but actually proves the product works. What does the skill propose — and do you agree?

---

## Bonus Lab B: PM Second Brain

You just produced six outputs — a discovery cycle, a beta-feedback analysis, a competitive analysis, OKRs, a pre-mortem, and a North Star definition. Where do these go? Most PMs paste them into Notion. Within a month, nobody can find them. Six months later, the team re-discovers the same issues they already analyzed.

PM Brain is a folder of markdown files that Claude reads before answering and writes to after. Every claim is tagged with where it came from — interview, stakeholder comment, your own hunch. The `/review` command sweeps it every Friday and tells you what's drifting.

No vector database. No cloud sync. Your repo, your laptop, your data.

### How PM Brain works

One product, one brain — a folder of plain markdown files Claude reads before answering and writes to after. Everything runs through a single loop:

1. **Ingest** an artifact — a transcript, doc, screenshot, app-connector pull (Notion/Jira/Slack), or a line you paste in chat.
2. **Source + synthesize** — the original copies to `source/` untouched (the audit anchor); a structured synthesis lands in `ingestion/`, with each line tagged as an **observation**, **interpretation**, **hypothesis**, or **assumption**.
3. **Propagate** — the durable layer updates wherever the new signal applies. One artifact often touches four to six files: a user insight, a hypothesis's evidence, a stakeholder's touchpoints, a drafted decision.
4. **Tag provenance** — every load-bearing claim wears a marker showing how trustworthy it is and where to verify it. The implicit hierarchy: documented decision > documented research > documented stakeholder claim > verbal claim > PM intuition. It's a default, not a rule — when something low contradicts something high, the brain surfaces both and lets you decide.
5. **Sweep** — Friday's `/review` reads the whole folder, compresses what's recurring, flags what's drifting, and drafts the bigger calls for you.

**The promotion rule is the point.** A one-off observation stays in `ingestion/`. Only signals that are *recurring + decision-relevant + strategy-relevant* get promoted into `knowledge/` — N observations fold into one cited claim. The brain forgets on purpose; that's what stops it becoming a landfill.

#### The structure of the brain

Three layers, six folders — all plain markdown you can grep and edit yourself:

| Layer | Folders | What's in it |
|---|---|---|
| **Raw** (cold storage, audit trail) | `source/` · `ingestion/` | `source/` = untouched originals; `ingestion/` = the tagged synthesis of each artifact, linked back to its source. Read only when a claim's provenance link points at them. |
| **Active** (your working set) | `hypotheses/` · `decisions/` · `stakeholders/` | Hypotheses you're tracking evidence for; decisions with their evidence trail and reversal conditions; one file per stakeholder with their asks and concerns. |
| **Durable** (compressed knowledge) | `knowledge/` | Your stable picture of strategy, product, users, market, org. Grows slowly — it's what `/ideate`, `/plan`, and `/risk` actually load. |

A few concepts that make it work:
- **Provenance tag** — the marker on every claim (`[source/...]`, `(stakeholder-verbal, name, date)`, `(intuition, PM, date)`, …). "We need dark mode" from one verbal comment reads very differently from the same claim across 27 documented sales calls.
- **Hypothesis lifecycle** — `candidate → proposed → validated → demoted → archived`, driven by accumulating (or contradicting) evidence.
- **Reversal condition** — every decision names the specific thing that would reopen it ("if 3 lost deals cite enforced SSO"), not a vague "if circumstances change."
- **Contradiction** — two signals that disagree. The brain surfaces them and does *not* flatten them to consensus; the disagreement is the signal.
- **Drift** — a hypothesis or strategy that's gone stale. `/review` flags it so you revive, demote, or archive.

#### The six commands

| Command | What it does |
|---|---|
| `/ingest <thing>` | Feed an artifact in — file, paste, or note. The skill detects the shape (interview, meeting, market signal, ad-hoc) and routes it. |
| `/prep <stakeholder>` | One-page brief before a meeting: their open asks, last unresolved concern, suggested questions. |
| `/review` | Weekly sweep — six checks across the brain. Fixes small things directly, drafts the bigger calls for you. |
| `/ideate <problem>` | Synthesis, not brainstorm. Loads strategy, insights, and hypotheses; surfaces 3–7 evidence-tagged directions. |
| `/risk <feature>` | Five-area risk scan; drafts hypothesis stubs for any area with no coverage. |
| `/plan <objective>` | Six-block draft plan: what we know, assumption vs evidence, who to interview, hypotheses to open, experiments to run, decision points. |

#### What it is *not*

- Not a notes app — it's an opinionated structure with maintenance built in.
- Not a chatbot with memory — the memory lives in your repo, not in Claude.
- Not a vector database — every file is plain markdown you can grep yourself.
- Not an agent memory system — nothing is embedded or retrieved by similarity; it stores only what you and Claude deliberately wrote down.
- Not autonomous product management — judgment stays with you; the brain just makes the cross-referencing easier.

#### Tips to get the most from it

- **One brain per product.** Keep each product's context in its own folder — don't merge.
- **Capture small and often.** Ingest a file, an app export, or a one-line note the same day it happens. Over-capture beats under-capture — the promotion rule filters relevance, so a dead-end note costs disk space, not clarity.
- **Tag honestly.** A hunch is a hunch; a verbal comment is a verbal comment. The value comes from *not* dressing intuition up as research — the brain weights evidence by how it's tagged.
- **Run `/review` every Friday.** The weekly sweep is the forcing function that compresses the pile and surfaces drift. Skip it and the brain rots like any other notes system.
- **Flag, never gate.** The brain surfaces; you decide. Override the evidence hierarchy whenever your judgment says so.
- **Give every decision a reversal condition.** Future-you needs to know what would reopen the call, not just what you chose.
- **Don't backfill your whole history.** In migration mode, capture your *current* state — active strategy, in-flight hypotheses, recent decisions. Old artifacts resurface through current work; forcing 200 old transcripts in just clogs it.
- **When it's wrong, edit the markdown.** It's your repo — fix the file directly. Trust comes from it being inspectable.

### Install PM Brain (2 min)

Paste this into Claude Code:

```
Install the PM Brain skill from GitHub: https://github.com/phuryn/pm-brain

The skill files live at .claude/skills/pm-brain/ inside the repo. Install them so they land at ~/.claude/skills/pm-brain/ on my machine. Tell me when done and confirm the path exists.
```

> **⚠️ Mac heads-up — fix one line before you bootstrap.** The brain installs a PostToolUse validation hook (`.claude/settings.json`) that runs `python …`. macOS ships only `python3` — there is no bare `python` — so on every file the brain writes you'll get a *"PostToolUse edit hook error" / `command not found`* and the validator silently never runs. Fix it once in the scaffold so every brain you create is correct. Paste into Claude Code:
>
> ```
> In ~/.claude/skills/pm-brain/scaffold/.claude/settings.json the hook command runs bare `python`, which doesn't exist on macOS (only python3). Change it to a portable form that prefers python3 and falls back to python:
> sh -c 'if command -v python3 >/dev/null 2>&1; then python3 .claude/hooks/validate_brain_file.py; else python .claude/hooks/validate_brain_file.py; fi'
> ```
>
> Why the fallback instead of just hardcoding `python3`: it keeps working on machines that only have `python`. The setting lives inside each brain's own `.claude/` — it never changes how your other projects resolve Python. (If you already created a brain before fixing this, apply the same change to that brain's `.claude/settings.json` too, and restart the session so the hook reloads.)

---

### Exercise 1: Bootstrap your TalentScope brain (10 min)

> ## 🛑 STOP — OPEN A FRESH, NEW TERMINAL FIRST
> **Do NOT reuse the `prd-generator` tab or your main repo session.** Open a **brand-new terminal window**, then create the brain in its **own folder, `talentscope-brain`, inside `thecrux-pmtrack`:**

```bash
mkdir ~/thecrux-pmtrack/talentscope-brain
cd ~/thecrux-pmtrack/talentscope-brain
claude
```

> **One brain per product.** Spin up a separate brain folder for each product you manage. A brain can start two ways: **blank** (an empty folder → *greenfield*, what we do here) or **seeded** — drop existing files in first, then call it, and it boots in **migration mode**, reading and absorbing what's already there. We start blank and ingest gradually, so you watch each artifact land. More at [github.com/phuryn/pm-brain](https://github.com/phuryn/pm-brain).

In Claude Code:

```
/pm-brain
```

Your folder is empty, so `/pm-brain` starts in **greenfield mode** — a short onboarding interview (5 batches). Answer as TalentScope's first PM, two weeks into the role. Use everything from the session:

| Question batch | What to say |
|---|---|
| Company | TalentScope, Pune-based HR-tech startup, AI-powered hiring assistant for Indian mid-market companies (200–2,000 employees) |
| Your role | First PM, building v1 from beta to GA. Reporting to Meera (CEO). |
| Current priorities | Resolve 7 P1 bugs, ship fairness guardrail, convert 4 beta customers to paid, GA by end of August |
| Key stakeholders | Meera Pillai (CEO), Aditya Shah (CTO), 6 beta customer contacts across NBFC/manufacturing/FMCG |
| What's in flight | GA launch plan, fairness guardrail spec, confidence score redesign, Excel export |

**What to watch:** At the end of onboarding, the brain produces a report: what it understood, what it couldn't make sense of, and the questions you should answer this week. That gap list is your actual first-PM-week agenda — not the one in the onboarding doc.

---

### Exercise 2: Ingest the user research (8 min)

```
/ingest ~/thecrux-pmtrack/aipm-01/talentscope-data/user-research-transcripts.md
```

After it finishes, ask:

```
What does the brain now understand about what these three HR managers have in common — and where do they diverge? What hypotheses can I form about what TalentScope must get right to earn their trust?
```

**What to watch:** The brain reads all three transcripts simultaneously and cross-references them. It should surface that Anita, Rajesh, and Pooja all named the same kill condition without using the same words: one visible wrong recommendation reaching a hiring manager ends adoption. A human analyst usually misses this when reading transcripts back-to-back because the phrasing is different. The brain catches the pattern.

---

### Exercise 3: Ingest the beta feedback (8 min)

```
/ingest ~/thecrux-pmtrack/aipm-01/talentscope-data/beta-feedback.md
```

After ingestion, ask:

```
Based on everything ingested so far, what hypotheses should I be tracking? List each as: claim, evidence count, evidence source tags, and what would validate or invalidate it.
```

**What to watch:** The brain should form at least three structured hypotheses from the ticket patterns:
1. College-tier bias is systematic, not random (evidence: Ticket #004, Marico NPS verbatim, user research)
2. Geography handling fails because structured criteria are buried in free-text JDs (evidence: Ticket #002, Rajesh's interview)
3. Confidence score is consistently misread as candidate quality rather than model certainty (evidence: Ticket #008, anonymous detractor verbatim)

These are now tracked hypotheses — not just observations — with evidence rows that future ingestion will update.

---

### Exercise 4: Capture a live stakeholder signal (5 min)

You just got off a call with Rajesh Menon (Sundaram Finance). He said:

> "Fix the geography problem and add Excel export — we'll sign the 12-month contract by end of July."

```
Rajesh Menon, HR Manager at Sundaram Finance, told me on a call today: "Fix the geography problem and add Excel export — we'll sign the 12-month contract by end of July."

Capture as a verbal stakeholder claim. Route to any open hypotheses or decisions it touches. Note any conflict with documented evidence.
```

**What to watch:** The brain should tag this `(stakeholder-verbal, rajesh-menon, [today])` and route it to: (1) the geography handling hypothesis, (2) the output format issue from Ticket #007, and (3) the commercial pipeline. One verbal signal, three open items updated — without PM Brain, this lives in your notes app until you forget it.

---

### Exercise 5: Prep for the Rajesh meeting (4 min)

Rajesh Menon is now a stakeholder in your brain (from the last exercise), and you have a follow-up call with him. Get a brief without reading a single note yourself:

```
/prep Rajesh Menon
```

**What to watch:** The brief should pull only what the call needs — his open ask (fix geography + add Excel export), the commercial context (12-month contract, end-July deadline), the last unresolved concern, and a few suggested questions. It reads his `stakeholders/` file and the items linked from it, not the whole brain. That's the stakeholder layer paying off: one command, meeting-ready.

---

### Exercise 6: Ideate on the hardest problem (8 min)

```
/ideate How should TalentScope address the college bias problem before GA? The model over-weights IIM/XLRI/SPJIMR candidates even when instructed to focus on substance. Marico called it a dealbreaker. We have 6 weeks before GA target.
```

**What to watch:** `/ideate` draws from everything ingested — not a blank brainstorm. The output should reference Marico's specific verbatim, the hypothesis from Exercise 3, the time constraint from your OKRs, and the governance requirement from the case study. That's synthesis from your knowledge base. Generic ideation from a fresh Claude session can't do this.

---

### Exercise 7: Risk-scan a feature before building it (5 min)

```
/risk Fairness guardrail for the resume screener — flags or blocks recommendations that show college-tier or geography bias before they reach a hiring manager.
```

**What to watch:** `/risk` runs a five-area risk scan and drafts hypothesis stubs for any area with no coverage yet in the brain. It should connect to the college-bias hypothesis from Exercise 3 and the governance/DPDP exposure from the case study — and, crucially, turn the areas where you have *no* evidence into tracked hypotheses instead of silent blind spots.

---

### Exercise 8: Draft the GA launch plan (6 min)

```
/plan Get TalentScope to GA in 6 weeks: resolve the college-bias and geography issues, ship the fairness guardrail, and convert at least 4 of 6 beta customers to paid.
```

**What to watch:** `/plan` returns a six-block draft — what we know, assumption vs evidence, who to interview, hypotheses to open, experiments to run, decision points. It should be grounded in everything you've ingested: the hypotheses from Exercise 3, Rajesh's commitment from Exercise 4, and the OKRs you built in Bonus Lab A. It's a starting draft for your judgment — note where it honestly marks assumptions vs evidence rather than guessing.

---

### Exercise 9: Friday review (5 min)

```
/review
```

**What to watch:** After eight exercises, the review should already surface:
- One open decision with a deadline (how to fix college bias in 6 weeks)
- One hypothesis ready to act on (geography: 3 independent observations, solution candidate exists)
- One verbal commitment needing a follow-up (Rajesh's July offer)
- The hypothesis stubs `/risk` opened for areas with no evidence yet
- One drift flag (anything you captured but left without a next action)

The review is the payoff for every small capture during the week. Skip it once and the system stays useful. Skip it for a month and you're back to notes-in-Notion.

> **The one habit that makes this work:** Every customer call, every Slack decision, every stakeholder commitment — capture it the same day. The Friday `/review` output is the return on every small capture made Monday through Thursday.

---

## Appendix: Other PM Skill Libraries Worth Knowing

Bonus Lab A ran on `phuryn/pm-skills` — but that's one library, not the whole map. The PM-skills ecosystem moved fast through 2026, and a few others are genuinely worth knowing. Here's the shortlist, what makes each different, and when to reach for it.

These overlap heavily with what you already installed. So treat this as **"explore for X," not "install everything."** Pick by the gap you actually have.

### The four worth a look

**1. Anthropic's official `product-management` plugin** — the one Anthropic maintains itself, and the only one wired into real tools.

```
claude plugin marketplace add anthropics/knowledge-work-plugins
claude plugin install product-management@knowledge-work-plugins
```

What's different: **connectors.** It plugs PM workflows straight into Slack, Linear, Jira, Asana, Notion, Figma, Amplitude, Pendo, Intercom, and Fireflies — so "synthesize this research" or "draft the stakeholder update" runs against your actual data, not a pasted snippet. Covers specs, roadmaps, research synthesis, stakeholder updates, and competitive tracking. *Best for: when you want PM work happening inside the tools you already live in.*

**2. `Product-Manager-Skills`** by Dean Peters / Productside — the most pedagogic of the bunch.

```
/plugin marketplace add deanpeters/Product-Manager-Skills
```

What's different: 49 skills + 6 workflows that **teach the reasoning, not just fill a template** — grounded in named methods (Geoffrey Moore, Jeff Patton, Teresa Torres, Amazon's Working Backwards). It even has career-pathway skills for the PM→Director→VP/CPO jump. *Best for: learners — it's the closest in spirit to how this course teaches the *why*, not just the output.*

**3. `PM Superpowers`** by aniganti — strategy and defensibility, deep.

```
claude plugin marketplace add aniganti/pm-superpowers
claude plugin install pm-superpowers
```

What's different: 13 strategy skills that **chain** — competitive landscape → VRIO → moat analysis → full strategy → pre-mortem — plus RICE/ICE prioritization and decision logging. *Best for: competitive strategy and moat work, where one analysis should feed the next.*

**4. `pmprompt`** — a free, open-source grab-bag of 26+ PM frameworks.

```
/plugin marketplace add pmprompt/skills
```

*Best for: when you just want a quick framework on tap.*

> **One heads-up before you install.** `deanpeters/Product-Manager-Skills` also nicknames its marketplace `pm-skills` — the *same* nickname as the `phuryn/pm-skills` you used in Bonus Lab A. Add both and any `install ...@pm-skills` command turns ambiguous. Add one at a time, and you'll be fine.

That's it — same job as Bonus Lab A, different lenses. Start with the gap you have, not the longest install list.
