# UI/UX & Information Design — Checklist

Actionable **Do / Don't** plus **Test-for** criteria for HUD classification, information hierarchy, feedback design, menu flow, and accessibility. Run against a HUD spec review, a feedback vocabulary audit, or a batch of generated content. Test-for items are written to be enforced as automated validators in a headless generation loop. See `GUIDE.md` for reasoning and sources.

> **Order matters:** classify → tier → place → feedback vocabulary → menu flow → accessibility audit. Never commit to diegetic UI before doing a Tier assignment pass.

---

## A · HUD type classification (Fagerholt–Lorentzon)

**Do**
- [ ] **Classify every HUD element** on both Fagerholt axes before placing it: Fiction (can characters acknowledge it?) and Geometry (does it live in 3D space?).
- [ ] **Default to non-diegetic** for fast-changing, complex, or high-volume data (ammo count, cooldown timers, quest tracker, raid UI).
- [ ] **Reserve diegetic** for elements where the in-world presentation is more dramatically fitting than an overlay — and budget 2–3x the iteration time.
- [ ] **Use spatial UI** for world-grounded signposting where context-switching to a non-diegetic layer would break flow (prompts above objects, team outlines through walls).
- [ ] **Use meta UI** (screen vignettes, damage flash) for character subjective state; wire every meta effect to a player opt-out setting.

**Don't**
- [ ] Don't commit to pure-diegetic UI for information that changes faster than in-world animation can track.
- [ ] Don't use spatial UI in photorealistic environments for information the fiction cannot explain — it's worse than an overlay (narrative incoherence + visual noise).
- [ ] Don't ship a meta effect (full-screen flash, screen shake) without routing it through an accessibility toggle.

**Test for** — Does every HUD element have an explicit classification in the design doc? Remove the element from its current position and add a non-diegetic overlay: is the information *worse* to convey that way, or just aesthetically different? A diegetic element that isn't *more fitting* is diegetic theater.

---

## B · Information hierarchy & cognitive load

**Do**
- [ ] **Assign a tier to every state variable** before placing it: Tier 1 (death-relevant, per-second) → permanent HUD corners; Tier 2 (per-encounter) → context-triggered; Tier 3 (per-session) → menus only.
- [ ] **Apply progressive disclosure** — surface a mechanic's UI only when the player first encounters that mechanic in play.
- [ ] **Test every icon** with ≥43 representative participants: ≥80% must correctly identify its function without a label; redesign (not label) icons that fail.
- [ ] **Use visual weight** (size, contrast, color intensity, animation) to encode priority — primary state must dominate at a glance; secondary state must recede.

**Don't**
- [ ] Don't put Tier-3 data (stats, extended inventory, crafting trees) on the permanent HUD.
- [ ] Don't explain mechanics via static instruction screens before players touch the controls — route that content into play-time discovery.
- [ ] Don't rely on labels as a substitute for icon clarity — labels are a fallback for a failed icon.
- [ ] Don't style secondary and primary HUD elements at equal contrast and size.

**Test for** — Can a playtester report their current health, ammo, and objective *without pausing*, while navigating a difficult encounter? At 50% screen brightness in active combat, can they identify health status within 0.5 seconds? Does a new player spend any time reading tooltip walls in the first 5 minutes?

---

## C · Feedback & game-state communication

**Do**
- [ ] Give **every state-changing player action** at minimum one immediate visual and one audio response, synchronized within 15ms.
- [ ] Scale **screen shake and hit-stop** proportionally to impact magnitude — major impacts get full shake; minor hits get flash only; reserve max-shake for rare climactic events.
- [ ] **Signpost affordances environmentally first** — glowing edges, worn paths, contrast-highlighted interactables — before falling back to spatial UI prompts or text.
- [ ] Give **success and failure distinct signal vocabularies** across visual, audio, and haptic channels; never use the same animation or chime for both outcomes.
- [ ] Restrict **red** to critical-only signals (damage, low health, failure); use orange for warnings; never use red for neutral or ambient information.

**Don't**
- [ ] Don't ship any player action (melee, projectile, ability) that produces no audio, no visual, and no camera response on connect.
- [ ] Don't apply camera shake to every bullet impact, footstep, and jump — the signal degrades to noise and players disable it as a QoL measure.
- [ ] Don't open menus or instruction screens as the primary teaching mechanism for a mechanic a player can learn by encountering it.
- [ ] Don't use a single chime for level completion, item pickup, achievement, and errors.

**Test for** — Play with display off: can you identify hit, damage received, and kill from audio alone? Eyes closed: can you distinguish success from failure from audio alone? Do playtesters report combat "feels good to play" or "feels like hitting air"? In a procedurally generated level, does every mechanic the player must execute have at least one guaranteed environmental signifier injected by the generation rules?

---

## D · Menu & navigation flow

**Do**
- [ ] Keep **every primary action** (anything taken >1×/min) reachable in ≤2 inputs from any game state, including from within submenus.
- [ ] Design menus for the **primary input modality first** (controller / KBM / touch), then explicitly port and test each secondary modality.
- [ ] Implement **visible focus state** (highlight, scale, glow) on every interactive widget so the currently selected element is always unambiguous.
- [ ] Ship **settings infrastructure** (graphics, audio, controls, accessibility) accessible from the main menu before first gameplay and from the pause menu — mid-production, not as a polish pass.
- [ ] **Match platform/genre navigation conventions** (B/Circle = back, A/Cross = confirm, Escape = close); justify any deviation with a measurable interaction improvement.
- [ ] **Place primary touch actions** in the bottom 60–70% of screen (comfortable thumb reach); destructive or secondary actions in stretch zones.
- [ ] Set **minimum touch targets** at ≥9.2mm physical / 44px at 96dpi.

**Don't**
- [ ] Don't require ≥3 inputs to reach any primary action.
- [ ] Don't ship a menu without testing D-pad-only navigation end-to-end on every supported platform.
- [ ] Don't launch without a visible focus state on every navigable widget.
- [ ] Don't treat settings as a polish-phase deliverable — broken settings ship broken.
- [ ] Don't invent a novel navigation paradigm without a measured justification (novel paradigms are roadblocks until explicitly taught).

**Test for** — Map every primary action to its input path from the default game state: any ≥3-input path is a red flag. Navigate the entire menu D-pad only — can you reach every option? Can you reach settings before any gameplay state? Navigate the entire menu **eyes closed**: can you tell what's highlighted, when a selection confirms, and when you go back?

---

## E · Accessibility

**Do**
- [ ] **Add a redundant shape, icon, or text label** to every color-encoded signal (ally/enemy dots, health state, objective type, danger zones).
- [ ] Provide a **colorblind mode** covering at minimum deuteranopia, protanopia, and tritanopia — each with adjustable intensity.
- [ ] **Maintain ≥4.5:1 contrast** for body text and ≥3:1 for large text/UI components against background — measured against the brightest *and* darkest scenes, not average scenes.
- [ ] Provide a **text size slider** with at least three steps (default, large, largest).
- [ ] Ship **full button remapping** for every input modality, with per-action hold/toggle customization and a "restore defaults" path.
- [ ] **Toggle every screen flash and camera shake** independently through settings; validate that each toggle covers all instances including hardcoded effects.
- [ ] Test subtitle readability at the game's minimum intended viewing distance on the smallest target display.

**Don't**
- [ ] Don't ship any game state that is communicated by color alone.
- [ ] Don't allow settings to reset on session start (persist all accessibility settings).
- [ ] Don't allow two remapped inputs to silently conflict.
- [ ] Don't design accessibility options as a separate "Accessibility" submenu segregated from core settings.
- [ ] Don't ship any flash effect without verifying it stays below 3Hz over large screen areas.

**Test for** — Simulate deuteranopia using a filter (iOS accessibility or browser extension): does all game state remain legible? Can a single-handed player (right hand only) configure a playable control scheme? Does disabling screen shake in settings disable *every* instance, including boss abilities? Run a WCAG contrast check on all UI text against the brightest scene in the game.

---

## Headless guardrails (author once, enforce always)

Hand-authored contracts the generator must respect — author before generating, validate every batch, **block commit** on failure:

- [ ] **HUD spec contract** (state variable → tier → UI type, authored from the mechanic inventory) → **tier-validator**: reject any generated content that requires a Tier-1 variable to be off-screen or menu-only.
- [ ] **Feedback vocabulary table** (action × magnitude → minimum channel bundle: visual + audio within 15ms) → **feedback-completeness linter**: reject any generated mechanic or room with no signifier for an action the player must execute.
- [ ] **Signifier budget rule** (every mechanic the player must execute in a generated room has ≥1 guaranteed environmental signifier) → **signifier-coverage validator** run at generation time.
- [ ] **Color palette safe set** (pre-validated for ≥4.5:1 body contrast in all colorblind modes) → **palette validator**: clamp all generated colors to the safe set.
- [ ] **Flash rate limiter** at the renderer level: rate-limit screen-space flash events to <3Hz independent of generated content.
- [ ] **Audio peak limiter**: normalize all generated audio against a peak limiter; no generated audio spike above the platform ceiling.
- [ ] **Menu content template** (reflowable containers tested at maximum generated label length; "blank/unknown" icon state visually distinct from "empty") → validate at generation time with worst-case string lengths.

> For the *feel* of UI elements (button squish, easing curves, transition timing) — apply `game-feel-and-juice`, Sub-domain F and C.7. Gate every generated batch through `procgen-review` as well.
