# Agile & Scrum — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — clear, confident, plain English.

---

## 1. What is Agile? How is it different from Waterfall?

**Answer:**
Agile is an **iterative, incremental** approach to software development. Instead of building the entire product in one go (which is what Waterfall does), we break the work into small cycles called **sprints** — typically 2 weeks — and deliver working software at the end of each sprint.

The key difference is **feedback**. In Waterfall, the client sees the product only at the end, so if something is wrong, it's very expensive to fix. In Agile, we demo working features every sprint, get feedback, and adapt. So Agile **embraces change**, while Waterfall tries to **prevent change**.

Waterfall follows a strict sequence — Requirements → Design → Build → Test → Deploy. Agile overlaps these activities within each sprint.

---

## 2. What is the Agile Manifesto? Can you name the four values?

**Answer:**
The Agile Manifesto was created in 2001 by a group of software developers. It defines four core values:

1. **Individuals and Interactions** over Processes and Tools — people and communication matter more than rigid processes.
2. **Working Software** over Comprehensive Documentation — a running feature is worth more than a 100-page spec.
3. **Customer Collaboration** over Contract Negotiation — we work WITH the client, not just follow a contract.
4. **Responding to Change** over Following a Plan — plans are important, but we should be flexible when things change.

The key phrase is: **"while there is value in the items on the right, we value the items on the left more."** It's not saying documentation or planning is bad — it's saying the left side matters MORE.

---

## 3. Can you mention a few Agile principles?

**Answer:**
There are 12 principles behind the Agile Manifesto. The ones I find most important are:

- **Deliver working software frequently** — every few weeks, not months.
- **Welcome changing requirements**, even late in development.
- **Business people and developers work together daily.**
- **The best architectures and designs emerge from self-organizing teams.**
- **At regular intervals, the team reflects on how to become more effective** — that's the retrospective.
- **Simplicity** — maximize the amount of work NOT done. Don't over-engineer.
- **Working software is the primary measure of progress** — not documents, not story points, but actual working features.

---

## 4. What is Scrum? How is it related to Agile?

**Answer:**
Scrum is a **framework** for implementing Agile. Agile is a philosophy — it tells you WHAT to value. Scrum is a practical framework — it tells you HOW to do it with specific roles, events, and artifacts.

Think of it this way: Agile is like saying "eat healthy." Scrum is a specific diet plan that tells you what to eat, when, and how much.

There are other Agile frameworks too — like Kanban, XP (Extreme Programming), and SAFe — but Scrum is by far the most popular.

---

## 5. What are the three roles in Scrum?

**Answer:**
1. **Product Owner (PO)** — represents the customer and the business. They own the Product Backlog, prioritize user stories, and decide WHAT gets built and in what order. They make sure the team is building the right thing.

2. **Scrum Master (SM)** — the team's coach and facilitator. They remove blockers, protect the team from distractions, and make sure the Scrum process is followed properly. They are NOT a project manager — they serve the team, not manage it.

3. **Development Team** — the cross-functional team of 5-9 people who actually BUILD the product. This includes developers, testers, designers — whoever is needed. The team is **self-organizing** — they decide HOW to do the work.

---

## 6. What are the Scrum Artifacts?

**Answer:**
There are three main artifacts:

1. **Product Backlog** — a prioritized list of ALL the work that needs to be done on the product. The Product Owner maintains it. Items at the top are well-defined and ready for the next sprint; items at the bottom are rough ideas.

2. **Sprint Backlog** — the subset of Product Backlog items that the team commits to completing in the current sprint, plus the plan for how they'll do it.

3. **Increment** — the working, potentially shippable piece of software delivered at the end of each sprint. Every increment must meet the **Definition of Done (DoD)**.

---

## 7. What are the Scrum Ceremonies/Events?

**Answer:**
There are five key events:

1. **Sprint Planning** — happens at the start of a sprint. The team picks items from the Product Backlog, discusses them, breaks them into tasks, and commits to what they can deliver. Usually takes 2-4 hours for a 2-week sprint.

2. **Daily Standup (Daily Scrum)** — a 15-minute daily meeting where each team member answers: What did I do yesterday? What will I do today? Do I have any blockers? It's for synchronization, not status reporting.

3. **Sprint Review (Demo)** — at the end of the sprint, the team demos the completed work to stakeholders. We show working software, get feedback, and the Product Owner may update the backlog based on that feedback.

4. **Sprint Retrospective** — the team reflects on the sprint process: What went well? What didn't? What can we improve? This is how we continuously improve as a team.

5. **Backlog Refinement (Grooming)** — an ongoing activity where the team clarifies, estimates, and breaks down upcoming user stories so they're ready for future sprint planning.

---

## 8. What is a Sprint? What's a typical sprint length?

**Answer:**
A Sprint is a **fixed-length iteration** — a time-boxed period during which the team works on a set of committed items and delivers a working increment. The sprint length is typically **2 weeks**, though some teams use 1, 3, or 4 weeks.

Key rules: the sprint length stays consistent. Once a sprint starts, the scope shouldn't change — no adding new stories mid-sprint without removing something else. If something comes up that's truly urgent, the Product Owner and Scrum Master discuss it, but it's the exception.

---

## 9. What is a User Story? How do you write one?

**Answer:**
A User Story is a short, simple description of a feature written from the **end user's perspective**. The format is:

**"As a [type of user], I want [some goal], so that [some reason]."**

For example: "As a customer, I want to reset my password, so that I can regain access to my account."

A good user story follows the **INVEST** criteria:
- **I**ndependent — can be developed separately
- **N**egotiable — details can be discussed
- **V**aluable — delivers value to the user
- **E**stimable — the team can estimate the effort
- **S**mall — can be completed in one sprint
- **T**estable — has clear acceptance criteria

Each story also has **Acceptance Criteria** — specific conditions that must be met for the story to be considered done. For the password reset example: "Given I'm on the login page, when I click 'Forgot Password' and enter my email, then I receive a reset link within 2 minutes."

---

## 10. What are Story Points? How do you estimate them?

**Answer:**
Story Points are a **relative measure of effort, complexity, and uncertainty** — NOT hours or days. We compare stories to each other rather than estimating absolute time.

We use the **Fibonacci sequence** (1, 2, 3, 5, 8, 13, 21) because as work gets bigger, uncertainty increases, so the gaps between numbers should also increase.

The estimation process is called **Planning Poker**:
1. The Product Owner describes a story.
2. The team discusses it.
3. Everyone privately picks a card with their estimate.
4. Cards are revealed simultaneously.
5. If there's a big difference (e.g., someone says 3, another says 13), those people explain their reasoning.
6. The team re-votes until they converge.

We typically pick a **reference story** — a story everyone agrees is a "3" — and compare everything else to it. "Is this bigger or smaller than our reference?"

---

## 11. What is Velocity? How is it used?

**Answer:**
Velocity is the **total number of story points the team completes in a sprint**. If the team finished stories worth 3 + 5 + 8 + 5 = 21 points in a sprint, their velocity is 21.

Over a few sprints, velocity stabilizes and becomes a **predictability tool**. If the team's average velocity is 20 points per sprint and there are 100 points left in the backlog, we can estimate roughly 5 more sprints to complete the work.

Important: velocity is a **team metric, not an individual one**. It's used for planning, not for judging performance. And you should never compare velocity across different teams because each team calibrates story points differently.

---

## 12. What is a Burndown Chart?

**Answer:**
A Burndown Chart is a visual graph that shows **how much work is remaining** versus how much time is left in the sprint. The Y-axis is the remaining story points, the X-axis is the days in the sprint.

There's an **ideal line** that goes from the total story points on Day 1 down to zero on the last day. Then there's the **actual line** showing real progress.

If the actual line is above the ideal line — the team is **behind schedule**. If it's below — they're **ahead**. If it's flat — they're **blocked** on something.

It's a great tool for the Daily Standup to quickly visualize if the sprint is on track.

---

## 13. What is the Definition of Done (DoD)?

**Answer:**
The Definition of Done is a **shared checklist** that the team agrees on — it defines what "done" means for every user story. Without it, "done" is subjective.

A typical DoD includes:
- Code is written and peer-reviewed
- Unit tests are written and passing
- Integration tests pass
- Code meets coding standards
- Documentation is updated
- Feature is deployed to the staging environment
- Product Owner has accepted the story

The DoD ensures quality and consistency. If a story doesn't meet all the DoD criteria, it's **not done** — it goes back to the backlog.

---

## 14. What happens if the team can't finish all stories in a sprint?

**Answer:**
Incomplete stories are **NOT** marked as partially done. They go back to the Product Backlog. The Product Owner re-prioritizes them for the next sprint.

In the Sprint Retrospective, the team discusses WHY they couldn't finish — Was the estimation off? Were there unexpected blockers? Was the scope too ambitious? This feedback helps improve future sprint planning.

The key principle is: we ONLY count stories that are fully done (meet the DoD). No partial credit. This maintains the integrity of our velocity metric.

---

## 15. What is the difference between Scrum and Kanban?

**Answer:**
Both are Agile frameworks, but they work differently:

| Aspect | Scrum | Kanban |
|--------|-------|--------|
| Iterations | Fixed sprints (2 weeks) | Continuous flow, no sprints |
| Roles | PO, SM, Dev Team | No prescribed roles |
| Board | Reset each sprint | Persistent, never reset |
| WIP Limits | Sprint capacity | Explicit WIP limits per column |
| Changes | Not during sprint | Anytime |
| Planning | Sprint Planning | On-demand |
| Metrics | Velocity | Lead time, cycle time |

Scrum is great when you want **structure and predictability**. Kanban is better for **continuous delivery** or support/maintenance work where priorities change frequently.

---

## 16. What is a Blocker? How do you handle it?

**Answer:**
A Blocker is anything that **prevents a team member from making progress** on their task. It could be a technical issue, a missing requirement, a dependency on another team, waiting for access, etc.

How we handle it:
1. **Raise it immediately** — don't wait for the Daily Standup.
2. **The Scrum Master** takes ownership of resolving it — that's their primary job.
3. It gets tracked on the sprint board (often with a red flag or label).
4. If it can't be resolved quickly, the team may need to **swarm** — multiple people focusing on it, or escalate to management.

---

## 17. What does a typical day look like for you in an Agile team?

**Answer:**
My typical day starts with the **Daily Standup** at around 9:15 AM — about 15 minutes. I share what I worked on yesterday, what I'm picking up today, and if I have any blockers.

After standup, I work on my assigned user stories — writing code, writing tests, doing code reviews for teammates. If I have questions about a story, I reach out to the Product Owner directly rather than making assumptions.

Throughout the day, I might pair-program with a teammate if we're working on a complex feature. I also participate in Pull Request reviews.

If it's a sprint boundary, we have Sprint Review where we demo our work, and Sprint Retrospective where we discuss improvements. If it's mid-sprint, we might have a Backlog Refinement session to prepare stories for the next sprint.

---

## 18. What is the SDLC? How does Agile fit into it?

**Answer:**
SDLC stands for **Software Development Life Cycle** — it's the overall process of planning, creating, testing, and deploying software. The common phases are: Requirements → Design → Implementation → Testing → Deployment → Maintenance.

Agile doesn't eliminate these phases — it **compresses them into each sprint**. Every 2-week sprint includes a bit of requirements (Sprint Planning), design, coding, testing, and potentially deployment. So instead of doing each phase once over 6 months, we do all phases every 2 weeks in mini-cycles.

Other SDLC models include Waterfall (sequential), V-Model (testing mirrors development), Spiral (risk-driven), and Iterative. Agile is the most widely adopted today because of its flexibility and fast feedback.

---

## 19. How do you handle scope creep in Agile?

**Answer:**
Scope creep is when new requirements keep getting added without removing anything. In Agile, we handle it through:

1. **Sprint boundaries** — once a sprint starts, the scope is locked. New requests go to the Product Backlog, not the current sprint.
2. **Product Owner prioritization** — the PO decides if a new request is more important than existing backlog items.
3. **Trade-offs** — if something must be added mid-sprint, something else of equal size must be removed.
4. **Backlog Refinement** — keeps the backlog groomed so priorities are always clear.

Agile WELCOMES change, but it manages it through process. We don't just say yes to everything — we say "yes, and here's the trade-off."

---

## 20. What tools have you used for Agile project management?

**Answer:**
I've used **Jira** as the primary tool — it's the most common for Agile teams. In Jira, we create Epics, User Stories, Tasks, and Bugs. We use the Scrum Board to track sprint progress, and Jira generates Burndown Charts, Velocity Charts, and Sprint Reports automatically.

I've also used **Confluence** alongside Jira for documentation — sprint notes, architecture decisions, and meeting minutes. For daily communication, we use **Slack** or **Microsoft Teams**, and for retrospectives, tools like **Miro** or **EasyRetro**.

For version control and CI/CD, we use **Git with GitHub/GitLab**, and the pull request process ties into our Definition of Done.
