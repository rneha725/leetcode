## How to understand a problem
- Take some time to just write down brute force, it will make you understand the problem better which can guide you intuition
- Check the constraints
- Understand the problem pattern
- Optimize and solve

## When you are stuck while solving a problem on Leetcode

**1. You have the approach but the code is buggy**

Stop staring at the code. Trace through a small example by hand, step by step, printing what each variable actually holds. The bug almost always shows up in 2 minutes of tracing vs 20 minutes of staring.

---

**2. You have a partial idea but can't complete it**

Write down in plain English what you *do* know:
- What's the subproblem?
- What are you trying to compute at each step?
- What information do you need to carry forward?

Often just articulating it unblocks you. If it doesn't, that's fine — see below.

---

**3. Completely stuck, no idea where to go**

Give yourself a hard cutoff — **30-45 minutes total** on a medium, not more. After that, look at the solution. But don't just read it — close it after 2 minutes and **re-implement from memory**. This is the highest value thing you can do. Reading and understanding feels like learning, but it isn't. Re-implementing forces actual encoding.

---

**4. After looking at the solution**

This is the most important step that most people skip:

- Write down **one sentence** — the core insight that you were missing. Not the code, the *idea*.
- Ask yourself: "what was the mental model I had that was wrong, and what's the correct one?"

For you with coin change — the insight was *"outer loop is amounts, not coins"*. That one line is what should've been in your notes 4 months ago.

---

**The meta-rule:**

Being stuck halfway and looking at the solution is not failure — solving it, not extracting the insight, and forgetting it in 4 months is. The note you write after is more valuable than the solve itself.
