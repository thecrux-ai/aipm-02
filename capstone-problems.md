# Capstone Case Studies

**11 business problems across Indian and US companies. Pick one, design and build an AI system that solves it, and demo it on August 15.**

These are not "AI feature" prompts. They are business problems — messy, multi-step, real. Most of them cannot be solved by a single LLM call. Some require agents working in loops. Some require deterministic programs working alongside AI. Some require multiple agents with different responsibilities coordinating with each other. Some require human-in-the-loop at specific decision points.

Your job as a PM is to figure out the architecture. That means deciding:

- Which parts of the problem should be solved by deterministic code (rules, calculations, database queries) and which parts need an agent
- Where the loops are — what does the system try, evaluate, and retry?
- Where the human needs to be in the loop — and when the system should proceed autonomously
- What agents, if more than one, need to exist — and how they hand off to each other
- What the failure modes are and how to handle them

**There is no prescribed solution.** Two students picking the same case study should build different things — and both can be right.

---

## How to Choose

Pick the case study where:

1. **You understand the business deeply enough to make opinionated architecture decisions** — you can't design what you don't understand
2. **The failure modes are interesting to you** — every one of these problems has ways to go wrong that aren't obvious. Pick the problem where you find yourself wanting to solve that.
3. **You have a strong point of view on the governance question** — don't pick a case study where your answer to the hard question is "it depends"

---

## Case Study 1 — Swiggy: Restaurant Turnaround

### The business problem

Swiggy has tens of thousands of restaurant partners. Some of them are declining — ratings dropping, order volumes falling, customer complaints increasing. When a restaurant's rating falls below a threshold or its complaint rate spikes, Swiggy currently does nothing systematic. An account manager might eventually notice. Or the restaurant churns.

Swiggy wants a system that detects restaurants heading for decline before they get there, understands why they're declining, and drives recovery — without a human account manager involved in every case. There are too many restaurants for human review at scale.

The problem is harder than it looks. A restaurant declining because the chef left is a different problem from one declining because of packaging quality, which is different from one declining because a competitor opened next door, which is different from one declining because of a single viral negative review that doesn't reflect their actual quality. The right intervention for each is completely different. An intervention designed for one situation applied to another makes things worse — a restaurant wrongly told their food quality is the problem, when the real issue is delivery time, loses trust in Swiggy's feedback and ignores all future communication.

The data exists: customer reviews, order data, prep time logs, delivery time logs, item-level complaint data, refund patterns, and Swiggy's own internal operational data. But the data is in different places, has different latency, and tells different parts of the story.

### Why a single AI call won't solve this

Detecting decline requires pattern recognition across time-series data. Understanding why requires synthesizing multiple data sources and reasoning about which explanation fits the evidence. Designing the right intervention requires judgment about what's actually fixable. Executing the intervention requires producing the right communication in the right format for the right channel. Monitoring whether the intervention worked requires checking again later and deciding whether to escalate or change approach.

This is a process, not a prompt.

### What good looks like

Swiggy's account management team receives a ranked list of at-risk restaurants each week — with a diagnosis per restaurant and a recommended intervention already drafted. Restaurants that receive an intervention from this system show measurably better recovery rates than restaurants that receive no intervention. The system doesn't cry wolf: a 15% false positive rate means 15 out of 100 restaurants flagged as at-risk are actually fine, and those restaurants learn to ignore Swiggy's outreach.

### The hard question

The system will sometimes diagnose a restaurant incorrectly — and an incorrect diagnosis followed by an incorrect intervention can make things worse. Who in Swiggy's org is accountable for a wrong intervention? Is there a review step before any intervention is sent to a restaurant? What does that review process look like at scale?

---

## Case Study 2 — Zomato: Hyperlocal Supply Intelligence

### The business problem

Zomato's supply team acquires new restaurant partners — they find restaurants that should be on Zomato but aren't, convince them to join, and help them get set up. The acquisition is done by a field sales team organized by city and zone.

The problem: the field sales team operates largely on intuition. They go to neighborhoods they know, talk to restaurants they've heard of, and pitch Zomato's value proposition. They have no systematic way to know which cuisines and price points are undersupplied in which pin codes, which unpartnered restaurants are most likely to fill a genuine gap in Zomato's supply, or how to prioritize their time across a city with thousands of potential acquisition targets.

Meanwhile, Zomato's own platform is generating constant demand signals: what users are searching for and not finding, what categories are failing to fulfill demand, where users are ordering from outside their neighborhood because local supply is weak. This signal exists but isn't reaching the field sales team in a usable form.

The supply gap is also dynamic. A new residential development opens in Whitefield — suddenly there's demand for Rs. 200-400 lunch options and the supply is all Rs. 600+ restaurants and dhabas. The window to capture this is 4-6 weeks before a competitor does. The current system doesn't move that fast.

### Why a single AI call won't solve this

Identifying a supply gap requires querying demand data (what are people searching for?), querying supply data (what restaurants exist in this pin code and what do they offer?), comparing the two, and determining what's genuinely missing vs. what's just not popular. Then it requires finding unpartnered restaurants that could fill the gap — which means searching external data sources, assessing quality signals, and ranking acquisition targets. Then it requires packaging this into a briefing a field salesperson can act on in the next hour.

Each of these is a different task with different data sources, different reasoning requirements, and different output formats.

### What good looks like

The field sales team starts each day with a prioritized list of restaurants to approach — by area, by category — with a one-paragraph brief on why this restaurant matters for this pin code's supply gap. The brief is specific enough that the salesperson can walk into the conversation with context. The list updates daily as Zomato's demand data changes.

### The hard question

Zomato's supply intelligence system will inevitably favor certain cuisines, price points, and neighborhoods over others — based on what the data shows is undersupplied. What does this mean for restaurant diversity? Could a system optimizing for gap-filling systematically under-represent cuisines with lower search volume (which may correlate with lower-income users)? Who at Zomato is responsible for auditing this?

---

## Case Study 3 — CRED: Credit Deterioration Early Warning

### The business problem

CRED can see financial health signals that most members can't: credit score trends from bureau data, payment timing patterns across credit cards, utilization trends across all cards a member has linked, and new hard inquiry signals. CRED's data advantage is real — but they're not using it to help members.

The problem CRED wants to solve: when a member's financial health starts deteriorating — utilization creeping up month-over-month, payment timing getting later, new hard inquiries appearing — CRED wants to intervene 60-90 days before the member reaches distress, not after. By the time a member misses a payment, a coaching intervention is too late to prevent the score damage. But 90 days earlier, the right intervention might change the trajectory.

What makes this hard is that "deterioration" looks different for different members. A member who always pays on day 25 of a 30-day cycle paying on day 28 is a different signal than a member who always pays on day 5 suddenly paying on day 25. The right intervention for a member who is spending more because of a temporary life event (job loss, medical expense) is completely different from a member who has let lifestyle creep push their utilization above 80%. And the wrong intervention — alarming a member about "credit risk" when they're actually financially fine — destroys trust and trains members to ignore CRED's communications.

The system also has to coordinate with CRED's lending marketplace: a member who is heading toward distress might benefit from a debt consolidation offer, but showing a consolidation ad to a member who is fine looks predatory.

### Why a single AI call won't solve this

Detecting early warning signals requires monitoring data over time and applying rules (some of which are deterministic thresholds, some of which require pattern inference). Deciding whether a signal is genuine vs. noise requires contextual reasoning. Designing the right intervention requires personalization to the member's specific situation. Executing the intervention requires choosing the right message, the right channel, and the right timing. Monitoring whether the intervention worked requires checking back 30 days later and adjusting.

Some of this is rules. Some is AI. The hard part is knowing which is which.

### What good looks like

Members who receive a proactive CRED intervention at the early warning stage show measurably better credit trajectory 90 days later than members who don't. The system doesn't over-fire: members who are flagged but turn out to be fine don't increase their churn rate as a result of the intervention. CRED's lending offers, when shown, are shown to members for whom they are genuinely beneficial.

### The hard question

CRED earns revenue when members use its lending marketplace. If the early warning system is designed to intervene with members who are financially vulnerable, and CRED's intervention includes lending product recommendations, is CRED helping members or exploiting their vulnerability? How do you design this system so that the commercial incentive doesn't distort the intervention?

---

## Case Study 4 — Razorpay: Integration Health Monitor

### The business problem

When a merchant integrates Razorpay, there is a critical window — the first 30 days of going live — where integration bugs cause real transaction failures that cost the merchant money and erode their trust in Razorpay. A merchant who experiences 10%+ payment failure in their first month almost never recovers as a Razorpay customer: they switch to a competitor or switch back to offline payments.

The problem is that merchants don't know when they have an integration bug. They assume failed transactions are normal. They don't know that failed transactions have root causes — webhook misconfiguration, incorrect error handling, payment method not enabled, currency settings wrong — that can be fixed. By the time the merchant realizes something is wrong, they've already lost thousands of rupees in failed transactions and their trust is gone.

Razorpay's developer support team handles inbound tickets reactively. By the time a ticket arrives, the problem has been happening for days. And most integration issues don't generate tickets at all — the merchant just sees lower revenue than expected and assumes it's demand, not their payment integration.

What Razorpay actually wants: a system that monitors every new merchant's integration health in the first 30 days, detects anomalies in transaction patterns that suggest integration problems, diagnoses the root cause automatically where possible, and intervenes with the merchant — proactively, before the merchant even knows they have a problem — with a specific fix.

### Why a single AI call won't solve this

Monitoring requires watching transaction data over time and applying anomaly detection (partially deterministic: "this merchant's failure rate is 3x the category average"). Diagnosing root cause requires correlating multiple signals: what type of failures (API error code), at what stage (checkout, payment confirmation, webhook), with what patterns (only on UPI? only on mobile? only above a certain transaction value?). Generating the fix requires looking up the specific integration issue in Razorpay's documentation and generating a specific, actionable instruction for this merchant's apparent tech stack. Following up requires checking whether the failure rate improved after the merchant made changes.

The merchant's experience of this should feel like Razorpay is watching out for them — not like they received a generic support ticket.

### What good looks like

Merchants in their first 30 days receive a proactive outreach from Razorpay when their integration health degrades, with a specific diagnosis of what's wrong and what to do about it. Merchants who receive this outreach have measurably lower 30-day churn than merchants who don't. The diagnosis is accurate enough that merchants act on it: a 30% false diagnosis rate (where the stated cause isn't the real cause) is too high.

### The hard question

Razorpay's integration health monitor will sometimes get the diagnosis wrong — and a merchant who acts on a wrong diagnosis may make their integration worse. When the system proactively contacts a merchant with a diagnosis, is Razorpay liable if the diagnosis is incorrect and the merchant loses transactions as a result of following the advice? What does the system need to say, and how does it need to be structured, to give Razorpay appropriate legal cover without making the outreach useless?

---

## Case Study 5 — ClearTax: SMB Compliance Risk Audit

### The business problem

India's Income Tax Department has substantially improved its data matching capabilities. The department now automatically cross-references GST filings (GSTR-1, GSTR-3B) with income tax returns (ITR) and sends automated notices to businesses where the numbers don't match. ClearTax has 2 million SMB customers. A significant fraction of these businesses have mismatches in their filings — not because they're evading taxes, but because GST accounting and income tax accounting categorize certain transactions differently, and the reconciliation is genuinely complex.

When a business receives a notice from the IT department, they panic. They hire a CA to respond. The CA charges ₹10,000-₹50,000 to resolve the notice. Many businesses pay more than they owe just to make the notice go away. ClearTax wants to get ahead of this: audit its SMB customers' filings before the IT department does, identify mismatches that are likely to trigger notices, explain to the customer what the mismatch is and why it exists, and either help them correct it before filing or prepare them to respond if they receive a notice.

The scale makes this impossible to do manually: 2 million customers, multiple filings each per year, mismatches that require understanding both GST rules and income tax rules to assess. ClearTax needs a system that can prioritize which customers are at highest risk, diagnose what their specific mismatches are, assess whether the mismatch is a genuine error or a legitimate difference in accounting treatment, and generate the customer-facing explanation and action plan.

### Why a single AI call won't solve this

The audit requires pulling and comparing data from multiple filing systems (some of this is deterministic arithmetic — does number A match number B?). But assessing whether a mismatch is a real problem or a legitimate difference requires reasoning about tax rules applied to specific transaction types. Prioritizing which customers to contact first requires a risk score that combines mismatch size, mismatch type, and the IT department's known focus areas. Generating the explanation requires translating complex tax reconciliation into something a non-CA business owner can understand. And if a customer has a genuine error, the remediation path varies based on what they filed, when, and whether the filing period is still open for amendment.

### What good looks like

ClearTax's top-risk SMB customers receive a proactive audit report before filing season, with specific mismatches identified and an action plan for each. Customers who go through this process have measurably lower rates of IT notices than customers who don't. The false alarm rate is low enough that customers trust the audit: flagging a customer as high-risk when they're actually clean destroys trust and increases support load.

### The hard question

ClearTax's audit system will identify genuine tax errors in some customers' filings. If a customer has been underpaying taxes — not due to evasion, but due to genuine misclassification — and ClearTax's system identifies this and tells the customer, the customer now has to either correct the filing (and potentially pay more tax) or continue with the incorrect filing knowingly. What is ClearTax's responsibility in this situation? Does surfacing the error create a legal obligation for the customer to disclose it?

---

## Case Study 6 — Urban Company: Service Quality Recovery

### The business problem

Urban Company's beauty category in Delhi has a structural quality problem: customer satisfaction scores are 15% below the platform average, and the problem isn't uniformly distributed. Haircuts are fine. Hair coloring is terrible. South Delhi partners perform better than North Delhi partners. The top 20% of partners have 4.7+ ratings; the bottom 30% have below 3.5 — and the bottom 30% are generating 70% of the complaints and refunds.

Urban Company's quality team knows this at the aggregate level. What they don't know: why the bottom 30% are underperforming. Some of these partners lack specific technical skills. Some have the skills but are rushing jobs to maximize bookings. Some are using cheap supplies that aren't disclosed to customers. Some are getting unfair reviews because of reasons outside their control (the customer had unrealistic expectations, the customer's hair was damaged before the visit). And some are genuinely not good enough and should be offboarded.

The interventions are completely different for each root cause — and applying the wrong intervention wastes money and time, and often makes the partner feel wrongly accused, which makes them less likely to engage with future interventions.

Urban Company has: partner performance data (ratings, completion rates, cancellations), customer review text, service category data, booking patterns, and the ability to deploy interventions (training modules, supply kits, increased scrutiny, temporary hold from bookings, offboarding).

### Why a single AI call won't solve this

Root cause analysis requires reading each underperforming partner's review history, identifying what customers specifically say is going wrong, cross-referencing with the partner's booking patterns and service category, and forming a hypothesis about the root cause. This is different for each partner. Then an intervention needs to be selected from the available set based on the diagnosis. Then the intervention needs to be executed — and for some interventions (like training), that's a multi-week process with checkpoints. Then the results need to be monitored and the intervention adjusted if it's not working.

This is a per-partner diagnosis and intervention pipeline, running in parallel for hundreds of partners, with feedback loops.

### What good looks like

The bottom 30% of underperforming Urban Company beauty partners in Delhi are each classified into one of a small number of root-cause buckets. The appropriate intervention for each bucket is executed in the right sequence. 60 days later, measurably more partners have improved vs. a control group. Partners who are genuinely unimprovable are identified and offboarded faster, freeing capacity for better partners.

### The hard question

Urban Company's intervention system will sometimes misclassify a partner — diagnosing them as having a skill gap when the real problem is customer expectations, for example. This leads to a training intervention that doesn't fix the underlying problem, and leaves the partner feeling unsupported or wrongly criticized. Given that these partners depend on Urban Company for their livelihood, what is Urban Company's accountability when an incorrect diagnosis leads to the wrong intervention and a partner's bookings decline? What oversight mechanism should exist before a system-generated diagnosis is acted upon?

---

## Case Study 7 — Meesho: New Seller Activation

### The business problem

Every month, 50,000-100,000 new sellers join Meesho. Seventy percent of them make zero sales in their first 60 days and go inactive. This churn costs Meesho's seller acquisition investment and reduces supply diversity on the platform.

The sellers who succeed in the first 60 days share predictable behaviors: they list 10+ products in the first week, price competitively relative to similar items on the platform, have product photos that meet basic quality standards, and process their first order within 24 hours. Sellers who don't do these things rarely recover.

What Meesho doesn't have: a system that identifies which new sellers are at risk before they go inactive, diagnoses why they're struggling (wrong category? bad listings? pricing too high? confused about order processing?), and intervenes with the right action at the right time. The seller success team currently sends the same generic onboarding sequence to all new sellers — regardless of whether the seller is a sophisticated FMCG trader or a first-time seller who doesn't know what a stock keeping unit is.

The intervention needs to happen through channels the seller actually uses — primarily WhatsApp and in-app notifications — and in the seller's language. A seller in rural Maharashtra who writes in Marathi and a seller in Rajkot who writes in Gujarati need different interventions communicated differently.

### Why a single AI call won't solve this

Diagnosing a new seller's failure mode requires looking at their listing quality, their category, their pricing relative to comparable products, their activity patterns on the platform, and their communication history. Each of these is a different signal that needs to be pulled and synthesized. The intervention isn't a single message — it may be a sequence of actions over two weeks (first, fix the listing; then, address the pricing; then, walk through order processing). Each step in the sequence needs to be adapted based on whether the seller acted on the previous step. And all of this needs to happen at the scale of tens of thousands of sellers per month.

### What good looks like

The share of new sellers who make at least one sale in their first 60 days increases meaningfully. The intervention system reaches sellers who are at risk before they go inactive — not after. Sellers who receive interventions report finding them relevant and useful, not generic.

### The hard question

Meesho's seller activation system will sometimes recommend that a seller change their product category, discontinue a product, or lower their prices significantly — interventions that directly affect the seller's business strategy. These recommendations are generated by a system that doesn't know the seller's full situation: their supplier relationships, their margins, their local market. When the system is wrong, it could lead a seller to make business decisions that hurt them. Who bears responsibility for this? Does Meesho have a disclosure obligation to sellers that the advice is AI-generated?

---

## Case Study 8 — Groww: Investment Goal Recovery

### The business problem

Groww has 50 million registered retail investors — most of them first-generation investors who started investing because of a specific life goal: retirement corpus, children's education fund, house down payment in 5 years. When they started, they set a goal, calculated how much to invest monthly, and began a SIP.

Three years later, the data tells a troubling story. A significant fraction of these investors are off-track for their stated goals. Some have paused or cancelled their SIPs after a market correction — locking in losses and abandoning the compounding runway they had built. Some have redeemed their mutual fund units for a short-term expense and never restarted. Some were investing correctly, but their life situation changed (had a child, lost a job, changed income level) and their investment plan was never updated to reflect the new reality. Some are still investing the original SIP amount, but because their income has grown 50% in 3 years and their goal amount is now larger, the original SIP is no longer enough.

Groww knows all of this. They can see the original goal the investor set, the actual portfolio performance, the SIP history, the redemption history, and the current portfolio composition. What Groww does not have is a system that actively monitors whether each investor is on track, detects the moment they go off-track or experience a trigger event, diagnoses why, and intervenes — at the right time, through the right channel, with the right message — to bring them back to their goal trajectory.

The problem is not just data or analytics. It is a continuous, personalized process of monitoring → detection → diagnosis → intervention → follow-up, running in the background for 50 million investors simultaneously.

### Why a single AI call won't solve this

Goal tracking is partially deterministic: given the investor's goal amount, timeline, current corpus, and expected return, you can calculate whether they are on track. But detecting *why* they went off-track — panic sale vs. genuine financial need vs. goal becoming irrelevant vs. plan never updated after a life event — requires reasoning over behavioral signals and communication history. Deciding what intervention is appropriate requires judgment: the same "you paused your SIP" event calls for a very different response depending on whether it happened during a market crash (behavioral coaching) vs. a verified job loss (goal recalibration + empathy). Executing the intervention requires choosing the channel, the tone, the timing, and the call to action. Following up requires monitoring whether the investor acted, and escalating to a human advisor if the automated intervention isn't working.

Some parts of this should never be autonomous — a system should not automatically restart an investor's SIP, for example.

### What good looks like

Investors who go off-track for their goals are detected within weeks, not months. The intervention they receive is specific to their situation — not a generic "don't panic, stay invested" message. The share of off-track investors who return to their goal trajectory within 90 days of an intervention is meaningfully higher than the baseline. Investors who receive interventions trust Groww more, not less — the outreach feels like a helpful advisor, not a marketing push.

### The hard question

SEBI's investment advisor regulations are clear: giving personalized investment advice requires a registered investment advisor (RIA) license. Groww is not a SEBI-registered investment advisor. A system that tells an investor "increase your SIP by ₹3,000/month to get back on track" is providing investment advice in the regulatory sense — even if Groww calls it a "suggestion" or a "nudge." How do you design a system that is genuinely useful to investors heading off-track — not just a generic disclaimer — while staying within what a non-RIA platform is permitted to do under SEBI regulations? Where exactly is the line, and how do you encode it into the system's behavior rather than just a legal footer?

---

## Case Study 9 — Practo: Post-Discharge Recovery Monitoring

### The business problem

Practo has partnered with cardiac surgery departments at 10 major hospitals in India. The clinical problem: after cardiac surgery, patients are discharged with a medication regimen, dietary restrictions, a physical activity plan, and a schedule of follow-up appointments. The 30 days after discharge are the highest-risk period for readmission — and most readmissions are preventable if the right signals are detected early.

Currently, hospitals make one follow-up phone call at day 7. That call is handled by a junior nurse who asks "how are you feeling?" and documents the response. If the patient says "a little tired," the nurse says "that's normal, keep taking your medicines" — whether the fatigue is a normal part of recovery or an early sign of fluid retention that indicates cardiac decompensation.

What Practo wants to build: a continuous post-discharge monitoring system that collects daily symptom reports from patients, applies clinical logic to determine whether a symptom pattern warrants escalation, and routes the response through the right channel — self-care guidance for minor symptoms, a call from Practo's nurse line for concerning patterns, coordination with the patient's cardiologist for urgent situations, and emergency protocol for critical symptoms.

The system has to navigate real tension: cardiac patients are anxious, and overcommunication can create health anxiety that is itself harmful. A patient who gets a call asking "are you having chest pain?" every day develops cardiac anxiety. A patient who is given too many warning signs to watch for spends their recovery focused on symptoms rather than healing. But under-monitoring kills people. The system has to be calibrated to act on genuine signals while not amplifying anxiety.

### Why a single AI call won't solve this

Daily monitoring requires asking the right questions, interpreting the answers, applying clinical triage logic (which involves deterministic rules for high-risk symptoms, probabilistic reasoning for ambiguous ones), and routing to the right response. The routing logic involves different actors (patient, nurse line, cardiologist, emergency services) and different latencies (some responses need to happen in minutes, others can wait for a scheduled check-in). The system needs to remember previous days' responses and detect trends (three days of increasing shortness of breath is different from one day). And the patient communication must be carefully calibrated — tone, language, frequency, and reassurance all matter clinically.

### What good looks like

30-day readmission rates for Practo-monitored patients drop meaningfully vs. unmonitored patients. Zero genuine deterioration signals are missed by the system — if a patient is getting worse, the system catches it and routes appropriately. Monitored patients' cardiac anxiety scores are no higher than unmonitored patients (the monitoring doesn't create its own clinical problem). Cardiologists who receive escalations from the system trust them — the system isn't crying wolf.

### The hard question

The post-discharge monitoring system is making triage decisions that affect patient outcomes. If the system fails to flag a deteriorating patient (false negative), the patient may experience a preventable cardiac event. If it over-flags (false positive), healthcare resources are wasted and patient anxiety increases. In either case, the harm is clinical. What is Practo's liability for the system's triage decisions? Is this a medical device under CDSCO regulations? How do you design the system so that it is clearly a communication and routing tool — not a diagnostic tool — while still being clinically useful?

---

## Case Study 10 — IndiaMART: B2B Deal Facilitation

### The business problem

IndiaMART connects buyers who post procurement requirements with suppliers who can fulfill them. A buyer posts a requirement ("looking for 10,000 stainless steel fasteners, Grade 316, M8 x 20mm, delivery within 3 weeks, budget ₹2-4 per piece"). Fifty suppliers respond with some variant of "we can supply, please share your contact details."

The deal conversion rate is under 2%. Most inquiries die in what IndiaMART calls the "ghost zone" — the buyer got overwhelmed with identical-looking responses, stopped responding, and the suppliers gave up. Or the buyer couldn't verify which suppliers were credible. Or the back-and-forth on specifications took too long and the buyer found another channel. Or the suppliers didn't understand what the buyer actually needed and sent the wrong quote.

The problem isn't a lack of intent — buyers and suppliers both want to transact. The problem is that IndiaMART currently connects them and then steps back. The actual deal-making — understanding the buyer's real requirements, qualifying which suppliers can actually fulfill them, managing the specification conversation, helping the two sides get to a quote — requires work that nobody is doing.

What IndiaMART wants: a system that actively facilitates B2B deals. Not just connecting buyers and suppliers, but managing the process from inquiry to order — understanding what the buyer actually needs (often they don't specify correctly the first time), qualifying suppliers on their real capability to fulfill the specific requirement, managing the communication between buyer and shortlisted suppliers, helping them resolve specification ambiguities, and driving the process toward an order being placed.

This is not a one-session interaction. A typical B2B deal on IndiaMART involves 5-15 exchanges over 3-10 days. The system needs to be persistent, asynchronous, and capable of managing multiple threads simultaneously.

### Why a single AI call won't solve this

The buyer's requirement often needs to be clarified before being shared with suppliers (buyers frequently underspecify or misspecify). Supplier qualification requires checking their capabilities against the requirement (some of this is deterministic — does the supplier's listed product range match? — and some requires inference from their response quality). Managing the buyer-supplier conversation requires keeping track of what has been discussed, what questions are pending, and what commitments have been made. Driving toward an order requires knowing when to push, when to wait, when to bring in a human deal manager, and when to declare the deal dead and move on.

Different deals have different dynamics — a high-value first-time transaction between a buyer and supplier who've never worked together needs different facilitation than a repeat order.

### What good looks like

The conversion rate from inquiry to order for deals where facilitation is applied meaningfully exceeds the unassisted baseline. The time from first inquiry to order placement decreases significantly. Both buyers and suppliers report a better experience vs. the unassisted platform. The system correctly identifies deals that are likely to convert and focuses facilitation effort on them, rather than treating all inquiries equally.

### The hard question

IndiaMART's facilitation system will develop detailed knowledge of buyer requirements and supplier capabilities — including information that buyers and suppliers consider competitively sensitive (pricing floors, supply constraints, buyer budget). This information flows through IndiaMART's system and could, in principle, be used to benefit one party over another. What obligations does IndiaMART have to buyers and suppliers regarding how this information is used? Under DPDP Act 2023, what consent framework needs to exist before IndiaMART's AI system uses information shared in a deal negotiation to improve future facilitation?

---

## Case Study 11 — Airbnb: Guest Refund Request Triage

### The business problem

A guest messages Airbnb during or right after a stay: the AC didn't work, the unit was dirty, an advertised amenity wasn't there. Airbnb has to decide fast — full refund, partial refund, or deny the claim — and both sides have something real at stake. Grant refunds too easily and hosts lose income on stays that may have been fine, and eventually stop trusting the platform to have their side. Deny too readily and guests feel cheated, leave a bad review, or just stop booking through Airbnb.

The same complaint can mean very different things. "The AC didn't work" could mean it was genuinely broken the whole stay — and the guest mentioned it in the in-stay message thread, and the host's own maintenance log shows a ticket. Or it could mean the guest never figured out how to use it, and no other guest in the last twenty stays has said a word about that unit's AC. Or it could be a guest who had a perfectly fine stay and files a complaint on checkout day, having said nothing during the stay, hoping for a discount. Telling these apart means reading the in-stay message thread (did they raise it when it was happening, or only after checkout), checking what the listing actually promises or disclaims, checking whether other recent guests of the same unit mention the same issue, and checking whether this particular guest has a pattern of filing this kind of complaint.

### Why a single AI call won't solve this

Each piece of evidence lives in a different unstructured source — the message thread, the listing description, other guests' review text, this guest's own complaint history — and none of it settles the question by itself. The system has to synthesize all four into a legitimacy judgment, then apply a remedy that's proportional to how much of the stay was actually affected (one bad night out of five reads differently than the whole stay), then write the decision back to both host and guest in a way that doesn't sound accusatory to whichever side didn't get what they wanted. And when the evidence points to a genuine pattern — a guest who does this often, or a host whose listing draws this complaint repeatedly — that needs to surface to a human reviewer rather than get resolved silently in either direction.

### What good looks like

Most refund decisions resolve within an hour instead of sitting in a support queue for days. The system's legitimacy calls track closely with what a human trust & safety reviewer would decide given the same evidence. Hosts don't see a rise in refunds granted against issues they can show were fine, and guests with real issues get resolved without needing to escalate or leave a review just to get attention.

### The hard question

Airbnb's growth depends far more on guest satisfaction and repeat bookings than on any single host's happiness — hosts are, in aggregate, replaceable; a guest's next ten years of bookings are not. A refund-adjudication system built and tuned by Airbnb has a structural incentive to lean guest-friendly on close calls, independent of what the evidence actually supports. How would you even detect, let alone prove, whether the system is deciding disputes on the merits versus quietly optimizing for whichever side is more valuable to Airbnb's business? And if you found it was biased, would Airbnb's own incentives let it fix that?

---

## Capstone Evaluation Criteria

Your demo on August 15 will be evaluated on these dimensions. The evaluation doesn't care whether you built one agent or five, whether you used tools or multi-step workflows, or whether your solution is simple or complex. It cares whether you solved the problem well and understood what you built.

| Criterion | What we're looking for |
|---|---|
| **Does it run?** | Complete end-to-end demo of a real scenario, live, without breaking |
| **PM decisions visible** | You can articulate 3–5 architecture decisions you made and why — including decisions about what NOT to automate |
| **Loops and agentic patterns identified** | You can explain where your system loops, where it makes autonomous decisions, and where it hands off to a human |
| **Failure surface understood** | You've tested the system against inputs designed to break it and addressed the most important failure modes |
| **Evals present** | At least 5 test cases documented with pass/fail results, including at least one adversarial test case |
| **Cost and scale awareness** | You can estimate the monthly cost of running this system at realistic scale for the business |
| **Governance decision made** | You've made a specific, defensible decision about the hard governance question — not a disclaimer, an actual product decision |

A capstone that scores well on all seven is exceptional. Getting through the first four is the baseline.
