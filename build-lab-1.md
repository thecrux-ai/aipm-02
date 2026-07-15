# Build Lab 1 — Build Your First AI-Powered Product Tool

**After Session 1 · Due before Session 2 (June 24) · 2–3 hours**

In Session 1 you built and ran a tool locally with `claude -p`. Build Lab 1 goes further — you build something that connects to a real product problem, with a real input/output contract and a clear sense of what "working" means, and you ship it as a live product anyone can open.

---

## What You're Building

Everyone builds the same tool in Build Lab 1 — the **TalentScope resume screener.** One product, one path, so the whole cohort works from the same data and can compare PM decisions. See `case-study-talentscope.md` for full context.

> **Want to build for your own product?** That's your **capstone** — and you can start it any time from week 1. Build Lab 1 stays on TalentScope for everyone.

It takes a job description and a resume and produces a **structured hiring recommendation.** Concretely, it:
- Takes a real, structured input (a job description + a resume) — not a free-text chat prompt
- Produces a meaningful, structured output (the recommendation below) — not a paragraph of text
- Does something that would be harder or slower without AI

**The output doesn't need to be beautiful.** It needs to run, produce the right kind of output, and be something you can show to someone and explain why the AI is doing useful work.

**The output contract:**
```json
{
  "recommendation": "recommend | hold | reject",
  "confidence": 0.0–1.0,
  "must_have_coverage": 0.0–1.0,
  "reasons": [
    { "label": "string", "evidence": "quote from resume", "signal": "positive | gap" }
  ],
  "flags": ["optional: low_confidence | incomplete_data"]
}
```

**Your test inputs are in the `talentscope-data/` folder:**
- `job-description-csm.txt` — the role you're screening against
- `candidate-a-priya.txt` through `candidate-f-incomplete.txt` — six real candidate resumes (`candidate-f-incomplete` is deliberately broken — use it to test your bad-input handling)
- `expected-outputs.json` — what a good screener should produce, so you can check your work

---

## Before You Open Claude Code

Do this first, on paper or in a notes doc:

**Step 1: Write the input/output contract**
- What exactly goes in? (Format, length, required fields)
- What exactly comes out? (Format, length, required fields)
- What happens with bad inputs? (Empty, wrong format, irrelevant content)

**Step 2: Write three test cases**
Write three specific input/output pairs you expect before you build. Don't worry about being exactly right — you're setting up test cases to evaluate your build against.

**Step 3: Identify the highest-risk failure mode**
What's the most likely way this tool gives a wrong output without the user knowing? Write it down. You'll come back to this.

This pre-work takes 20–30 minutes and makes the build significantly faster and more purposeful.

---

## Building It

### Getting started in Claude Code

Create a **separate `talentscope` project** under your PM Track directory and start a git repo in it — this is the repo you'll deploy.

Run these in your **terminal** (a normal shell, *not* inside Claude Code). The last line, `claude`, is what launches Claude Code — so run everything above it first, then start Claude Code from inside the `talentscope` folder:

```bash
cd ~/thecrux-pmtrack
mkdir talentscope
cd talentscope
git init
claude
```

### What to describe to Claude Code

Your first message to Claude Code should describe:
1. What you're building (one paragraph)
2. The input/output contract (use the format you wrote above)
3. The tech constraints: a small web app with a **serverless backend function that calls the Anthropic API** — your `ANTHROPIC_API_KEY` stays server-side, never in the browser. Keep it simple, but this is the setup you'll deploy to Vercel.
4. The test cases you want it to handle (use the candidate files from `talentscope-data/`)

**Do not give Claude Code the full prompt for your tool.** Describe what you want the tool to do, and let Claude Code figure out the prompt. Then iterate on the prompt once you see what it produces.

### The iteration loop

After your first build, run your three test cases. For each failure:
1. Describe what happened vs. what you expected
2. Ask Claude Code to fix it — be specific about the behavior, not just "it's wrong"

The most valuable thing in Build Lab 1 is discovering a failure mode you didn't predict. When you find one, write it down. This will be important for Build Lab 3.

### Switch to the API and deploy to Vercel (required)

In Session 1 you ran everything through `claude -p` — Path A. That only works on your own machine. Build Lab 1's deliverable is a **live URL**, so you switch to **Path B: the Anthropic API**, and deploy to Vercel.

`claude -p` does not exist on Vercel's servers — that's why the API-key path is the only one that deploys. Your app calls the Anthropic API with your `ANTHROPIC_API_KEY` from a serverless function, never from the browser. You'll use this same Path B setup for every Build Lab and your capstone.

**Step 1 — Sign up for Vercel (free).**
Go to [vercel.com](https://vercel.com) and sign up — the Hobby plan is free and is all you need. Signing up with your GitHub account is the smoothest option, because Vercel can deploy straight from a GitHub repo.

**Step 2 — Commit your `talentscope` files.**
You already ran `git init` in the `talentscope` folder. Before committing, make sure your **API key and dependencies are never checked in** — Claude Code should have created a `.gitignore`; confirm it lists `node_modules` and `.env`. Then:

```bash
git add .
git commit -m "TalentScope resume screener"
```

**Step 3 — Deploy.** Either path works:
- **CLI:** run `npx vercel` from the `talentscope` folder and follow the prompts, or
- **GitHub:** push the repo to a GitHub repo named `talentscope`, then "Add New → Project" in Vercel and import it.

**Step 4 — Add your key.** In the Vercel project's **Settings → Environment Variables**, add `ANTHROPIC_API_KEY` with your key, then redeploy. (The key lives only in Vercel — never in the repo.)

**Step 5 — Verify and share.** Open your live URL, run one candidate end to end, then **post your Vercel link in the cohort WhatsApp group.** That's your Build Lab 1 done.

---

## What to Bring to Session 2

1. **Your live Vercel URL** — the deployed screener, running and ready to demo
2. **Your input/output contract** — written down
3. **One discovered failure mode** — the most interesting thing that went wrong
4. **Your answer to this question:** "If this tool was running for 100 users per day, how would you know if it started giving wrong answers?"

---

## Getting Unstuck

**If you're not sure where to start:** Open `case-study-talentscope.md` and the `talentscope-data/` files. The case study and the six candidate resumes give you everything you need to define the contract and your test cases.

**If Claude Code produces something that looks right but fails on test cases:** Tell it specifically what you expected vs. what you got. "The output says 'recommend' for a candidate who has none of the must-have requirements" is useful. "It's wrong" is not.

**If the output format is inconsistent:** Ask Claude Code to add a strict output format instruction to the system prompt. "Always respond in valid JSON matching this exact schema: [paste schema]."

**If you're spending more than 45 minutes on setup:** Stop and simplify. The tool does not need to be complex. One input, one structured output, running reliably on 3 test cases is a successful Build Lab 1.

**Office hours:** Check the cohort WhatsApp group for times. Bring a specific question, not just "I'm stuck."

---

## Evaluation Criteria

Build Lab 1 is not graded. But when you present it in the hot seat, these are the questions instructors will ask:

- What decision does the AI make? (Not "what does it say" — what specific structured judgment does it produce?)
- What's the input/output contract?
- What test case breaks it? Did you find one?
- How would you know at scale if the outputs are drifting?

If you can answer all four, Build Lab 1 is done well.
