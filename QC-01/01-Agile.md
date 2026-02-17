# Agile Methodology — From Basics to Expert

---

## 1. Introduction to Agile

### What is Agile?

Agile is a **software development methodology** that emphasizes iterative development, collaboration, flexibility, and delivering working software frequently. Instead of building the entire product at once (like Waterfall), Agile breaks work into small, manageable chunks called **iterations** or **sprints**.

### Why Agile Was Born

In the 1990s, software projects had a **70% failure rate** using traditional Waterfall methods. The problems:
- Rigid planning upfront — requirements change was costly
- Customers only saw the product at the end
- Testing happened too late
- Teams worked in silos

Agile was created to solve these exact problems.

### Under the Hood: The Core Philosophy

Agile is not a single process — it's an **umbrella term** for multiple frameworks (Scrum, Kanban, XP, etc.) that share common values. At its core, Agile is about **feedback loops**:

```
Plan → Build → Test → Review → Adapt → Repeat
         ↑________________________________↓
```

Every iteration produces a **potentially shippable product increment**.

---

## 2. Agile Manifesto

The **Agile Manifesto** was created in 2001 by 17 software practitioners at a ski resort in Utah. It defines **4 values** and **12 principles**.

### The 4 Values

| We Value More                       | Over                          |
|-------------------------------------|-------------------------------|
| **Individuals and interactions**    | Processes and tools           |
| **Working software**                | Comprehensive documentation   |
| **Customer collaboration**          | Contract negotiation          |
| **Responding to change**            | Following a plan              |

> **Note**: The items on the right still have value — but we value the items on the left **more**.

### The 12 Principles

1. **Highest priority** is to satisfy the customer through early and continuous delivery of valuable software.
2. **Welcome changing requirements**, even late in development.
3. **Deliver working software frequently** (weeks rather than months).
4. **Business people and developers** must work together daily.
5. Build projects around **motivated individuals** — give them the environment and trust.
6. **Face-to-face conversation** is the most efficient communication method.
7. **Working software** is the primary measure of progress.
8. Agile promotes **sustainable development** — maintain a constant pace indefinitely.
9. Continuous attention to **technical excellence** and good design.
10. **Simplicity** — maximizing the amount of work NOT done — is essential.
11. Best architectures, requirements, and designs emerge from **self-organizing teams**.
12. Team **reflects and adjusts** its behavior at regular intervals.

### Under the Hood: Why These Principles Matter

Principle #2 ("Welcome changing requirements") is revolutionary. In Waterfall, a change request at month 10 of a 12-month project was a disaster. In Agile, because we work in 2-week sprints, changes only affect the **next sprint**, not the whole project.

---

## 3. Scrum Framework

Scrum is the **most popular** Agile framework. ~70% of Agile teams use Scrum.

### Scrum Roles

| Role | Responsibility |
|------|----------------|
| **Product Owner (PO)** | Defines WHAT to build. Owns the Product Backlog. Represents stakeholders. |
| **Scrum Master (SM)** | Ensures the team follows Scrum. Removes blockers. Servant-leader. |
| **Development Team** | Self-organizing cross-functional team (3-9 people). Decides HOW to build. |

### Scrum Artifacts

| Artifact | Description |
|----------|-------------|
| **Product Backlog** | Ordered list of everything needed in the product. Maintained by PO. |
| **Sprint Backlog** | Subset of Product Backlog selected for the current sprint + a plan to deliver. |
| **Increment** | The sum of all completed backlog items at the end of a sprint — must be "Done". |

### Scrum Events (Ceremonies)

| Event | Duration (2-week sprint) | Purpose |
|-------|--------------------------|---------|
| **Sprint Planning** | 4 hours max | Decide what to do in this sprint |
| **Daily Standup** | 15 minutes max | Sync up — what did I do, what will I do, any blockers? |
| **Sprint Review** | 2 hours max | Demo the increment to stakeholders, get feedback |
| **Sprint Retrospective** | 1.5 hours max | Team reflects on how to improve |

### Scrum Flow Diagram

```
Product Backlog
      │
      ▼
Sprint Planning → Sprint Backlog
                       │
                       ▼
                 ┌─────────────┐
                 │   Sprint    │ ← Daily Standups (every day)
                 │  (2-4 weeks)│
                 └─────────────┘
                       │
                       ▼
              Product Increment
                       │
              ┌────────┴────────┐
              ▼                 ▼
        Sprint Review    Sprint Retrospective
              │                 │
              └────────┬────────┘
                       ▼
                  Next Sprint
```

### Under the Hood: Why Scrum Works

Scrum creates a **fixed time-box** (sprint). Within that box:
- Scope is fixed (Sprint Backlog items)
- No changes mid-sprint (unless PO cancels the sprint — very rare)
- Team self-organizes to meet the Sprint Goal

This gives teams **predictability** while maintaining **flexibility** at the sprint boundary.

---

## 4. Sprint Planning

Sprint Planning is the ceremony that **kicks off every sprint**. It's where the team decides what they'll work on for the next 2 weeks and how they'll get it done. Good sprint planning sets the team up for success by ensuring everyone understands exactly what's expected, agreements are clear, and the workload is realistic given the team's capacity.

### What Happens in Sprint Planning?

Sprint Planning answers two questions:
1. **WHAT** can be delivered in this Sprint?
2. **HOW** will the chosen work get done?

### The Process

```
Step 1: PO presents the top prioritized items from the Product Backlog
Step 2: Team discusses each item — clarifies requirements
Step 3: Team estimates the effort for each item
Step 4: Team selects items they can finish in the sprint (based on velocity)
Step 5: Team breaks selected items into tasks (Sprint Backlog)
Step 6: Team defines a Sprint Goal
```

### Capacity Planning

```
Team Capacity = Number of Developers × Available Hours × Focus Factor

Example:
- 5 developers
- 6 hours/day productive (out of 8)
- 10 working days in a 2-week sprint
- Focus factor: 0.7 (70% — accounting for meetings, interruptions)

Capacity = 5 × 6 × 10 × 0.7 = 210 hours
```

### Definition of Done (DoD)

The team must define what "Done" means. Example:

```
✅ Code complete
✅ Unit tests written and passing
✅ Code reviewed by at least 1 peer
✅ No critical/high bugs
✅ Documentation updated
✅ Deployed to staging environment
✅ PO has accepted the story
```

---

## 5. Agile User Stories

### What is a User Story?

A User Story is a short, simple description of a feature **told from the user's perspective**.

### Format

```
As a [type of user],
I want [an action/feature],
So that [a benefit/value].
```

### Examples

```
As a customer,
I want to search products by category,
So that I can quickly find what I need.

As an admin,
I want to export reports as PDF,
So that I can share them with stakeholders.
```

### INVEST Criteria for Good User Stories

| Letter | Meaning | Explanation |
|--------|---------|-------------|
| **I** | Independent | Story can be developed independently of others |
| **N** | Negotiable | Details can be discussed and refined |
| **V** | Valuable | Must deliver value to the user/customer |
| **E** | Estimable | Team must be able to estimate the effort |
| **S** | Small | Small enough to complete in one sprint |
| **T** | Testable | Must have clear acceptance criteria |

### Acceptance Criteria (AC)

Every user story needs acceptance criteria — these define when the story is "done":

```
Story: As a user, I want to log in with email and password.

Acceptance Criteria:
  Given I am on the login page
  When I enter a valid email and password
  Then I should be redirected to the dashboard

  Given I am on the login page
  When I enter an invalid email or password
  Then I should see an error message "Invalid credentials"

  Given I am on the login page
  When I leave email or password empty
  Then the login button should be disabled
```

### Under the Hood: Epics → Stories → Tasks

```
Epic (large feature)
  └── User Story 1
  │     ├── Task 1.1
  │     ├── Task 1.2
  │     └── Task 1.3
  └── User Story 2
  │     ├── Task 2.1
  │     └── Task 2.2
  └── User Story 3
        ├── Task 3.1
        └── Task 3.2
```

- **Epic**: Large body of work (e.g., "User Authentication System")
- **Story**: A slice of the epic (e.g., "Login with email/password")
- **Task**: Technical work to complete the story (e.g., "Create login API endpoint")

---

## 6. Story Pointing

### What is Story Pointing?

Story pointing is a **relative estimation** technique. Instead of estimating in hours, teams assign **story points** — a unit of effort, complexity, and uncertainty.

### Fibonacci Scale

Teams commonly use the **Fibonacci sequence**: `1, 2, 3, 5, 8, 13, 21`

| Points | Meaning |
|--------|---------|
| 1 | Trivial — change a label, fix a typo |
| 2 | Simple — well-understood, minimal effort |
| 3 | Small — clear requirements, moderate effort |
| 5 | Medium — some complexity, might need research |
| 8 | Large — significant complexity, multiple components |
| 13 | Very Large — high uncertainty, consider splitting |
| 21 | Too Large — MUST be split into smaller stories |

### Planning Poker

The most popular estimation technique:

```
1. PO reads a user story
2. Team discusses and asks clarifying questions
3. Each member privately selects a card (1, 2, 3, 5, 8, 13, 21)
4. All cards are revealed simultaneously
5. If consensus → that's the estimate
6. If disagreement → highest and lowest explain their reasoning
7. Re-vote until consensus
```

### Under the Hood: Why Relative Estimation Works

Humans are bad at absolute estimation ("This will take 4.5 hours") but good at **relative** estimation ("This is twice as complex as that other story").

Story points abstract away:
- Individual skill differences
- Interruptions
- Unknown unknowns

Over time, the team's **velocity** (average story points completed per sprint) becomes the predictor.

---

## 7. Agile Estimation and Planning

### Velocity

**Velocity** = Total story points completed in a sprint.

```
Sprint 1: 24 points completed
Sprint 2: 28 points completed
Sprint 3: 22 points completed
Sprint 4: 26 points completed

Average Velocity = (24 + 28 + 22 + 26) / 4 = 25 points/sprint
```

### Release Planning

```
Total remaining story points: 200
Average velocity: 25 points/sprint
Sprint duration: 2 weeks

Estimated sprints to complete = 200 / 25 = 8 sprints
Estimated release date = 8 × 2 = 16 weeks from now
```

### Burndown Chart

A burndown chart tracks remaining work over time:

```
Story Points
    │
 100├─●
    │   ●
  80├─────●
    │       ●
  60├─────────●
    │           ●
  40├─────────────●          ← Ideal burndown (straight line)
    │               ●
  20├─────────────────●
    │                   ●
   0├─────────────────────●
    └──┬──┬──┬──┬──┬──┬──┬──→ Days
       1  2  3  4  5  6  7  8
```

### Burnup Chart

Shows work completed vs total scope:

```
Story Points
    │                        ── Total Scope
 100├─────────────────────────
    │                   ●●●●● ← Completed Work
  80├─────────────────●●
    │               ●●
  60├─────────────●●
    │           ●●
  40├─────────●●
    │       ●●
  20├─────●●
    │   ●●
   0├─●●
    └──┬──┬──┬──┬──┬──┬──┬──→ Days
```

---

## 8. Agile Metrics and Reporting

Metrics help teams **measure their performance objectively** so they can improve over time. The goal is not to use metrics to punish teams or compare individuals — it's to give the team honest data about how they're doing so they can make informed decisions during retrospectives. Good metrics answer questions like: "Are we getting faster?", "How long does work actually take?", and "Are we shipping quality code?"

### Key Metrics

| Metric | What It Measures | Formula |
|--------|-----------------|---------|
| **Velocity** | Team throughput per sprint | Sum of completed story points |
| **Sprint Burndown** | Remaining work in current sprint | Remaining points vs. time |
| **Cycle Time** | Time from start to finish of an item | End date - Start date |
| **Lead Time** | Time from request to delivery | Delivery date - Request date |
| **Throughput** | Number of items completed per time period | Items completed / Time period |
| **Escaped Defects** | Bugs found in production | Count of production bugs per sprint |

### Velocity Chart

```
Points
  30│     ██
    │  ██ ██    ██
  25│  ██ ██ ██ ██
    │  ██ ██ ██ ██ ██
  20│  ██ ██ ██ ██ ██
    │  ██ ██ ██ ██ ██
  15│  ██ ██ ██ ██ ██
    └──S1─S2─S3─S4─S5──→ Sprints
```

### Cumulative Flow Diagram (CFD)

Shows WIP (Work In Progress) over time:

```
Items │████████████████████████████████ Done
      │██████████████████████████ In Progress
      │████████████████████ In Review
      │██████████████ To Do
      │████████ Backlog
      └────────────────────────────→ Time
```

---

## 9. SDLC vs Agile

The **Software Development Life Cycle (SDLC)** is the overall process used to develop software. Waterfall was the traditional SDLC model — you complete each phase fully before starting the next. Agile is an alternative SDLC approach that runs all phases (planning, design, coding, testing, deployment) within every sprint. Understanding the difference is important because organizations often transition from Waterfall to Agile, and you'll be asked about this in interviews.

### Traditional SDLC (Waterfall)

```
Requirements → Design → Development → Testing → Deployment → Maintenance
     ↓            ↓          ↓            ↓          ↓
  (Months)    (Months)   (Months)     (Months)   (Once)
```

### Agile SDLC

```
Sprint 1: [Plan → Design → Code → Test → Deploy] → Increment 1
Sprint 2: [Plan → Design → Code → Test → Deploy] → Increment 2
Sprint 3: [Plan → Design → Code → Test → Deploy] → Increment 3
...
```

### Comparison Table

| Aspect | Waterfall | Agile |
|--------|-----------|-------|
| **Approach** | Sequential, linear | Iterative, incremental |
| **Requirements** | Fixed upfront | Evolving, flexible |
| **Delivery** | One big release at the end | Frequent small releases |
| **Testing** | After development phase | Continuous (every sprint) |
| **Customer Involvement** | Beginning and end | Throughout the project |
| **Change** | Expensive and difficult | Welcome and expected |
| **Risk** | High — discovered late | Low — discovered early |
| **Documentation** | Extensive, detailed | Lightweight, just enough |
| **Team Structure** | Specialized silos | Cross-functional |
| **Feedback** | Late | Continuous |
| **Best For** | Stable, well-defined requirements | Evolving, complex projects |

### Under the Hood: The Cost of Change

```
Cost of Change in Waterfall:
                                          ████
                                      ████
                                  ████
                              ████
                          ████
                      ████
                  ████
              ████
          ████
      ████
  ████
Requirements  Design  Development  Testing  Deployment
               (Cost increases exponentially over time)

Cost of Change in Agile:
  ████  ████  ████  ████  ████  ████
Sprint1  S2    S3    S4    S5    S6
               (Cost stays relatively flat)
```

---

## 10. Software Support and Maintenance

Once software is deployed, the work doesn't stop — in fact, **60-80% of a software product's total cost** is spent on maintenance, not the initial build. Maintenance includes fixing bugs users discover, updating the system when libraries or platforms change, improving performance, and cleaning up technical debt before it slows the team down. In Agile, maintenance is woven into regular sprints rather than treated as a completely separate activity.

### Types of Maintenance

| Type | Description | Example |
|------|-------------|---------|
| **Corrective** | Fixing bugs | Fixing a broken login flow |
| **Adaptive** | Adapting to environment changes | Upgrading to new OS version |
| **Perfective** | Improving performance/features | Optimizing database queries |
| **Preventive** | Preventing future problems | Refactoring technical debt |

### Maintenance in Agile

In Agile, maintenance is **not a separate phase** — it's part of every sprint:

```
Sprint Backlog:
  ├── New Feature Stories (60-70%)
  ├── Bug Fixes (15-20%)
  ├── Technical Debt (10-15%)
  └── Maintenance Tasks (5-10%)
```

### Service Level Agreements (SLAs)

```
Priority 1 (Critical): Response within 15 minutes, resolution within 4 hours
Priority 2 (High):     Response within 1 hour, resolution within 8 hours
Priority 3 (Medium):   Response within 4 hours, resolution within 24 hours
Priority 4 (Low):      Response within 8 hours, resolution within 72 hours
```

### Incident Management Flow

```
Incident Detected
       │
       ▼
   Triage & Classify (P1/P2/P3/P4)
       │
       ▼
   Assign to Team
       │
       ▼
   Investigate & Diagnose
       │
       ▼
   Implement Fix
       │
       ▼
   Test & Verify
       │
       ▼
   Deploy to Production
       │
       ▼
   Post-Incident Review (Retrospective)
```

---

## Quick Reference Summary

```
Agile = Iterative + Incremental + Collaborative
Scrum = Most popular Agile framework
Sprint = Fixed time-box (usually 2 weeks)
User Story = As a [user], I want [feature], so that [benefit]
Story Points = Relative estimation (Fibonacci: 1,2,3,5,8,13,21)
Velocity = Story points completed per sprint
Definition of Done = Checklist for "complete" work
```
