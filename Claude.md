# claude.md — Ruthvik's Rules for Claude

> Last synced: 2026-05-26.

---

## Priority Order

When rules conflict, this order wins:

1. **Don't break working code** — stability beats cleanliness
2. **Do only what was asked** — scope beats completeness
3. **Read as little as possible** — efficiency beats thoroughness
4. **Structure scales to complexity** — pragmatism beats perfection

---

## 0. Operating Principles

These are the *why* behind every rule. When an edge case isn't covered, reason from these.

| Principle | Statement |
|---|---|
| **Surgical** | Touch only what the task requires. Everything else is frozen. |
| **Minimal** | Smallest diff that satisfies the requirement. No bonus improvements. |
| **Declarative** | State what will change before changing it. |
| **Self-evident** | A path must describe its file. No search needed in a well-structured project. |
| **Scaled** | Structure matches project size. Never over-architect upfront. |

---

## 1. Before You Act — Every Task

```
1. Identify the target file(s) from the request.
2. State them explicitly: "I will modify X and Y."
3. Go directly to those files. Do not read adjacent, parent, or index files first.
4. If the location is uncertain → read index.md once.
5. Still uncertain → ask the user. Do not keep exploring.
6. Make the change. Nothing outside the declared set is touched.
```

**Hard limits:**
- Maximum 2 reads to orient. After that, ask.
- Never read a file "just to check" if it wasn't in the declared set.
- Never open `index.md`, `dependencies.md`, or `update_log.md` as a ritual — only when lost.

---

## 2. Modification Rules

### Frozen-by-Default
Code not mentioned in the request is **frozen**. This means:
- No variable renames for style reasons
- No reformatting of untouched functions
- No folder restructuring as a side-effect

**Exception — Surface, Never Touch:**
If Claude discovers a bug or sees a valuable improvement while working:
1. Complete the requested task first.
2. In the postamble, flag it: `⚠️ Noticed: [what] in [file] — fix it?`
3. Do nothing until explicitly told to.

### Declare Before Acting
Before any edit, output:
```
Files I will change: [list]
```
Touching anything outside that list is a violation — stop and ask before proceeding.
If the list grows mid-task, pause and declare the addition.

### One Task, One Concern
A functional fix does not include a style cleanup.
A style cleanup does not include a logic change.
These are separate tasks. Do them separately if asked.

### No Cosmetic Bundling
Never bundle into one response:
- Bug fix + rename
- Feature + refactor
- "Fix X, and also I improved Y"

---

## 3. Code Naming Conventions

| Identifier | Convention | Example |
|---|---|---|
| Variables | `camelCase` (JS/TS/Java/Kotlin) · `snake_case` (Python/Rust/C) | `userAge`, `user_age` |
| Classes / Types / Interfaces | `PascalCase` | `UserProfile`, `AuthService` |
| Constants | `UPPER_SNAKE_CASE` | `MAX_RETRY_COUNT` |
| JS `const` (non-primitive) | `camelCase` | `const apiClient = ...` |
| Functions / Methods | verb-first | `fetchUser()`, `get_total()` |
| Booleans | `is` / `has` / `can` prefix | `isLoggedIn`, `hasPermission` |
| Files (web/TS/JS) | `kebab-case` | `user-profile.tsx` |
| Files (Python/Rust/C) | `snake_case` | `user_profile.py` |
| Packages / modules | `lowercase` no separators | `utils`, `auth` |

**Rules:**
- No single-letter vars except loop `i`/`j` (never `l`/`O`).
- No type suffixes — `userArray` → bad; `users` → good.
- No misleading names. No abbreviations unless universally understood (`url`, `id`, `api` are fine).
- Consistent verb families — don't mix `fetch`/`get`/`retrieve` for the same operation type.
- Names must answer: **why / what / how**.
- No cultural slang. Prefer full descriptive names over clever short ones.
- Initialisms in class names: prefer `XmlParser` over `XMLParser` unless the ecosystem disagrees.

---

## 4. Folder Structure

### The Five Core Rules

| Rule | Statement |
|---|---|
| **Flat first** | Start one level. Add nesting only when 3+ files share a clear sub-concern. |
| **Boring names** | `src/`, `tests/`, `config/`, `utils/`, `scripts/`, `docs/`, `public/`, `data/`, `assets/`. No creativity. |
| **One job per folder** | If it does two things, split it. |
| **Max 3 levels** | Below `src/`. Level 4 means your module boundaries are wrong. |
| **Path describes the file** | Reading the path alone must tell you what the file does. |

### Standard Layout

```
project-root/
├── src/              # all application source code
├── tests/            # mirrors src/ exactly
├── docs/             # documentation
├── scripts/          # build, deploy, maintenance (not app code)
├── assets/           # static resources
├── config/           # environment and app configuration
├── data/
│   ├── raw/          # immutable originals — never modify
│   ├── processed/    # transformed / cleaned
│   └── final/        # output-ready
├── notebooks/        # exploratory scratch — never in src/
└── README.md
```

### Tests Mirror Source

```
src/auth/login.py     →   tests/auth/test_login.py
src/utils/format.ts   →   tests/utils/format.test.ts
```

### Where Does This File Go?

```
Config / env vars?          → src/config/
Test?                       → tests/<mirror of src path>
Raw input data?             → data/raw/
Processed data?             → data/processed/
Final output?               → data/final/
Static asset?               → public/ or assets/
Build / deploy script?      → scripts/
Stateless pure helper?      → src/utils/
Feature-specific logic?     → src/modules/<feature>/
Core domain / business?     → src/core/
Exploratory / scratch?      → notebooks/
Documentation?              → docs/
Nothing fits?               → Fix the structure, not the file.
```

### Naming

| Rule | Detail |
|---|---|
| ISO dates | `2026-05-26_report.md` |
| Versions | `_v1`, `_v2` — never `_final`, `_new`, `_fixed` |
| Zero-pad | `01_`, `02_` for numbered sequences |
| No spaces | Anywhere in paths or filenames |
| Folders | `snake_case` |
| Web assets | `kebab-case` — `hero-banner.svg` |
| Abbreviations | Document all non-obvious ones in root `README.md` |

---

## 5. Documentation — Tiered by Project Size

### Tier 1 — Small (< ~10 files)
- Root `README.md` only. No index files required.

### Tier 2 — Medium (multiple modules)
- Root `README.md`
- Per-module `index.md` (on request or when a module exceeds ~5 files)

### Tier 3 — Large / Team
Full IX system applies:

**`index.md`** — per folder, lists all files with description, purpose, inputs, outputs, functions.  
**`dependencies.md`** — per folder, lists internal imports, external packages, shared state.  
**`update_log.md`** — append-only. Format: `YYYY-MM-DD — <requirement> — files: <list> — reason: <why>`.

**Key constraint:** Never auto-generate these files as a side-effect of a coding task. Only create/update them when explicitly asked or when maintaining a Tier 3 project.

#### `index.md` File Block Template

```md
### `filename.ts`

**Description:** <what this file is and why it exists>
**Purpose:** <the single job this file does>
**Inputs:** <data/args received — mention key fields>
**Dependencies:** <internal modules, external packages, shared state>
**Outputs:** <what it returns, emits, or writes>

**Functions:**

| Function | Description |
|---|---|
| `fetchUser(id)` | Fetches a user record from DB by ID |
```

---

## 6. General

- Relative paths everywhere. No hardcoded absolute paths.
- Delete empty folders immediately. No placeholders.
- Scratch work → `notebooks/` (gitignored unless under review).
- `README.md` mandatory at root before first commit.
- Raw data files are immutable. Never modify `data/raw/`.
- Data pipeline is one-way: `raw/` → `processed/` → `final/`.

---

## 7. Output Format — Claude Code

**Preamble (required, max 2 lines):**
State what file(s) change and why. Nothing else.

✅ `Fixing null check in auth.py — add early return before token parse.`  
❌ `Sure! I'll take a look at your code and help you fix the issue you mentioned.`

Never open with affirmations: "Sure", "Great", "Of course", "Absolutely", "Happy to".

---

**Postamble (required, every response):**

```
✅ Done: [file:line-range] — [one line what changed]
🧪 Test: [exact thing to verify — omit if trivially obvious]
⚠️ Noticed: [bug/improvement found, untouched — omit if none]

🔍 Drift check:
  Scope    → only declared files touched?           [yes/no]
  Format   → preamble ≤ 2 lines, no affirmations?  [yes/no]
  Patch    → diff not full-rewrite when < 40%?      [yes/no]
  Comments → only where permitted (§9)?             [yes/no]
  Finds    → discoveries flagged, not acted on?     [yes/no]
```

Any `no` → state what drifted and why before ending the response.

---

## 8. Patch vs Rewrite Rule

- Count lines your change touches vs total file lines.
- **≤ 40% touched** → output ONLY the changed block(s) with 3 lines of context above and below. Never output unchanged code beyond those 3 lines.
- **> 40% touched** → full file rewrite is permitted. State the percentage first.

❌ Bad: Rewriting 400 lines because one function signature changed.  
✅ Good: Output the changed function + 3 lines above/below. Nothing else.

---

## 9. Comment Policy

Comments are permitted in exactly three cases:
1. A non-obvious algorithm where the *why* cannot be inferred from variable/function names.
2. An external API contract or constraint not visible in the code.
3. A `// TODO: <description>` with a concrete next action.

Everything else: no comment. If the name needs a comment, rename it.

❌ Bad: `// increment counter` above `count++`  
❌ Bad: JSDoc on every function by default  
✅ Good: A comment explaining why a sort is descending due to an upstream API quirk

---

## 11. Execution-Oriented Development

When touching external systems (APIs, DBs, CLIs, SDKs, file I/O, env vars):
**never assume — probe first, build after.**

### The Protocol

**Step 1 — Pre-Probe Declaration (1–2 lines before any code):**
State exactly what the probe tests and what a passing result looks like.
> `Probing: POST /auth/token with client_credentials grant.`
> `Pass: 200 + JSON body containing access_token field.`

**Step 2 — Write the Probe**
- Maximum 20 lines. Single concern only.
- Save to `notebooks/probe_<topic>.py` (or language equivalent) — never in `src/`.
- No error suppression. Let it fail loudly.
- If probe exceeds 20 lines, state why inline before proceeding.

**Step 3 — Run It**
Use bash tool. Run immediately. Do not ask permission.

**Step 4 — Analyze Full Output**
Read ALL of: status code + response body + error fields inside success envelopes.
> ❌ Bad: "Got 200, proceeding."
> ✅ Good: "Got 200, body contains `access_token`, no `error` field present. Pass."

**Step 5 — Decide**

| Output | Action |
|---|---|
| Clear pass | Delete probe file. Proceed to implementation. |
| Network / auth error with obvious fix | Apply fix, retry once. Max 2 attempts total. |
| Unexpected response shape | STOP. Show raw output. Ask user before proceeding. |
| Ambiguous result | STOP. Show raw output. Ask user before proceeding. |

**Hard limits:**
- Retry ceiling: 2 attempts. After 2, always halt and surface.
- Never retry on unexpected business logic — that's a user decision.
- Never assume sandbox behavior == production behavior. State the environment tested.
- Probe files are throwaway. Delete after analysis unless user asks to keep them.

---

## 12. Context Reuse

- If a file's content was already shown in this conversation, do not read it again. Use what's in context.
- If a variable, schema, or function was defined earlier in this session, do not re-derive it.
- "I'll check the file to be sure" is a rules violation. Trust context.

❌ Bad: Reading `config.py` again after it was shown two messages ago.  
✅ Good: Referencing the already-visible `DB_URL` constant directly.
