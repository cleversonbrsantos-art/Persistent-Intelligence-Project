# Field Note 002 — Data Integrity Audit and Correction of Previously Published Metrics

## Context

During routine analysis of operational records, the project found that a large share of stored data did not come from real interactions.

Test artifacts from automated test runs had been written to the same stores as real interaction data.

---

# 1. What the audit found

* About 82% of recorded messages were test artifacts
* About 66% of episodic memories were test artifacts

Both stores were cleaned after full forensic audits, with backups taken beforehand.

---

# 2. Impact on previously published figures

Metrics published in June 2026 were computed before the cleanup.

| Figure | Status |
| ------ | ------ |
| Interaction and memory counts | Superseded |
| Cognitive Independence Score | Superseded |
| External model dependency (76.1% in README; internal reports ranged 63–78%) | Superseded |
| External model dependency after audit | 86.5% |

The earlier figures made the system look less dependent on external models than it was.

---

# 3. What still drives external model calls

Share of external calls by request type, after the audit:

| Request type | Share |
| ------------ | ----: |
| Unclassified | 56.7% |
| Factual questions | 19.4% |
| Questions about the entity itself | 10.1% |
| Technical requests | 9.3% |

---

# 4. Consequences

* A routing path was introduced where memory answers directly and the external model only phrases the answer, for requests whose content memory already guarantees.
* Backups are required before any change to persistent identity structures.
* Observation #004 and Milestone #002 are marked as pending revalidation on audited data.

---

# Why this is being documented

Public research has to correct itself in public.

The earlier numbers were published in good faith. Once they were found to be unreliable, leaving them unmarked would have contradicted the project's own rule that documentation reflects verified progress.

Data provenance is now treated as a prerequisite for publishing any metric.
