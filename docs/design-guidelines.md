design-guidelines.md — security-hardened, production-ready

Emotional thesis (unchanged)
Feels like a creative command center — calm, confident, and empowering.
Users create pro-quality videos effortlessly and safely. UI must feel supportive and never expose technical details about data, models, or infra.

1. Emotional Thesis (implementation notes)

Visual: confident accents, generous whitespace, clear hierarchy.

Motion: gentle, purposeful — communicates progress and forgiveness.

Security implication: hide sensitive system details from users; show only friendly, actionable messages. (E.g., “We’re optimizing your clip — hang tight” → no low-level error codes.)

2. Typography System (production-ready)
Philosophy (concise)

Use strong type for control; generous spacing for ease.

All text must be accessible (AA+). Use rem-based sizes for scaling.

Use system-font fallback stack for performance and privacy.

Primary Typeface

Inter (variable font). Fallback: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial.

Typographic tokens (CSS-ready)
:root{
  --type-h1: 2.5rem; /* 40px */
  --type-h2: 2rem;   /* 32px */
  --type-h3: 1.5rem; /* 24px */
  --type-h4: 1.25rem;/* 20px */
  --type-body: 1rem; /* 16px */
  --type-caption: 0.8125rem; /* 13px */
  --line-height-body: 1.55;
}

Hierarchy & rules

Max line length: 65–75 chars for readable blocks.

Headings use font-weight: 600–700. Body 400–500.

Baseline grid of 8px for vertical rhythm.

3. Color System (design tokens + accessibility)
Palette (tokens)
--bg: #F7F8FA;
--surface: #FFFFFF;
--card-border: #E3E6EA;
--text-primary: #1A1F36;
--text-secondary: #4E5566;
--text-tertiary: #9AA1AF;
--accent: #3B82F6;
--accent-hover: #2563EB;
--success: #10B981;
--warning: #F59E0B;
--error: #EF4444;
--info: #0EA5E9;

Accessibility rules

Contrast: body text ≥ 4.5:1 vs background. Headings ≥ 3:1. Enforce via CI color-contrast checks.

Provide alt palettes for color-blind-safe variants and test color maps with tools.

Dark mode: toggle and persist preference; use token overrides rather than hard-coded colors.

4. Spacing & Layout System (practical)

8px grid tokens: 4px base, then 8 / 16 / 24 / 32 / 48 / 64.

Container widths and responsive breakpoints:

mobile <640px — stacked, full-width cards

tablet 640–1024px — 2-column where useful

desktop >1024px — centered main column (820–1040px) + sticky right actions

Cards: border-radius 10px, shadow 0 2px 6px rgba(0,0,0,0.06).

5. Buttons & Interactive Components (secure & accessible)
Tokens & CSS
--btn-radius: 8px;
--btn-padding: 12px 16px;
--btn-primary-bg: var(--accent);
--btn-primary-hover: var(--accent-hover);
--focus-ring: 0 0 0 3px rgba(59,130,246,0.18);

Accessibility & behavior

All interactive elements must have keyboard focus and visible focus ring.

Prefer semantic <button> elements; do not rely on click-only handlers.

Disable buttons during long-running operations and provide progress feedback.

Confirm destructive actions with a clear modal (undo available where possible).

6. Motion & Interaction Principles (safe & performant)

Durations: 150–250ms for most transitions; hover 90–120ms; modal 200–300ms.

Respect prefers-reduced-motion—provide alternative non-animated feedback (progress bar).

Motion should indicate cause → effect (e.g., “generating clip” → progress).

For long jobs, show incremental progress + ETA when possible; never show raw system logs.

7. Component Library (detailed & implementation-safe)

Provide each component as a single source of truth (design token + small usage examples). Components must include ARIA attributes and keyboard behavior.

Scene Card (spec)

Structure: header (H4) → narration (editable) → thumbnail (16:9) → actions row.

States: default / hover / selected / loading / error.

Loading state: skeleton + accessible label like aria-busy="true" aria-live="polite".

Image Carousel

Thumbnails 120×120, rounded 8px.

Use role="listbox" + aria-activedescendant for selection.

Lazy-load images; load priority for selected/nearby images only.

Clip Card

Preview: muted loop with playsinline and preload="metadata".

Provide fallback poster image for devices that block autoplay.

Store preview as low-res 480p to reduce bandwidth.

Transition Selector

Visual tiles with 1s preview loop. Cache previews in CDN with TTL to avoid re-renders.

Music Browser & Playback

Stream-only by default. If saving is allowed, require explicit consent and store license metadata.

Display license and attribution clearly beside each track.

Caption Editor

Split view: timecodes (left) + text editor (right).

Provide keyboard shortcuts for navigation and editing.

Export SRT easily; support accessibility for screen readers.

8. Voice & Tone (microcopy + privacy)

Microcopy: empowering, short, and actionable. Avoid system jargon (no error stack traces).

Privacy copy: when user generates voice using a sample or an uploaded voice, show a consent modal describing retention and usage. Provide explicit opt-in and “delete voice model” controls.

Example:

“We’ll generate a voice track from your script. By creating a custom voice, you consent to storing and using this voice for this project. You can delete it anytime under Project → Settings.”

9. System Consistency & Developer Handoff

Use shadcn/ui tokens and component library. Export Figma or tokens JSON for engineering.

Maintain a living style guide that includes all tokens and component usage examples.

Version components with changelogs; deprecate gracefully.

10. Accessibility Guidelines (strict)

Keyboard-first navigation; tab order documented.

ARIA roles and states: dialogs, tooltips, sliders, listboxes.

Focus management on modal open/close.

Screen reader friendly labels for media controls.

Captions must be toggleable and readable at small sizes (WCAG AA).

Conduct accessibility audits (axe, manual) before each major release.

11. Privacy, Data & Security Design Notes (NEW — required)

Design decisions must embed privacy and security controls:

Media handling

All media served via short-lived signed URLs; never expose permanent public links in the UI.

Do not render raw storage URLs client-side — always request signed URLs from backend.

Apply bandwidth-friendly previews (480p) and lazy load full-res only after explicit user action.

Model prompts & PII

Sanitize and truncate long prompts client-side; strip any HTML or control characters.

Provide a PII-detection step on the backend before sending prompts to Fal.ai and flag redaction opportunities.

If PII is detected, prompt user to redact or opt into secure processing (explain retention and access controls).

Consent & likeness

When TTS or image generation uses a real person’s voice/image, require explicit consent and check legal footage (terms & conditions). Store consent record in audit_logs.

Retention & deletion

Default drafts auto-delete after 15 days (Free) unless user explicitly changes retention.

Provide clear UI for "Delete project" with 48-hour quarantine and a recorded delete action in audit_logs.

Allow users to request data export (projects, captions, scripts) and deletion per GDPR/CCPA.

Logging & telemetry

Mask PII in application logs. Do not log prompt text verbatim unless user has opted into sharing prompt history for improvements; if stored, encrypt at rest and record consent.

Usage logs should store request hash (non-reversible) for audit without storing raw prompt text.

Legal & licensing UX

Show track license details from Pixabay and require acceptance where applicable.

Provide a “Model usage” section explaining which models were used (friendly, non-technical) and the retained artifacts.

12. Emotional Audit Checklist (operationalized)

Before release, run this checklist and capture results in release notes:

Does the main task complete in ≤ 3 obvious steps?

Are all long-running operations providing progressive feedback?

Does the UI avoid technical error exposure?

Have PII & consent checks been performed for TTS / likeness / uploads?

Are signed URLs and RLS validated in staging?

13. Technical QA Checklist (concrete)

Export design tokens and validate them in a token test harness.

Color contrast tests automated in CI.

Prefers-reduced-motion mode respected.

All components have keyboard + screen reader tests.

Signed URL access tests and media TTL tests.

Penetration test items for UI surface: XSS, CSRF, clickjacking mitigations.

14. Design Snapshot (ready-to-use tokens)

Colors

--bg: #F7F8FA
--surface: #FFFFFF
--card-border: #E3E6EA
--text-primary: #1A1F36
--accent: #3B82F6
--accent-hover: #2563EB


Type
H1 40px / 700
H2 32px / 600
H3 24px / 600
H4 20px / 500
Body 16px / 400–500
Caption 13px / 400

Spacing
8px grid; card padding 24px; section padding 32–40px.

15. Handoff & Versioning

Publish a versioned tokens JSON + component catalog to the repo (/design/system/tokens.json) and link Figma library.

When design tokens change, increment semantic version, run visual regression tests, and update changelog.

16. Design Integrity Review (concrete improvement)

Microinteraction suggestion: during clip generation, show a heartbeat micro-animation with incremental steps (Script → Scenes → Clips) plus a visible estimated time. Provide “notify me” toggle and optional email/push notification so users don’t need to keep the app open.

17. Next steps (practical deliverables I can provide)

Export tokens JSON and a simple CSS variables file.

Produce a Figma-ready component sheet (SVGs & tokens).

Create an accessibility test checklist and Lighthouse preset for CI.

Create a short UI copy deck (onboarding, errors, success) with localized variants.

Files I referenced (local paths)

Design prompt & tips: /mnt/data/design-tips.md.

Master system spec: /mnt/data/MASTER SYSTEM SPECIFICATION DOCUMENT.docx.
