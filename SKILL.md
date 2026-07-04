---
name: value-realization-slide
description: "Create a Snowflake-branded value realization HTML slide from any GitHub repository. Reviews the repo's documentation (README, docs/, examples/) to extract the business problem, solution approach, Snowflake capabilities used, and key outcomes — then generates a polished, print-ready slide saved to ~/Downloads AND writes the extracted data + HTML to AFE.TEST.VALUE_REALIZATION_CATALOG in Snowflake. Use when: building value realization slides, creating exec-ready leave-behind decks, generating value slides from a GitHub repo, summarizing a project for a customer pitch, building a one-pager from a repo, documenting what a Snowflake project does and why it matters. Triggers: value realization slide, value slide, create a slide from repo, generate slide from github, value realization from github, slide from repo, one-pager from repo, snowflake branded slide, pitch slide from code, value deck, leave-behind from github, vr slide."
---

# Value Realization Slide Generator

Creates a single Snowflake-branded HTML value realization slide from a GitHub repo's documentation.
Output is saved to `~/Downloads/<repo-name>-value-realization.html`, opened automatically, and all extracted data + HTML is written to `AFE.TEST.VALUE_REALIZATION_CATALOG` in Snowflake.

---

## Reference Files

Before running any step, note the paths to supporting files:

- **Slide template:** `~/.snowflake/cortex/skills/value-realization-slide/references/slide-template.html`
- **Value pillars:** `~/.snowflake/cortex/skills/value-realization-slide/references/snowflake-value-pillars.md`

---

## Catalog Table

**`AFE.TEST.VALUE_REALIZATION_CATALOG`** — Step 6 writes to this table after the slide is generated.

Schema (for reference):

| Column | Type | Notes |
|--------|------|-------|
| REPO | VARCHAR(255) PK | repo name slug (e.g. `coco-daily-routines`) |
| PROJECT_NAME | VARCHAR(500) | formatted title |
| PERSONA | VARCHAR(255) | target persona label |
| TAGLINE | VARCHAR(1000) | one-line description |
| SUMMARY | VARCHAR(4000) | summary sentence |
| CHALLENGE_HEADING | VARCHAR(500) | challenge panel heading |
| CAPABILITY_HEADING | VARCHAR(500) | capabilities panel heading |
| OUTCOME_HEADING | VARCHAR(500) | outcomes panel heading |
| CHIPS | ARRAY | capability chip labels |
| BULLETS | ARRAY | challenge bullet text (plain text, no HTML) |
| OUTCOMES | ARRAY | outcome item titles (plain text, no HTML) |
| NEXT_STEPS | ARRAY | next step text (plain text, no HTML) |
| URL | VARCHAR(1000) | full GitHub URL |
| RAW_URL | VARCHAR(1000) | raw base URL used for fetching |
| SLIDE_HTML | VARCHAR(16777216) | complete rendered HTML |
| LAST_UPDATED_AT | TIMESTAMP_NTZ | auto-set to CURRENT_TIMESTAMP |

---

## Workflow

### Step 1: Parse the Repo URL

**Input:** The GitHub URL provided by the user. Acceptable formats:
- `https://github.com/org/repo`
- `https://github.com/org/repo/tree/branch`
- `github.com/org/repo`

**Actions:**

1. Extract `<org>` and `<repo>` from the URL.
2. Determine the default branch — try `main` first, fall back to `master`.
3. Construct the raw content base URL: `https://raw.githubusercontent.com/<org>/<repo>/<branch>/`

**Output:** `org`, `repo`, `branch`, `raw_base` variables for use in Step 2.

---

### Step 2: Fetch Documentation

**Actions:**

1. Use the GitHub Contents API to list all root-level files:
   ```
   https://api.github.com/repos/<org>/<repo>/contents
   ```
   Run this via bash curl and parse the JSON to find all `.md` files in the root.

2. Fetch these files (if they exist) using bash curl — fetch in priority order:
   - `README.md` (always try first — highest priority)
   - `SKILL.md` (if it's a CoCo skill repo)
   - `docs/README.md` or `docs/index.md`
   - `CONTRIBUTING.md`
   - `examples/README.md`
   - Any other `.md` files found in the root listing

3. For each file, use:
   ```bash
   curl -s "https://raw.githubusercontent.com/<org>/<repo>/<branch>/<file>"
   ```

4. Also fetch the repo metadata for description:
   ```bash
   curl -s "https://api.github.com/repos/<org>/<repo>"
   ```
   Extract: `description`, `topics`, `language`, `stargazers_count`.

**Fallback:** If `main` branch returns 404, retry with `master`. If the repo is private or inaccessible, stop and ask the user to paste the README content directly.

**Output:** Combined documentation text (all fetched files concatenated with file-path headers).

---

### Step 3: Analyze the Documentation

Read the combined documentation and extract the following structured facts. Think carefully — infer from context when not explicitly stated.

**Extract:**

| Field | What to Look For |
|-------|-----------------|
| `project_name` | Repo name formatted as a readable title (e.g., `industry-account-classifier` → `Industry Account Classifier`) |
| `tagline` | The repo's one-line description (from GitHub API `description` field or README subtitle) |
| `summary_sentence` | One sentence (max 200 chars) describing what the project does and who it's for |
| `business_problem` | The pain point or manual process this project eliminates. Look for: "currently", "manual", "problem", "challenge", "bottleneck", "before" language |
| `challenge_bullets` | 3 specific pain points (1 line each). Be concrete, not generic |
| `solution_approach` | What the project actually does technically — the mechanism |
| `tech_stack` | Every Snowflake feature/product mentioned or implied (Cortex, Dynamic Tables, Streamlit, etc.) |
| `target_persona` | Who built or uses this (SE, data engineer, analyst, RevOps, etc.) |
| `outcomes` | Measurable or qualitative results. Look for: time saved, accuracy, scale, automation rate, cost |
| `next_steps` | 3 logical follow-on actions for the customer/SE to take after seeing this |

**Challenge Heading:** Write a 6–10 word problem framing sentence (e.g., "Manual account routing slows revenue operations at scale").

**Capabilities Heading:** Write a 6–10 word Snowflake-solution framing sentence (e.g., "AI-native classification running on Snowflake's data platform").

**Outcomes Heading:** Write a 6–10 word impact framing sentence (e.g., "Faster, more consistent decisions that scale with the business").

---

### Step 4: Map to Snowflake Value Pillars

Read `~/.snowflake/cortex/skills/value-realization-slide/references/snowflake-value-pillars.md`.

**Actions:**

1. Scan the `tech_stack` and `solution_approach` extracted in Step 3 against the trigger keywords in the pillars table.
2. Select the **top 2–4 pillars** with the most keyword matches.
3. For each selected pillar, pick 2–3 specific chip labels from the "Chip Labels" column that actually appear in the project (not generic ones).
4. Select the `target_persona` label from the Persona Labels section.
5. Select outcome icons from the Outcome Icons table — one icon per outcome item.

**Output:** Final list of capability chip labels + persona label + outcome icons.

---

### Step 5: Generate the HTML Slide

Read `~/.snowflake/cortex/skills/value-realization-slide/references/slide-template.html`.

**Actions:**

1. Replace every `{{PLACEHOLDER}}` in the template with the generated content:

   | Placeholder | Content |
   |------------|---------|
   | `{{PROJECT_NAME}}` | Formatted project name |
   | `{{TAGLINE}}` | One-line tagline |
   | `{{SUMMARY_SENTENCE}}` | One-sentence summary |
   | `{{PERSONA}}` | Target persona badge label |
   | `{{DATE}}` | Today's date formatted as `Mon YYYY` (e.g., `Jul 2026`) |
   | `{{CHALLENGE_HEADING}}` | Challenge section heading |
   | `{{CHALLENGE_BULLETS}}` | 3x `<li>` elements (no wrapper `<ul>` — the template provides it) |
   | `{{CAPABILITIES_HEADING}}` | Capabilities section heading |
   | `{{CAPABILITY_CHIPS}}` | 4–8x `<span class="chip">Label</span>` elements |
   | `{{CAPABILITIES_HEADING}}` | Capabilities section heading |
   | `{{OUTCOMES_HEADING}}` | Outcomes section heading |
   | `{{OUTCOME_ITEMS}}` | 3x `<div class="outcome-item">` blocks (use the format from the template comments) |
   | `{{NEXT_STEPS}}` | 3x `<div class="step">` blocks (use the format from the template comments) |

2. Remove all HTML comment blocks (`<!-- Example format ... -->`) from the filled template — they are scaffold notes only.

3. Write the completed HTML to:
   ```
   ~/Downloads/<repo-name>-value-realization.html
   ```
   Use bash:
   ```bash
   cat > ~/Downloads/<repo-name>-value-realization.html << 'HTMLEOF'
   <filled html content>
   HTMLEOF
   ```

4. Open the file in the default browser:
   ```bash
   open ~/Downloads/<repo-name>-value-realization.html
   ```

**Output:** Confirmation of the file path + a compact markdown summary of what was extracted and placed in the slide.

---

### Step 6: Write to Snowflake Catalog

MERGE the extracted data and full HTML into `AFE.TEST.VALUE_REALIZATION_CATALOG`.
Use the `REPO` slug (repo name only, no org prefix) as the match key — re-running the skill on the same repo updates the existing row.

**Actions:**

1. Build plain-text versions of the array fields (strip any HTML tags):
   - `chips_plain` — list of chip label strings (e.g. `["Cortex Code (CoCo)", "CoCo Skills", ...]`)
   - `bullets_plain` — list of challenge bullet strings (plain text, no `<li>` tags)
   - `outcomes_plain` — list of outcome title strings (e.g. `["Seconds Per Account", ...]`)
   - `next_steps_plain` — list of next step strings (plain text, no `<div>` tags)

2. Execute a MERGE using the `snowflake_sql_execute` tool:

   ```sql
   MERGE INTO AFE.TEST.VALUE_REALIZATION_CATALOG AS t
   USING (
     SELECT
       '<repo>'                         AS REPO,
       '<project_name>'                 AS PROJECT_NAME,
       '<persona>'                      AS PERSONA,
       '<tagline>'                      AS TAGLINE,
       '<summary_sentence>'             AS SUMMARY,
       '<challenge_heading>'            AS CHALLENGE_HEADING,
       '<capabilities_heading>'         AS CAPABILITY_HEADING,
       '<outcomes_heading>'             AS OUTCOME_HEADING,
       PARSE_JSON('<chips_json>')        AS CHIPS,
       PARSE_JSON('<bullets_json>')      AS BULLETS,
       PARSE_JSON('<outcomes_json>')     AS OUTCOMES,
       PARSE_JSON('<next_steps_json>')   AS NEXT_STEPS,
       '<github_url>'                   AS URL,
       '<raw_base_url>'                 AS RAW_URL,
       $<slide_html_dollar_literal>$    AS SLIDE_HTML
   ) AS s ON t.REPO = s.REPO
   WHEN MATCHED THEN UPDATE SET
     t.PROJECT_NAME      = s.PROJECT_NAME,
     t.PERSONA           = s.PERSONA,
     t.TAGLINE           = s.TAGLINE,
     t.SUMMARY           = s.SUMMARY,
     t.CHALLENGE_HEADING = s.CHALLENGE_HEADING,
     t.CAPABILITY_HEADING = s.CAPABILITY_HEADING,
     t.OUTCOME_HEADING   = s.OUTCOME_HEADING,
     t.CHIPS             = s.CHIPS,
     t.BULLETS           = s.BULLETS,
     t.OUTCOMES          = s.OUTCOMES,
     t.NEXT_STEPS        = s.NEXT_STEPS,
     t.URL               = s.URL,
     t.RAW_URL           = s.RAW_URL,
     t.SLIDE_HTML        = s.SLIDE_HTML,
     t.LAST_UPDATED_AT   = CURRENT_TIMESTAMP()
   WHEN NOT MATCHED THEN INSERT
     (REPO, PROJECT_NAME, PERSONA, TAGLINE, SUMMARY,
      CHALLENGE_HEADING, CAPABILITY_HEADING, OUTCOME_HEADING,
      CHIPS, BULLETS, OUTCOMES, NEXT_STEPS, URL, RAW_URL, SLIDE_HTML)
   VALUES
     (s.REPO, s.PROJECT_NAME, s.PERSONA, s.TAGLINE, s.SUMMARY,
      s.CHALLENGE_HEADING, s.CAPABILITY_HEADING, s.OUTCOME_HEADING,
      s.CHIPS, s.BULLETS, s.OUTCOMES, s.NEXT_STEPS, s.URL, s.RAW_URL, s.SLIDE_HTML);
   ```

   **Field substitution rules:**
   - All VARCHAR fields: escape single quotes by doubling them (`'` → `''`)
   - `<chips_json>`, `<bullets_json>`, `<outcomes_json>`, `<next_steps_json>`: JSON arrays of plain-text strings — escape single quotes inside the JSON strings by doubling them, e.g. `["Item 1", "Item 2"]`
   - `SLIDE_HTML`: use Snowflake dollar-quoting (`$$...$$`) to avoid any quote conflicts. Replace the literal `$<slide_html_dollar_literal>$` placeholder with `$$` + full HTML content + `$$`
   - `RAW_URL`: the raw base URL from Step 1 (e.g. `https://raw.githubusercontent.com/org/repo/main/`)

3. Verify the MERGE succeeded (1 row inserted or 1 row updated).

**Output:** `✅ Catalog row written: AFE.TEST.VALUE_REALIZATION_CATALOG — REPO='<repo>' (inserted|updated)`

---

## Stopping Points

✋ **Before fetching:** If the GitHub URL cannot be parsed into org/repo (e.g., it's a link to a specific file or PR), ask the user to confirm the repo root URL.

✋ **If repo is inaccessible:** If all curl attempts return 404 or empty, stop and ask the user to paste the README content directly into chat rather than silently generating a slide with no source data.

✋ **Low-confidence analysis:** If the fetched documentation is under 200 words or consists only of a placeholder README, warn the user and ask if they want to proceed with a skeleton slide or provide more context.

✋ **MERGE fails:** If the Snowflake MERGE returns an error, report the full error message. Check that the current role has INSERT/UPDATE privileges on `AFE.TEST.VALUE_REALIZATION_CATALOG`. Do not skip the step silently.

---

## Output

- **File:** `~/Downloads/<repo-name>-value-realization.html`
- **Opened:** automatically via `open` command (macOS)
- **Summary:** markdown block in chat confirming extracted fields:
  ```
  ✅ Slide generated: ~/Downloads/<repo-name>-value-realization.html

  Extracted:
  • Project: <name>
  • Persona: <persona>
  • Pillars: <pillar 1>, <pillar 2>
  • Chips: <chip list>
  • Challenge bullets: 3 extracted
  • Outcomes: 3 extracted
  • Next steps: 3 generated

  Catalog:
  • AFE.TEST.VALUE_REALIZATION_CATALOG — REPO='<repo>' (inserted|updated)
  ```

---

## Error Handling

| Error | Action |
|-------|--------|
| GitHub API rate limit (403) on fetch | Retry the raw file fetch directly without the API; skip the repo metadata |
| Branch not found (404) | Try `main` → `master` → ask user for correct branch name |
| Empty README | Warn user and ask for additional context before proceeding |
| No Snowflake features found | Still generate the slide — note in the capabilities section that explicit Snowflake features were not detected and suggest likely ones based on the project type |
| Write permission denied on ~/Downloads | Write to the current working directory instead and report the new path |
| MERGE fails with SQL error | Report full error; check role has INSERT/UPDATE on AFE.TEST.VALUE_REALIZATION_CATALOG |
| SLIDE_HTML too large (>16MB) | Truncate CSS comments and whitespace from the HTML before inserting |
