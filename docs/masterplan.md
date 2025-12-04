## **1\. 30-Second Elevator Pitch**

An AI-powered, continuous-scroll video creation platform that transforms a simple idea into a fully edited, professional-quality video. Users feel powerful and at ease as they generate scripts, scenes, images, clips, captions, music, and voice-over — all without technical skills. Powered by Fal.ai, FFmpeg, Supabase, Vercel workers, Clerk, Stripe, and Backblaze, the platform ensures speed, reliability, and creative freedom at every step.

---

## **2\. Problem & Mission**

### **Problem**

* Creating high-quality, edited videos requires technical expertise, manual workflows, and time-consuming tools.

* Existing AI video generators feel rigid, limited, or overwhelming.

* Users want simplicity without sacrificing creative control.

### **Mission**

Empower anyone — regardless of skill — to produce stunning videos through a smooth, supportive, emotionally intelligent workflow that feels effortless yet powerful.

---

## **3\. Target Audience**

* **Creators (Plus/Pro):** Frequent users wanting speed, quality, and customization.

* **Casual Free Users:** New or occasional creators who accept watermarks/limits.

* **No admin-facing UX initially** (admins exist but not as user roles).

---

## **4\. Core Features**

* Idea → Final video continuous-scroll workspace

* Script generation (Fal.ai OpenRouter)

* Storyboard with editable scenes

* Image generation with full Fal.ai model library

* Video clip generation with per-scene model selection

* FFmpeg transitions (31 presets)

* Music search (Pixabay)

* Voice-over (fal-ai/elevenlabs/tts/turbo-v2.5)

* Auto captioning \+ styles \+ burn-in

* Final FFmpeg assembly pipeline

* Real-time progress updates

* Project dashboard with versions, previews, and artifacts

* Subscription tiers (Free / Plus / Pro) via Stripe

* Authentication via Clerk

* Permanent retention for paid users

---

## **5\. High-Level Tech Stack (and why it fits)**

### **Frontend**

* **Next.js App Router** — fast, ergonomic, ideal for interactive editors.

* **Tailwind \+ shadcn/ui** — consistent visual system and rapid iteration.

* **Supabase Realtime / Pusher** — real-time render status.

### **Backend**

* **Vercel Serverless Functions** — scalable, pay-per-execution, lightweight.

* **Vercel Background Workers** — ideal for FFmpeg rendering workloads.

* **Fal.ai model APIs** — fully managed, flexible image/video/script generation.

* **FFmpeg** — industry-standard video assembly.

### **Storage**

* **Backblaze B2** — affordable, durable storage for media assets.

* **Supabase** — relational DB for metadata, usage logs, and project state.

### **Auth & Billing**

* **Clerk** — frictionless authentication.

* **Stripe** — subscription management and usage-based credit tracking.

---

## **6\. Conceptual Data Model (ERD in words)**

* **Users**

  * plan, clerk\_id, email

* **Projects**

  * belongs to user

  * stores duration, aspect ratio, preset, quality, status

* **Scripts**

  * belongs to project

  * holds versions and model metadata

* **Scenes**

  * belongs to project

  * contains narration, image prompt, animation prompt

* **Images**

  * belong to scene

  * multiple versions with model metadata

* **Video Clips**

  * belong to scene

  * multiple versions with metadata

* **Music Tracks**

  * belong to project (Pixabay references)

* **Captions**

  * SRT text \+ style preset \+ optional burn-in flag

* **Renders**

  * stores final URL \+ status

* **Usage Logs**

  * model, tokens, cost, user\_id

This structure guarantees simple querying and scalable growth.

---

## **7\. UI Design Principles (aligned with Lovable & design-tips.md)**

* **Emotion first:** Users should feel powerful and at ease.

* **Continuous scroll → Zero cognitive jumps:** Each section flows into the next.

* **Calm, confident aesthetic:** generous spacing, warm motion, friendly microcopy.

* **Shadcn/ui components:** intuitive, consistent, readable.

* **Hover states that reassure, not overwhelm.**

* **Kindness built-in:** Undo opportunities, soft error messages, forgiving defaults.

* **Avoid complexity:** Always reduce to what is essential on screen.

* **Motion with intent:** gentle, informative micro-interactions.

---

## **8\. Security & Compliance Notes**

* Clerk handles authentication, password resets, 2FA.

* Stripe manages payment security (PCI-compliant).

* Supabase RLS ensures users only access their own projects.

* Media stored in Backblaze with signed URLs.

* Background jobs isolated from user data.

* Draft auto-deletion policy enforced via cron.

* Logs track model usage for billing transparency.

---

## **9\. Phased Roadmap**

### **MVP (Phase 1\)**

* Idea → Render full pipeline

* Script, scenes, images, clips generation

* Transitions, music, voice-over

* Captions \+ burn-in

* FFmpeg final assembly

* Project dashboard

* Stripe \+ Clerk integration

* Real-time updates via Supabase Realtime

* Minimum UI with continuous scroll

* Admin panel (basic)

### **V1 (Phase 2\)**

* More advanced versioning & history

* Scene-level video model switching UX upgrades

* Improved transitions preview

* Better music trimming and beat-matching

* Enhanced caption style presets

* Robust error recovery states

### **V2 (Phase 3\)**

* Templates for idea → full video styles

* Collaboration features

* Brand kits (fonts, colors, overlays)

* Marketplace for presets

* Multi-language interface

* Social sharing integrations

---

## **10\. Risks & Mitigations**

| Risk | Mitigation |
| ----- | ----- |
| Fal.ai model changes or rate limits | Always consult Fal.ai docs before integration; store model capabilities in DB |
| FFmpeg rendering failures | Use background workers \+ retry queues |
| High storage cost | Auto-delete drafts; compress previews; use 480p intermediate |
| User overwhelm | Stick to emotional clarity, microcopy, and simple defaults |
| Long waits for video generation | Priority queue for Pro; generate previews early |

---

## **11\. Future Expansion Ideas**

* Automated editing “styles” (TikTok, cinematic, vlog).

* Real-time collaborative editing.

* Brand library for agencies.

* Long-form storytelling mode.

* AI adaptive scenes based on selected music.

* Marketplace for creators to sell presets.

---

**End of masterplan.md**

