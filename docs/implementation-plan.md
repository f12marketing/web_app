implementation-plan.md — security & ops hardened
Purpose

A step-by-step, phased build plan that turns the master specification into a stable, secure, and scalable product with minimal bugs. Phases are broken into single-responsibility micro-tasks so developers can pick tasks and deliver predictable results. Priorities: security-by-design, scalability, simplicity, predictable QA, cost controls, and a fast end-to-end MVP.

Principles (short, mandatory list)

Security-first SDLC: threat models, SAST/SCA in CI, IaC reviews, mandatory code review.

Ship small + test often: E2E pipeline early, follow with hardening sprints.

Single responsibility tasks: one job = one test.

Progressive enhancement: FFmpeg fallback first; Fal.ai features layered on top.

Automated acceptance: unit + integration + smoke + nightly E2E.

Cost-aware defaults: 480p preview, cheapest models by default, plan quotas.

Fal.ai compliance: always reference Fal.ai docs before using a model.

Phase 0 — Setup & Foundations (1–2 sprints)

Objective: bootstrap infra, security controls, and developer ergonomics so teams can work in parallel securely.

Micro-tasks (exact)

Repo & monorepo structure

Create mono-repo: web/ (Next.js), api/ (serverless), workers/ (render), infra/ (IaC).

Enforce branch protection and commit signing on main branches.

IaC & environments

Write Terraform/Pulumi skeleton for prod/staging/dev.

Define least-privilege IAM roles for services.

Add automated IaC plan checks and drift detection in CI.

Secrets & config

Provision Secrets Manager (Vault/AWS Secrets Manager/Vercel secrets).

Establish key rotation policy and human-access audits.

Accounts & dev provisioning

Create dev/staging projects: Clerk (dev keys), Stripe (test), Supabase (dev), Backblaze (test), Vercel.

Lock down prod keys to CI/CD and admin-only consoles.

Baseline DB schema & RLS

Seed core tables: users, projects, scripts, scenes, images, video_clips, renders, usage_logs, audit_logs.

Implement Supabase row-level security (RLS) policies; test them thoroughly.

CI/CD & security gates

CI: lint, test, SAST (e.g., CodeQL), SCA (dependabot-like), build.

Block merges on critical security findings.

Canary deploy to staging; auto-rollback on health failures.

Local dev ergonomics

Provide docker-compose or local stubs for Fal.ai calls (mock server).

Observability baseline

Provision logging & monitoring (Sentry + Prometheus + central logs).

Acceptance

Local app runs; staging deploys pass CI; secrets load; Clerk + Stripe test keys accepted; RLS blocking cross-user access in staging.

Phase 1 — Core E2E MVP (2–4 sprints)

Objective: Build a secure end-to-end flow (Idea → 480p preview → final MP4) with baseline protections and cost controls.

Security-forward micro-tasks (ordered to minimize blast radius)

Auth & Onboarding

Integrate Clerk; enable secure cookie flags, session TTLs.

Implement optional 2FA for early adopters.

Create users record mapping and plan defaults.

Security tasks: verify Clerk webhooks signature validation; restrict API keys to origin domains.

Acceptance: signup creates row; sessions valid; insecure tokens rejected.

Project CRUD + Dashboard

Build secure endpoints with server-side auth checks.

DB queries must include user_id filter; test RLS blocking.

Acceptance: create/read/update/delete works only for owner accounts.

Script generation (Fal.ai wrapper)

Endpoint /api/script routes through a model integration layer that:

sanitizes inputs (strip HTML/control chars),

enforces per-user rate limits,

logs request hash and model meta to usage_logs.

Provide mock mode for offline dev.

Acceptance: scripts persist; usage logs include request hash and cost estimate.

Scene / Storyboard generation

/api/scenes with edit/save and strict input validation.

Security tasks: detect and redact PII where flagged; mark redaction_flags in DB.

Acceptance: scenes editable and validated.

Image generation (basic)

/api/images calls Fal.ai (cheapest model) through integration layer.

Store artifacts in Backblaze via server-side upload; return short-lived signed URLs.

Persist checksums and encryption key identifiers with each image.

Acceptance: images accessible via signed URL only; checksum stored.

Clip generation (basic)

/api/clips creates clips via Fal.ai or FFmpeg fallback.

Workers run in isolated containers with CPU/memory limits and job timeouts.

Job metadata: idempotency token, request hash, user_id.

Acceptance: 3s clips available; worker logs job trace.

Transitions & FFmpeg assembly worker

Implement workers/assemble as isolated worker pool; use queue backed by Redis or Supabase queue.

Worker must: validate job, fetch signed assets, assemble via FFmpeg, upload final to Backblaze with encryption metadata.

Use dead-letter queue with alerting.

Acceptance: submit render job → worker completes → final MP4 saved with signed URL + cost estimate logged.

Music & Voice-over

Music: Pixabay streaming only; do not persist music unless user explicitly saves. Validate license metadata and store usage_terms.

Voice-over: create TTS via Fal.ai/ELEVENLABS wrapper; log voice model meta and cost. Mask any user PII in TTS payloads if requested.

Acceptance: audio tracks included in final render; usage logged.

Captions

Auto-captions via Fal.ai subtitle model; saved as SRT; allow user edits.

Burn-in via FFmpeg with preset styles.

Acceptance: captions editable and burn-in works; SRT downloadable.

Real-time updates

Use Supabase Realtime or Pusher with short-lived tokens per session.

Limit payload size and frequency to prevent abuse.

Acceptance: UI receives render progress and job events securely.

Billing & Plans

Integrate Stripe with hosted checkout, validate webhooks, persist subscription state.

Enforce plan-based model gating server-side (not only UI).

Acceptance: plan upgrades applied server-side, model access toggled, retention policy enforced.

Quality & Testing

Build E2E tests that perform Idea→Render using mocked Fal.ai for reliability.

Load test workers and queue under synthetic jobs; assert cost budget enforcement.

Add automated job-failure and retry tests.

MVP Acceptance Criteria (tight)

Full flow works in staging: Idea → 480p preview → final MP4 (720p/1080p optional).

RLS prevents cross-user access in staging/prod.

Media only accessible via signed, short-lived URLs; no public buckets.

Usage logs exist and reconcile with model calls.

Dead-letter queues and alerts for failing jobs.

Billing flows tested in Stripe test mode.

CI security scans pass; deployments use IaC plans.

Phase 2 — Stability, UX polish & Scale (2–3 sprints)

Objective: Harden the platform, reduce failures, improve UX, scale queues and cost controls.

Tasks (security & scale)

Model capabilities table

Persist model capability metadata, rate limits, accepted params; fetch Fal.ai docs and cache URL per model (Fal.ai compliance).

Transitions preview

Pre-render low-res preview loops for hover; store as cached assets with TTL to avoid re-renders.

Version history & rollback

Implement immutable versioning with dedupe; allow rollback while preserving audit trails.

Queue scaling

Move queue to Redis/managed queue (e.g., RQ, Bull), add autoscaling rules for workers, and maintain concurrency limits per plan.

Cost controls

Implement per-user budgets, model-call budget alerts, and emergency throttling.

Monitoring & observability

Add SLOs, dashboards (render latency, job success rate, cost per minute), and alerts (pager duty).

Robust UX & copy

Friendly recovery flows, undo affordances, descriptive error pages that avoid technical exposure (design-tips aligned).

Acceptance

Under synthetic load, jobs scale and error rate < defined threshold; cost alerts trigger correctly.

Phase 3 — Feature Expansion & Productivity (3+ sprints)

Objective: Add collaboration, templates, brand kits; prepare for enterprise demands.

Tasks

Templates / Brand kits

Persist kit assets with tenant-level encryption options.

Collaboration (roleless access)

Implement simple share links or project-level sharing; maintain audit logs for every change.

Advanced audio

Beat-matching & auto-slicing — use deterministic, testable algorithms with bounded CPU cost.

Marketplace

Vetting workflow, content moderation, contractual terms, and revenue accounting.

Acceptance

Feature flags control rollout; user studies show reduced time-to-video for non-technical users.

Team Roles & Recommended Rituals (secure-minded)

CTO / Tech Lead — security & architecture sign-off, Fal.ai compliance.

Full-stack Engineer — secure Next.js patterns, server-side checks.

Backend Engineer — worker orchestration, queue, FFmpeg encapsulation.

ML / Integration Engineer — Fal.ai modeling, capability mapping.

Product Designer — emotional UX, error messaging (design-tips).

QA / SRE — test suite, monitoring, incident ops.

Rituals

Weekly security triage (costs, open alerts).

Bi-weekly 3-user guerrilla tests (Lovable P6).

Incident post-mortems required for P1 incidents.

Timeline (recommended)

Phase 0: 2–4 weeks (1–2 sprints)

Phase 1 (MVP): 4–8 weeks (2–4 sprints)

Phase 2: 4–6 weeks (2–3 sprints)

Phase 3: ongoing

Adjust to team cadence and parallel workstreams.

Acceptance Criteria & QA Checklist (per deliverable)

For every endpoint/feature:

Unit tests & integration tests present.

DB migrations tested; RLS validated.

Worker test job completes within expected time.

E2E manual workflow (Idea→Script→Preview→Render) passes nightly in staging.

Cost estimate recorded for model calls in usage_logs.

All deployments pass SAST/SCA gates.

Signed URL verification and expiration enforced.

Rollback & Recovery Plan

Idempotent jobs: use idempotency tokens; detect duplicates.

Retries & DLQ: exponential backoff, bounded retries, then dead-letter queue and human alert.

Storage cleanup: quarantine before permanent delete; retention enforcement per plan.

Backups: automated DB backups + periodic test restores; media snapshots for paid users.

Incident response: documented runbooks and on-call rotation.

Notes & Trade-offs (explicit)

Vercel workers: fast TTM, but monitor cost vs. dedicated render infra. Use Vercel for MVP; move to dedicated containers (GKE/Cloud Run) if throughput/cost warrants.

Fal.ai dependency: store model metadata and be ready to swap providers. Use abstraction layer for model calls to limit vendor lock-in.

Deterministic FFmpeg path: essential fallback. Keep exhaustive FFmpeg templates and test vectors.

Final deliverables from this plan

Secure, working MVP in staging with full E2E pipeline.

CI/CD with security gates and nightly E2E.

Monitoring, cost controls, and runbook for production readiness.

Acceptance test suite and scheduled pentest before broad rollout.

Security Checklist (quick highlights to confirm)

IaC PR review + drift detection.

Secrets manager + rotation.

RLS across DB.

Signed URLs for media.

WAF + rate limiting.

Model integration layer with input sanitization & cost logging.

Dead-letter queues & alerts.

Backups + tested restores.

Pen test scheduled and remediations tracked.

References

Primary spec: file:///mnt/data/MASTER SYSTEM SPECIFICATION DOCUMENT.docx

Design guidance: design-tips.md (used for microcopy & motion).
