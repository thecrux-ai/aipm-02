# TalentScope — Beta Customer Feedback

**Context:** TalentScope ran a closed beta with 6 enterprise customers over 8 weeks (Feb–Apr 2026). This is a compilation of support tickets, NPS verbatims, and PM interview notes from that beta period. The PM team uses this to inform the v1 product roadmap.

---

## Support Tickets (Zendesk export, beta period)

**Ticket #001**
**Customer:** Bharat Forge (Anita Verma)
**Date:** Feb 14
**Category:** Output quality
**Description:** The screener recommended a candidate for a QA Engineer role who had zero manufacturing experience. When I looked at the resume, the AI had interpreted "quality assurance" on a software testing resume as equivalent to "quality control" in a manufacturing context. These are completely different jobs. We almost sent this person to the hiring manager — caught it manually before it went out.
**Resolution:** Escalated to product team. No fix available in beta.

---

**Ticket #002**
**Customer:** Sundaram Finance (Rajesh Menon)
**Date:** Feb 19
**Category:** Missing criterion
**Description:** We gave the screener our job requirements for a Relationship Manager role. It's screening fine for experience, but it's not checking geography. We specified "must have worked in Chennai zone" but candidates from Trichy, Madurai, and even Bengaluru are being recommended as "good fit." This is a dealbreaker for us — we explained in the kickoff call that geography is our #2 criterion after experience.
**Resolution:** Asked customer to add geography requirement in bold at the top of JD. Partial workaround only.

---

**Ticket #003**
**Customer:** Indiabulls Housing Finance (Sanjay Tripathi)
**Date:** Feb 22
**Category:** Explanation quality
**Description:** The rejection reason for one of our candidates says "insufficient relevant experience." That's it. That's the entire reason. I need to be able to defend every rejection if a candidate calls to ask why. "Insufficient relevant experience" tells me nothing specific. What experience? Compared to what requirement? I need the AI to cite the specific gap.
**Resolution:** Product team updated the prompt to require specific evidence. Deployed in v0.3.

---

**Ticket #004**
**Customer:** Marico (Pooja Kulkarni)
**Date:** Feb 28
**Category:** College bias concern
**Description:** We ran the screener on 200 MBA fresher applications. The shortlist it produced has a suspiciously high proportion of IIM/XLRI/SPJIMR candidates and very few from Tier 2 colleges — even for profiles where the Tier 2 candidate has demonstrably stronger project work and internship outcomes. We specifically told the screener to focus on substance over institution. Either it's not doing that or it's defining "substance" in a way that correlates with college tier. This needs investigation.
**Resolution:** Known issue. Model trained on data where top-college candidates were more often shortlisted historically. Working on prompt mitigation.

---

**Ticket #005**
**Customer:** Bharat Forge (Anita Verma)
**Date:** Mar 5
**Category:** Speed issue
**Description:** The screener takes 45-55 seconds per resume. We have 400 applications. That's 5-6 hours of processing time. We expected near-instant results. Can you batch-process?
**Resolution:** Explained that each resume requires a full API call. Offered priority queue for large batches. Customer not satisfied.

---

**Ticket #006**
**Customer:** Kirloskar Electric (HR Team)
**Date:** Mar 11
**Category:** Feature request
**Description:** We want the screener to flag when a candidate's resume might have been AI-generated or heavily embellished. We've started seeing resumes that list impressive-sounding projects but when you probe in the interview, the candidate clearly didn't do what they claimed. Is there a way to flag "resume authenticity risk"?
**Resolution:** Feature request logged. Not in roadmap.

---

**Ticket #007**
**Customer:** Sundaram Finance (Rajesh Menon)
**Date:** Mar 15
**Category:** Workflow integration
**Description:** The screener outputs a JSON file. My HR team doesn't know what to do with a JSON file. Can we get the output as a formatted PDF or at least an Excel sheet? The team is using WhatsApp to share the results because the JSON format is unusable for them.
**Resolution:** Added PDF export in v0.4. Excel pending.

---

**Ticket #008**
**Customer:** Indiabulls Housing Finance (Sanjay Tripathi)
**Date:** Mar 20
**Category:** Confidence scoring
**Description:** Two candidates got the same confidence score (0.72) for a senior role. One had 8 years of exactly relevant experience and one had 3 years of partially relevant experience. They should not have the same score. I don't know if this is a calibration problem or if I'm misunderstanding what the confidence score means.
**Resolution:** Explained confidence score is the model's certainty about its recommendation, not the candidate's qualification level. Customer found this confusing — suggested we rename or redesign the metric.

---

**Ticket #009**
**Customer:** Marico (Pooja Kulkarni)
**Date:** Apr 1
**Category:** Language support
**Description:** We occasionally receive resumes in regional languages — Tamil, Kannada, Hindi. The screener is producing garbage output for Hindi resumes (it seems to be trying to translate and then evaluate, but the translation is poor). Tamil resumes are rejected immediately with "unable to process." This is a problem for us because some of our best candidates in south India submit in Tamil.
**Resolution:** Escalated. Beta version only processes English resumes reliably. Multi-language is post-GA.

---

**Ticket #010**
**Customer:** Kirloskar Electric (HR Team)
**Date:** Apr 8
**Category:** False negative
**Description:** One of our hiring managers recognised a rejected candidate as someone he knew from the industry — very strong performer, 15 years of relevant experience. The screener rejected because the person's resume didn't use our standard job title terminology. Our JD says "Senior Mechanical Design Engineer" — the candidate's resume says "Lead Product Design Engineer, Mechanical." These are the same thing. The screener treated the different title as a mismatch.
**Resolution:** Known limitation. Semantic matching for job titles is being improved.

---

## NPS Survey Verbatims (End of Beta)

**Promoters (score 9-10):**

"The specificity of the rejection reasons is 10x better than what I could get from a junior HR person doing manual screening. When it says 'candidate has 2 years of loan sales experience but your requirement specifies 3+ years — 1-year gap on a hard requirement,' that's usable. That's what I can defend." — Sundaram Finance

"It processed 380 resumes in 6 hours while my team was doing other work. Even with all the issues, the time saving is real. The quality issues are fixable. The time we got back is not going back." — Bharat Forge

**Detractors (score 1-6):**

"The college bias issue is a dealbreaker for us. We specifically chose TalentScope because we wanted to screen on merit, not credentials. If the AI has the same bias as the market, what's the point?" — Marico

"My team spent more time reviewing the AI's outputs than they would have spent doing the screening manually. The confidence scores are confusing, the reasons are sometimes wrong, and they have to check every recommendation before they'd trust it. That's more work, not less." — (Anonymous, Tier 2 NBFC)

**Passive (score 7-8):**

"Good start. Fix the geography problem, fix the multilingual problem, fix the output format, and we'll be happy customers. The core screening logic is directionally right." — Indiabulls Housing Finance

"It works for the easy cases. What we really need it for is the hard cases — the ones my team would debate for 20 minutes. For those, it's not better than a experienced recruiter. Not yet." — Kirloskar Electric

---

## PM Interview Notes (Customer Discovery, Post-Beta)

**What would make you increase usage from "evaluating" to "core workflow"?**

- Anita (Bharat Forge): "If it understood industry context — manufacturing vs. IT vs. financial services — and calibrated its evaluation accordingly. Right now it feels generic."
- Rajesh (Sundaram Finance): "If it could handle my full requirements list, including geography, without me having to hack the JD to make it work."
- Pooja (Marico): "If I could train it on our culture and values — not just job requirements. I want it to know what 'Marico quality' looks like in a resume."

**What would make you stop using it?**

- All three: "One very visible wrong recommendation that reaches the hiring manager." (Trust is fragile — one failure in front of the business ends adoption.)
- Rajesh: "Any evidence that it's discriminating on factors I didn't specify — gender, religion, caste, anything like that."
- Anita: "If the team feels like the AI is replacing their judgment rather than supporting it. They need to feel like they're still in control."

---

## Key Themes (for PM analysis)

Patterns to extract:
1. What are the top 3 product gaps the beta surfaced?
2. What are the trust-breaking scenarios that would kill adoption?
3. Which features are must-haves for GA vs. nice-to-haves post-GA?
4. What governance requirements are hidden in these tickets?
5. What does the ideal HITL design look like based on what customers said?
