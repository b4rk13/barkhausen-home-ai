# Barkhausen Home AI — Roadmap

*Mind-map style. Phases are loose — items can move based on priority.*

---

## 🗺️ Mind Map

```
Barkhausen Home AI
│
├── 👤 IDENTITY & PROFILES
│   ├── David profile (default — hermesagent LXC)
│   ├── Bridget profile (separate memory, same Telegram gateway)
│   ├── Shared household context (sync skill)
│   └── Care Bot stays separate (treatment-only)
│
├── 🏠 SMART HOME CONTROL
│   ├── Already working: HA lights, switches, climate, UniFi
│   ├── Already working: Protect cameras
│   ├── Natural language device control via Telegram
│   ├── Scenes & routines ("goodnight", "movie time", etc.)
│   └── Multi-room awareness (who's where → relevant context)
│
├── 🗣️ VOICE INTERFACE
│   ├── Phase 1: Telegram voice notes (already works — pull-based)
│   ├── Phase 2: HA Assist as wake-word frontend
│   │   ├── Wake word detection (microWakeWord or openWakeWord)
│   │   ├── ESPHome voice devices (cheap always-on hardware)
│   │   └── Hermes as conversation backend behind HA Assist
│   ├── Phase 3: TTS responses through room speakers
│   │   └── HA media players as output targets
│   └── Phase 4: Ambient always-on in key rooms (kitchen, living room)
│
├── 📅 CALENDAR & SCHEDULING
│   ├── Google Calendar integration (personal family calendar)
│   ├── "What's on this week?" queries
│   ├── Kids' activities and school events
│   ├── Reminders and proactive nudges
│   └── David+Bridget shared view (NOT treatment calendar — that's Care Bot)
│
├── 🍳 RECIPES & HOUSEHOLD
│   ├── Recipe suggestions (dietary prefs, pantry-aware)
│   ├── Meal planning
│   ├── Shopping list management (HA todo or Notion)
│   └── Home maintenance reminders
│
├── 🎵 MUSIC & MEDIA
│   ├── Spotify already integrated (skill exists)
│   ├── Room-targeted playback via HA media players
│   ├── "Play something for dinner" natural language
│   └── Bridget-specific playlists / preferences
│
├── 👨‍👩‍👧‍👦 FAMILY CONTEXT
│   ├── Kids' names, ages, schools, schedules
│   ├── Family preferences, routines
│   ├── Shared household memory (not tied to either profile)
│   └── Guest/visitor awareness
│
├── 🔔 PROACTIVE NOTIFICATIONS
│   ├── Morning briefing (weather, calendar, tasks)
│   ├── Package delivery alerts (camera-triggered)
│   ├── Household anomalies (door left open, etc.)
│   └── Gentle nudges for recurring tasks
│
└── 🔧 INFRASTRUCTURE
    ├── Hermes profiles (David / Bridget)
    ├── Shared household context sync skill
    ├── Telegram gateway multi-user routing
    ├── HA Assist ↔ Hermes pipeline
    ├── ESPHome voice hardware
    └── TTS voice selection (per-person)
```

---

## 📋 Phases

### Phase 0 — Foundation (now)
- [x] Hermes running on hermesagent LXC
- [x] Telegram gateway (David)
- [x] Home Assistant integration (lights, switches, climate, cameras)
- [x] Spotify skill
- [x] Google Workspace skill
- [ ] Create this repo and roadmap

### Phase 1 — Multi-Profile & Shared Context
*Goal: Bridget can chat with Hermes via Telegram with her own memory, but household facts are shared.*

- [ ] Create `bridget` Hermes profile
- [ ] Wire Bridget's Telegram account to her profile in the gateway
- [ ] Define shared household context file structure
- [ ] Build `household-sync` skill (reads/writes shared context both profiles see)
- [ ] Seed household SOUL.md / shared memory with family facts
- [ ] Decide: shared SOUL.md or per-profile with household include?

### Phase 2 — Family Calendar & Household Tasks
*Goal: "What's on this week?" works for either of them.*

> **Architecture note (needs design decision before implementation):**
> Currently David and Bridget have separate Google Calendars. Skylight Calendar displays both + kids-related events added directly to Skylight. This works visually but is fragmented for AI use.
> Proposed direction: create a shared Family calendar in Google, use Skylight's per-person profile feature to show whose events are whose. Need to decide: migrate existing calendars, or layer the family calendar on top? Spec this out in Phase 2 Kiro process.

- [ ] Design family calendar architecture (shared Google Calendar vs. per-person + family overlay)
- [ ] Connect family Google Calendar to Hermes (separate from Care Bot's treatment calendar)
- [ ] Per-profile calendar access (David sees his + family; Bridget sees hers + family)
- [ ] Shopping list / todo via HA or Notion
- [ ] Morning briefing cron job (per-profile delivery)
- [ ] Kids' activities context in shared memory

### Phase 3 — Voice (Pull-Based, Low Friction)
*Goal: Voice notes in Telegram work well end-to-end.*

- [ ] Confirm STT pipeline is solid (faster-whisper or Groq)
- [ ] TTS responses back to Telegram (Edge TTS, per-profile voice)
- [ ] Test Bridget's voice note → response round-trip
- [ ] Voice-activate smart home commands via Telegram voice

### Phase 4 — Voice (Ambient / Wake Word)
*Goal: "Hey Hermes" in the kitchen works.*

- [ ] Evaluate HA Assist pipeline as frontend
- [ ] Test openWakeWord / microWakeWord
- [ ] ESPHome voice device — prototype one room (kitchen?)
- [ ] Wire HA Assist conversation backend → Hermes API
- [ ] TTS back through HA media player / room speaker
- [ ] Expand to living room, bedroom

### Phase 5 — Recipes, Music, Proactive

> **Prerequisite:** Add `SKYLIGHT_EMAIL` and `SKYLIGHT_PASSWORD` to `~/.hermes/.env` on LXC before starting Skylight skill work.

- [ ] Port Skylight Calendar skills from Claude Cowork (Playwright-based: add recipe, add to meal plan, add groceries to shopping list — no Skylight API exists)
- [ ] Room-targeted Spotify playback
- [ ] Proactive morning briefing with weather + calendar
- [ ] Camera-triggered delivery notifications

### Phase 6 — Infrastructure Hardening
- [ ] 1Password CLI (`op`) on LXC — service account setup, migrate credentials out of `.env`
- [ ] Evaluate additional integrations needing credentials (Google, Spotify already handled)


**Current:** Skylight (and future service) credentials stored in `~/.hermes/.env` on the LXC. Simple, access-controlled to root, good enough for home use.

**Long-term:** Migrate to 1Password CLI (`op`). Install `op` on LXC, create a service account in 1Password Personal vault, store only the service account token in `.env`. Skills call `op read "op://vault/item/field"` at runtime. Single source of truth, credentials rotate in one place.

Note: 1Password Connect Server is Teams/Business only — not available on Personal plan. The `op` CLI is the right path for personal accounts.

---

## 🔑 Key Decisions to Make

| Decision | Options | Notes |
|---|---|---|
| Bridget profile isolation | Full profile vs. same profile + routing tag | Full profile is cleaner for memory separation |
| Shared context mechanism | Sync skill vs. shared SOUL.md path vs. symlinked file | Sync skill most flexible |
| Voice hardware | ESPHome + HA Assist vs. dedicated Pi vs. old phone | ESPHome cheapest, HA-native |
| Hermes ↔ HA Assist bridge | API Server adapter vs. custom HA integration | API Server already in Hermes |
| TTS voice | Edge TTS (free) vs. ElevenLabs (realistic) | Per-profile voice makes sense |

---

*Last updated: May 2026*
