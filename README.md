# Custom Claude Skills

A collection of custom skills for Claude that enhance its capabilities in specific workflows and interaction patterns.

## What are Claude Skills?

Claude skills are structured instruction sets that teach Claude how to handle specific types of tasks more effectively. Each skill is a markdown file that triggers automatically when relevant and guides Claude through optimized workflows.

## Skills in this Repository

### 🔧 bash-error-diagnose

**Purpose:** Systematic debugging of bash command failures through iterative diagnosis.

**Triggers:** When a user reports errors from terminal commands Claude provided.

**What it does:**
- Runs structured diagnosis using real output from the user's machine
- Iteratively collects data before suggesting fixes (never guesses)
- Confirms fixes work after implementation
- Handles permission issues, service failures, port conflicts, and more

**Use case example:** User runs `systemctl start nginx` and gets an error → Claude diagnoses step-by-step using targeted commands instead of guessing.

---

### 💡 brainstorming

**Purpose:** Structured idea generation using proven techniques.

**Triggers:** "help me think of", "I need ideas", "brainstorm", "what are some options", or any open-ended problem needing creative exploration.

**What it does:**
- Selects appropriate brainstorming technique (Classic Divergence, SCAMPER, Reverse Brainstorming, How Might We, Forced Connections, Six Thinking Hats)
- Generates categorized ideas: core, unconventional, quick wins
- Tailors output to constraints (time, budget, technical complexity)
- Offers next-step iterations: expand specific ideas, stress-test top options, or reframe the problem

**Use case example:** "Help me think of ways to monetize my side project" → generates structured ideas across practical, unconventional, and quick-win categories.

---

### 🏛️ council

**Purpose:** Multi-perspective adversarial debate using expert personas.

**Triggers:** "council mode", "debate this", "devil's advocate", "argue all sides", or high-stakes decisions needing thorough vetting.

**What it does:**
- Assembles 4-8 expert personas with distinct lenses (Pragmatist, Skeptic, Visionary, Risk Officer, plus unusual characters like Hans the German Overengineer, The Lunatic, The Chaos Monkey)
- Runs structured debate rounds: opening positions, cross-examination, synthesis, vote
- Delivers a verdict with consensus level, reasoning, minority views, and risk factors
- All personas maintain factual accuracy while bringing unique perspectives

**Use case example:** "Should I switch from Python to Rust for my backend?" → Council debates from performance, maintainability, team knowledge, hiring, and ecosystem perspectives before delivering a verdict.

---

### ⏯️ resume-interrupted

**Purpose:** Continue Claude responses that were cut off or interrupted mid-generation.

**Triggers:** "resume", "continue from where you stopped", "response got cut off", "retry smartly", or when generation fails.

**What it does:**
- Reconstructs Claude's original reasoning and planned structure
- Locates exact interruption point (mid-sentence, mid-section, mid-file)
- Recovers partially written files without restarting
- Continues seamlessly in the same style, tone, and format
- Saves tokens and time by not regenerating completed content

**Use case example:** Claude is generating a 500-line React component, network drops at line 250 → say "resume" and it picks up from line 250 instead of restarting.

---

## Installation

### Option 1: Clone the Repository

```bash
git clone https://github.com/yourusername/claude-custom-skills.git
cd claude-custom-skills
```

Then upload the skill files to your Claude environment according to [Anthropic's skill documentation](https://docs.claude.com).

### Option 2: Download Individual Skills

Browse to the skill you want and download its `SKILL.md` file directly.

---

## How to Use

1. **Upload skills** to your Claude skills directory (typically `/mnt/skills/user/` in Claude environments that support custom skills)
2. **Skills trigger automatically** when Claude detects relevant patterns in your requests
3. **No special commands needed** — just interact naturally. For example:
   - Run a command, get an error, paste it → bash-error-diagnose triggers
   - Say "I need ideas for X" → brainstorming triggers
   - Say "debate whether I should do X" → council triggers
   - Response gets cut off, say "resume" → resume-interrupted triggers

---

## Skill Structure

Each skill follows this format:

```markdown
---
name: skill-name
description: When this skill triggers and what it does
---

# Skill Title

[Detailed instructions for how Claude should execute this skill]
```

The `description` field is critical — it defines the trigger patterns that activate the skill.

---

## Contributing

Found a bug? Have an improvement? Want to add a new skill?

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/improved-debugging`)
3. Make your changes
4. Test the skill with Claude
5. Submit a pull request with:
   - Clear description of what changed
   - Example interactions showing the skill working
   - Any new trigger patterns added

---

## Tips for Creating Your Own Skills

1. **Narrow the trigger scope** — overly broad triggers cause unwanted activation
2. **Make instructions concrete** — "use 3-5 diagnosis commands" is clearer than "diagnose thoroughly"
3. **Include examples** — showing Claude what good output looks like improves consistency
4. **Test edge cases** — what happens if the user's input is malformed or incomplete?
5. **Maintain factual accuracy** — skills can guide style and process, but never compromise truthfulness

---

## Known Limitations

- Skills work best in Claude environments with extended thinking capabilities
- Trigger patterns may need tuning based on your usage patterns
- Very complex multi-skill workflows may require explicit skill selection

---

## License

MIT License - see [LICENSE](LICENSE) file for details.

---

## Acknowledgments

Skills inspired by real-world Claude usage patterns and community feedback. Special thanks to users who identified workflow gaps that these skills address.

---

## Questions?

- **How do I know which skill triggered?** Look for structured output patterns (e.g., council shows "🏛️ COUNCIL ASSEMBLED")
- **Can I modify these?** Yes! Fork and adapt to your needs.
- **Do skills work with Claude API?** Skills are designed for Claude.ai environments with custom skill support.

---

**Version:** 1.0  
**Last Updated:** May 2026  
**Maintained by:** [Ruthvik-Anne]
