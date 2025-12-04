app-flow-pages-and-roles.md — secure, production-ready edition
1. Site Map (Top-Level Pages Only)
Public

Landing Page
Secure, static-first page with no authenticated calls. Introduces the product, pricing, and uses a safe CTA pathway.

Auth Pages (Clerk)

Login

Signup

Password reset

Email verification
All sensitive authentication handled exclusively by Clerk. No password forms or session cookies handled by your servers.

Authenticated

Dashboard
Project list, thumbnails, statuses.
Security: all queries RLS-scoped, no client-side filtering.

Project Workspace (Continuous Scroll)
Main creation environment.
Security:

All media loaded through short-lived signed URLs only

Each section fetches only project-scoped data

No public or guessable file paths

Includes the full pipeline:
Idea → Script → Scenes → Images → Clips → Transitions → Music → Voice-over → Captions → Preview → Final Render

Billing & Account Settings
Stripe-hosted billing; no card data hits your backend.
Security:

Webhooks signed and verified

Plan gating validated server-side

Retention settings require explicit user consent

Downloads / Final Files Page
Shows only signed URLs with 60–300s TTL.
No directory browsing, no persistent public links.

Admin Panel (Internal-only)
Exists for ops teams.
Hidden from UI, protected by SSO group membership + short-lived session tokens. Users do not reach this interface.

2. Purpose of Each Page (One Line Each)

Landing Page — Teach value quickly and drive safe, frictionless signup.

Login / Signup — Provide secure access via Clerk (passwordless optional).

Dashboard — Manage all projects with strict isolation.

Project Workspace — Edit every stage of the pipeline with full auditability.

Billing & Account Settings — Safely manage subscriptions, receipts, and retention.

Downloads Page — Provide secure, expiring access to final renders.

3. User Roles & Access Levels
User Roles

There is only one role: User.
No customer-facing admin privileges.

Plan tiers define access, not permissions.

Plan-Based Capabilities (from master spec)
Capability	Free	Plus	Pro
Watermark	Yes	Optional	No
Models	Cheapest only	Mid-tier	Full
Clip Queue Priority	Low	Medium	High
Permanent Retention	No	Yes	Yes
Draft Retention	15 days	Permanent	Permanent
Final Video Quality	720p	720p–1080p	1080p
Security Enforcement

All plan rules enforced server-side — never rely solely on UI gating.

All Supabase queries scoped via RLS policies bound to auth.uid.

Admin panel access requires SSO + IP allowlist (optional).

No ability for users to change plan restrictions or spoof model levels.

4. Primary User Journeys (Best-practice version)
A. Creating a New Video

Dashboard → Create Project (server-side validation)

Enter idea → script & scenes generated securely (sanitized text → Fal.ai)

Move down the pipeline: images → clips → transitions → music → VO → captions → render

Security notes:

All model inputs sanitized before API call.

All assets created under project-scoped storage paths.

B. Editing a Specific Stage

Scroll to stage

Edit inputs

Regenerate output

Security notes:

Every regenerate event creates a new version, stored immutably.

Version rollback recorded in audit logs.

C. Rendering the Final Video

Scroll to Final Render

“Render Video”

Background worker processes job → signed URL delivered

Security notes:

Jobs use idempotency tokens

Worker cannot access projects it doesn’t own

Render artifacts stored encrypted

D. Managing Subscription

Account Settings

Upgrade → Stripe checkout

Updated plan applied server-side

Security notes:

Stripe webhook verification mandatory

Billing state changes logged in audit logs

E. Returning to an Existing Project

Dashboard → project

Resume from last position

Edit or render

Security notes:

If a user changes upstream sections, downstream data is invalidated with a confirmation modal.

5. Detailed User Flow (Security-enhanced)
1. Landing → Signup

Landing page loads no user data, no cookies beyond analytics (strict, anonymous).

Signup via Clerk → redirect to Dashboard.

2. Dashboard

Fetch only the authenticated user’s projects (RLS enforced).

Each project tile uses signed thumbnail URLs to prevent URL leakage.

3. Workspace Sections

(the continuous-scroll workflow remains unchanged, but with security implications added)

Idea Section

Input sanitized before saving.

Settings restricted to allowed enums.

CTA “Generate Script” calls backend to generate securely.

Script

Uses Fal.ai through a secure integration layer.

Script stored in project-scoped DB tables.

Regeneration increments version IDs.

Scenes

Scenes stored with redaction flags (if PII detected).

Edits autosaved with safe HTML stripping.

Images

User selects model; model access enforced server-side.

Generated images stored via backend-upload → Backblaze.

Signed URLs generated when requested, not stored persistently.

Clips

Clips generated via Fal.ai or FFmpeg fallback.

Workers run in sandboxed environments.

Metadata (duration, model, checksum) stored for auditability.

Transitions

Static list of 31 templates (safe, deterministic).

No remote code execution or dynamic templating.

Hover previews served via cached small assets.

Music

Pixabay queries proxied through backend → protects API key.

Stream-only unless explicitly saved.

License metadata stored with track association.

Voice-over

Uses ElevenLabs via Fal.ai wrapper.

Requires clear consent whenever real user voice is cloned or uploaded.

PII stripping applied before TTS call unless user opts in.

Captions

Auto-generated → editable.

SRT format validated before save (reject malicious payloads).

Burn-in via FFmpeg inside isolated worker.

Preview

Generate 480p preview with strictly limited runtime.

Preview cached for 24 hours to reduce worker load.

Final Render

Background worker pulls job → fetches assets via scoped signed URLs.

Produces final MP4 → uploaded via server-side only.

Expiring signed download URL sent to user.

4. Return Flow (Security Notes)

Downstream invalidation prompts:
“This will regenerate clips/images. Continue?”

All old versions kept in immutable version history.

6. Interaction Patterns (Security & UX)
Navigation

Vertical-only scroll reduces complexity.

Sticky header contains non-sensitive navigation shortcuts.

Versioning

All versions immutable; each has version_id, created_at, created_by.

Reverting creates a new version rather than overwriting.

Saving

Autosave uses debounced server-side updates.

No client-side-only drafts.

Error Handling

Never expose model errors directly.

Show user-friendly messages:

“Something paused — let’s retry together.”

Model Constraints

UI disables unauthorized models; back-end enforces final say.

Tooltips explain upgrade requirements.

7. Data Access Rules (Security-critical)
Database

All Supabase queries constrained by:

RLS with auth.uid()

Parameterized queries

No client-provided IDs without server verification

Media

All media URLs are:

Signed (5–10 min TTL)

Single-use preferred for sensitive data

Generated per-request (never stored long-term)

Workers

Worker jobs include:

User ID

Project ID

Idempotency token

Limited-scoped credentials

Workers can never modify or even reference another user’s project.

Realtime

Supabase Realtime or Pusher channels scoped to project_id + user_id.

All subscriptions verified by server-generated access tokens.

End of app-flow-pages-and-roles.md

Security-Hardened • UX-Optimized • Production-Ready
