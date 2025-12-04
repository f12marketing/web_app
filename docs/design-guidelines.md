design-guidelines.md
1. Emotional Thesis
Feels like a creative command center — calm, confident, and empowering.
 A space where anyone can create professional videos effortlessly, guided by warm motion, clear hierarchy, and interfaces that feel supportive rather than demanding.

2. Typography System
Philosophy
Strong, confident type conveys power.


Generous spacing and soft contrast convey ease.


Modular scale ensures predictability and visual rhythm.


≥1.5× line-height everywhere for readability.


Primary Typeface
Inter (geometric sans)
 Reason: clean, modern, and neutral — ideal for clarity and control.
Typographic Hierarchy
Style
Size
Weight
Line Height
Usage
H1
40px
700
1.3
Section headers (“Script”, “Scenes”)
H2
32px
600
1.35
Sub-sections
H3
24px
600
1.4
Labeled blocks (“Voice-over”, “Captions”)
H4
20px
500
1.45
Card titles, panel headings
Body
16px
400–500
1.55
Standard text everywhere
Caption
13px
400
1.6
Timestamps, metadata, tooltips

Additional Notes
Avoid long paragraphs; break with spacing.


Maintain consistent vertical rhythm: baseline grid of 8px.


Always ensure AA+ contrast.



3. Color System
Color Philosophy
Colors should feel empowering, modern, and calm — not loud.


Muted neutrals + a confident accent color.


Strict contrast rules to support ease of use.


Palette
Primary Neutral Base
Background: #F7F8FA


Surface: #FFFFFF


Card Border: #E3E6EA


Soft Shadow: rgba(0, 0, 0, 0.06)


Text Colors
Primary text: #1A1F36


Secondary text: #4E5566


Tertiary text: #9AA1AF


Accent Color (Empowerment Blue)
Primary accent: #3B82F6


Primary accent (hover): #2563EB


Signifies control, clarity, action.


Support Colors
Success: #10B981


Warning: #F59E0B


Error: #EF4444


Info: #0EA5E9


Dark Mode Palette (future optional)
Background: #0D0F12


Surface: #1A1D21


Primary text: #E6E8EB


Card border: #2A2E34


Light/Dark Contrast
Always maintain WCAG AA+:
Body text contrast ≥ 4.5:1


Large headings ≥ 3:1



4. Spacing & Layout System
8pt Grid
All spacing increments follow 8px, 16px, 24px, 32px, 48px, 64px.
Page Structure
Continuous Scroll Layout (per master spec):
 Idea → Script → Scenes → Images → Clips → Transitions → Music → Voice-over → Captions → Preview → Final Render


Section containers:


Padding: 32px top, 40px bottom


Width: 820–1040px (centered)


Cards & Panels


Padding: 24px


Border radius: 10px


Shadow: subtle (0, 2px, 6px, 6% opacity)


Breakpoints
Mobile: stacked layout, 100% width cards


Tablet: 2-column grids where relevant


Desktop: fixed center column + sidebar for actions when needed



5. Buttons & Interactive Components
Buttons
Primary Button
Background: #3B82F6


Hover: #2563EB


Text: white


Radius: 8px


Padding: 12px 16px


Motion: 120ms ease-out subtle lift (translateY -1px)


Secondary Button
Border: 1px solid #E3E6EA


Background: white


Hover: light gray surface


Text: #1A1F36


Destructive Button
Background: #EF4444


Hover: #DC2626


Dropdowns
Border: #E3E6EA


Surface: white


Hover item: #F3F4F6


Transition: fade + 4px downward slide


Arrow: soft rotation on expand (90ms ease)


Inputs / Textareas
Border: #E3E6EA default → #3B82F6 focus


Focus ring: 2px inner glow, #BFDBFE


Padding: 12px


Radius: 8px


Placeholder: #9AA1AF



6. Motion & Interaction Principles
Based on “Kindness in Design” from design-tips.md.
Duration
UI motion: 150–250ms


Hover effects: 90–120ms


Modal transitions: 200–300ms, spring easing


Tone
Confident, smooth, never flashy


Animations act as guides, not decorations


Always reinforce clarity, never distract


Microinteractions
Hover lifts: 1–2px upward shift, low opacity shadow


Button hover: subtle scaling (1.02–1.04)


Section load: soft fade + upward 8px slide


Object creation (image/clip): “materialize” fade-in from 90% scale


Deletion: collapses height smoothly → fade-out


Empty States
Warm, encouraging copy


Use illustrations or soft icons


Example:


 “Start with an idea — you’ll be amazed how far we can take it together.”




7. Component Library
Scene Card
Header: H4


Body: narration text + image prompt


Thumbnail: 16:9 rounded


Actions: regenerate, replace, edit


Hover: border highlight using accent color


Image Carousel
Thumbnails: 120×120, radius 8px


Selected state: 2px accent border


Scroll motion: natural, physics-based


Clip Card
Preview: muted auto-play loop


Metadata: duration, model used


Refresh icon: rotates 90° on hover


Transition Selector
31 presets (per spec)


Hover: play 1s loop


Selected: blue outline


Music Browser
Horizontal cards with waveform preview


Stream-only playback


CTA: “Use Track”


Caption Editor
Left side: timecode list


Right side: editable text


Style presets laid out visually (Modern, Bold, Cinematic, Minimal)



8. Voice & Tone (Microcopy Guidelines)
Keywords
Empowering · Friendly · Clear · Calm · Encouraging
Do
Celebrate progress (“Scene ready!”)


Use active voice


Keep messages short


Offer recovery paths


Don’t
Blame the user


Use technical jargon unless necessary


Over-explain


Examples
Onboarding:
“Tell us your idea. We’ll take it from here.”
Success message:
“Your clip is ready — looking great.”
Error message:
“Something hiccupped. Let’s try that again together.”

9. System Consistency
Components follow shadcn/ui architecture.


All paddings and radii based on 8pt grid.


All headings use Inter semibold/medium.


All interactive items have hover, focus, and active states.


All modals behave the same (fade + slide).


All scenes/images/clips use the same card structure for recognition.



10. Accessibility Guidelines
Full keyboard navigation (tab order, space/enter activation).


ARIA roles for dialog, listbox, menus, sliders.


Focus ring always visible and never removed.


Captions editor supports screen readers.


Color contrast AA+ minimum.


Avoid motion-triggered discomfort:


Reduce motion mode if OS prefers-reduced-motion is active.



11. Emotional Audit Checklist
Use this before shipping any screen or feature:
Does the interface make the user feel empowered?


Is everything clear at a glance?


Is the UI uncluttered, with generous spacing?


Does motion feel supportive and purposeful?


Does microcopy feel encouraging, not mechanical?


Does the visual hierarchy reduce anxiety?


If the user is tired or overwhelmed, would this still feel approachable?



12. Technical QA Checklist
Typography sizes align to 8pt rhythm.


All contrast ratios meet AA+.


Buttons have distinct hover, focus, active states.


Motion durations stay within 150–300ms.


Dropdowns/menu components follow consistent patterns.


Modals and overlay layers use correct z-index structure.


Captions remain readable at all aspect ratios.



13. Adaptive System Memory
If future products are created:
Reuse accent color + typographic scale for brand consistency.


Preserve interaction tone (calm, empowering).


Maintain the same spacing system.



14. Design Snapshot
Color Palette (Hex Codes)
Background: #F7F8FA
Surface: #FFFFFF
Card Border: #E3E6EA
Primary Text: #1A1F36
Secondary Text: #4E5566
Tertiary Text: #9AA1AF
Accent Blue: #3B82F6
Accent Blue Hover: #2563EB
Success: #10B981
Warning: #F59E0B
Error: #EF4444
Info: #0EA5E9

Typographic Scale
Type
Size
Weight
H1
40px
700
H2
32px
600
H3
24px
600
H4
20px
500
Body
16px
400–500
Caption
13px
400

Spacing System
8pt grid


Section padding: 32–40px


Card padding: 24px


Button padding: 12×16px


Emotional Thesis
Feels like a creative command center — calm, confident, empowering.

Design Integrity Review
The system successfully blends empowerment (bold accents, confident type) with ease (soft surfaces, gentle motion, clear hierarchy). The emotional tone and technical rules align well.
 One area to enhance:
 Add subtle “reaction” microinteractions during long waits (e.g., generating video clips) to reassure users and reduce perceived effort.

End of design-guidelines.md

