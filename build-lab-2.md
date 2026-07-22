# Build Lab 2 — Build and Run an Agentic System

**After Session 2 · Due before Session 3 (July 29) · 2–3 hours**

In Session 2 you built a **pipeline** — three fixed steps, hardcoded order, no external tools. It caught the bugs Build Lab 1's single call missed, because Step 2 could say *CANNOT_VERIFY* instead of guessing.

Build Lab 2 is **the crossing.** Your system takes an input, *decides* what to do, calls something, observes the result, and decides what to do next. At least two sequential decisions. At least one external tool. At least one failure mode handled gracefully.

This is a real architectural step — and a real increase in failure surface. Take it deliberately.

---

## What You're Building

An agentic system that:
- Makes at least **two sequential decisions**, the second depending on the result of the first
- Calls at least **one external tool** (web search, a file read, a mock API, a database lookup — anything outside the model itself)
- Handles at least **one failure mode gracefully** — produces a useful response when something goes wrong, not a crash or empty output

"External tool" is deliberately broad: reading a file from disk, calling a weather API, a hardcoded dictionary lookup that simulates a database, Claude's web search via the API, reading a webpage. The point is the agent's next step depends on information it *retrieves*, not just information in the original input.

---

## TalentScope Interview Agent

Build the TalentScope async interview agent. This is the genuine multi-step agentic flow Session 2 gestured at — and you have real material to build from: `talentscope-data/interview-transcript-priya.txt` and `interview-transcript-vikram.txt`.

**What it does:** conducts a structured text-based interview.
1. Reads the job description and the candidate's resume
2. Generates an opening question relevant to the candidate's background and the role
3. Receives the candidate's answer
4. *Decides:* is this answer complete, or does it need a follow-up?
5. If a follow-up is needed: generates a specific one, grounded in the answer just given
6. Continues until it has enough — or hits the exchange cap
7. Produces a structured interview summary

This is a real agentic loop: question 2 depends on what the candidate answered in question 1. The model is making a sequential decision based on its observation.

**Constraints:**
- Maximum 8 exchanges per interview
- Must terminate early if the candidate signals they want to stop
- Cannot ask the same question twice
- Must produce a summary even if the interview is incomplete

---

## Before You Open Claude Code

Do this first, on paper or in a notes doc. (Same discipline as Build Lab 1 — the pre-work is what makes the build fast.)

**Step 1: Draw the agent loop**
- What is the initial input?
- What does the agent decide *first*?
- What does it call / retrieve?
- What does it do with that information?
- What triggers the next step?
- What triggers termination?
- What is the final output?

If you can't draw it in five minutes, you're over-complicating the architecture.

**Step 2: List your tools**
For each external tool: what does it do, what data does it access, and what happens if it fails?

**Step 3: Write one failure-mode scenario**
Pick the most likely failure. Write: *"When [specific input condition], the agent will [fail this way]. Instead, it should [desired behaviour]."* This becomes your first test case for Build Lab 3.

---

## Building It

### Using the Anthropic API (required)

Build Lab 2 runs on the **Anthropic API**, not the `claude` CLI — same switch you made for Build Lab 1. You need API credits: add at least $5 before starting.

```bash
export ANTHROPIC_API_KEY=your_key_here
```

Get your key from console.anthropic.com. It stays server-side — never in the browser, never checked into git.

### Starting the build

Work in a project under your PM Track directory:

```bash
cd ~/thecrux-pmtrack
mkdir interview-agent && cd interview-agent
git init
claude
```

(Use whatever folder name fits — `interview-agent` works fine.)

Describe to Claude Code:
1. The agent's goal and the loop you drew in your pre-work
2. The tools it needs to call
3. The failure mode it must handle
4. Your preferred stack — a Python script, a Node script, or HTML + JS with API calls (whatever you deployed in Build Lab 1 is fine)

**Do not ask Claude Code to build the whole agent in one prompt.** Break it:
1. First prompt: the agent loop with one tool call and **no** failure handling
2. Second prompt: add failure handling for the case you identified in pre-work
3. Third prompt: test with an adversarial input and fix what breaks

### Deploy (required)

Like Build Lab 1, the deliverable is a **live URL.** Commit your files (confirm `.gitignore` lists `node_modules` and `.env`), deploy to Vercel, add `ANTHROPIC_API_KEY` in **Settings → Environment Variables**, redeploy, and verify end to end.

---

## What to Test

After the basic build works:
1. **The happy path** — correct output for a straightforward input
2. **The failure mode you identified** — does it handle it gracefully?
3. **An unexpected input** — something you didn't design for. What happens?

Test with `candidate-d-rahul.txt` — the prompt-injection resume. Does the agent follow the injected instructions, or ignore them and stay on task? This is the sharpest test of whether your guardrails are real.

Also test against the two interview transcripts in `talentscope-data/` — feed the agent Priya's or Vikram's resume and play the candidate, then compare the interview your agent runs to the real transcript.

---

## What to Bring to Session 3

1. **Your agent running** — be ready to demo one complete end-to-end flow
2. **Your agent architecture diagram** — the five-element format from Session 2: orchestrator, tools, memory, failure modes, HITL triggers
3. **One failure mode you discovered** that you did not predict before building — this is your first eval case for Build Lab 3
4. **Your answer to this question:** *"What is the worst thing that could happen if this agent gets a malicious input?"*

---

## Getting Unstuck

**If the agent loop isn't working:** start with two sequential model calls — no loops, no tools. One call generates something; the second uses that output to generate the next thing. Get *that* working before adding complexity. (You already built exactly this in Session 2 — a three-step pipeline. Start from there and add the deciding + tool-use.)

**If tool calls are failing:** simplify the tool. Instead of a real API, use a hardcoded dictionary lookup that returns what the API would. Get the agent logic right, then swap in the real tool.

**If the output is inconsistent:** add a strict output-format instruction (a JSON schema) to every model call in the loop. If each step returns structured JSON, the agent can't drift.

**If you're unsure which tools to use:** for the interview agent, you don't need external tools at all — the "tool" is the conversation history. The agent uses the transcript of previous exchanges to decide what to ask next. Start there.

**If this is taking longer than 90 minutes:** step back and simplify. An agent that makes two sequential model calls and handles one failure case is a complete Build Lab 2. Don't add features — add robustness.

---

## Evaluation Criteria

Build Lab 2 is not graded. But in the hot seat at Session 3, these are the questions instructors will ask:

- Walk me through one complete end-to-end flow. What decisions is the agent *making* — and where does the path depend on what it observed?
- What external tool does it call? What data does it access?
- Show me what happens with [a specific edge case — instructors will name one].
- What's the most likely way this agent **fails silently** — wrong output, no error?
- How did you constrain it so it can't run forever, call tools endlessly, or take an action you didn't intend?

If the agent handles an input you didn't design for gracefully — not a crash, a meaningful fallback — that's a strong Build Lab 2.

---

## A Note Before You Start

Session 2 made one point repeatedly: **the crossing from pipeline to agent is not a capability upgrade you get for free.** You're about to feel that firsthand. Your agent will be more capable than your pipeline — and it will do things you didn't predict, some of them wrong.

That unpredictability is the entire reason Build Lab 3 exists. Every surprising failure you hit this week is material for next week: that's what evals are for.
