# Skill: Prompt Consolidator (Consolidated Prompt Executor)

**Purpose**: Execute the latest version of a prompt artifact (e.g., `PRD_v#.md`) and merge its output with previous executions, preserving all prior sections and metrics. The skill creates a new version file `PRD_vX.md` (where `X` is the next incremental version) inside `docs/prd/`.

---

## Consolidation Rules

1. **Do not recreate the whole artifact** – only append/merge new sections.
2. **Section handling**:
   - For each heading that already exists in the previous consolidated version, keep the old content and append the new output beneath a clear separator.
   - If the latest prompt introduces a heading that does not exist yet, add the whole new section as‑is.
3. **Metrics handling**:
   - All metric tables/sub‑sections from earlier runs must remain untouched.
   - Add a fresh subsection `## Metrics – Run <timestamp>` that contains the metrics of the current execution.
4. **Version naming**:
   - Detect the highest existing version `PRD_vN.md` in `docs/prd/` and create `PRD_v(N+1).md`.
   - If the directory is empty, start with `PRD_v0.md`.

---

## Execution Steps (Python pseudocode)

```python
import os
import re
from datetime import datetime

PROMPT_DIR = "docs/prd"
VERSION_RE = r"PRD_v(\d+)\.md"

# 1️⃣ Ensure the prompt directory exists
if not os.path.isdir(PROMPT_DIR):
    raise FileNotFoundError(f"Prompt directory not found: {PROMPT_DIR}")

# 2️⃣ Gather existing version files
files = [f for f in os.listdir(PROMPT_DIR) if re.match(VERSION_RE, f)]
if not files:
    # No previous versions – start fresh with v0 (user must provide an initial file)
    raise ValueError("No PRD_v#.md files found in the directory. Add at least one version.")

# 3️⃣ Determine latest and next version numbers
latest_file = max(files, key=lambda x: int(re.search(VERSION_RE, x).group(1)))
latest_version = int(re.search(VERSION_RE, latest_file).group(1))
next_version = latest_version + 1
new_file_name = f"PRD_v{next_version}.md"
new_path = os.path.join(PROMPT_DIR, new_file_name)

# 4️⃣ Load the latest prompt (this is the input you will run)
with open(os.path.join(PROMPT_DIR, latest_file), "r", encoding="utf-8") as f:
    latest_content = f.read()

# -----------------------------------------------------------------
# 👉 **INSERT YOUR PROMPT EXECUTION LOGIC HERE**
# For example, you could call an LLM API, a CLI tool, or a custom script.
# The variable `execution_output` must contain the *raw* markdown output
# produced by that execution.
# -----------------------------------------------------------------
# Placeholder – copy the latest content (replace with real call)
execution_output = latest_content

# 5️⃣ Helper: split markdown into sections (headings start with ##)
def split_sections(md):
    sections = {}
    current_heading = "#root"
    sections[current_heading] = []
    for line in md.splitlines():
        heading_match = re.match(r"^(##+ )", line)
        if heading_match:
            current_heading = line.strip()
            sections[current_heading] = []
        else:
            sections[current_heading].append(line)
    # Join back into strings
    return {h: "\n".join(c).strip() for h, c in sections.items()}

latest_secs = split_sections(latest_content)
output_secs = split_sections(execution_output)

# 6️⃣ Load the previous consolidated version (if any)
prev_file = None
if len(files) > 1:
    # Pick the highest version that is NOT the latest
    prev_candidates = [f for f in files if f != latest_file]
    if prev_candidates:
        prev_file = max(prev_candidates, key=lambda x: int(re.search(VERSION_RE, x).group(1)))

prev_secs = {}
if prev_file:
    with open(os.path.join(PROMPT_DIR, prev_file), "r", encoding="utf-8") as f:
        prev_content = f.read()
    prev_secs = split_sections(prev_content)

# 7️⃣ Build the new consolidated markdown
consolidated = []
# Preserve the top‑level title from the latest file (first line starting with # )
title_match = re.search(r"^# .+$", latest_content, flags=re.MULTILINE)
consolidated.append(title_match.group(0) if title_match else "# PRD")

for heading, latest_body in latest_secs.items():
    if heading == "#root":
        continue
    consolidated.append(heading)
    # Append previous content for this heading if it exists
    if heading in prev_secs:
        consolidated.append(prev_secs[heading])
    # Separator and new output for this heading
    consolidated.append("\n--- New Output ---\n")
    consolidated.append(output_secs.get(heading, ""))

# 8️⃣ Append a fresh metrics subsection (always added)
metrics_heading = f"## Metrics – Run {datetime.utcnow().isoformat()}"
consolidated.append(metrics_heading)
consolidated.append("```
# TODO: Insert metrics JSON or table here
```")

# 9️⃣ Write the new version file
with open(new_path, "w", encoding="utf-8") as f:
    f.write("\n\n".join(consolidated))

print(f"Created consolidated version: {new_path}")
```

---

## How to Use the Skill
1. Save this file as `agents/skills/SKILL.md` (already done).
2. Ensure the repository contains a `docs/prd/` directory with at least one versioned prompt file (`PRD_v0.md`).
3. Run the skill with your agent framework, for example:
   ```bash
   gemini run agents/skills/SKILL.md
   ```
   The script will automatically detect the newest prompt, execute it (replace the placeholder with your real command), and generate a new consolidated version.

---

> **Important**: Replace the placeholder section marked *INSERT YOUR PROMPT EXECUTION LOGIC HERE* with the actual call that runs your prompt and returns markdown output. The rest of the script handles version detection, merging, and metrics aggregation.
