# Seabend Automation Context

> **Confidential client material. Keep this repository private.** Do not commit credentials, API keys, tenant exports, or recordings. The curated AppFolio screenshots in this repository contain internal client workflow information and must not be placed in a public repository.

This repository preserves the requirements and implementation context for Seabend's automation work so it can be resumed from a different laptop or by another agent.

## Start here

Read these files in order:

1. [`seabend-requirements-call-2026-10-06.md`](./seabend-requirements-call-2026-10-06.md) — complete requirements gathered from the client call and the broader automation backlog.
2. [`appfolio-work-order-follow-up-handoff-2026-10-12.md`](./appfolio-work-order-follow-up-handoff-2026-10-12.md) — exact Realm-X draft configuration, message copy, safety controls, unresolved questions, and next steps.

## Current AppFolio status

- The first-priority Work Order Follow-Up workflow exists in Realm-X as `Work Order Created`.
- It is saved as **DRAFT** and must remain unpublished until the client approves a safe test tenant/unit and resolves the open routing questions.
- No existing published workflow was edited.
- No live tenant was contacted and no live test was run.

## Restore on a new laptop

```powershell
git clone https://github.com/vaibhavpatil-quantal/seabend.git
cd seabend
```

Then:

1. Read both documents above before opening AppFolio.
2. Sign into the approved Seabend AppFolio user; never store its credentials in this repository.
3. Open Realm-X Flows and confirm `Work Order Created` still shows **DRAFT**.
4. Follow the handoff's next-agent procedure and safety rules.

Curated workflow screenshots are stored in [`docs/screenshots`](./docs/screenshots). They are included only to preserve the Realm-X build context and must remain in a private repository.

