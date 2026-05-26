---
name: brainstorming
description: >
  Structured idea generation for any topic using proven brainstorming techniques.
  Use this skill whenever the user wants to brainstorm, ideate, generate options,
  explore possibilities, or think creatively about a problem — even if they don't
  use the word "brainstorm". Trigger on: "help me think of", "what are some ideas
  for", "I need ideas", "let's explore", "how might I", "what could I do about",
  "generate options for", "come up with X", or any vague open-ended problem that
  needs divergent thinking before a decision. Also trigger when the user is stuck,
  describing a challenge without a clear path, or asking "what should I do about X".
---

# Brainstorming Skill

Helps users generate, organize, and evaluate ideas using structured techniques
tailored to the problem type and context.

---

## Step 1 — Understand the Problem Space

Before generating ideas, quickly identify:

| Factor | What to detect |
|---|---|
| **Domain** | Tech, product, creative, personal, business, academic, etc. |
| **Constraint level** | Fully open ("any ideas?") vs. constrained ("within ₹5K budget") |
| **Goal** | Generate many options? Explore one direction deeply? Solve a specific blocker? |
| **Output need** | Raw idea dump? Categorized list? Actionable next steps? |

If the user's prompt is too vague to generate useful ideas, ask **all relevant
clarifying questions upfront** — don't drip-feed them one at a time. For each
question, present the possible options and briefly explain what each option
changes about the output. Example:

> **1. What's the main constraint?**
> - *Time* — I'll prioritize quick-to-execute ideas
> - *Budget* — I'll filter out anything expensive
> - *Technical complexity* — I'll focus on feasible-now options
>
> **2. What kind of output do you want?**
> - *Raw idea dump* — quantity over quality, no structure
> - *Categorized list* — grouped by theme or effort level
> - *Actionable plan* — fewer ideas but with next steps

If the context is already clear enough, skip clarification and dive straight in.

---

## Step 2 — Select Technique(s)

When the user hasn't specified how they want to brainstorm, **present all
applicable techniques with a brief explanation of each**, so they can pick the
one that fits their mindset right now. Format it like:

> Here are the approaches I can use — pick one or combine:
>
> **🔀 Classic Divergence** — broad idea dump, quantity-first. Best when you want
> many options before narrowing down.
>
> **🔄 SCAMPER** — systematically improves something that already exists.
> Best when you have a starting point and want to evolve it.
>
> *(etc.)*

If the user has already signaled a preference (e.g., "wild ideas only", "practical
options", "help me think through this decision"), skip the menu and apply the
matching technique directly.

---

### Full Technique Reference

Pick the technique(s) that fit the problem. Combine when useful.

### 🔀 Classic Divergence (default for open-ended)
Generate a broad list of ideas quickly. Aim for quantity over quality. Include
wild/impractical ideas — they often spark the best ones. Group loosely by theme.

### 🔄 SCAMPER (for improving something existing)
Apply these lenses to the subject:
- **S**ubstitute — What can be replaced?
- **C**ombine — What can be merged?
- **A**dapt — What can be borrowed from elsewhere?
- **M**odify/Magnify — What can be changed, exaggerated, or minimized?
- **P**ut to other uses — How else could this be used?
- **E**liminate — What can be removed?
- **R**everse/Rearrange — What happens if you flip it?

### 🔁 Reverse Brainstorming (for breaking out of stuck thinking)
Instead of "how do I solve X?", ask "how would I make X worse / guarantee failure?"
Then flip each answer into a solution. Useful when the user is blocked.

### 🌐 How Might We (HMW) — (for design/product/UX problems)
Reframe the problem as a series of "How might we…?" questions, then answer each.
Keeps ideas possibility-focused rather than constraint-focused.

### 🧩 Forced Connections (for creativity blocks)
Pick a random unrelated domain (nature, cooking, sports, architecture). Force an
analogy with the user's problem. "If this were a kitchen problem, what would the
solution look like?"

### 📊 Six Thinking Hats (for decisions with competing perspectives)
Briefly run through: facts (white), optimism (yellow), caution (black), emotions
(red), creativity (green), process (blue). Good for "should I do X?" questions.

---

## Step 3 — Generate Ideas

Structure the output clearly:

```
## Brainstorm: [Topic]

### Core Ideas
[5–10 solid, directly applicable ideas]

### Unconventional / Wildcard Ideas
[2–4 ideas that are unusual, risky, or out-of-the-box]

### Quick Wins (if applicable)
[Ideas that are low-effort, fast to implement]

### Next Step Suggestions
[2–3 concrete actions the user could take right now to move forward]
```

Adjust depth based on scope:
- Narrow/specific question → 5–8 ideas, focused
- Broad/open question → 10–15 ideas, across categories
- "Help me decide" → fewer ideas + brief pros/cons

---

## Step 4 — Invite Iteration

End by presenting **all the meaningful ways to go deeper**, each with a one-line
explanation of what it produces. Example:

> Want to go further? Here's what I can do next:
> - **Expand a specific idea** — I'll flesh out any one of these in full detail
> - **Reverse brainstorm the top 3** — stress-test them by asking "how would this fail?"
> - **Make a decision** — I'll compare the best options and recommend one with reasoning
> - **Reframe the problem** — if none of these feel right, I'll reframe with HMW and try again

This gives the user a menu to click through rather than guessing what to ask next.

---

## Output Principles

- **Concrete over abstract**: "Build a waitlist page with a referral counter" > "create buzz"
- **Labeled clearly**: Group ideas so the user can scan fast
- **Honest about tradeoffs**: Flag if an idea has a significant downside, briefly
- **Calibrated to context**: A student brainstorming a college project ≠ a startup founder brainstorming a product pivot — adjust scope, tone, and depth accordingly
- **Ruthvik-aware**: If domain context is available (e.g., Android dev, backend, CSE student), skew examples and suggestions toward that world unless the topic is clearly unrelated

---

## Edge Cases

**"Just give me ideas, no structure"** → Give a flat numbered list, skip headers.

**User wants ideas AND a decision** → Generate first, then briefly recommend top 1–2 with a reason.

**Very constrained problem** (e.g., "₹500 budget, 2 hours, Andhra Pradesh")  
→ Filter aggressively. Flag ideas that technically violate constraints but are worth
considering if constraints relax.

**Abstract/philosophical topic** → Use HMW or forced connections to make it generative.
Avoid vague platitudes.
