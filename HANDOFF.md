# Handoff — Barkhausen Home AI

Last updated: May 2026

---

## Where We Left Off

Phase 1 spec is complete and committed. All three Kiro documents are written and awaiting David's review/approval before any implementation begins.

**Status**: Waiting for David to review and approve the Phase 1 spec before starting task 1.1.

---

## What's Been Done This Session

- Created repo: https://github.com/b4rk13/barkhausen-home-ai
- Wrote ROADMAP.md (6 phases, mind-map structure)
- Adapted Kiro templates from software-eng to config/hardware/skills context
- Captured Phase 2 calendar architecture decision in ROADMAP.md
- Wrote full Phase 1 Kiro spec:
  - `.kiro/specs/phase-1-multi-profile/requirements.md`
  - `.kiro/specs/phase-1-multi-profile/design.md`
  - `.kiro/specs/phase-1-multi-profile/tasks.md`

---

## Phase 1 Spec Summary

**Goal**: Separate Hermes profiles for David and Bridget (Telegram routing), plus a shared household context file both profiles read.

**Key facts baked into the spec**:
- Bridget's Telegram ID: `8686564175`
- Bridget's profile tone: casual, warm, concise — non-technical
- Bridget's primary use cases: schedules, recipes, wifi help, household questions
- Shared context includes: family, locations, dietary (Bridget's cancer diet + David's high-cholesterol), contacts
- Contacts database: Notion (already integrated)
- Shared context update model: explicit write ("add X to household context") + proactive Hermes suggestion when info seems household-relevant
- Shared context is read-only from sessions — no auto-writes

**Still needed from David before task 2.1**:
- Kids' names, ages, schools
- Key locations (swim class venue, regular spots)
- Bridget's dietary specifics (enough for recipe/food context)
- Notion contacts content (nanny name, grandparents, babysitters, school contacts)

---

## Pending Decisions

| Decision | Status |
|---|---|
| Phase 1 spec approval | Awaiting David review |
| Contacts database: confirmed Notion | Decided |
| Calendar architecture (Phase 2) | Deferred — needs design session. See ROADMAP.md Phase 2 note. David + Bridget have separate Google Calendars; Skylight shows both + kids events. Likely direction: shared Family Google Calendar + Skylight profiles. |
| Shared context update model | Decided — explicit write + proactive suggestion |
| Bridget's profile tone | Decided — casual, warm, concise |

---

## Next Steps (in order)

1. David approves Phase 1 spec (requirements, design, tasks)
2. David provides household content (kids, locations, dietary, contacts)
3. Start task 1.1 — confirm gateway routing syntax in live config
4. Execute remaining tasks per tasks.md

---

## Key Files & Paths

| Thing | Location |
|---|---|
| Repo | https://github.com/b4rk13/barkhausen-home-ai |
| Roadmap | ROADMAP.md |
| Phase 1 spec | .kiro/specs/phase-1-multi-profile/ |
| Kiro templates | .kiro/kiro-system-templates/ |
| Hermes config | ~/.hermes/config.yaml |
| Hermes skills | ~/.hermes/skills/ |
| Household context (not yet created) | ~/.hermes/household.md |
| Bridget profile (not yet created) | ~/.hermes/profiles/bridget/ |
