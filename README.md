AI Knowledge Platform

A commercial-grade AI-assisted knowledge platform 
built around a multi-model orchestration pipeline, 
structured validation architecture, and 
human-in-the-loop review system.

This is not a prototype. It is designed to be 
maintainable, auditable, and extensible.

Architecture

Four-Phase Completion Pipeline

Phase 1 — Evidence Gathering
Perplexity Sonar with automatic tier upgrade 
logic based on evidence quality thresholds. 
Domain-specific API enrichment for 
high-safety records. Vector DB retrieval 
via ChromaDB.

Phase 2 — Analytical Draft
Primary model via OpenRouter with locked 
prompt rules enforcing effect size ceilings, 
evidence traceability, and structured field 
formats. Claude Sonnet fallback via 
Anthropic direct.

Phase 3 — Logic Audit
Structured JSON audit report with 
section-level severity ratings. Routing 
recommendation overridden by section 
severity fields when inconsistent. 
Retry with backoff. o3 fallback.

Phase 4 — Multi-Model Consensus Scoring
Three independent models score each record 
in parallel across evidence quality, effect 
size, and risk. A fourth model arbitrates 
disagreements with confidence scoring. 
A fifth reviews the hardest cases where 
models conflict or confidence is low.

Key Design Decisions

LLMs assist. They do not decide.
Safety checks, scoring, and activation 
decisions are deterministic.

Versioned records, never overwritten.
Every pipeline run increments a version. 
Historical records are preserved.

All weights and thresholds are configurable.
Nothing is hardcoded.

Human-in-the-loop review system.
AI triage generates confidence-scored 
reviewer cards. Humans make final 
activation decisions.

Independent model perspectives.
Evidence quality and risk scored by 
separate models before arbitration.

Review System

AI-generated triage cards with 
confidence scoring
Confidence color distribution across 
the full queue
Bulk confirmation flow for 
high-confidence approvals
Edit flow with AI-generated diff preview 
and mandatory re-audit after edits
Hard reject path for unsafe records
Immutable edit history audit trail

Scoring System

1,000+ structured records scored across 
three dimensions:

Evidence Quality (4 levels)
Effect Size (3 levels)  
Risk Level (5 levels, conservative)

All scores stored with full intermediate 
model outputs for re-scoring as models improve.

Taxonomy System

Records tagged across two orthogonal 
dimensions using a junction table architecture:

Goal dimension — what outcome does this 
record serve?
Type dimension — what kind of intervention 
is this?

Engineering Discipline

1,200+ tests (backend + frontend)
Branch-based development
Docker for all database operations
Checkpoint logging in batch runs
Configurable thresholds, never hardcoded
Concurrent batch processing with 
retry and fallback
Strict JSON validators
Per-record versioned scoring with 
full audit trail

Stack

Backend: Python 3.11+, FastAPI
Databases: PostgreSQL, ChromaDB
Frontend: React 18, Vite, React Router
Infrastructure: Docker, OpenRouter, 
Anthropic API, OpenAI API

Status

Active development. Pipeline complete. 
Review system complete. Multi-model 
scoring complete. User-facing browse 
and detail pages complete. 
Runtime in progress.
