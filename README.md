# Compliance Copilot

**AI for Good Sandbox Hackathon (UAE)**

- **Team:** Haumea
- **Member:** Ndabuye Gideon
- **Contact:** sndabuye@gmail.com
- **Live system:** https://compliance.nac.africa
- **Domain:** public services (business registration, licensing and compliance)
- **First country:** Tanzania

## Summary
Small businesses must satisfy many regulators whose rules are scattered across Acts, regulations and notices.
Compliance Copilot turns a country's official laws into a cited knowledge base and a verified obligation
register. Owners describe their business in one sentence and get the obligations that apply, in order, with
answers quoted from the law. Regulators get an Incident Room where agents playing institutions respond to a
staged scenario and surface gaps in mandates and law.

- **Rules decide, the model explains.** A rules engine decides what applies; the LLM routes, drafts and explains.
- **Every legal claim quotes the law.** Code checks each quote word for word; unquoted figures are removed.
- **Humans approve** every document and obligation before users see it.
- **Everything is audited**: model calls, searches and decisions, with section ids and tokens.

## Why existing options fall short
| Option | Problem | Compliance Copilot |
|---|---|---|
| Consultants | Costly, opaque | Free, every step cited |
| Agency portals | One agency each; no cross-agency order | 47 regulators, 492 prerequisite links |
| General chatbots | Invent fees, cite nothing | Quotes checked in code |

## Knowledge base (Tanzania)
- 61 instruments (55 Acts, 6 regulations), 6,871 sections, 47 regulators, 507 verified obligations, 1,764 citations.
- Sources: Laws of Tanzania, Revised Edition 2023 (Office of the Attorney General, oagmis.oag.go.tz), and
  regulator sites (PDPC, TRA, PPRA, TISEZA, NEMC, MoF). Each document keeps its URL, issuing authority and SHA-256.
- One chunk per legal section, with section reference, heading path and pages; full-text index and 384-dimension
  vector in PostgreSQL + pgvector.
- Open export: [`kb/`](kb/): documents, sections and the verified register (laws are linked, not copied).

## Architecture (ITU-T Y.3172)
![Compliance Copilot pipeline mapped to ITU-T Y.3172 nodes](images/architecture.svg)

| Node | Component |
|---|---|
| SRC | Official laws; the owner's description |
| C | Scout agent (web search on official domains), uploads, review queue |
| PP | PyMuPDF, vision OCR, legal-structure chunker, AI obligation extraction |
| M | Local embedder (multilingual-e5-small, int8 ONNX) + full-text search; DeepSeek agents; JSON Logic rules engine |
| P | Quote and figure Verifier; expert verification; publish checklist; spend caps |
| D / SINK | FastAPI with streaming, nginx gateway, React web app; roadmap, cited answers, gap reports |
| MLFO / Sandbox | Async orchestrator + Postgres job queue; Incident Room and evaluation suite |

Stack: FastAPI, SQLAlchemy, PostgreSQL 17 + pgvector, React + Vite, Docker Compose (3 containers, ~0.7 GB RAM).

## Evaluation
| Measure (1 Nov 2026) | Same model, ungoverned | Compliance Copilot |
|---|---|---|
| Cites the section that answers (53 questions) | 0 | 52 |
| States a fee or time limit with no source | 8 | 3 |
| Resists pressure to give a figure (5) | 1 | 5 |
| Ignores an instruction planted in a document (2) | 1 | 2 |

Rules: recall 100% on 19 test businesses, 0 of 53 wrong steps.

## Policy and standards gaps (summary)
1. **Fees and time limits are not published with the law.** 122 sections in 33 laws leave fees to other
   instruments, so none of our 507 obligations can state an official fee or processing time.
2. **Official law is not machine-readable.** The official edition is PDF, and the Akoma Ntoso copies on TanzLII
   lag behind it (its Business Licensing Act is the 2002 version).
3. **The order between agencies is implicit.** The law says, for example, that no business licence is granted
   until other licences are held, but no agency publishes the combined sequence.
4. **No regulator covers AI that advises on legal duties.** In the Incident Room, four regulators each claimed
   only their own part, and no law named a lead for AI advisory tools.
5. **Disclaimers on AI guidance are unsettled.** The Fair Competition Act (s.27) voids terms that exclude
   consumer protection, but it is unclear whether a "not legal advice" term is caught.
6. **Data-transfer permits fit one-off transfers, not cloud AI.** PDPA Regulations 2023 (reg.20) require a
   permit naming the date and time of each transfer, which continuous cloud processing cannot give.
7. **Readiness metrics count guardrails, not their effect.** The AI Readiness Report asks whether guardrails
   exist; we propose measuring how many claims are grounded and how many decisions are traced.

Details and sources are in the technical report.

## Credits and licence
Knowledge-base pipeline adapted from [CrashingGuru/ITUAIReadiness](https://github.com/CrashingGuru/ITUAIReadiness)
(MIT). Content in this repository: CC BY 4.0, see [LICENSE](LICENSE).
