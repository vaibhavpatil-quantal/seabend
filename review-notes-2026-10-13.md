# Review Notes: Work Order Follow-Up Draft (13 Oct 2026)

Independent review of the Realm-X draft built on 12 Oct 2026 (see [`appfolio-work-order-follow-up-handoff-2026-10-12.md`](./appfolio-work-order-follow-up-handoff-2026-10-12.md)). The handoff was checked against the screenshots in [`docs/screenshots`](./docs/screenshots).

## 1. Repo visibility incident (resolved)

- On 13 Oct 2026 the GitHub repo `vaibhavpatil-quantal/seabend` was found to be **public**, although the README requires it to be private.
- It contained client documents and 11 AppFolio screenshots showing staff names (Leila Buker, Dev Chandan), property names (e.g. Montlake Villas), and Seabend's internal workflow list.
- **Action taken:** the repo was switched to **private** on 13 Oct 2026.
- **Follow-up:** tell the manager that the repo was public for a period (approx. 12–13 Oct). Never make this repo public again. Deleting files does not remove them from git history.

## 2. Verified as correct

| Check | Evidence |
|---|---|
| Flow is a **DRAFT**; Publish is a separate menu option and was not clicked | Screenshot 11 |
| Demo token `SEABEND_AUTOMATION_DEMO_ONLY_2026` is required in the work order description, so real work orders can't start the flow | Screenshot 03 |
| `Work Order Is Urgent is No` is joined with AND | Screenshot 10 |
| `Work Order Completed` and `Work Order Canceled` interrupts each go to End Workflow | Screenshot 04 |
| Cadence: pause 3d → email and text tenants → pause 2d → email and text tenants → pause 2d → email work order assignees | Screenshots 05–07, 09 |
| Day-7 action is `Email Work Order Assignees` and passes validation | Screenshot 09 |
| No existing flow was edited and no live permissions were changed | Handoff §3 |

The handoff's list of gaps is accurate and honest: blank Reply-To, a tenant reply doesn't stop the flow, day-7 goes to assignees rather than the property manager, and the day-5 steps are unverified.

## 3. Recommended improvements (not yet done)

1. **Verify the day-5 email and text steps** in the builder: subject, body, Reply-To, daytime and weekdays toggles, co-signers, and failure notification. There's no screenshot of their final state.
2. **Add an issue summary to the tenant messages.** Right now they only show the work order number, so tenants may not know which request is meant. Use a summary or category field if one exists. **Don't insert the Description field**, because it contains the demo token.
3. **Rename the flow.** "Work Order Created" is confusing next to the client's other flows. The handoff only checked the list menu, so check the **Settings** button in the builder header for a name field.
4. **Investigate Montlake Villas.** Even the *Property Manager role* has no user with access to it (screenshot 08), so it may be inactive or have no staff assigned. Ask Leila. If it's inactive, excluding it from the trigger's property scope may let a fixed property manager or role recipient work for day 7.
5. **Day-7 routing to property managers:** AppFolio's Tasks panel has a **"Property Staff Assignments"** report (screenshot 11). It may show which property manager handles each property, which would support the per-property-flow option in handoff §6.
6. **Reply-To is the #1 blocker before any test.** The email asks tenants to reply, but replies currently go to a do-not-reply address. Keep this at the top of the client question list.

## 4. Status of related docs

- `seabend-requirements-call-2026-10-06.md` §7 (9 Oct, "can't reach builder") is now marked as historical, with a pointer to the handoff.

## 5. Next steps

1. Get the client's answers to handoff §8 (Reply-To inboxes, day-7 recipient, test tenant or unit, and so on).
2. Re-open the draft with **Edit Workflow** only. Do items 1–3 above and keep the demo token.
3. Test only with a client-approved test tenant or unit, then roll out in stages (handoff §9).
