---
name: bash-error-diagnose
description: Use this skill whenever a user reports an error after running a bash command that Claude provided. Triggers when the user pastes an error message, says "it failed", "got an error", "not working", or similar after running a terminal command. Claude should NOT guess or immediately suggest fixes — instead, follow this skill to run a structured iterative diagnosis using the ask_user_input interface to collect real output from the user's machine before giving a fix.
---

# Bash Error Diagnosis Skill

## When This Skill Applies

Triggered when:
- User ran a bash/terminal command Claude gave them
- User reports an error, failure, or unexpected output
- User pastes an error message or stack trace

---

## The Core Loop

### Step 1 — Understand the Error

Read what the user reported. Identify:
- What command failed
- What the error message says (permission denied, not found, port in use, service down, etc.)
- The likely cause category (permissions, missing dependency, wrong path, config issue, etc.)

---

### Step 2 — Iterative Diagnosis (one command at a time)

Pick **ONE** focused, safe diagnosis command based on what you know so far. Tell the user:

> "Run this command and paste the output:"

Then use `ask_user_input_v0` to collect the result.

**After receiving the output:**
- If the root cause is now clear → proceed to Step 3
- If more info is needed → formulate the **next** diagnosis command based on what you just learned, and repeat Step 2

**Keep iterating until you are confident about the root cause.** Never give multiple diagnosis commands at once — always one, collect result, then decide next.

Common diagnosis commands by error type:
- Service issues: `sudo systemctl status <service>`
- File/path issues: `ls -la <path>`
- Port conflicts: `sudo ss -tlnp | grep <port>`
- Permission issues: `ls -la <file> && whoami`
- Python/pip issues: `pip show <package> 2>&1` or `which python3`
- Log inspection: `sudo journalctl -u <service> -n 30 --no-pager`
- Process check: `ps aux | grep <process>`
- Disk/memory: `df -h` or `free -h`

---

### Step 3 — Confirm Fixability

Before giving the fix, assess:
- Is this solvable with a bash command on the user's machine?
- Does it require something external (missing API key, broken upstream service, hardware failure)?
- Is the fix reversible or potentially destructive?

**If NOT fixable via bash:** Tell the user clearly what the blocker is and what they need to do externally. Do not give a fake fix.

**If fixable:** Proceed to Step 4.

---

### Step 4 — Give a Targeted Fix

- Give **one exact fix command** — no vague suggestions
- Explain in one line **why** this fixes the root cause
- If the fix is destructive or irreversible, **warn the user first** before they run it

---

### Step 5 — Always Verify

After the user runs the fix, **always** use `ask_user_input_v0` to ask:

> "Run the original command again and paste the result — let's confirm it's fixed."

Based on their response:
- ✅ Fixed → confirm and close the loop
- ❌ Still failing → return to Step 2 with the new output as fresh context

---

## Rules

- **Never guess a fix without diagnosis output.** Always collect real data first.
- **One diagnosis command at a time.** Decide the next step based on the result.
- **Always confirm fixability** before giving a fix — don't suggest unfixable things.
- **Always verify after the fix** — never assume it worked.
- **Fixes must be concrete.** No "you might want to try..." — give the exact command.
- **Ubuntu/Linux assumed** unless user specifies otherwise.
