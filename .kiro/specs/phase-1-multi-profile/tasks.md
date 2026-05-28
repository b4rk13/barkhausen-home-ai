# Phase 1 — Multi-Profile & Shared Context: Tasks

## Overview

**Requirements**: `requirements.md`
**Design**: `design.md`
**Roadmap Phase**: Phase 1 — Multi-Profile & Shared Context

---

## Tasks

### Phase 1: Bridget Profile Setup

- [ ] **1.1** Confirm gateway profile routing config key
  - **What**: Inspect `~/.hermes/config.yaml` and running gateway config to confirm the exact syntax for mapping a Telegram chat ID to a named profile. Check gateway docs / source if unclear.
  - **Deliverable**: Known config key path — ready to add Bridget's routing entry
  - **Requires**: Nothing

- [ ] **1.2** Create the `bridget` Hermes profile
  - **What**: Run `hermes profile create bridget --clone default` to scaffold the profile directory from the default profile. Confirm `~/.hermes/profiles/bridget/` exists with memories/ and config.yaml.
  - **Deliverable**: `~/.hermes/profiles/bridget/` directory created
  - **Requires**: 1.1

- [ ] **1.3** Seed Bridget's USER.md
  - **What**: Write `~/.hermes/profiles/bridget/memories/USER.md` with: name, relationship to David, health context (cancer treatment — aware but not focus), tone (casual, warm, concise), primary use cases (schedules, recipes, wifi help, household questions), non-technical preference.
  - **Deliverable**: USER.md populated — verify with `hermes --profile bridget chat -q "Who am I?"`
  - **Requires**: 1.2

- [ ] **1.4** Wire Bridget's Telegram account to her profile in gateway config
  - **What**: Add Telegram chat ID `8686564175` → `bridget` profile mapping to `~/.hermes/config.yaml` using the syntax confirmed in 1.1. Restart gateway.
  - **Deliverable**: Gateway config updated, gateway restarted
  - **Requires**: 1.1, 1.2

### Phase 2: Shared Household Context

- [ ] **2.1** Gather household content from David
  - **What**: Before writing the file, confirm with David: kids' names + ages, schools, key locations (swim class, etc.), Bridget's dietary specifics, any shared preferences or routines to include now. This is a conversation, not a code task.
  - **Deliverable**: All content needed to populate household.md collected
  - **Requires**: Nothing (can run in parallel with Phase 1)

- [ ] **2.2** Create `~/.hermes/household.md`
  - **What**: Write the shared household context file using the structure from design.md, populated with content from 2.1. Sections: Family Members, Key Locations, Dietary & Health, Shared Preferences, Home Layout, Key Contacts (stub — full detail in Notion).
  - **Deliverable**: `~/.hermes/household.md` exists and is populated
  - **Requires**: 2.1

- [ ] **2.3** Build the `household-context` skill
  - **What**: Create `~/.hermes/skills/household-context/SKILL.md`. Skill should: always auto-load household.md into context; handle explicit "add X to household context" by appending to the correct section; proactively suggest adding household-relevant info when detected.
  - **Deliverable**: Skill installed, loads household.md on session start
  - **Requires**: 2.2

### Phase 3: Notion Contacts Database

- [ ] **3.1** Create Notion contacts database
  - **What**: Use Notion MCP to create a new database in the household Notion space. Fields: Name, Relationship, Phone/Contact, Notes, Active (checkbox). Title the database "Household Contacts".
  - **Deliverable**: Notion database created, database ID noted for household.md reference
  - **Requires**: Nothing (parallel with other phases)

- [ ] **3.2** Seed initial contacts
  - **What**: Add seed entries for: Keira's nanny, grandparents (both sets if applicable), babysitters, key school contacts. David to provide names and details.
  - **Deliverable**: Core contacts in Notion, queryable by name
  - **Requires**: 3.1, content from David

- [ ] **3.3** Add Notion contacts reference to household.md
  - **What**: Update the Key Contacts section of household.md with names + relationships (no phone numbers in the file), with a note that full details are in Notion "Household Contacts" database.
  - **Deliverable**: household.md Key Contacts section updated
  - **Requires**: 3.1, 3.2, 2.2

### Phase 4: End-to-End Verification

- [ ] **4.1** Verify profile isolation
  - **What**: From Bridget's Telegram account, send a message to the Hermes bot. Check: (a) gateway logs show `bridget` profile routing, (b) Hermes addresses her by name, (c) response tone is casual/warm. Then verify David's account still routes to default profile unchanged.
  - **Deliverable**: Both profiles confirmed routing correctly
  - **Requires**: 1.4, 1.3

- [ ] **4.2** Verify shared household context
  - **What**: From each profile, ask "what are the dietary restrictions in this household?" — both should answer correctly from household.md. Ask "what's the nanny's name?" — should query Notion and return the right answer.
  - **Deliverable**: Shared context confirmed accessible from both profiles
  - **Requires**: 2.3, 3.3, 4.1

- [ ] **4.3** Verify household context write mechanism
  - **What**: Say "add [test fact] to household context" from David's profile — confirm household.md is updated. Then start a new Bridget session and confirm she can see it. Then test Hermes proactive suggestion: tell it a household-relevant fact without explicitly asking to save it — confirm it suggests adding to household context.
  - **Deliverable**: Write mechanism confirmed working from both profiles
  - **Requires**: 4.2

- [ ] **4.4** Update ROADMAP.md and commit all spec files
  - **What**: Check off Phase 1 items in ROADMAP.md. Commit all spec files (requirements, design, tasks with checkboxes ticked) and any config/skill files created.
  - **Deliverable**: Clean commit, Phase 1 marked done in roadmap
  - **Requires**: 4.3

---

## Definition of Done

A task is complete when:
- The deliverable exists and behaves as described
- Any affected docs (ROADMAP.md, household.md, skill files) are updated
- Changes committed to the repo

---

## Notes & Risks

- Task 1.1 (confirm routing syntax) is a prerequisite for 1.4 — check the live gateway config before assuming the yaml key names
- Task 2.1 (content gathering) can happen async — start it in conversation with David early so it doesn't block 2.2
- Bridget's dietary specifics are sensitive — store in household.md in plain language but don't over-detail; enough for recipe/food context
- If skills directory is per-profile rather than shared, household-context skill will need to be installed in both — check during 2.3

---

**Status**: Not Started

**Current Task**: 1.1

**Last Updated**: May 2026
