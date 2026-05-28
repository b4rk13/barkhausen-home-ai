# How Kiro Works — Barkhausen Home AI

Kiro is a spec-driven development process adapted from Amazon's internal workflow. It ensures features are well-planned before implementation begins — especially important for a household system where half-built features cause real friction for real people.

---

## Core Philosophy

Don't build until the plan is approved. Three phases, each requiring explicit sign-off before moving forward:

1. **Requirements** — What needs to be built and why
2. **Design** — How it will be built (config, skills, hardware, wiring)
3. **Tasks** — Step-by-step implementation checklist

---

## Spec Structure

Each feature gets its own folder:

```
.kiro/specs/{feature-name}/
├── requirements.md
├── design.md
└── tasks.md
```

Templates for all three live in `.kiro/kiro-system-templates/`.

---

## Workflow

### Phase 1 — Requirements
- Hermes drafts `requirements.md` from your feature description
- Covers user stories (David / Bridget / household), acceptance criteria, scope, dependencies, risks
- **You approve before moving on** — iterate until it's right

### Phase 2 — Design
- Hermes drafts `design.md` covering system components: Hermes config, HA automations, n8n workflows, hardware, skills, credentials
- Includes a verification checklist (no formal test suite — just clear manual checks)
- **You approve before moving on**

### Phase 3 — Tasks
- Hermes drafts `tasks.md` as a numbered, checkboxable implementation plan
- Tasks are concrete and sequential — one deliverable each
- **You approve before implementation begins**

### Implementation
- Work through tasks one at a time
- Stop for review between tasks (or batches, your call)
- Update task checkboxes as work completes
- Commit spec files alongside implementation changes

---

## When to Use Kiro

Use Kiro for any feature that:
- Touches more than one system (e.g., Hermes + HA + n8n)
- Requires hardware setup
- Affects both David and Bridget
- Will take more than one session to complete

Skip Kiro for quick one-offs — a single skill tweak, a memory update, a minor HA config change.

---

## Key Behaviors for Hermes

- **Always check `.kiro/specs/` first** when asked to build something — a spec may already exist
- **Suggest Kiro** when a feature request is non-trivial
- **Don't skip phases** — requirements before design, design before tasks, tasks before implementation
- **Note adapted scope** — templates are for configuration, skills, hardware, and light coding; not traditional software engineering artifacts (no TypeScript interfaces, unit test percentages, etc.)
- **Keep traceability** — task items should reference the requirement they satisfy
