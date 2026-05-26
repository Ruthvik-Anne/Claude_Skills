---
name: resume-interrupted
description: >
  Resume a Claude response that was cut off, interrupted, or failed mid-generation.
  Trigger this skill whenever the user says "resume", "continue from where you left off",
  "retry smartly", "pick up where you stopped", "response got cut off", "got interrupted",
  "continue the previous response", or pastes a partial/truncated Claude response and asks
  to continue it. Also trigger when the user says something like "it failed again" or
  "interrupted again" after a generation error. The goal is to continue seamlessly without
  restarting from scratch, saving tokens and time.
---

# Resume Interrupted Response

## Purpose
When a Claude response is interrupted (tab switch, network drop, generation failure), this
skill lets you pick up exactly where generation stopped — without restarting the full response.

## How to use
Tell Claude any of these:
- `resume`
- `continue from where you stopped`
- `response got cut off` (paste partial if you have it)
- `retry smartly`

Optionally paste the partial response Claude had generated before the interruption.

---

## Claude Instructions

When this skill is triggered, follow this exact workflow:

### Step 1 — Reconstruct the hidden line of thought

Before looking at the partial response, re-derive Claude's reasoning from context:

1. **Re-read the original user request** carefully — what was the full intent?
2. **Check for any visible thinking/reasoning blocks** in the interrupted turn (shown as collapsed "thinking" sections). Extract the key decisions made: approach chosen, structure planned, constraints identified.
3. **Infer the planned structure** from what was already generated — e.g. if 3 of 5 sections exist, the plan was clearly 5 sections.
4. **Reconstruct the mental model**: What assumptions was Claude operating under? What order was content being generated in? What style/depth was being used?

This step is critical — without it, resumed content will feel disconnected from what came before.

### Step 2 — Locate the interruption point

Check the conversation context for:
1. A partial Claude response (visible in conversation or pasted by the user)
2. The last complete sentence or logical unit in that partial
3. Any partially generated files (from bash_tool, create_file, or str_replace calls)
4. The original user request that started the interrupted response

### Step 3 — Recover any interrupted file generation

If the interrupted response was generating files (code, documents, cheat sheets, etc.):

1. **Check what files were created** before the interruption using `bash_tool`:
   ```bash
   ls -la /home/claude/        # check working directory
   ls -la /mnt/user-data/outputs/  # check outputs
   ```
2. **Read the partial file content** to see how far generation got
3. **Identify remaining content** — what sections/functions/pages are missing based on the original request
4. **Continue the file from where it stopped** — do NOT recreate the full file from scratch
5. If the file is corrupt or truncated mid-write, reconstruct only the missing portion and append/merge

### Step 4 — Identify what remains in the response

From the original request, reconstructed thought, and what was already generated:
- What topics/sections were already covered (do NOT regenerate)
- Where exactly generation stopped (mid-sentence? mid-section? mid-list? mid-file?)
- What still needs to be generated to fully satisfy the original request

### Step 5 — Continue seamlessly

- If stopped **mid-sentence**: complete that sentence first, then continue
- If stopped **mid-section**: finish that section, then remaining sections
- If stopped **mid-list**: continue from the next item
- If stopped **mid-file**: append the missing content to the existing file
- If partial is unavailable: infer from conversation history and continue from next logical point

**NEVER restart from the beginning.** Do not repeat already-generated content.
**Do not add preamble** like "Continuing from where I left off..." — just continue directly.
**Match the style, format, depth, and tone** of the interrupted response exactly.
**Use the same reasoning approach** that was being used before interruption.

### Step 6 — Signal completion

At the very end, add a subtle marker only if the original response is now complete:
`[Response complete]`

If more content is still needed but you've reached a natural break, end with:
`[Pausing here — say "resume" to continue]`

---

## Edge Cases

**User has no partial text (just says "resume"):**
Look at the last assistant turn in conversation history. If it ends abruptly or mid-thought, continue from there. If unclear, ask: "I see the previous response — should I continue from [last topic covered]?"

**Thinking block was interrupted:**
If the reasoning/thinking was cut off before any response was generated, reconstruct the reasoning from scratch using the original request, then generate the response. Don't mention the failed thinking block.

**File was partially written then interrupted:**
Read what exists, identify the gap, write only the missing portion. Merge or append as needed. Re-present the completed file to the user.

**Response was fully generated but user thinks it was cut off:**
Confirm: "It looks like the response was fully generated. Is there a specific part you'd like me to expand?"

**Multiple interruptions in a row:**
Treat each "resume" as continuing from the latest stopping point, accumulating all segments.

**Original request was very long/complex:**
After resuming, offer to break the remaining content into smaller chunks to avoid future interruptions.
