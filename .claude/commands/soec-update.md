# /soec-update — Rice SOEC Faculty Activity Tracker Update

You are executing the `/soec-update` command. Follow every step below precisely.

Arguments: $ARGUMENTS

---

## STEP 0 — Parse Arguments

Extract these optional flags from `$ARGUMENTS`:
- `--faculty "Name"` → set FACULTY_FILTER to that name; otherwise FACULTY_FILTER = null (search all)
- `--department "CODE"` → set DEPT_FILTER to that department code; otherwise DEPT_FILTER = null (all departments)
- `--type "Type"` → set TYPE_FILTER to that type; otherwise TYPE_FILTER = null (all types)
- `--since YYYY-MM-DD` → set SINCE_DATE to that date; otherwise SINCE_DATE = "2026-01-01"
- `--dry-run` → set DRY_RUN = true; otherwise DRY_RUN = false
- `--no-push` → set NO_PUSH = true; otherwise NO_PUSH = false (has no effect if DRY_RUN = true)

---

## STEP 1 — Load Existing Data

Read `rice_soec_tracker.json`. It is a JSON object with three top-level keys:
- `last_updated`: ISO date string (`YYYY-MM-DD`) of the last update run
- `record_count`: integer count of records in `records`
- `records`: the array of record objects, each with keys `faculty_name`, `activity_type`, `description`, `organization_venue`, `date_period`, `source_url_1`, `source_url_2`, `verified`, `department`

Use `records` as your deduplication baseline.

---

## STEP 2 — Determine Search Scope

**Faculty list** (use all unless FACULTY_FILTER or DEPT_FILTER is set):

- **BIOE** (19): Amanda Nash, Antonios Mikos, Cynthia Reinhart-King, Gang Bao, George Lu, Jeffrey Tabor, Jerzy Szablowski, Julea Vlassakis, K. Jane Grande-Allen, Kelsey Swingle, Mario Escobar, Michael R. King, Oleg Igoshin, Omid Veiseh, Rebecca Richards-Kortum, Renata Ramos, Sabia Abidi, Tomasz Tkaczyk, Z. Maria Oden
- **CEE** (23): Avantika Gori, Bezawit Getachew, Daniel Cohan, James Doss-Gollin, Jamie E. Padgett, Jim Blackburn, Jorge Loyo, Kai Gong, Kalil Erazo, Larissa Novelino, Lauren Stadler, Leonardo Dueñas-Osorio, Mason Tomson, Menachem Elimelech, Pedro J.J. Alvarez, Philip B. Bedient, Pol D. Spanos, Qilin Li, Reginald DesRoches, Sang-ri Yi, Satish Nagarajaiah, Shihong Lin, Xinwu Qian
- **CHBE** (16): Abbas Firoozabadi, Aditya Mohite, David Sholl, Frederick C. MacKintosh, Glen C. Irvin Jr., Haotian Wang, Matteo Pasquali, Michal S. Wong, Philip Singer, Rafael Verduzco, Ross Thyer, Sibani Lisa Biswal, Stavroula Kampouri, Thomas Senftle, Walter Chapman, Yirui Arlene Zhang
- **CMOR** (12): Andrew J. Schaefer, Beatrice Riviere, Eylem Tekin, Guodong Pang, Illya V. Hicks, Lu Zhang, Maarten De Hoop, Matthias Heinkenschloss, Sebastian Perez-Salazar, Shiqian Ma, Shuvomoy Das Gupta, Thomas G. Anderson
- **CS** (19): Alireza Fallah, Anastasios Kyrillidis, Anshumali Shrivastava, Chen Wei, Hanjie Chen, Jiarong Xing, Konstantinos Mamouras, Luay Nakhleh, Lydia E. Kavraki, Maryam Aliakbarpour, Moshe Y. Vardi, Nai-Hui Chia, Rodrigo Ferreira, Sanjoy Paul, T. S. Eugene Ng, Thirak Patel, Vicente Ordoñez Román, Vicky Yao, Yuke Wang
- **ECE** (21): Alessandro Alabastri, Ashok Veeraraghavan, Ashutosh Sabharwal, Behnaam Aazhang, Cesar A. Uribe, Edward W. Knightly, Guha Balakrishnan, Jacob T. Robinson, Joseph Young, Junichiro Kono, Kaiyuan Yang, Nakul Garg, Naomi J. Halas, Nishal Shah, Rahman Doost-Mohammady, Richard G. Baraniuk, Rober LiKamWa, Santiago Segarra, Schengxi Huang, Taiyun Chi, Tony Geng
- **MECH** (10): B.J. Fregly, Daniel J. Preston, Geoffrey Wehmeyer, Keya Ghonasgi, Lin Kong, Marcia O'Malley, Peter B. Lillehoj, Raudel Avila, Sasha Davydov, Vanessa Sanchez
- **MSNE** (11): Boris Yakobson, Christina Tringides, Geoffroy Hautier, Gilherme Marega, Harris Pirie, Jun Lou, Karen Lozano, Lane Martin, Ming Tang, Pulickel Ajayan, Robert Vajtai
- **STAT** (8): Cindy Zhang, Frederi Viens, Gina Pizzo, Huixia Judy Wang, Katherine B. Ensor, Leif Peterson, Marina Vannucci, Meng Li

(139 faculty total across 9 departments.)

**Activity types** (search all unless TYPE_FILTER is set):
Published Paper, Award / Honor, Keynote, Faculty Talk, Conference, Editorial Board, Society Leadership, Collaboration, New Appointment, Academy

---

## STEP 3 — Search for New Activities

For each faculty member in scope, run web searches. Introduce a short pause between searches to be respectful of rate limits.

**Department site mapping** (use the faculty member's department for the site-restricted searches):

| Dept | Site(s) |
|---|---|
| BIOE | bioengineering.rice.edu |
| CEE | cee.rice.edu |
| CHBE | chbe.rice.edu |
| CMOR | cmor.rice.edu |
| CS | cs.rice.edu, csweb.rice.edu |
| ECE | eceweb.rice.edu |
| MECH | mech.rice.edu |
| MSNE | msne.rice.edu |
| STAT | statistics.rice.edu |

**Search queries to run for each faculty member** (adapt name, department site, and year as needed):
1. `"[Faculty Name]" site:news.rice.edu 2026`
2. `"[Faculty Name]" site:[department site] 2026`
3. `"[Faculty Name]" site:engineering.rice.edu 2026`
4. `"[Faculty Name]" site:profiles.rice.edu`
5. `"[Faculty Name]" Rice University award honor keynote 2026`
6. `"[Faculty Name]" Rice University published journal paper 2026`
7. `"[Faculty Name]" conference keynote invited lecture 2026`

**Additional targeted searches:**
- Google Scholar: `"[Faculty Name]" Rice University site:scholar.google.com`
- ResearchGate: `"[Faculty Name]" Rice site:researchgate.net`
- Rice news archive search via WebFetch on `https://news.rice.edu/?s=[faculty+name]`
- EurekAlert: `"[Faculty Name]" Rice site:eurekalert.org`
- Discipline-specific societies as relevant (ASCE, ACS, AGU, IWA, IEEE, ACM, ASME, AIChE, MRS, ASA, INFORMS, SIAM, etc.): `"[Faculty Name]" [society] 2026`
- Journal publisher sites (Elsevier, RSC, ACS, Wiley, Nature, Springer, IEEE Xplore): `"[Faculty Name]" Rice 2026`
- Personal/lab pages if known from prior tracker entries (e.g. `[name].rice.edu`, lab-specific domains)

**Activity-specific search angles:**
- For papers: look for DOIs, journal titles, publication dates
- For awards: check discipline-specific society annual award announcements
- For keynotes/talks: check conference programs
- For editorial boards: search journal masthead pages
- For academy elections: check NAE, AAAS, NAI, NAS announcement pages
- For new appointments: check `engineering.rice.edu/news` faculty-hire announcements
- For startups/spinoffs/collaborations: check EurekAlert, TechCrunch-style outlets, and Rice News for grant/funding announcements tied to faculty labs

---

## STEP 4 — Validate Each Finding

For every potential new record:

1. **Source URL check**: Attempt to fetch the URL via WebFetch. If it returns a 404 or error, **skip the record** and log a warning: `WARN: [faculty] — URL 404: [url]`.
2. **Date check**: Confirm the activity date is on or after SINCE_DATE. Exclude anything earlier.
3. **Identity check**: Confirm the finding is about this specific faculty member, not a different person with a similar name.
4. **Department check**: Confirm the faculty member's department matches the one in the scope list (record it as `department`).
5. **Primary source confirmation**: Mark `"verified": true` only if the finding appears in at least one of:
   - The journal's own page (not just Google Scholar)
   - An official conference program
   - Rice University news or faculty profile
   - An official society or organization announcement
6. If you cannot confirm a primary source, mark `"verified": false` and add to a separate NEEDS_REVIEW list with a note explaining why.

---

## STEP 5 — Deduplicate

For each candidate new record, compare against every record in `rice_soec_tracker.json`:
- **Exact match**: same `faculty_name` AND same `source_url_1` → skip (duplicate)
- **Near-match**: same `faculty_name` AND descriptions share more than ~80% of key words → skip (duplicate)
- If no match found, include the record as new

---

## STEP 6 — Format Records

Structure each new verified record exactly as:

```json
{
  "faculty_name": "exact name from the faculty list in Step 2",
  "activity_type": "one of: Published Paper | Award / Honor | Keynote | Faculty Talk | Conference | Editorial Board | Society Leadership | Collaboration | New Appointment | Academy",
  "description": "one clear sentence describing the activity",
  "organization_venue": "organization or venue name",
  "date_period": "specific date or range if known",
  "source_url_1": "direct URL to primary source — required, must be verified accessible",
  "source_url_2": "secondary URL or empty string",
  "verified": true,
  "department": "one of: BIOE | CEE | CHBE | CMOR | CS | ECE | MECH | MSNE | STAT"
}
```

---

## STEP 7 — Output JSON

Print the full JSON array of new records found (both verified and unverified), clearly labeled:

```
=== NEW RECORDS (verified) ===
[JSON array]

=== NEEDS HUMAN REVIEW (unverified) ===
[JSON array with verified: false]
```

If no new records were found, say so explicitly for each faculty member searched.

---

## STEP 8 — Update Files (skip all of this if DRY_RUN = true)

### 8a. Update `rice_soec_tracker.json`

1. Read the current `rice_soec_tracker.json`
2. Append all new **verified** records (verified: true) to the `records` array
3. Set `last_updated` to today's date in `YYYY-MM-DD` format
4. Set `record_count` to the new total length of `records`
5. Write the complete updated object (`last_updated`, `record_count`, `records`) back to `rice_soec_tracker.json`
6. Do NOT modify any existing records

### 8b. Update `soec_faculty_dashboard.html`

1. Read the current `soec_faculty_dashboard.html`
2. Locate the line that begins `const RAW_DATA = [`
3. Locate the matching closing `];`
4. For each new **verified** record, append a JSON object (same shape as Step 6, including `department`) before the closing `];`, comma-separated
5. Update the header count placeholder: `<span id="hdr-count">[N]</span>` → new total record count (this is also recomputed at runtime by JS, but keep the static text in sync)
6. Update the footer count placeholder: `<span id="ftr-count">[N]</span>` → new total record count
7. Update both `Updated [Month DD, YYYY]` occurrences (header `<p>` and `<footer>`) to today's date
8. Write the updated HTML back to `soec_faculty_dashboard.html`

### 8c. Sync `index.html`

Overwrite `index.html` with the exact contents of the just-updated `soec_faculty_dashboard.html` (index.html is the GitHub Pages entry point and must always mirror the dashboard).

### 8d. Update `rice_soec_faculty_tracker.html`

1. Read the current `rice_soec_faculty_tracker.html`
2. Locate the line that begins `const D=[`
3. Locate the closing `];` of that array
4. For each new **verified** record, convert to HTML row format and insert before the `];`:
   ```javascript
   ["faculty_name","activity_type","description","organization_venue","date_period","source_url_1","source_url_2"],
   ```
   Escape any double quotes inside field values as `&quot;` or use single quotes inside the string if needed. Escape any backticks or template literal syntax. (No `department` field in this row format.)
5. Update the `Updated [Month DD, YYYY]` text in the `.sub` div to today's date.
6. Write the updated HTML back to `rice_soec_faculty_tracker.html`

### 8e. Append to `rice_soec_update_log.md`

Append (do not overwrite) a new entry:

```markdown
## Run: [ISO timestamp]

- **Faculty searched**: [comma-separated list, or "all 139"]
- **Departments**: [comma-separated list, or "all 9"]
- **Activity types**: [comma-separated list, or "all 10"]
- **Date filter**: since [SINCE_DATE]
- **Dry run**: [yes/no]
- **Records found**: [N total candidates]
- **Records added**: [N new verified records appended]
- **Records skipped (duplicate)**: [N]
- **Records flagged for review (unverified)**: [N]
- **Sources queried**: [list of domains checked]

### New records added
[bulleted list: "Faculty Name (Dept) — Activity Type — one-line description"]

### Warnings
[any 404s or skipped records; "None" if clean]

---
```

### 8f. Commit and push to GitHub (skip if NO_PUSH = true)

The repo remote is `origin` → `https://github.com/ma212-rice/soec-tracker`. Only `index.html`, `rice_soec_tracker.json`, and `.gitignore` are tracked in git — `rice_soec_faculty_tracker.html` and `rice_soec_update_log.md` are intentionally local-only and must NOT be added to git.

1. Run `git status` to confirm which tracked files changed.
2. If neither `index.html` nor `rice_soec_tracker.json` changed, skip commit/push entirely and note this in the run summary.
3. Otherwise: `git add index.html rice_soec_tracker.json`
4. Commit with a message summarizing the update, e.g.:
   ```
   git commit -m "Update SOEC tracker: +N records ([date])

   [bulleted list of faculty (dept) — activity type, max ~10 lines; if more, summarize by department]"
   ```
5. `git push origin main`
6. If the push fails (e.g. remote has diverged), do NOT force-push. Run `git pull --rebase origin main` to reconcile, resolve any conflicts by preferring the local content (it is the source of truth for this run), then retry the push. Report the outcome — success, or what manual intervention is needed — in the run summary. Never use `--force`.

---

## STEP 9 — Print Run Summary

```
=== Rice SOEC Update Complete ===
Timestamp    : [ISO timestamp]
Faculty scope: [N faculty searched] across [N] departments
Date filter  : [SINCE_DATE] to present
Dry run      : [yes/no]

New records found   : [N]
  By faculty        : [Name: N, ...]
  By department     : [Dept: N, ...]
  By activity type  : [Type: N, ...]
Duplicates skipped  : [N]
Flagged for review  : [N]

Files updated:
  rice_soec_tracker.json          [N records → now N total]
  soec_faculty_dashboard.html     [updated / skipped (dry run)]
  index.html                      [synced / skipped (dry run)]
  rice_soec_faculty_tracker.html  [updated / skipped (dry run)]
  rice_soec_update_log.md         [appended / skipped (dry run)]

Git:
  Committed  : [yes/no — commit hash if yes]
  Pushed     : [yes/no/skipped(--no-push)/failed — reason if failed]
```

If NEEDS_REVIEW is non-empty, list each item:
```
=== Needs Human Review ===
• [Faculty Name] ([Dept]) — [activity_type] — [description]
  Reason: [why unverified]
  Candidate URL: [url]
```

---

## STEP 10 — Done Announcement

After all files are updated (or after dry-run output), print this completion block. Use a horizontal rule to make it visually distinct:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅  /soec-update complete
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  Faculty searched  : [N] / 139
  Sources queried   : [N] domains
  Candidates found  : [N]

  ✔ Added to tracker : [N] new verified records
  ⚠ Needs review     : [N] unverified (not added)
  ↩ Skipped          : [N] duplicates

  Tracker total      : [N] records across [N] faculty

  Files touched:
    rice_soec_tracker.json         ([prev] → [new] records)
    soec_faculty_dashboard.html    (updated [date])
    index.html                     (synced [date])
    rice_soec_faculty_tracker.html (updated [date])
    rice_soec_update_log.md        (run appended)

  Git: [committed & pushed [hash] / skipped (dry run) / skipped (--no-push) / push failed — see summary]

[If --dry-run:]  ⚠ DRY RUN — no files were modified.
[If 0 new records:]  ℹ  No new activities found since [SINCE_DATE].
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

This block must always be the final output, even if no new records were found or an error was encountered mid-run.

---

## Behavioral Rules (enforced throughout)

1. **Never add a record without a working `source_url_1`**. If the URL cannot be fetched or returns 404, skip it and log a warning.
2. **Never modify existing records** in `rice_soec_tracker.json` — only append new ones.
3. **Date filter is strict** — exclude anything dated before SINCE_DATE.
4. **Rate-limit searches** — pause briefly between web requests to avoid being blocked.
5. **No invented records** — if a faculty member has no new verifiable activities, log "no new records found" for them. Do not speculate or fill gaps.
6. **Preserve all existing HTML rows/entries unchanged** — only append new entries to `RAW_DATA` and `D` arrays.
7. **Deduplication is mandatory** — never add a record that matches an existing one in `rice_soec_tracker.json`.
8. **`verified: false` records are not written to files** — only print them in the NEEDS_REVIEW section for human review.
9. **Never `git push --force`**. If a push is rejected, rebase and retry once; if it still fails, stop and report it rather than forcing.
10. **Never commit `rice_soec_faculty_tracker.html` or `rice_soec_update_log.md`** to git — they stay local-only unless the user explicitly changes this.
