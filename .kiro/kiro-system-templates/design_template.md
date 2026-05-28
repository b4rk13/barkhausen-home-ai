# [Feature Name] Design Document

## Overview

[What this feature does, how it works at a high level, and the key design decisions made.]

---

## Architecture

### Component Diagram

```
[Draw a simple ASCII or Mermaid diagram showing how the pieces connect]

Example:
Telegram → Hermes (bridget profile) → Home Assistant → Lights
                ↓
         Shared household context file
```

### Data / State Flow

[Describe how information moves through the system. What triggers what. What gets persisted where.]

---

## Components

### Hermes Configuration
- **Profile(s) involved**: [e.g., default / bridget]
- **Skills needed**: [New skills to build, or existing skills to modify]
- **Memory changes**: [New memory files, shared context updates, SOUL.md changes]
- **Environment variables**: [New entries needed in ~/.hermes/.env]
- **Gateway changes**: [Routing rules, platform config changes]

### Home Assistant Configuration (if applicable)
- **Integrations**: [HA integrations to add or configure]
- **Automations**: [New HA automations needed]
- **Scripts / Scenes**: [HA scripts or scenes]
- **Entities involved**: [Key entity IDs]

### n8n Workflows (if applicable)
- **New workflows**: [Description of any new n8n workflows]
- **Modified workflows**: [Existing workflow IDs and what changes]
- **Triggers**: [Webhook, schedule, event]

### Hardware (if applicable)
- **Device(s)**: [Make/model, quantity]
- **Placement**: [Room / location]
- **Network**: [VLAN assignment, wired vs wireless]
- **Power**: [PoE, USB, mains]
- **HA integration method**: [ESPHome, Z-Wave, Zigbee, native, etc.]

### External Services (if applicable)
- **Service**: [Name]
- **Auth method**: [API key in .env / OAuth / Playwright session]
- **Credential location**: [~/.hermes/.env key name]

---

## Skill Design (if building a new Hermes skill)

**Trigger conditions**: [When should this skill auto-load or be invoked]

**Inputs**: [What the user says / provides]

**Steps**:
1. [Step]
2. [Step]
3. [Step]

**Outputs**: [What the skill produces — message, HA action, file write, etc.]

**Error handling**: [What to do if a step fails]

---

## File & Directory Changes

```
[Show any new files or directories this feature adds to the repo or ~/.hermes]

Example:
~/.hermes/profiles/bridget/          ← new profile directory
~/.hermes/skills/household-sync/     ← new skill
barkhausen-home-ai/.kiro/specs/bridget-profile/   ← this spec
```

---

## Testing & Verification

[How to confirm this feature is working correctly — no formal test suite needed, just a clear manual verification checklist.]

- [ ] [Verification step]
- [ ] [Verification step]
- [ ] [Verification step — include the exact message or command to test with]

---

## Alternatives Considered

[Brief note on any other approaches that were considered and why this one was chosen. Useful to capture so we don't re-debate later.]

| Alternative | Why not chosen |
|---|---|
| [Option] | [Reason] |

---

## Assumptions

- [Things assumed to be true when designing this — call them out so they can be revisited if wrong]

---

**Requirements Traceability**: Addresses requirements in `requirements.md`

**Review Status**: Draft / In Review / Approved

**Last Updated**: [Date]
