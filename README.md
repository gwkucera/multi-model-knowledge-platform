# AI Knowledge Platform

A commercial-grade AI-assisted knowledge platform built
around a multi-model orchestration pipeline, structured
validation architecture, and human-in-the-loop review system.

This is not a prototype. It is designed to be maintainable,
auditable, and extensible.

---

## Architecture

### Four-Phase Completion Pipeline

Evidence is gathered and validated through three models
in sequence, each with a defined role:

**Phase 1 -- Evidence Gathering**
Perplexity Sonar (standard + pro tiers) with automatic
upgrade logic based on evidence quality thresholds.
PubMed NCBI E-utilities for high-safety domains.
Vector DB lookup via ChromaDB for corpus-specific retrieval.
Huberman Lab episode lookup via Chroma + Sonar fallback.

**Phase 2 -- Analytical Draft**
arcee-ai/trinity-large-thinking via OpenRouter.
Locked prompt rules enforce effect size ceilings,
evidence traceability, and dose-response field formats.
Fallback: Claude Sonnet via Anthropic direct.
120-second timeout with retry.

**Phase 3 -- Logic Audit**
moonshotai/kimi-k2.5 via OpenRouter.
Structured JSON audit report with section-level severity
ratings. Routing recommendation overridden by section
severity fields when inconsistent.
3-attempt retry with 0s/5s/15s backoff.
Fallback: o3 via OpenAI direct.

**Phase 4 -- Multi-Model Evidence and Risk Scoring**
Three independent models score each record in parallel:
DeepSeek V3 evaluates evidence quality and effect size.
Gemini 2.5 Flash evaluates risk level from the perspective
of the most vulnerable plausible user.
Kimi K2.5 arbitrates both assessments into a final score
with 0-1 confidence rating.
Claude Haiku reviews the hardest 10% of cases where models
disagree or confidence is low.
All intermediate scores stored for subsequent analysis.

---

## Key Design Decisions

**LLMs assist. They do not decide.**
Safety checks, scoring, and activation decisions are
deterministic. LLMs handle drafting, parsing, summarization,
and structured evaluation -- not logic.

**Versioned records, never overwritten.**
Every pipeline run increments a version. Historical records
are preserved as an audit trail.

**All weights and thresholds are configurable.**
Nothing is hardcoded. Routing thresholds, evidence weights,
and scoring parameters live in a config table.

**Human-in-the-loop review system.**
AI triage generates confidence-scored reviewer cards
(green/blue/yellow/red) with structured recommendations.
Humans make final activation decisions.

**Independent model perspectives.**
Evidence quality and risk are scored by separate models
with different prompts and analytical frames before
arbitration. No model sees another's output until
the arbitration stage.

---

## Review System

The curator review system includes:

- AI-generated triage cards with confidence scoring
  (Kimi K2.5)
- Confidence color distribution across the full queue
- Bulk confirmation flow for high-confidence approvals
- Edit flow with AI-generated diff preview and mandatory
  re-audit after edits
- Frontier disposition tier for records with thin evidence
  but low downside risk -- surfaced behind a tailored
  disclaimer rather than discarded
- Hard reject path for genuinely dangerous records
- Immutable edit history audit trail
- Confidence heuristic bulk processing with per-band
  activation logic

---

## Scoring System

800+ structured records scored across three dimensions
using a four-model consensus pipeline:

**Evidence Quality** (4 levels)
Well Studied / Some Research / Early Research /
Practitioner Supported

**Effect Size** (3 levels)
Meaningful / Moderate / Subtle

**Risk Level** (5 levels, conservative)
Safe under most conditions / Safe with awareness /
Risk in some situations / High risk in some situations /
Very high risk in some situations

All scores stored with full intermediate model outputs
for subsequent analysis and re-scoring as new models
become available.

---

## Taxonomy System

800+ structured records tagged across two orthogonal
dimensions using a junction table architecture:

- **Outcome dimension** -- what goal does this record serve?
  9 outcome categories, ~87 subcategories
- **Modality dimension** -- what type of intervention is this?
  7 modality categories including Clinical & Medical

Concurrent AI tagging (N=10) with smart resume semantics,
is_primary auto-correction, and version-based re-tagging
for taxonomy updates. Partial UNIQUE indexes enforce
data integrity at the DB layer.

---

## User-Facing Description Generation

Plain-language descriptions generated for every active
record using a three-phase enrichment pipeline:

**Phase 1:** Claude Haiku generates "What it is" and
"How it works" sections with self-assessed confidence
(HIGH / MEDIUM / LOW).

**Phase 2:** Perplexity Sonar enriches MEDIUM confidence
records with current search results before Haiku regenerates.

**Phase 3:** Perplexity Sonar Pro enriches LOW confidence
records with deeper search before final regeneration.

87.7% of records achieved HIGH confidence descriptions
on the first enrichment pass. All intermediate confidence
scores stored for quality tracking.

---

## Browse Experience

Three free browse dimensions for users:

- **By Outcome** -- what goal does this protocol serve?
- **By Protocol Type** -- what kind of intervention is this?
- **By Huberman Source** -- which episode discussed this?

Each protocol displays evidence quality, effect size,
and risk level indicators inline. Full protocol detail
includes sourced episode links with timestamps.

---

## Stack

**Backend:** Python 3.11+, FastAPI
**Databases:** PostgreSQL, ChromaDB
**Frontend:** React 18, Vite, React Router
**AI Models:** Perplexity Sonar, Trinity Large,
Kimi K2.5, Claude Sonnet, Claude Haiku,
DeepSeek V3, Gemini 2.5 Flash
**Infrastructure:** Docker, OpenRouter, Anthropic API,
OpenAI API, Google AI API

---

## Engineering Discipline

- 874 tests (backend + frontend)
- Branch-based development, no direct main commits
- Docker for all database operations
- Checkpoint logging every 25-50 records in batch runs
- Configurable thresholds, never hardcoded
- Concurrent batch processing with retry and fallback logic
- Strict JSON validators that raise on format mismatch
- Per-protocol versioned scoring with full audit trail
- Three-phase user description enrichment with
  confidence-gated quality control

---

## Status

Active development. Pipeline complete.
Review system complete. Taxonomy restructure complete.
Multi-model scoring complete (668 active/frontier records).
User-facing browse and protocol detail pages complete.
Phase 1 core runtime in progress.
