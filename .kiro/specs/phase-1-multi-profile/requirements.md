# Phase 1 — Multi-Profile & Shared Context: Requirements

## 1. Introduction

David and Bridget Barkhausen both use Telegram as their primary interface to Hermes. Currently only David's account is wired to the Hermes gateway. This feature sets up a separate Hermes profile for Bridget so each person has their own memory and conversational context, while both profiles share a common household knowledge base (family facts, home layout, preferences, routines).

Care Bot (Bridget's cancer treatment assistant) is a separate system and is explicitly out of scope here.

**Integration points**: Hermes gateway, Telegram (two accounts), Hermes profile system, shared household context file, ~/.hermes/.env, skill system.

---

## 2. User Stories

### David
- **As David**, I want my Hermes memory and conversation history to remain private to me, so that Bridget's sessions don't surface my personal context or vice versa.
- **As David**, I want Hermes to know household facts (kids, routines, layout) without me having to re-explain them, so that I can ask household questions without preamble.

### Bridget
- **As Bridget**, I want to message Hermes on Telegram and get responses that feel tailored to me, so that the assistant feels like it knows who I am.
- **As Bridget**, I want Hermes to already know our household basics, so that I don't have to set up context from scratch.
- **As Bridget**, I want my memory and preferences to be separate from David's, so that corrections or preferences I share don't accidentally affect his sessions.

### Household (either person)
- **As a household member**, I want either of us to be able to update shared household facts (e.g., kids' schedules change), so that both profiles stay current without manual duplication.
- **As a household member**, I want Hermes to respond in a tone appropriate for each person, so that the assistant feels natural to talk to regardless of who is using it.

---

## 3. Acceptance Criteria

### Profile Isolation
- **WHEN** Bridget messages Hermes on Telegram, **THEN** Hermes **SHALL** respond using Bridget's profile (her USER.md, her MEMORY.md, her tone settings).
- **WHEN** David messages Hermes on Telegram, **THEN** Hermes **SHALL** respond using David's (default) profile, unchanged from current behavior.
- **WHEN** Bridget's session writes a memory, **THEN** that memory **SHALL** be written to Bridget's profile memory files, not David's.
- **IF** one profile's memory is updated, **THEN** the other profile's private memory **SHALL NOT** be affected.

### Shared Household Context
- **WHEN** either profile starts a session, **THEN** Hermes **SHALL** have access to the shared household context (family members, home layout, shared routines).
- **WHEN** the shared household context is updated, **THEN** both profiles **SHALL** see the updated content on their next session.
- **WHEN** Hermes is asked a household question (e.g., "what are the kids' names?"), **THEN** Hermes **SHALL** answer correctly from shared context regardless of which profile is active.

### Telegram Gateway Routing
- **WHEN** a Telegram message arrives from Bridget's account, **THEN** the gateway **SHALL** route it to the bridget profile.
- **WHEN** a Telegram message arrives from David's account, **THEN** the gateway **SHALL** route it to the default profile (current behavior preserved).
- **IF** an unrecognized Telegram account messages Hermes, **THEN** the gateway **SHALL** handle it gracefully (default behavior, not crash).

### Bridget's Profile Baseline
- **WHEN** Bridget's profile is first set up, **THEN** it **SHALL** have her name, relationship to David, and basic personal context seeded in USER.md.
- **WHEN** Bridget interacts with Hermes, **THEN** Hermes **SHALL** address her by name and respond with appropriate warmth/tone.

---

## 4. Scope

### In Scope
- Creating the `bridget` Hermes profile
- Wiring Bridget's Telegram account to her profile in the gateway config
- Defining and seeding the shared household context file
- Mechanism for both profiles to read shared context
- Seeding Bridget's USER.md with baseline personal context

### Out of Scope
- Care Bot integration (separate system, not touched)
- Calendar integration (Phase 2)
- Voice interface (Phase 3+)
- Bridget's profile having different tool access than David's (not needed yet)
- Any workflow automation (n8n) changes

---

## 5. Dependencies & Prerequisites

### Systems Required
- Hermes gateway running and stable on hermesagent LXC (192.168.10.95)
- Telegram gateway active and David's account confirmed working
- Bridget has an active Telegram account

### Information Needed Before Implementation
- Bridget's Telegram account ID (numeric chat ID — needed for gateway routing)
- Agreement on what goes in shared household context vs. per-profile memory
- Bridget's preferences to seed into her USER.md (tone, name she prefers to be addressed by, etc.)

### Must Be Done First
- Nothing — this is Phase 1, no upstream dependencies

---

## 6. Constraints

- Hermes profile system uses `~/.hermes/profiles/<name>/` with isolated memory, config, and skills
- The gateway routes by Telegram chat ID, not username — Bridget's numeric ID must be known
- Shared context cannot be a file that either profile's memory tool writes to automatically (risk of overwrite) — it must be intentionally managed
- Care Bot's Telegram connection must not be disrupted — it runs on a separate n8n workflow and is independent of this gateway

---

## 7. Risks

- **Risk**: Bridget's Telegram chat ID unknown until she messages the bot or we look it up
  - **Likelihood**: Low (easy to get)
  - **Impact**: Medium (blocks routing setup)
  - **Mitigation**: David can forward a message from Bridget, or she messages the bot first and we capture the ID from gateway logs

- **Risk**: Shared context file gets stale or diverges if both profiles can write to it
  - **Likelihood**: Medium
  - **Impact**: Low (household facts change slowly)
  - **Mitigation**: Make shared context read-only from profile sessions; updates are intentional/manual via David's default profile or direct file edit

- **Risk**: Gateway misconfiguration routes Bridget's messages to David's profile
  - **Likelihood**: Low
  - **Impact**: Medium (privacy, wrong memory)
  - **Mitigation**: Test with a known message from Bridget's account before considering complete; verify in gateway logs

---

**Document Status**: Draft

**Last Updated**: May 2026

**Related Roadmap Phase**: Phase 1 — Multi-Profile & Shared Context
