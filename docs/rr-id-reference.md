# RR-ID Reference

Every entity in this API carries a stable **RR-ID**. The ID answers one question
only — *"which thing is this?"* — and never changes once published. Where a thing
*lives* (which institute offers which course, which branch teaches which subject)
is relationship data carried **alongside** the ID, never baked into it.

Generated 13 Nov 2026, 14:32 IST · currently 1 universities · 26 courses · 153 branch pairs · 784 institutes · subjects reserved.

## Anatomy

| Prefix | Pattern | Example | Identifies |
| --- | --- | --- | --- |
| `U` | `U` + 2-digit university serial | `U01` | a university (root entity) |
| `C` | `C` + university(2) + 2-digit course serial | `C0101` | a course **within one university** |
| `B` | `B` + university(2) + course(2) + 2-digit branch serial | `B010101` | a **(course × branch) offering pair** |
| `I` | `I` + university(2) + 3-digit institute serial | `I01001` | an institute **within one university** |
| `S` | `S` + 4-digit flat serial | *(reserved)* | a subject — **planned**, ships with the syllabus dataset |

Reading the digits: every ID after `U` starts with the university pair, so
`C0105` is university `01`'s 5th course and `B010507` is the 7th branch pair of
course `C0105` at university `01`. The path digits exist only because those
relations are strict trees (a course has exactly one university; an institute
belongs to exactly one university).

Worked examples from the live data: `U01` = Dr. A.P.J. Abdul Kalam Technical University (AKTU) · `C0101` = Bachelor of Technology · `B010101` = the first (course × branch) pair of Bachelor of Technology · `I01001` = institute `002`.

## The rules

1. **Frozen forever.** Once an RR-ID appears in a published commit it will never
   be reused, renumbered or reassigned. URLs, caches and cross-references depend on it.
2. **Append-only registry.** `api/v1/registry/rr-ids.json` maps every natural key
   (course name, `course||branch` pair, institute code, …) to its RR-ID. The
   generator loads it first and only *appends* unseen keys with the next free
   serial in scope. Deleting rows from the source CSVs removes them from views —
   the ID assignment itself is never rolled back.
3. **Caps, asserted at build time.** ≤ 99 universities · ≤ 99 courses per
   university · ≤ 99 branch pairs per course · ≤ 999 institutes per university ·
   ≤ 9999 subjects. A breach aborts the build loudly instead of colliding silently.
4. **`00` is reserved** as the null slot in any pair (e.g. a course-level resource
   with no specific branch). Branch serials therefore start at `01`.
5. **Year / semester live on relationships, not in IDs.** Roughly half of the
   upcoming subjects have no fixed semester; that flexibility is data
   (`semester: null`) on the subject × branch edge, never digits inside an ID.
6. **Historic codes are labels, not identity.** ~6000 subject codes (current +
   historic like `KCS051`) will map onto ~2000 subject RR-IDs via a lookup; codes
   get reused across revisions, RR-IDs do not.

## Resolving an RR-ID

Every entity type has a by-ID endpoint — plain GET, no lookup table needed:

```text
GET /api/v1/universities/U01.json
GET /api/v1/courses/C0101.json
GET /api/v1/branches/B010101.json
GET /api/v1/institutes/I01001.json
```

All list endpoints also embed `rr_id` fields (per row), and `branches.json`
carries `institute_rr_id` / `course_rr_id` / `branch_rr_id` per offering row.

## The registry

`/api/v1/registry/rr-ids.json` is the machine-readable map:

```json
{
  "prefixes":   { "U": "…", "C": "…", "B": "…", "I": "…", "S": "…" },
  "sequences":  { "universities": 2, "courses": {"U01": 27}, "branches": {"C0101": 61}, "institutes": {"U01": 785} },
  "assignments": { "courses": {"Bachelor of Technology": "C0101"}, "branches": {"Bachelor of Technology||Computer Science and Engineering": "B010101"}, "institutes": {"002": "I01001"} }
}
```

Consume it to invert lookups (RR-ID → name) or to pre-assign IDs for entities you
know are coming. Do **not** hand-edit it — regenerate instead.

## Reference: universities

| rr_id | university | courses | branch pairs | institutes |
| --- | --- | --- | --- | --- |
| U01 | Dr. A.P.J. Abdul Kalam Technical University (AKTU) | 26 | 153 | 784 |

## Reference: courses

| rr_id | course | slug | institutes | branch pairs | detail |
| --- | --- | --- | --- | --- | --- |
| C0101 | Bachelor of Technology | bachelor-of-technology | 191 | 60 | `/api/v1/courses/bachelor-of-technology.json` |
| C0102 | Bachelor of Pharmacy | bachelor-of-pharmacy | 446 | 1 | `/api/v1/courses/bachelor-of-pharmacy.json` |
| C0103 | Masters of Business Administration | masters-of-business-administration | 256 | 8 | `/api/v1/courses/masters-of-business-administration.json` |
| C0104 | Master of Pharmacy | master-of-pharmacy | 103 | 6 | `/api/v1/courses/master-of-pharmacy.json` |
| C0105 | Master of Technology | master-of-technology | 70 | 38 | `/api/v1/courses/master-of-technology.json` |
| C0106 | Master of Computer Applications | master-of-computer-applications | 108 | 1 | `/api/v1/courses/master-of-computer-applications.json` |
| C0107 | BCA | bca | 53 | 1 | `/api/v1/courses/bca.json` |
| C0108 | BBA | bba | 49 | 1 | `/api/v1/courses/bba.json` |
| C0109 | Bachelor of Technology Working Professional | bachelor-of-technology-working-professional | 8 | 4 | `/api/v1/courses/bachelor-of-technology-working-professional.json` |
| C0110 | B Voc | b-voc | 6 | 8 | `/api/v1/courses/b-voc.json` |
| C0111 | PHARM D | pharm-d | 12 | 2 | `/api/v1/courses/pharm-d.json` |
| C0112 | MBA(Integrated) | mba-integrated | 11 | 1 | `/api/v1/courses/mba-integrated.json` |
| C0113 | BHMCT | bhmct | 7 | 1 | `/api/v1/courses/bhmct.json` |
| C0114 | Bachelor of Architecture | bachelor-of-architecture | 6 | 1 | `/api/v1/courses/bachelor-of-architecture.json` |
| C0115 | M.Tech (Part Time) | m-tech-part-time | 1 | 5 | `/api/v1/courses/m-tech-part-time.json` |
| C0116 | Master of Technology Working Professional | master-of-technology-working-professional | 3 | 2 | `/api/v1/courses/master-of-technology-working-professional.json` |
| C0117 | Master of Architecture | master-of-architecture | 1 | 3 | `/api/v1/courses/master-of-architecture.json` |
| C0118 | MCA(Integrated) | mca-integrated | 3 | 1 | `/api/v1/courses/mca-integrated.json` |
| C0119 | Bachelor of Design | bachelor-of-design | 2 | 1 | `/api/v1/courses/bachelor-of-design.json` |
| C0120 | BFAD | bfad | 2 | 1 | `/api/v1/courses/bfad.json` |
| C0121 | M.Design. | m-design | 2 | 1 | `/api/v1/courses/m-design.json` |
| C0122 | Master of Planning | master-of-planning | 1 | 2 | `/api/v1/courses/master-of-planning.json` |
| C0123 | MBA Working Professional | mba-working-professional | 2 | 1 | `/api/v1/courses/mba-working-professional.json` |
| C0124 | Bachelor of Fine Art(BFA) | bachelor-of-fine-art-bfa | 1 | 1 | `/api/v1/courses/bachelor-of-fine-art-bfa.json` |
| C0125 | Master Of Integrated Technology | master-of-integrated-technology | 1 | 1 | `/api/v1/courses/master-of-integrated-technology.json` |
| C0126 | MCA Working Professional | mca-working-professional | 1 | 1 | `/api/v1/courses/mca-working-professional.json` |

## Reference: branch pairs (course × branch)

One row per offering pair — the same branch *name* can exist under several
courses (e.g. Computer Science and Engineering exists under B.Tech **and**
M.Tech); those are distinct entities with distinct RR-IDs.

| rr_id | course | branch | institutes |
| --- | --- | --- | --- |
| B010101 | C0101 · Bachelor of Technology | Aeronautical Engineering | 1 |
| B010102 | C0101 · Bachelor of Technology | Agricultural Engineering | 12 |
| B010103 | C0101 · Bachelor of Technology | Artificial Inelligence (AI) And Data Science | 12 |
| B010104 | C0101 · Bachelor of Technology | Artificial Intelligence | 1 |
| B010105 | C0101 · Bachelor of Technology | Artificial Intelligence And Machine Learning | 18 |
| B010106 | C0101 · Bachelor of Technology | Automation and Robotics | 1 |
| B010107 | C0101 · Bachelor of Technology | Automobile Engineering | 2 |
| B010108 | C0101 · Bachelor of Technology | Bio Medical Engineering | 1 |
| B010109 | C0101 · Bachelor of Technology | Bioinformatics | 1 |
| B010110 | C0101 · Bachelor of Technology | Biotechnology | 24 |
| B010111 | C0101 · Bachelor of Technology | CYBER SECURITY | 1 |
| B010112 | C0101 · Bachelor of Technology | Carpet & Textile Technology | 1 |
| B010113 | C0101 · Bachelor of Technology | Chemical Engineering | 5 |
| B010114 | C0101 · Bachelor of Technology | Civil Engineering | 126 |
| B010115 | C0101 · Bachelor of Technology | Computer Science | 12 |
| B010116 | C0101 · Bachelor of Technology | Computer Science & Engineering (Cyber Security) | 12 |
| B010117 | C0101 · Bachelor of Technology | Computer Science & Technology | 1 |
| B010118 | C0101 · Bachelor of Technology | Computer Science (Data Science) | 1 |
| B010119 | C0101 · Bachelor of Technology | Computer Science (Hindi) | 1 |
| B010120 | C0101 · Bachelor of Technology | Computer Science And Business System | 1 |
| B010121 | C0101 · Bachelor of Technology | Computer Science And Design | 3 |
| B010122 | C0101 · Bachelor of Technology | Computer Science And Engineering(AIML) (Collaboration & Twining Program) | 1 |
| B010123 | C0101 · Bachelor of Technology | Computer Science And Engineering(Artificial Intelligence & Machine Learning) | 89 |
| B010124 | C0101 · Bachelor of Technology | Computer Science And Engineering(Data Science) | 57 |
| B010125 | C0101 · Bachelor of Technology | Computer Science And Engineering(Internet Of Things) | 3 |
| B010126 | C0101 · Bachelor of Technology | Computer Science Information Technology | 3 |
| B010127 | C0101 · Bachelor of Technology | Computer Science and Engineering | 182 |
| B010128 | C0101 · Bachelor of Technology | Computer Science and Engineering (Artificial Intelligence) | 34 |
| B010129 | C0101 · Bachelor of Technology | Computer Science and Engineering (Artificial Intelligence) (Collaboration & Twining Program) | 1 |
| B010130 | C0101 · Bachelor of Technology | Computer Science and Engineering (Collaboration & Twining Program) | 1 |
| B010131 | C0101 · Bachelor of Technology | Computer Science and Engineering (Hindi) | 3 |
| B010132 | C0101 · Bachelor of Technology | Computer Science and Information Technology | 1 |
| B010133 | C0101 · Bachelor of Technology | DATA SCIENCE | 1 |
| B010134 | C0101 · Bachelor of Technology | Electrical & Computer Engg. | 3 |
| B010135 | C0101 · Bachelor of Technology | Electrical & Electronics Engineering | 49 |
| B010136 | C0101 · Bachelor of Technology | Electrical Engineering | 81 |
| B010137 | C0101 · Bachelor of Technology | Electronic And Computer Engineering | 4 |
| B010138 | C0101 · Bachelor of Technology | Electronics Engineering | 9 |
| B010139 | C0101 · Bachelor of Technology | Electronics Engineering (VLSI Design and Technology) | 8 |
| B010140 | C0101 · Bachelor of Technology | Electronics and Communication (Advanced Communication Technology) | 2 |
| B010141 | C0101 · Bachelor of Technology | Electronics and Communication Engineering | 123 |
| B010142 | C0101 · Bachelor of Technology | Electronics and Telecommunication Engineering | 2 |
| B010143 | C0101 · Bachelor of Technology | Electronics and communication Engineering (VLSI Design and Technology) | 1 |
| B010144 | C0101 · Bachelor of Technology | Environmental Engineering | 1 |
| B010145 | C0101 · Bachelor of Technology | Food Technology | 1 |
| B010146 | C0101 · Bachelor of Technology | Handloom & Textile Technology | 1 |
| B010147 | C0101 · Bachelor of Technology | Industrial Production Engineering | 1 |
| B010148 | C0101 · Bachelor of Technology | Information Technology | 67 |
| B010149 | C0101 · Bachelor of Technology | Information Technology (Collaboration & Twining Program) | 1 |
| B010150 | C0101 · Bachelor of Technology | Instrumentation and Control Engineering | 1 |
| B010151 | C0101 · Bachelor of Technology | Man Made Fibre Technology | 1 |
| B010152 | C0101 · Bachelor of Technology | Manufacturing Technology | 1 |
| B010153 | C0101 · Bachelor of Technology | Mechanical Engineering | 150 |
| B010154 | C0101 · Bachelor of Technology | Mechanical and Mechatronics Engineering(Additive Manufacturing) | 1 |
| B010155 | C0101 · Bachelor of Technology | Mining Engineering | 1 |
| B010156 | C0101 · Bachelor of Technology | Plastic Engineering | 1 |
| B010157 | C0101 · Bachelor of Technology | Production Engineering | 1 |
| B010158 | C0101 · Bachelor of Technology | Textile Chemistry | 1 |
| B010159 | C0101 · Bachelor of Technology | Textile Engineering | 1 |
| B010160 | C0101 · Bachelor of Technology | Textile Technology | 2 |
| B010201 | C0102 · Bachelor of Pharmacy | Bachelor of Pharmacy | 446 |
| B010301 | C0103 · Masters of Business Administration | Innovation Entrepreneurship and Venture Devlopement | 2 |
| B010302 | C0103 · Masters of Business Administration | MBA | 256 |
| B010303 | C0103 · Masters of Business Administration | MBA (AGRIBUSINESS MANAGEMENT) | 1 |
| B010304 | C0103 · Masters of Business Administration | MBA (Banking and Finance Service) | 2 |
| B010305 | C0103 · Masters of Business Administration | MBA (Health Care and Hospital Management) | 2 |
| B010306 | C0103 · Masters of Business Administration | MBA (Marketing and Finance) | 1 |
| B010307 | C0103 · Masters of Business Administration | MBA(Business Analytics) | 6 |
| B010308 | C0103 · Masters of Business Administration | MBA(Logistics and supply chain management) | 1 |
| B010401 | C0104 · Master of Pharmacy | PHARMACEUTICAL QUALITY ASSURANCE | 2 |
| B010402 | C0104 · Master of Pharmacy | Pharmaceutics | 93 |
| B010403 | C0104 · Master of Pharmacy | Pharmacogonosy | 22 |
| B010404 | C0104 · Master of Pharmacy | Pharmacology | 60 |
| B010405 | C0104 · Master of Pharmacy | Pharmacy Practice | 1 |
| B010406 | C0104 · Master of Pharmacy | Pharmceutical Chemistry | 55 |
| B010501 | C0105 · Master of Technology | Artificial Inelligence (AI) And Data Science | 2 |
| B010502 | C0105 · Master of Technology | Artificial Intelligence | 1 |
| B010503 | C0105 · Master of Technology | Bio Medical Engineering | 1 |
| B010504 | C0105 · Master of Technology | Biotechnology | 6 |
| B010505 | C0105 · Master of Technology | Civil Engineering | 8 |
| B010506 | C0105 · Master of Technology | Communication Engineering | 1 |
| B010507 | C0105 · Master of Technology | Computer Aided Design & Manufacturing | 4 |
| B010508 | C0105 · Master of Technology | Computer Science And Engineering(Artificial Intelligence & Machine Learning) | 3 |
| B010509 | C0105 · Master of Technology | Computer Science and Engineering | 57 |
| B010510 | C0105 · Master of Technology | Computer science and engineering with specialization in cyber security/ ICT | 1 |
| B010511 | C0105 · Master of Technology | Construction Technology & MGT | 1 |
| B010512 | C0105 · Master of Technology | Digital Communications | 1 |
| B010513 | C0105 · Master of Technology | Electrical & Electronics Engineering | 2 |
| B010514 | C0105 · Master of Technology | Electrical Drive and Control | 1 |
| B010515 | C0105 · Master of Technology | Electrical Engineering | 5 |
| B010516 | C0105 · Master of Technology | Electronics Engineering (VLSI Design) | 1 |
| B010517 | C0105 · Master of Technology | Electronics and Communication Engineering | 29 |
| B010518 | C0105 · Master of Technology | Energy Science & Technology | 2 |
| B010519 | C0105 · Master of Technology | Environmental Engineering | 4 |
| B010520 | C0105 · Master of Technology | Food Technology | 1 |
| B010521 | C0105 · Master of Technology | Geotechnical & Geoenvironmental Engineering | 1 |
| B010522 | C0105 · Master of Technology | Manufacturing Science & Technology | 1 |
| B010523 | C0105 · Master of Technology | Manufacturing Technology and Automation | 1 |
| B010524 | C0105 · Master of Technology | Mechanical Engineering | 19 |
| B010525 | C0105 · Master of Technology | Mechatronics | 1 |
| B010526 | C0105 · Master of Technology | Micro Electronic Engineering | 1 |
| B010527 | C0105 · Master of Technology | Nano Technology | 1 |
| B010528 | C0105 · Master of Technology | Plastic Engineering | 1 |
| B010529 | C0105 · Master of Technology | Power Electronics And Drives | 2 |
| B010530 | C0105 · Master of Technology | Power Systems | 2 |
| B010531 | C0105 · Master of Technology | Power and Energy System | 1 |
| B010532 | C0105 · Master of Technology | Production Engineering | 7 |
| B010533 | C0105 · Master of Technology | Remote Sensing & GIS | 1 |
| B010534 | C0105 · Master of Technology | Structural Engineering | 4 |
| B010535 | C0105 · Master of Technology | Textile Chemistry | 1 |
| B010536 | C0105 · Master of Technology | Textile Technology | 1 |
| B010537 | C0105 · Master of Technology | Thermal Engineering | 4 |
| B010538 | C0105 · Master of Technology | VLSI Design | 5 |
| B010601 | C0106 · Master of Computer Applications | MCA | 108 |
| B010701 | C0107 · BCA | BCA | 53 |
| B010801 | C0108 · BBA | BBA | 49 |
| B010901 | C0109 · Bachelor of Technology Working Professional | Computer Science and Engineering | 5 |
| B010902 | C0109 · Bachelor of Technology Working Professional | Electrical Engineering | 2 |
| B010903 | C0109 · Bachelor of Technology Working Professional | Electronics and Communication Engineering | 6 |
| B010904 | C0109 · Bachelor of Technology Working Professional | Mechanical Engineering | 5 |
| B011001 | C0110 · B Voc | Automobile Engineering | 2 |
| B011002 | C0110 · B Voc | Automobile Servicing | 2 |
| B011003 | C0110 · B Voc | BFSI | 2 |
| B011004 | C0110 · B Voc | Electronic Manufacturing Servicing | 1 |
| B011005 | C0110 · B Voc | Production Technology | 1 |
| B011006 | C0110 · B Voc | Refrigeration and Air Conditioning | 2 |
| B011007 | C0110 · B Voc | Robotics And Automation | 1 |
| B011008 | C0110 · B Voc | Software Development | 3 |
| B011101 | C0111 · PHARM D | Pharm D | 12 |
| B011102 | C0111 · PHARM D | Pharm D Post Baccalaureta | 2 |
| B011201 | C0112 · MBA(Integrated) | MBA(Integrated) | 11 |
| B011301 | C0113 · BHMCT | BHMCT | 7 |
| B011401 | C0114 · Bachelor of Architecture | Architecture | 6 |
| B011501 | C0115 · M.Tech (Part Time) | Civil Engineering | 1 |
| B011502 | C0115 · M.Tech (Part Time) | Computer Science and Engineering | 1 |
| B011503 | C0115 · M.Tech (Part Time) | Electrical Engineering | 1 |
| B011504 | C0115 · M.Tech (Part Time) | Electronics Engineering | 1 |
| B011505 | C0115 · M.Tech (Part Time) | Mechanical Engineering | 1 |
| B011601 | C0116 · Master of Technology Working Professional | Computer Science and Engineering | 3 |
| B011602 | C0116 · Master of Technology Working Professional | Mechanical Engineering | 1 |
| B011701 | C0117 · Master of Architecture | Environmental Design | 1 |
| B011702 | C0117 · Master of Architecture | Interior Design | 1 |
| B011703 | C0117 · Master of Architecture | Master of Architecture | 1 |
| B011801 | C0118 · MCA(Integrated) | MCA(Integrated) | 3 |
| B011901 | C0119 · Bachelor of Design | Bachelor of Design | 2 |
| B012001 | C0120 · BFAD | BFAD | 2 |
| B012101 | C0121 · M.Design. | M.Design. | 2 |
| B012201 | C0122 · Master of Planning | Infrastructure Planning and Management | 1 |
| B012202 | C0122 · Master of Planning | Urban and Regional Planning | 1 |
| B012301 | C0123 · MBA Working Professional | MBA | 2 |
| B012401 | C0124 · Bachelor of Fine Art(BFA) | Bachelor of Fine Art(BFA) | 1 |
| B012501 | C0125 · Master Of Integrated Technology | Computer Science and Engineering | 1 |
| B012601 | C0126 · MCA Working Professional | MCA | 1 |

## Institutes

All 784 institutes follow `I` + university(2) + 3-digit serial
(I01001 = institute `002`). The complete map lives in
the registry and in `/api/v1/institutes.json`; a 784-row table here would only go
stale — every institute row in every list endpoint carries its `rr_id`.

## Subjects (reserved)

The `S` prefix is reserved for the upcoming syllabus dataset. Subjects are
**flat** (`S0001`–`S9999`) because a subject is not a child of one branch — the
same subject is taught in many branches (all first-year B.Tech maths, for
example). The relationship is an *edge record*, with the flexible bits as data:

```json
{ "subject": "S0231", "branch": "B010101", "year": 1, "semester": 3 }
{ "subject": "S0231", "branch": "B010103", "year": 1, "semester": null }
```

`semester: null` is exactly the "free to be taught in any semester" case; if the
same subject ever sits in different years of different courses, that is two
edges, not a conflict. Every future use case (common subjects across branches,
everything taught in year 1, search by historic code) is a join over these edges.

---

_Generated by `scripts/build_api_tree.py` on 2026-11-13T14:32:36+05:30 — do not hand-edit; regenerate._
