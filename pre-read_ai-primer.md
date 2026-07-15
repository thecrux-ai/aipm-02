# AI Primer for Product Managers

*Pre-read for theCRUX AI for PM Working Sessions — ~35 min*

---

This primer exists for one reason: so you spend the working sessions making product decisions, not getting caught up in terminology. You don't need to know how to build a neural network. You need to know enough to ask the right questions, catch the wrong assumptions, and make calls your engineering team can act on.

Everything here is anchored to **TalentScope**, the AI-powered hiring platform you'll work with across all four sessions. As you read, notice the questions each concept raises for a PM — those are the ones that matter.

---

## Table of Contents

- [Section 1: The AI Landscape](#section-1-the-ai-landscape)
- [Section 2: How Machine Learning Works](#section-2-how-machine-learning-works)
- [Section 3: How Large Language Models Work](#section-3-how-large-language-models-work)
- [Section 4: Data Foundations](#section-4-data-foundations)
- [Section 5: Metrics for AI Products](#section-5-metrics-for-ai-products)
- [Section 6: PM Archetypes](#section-6-pm-archetypes)
- [Section 7: The Five Dimensions of the AI-Native PM](#section-7-the-five-dimensions-of-the-ai-native-pm)
- [Section 8: The PM's Role in AI Products](#section-8-the-pms-role-in-ai-products)
- [Quick Reference: Terms You'll Hear in the Sessions](#quick-reference-terms-youll-hear-in-the-sessions)

---

## Section 1: The AI Landscape

### Three categories worth keeping straight

**Narrow AI** — Systems designed to do one specific task well. A spam filter is narrow AI. A resume screener is narrow AI. TalentScope's hiring pipeline is narrow AI. It looks impressive but is brittle outside its defined task.

**Generative AI (Gen AI)** — Systems that produce new content: text, images, code, audio. Large language models (LLMs) like GPT-4, Claude, and Gemini are Gen AI systems. They don't retrieve answers; they generate them probabilistically. This is why they hallucinate — they're predicting what plausible text looks like, not looking up facts in a database.

**AGI (Artificial General Intelligence)** — A system that can perform any intellectual task a human can. AGI does not exist. When you see "AI" in a headline, assume they mean narrow AI or Gen AI.

*TalentScope uses both.* The resume screener is narrow AI (trained classifier). The candidate summary generator is Gen AI (LLM summarizing structured data). These require different PM skills to spec and evaluate.

---

### AI is not a product

AI is an ingredient. The product is the outcome you deliver to users.

"We built an AI screener" is an engineering description. "We reduced time-to-shortlist from 5 days to 4 hours while improving diverse-candidate pass rates" is a product description.

The PM job is to keep the organization honest about which problem they're actually solving for. Most AI projects fail not because the model underperforms but because the product was never properly defined.

---

### Generalist PM vs. AI PM

A generalist PM asks: *Is this the right product to build?*

An AI PM asks: *Is AI the right solution to this problem — and if so, which kind, under what constraints, with what failure modes?*

This isn't a ranking. It's an additional layer. An AI PM who can't do the first question has a shiny hammer and no nail. An AI PM who skips the second question ships a system they can't evaluate or govern.

The working sessions assume you've already answered "build the product." They teach you how to answer the AI-specific questions.

---

## Section 2: How Machine Learning Works

### What learning means

A traditional software system follows rules you write. If resume contains "5+ years experience," advance to next step. The PM writes the logic; the engineer implements it.

A machine learning system learns the rules from data. You give it thousands of resumes labeled "hired" or "not hired" by humans, and it figures out the pattern. You don't write the rule; you supervise the learning.

This is both more powerful and more dangerous than traditional software. More powerful because it can find patterns in data too complex to hand-code. More dangerous because you can't fully audit what pattern it learned — and it may have learned the wrong one.

*TalentScope implication:* If your training data came from five years of historical hiring decisions, and those decisions had a gender or geography bias, your model learned the bias. The screener's output is only as fair as the decisions it learned from.

---

### Four types of ML

| Type | How it learns | PM use case |
|---|---|---|
| **Supervised learning** | Labeled examples (input → correct answer) | Resume classification (select/reject), spam detection, fraud scoring |
| **Unsupervised learning** | No labels; finds structure in unlabeled data | Candidate clustering, topic modeling in feedback |
| **Reinforcement learning** | Agent learns by trial and error with reward signals | Ranking optimization, recommendation engines |
| **Generative AI** | Learns the distribution of training data to generate new examples | Candidate summaries, job description generation, chatbots |

Most PM-facing AI work at early-stage companies involves supervised learning and Gen AI. You will see all four in large companies.

---

### The training loop

Three phases every ML model goes through:

1. **Training** — The model sees labeled data and adjusts its internal parameters to minimize errors. You don't observe this directly; your data team runs it.

2. **Validation** — The model is tested on data it hasn't seen. This is where you tune hyperparameters (settings that control how the model learns) and catch overfitting.

3. **Testing** — Final evaluation on a completely held-out dataset. This is the number you report as model performance.

**Overfitting** is when a model performs well on training data and poorly on everything else. It memorized the training examples instead of learning a generalizable pattern. An overfit resume screener might reject any candidate who doesn't mention the same specific tools your training set happened to include — even when those tools are irrelevant to the role.

The classic sign: training accuracy is high, test accuracy is low. If your data team shows you only training metrics, ask for validation and test metrics.

---

### Train/validation/test split

The standard split is roughly **70% training / 15% validation / 15% test**, though this varies by dataset size.

**Why this matters to you as a PM:** The test set must be representative of real-world distribution. If TalentScope's test set is 80% resumes from Bengaluru when your actual users submit resumes from across India, your reported accuracy is an optimistic fiction. Ask: "How did we construct the test set, and does it reflect who will actually use this?"

---

## Section 3: How Large Language Models Work

### Tokens, not words

LLMs don't process text as words. They process **tokens** — chunks of characters that are roughly (but not exactly) word-length. "TalentScope" might be one token. "Unrecognizable" might be two or three. Code and non-English text are often less efficient — more tokens per unit of meaning.

Why it matters: you pay for tokens. A cloud API like Claude or GPT-4 bills by tokens in and tokens out. A system that sends 10,000-word resumes to an LLM for every screening request will cost 10–50× more than a system that extracts the five key fields first and sends only those.

*TalentScope implication:* In Session 4, you'll work through the ₹6.8L bill scenario. Token inefficiency is usually the culprit. Knowing what drives token count helps you ask the right questions before the bill arrives.

---

### Context window

The context window is how much text an LLM can "see" at once — its working memory. Current models range from ~8,000 tokens (GPT-3.5) to 200,000+ tokens (Claude 3.5 Sonnet). Everything outside the context window is invisible to the model.

This creates a hard constraint for agentic systems: if your pipeline passes data across multiple steps, each step only sees what you explicitly include in its context. A model analyzing a resume in Step 3 cannot "remember" a candidate's cover letter from Step 1 unless you pass it explicitly.

*TalentScope implication:* If the geography verification agent needs the resume extracted in Step 1, the orchestrator must pass it forward. This isn't automatic. Designing what each agent sees in its context is a PM decision — it affects cost, accuracy, and privacy.

---

### Probabilistic outputs

An LLM doesn't look up answers. It predicts the most plausible next token given everything before it. This means:

- The same prompt can produce different outputs on different runs
- There is no single "correct" answer being retrieved — there is a probability distribution over possible answers
- A highly confident-sounding response can be factually wrong

**Temperature** controls how much the model samples from the probability distribution. Low temperature (near 0) = deterministic, always picks the highest-probability token. High temperature (near 1) = creative, more random.

For structured outputs (classification, extraction), use low temperature. For creative tasks (job description drafting), higher temperature is appropriate.

*PM decision:* "What temperature should our screener use?" is not an engineering call. It depends on whether you want consistent, auditable decisions (low temperature) or diverse, exploratory outputs (higher). The PM sets the requirement; the engineer sets the parameter.

---

### Confidence scores and what they aren't

Many AI systems return a confidence score alongside a prediction: `{"recommendation": "advance", "confidence": 0.83}`.

What this score IS: the model's internal probability estimate for the predicted class. A higher number means the model's output distribution is more concentrated.

What this score IS NOT:
- A measure of whether the output is correct
- A measure of whether the model understood the question
- A measure of factual accuracy

A model can be 0.95 confident about a wrong answer. Confidence measures certainty, not correctness.

*TalentScope implication:* In Session 2, you'll set a Human-in-the-Loop (HITL) threshold — below 0.60 confidence triggers human review. This number comes from you, not the model. Lower threshold = more human reviews = higher cost but fewer missed mistakes. It's a product decision: what is your acceptable error rate, and what does one error cost?

---

## Section 4: Data Foundations

### Structured vs. unstructured data

**Structured data** has a defined schema — rows and columns you can query directly. A SQL table with candidate IDs, years of experience, and job titles is structured data. Traditional ML models (linear regression, decision trees) consume structured data naturally.

**Unstructured data** has no predefined schema. A resume PDF, a cover letter, a voice recording, a performance review. Humans read it; machines need help. LLMs handle unstructured text well. Historically, making unstructured data machine-readable required significant preprocessing.

*TalentScope uses both.* The structured data (years of experience, degree, location) feeds the classifier. The unstructured text (resume narrative, cover letter) gets passed to the LLM for extraction and summarization. A production system often converts unstructured → structured as its first step.

---

### Data quality is a PM problem

"Garbage in, garbage out" is a cliché because it's correct and frequently ignored.

Key data quality dimensions:

| Dimension | Question to ask | TalentScope example |
|---|---|---|
| **Completeness** | Are there gaps that affect prediction? | 30% of resumes have no location listed |
| **Accuracy** | Does it reflect reality? | Candidates exaggerate titles or dates |
| **Consistency** | Is the same thing represented the same way? | "Bengaluru" vs. "Bangalore" vs. "BLR" |
| **Recency** | Is it still true? | Skills from 2018 listed as current |
| **Representativeness** | Does it reflect who will actually use the system? | Training data skewed toward metro candidates |

Data quality decisions are product decisions. If you launch the screener knowing your location data has 30% gaps, you've made a choice about acceptable accuracy. Document it.

---

### Feature engineering

A feature is any input variable you feed to a model. Feature engineering is the process of deciding which inputs to use and how to represent them.

Simple example: "years of experience" is a feature. But raw years of experience is a weak feature — someone with 10 years in retail and 2 years in fintech is very different from someone with 12 years in fintech. A PM who understands the hiring problem would ask: should we create a feature for "relevant years of experience" rather than total years?

You won't engineer features yourself. But you will review whether the features your model uses reflect the actual signals that matter. Bad features = systematically wrong predictions regardless of model quality.

---

## Section 5: Metrics for AI Products

### Why accuracy is the wrong default metric

Accuracy = (correct predictions) / (total predictions)

This sounds right. It is often misleading.

Suppose TalentScope screens 1,000 resumes. 50 candidates are strong hires. 950 are not. A model that rejects everyone achieves 95% accuracy — and surfaces zero hires. This is the **class imbalance problem**: when one outcome is rare, accuracy inflates by getting the common case right.

For hiring, medical screening, fraud detection, and most consequential AI applications, accuracy is the wrong primary metric.

---

### Precision, recall, and the trade-off

Two metrics that actually matter:

**Precision:** Of the candidates you advanced, what fraction were actually qualified?
`Precision = True Positives / (True Positives + False Positives)`

High precision = few false positives = your shortlist is clean but potentially short.

**Recall:** Of all qualified candidates, what fraction did you surface?
`Recall = True Positives / (True Positives + False Negatives)`

High recall = few misses = you surface most of the good candidates but your shortlist has more noise.

**The trade-off:** You cannot maximize both simultaneously. Improving precision typically lowers recall and vice versa. The right balance is a product decision, not a model decision.

*TalentScope example:* For an enterprise customer hiring for a compliance role where a bad hire is catastrophic — optimize for precision (tight shortlist, few surprises). For a startup trying to cast a wide net for a generalist role — optimize for recall (don't miss good candidates, filter later). The PM sets the target; the data team tunes the threshold.

---

### F1 score

When you need a single number that balances precision and recall:

`F1 = 2 × (Precision × Recall) / (Precision + Recall)`

F1 is the harmonic mean — it penalizes extreme imbalance. A model with 0.95 precision and 0.10 recall has a poor F1 despite its high precision. F1 is useful when you want both to be reasonably good, not when you've explicitly decided to prioritize one.

---

### The eval framework for AI PM decisions

When evaluating any AI model, ask these five questions in order:

1. **What metric did we optimize for during training?** (Not always what you should optimize for in production)
2. **Is that metric the right proxy for user value?** (Accuracy vs. precision/recall; AUC vs. business outcome)
3. **How was the test set constructed?** (Representative? Recent? Held out properly?)
4. **What are the failure modes for the metrics we're NOT tracking?** (Optimizing precision; did recall collapse?)
5. **How will this metric change when the input distribution shifts?** (New industries, new resume formats, new geography)

You don't need to run the models. You need to ask these questions in every model review.

---

## Section 6: PM Archetypes

What you're building and how you work are two different axes — and confusing them is one of the most common mistakes in AI product teams.

**AI-Assisted PM**
A traditional PM who has added AI tools to their workflow. They use Copilot, ChatGPT for first drafts, Claude for research synthesis. Their process is largely unchanged — AI is a productivity layer on top of how they already worked. Most PMs in the market today sit here.

**AI Product Manager**
A PM who builds AI-powered products. They spec LLM features and recommendation/ranking engines, write eval criteria, make build/buy decisions about foundation models, and own the quality of AI outputs in production. What they ship is an AI system, not just a product that uses AI.

**AI-Native PM**
A PM whose entire operating model — discovery, strategy, decisions, execution — is rebuilt around AI. AI is not a tool they use. It's the medium they work in. Their thinking process, research process, spec process, and decision-making process all run through AI.

One distinction worth making explicit: the AI-native PM may or may not be building AI products. The distinction is **how they work**, not what they ship. An AI-native PM building a logistics product is still an AI-native PM. An AI product manager who only uses AI to clean up their meeting notes is not.

This taxonomy tells you where you sit today. Section 7 describes what you're developing toward.

> *AI-assisted PMs will be outcompeted by AI product managers who can spec and govern what gets shipped. AI product managers who rebuild how they work — not just what they build — will have the leverage that compounds.*

---

## Section 7: The Five Dimensions of the AI-Native PM

Being an AI PM isn't a job title — it's a way of working. Here are the five dimensions that separate an AI-native PM from a traditional PM who has learned some AI vocabulary.

---

### 1. Mindset: Probabilistic, Not Deterministic

Traditional PMs think in fixed logic: if user does X, show Y. The system does exactly what it's told.

AI-native PMs think in systems: if user does X, the system predicts the best Y — and it won't always be right. They are comfortable shipping incomplete systems that improve over time. They design for iteration, not perfection. They measure model accuracy alongside engagement metrics.

> *"Traditional PMs guide deterministic systems. AI PMs guide probabilistic ones."* — Aakash Gupta

*TalentScope implication:* Version 1 of the resume screener will make mistakes. An AI-native PM ships it knowing that, designs the HITL layer for the mistakes they can predict, and builds the eval suite to catch the ones they can't.

---

### 2. Technical Fluency — Enough to Belong in the Room

They don't code. But they can't be technically naive either. An AI-native PM knows:

- How models are trained and what makes them drift
- The precision/recall trade-off — and which one their product should optimize for
- What an eval is, how to write one, and what "vibes-based testing" costs in production
- What RAG, fine-tuning, and agents mean — well enough to make scoping decisions
- Token economics: what it costs to run their feature at scale

The bar is not engineering fluency. The bar is **not getting fooled** — by engineers, vendors, or their own assumptions about what AI can do.

> *"No Vibes, Just Evals: Proven Frameworks for AI-Native PMs"* — Maven AI-Native PM (Lenny)

---

### 3. Data as Infrastructure, Not Input

A traditional PM uses data to measure outcomes after the fact.

An AI-native PM designs products to generate the data that improves the system going forward. They think about:

- What data does this feature need to work? Where does it come from?
- What feedback loop am I building into this product?
- What is my AI flywheel — and can I draw it on a whiteboard?

> *"If you can't articulate your product's AI flywheel on a whiteboard, you don't have an AI strategy. You have a feature."* — Aakash Gupta

*TalentScope implication:* Every recruiter decision — advance, hold, reject — is a labeled data point that can retrain the next version of the screener. Is TalentScope capturing those decisions? In what format? With what latency? If not, you're leaving model improvement on the table.

---

### 4. Outcome Orientation — Ships Results, Not Features

An AI-native PM tracks model improvement and user trust, not velocity or feature count.

They think in **minimum viable models**, not minimum viable products. They know that v1 of an AI feature is a hypothesis about what the model will learn — not a finished product. Shipping is the beginning of the feedback loop, not the end of the build.

This reframes the launch milestone entirely. A traditional PM celebrates shipping. An AI PM treats shipping as the moment the real experiment begins.

---

### 5. AI as Cognitive Amplifier — In How They Work

This is what separates the truly AI-native PM from the AI-assisted one. They have rebuilt how they do their own job:

| PM task | AI-native approach |
|---|---|
| **Discovery** | AI-assisted interview synthesis, pattern detection, competitive signal scanning |
| **PRDs** | AI-generated first drafts with human judgment at the strategic layer |
| **Roadmapping** | Scenario modeling, trade-off analysis, stakeholder narrative — all AI-augmented |
| **Decisions** | Data-to-decisions workflows, not gut feel plus a chart |

The AI-native PM doesn't use AI to go faster. They use it to think more rigorously — to surface options they'd miss, stress-test assumptions they'd otherwise accept, and spend their human judgment on the decisions that actually require it.

---

## Section 8: The PM's Role in AI Products

### The AI product lifecycle

AI products have a lifecycle that doesn't match traditional product development:

```
Problem Definition
       ↓
Data Assessment (Do we have the data? Is it representative?)
       ↓
Model Selection (Build, buy, fine-tune, or prompt an existing model?)
       ↓
Spec & Evaluation Design (What does "good" look like? Write this before you see model outputs)
       ↓
Build + Iterate
       ↓
Deployment + Monitoring (Not launch-and-leave; models degrade as the world changes)
       ↓
Governance + Incident Response (Bias complaints, unexpected outputs, regulatory requirements)
```

Two stages where PM ownership is non-negotiable: **Evaluation Design** and **Governance**. Engineers can build the model. Only the PM can define what success looks like and what the acceptable failure modes are.

---

### The ML lifecycle vs. the product lifecycle

The ML lifecycle runs inside the product lifecycle. Understanding the hand-offs prevents the most common PM mistake — treating model training as equivalent to feature development.

| Phase | What engineers are doing | PM's job during this phase |
|---|---|---|
| **Data collection & labeling** | Gathering, cleaning, annotating training data | Define labeling criteria; review sample labels for quality |
| **Model training** | Running experiments, comparing architectures | Align on eval criteria before results come in |
| **Model evaluation** | Running test set metrics | Interrogate the metrics (see Section 5) |
| **Deployment** | Serving the model in production | Define monitoring thresholds; write incident playbook |
| **Monitoring** | Watching for drift and degradation | Set the alert thresholds; decide when to retrigger training |

The PM isn't in every step, but the PM owns the decisions at each hand-off point.

---

### PM decision ownership

Decisions that are explicitly yours as an AI PM:

**Before build:**
- Is AI the right solution, or would a rule-based system solve 80% of this at 10% of the cost?
- Build, buy, or fine-tune an existing model?
- What data do we need, and do we have it?

**During build:**
- What are the acceptance criteria for model performance? (Write these before you see results)
- What failure modes are we explicitly designing against?
- What is our HITL strategy — when does a human review, and what do they see?

**At launch:**
- What monitoring tells us the model is degrading in production?
- What triggers a rollback?
- What is our incident response plan for a bias complaint?

**Post-launch:**
- When does the model need to be retrained?
- Who is responsible for the eval suite as inputs shift?
- How do we audit the system for regulatory compliance?

---

### The skills ladder for AI PMs

Three levels of AI PM capability, adapted from Meta's practice:

**Good — knows what AI can do**
Understands the landscape. Can identify where AI adds value in a product. Asks the right questions in model reviews. Can write a PRD that includes data requirements and success criteria.

**Great — can spec, evaluate, and govern AI systems**
Writes eval suites before seeing model outputs. Designs HITL workflows. Sets monitoring thresholds. Writes a governance spec that would pass legal review. Can debug a production AI failure by asking the right questions.

**Rockstar — drives AI strategy and org design**
Makes build/buy/partner decisions with financial modeling. Presents AI investment cases to executive stakeholders. Designs the eval culture and incident response process for their team. Can identify when the organization's AI confidence is ahead of its actual capability.

The working sessions are designed to move you from Good toward Great. Session 4 gives you the first tools for Rockstar.

---

## Quick Reference: Terms You'll Hear in the Sessions

| Term | One-line definition |
|---|---|
| **Token** | Unit of text an LLM processes; roughly ~0.75 words on average |
| **Context window** | How much text an LLM can see at once; sets memory limits for agents |
| **Temperature** | Controls output randomness; 0 = deterministic, 1 = creative |
| **Overfitting** | Model performs well on training data but poorly on new data |
| **Precision** | Of predictions made, what fraction were correct |
| **Recall** | Of all correct answers, what fraction were predicted |
| **F1** | Harmonic mean of precision and recall |
| **HITL** | Human-in-the-Loop; when a human reviews before action is taken |
| **Confidence score** | Model's internal probability estimate; high confidence ≠ correct |
| **Hallucination** | When an LLM generates plausible but factually wrong output |
| **Prompt injection** | Malicious input designed to override an LLM's instructions |
| **Eval suite** | Collection of test cases used to systematically evaluate model quality |
| **Model drift** | Model performance degrades over time as real-world inputs shift |
| **Feature** | An input variable fed to a model for prediction |
| **Training data** | Labeled examples used to teach the model |
| **Supervised learning** | ML with labeled training examples |
| **Generative AI** | AI that produces new content (text, images, code) |

---

*You're ready to start Session 1.*

*What you need to bring: curiosity about the failure modes, not just the capabilities. The most important question a PM asks about any AI system is not "what can it do?" but "what does it do when it's wrong?"*
