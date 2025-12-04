# **implementation-plan.md**

## **Purpose**

A step-by-step, phased build plan that turns the master specification into a stable, scalable product with minimal bugs. Each phase is split into small, mindless micro-tasks (actionable items a developer can pick up and complete). Priorities: **scalability, simplicity, predictable QA, and delivering an end-to-end MVP quickly.**

---

## **Principles for this plan**

* **Ship small, test often.** Deliver an end-to-end render pipeline early (MVP) then iterate UX/features.

* **Single responsibility tasks.** Each task does one thing and is independently testable.

* **Progressive enhancement.** Start with reliable FFmpeg fallbacks; add fancy Fal.ai features once stable.

* **Automate acceptance.** Every deliverable has an acceptance checklist (unit, integration, manual workflow test).

* **Cost-aware defaults.** Cheap models / 480p preview by default; tiered upgrades for Plus/Pro.

* **Follow Fal.ai engineering rules** — confirm model docs before integration.

---

## **Phase 0 — Setup & Foundations (1–2 sprints)**

Objective: Project bootstrapping so teams can work in parallel without friction.

Micro-tasks

* Repo setup: mono-repo with `web/` (Next.js), `api/` (serverless), `workers/` (render), infra IaC skeleton.

* Provision dev accounts: Clerk, Stripe (test), Supabase dev project, Backblaze test bucket, Vercel.

* Environment & secrets: central vault for API keys (dev/staging/prod).

* CI pipeline: run lint / tests / build; deploy to staging on merge to main.

* Baseline DB schema: seed Supabase with `users`, `projects`, `scripts`, `scenes`, `images`, `video_clips`, `renders`, `usage_logs`. (Use the conceptual model from master spec.)

* Basic RBAC & RLS: Supabase RLS policy to ensure user isolation.

Acceptance

* Can run app locally and deploy to staging; DB migrations succeed; secrets load; Clerk \+ Stripe test keys accepted.

---

## **Phase 1 — Core E2E MVP (2–4 sprints)**

Objective: Build the end-to-end flow: idea → final MP4 (low-res preview \+ final render) with basic UI.

High-level tasks and micro-tasks (ordered for low-bug delivery)

1. **Auth & User onboarding**

   * Integrate Clerk for signup/login/email verification.

   * Connect Clerk user to `users` table; plan field `plan` default `free`.

   * Acceptance: signup → user row created; login returns session.

2. **Project CRUD \+ Dashboard**

   * POST /api/projects, GET /api/projects, rename, duplicate, delete.

   * UI: minimal dashboard grid with thumbnails, statuses.

   * Acceptance: Create project and view it in dashboard.

3. **Script generation (Fal.ai wrapper)**

   * Implement `/api/script` endpoint that will POST to Fal.ai OpenRouter.

   * Local mock mode: return deterministic script for offline dev.

   * UI: "Generate script" \+ editable textarea \+ versioning metadata.

   * Acceptance: Script generate → persisted `scripts` row.

4. **Scene/storyboard generation**

   * `/api/scenes` endpoint (uses local rule-based generator for MVP or Fal.ai if available).

   * UI: continuous-scroll scenes list with editable narration, image prompt fields.

   * Acceptance: scenes saved and editable.

5. **Image generation (basic)**

   * `/api/images` endpoint: integrate cheapest Fal.ai image model and provide fallback placeholder images.

   * Store image metadata in `images` and upload to Backblaze.

   * UI: image results carousel, select/replace, versioning.

   * Acceptance: generated image appears, persisted, accessible by signed URL.

6. **Clip generation (basic)**

   * `/api/clips` endpoint: request Fal.ai clip model for scenes; if model lacks feature, generate a placeholder static clip (image→video using FFmpeg).

   * Store in Backblaze; record `video_clips`.

   * Acceptance: scene → clip (3s) available.

7. **Transitions & FFmpeg assembly worker**

   * Start `workers/assemble` job that accepts job payload and runs FFmpeg assembly (concatenate \+ transition templates). Use fallback templates from spec.

   * Implement queue (simple pub/sub or Supabase queue) and worker durable retries.

   * Acceptance: submit render job → background worker runs → final MP4 saved to Backblaze.

8. **Music & Voice-over (MVP)**

   * Music: Pixabay search UI \+ stream preview (no permanent download).

   * Voice-over: single continuous TTS track via fal-ai/elevenlabs (or mock in dev).

   * Acceptance: music & TTS combined into final render.

9. **Captions**

   * Auto-generate captions using Fal.ai subtitle utility or accept upload; simple burn-in option via FFmpeg.

   * Acceptance: captions editable and can be burned into render.

10. **Real-time updates**

    * Use Supabase Realtime or lightweight Pusher to stream render status updates to the UI.

    * Acceptance: user sees real-time progress events.

11. **Billing & plans (basic)**

    * Stripe integration: Free/Plus/Pro product tiers; create checkout flow for Plus/Pro.

    * Plan gating for model access and retention rules.

Quality & Testing Tasks

* Create end-to-end tests that run script → render pipeline using mocked Fal.ai responses and a small FFmpeg test fixture.

* Add job queue failure tests and retry logic tests.

Acceptance Criteria (MVP)

* Full workflow from idea → final MP4 completes (480p preview \+ one 720p/1080p final) in staging.

* Users can edit at every step; versions persist.

* Real-time progress visible.

* Stripe and Clerk flows operate in test mode.

* Admin panel reads usage logs.

Reference: Master spec details for models, transitions, and persistence.

---

## **Phase 2 — Stability, UX polish & Scale (2–3 sprints)**

Objective: Harden system, reduce edge-case failures, refine UX.

Tasks

* **Model capability awareness**: Build DB table for model capabilities (params, max size, rate limits). On integration, fetch and cache Fal.ai docs reference. (Required by Fal.ai rules.)

* **Advanced transitions preview**: Pre-render small preview loops for hover states.

* **Version history UI**: timeline for each asset (image, clip) with rollback.

* **Queue scaling**: swap to Redis or managed queue for large loads; autoscaling worker pool.

* **Cost controls**: throttle jobs for free tier, queue priority for Pro.

* **Monitoring & observability**: add logs, Sentry, Prometheus-style metrics for job durations, costs.

* **Robust error messaging**: friendly, kind microcopy and undo affordances per design-tips.

Acceptance

* Jobs scale under synthetic load; error rate under threshold; UI shows clear recovery actions.

---

## **Phase 3 — Feature Expansion & Productivity (3+ sprints)**

Objective: Add collaborative workflows, templates, brand kits.

Tasks (examples)

* Templates library \+ one-click apply.

* Collaboration: shared projects with simple roleless access (all users equal per your request).

* Brand kits: persistent fonts, color overlays, intro/outro templates.

* Advanced audio editing: beat-matching, auto-cut to music.

* Marketplace for presets.

Acceptance

* Feature toggles behind flags; user testing shows improved speed-to-video for non-technical users.

---

## **Team Roles & Recommended Rituals**

Roles (small core team)

* **CTO / Tech Lead** — architecture, trade-offs, Fal.ai compliance.

* **Full-stack Engineer** (Next.js \+ Vercel) — UI \+ serverless endpoints.

* **Backend Engineer** (workers & FFmpeg) — workers, queues, storage.

* **ML / Integration Engineer** — Fal.ai integrations, model capability mapping.

* **Product Designer** — UI flows, motion, microcopy (follows `design-tips.md`).

* **QA / SRE** — tests, monitoring, cost tracking.

Rituals

* **Weekly triage** (30 min): flaky jobs, cost spikes, model errors.

* **Bi-weekly usability sync** (30 min) with 3-user guerrilla tests (per Lovable principle P6).

* **Daily standup** (15 min) for active sprints.

* **Monthly design review**: ensure emotional intent still holds.

---

## **Timeline (suggested)**

* Phase 0: 1–2 sprints (2–4 weeks)

* Phase 1 (MVP): 2–4 sprints (4–8 weeks)

* Phase 2: 2–3 sprints (4–6 weeks)

* Phase 3: ongoing

(Adjust sprints to your team cadence; these are conservative estimates.)

---

## **Acceptance Criteria & QA Checklist (per deliverable)**

For each endpoint/feature implement:

* Unit tests for core logic.

* Integration test for API → DB → storage.

* Worker test job that completes within expected time for a small sample project.

* Manual UX runthrough: create idea → generate script → generate images → assemble final render.

* Cost estimate recorded for model calls in `usage_logs`.

---

## **Rollback & Recovery Plan**

* Jobs persisted with idempotency tokens.

* Worker retries: exponential backoff with max attempts; on repeated failure, move to dead-letter queue and surface helpful recovery steps in UI.

* Storage cleanup: quarantine retention before deletion; allow admin forced-restore within 48 hours.

---

## **Notes & Trade-offs**

* **Use Vercel workers for speed vs. dedicated render cluster for heavy loads.** Start with Vercel for time-to-market; move to dedicated workers if throughput/cost becomes an issue.

* **Fal.ai managed models speed development** but lock you into their rate limits and pricing; store model metadata so you can swap to open-source or other providers later.

* **FFmpeg fallback is essential** — some Fal.ai features may be limited; guarantee a deterministic FFmpeg path for all key transforms.

---

## **Final deliverables from this plan**

* Working MVP in staging that completes full pipeline.

* CI and backup \+ monitoring.

* Documented runbook and cost controls.

* Acceptance test suite that runs nightly.

