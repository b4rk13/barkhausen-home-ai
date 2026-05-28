# Phase 1 — Multi-Profile & Shared Context: Design Document

## Overview

Two changes working together: (1) a separate Hermes profile for Bridget with her own memory and tone, routed from her Telegram account; (2) a shared household context file that both profiles load automatically, with an explicit write mechanism and proactive suggestion behavior.

---

## Architecture

```
Telegram: David (existing)  ──→  Hermes default profile  ──→  David's memory
                                         ↓
                               ~/.hermes/household.md  (shared, read by both)
                                         ↑
Telegram: Bridget (8686564175) →  Hermes bridget profile  ──→  Bridget's memory
```

### Shared Context Flow

```
Session starts
    → Profile loads its own USER.md + MEMORY.md
    → household-context skill loads ~/.hermes/household.md into context
    → Hermes answers with combined awareness

"Add X to household context" (either profile)
    → household-context skill appends to ~/.hermes/household.md

Hermes detects shared-relevant info
    → Proactively suggests: "That sounds like it applies to the whole household — want me to add it to shared context?"
```

---

## Components

### 1. Bridget Profile

**Directory**: `~/.hermes/profiles/bridget/`

Structure mirrors the default profile:
```
~/.hermes/profiles/bridget/
├── memories/
│   ├── USER.md       ← seeded with Bridget's context (see below)
│   └── MEMORY.md     ← starts empty, grows over time
└── config.yaml       ← cloned from default, tone adjustments applied
```

**USER.md seed content**:
- Name: Bridget Barkhausen
- Relationship: married to David, two kids
- Health context: undergoing cancer treatment (NALIRIFOX chemo) — Hermes should be aware but not dwell on it; Care Bot handles treatment specifics
- Tone: casual, warm, concise responses preferred
- Primary use cases: checking schedules, logging recipes to Skylight, household questions, tech help (wifi/internet troubleshooting)
- Not technical — avoid jargon, explain things plainly

**config.yaml**: Clone default profile config. No toolset changes needed for Phase 1.

---

### 2. Telegram Gateway Routing

Hermes gateway routes incoming messages to profiles based on Telegram chat ID, configured in `~/.hermes/config.yaml` under the gateway section.

**Change**: Add Bridget's Telegram ID (`8686564175`) mapped to the `bridget` profile.

Config pattern (exact key names to be confirmed against running gateway config):
```yaml
gateway:
  telegram:
    profiles:
      8686564175: bridget
```

**Verification**: After config change, Bridget sends a test message → confirm gateway logs show routing to `bridget` profile → confirm response uses her USER.md context.

---

### 3. Shared Household Context File

**Location**: `~/.hermes/household.md`

Lives outside any profile directory — neither profile owns it. Both profiles load it via the `household-context` skill (see below).

**Initial content sections**:
```markdown
# Barkhausen Household Context

## Family Members
- David Barkhausen (husband/dad)
- Bridget Barkhausen (wife/mom) — on cancer treatment, strict dietary restrictions
- [Kids' names, ages, schools — to be filled in]

## Key Locations
- Home: [address or neighborhood — TBD]
- School: [name, address]
- Swim class: [location, day/time]
- [Other regular venues]

## Dietary & Health
- Bridget: strict cancer treatment diet — [specifics to be provided by David]
- David: high-cholesterol — avoid saturated fats, red meat in moderation

## Shared Preferences
- [Music, temperature, routines — to be filled in]

## Home Layout
- Trusted network: 192.168.10.0/24
- Rooms with smart devices: [living room, bedroom, office — expand as needed]
- Key devices: [brief list]

## Key Contacts
> Full contact details in Notion contacts database. Reference by name here.
- Keira's nanny: [name] — see Notion
- Grandparents: [names] — see Notion
- Babysitters: see Notion
- [School contacts] — see Notion
```

**Update rules**:
- Either profile can append to this file when explicitly asked ("add X to household context")
- Hermes proactively suggests adding to household context when info seems shared (e.g., "that sounds like it applies to both of you — want me to add it to the household context?")
- File is human-readable and can be edited directly on the LXC

---

### 4. household-context Skill

**Location**: `~/.hermes/skills/household-context/SKILL.md`

Installed into the default Hermes skills directory — loaded by both profiles automatically since profiles share the skills directory unless overridden.

**Behavior**:
- Loads `~/.hermes/household.md` into context at session start (auto-load trigger)
- Responds to explicit "add [X] to household context" by appending to the correct section
- Proactively suggests household context updates when Hermes detects info that seems household-relevant (dietary facts, new contacts, schedule changes, location updates)

**Trigger conditions** (for auto-load):
- Always — household context is lightweight and always relevant

---

### 5. Notion Contacts Database

**Scope for Phase 1**: Stub only — create the database structure in Notion, add a few seed entries. Full population is ongoing.

**Database fields**:
- Name
- Relationship (nanny, grandparent, babysitter, school contact, etc.)
- Phone / contact method
- Notes (tone, context, kids they're associated with)
- Active (yes/no)

**How it's used**: household.md references contacts by name + relationship. When either profile needs details ("what's the nanny's number?"), Hermes queries Notion by name.

---

## File & Directory Changes

```
~/.hermes/profiles/bridget/           ← new profile
~/.hermes/profiles/bridget/memories/USER.md
~/.hermes/profiles/bridget/memories/MEMORY.md
~/.hermes/profiles/bridget/config.yaml
~/.hermes/household.md                ← new shared context file
~/.hermes/skills/household-context/   ← new skill
~/.hermes/config.yaml                 ← gateway routing addition

barkhausen-home-ai/.kiro/specs/phase-1-multi-profile/design.md  ← this file
```

---

## Testing & Verification

- [ ] `hermes profile list` shows `bridget` profile
- [ ] `hermes --profile bridget chat -q "What's my name?"` responds with Bridget, not David
- [ ] Gateway config updated — confirm with `hermes gateway status`
- [ ] Bridget sends test message on Telegram → gateway logs show `bridget` profile → response is correct
- [ ] David sends message → still routes to default profile, behavior unchanged
- [ ] Both profiles: ask "what are the dietary restrictions in this household?" → correct answer from household.md
- [ ] Either profile: say "add X to household context" → household.md is updated → other profile sees it in next session
- [ ] Hermes proactively suggests household context when appropriate (test with: "Bridget can't eat sugar" — expect suggestion to add to household context)
- [ ] "What's the nanny's name?" → Hermes queries Notion contacts → returns correct result

---

## Alternatives Considered

| Alternative | Why not chosen |
|---|---|
| Single profile with routing tag | Doesn't isolate memory — Bridget's corrections would affect David's sessions |
| Shared SOUL.md for household context | SOUL.md is per-profile; can't be truly shared without symlinks or duplication |
| Google Contacts for contacts DB | Less queryable by Hermes; Notion already integrated and structured |
| household.md inside the repo | Would require git pull to stay current; file on LXC is simpler and always live |

---

## Assumptions

- Bridget's Telegram account ID 8686564175 is correct and stable
- Both profiles share `~/.hermes/skills/` (default Hermes behavior — profiles don't isolate skills unless explicitly configured)
- Hermes gateway supports per-chat-ID profile routing in config.yaml (to be confirmed against live gateway config during implementation)
- Bridget's dietary specifics will be provided by David before or during task 3.2 (seeding household.md)

---

**Requirements Traceability**: Addresses all requirements in `requirements.md`

**Review Status**: Draft

**Last Updated**: May 2026
