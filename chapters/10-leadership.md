# 10. Leading as an architect

[← Mobile system design](09-system-design.md) · [Contents](../README.md)

Senior and architect interviews always include behavioural questions. There are no model answers here, only what a strong answer shows. Prepare a real story from your own work for each, in the **situation, task, action, result** shape, with numbers where you have them.

---

### 🟡 Tell me about a technical decision you made that turned out wrong.

What it shows: you make decisions with incomplete information, notice when they're wrong, and fix them without blame. Cover what you knew at the time, the signal that told you it was wrong, how you changed course, and what you do differently now.

### 🟡 How do you review code?

Strong answers separate **must-fix** (correctness, security, data loss) from **suggestions** (naming, style), automate style with linters so reviews focus on design, keep reviews fast (same day), and treat reviews as teaching: explain the "why", and approve when it's good enough rather than perfect.

### 🔴 How do you handle technical debt?

Make it visible and economic, not moral: keep a list with the cost of each item (slower features, incidents, onboarding time), fix debt in the areas you're already changing, reserve a steady share of capacity for the highest-cost items, and say no to new debt without a written reason and an owner.

### 🔴 Two senior engineers disagree strongly on an approach. What do you do?

Get both options written down against the same criteria (risk, cost, reversibility, team skills). Often the disagreement is about unstated assumptions. Prototype the riskiest part if it's cheap. Then decide, record it in an ADR including the losing option, and commit together. Reversible decisions should be made fast; irreversible ones deserve more time.

### 🔴 How do you grow the engineers on your team?

Concrete habits: pair on the hard parts instead of taking them over, delegate whole problems (not tasks), give feedback quickly and specifically, create a clear path from developer to senior (what "senior" means in practice on this team), and give people visible ownership of components and decisions.

### 🔴 How do you estimate a large feature?

Break it into thin vertical slices that each deliver something testable, estimate the unknowns separately from the knowns (a short spike first for anything new, like a payment SDK or BLE device), give a **range** with the assumptions it depends on, and re-estimate as you learn. Track estimates against actuals to calibrate the team.

---

[← Mobile system design](09-system-design.md) · [Contents](../README.md)
