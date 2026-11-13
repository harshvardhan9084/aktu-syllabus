# AKTU Syllabus — Data API v1

Static JSON API listing the institutes affiliated to **Dr. A.P.J. Abdul Kalam
Technical University (AKTU), Lucknow** and the courses / branches each one
offers. Every endpoint is a plain JSON file — no server, no database — so any
HTTP client works.

Generated 13 Nov 2026, 14:32 IST · **784** institutes · **26** courses ·
**2571** branch offerings · `api_version` **1.1.0** (RR-ID enabled).

## Bases

| Base | URL | Notes |
| --- | --- | --- |
| GitHub Pages | https://harshvardhan9084.github.io/aktu-syllabus/ | served as `application/json`, CORS-enabled |
| raw.githubusercontent | https://raw.githubusercontent.com/harshvardhan9084/aktu-syllabus/main | tracks `main` directly |
| jsDelivr CDN | https://cdn.jsdelivr.net/gh/harshvardhan9084/aktu-syllabus@main | globally cached |

## Endpoints (all GET)

| Path | Returns |
| --- | --- |
| `/api/v1/index.json` | manifest: endpoints, counts, RR-ID info, changelog, integrity summary |
| `/api/v1/tree.json` | whole dataset as one nested JSON tree (institutes → courses → branches) |
| `/api/v1/institutes.json` | all institutes, summary fields + detail links |
| `/api/v1/institutes/{institute_code}.json` | one institute, nested courses → branches — e.g. [`002.json`](https://harshvardhan9084.github.io/aktu-syllabus/api/v1/institutes/002.json) |
| `/api/v1/institutes/{rr_id}.json` | same payload by frozen RR-ID — e.g. [`I01001.json`](https://harshvardhan9084.github.io/aktu-syllabus/api/v1/institutes/I01001.json) |
| `/api/v1/branches.json` | flat mirror of branches.csv (institute name + RR-IDs joined) |
| `/api/v1/branches/{rr_id}.json` | one (course × branch) pair: every institute offering it — e.g. [`B010101.json`](https://harshvardhan9084.github.io/aktu-syllabus/api/v1/branches/B010101.json) |
| `/api/v1/courses.json` | course taxonomy with slugs + RR-IDs + counts |
| `/api/v1/courses/{slug}.json` | one course, every institute offering it — e.g. [`bachelor-of-technology.json`](https://harshvardhan9084.github.io/aktu-syllabus/api/v1/courses/bachelor-of-technology.json) |
| `/api/v1/courses/{rr_id}.json` | same payload by frozen RR-ID — e.g. [`C0101.json`](https://harshvardhan9084.github.io/aktu-syllabus/api/v1/courses/C0101.json) |
| `/api/v1/universities.json` | root university entities (currently one: AKTU) |
| `/api/v1/universities/{rr_id}.json` | one university with its full course catalogue — [`U01.json`](https://harshvardhan9084.github.io/aktu-syllabus/api/v1/universities/U01.json) |
| `/api/v1/registry/rr-ids.json` | append-only RR-ID registry: natural key → id, sequences, prefix grammar |
| `/api/v1/integrity.json` | source cross-check report |

## RR-ID system

Every entity carries a frozen **RR-ID** — a stable identifier that survives
regenerations and future dataset additions:

| Prefix | Pattern | Example | Identifies |
| --- | --- | --- | --- |
| `U` | `U` + 2-digit university | `U01` | AKTU (root university) |
| `C` | `C` + uni(2) + course(2) | `C0101` | Bachelor of Technology |
| `B` | `B` + uni(2) + course(2) + branch(2) | `B010101` | one (course × branch) offering pair |
| `I` | `I` + uni(2) + institute(3) | `I01001` | one institute |
| `S` | `S` + 4 digits | *(reserved)* | subjects — coming with the syllabus dataset |

**The one rule that matters:** once published, an RR-ID is frozen forever.
The generator never renumbers — it reads the append-only registry
(`/api/v1/registry/rr-ids.json`) and only appends unseen entities with the
next free serial. Subjects are deliberately **flat** (`S0001`–`S9999`), because
a subject belongs to many branches; subject × branch relationships will be
edge records carrying `year` / `semester` on the edge — never inside the ID.

Full anatomy, rules, and the complete reference tables for every university,
course and branch: **[`docs/rr-id-reference.md`](docs/rr-id-reference.md)**.

## Explorer frontend (index.html)

The repo root ships a dependency-free single-page explorer over the API.

- **GitHub Pages (the app's home):** `https://harshvardhan9084.github.io/aktu-syllabus/`
  — enable once in *Settings → Pages → Deploy from a branch → `main` / `(root)`*.
  CDN security policies (jsDelivr, statically) force `.html` to `text/plain`, so those
  CDNs serve the JSON endpoints perfectly but **cannot render this app**.
- **Until Pages is enabled:** any static server of this folder works, e.g.
  `python3 -m http.server` → `http://localhost:8000/`. (raw.githack renders it
  behind a click-through notice page.)

**Viewer defaults:** institutes grid with instant search, cards, 50/page.
**Power controls (Filters panel):** multi-select course chips, derived-location filter,
branch keyword search, sort/direction, table view, page size, CSV exports
(filtered institutes, offerings, course listings), shareable filter URLs,
per-entity JSON view/download, and an API explorer that can fetch any endpoint.
Architecture note: the app reads `index.json` first and lazy-loads heavier files
(`tree.json`, per-entity JSON), so plugging in the future syllabus dataset is a
data-source addition, not a rewrite.

## Versioning policy

- `api/v1/` is **updated in place** for data refreshes and **additive** changes
  (new fields, new endpoints — e.g. the syllabus dataset will land under
  `api/v1/syllabus/…`, still v1). Nothing breaks for consumers.
- A new folder (`api/v2/`) appears **only on breaking changes** (fields renamed/
  removed, envelope changed). Old versions then stay frozen and read-only.
- `index.json → changelog`, `meta.generated_at` and git history together are the
  changelog. For immutable access, pin a commit on jsDelivr:
  `https://cdn.jsdelivr.net/gh/harshvardhan9084/aktu-syllabus@<commit-sha>/api/v1/…`.

## Envelope

Every endpoint except `index.json` returns:

```json
{ "data": { ... }, "meta": { "api_version": "1.1.0", "generated_at": "...", "count": 123 } }
```

## Quick start

```bash
curl -s https://harshvardhan9084.github.io/aktu-syllabus/api/v1/universities/U01.json
curl -s https://harshvardhan9084.github.io/aktu-syllabus/api/v1/institutes/002.json
curl -s https://harshvardhan9084.github.io/aktu-syllabus/api/v1/branches/B010101.json
curl -s https://harshvardhan9084.github.io/aktu-syllabus/api/v1/courses/masters-of-business-administration.json
curl -s https://harshvardhan9084.github.io/aktu-syllabus/api/v1/tree.json | head -c 400
```

## Data dictionary

**institutes.csv** → `institute_code` (zero-padded string, primary key) ·
`institute_name` · `institute_address` · `institute_mobile` · `institute_email` ·
`course_count` (declared in the source extract)

**branches.csv** → `institute_code` (joins to institutes) · `course` · `branch`

## Provenance & policy

- Published **verbatim** from `data/raw/institutes.csv` and `data/raw/branches.csv`
  (byte-identical copies are kept in this repo).
- Source spellings are preserved as-is — this dataset is intentionally not hand-corrected.
- Only normalization applied: headers snake_cased, surrounding whitespace trimmed.
- Cross-checks (unknown institute codes, declared-vs-listed course counts, duplicates,
  missing values, RR-ID caps) are reported in `/api/v1/integrity.json` instead of
  being silently fixed.

_Regenerated by `scripts/build_api_tree.py`; see repo history for provenance._
_Frontend: `index.html` (vanilla JS, no build step)._
