masterplan.md — security-hardened edition
1. 30-Second Elevator Pitch

An enterprise-grade, AI-first continuous-scroll video creation platform that turns an idea into a fully edited, professional-quality MP4 — fast, intuitive, and secure. Users feel powerful and at ease while generating scripts, scenes, images, clips, captions, music, and voice-over — without technical skills. Built on Fal.ai + FFmpeg + Supabase + Vercel + Backblaze with strict security, privacy, and operational controls.

2. Problem & Mission
Problem

Video production is fragmented and technically demanding.

AI tooling often sacrifices control or privacy.

Customers demand both simplicity and trustworthiness.

Mission

Deliver a calming, empowering creative experience while treating user data responsibly: secure-by-design, auditable, and cost-efficient.

3. Target Audience

Creators (Plus/Pro): high-usage power users who value reliability and speed.

Casual Free Users: low-touch users who need safe, private defaults.

Operators & Compliance stakeholders: require traceability, data controls, and cost transparency.

4. Core Features (unchanged; operationally hardened)

Continuous-scroll workspace (Idea → Render)

Script generation (Fal.ai OpenRouter) with model-call auditing

Storyboard + scene-level editing with versioning

Image & clip generation via Fal.ai, FFmpeg fallback

31 FFmpeg transitions + preview assets

Music search (Pixabay) — stream-only unless explicitly saved

Voice-over via Fal.ai/ELEVENLABS (TTS) with usage logs

Auto-captions + styles + burn-in

Render assembly via background workers (FFmpeg)

Real-time progress, quotas, and plan-based limits

Clerk auth and Stripe billing

Paid-user retention policies with explicit consent

Security & operational changes are integrated into every feature below.

5. High-Level Tech Stack (security notes)
Frontend

Next.js App Router — server-rendered surface for safer XSS surface control.

Use strict CSP, sanitized client-side rendering of user-entered content, and framework security patches applied promptly.

Tailwind + shadcn/ui — locked version in package.json and dependency vulnerability scanning in CI.

Realtime: Supabase Realtime or Pusher — communicate minimal state; authenticate channels with short-lived tokens.

Backend

Vercel Serverless Functions — lightweight endpoints with short timeouts; sensitive workloads in background workers (see workers infra). Use environment-specific deployments (dev/staging/prod) and immutable artifacts.

Background Workers (Vercel/Container/Cloud Run) — long-running FFmpeg assembly runs in isolated worker instances with resource limits and job idempotency.

Fal.ai APIs — calls routed through a dedicated integration layer that:

sanitizes inputs,

enforces per-user/model rate limits,

logs model metadata,

records costs to usage_logs for billing and audit.

Storage

Backblaze B2 — for media; all files are encrypted at rest and served via short-lived signed URLs. Path and bucket access constrained by service account keys stored in a secrets manager.

Supabase (Postgres) — metadata store with RLS policies to enforce row-level access by user_id. Backups encrypted and tested.

Auth & Billing

Clerk — identity provider with SSO-ready hooks. Use session management with secure cookie flags, short session TTLs for sensitive operations, and optional 2FA.

Stripe — hosted checkout integration; no card data stored in your systems. Webhook validation and signature checks enabled.

6. Conceptual Data Model (with security/ops fields)

(Keep core entities; add audit and governance columns)

users

id, clerk_id, email, plan, mfa_enabled, created_at, deleted_at

projects

id, user_id, name, duration, aspect_ratio, preset, quality, status, retention_policy, legal_consent_flag, updated_at

scripts

id, project_id, content, version, model_meta, created_by, created_at

scenes

id, project_id, scene_index, narration, image_prompt, redaction_flags, updated_at

images

id, scene_id, project_id, url, model_meta, version, checksum, encryption_key_id

video_clips

id, scene_id, project_id, url, duration_ms, model_meta, checksum

music_tracks

id, project_id, pixabay_id, metadata, usage_terms

captions

id, project_id, srt_text, style_preset, burned_in, created_at

renders

id, project_id, status, final_url, resolution, cost_estimate, created_at

usage_logs

id, user_id, model, tokens, cost, request_payload_hash, timestamp

audit_logs

id, user_id, action, target_id, outcome, ip_address, user_agent, timestamp

Add checksums, encryption_key_id, and payload hashes to support integrity verification and forensic audits.

7. UI Design Principles (security-aware)

Keep emotional thesis: empowered, powerful, at ease.

Apply principle of least surprise: never surface technical error messages; surface actionable recovery steps.

Default privacy-first UX: opt-in for features that store or share personal data (e.g., permanent retention).

Transparent billing and usage meters in the UI.

Clear consent flows and policy links (retention, content moderation, acceptable use).

8. Security, Privacy & Compliance (core section)
8.1 Security-by-Design

Adopt secure SDLC: threat modeling for major flows, static analysis, dependency scanning, and CI gates before merge.

All infra is defined as IaC (Terraform or Pulumi). PRs to IaC must be code-reviewed and signed off.

Enforce branch protection, mandatory code reviews (2 approvers for infra or security-impact changes).

8.2 Data Protection

Encryption in transit: TLS 1.2+ for all services. Enforce HSTS and modern ciphers.

Encryption at rest: Backblaze and DB storage using provider-managed encryption keys; optionally use envelope encryption with KMS for critical artifacts.

Secrets Management: Use a central secrets manager (HashiCorp Vault, AWS Secrets Manager, or Vercel secrets). Rotate keys on a schedule and after personnel changes.

Signed URLs: All media access via short-lived signed URLs. No direct public access to buckets.

Data Minimization: Only persist data required for functionality. Drafts older than TTL auto-pruned; deletion quarantined for 48 hours.

8.3 Access Controls

Least Privilege IAM: Service accounts with minimal roles. No long-lived admin keys in code.

RLS: Supabase row-level security ensures users only access their data.

Admin Access: Admin-only capabilities exist off a separate admin console and require additional SSO group membership and short lived session tokens.

Audit logging: Every critical action (renders, model calls, billing events) logged with user context and retained per policy.

8.4 Network & Perimeter

Harden all public endpoints with WAF (Cloudflare / AWS WAF) rules for OWASP Top 10 protection.

Rate limiting at API gateway to prevent abuse and model-cost spikes.

Use Cloud-level DDoS protection and automated anomaly detection.

8.5 Application Security

Parameterized DB queries and ORM safeguards to prevent SQL injection.

CSP, X-Frame-Options, secure cookie flags (HttpOnly, Secure, SameSite=Strict) set.

Sanitize user inputs for prompts; strip active HTML in user-provided text to avoid XSS.

Validate and quarantine third-party content (e.g., user uploads).

8.6 Third-Party & Supply Chain

Vendor inventory and risk assessment for Fal.ai, Backblaze, Supabase, Clerk, Stripe, Pixabay, and any cloud providers. Maintain SLAs, data processing agreements, and proof of security posture.

Dependabot or equivalent for dependency updates plus weekly review of critical CVEs.

8.7 Privacy & Compliance

Implement GDPR/CCPA data access, deletion, and export endpoints.

Capture user consent for data retention and processing of media (especially voice or personal likenesses).

Data residency considerations documented — allow for regional storage if needed.

8.8 Incident Response & Forensics

Maintain an incident response playbook with roles, communication templates, and escalation contacts.

Have immutable logs and forensic snapshots for major incidents.

Conduct tabletop exercises quarterly.

8.9 Continuous Monitoring & SLOs

Define SLOs for render success rate, job latency (preview and final), and uptime.

Monitor costs per model-call; alert on cost anomalies.

Integrate Sentry, Prometheus, and centralized log aggregation (ELK / DataDog).

8.10 Penetration Testing & Compliance Readiness

Annual external pentests and quarterly internal red-team exercises.

Prepare for SOC2 readiness with policies, controls, and evidence collection.

9. Phased Roadmap (with security milestones)
MVP (Phase 1) — deliver secure E2E pipeline

Deliver idea → render pipeline with:

RLS & scoped APIs

Signed URL media access

Model-call logging and cost tracking

Basic WAF and rate-limiting

Disaster recovery (DB backups + media snapshots)

Security milestone: Threat model review and security gate before merging MVP to prod.

V1 (Phase 2) — harden and polish

Model capability table, preview improvements, UX polish.

Add prioritized security features:

KMS envelope encryption for high-value assets

Enhanced monitoring & alerting

Admin access controls + approval workflow for dangerous actions

Security milestone: External pen-test and remediation sprint.

V2 (Phase 3) — scale, compliance & enterprise readiness

Templates, collaboration, brand kits.

SOC2 readiness, regional data residency options, and advanced access controls.

Security milestone: SOC2 readiness runbook and compliance artifacts.

10. Risks & Mitigations (expanded)
Risk	Mitigation
Fal.ai rate/behavior or data policy change	Cache model capabilities; separate integration layer; vendor notice processes.
Unintended data exposure	Signed URLs, short TTLs, robust RBAC/RLS, automated scanning for public buckets.
High cost from model abuse	Usage budgets, per-user rate limits, job throttling, cost alerts.
Regulatory non-compliance	Data subject request endpoints, DPA, legal review of 3rd-party T&Cs.
Long-running FFmpeg jobs causing resource drain	Isolate workers in autoscaling pools with job timeouts & quotas.
11. Operational Runbook (summary)

Deployments: CI/CD with pre-deploy security checks, canary deployment, automatic rollback on health checks.

Backups: Nightly DB backups with point-in-time recovery; daily media snapshots for last 7 days and monthly archives for paid users.

DR: RTO target for metadata: 1 hour; RPO: 15 minutes for active projects. Media restores via prioritized restore queue.

On-call: 24/7 rotation with runbooks for render failures, cost spikes, and data leaks.

Cost control: Model-call budgets, usage quotas, per-plan throttles, and alerting thresholds.

12. Future Expansion (secure by default)

Brand kits, templates, and collaboration with tenant isolation and per-tenant encryption keys.

Marketplace: Require vetting, content moderation, and revenue share contract templates.

Enterprise plan (future): offer dedicated tenancy, contractual SLAs, and region-locked storage.

13. Acceptance Criteria (security-focused)

All endpoints require authentication; unauthenticated requests rejected with minimal info.

RLS policies prevent cross-user access; enforced in staging & prod.

Signed URLs used for all media access; no public bucket exposures.

Model calls logged with request hash and cost; billing reconciles within 24 hours.

At least one external pentest before offering Pro-tier credits in production.

14. Quick Security Checklist (50-point summary for launch)

IaC PR reviews + Terraform drift detection

Secrets manager in place + scheduled rotation

TLS enforced, HSTS, CSP configured

WAF & rate limiting enabled

RLS enforced across DB queries

Signed URL validation for media

Model integration layer with input sanitization

Usage logging and cost tracking

Automated backups + test restores

Incident response plan + contacts

Vulnerability scanning + dependency management

Pen test scheduled & remediated issues closed

GDPR / DSAR endpoints implemented

Encryption keys and KMS setup

Admin access controls + audit logs

CI security gate with SAST

Monitoring & SLOs configured

Daily cost alert thresholds

User-facing retention & consent controls

Disaster recovery drills scheduled

(Full checklist exportable into a runbook on request.)

15. Conclusion

This edition of the masterplan preserves the product’s emotional and functional goals while raising the bar on security, privacy, and operational maturity. The recommended path is to ship the MVP with the core security controls in place (RLS, signed URLs, WAF, cost controls), then immediately run a threat-model + penetration test and remediate before scaling user growth.

References & artifacts

Primary spec: /mnt/data/MASTER SYSTEM SPECIFICATION DOCUMENT.docx.

Design philosophy: /mnt/data/design-tips.md.

(The above local paths reference the files you uploaded; I used them as authoritative inputs for this security-hardened masterplan.)
