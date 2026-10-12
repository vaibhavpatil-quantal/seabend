# Seabend AppFolio Work Order Follow-Up: Implementation Handoff

| | |
|---|---|
| **Last updated** | 12 Oct 2026 |
| **Client** | Seabend Capital and Management LLC |
| **System** | AppFolio Property Manager → Realm-X Flows |
| **AppFolio user used** | `Dev Chandan` |
| **Workflow shown in AppFolio as** | `Work Order Created` |
| **Current status** | **DRAFT — not published and not running** |
| **Category** | Uncategorized |
| **Related requirements** | [`seabend-requirements-call-2026-10-06.md`](./seabend-requirements-call-2026-10-06.md) |

> **Critical safety rule:** This is a live AppFolio account containing real tenants, contact details, work orders, properties, and financial data. Do not click **Publish**, do not run a test against a real work order, and do not remove the demo-only trigger condition until Seabend has approved the final design and supplied a safe test tenant/unit.

## 1. Purpose of this document

This is the handoff for the first-priority workflow discussed during Seabend's 6 Oct 2026 requirements call: automating follow-ups for open maintenance work orders.

It records:

- What was built in Realm-X on 12 Oct 2026.
- The exact safety controls used to prevent live execution.
- The intended email and SMS cadence.
- AppFolio limitations discovered during the build.
- Decisions still required from Seabend.
- The exact next steps for another agent or team member.

The original discovery notes cover all Seabend automation opportunities. This file focuses only on the work-order follow-up workflow and supersedes the older statement that the `Dev Chandan` user could only view flow history. The client subsequently granted workflow-builder permission.

## 2. Client requirement being implemented

### Current pain point

Property managers manually monitor a high volume of work orders. After assigning work to a vendor or internal maintenance team, they must remember to check whether it was scheduled and completed. Vendors do not always communicate delays, so property managers spend time repeatedly following up.

Leila said tenants usually respond faster than vendors. Her desired process was approximately:

1. For a non-urgent maintenance request, contact the tenant after three days.
2. If it remains unresolved, follow up again two days later.
3. At approximately one week, escalate the open work order for human attention.
4. Stop reminders if the work order is completed or canceled.

### Current draft interpretation

- The clock starts when the **work order is created**, not when a vendor is assigned.
- Only work orders AppFolio considers **not urgent** qualify.
- Day 3: email and text the financially responsible tenants in the unit.
- Day 5: send a second email and text.
- Day 7: email all internal AppFolio users assigned to the work order.
- Completion or cancellation interrupts and ends the workflow at any time.

The day-7 recipient is not the exact requested "assigned property manager" because Realm-X did not provide a dynamic action for that recipient. See the limitations section.

## 3. Safety controls

The draft currently has multiple independent protections:

1. **Draft status:** The workflow is saved as `DRAFT`. AppFolio exposes **Publish** as a separate menu action. It has not been clicked.
2. **Unique demo token:** The workflow can start only when the work order's Service Request Description contains:

   ```text
   SEABEND_AUTOMATION_DEMO_ONLY_2026
   ```

   Ordinary live work orders should not contain this exact value.
3. **Non-urgent restriction:** `Work Order Is Urgent is No`.
4. **Completion interrupt:** A completed work order immediately moves to an End Workflow path.
5. **Cancellation interrupt:** A canceled work order immediately moves to an End Workflow path.
6. **No approved test record exists:** No test tenant, unit, property, or work order has been created or used.
7. **No existing flow was edited:** The workflow was built from scratch. Existing move-out, renewal, delinquency, accounting, and review-campaign flows were not changed.

### Actions that remain prohibited

- Do not publish or activate the draft.
- Do not remove or weaken the demo-token condition.
- Do not create a fake tenant/unit/work order in the live account without written client approval.
- Do not test with a real tenant's email address or phone number.
- Do not change user or property permissions to make a recipient validation warning disappear.
- Do not use Realm-X Assistant to generate, send, run, or bulk-execute anything.
- Do not edit an existing published Seabend workflow.

## 4. Exact Realm-X configuration

### 4.1 Trigger

**Trigger:** `Work Order Created`

**Scope shown by the trigger card:**

- Work Order Type: Any
- Properties: All properties
- Conditions: 2 conditions
- Workflow owner: Dev Chandan

**Condition group (AND):**

```text
Work Order Service Request Description
contains
SEABEND_AUTOMATION_DEMO_ONLY_2026

AND

Work Order Is Urgent
is
No
```

Why `Work Order Created` was chosen:

- The call described following up about three days after a non-urgent request is submitted.
- It covers work that will later be assigned either to an external vendor or to internal maintenance.
- `Vendor Assigned to Work Order` could miss internally assigned work.
- Maintenance Performer-only triggers were rejected because it is not known whether every property is enrolled in Maintenance Performer.

Other triggers observed but not selected:

- Vendor Assigned to Work Order
- Vendor Assigned to Work Order (Maintenance Performer Only)
- Work Order First Assigned to Maintenance Tech
- Work Order First Assigned to Maintenance Tech (Maintenance Performer Only)

### 4.2 Global interrupt paths

Two interrupts were added. Realm-X created an End Workflow step under each interrupt automatically.

```text
Work Order Completed → End Workflow
Work Order Canceled  → End Workflow
```

`Work Order Marked Work Done` was available but was not added. The client should clarify whether reminders must also stop at that earlier operational status or only when a work order is fully completed.

### 4.3 Main workflow path

```text
Work Order Created
  → Pause for Duration: 3 days
  → Email All Tenants In Unit
  → Text All Tenants In Unit
  → Pause for Duration: 2 days
  → Email All Tenants In Unit
  → Text All Tenants In Unit
  → Pause for Duration: 2 days
  → Email Work Order Assignees
  → End Workflow
```

The three pauses produce the intended day-3, day-5, and day-7 cadence.

## 5. Message actions and wording

### 5.1 Day-3 tenant email

**Action:** `Email All Tenants In Unit`

AppFolio defines this action as emailing the **financially responsible tenants** in the unit. It does not necessarily email every occupant or only the original work-order reporter.

**Subject:**

```text
Update on your maintenance request #{{Work Order Number}}
```

`Work Order Number` is an AppFolio inserted-field token, not hand-typed placeholder text.

**Body:**

```text
Hi,

We're following up on your recent maintenance request. Has someone contacted you or completed the work?

Please reply with an update.

Thank you,
Seabend Capital and Management
```

**Intended settings:**

- Include Co-Signers: Off
- Send during daytime hours: On
- Send on weekdays only: On
- Pause and notify on failure: Off
- Reply-To: Blank in the draft; must be resolved before publishing

### 5.2 Day-3 tenant text

**Action:** `Text All Tenants In Unit`

AppFolio defines this action as texting the **financially responsible tenants** in the unit.

**Message:**

```text
Seabend follow-up for maintenance request #{{Work Order Number}}: Has someone contacted you or completed the work? Please reply with an update.
```

`Work Order Number` is an inserted-field token.

**Intended settings:**

- Include Co-Signers: Off
- Send during daytime hours: On
- Send on weekdays only: On
- Pause and notify on failure: Off
- Reply-To: Blank in the draft; see reply-routing issue below

### 5.3 Day-5 tenant email

**Action:** `Email All Tenants In Unit`

**Subject:**

```text
Second follow-up on maintenance request #{{Work Order Number}}
```

**Body:**

```text
Hi,

We're checking again regarding your maintenance request. Has the work been completed? If not, please let us know whether a vendor has contacted you.

Thank you,
Seabend Capital and Management
```

**Intended settings:** Same as the day-3 email.

> The day-5 email was configured during the guided session, but its final content/settings were not captured in a dedicated screenshot. Re-open this step and verify the subject, body, Reply-To, daytime/weekdays toggles, co-signer setting, and failure-notification setting before presenting the draft.

### 5.4 Day-5 tenant text

**Action:** `Text All Tenants In Unit`

**Message:**

```text
Seabend second follow-up for maintenance request #{{Work Order Number}}: Has the work been completed? If not, has a vendor contacted you? Please reply with an update.
```

**Intended settings:** Same as the day-3 text.

> The day-5 text was configured during the guided session, but its final content/settings were not captured in a dedicated screenshot. Re-open and verify it before the client walkthrough.

### 5.5 Day-7 internal escalation

**Action:** `Email Work Order Assignees`

AppFolio defines this action as emailing all **users assigned to the work order**. This is a dynamic recipient and avoids the all-property permission error encountered with a fixed user/role, but it does not guarantee that the responsible property manager receives the alert.

**Subject:**

```text
Action required: work order #{{Work Order Number}} is 7 days old
```

**Body:**

```text
Work order #{{Work Order Number}} has remained open for seven days.

Please review the work-order notes, confirm the current status with the tenant or vendor, and document the next action in AppFolio.

This is an automated maintenance follow-up reminder.
```

**Intended settings:**

- Send during daytime hours: On
- Send on weekdays only: On
- Pause and notify on failure: Off
- Only perform this action if: No extra action-level condition

The builder displayed `This step looks good`, and the workflow remained a draft.

## 6. AppFolio behavior discovered

### Draft and publishing

- New workflows open with a visible `DRAFT` status.
- Saving a workflow keeps it in draft.
- Publishing is a separate action in the workflow list menu.
- The draft's list-menu options were:
  - Edit Workflow
  - Change Category
  - Duplicate
  - View Activity Log
  - Publish
  - Delete
- No rename option was available. The custom workflow was automatically named after its trigger: `Work Order Created`.

### Tenant communication recipients

- `Email All Tenants In Unit` and `Text All Tenants In Unit` target financially responsible tenants.
- Co-signers can be included separately; they were intentionally left out of the draft.
- No action was found for contacting only the person who originally reported the work order.

### Email replies

- If Reply-To is blank on the tenant-email action, replies go to a do-not-reply address and are not read or received.
- The email currently asks the tenant to reply, so a monitored Reply-To must be selected before any test or publication.

### SMS replies

- If Reply-To is blank, AppFolio sends replies to the last employee who texted that tenant before the workflow.
- If nobody previously texted the tenant, AppFolio notifies the email configured in the company's texting settings.
- The client must confirm whether this routing is acceptable or select a specific recipient.

### Failure notifications and property access

- The default/selected failure-notification user appeared internally as `user_472`.
- AppFolio reported that the selected user did not have access to `Montlake Villas`.
- Failure notifications were therefore turned off in message actions rather than changing live user permissions.
- Before production, choose an authorized failure recipient who has access to all properties within the workflow's scope, or scope the workflow more narrowly.

### Day-7 property-manager problem

The requested behavior was to alert the responsible property manager on day 7. The builder did not expose a dynamic `Email Assigned Property Manager` action.

Two attempted recipients failed validation:

1. **Leila Buker** as a fixed user: no access to `Montlake Villas`.
2. **Property Manager** as a user role: AppFolio still reported that no selected user had access to `Montlake Villas`.

The incomplete `Email Selected Users` action was replaced with `Email Work Order Assignees`.

Possible production solutions:

- Keep `Email Work Order Assignees` if Seabend accepts notifying internal assignees rather than the PM.
- Select one central maintenance supervisor who already has access to every included property.
- Correct Seabend's property-staff assignments if the missing access is unintended.
- Build separate workflows for property lists managed by different PMs, with each flow using the correct fixed PM recipient.
- Use an API/external automation if Realm-X cannot resolve the property-to-PM relationship dynamically.

Do not grant or change permissions without client authorization.

## 7. Known gaps and risks

1. **Tenant replies do not stop the flow.** Available interrupts included completion and cancellation, but no tenant-replied interrupt was found. A tenant may reply after day 3 and still receive the day-5 message if the work order remains open.
2. **Email Reply-To is unresolved.** The current copy asks tenants to reply, but the draft has no monitored Reply-To address.
3. **Day-7 recipient differs from the request.** It emails work-order assignees, not necessarily the responsible PM.
4. **Financially responsible tenants only.** The reporter could be an on-site manager or another occupant who will not receive these actions.
5. **Vacant/common-area work orders may have no tenant recipients.** Confirm expected behavior for these cases and whether such orders should be excluded at the trigger.
6. **Calendar versus business-day behavior needs confirmation.** The pause uses days, while message actions are intended to send only on weekdays and during daytime hours. A weekend may delay delivery beyond the exact day-3/day-5/day-7 dates.
7. **Trigger begins at creation.** Confirm whether Seabend instead wants the clock to begin only after vendor/maintenance assignment.
8. **Urgent requests are excluded.** A separate urgent-work-order workflow may be needed.
9. **No restart after a delay note.** The requested future behavior—restart a one-week follow-up clock after a note such as "part ordered"—is not included in v1.
10. **Work Done is not an interrupt.** Only Completed and Canceled stop the flow. Confirm whether `Work Order Marked Work Done` should also stop tenant messages.
11. **All-properties scope remains visible.** The unique demo token prevents ordinary records from entering, but production rollout should start with an approved test record and then a limited property scope.
12. **Exact day-5 settings require an audit.** They were entered during the guided build but not captured in a final screenshot.

## 8. Questions to send Seabend before testing

1. Can Seabend provide a test tenant/unit using a safe email address and phone number controlled by Seabend or our team?
2. Which monitored Seabend inbox should receive tenant email replies?
3. Where should tenant SMS replies go: a specific employee, the property manager, or the existing company texting inbox?
4. Who should receive the day-7 escalation?
   - Work-order assignees;
   - One central maintenance supervisor;
   - The responsible PM through property-specific workflows; or
   - Another recipient/method?
5. Should only financially responsible tenants be contacted? Should co-signers be included?
6. Should the cadence use calendar days or business days?
7. Should a tenant reply prevent the day-5 reminder? If yes, what manual or technical flag should stop it?
8. Should reminders stop when a work order is marked `Work Done`, or only when it is `Completed` or `Canceled`?
9. Should the clock start at work-order creation or when a vendor/maintenance technician is assigned?
10. How should vacant-unit and common-area work orders be handled?
11. Is `Is Urgent = No` the correct definition of a non-urgent work order?
12. Who should receive step-failure notifications and has access to every property in scope?

## 9. Recommended next-agent procedure

1. Read this file and the original requirements document.
2. Open AppFolio → Realm-X → Flows.
3. Confirm `Work Order Created` still shows `DRAFT` before opening it.
4. Use **Edit Workflow** only. Do not select Publish.
5. Verify both trigger conditions and both interrupt paths.
6. Audit every message action, especially:
   - Day-5 email and SMS text;
   - Daytime-hours and weekdays-only toggles;
   - Co-signer setting;
   - Failure-notification setting;
   - Reply-To fields.
7. Do not remove the demo token.
8. Get written client answers to the questions above.
9. Once a client-approved test tenant/unit exists, update only the approved test scope and conduct a supervised test. Keep the production workflow unpublished until the test result is reviewed.
10. If a production rollout is approved, use staged deployment:
    - Approved test tenant/unit;
    - One approved property;
    - A small property group;
    - All approved properties.

## 10. Evidence/screenshots from the guided build

The following local screenshots were used during the build. They may help reconstruct the UI state:

- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 134229.png` — builder permission and New Workflow button.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 140323.png` — new draft builder and trigger editor.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 142906.png` — demo-token condition.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 144943.png` — completed and canceled interrupt paths.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 153632.png` — day-3 email action in the main path.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 155841.png` — valid day-3 SMS with inserted Work Order Number.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 161450.png` — two-day pause after the day-3 actions.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 163833.png` — Property Manager role access failure for Montlake Villas.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 165101.png` — valid day-7 assignee email action.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 165514.png` — `Is Urgent is No` condition joined with AND.
- `C:\Users\patil\Pictures\Screenshots\Screenshot 2026-10-12 165700.png` — workflow list showing DRAFT and separate Publish action.

## 11. Current completion status

The Realm-X proof-of-concept structure is complete and saved as a draft. It has not affected live users or data. It is **not ready to publish** because recipient routing, Reply-To behavior, tenant-response handling, testing scope, and PM escalation still require client decisions.

