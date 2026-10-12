# Seabend: Requirement Gathering Call #2 (Automation Opportunities)

| | |
|---|---|
| **Call date** | Tue, 6 Oct 2026 (taken from the recording file name `06102026`) |
| **Client** | Seabend, a property management company in Washington State (they work under the **RCW**, the Revised Code of Washington) |
| **Seabend attendees** | **Leila** (ran the call and shared her screen; she oversees the property managers) and **Steve** (listened only; maintenance isn't his area). **Tyler** was expected but didn't join. |
| **Our attendees** | Speaker 1 (opened the call), Speaker 5 (led discovery), **Nirav** (closed the call), and Speaker 3 |
| **Next call** | **Wed, 14 Oct 2026, 9:00 AM** (timezone TBC; Seabend is likely on Pacific time). Wednesday works because Steve is busy Mon–Tue with Tyler in town for annual meetings. |
| **Systems seen** | AppFolio (with Realm-X workflows), Outlook and Microsoft 365, Teams, OneDrive/Word, Adobe (PDF e-signing), ChatGPT (one shared "Seabend" profile), Asana (larger projects) |

> **⚠️ Ground rule:** Seabend gave us AppFolio access **to explore only**. The data is **live** (real tenants, real money).
> **Do NOT enable, activate, or test-run any Realm-X workflow, and do NOT send any email or text from AppFolio.**
> Build drafts only. Anything that would contact a tenant or change a ledger needs Seabend's explicit sign-off first.

---

## 1. TL;DR: What Seabend wants (priority order)

| # | Workflow | Status today | What they want | Priority | Likely build path |
|---|---|---|---|---|---|
| 1 | **Work order follow-ups** | Fully manual; this is their biggest time sink | Automatic check-ins with the **tenant** on day 3 and day 5, then escalation to the PM on day 7 | 🔴 **Highest** | Realm-X workflow (verify), or the API plus our own scheduler |
| 2 | **Legal notice drafting** (pay-or-vacate and similar) | Manual Word copy-paste, 5–15 notices per PM per month | PM gives a unit; AI pulls the ledger from AppFolio, fills the template, and saves a draft to OneDrive for review | 🔴 **High** ("would save an enormous amount of time") | AI (Claude/ChatGPT) + AppFolio API + OneDrive |
| 3 | **Daily inbox briefing email** | Not built; Leila likes the version we built for Tyler | An 8 AM summary **emailed** to her, plus Outlook folder sorting and drafted replies to tenants | 🔴 **High** | AI + Outlook/M365 (same pattern as Tyler's, but inbox only) |
| 4 | **Move-out reminder to on-site managers** | The move-out workflow exists and works; the reminder is missing | Email **and** text to the on-site manager about 1 week before the move-out inspection | 🟠 Medium | Extend Leila's existing Realm-X workflow |
| 5 | **Lease renewal and month-to-month (MTM)** | The reminder workflow exists (text, using AppFolio's template); the MTM rent change is manual | Review and improve the reminders; possibly automate the manual MTM charge entry | 🟠 Medium | Realm-X review; MTM charge entry via API (needs care, because it changes money) |
| 6 | **Weekly/monthly/seasonal PM task tracking** | Outlook calendar blocks plus Teams pings from Leila | Less chasing to confirm tasks got done | 🟡 Low (discovery) | TBD |
| 7 | **Asana (bigger projects)** | In use; no access yet | "Next place to optimize" after AppFolio | 🟡 Later | Waiting on access |
| 8 | **Seasonal bulk notices** | Bulk send already exists | Nothing needed ("pretty okay") | ⚪ None | n/a |
| 9 | **Weekly leasing report and rent survey** | **Already works** in ChatGPT | Maybe optimize later | ⚪ Later | n/a |
| 10 | **ChatGPT setup** (skills, connectors, per-user accounts) | One shared account, with the password shared among staff | Proper multi-user setup, private inboxes, saved prompts ("skills") | 🟠 Needed for #2 and #3 | Research the right subscription |

---

## 2. Glossary (domain terms from the call)

| Term | Meaning |
|---|---|
| **AppFolio** | Seabend's property management software, where PMs work "every day, all day" |
| **Realm-X** | AppFolio's built-in AI and low-code **workflow** builder. Leila has built a few workflows herself. |
| **Work order (WO)** | A maintenance request (for example, a broken fan or light, or fascia board that needs replacing) |
| **Reporter** | Whoever submitted the work order: usually the tenant, sometimes an on-site manager or PM |
| **Vendor** | Whoever the work order is assigned to. Often Seabend's **in-house maintenance** team. The transcript mentions "PDS", which may be a vendor or the in-house team's name (confirm). |
| **PM** | Property manager, who manages several properties remotely |
| **On-site manager / community manager** | A person physically at the property. They do inspections and post notices, and they are AppFolio users. |
| **Notice to vacate** | The tenant's notice that they're moving out, submitted via the tenant portal |
| **Move-out inspection** | A walkthrough of the unit after move-out, recorded in AppFolio's **Inspections** tab |
| **Tenant ledger / unpaid charges** | The tenant's account in AppFolio, showing what they owe, line by line |
| **Delinquency report** | The AppFolio report listing tenants who owe money |
| **Pay-or-vacate notice** | A legal notice: pay what's owed by the date given, or leave |
| **MTM** | Month-to-month. If a tenant doesn't renew, the lease rolls to MTM at a higher rent or with a fee. |
| **RCW** | Revised Code of Washington, the state law that governs notices, pets, and similar rules |
| **Rent survey / market comps** | A comparison of the unit's rent against similar nearby listings (Zillow) |

---

## 3. Workflow details

### 3.1 🔴 Work order follow-up automation (#1 priority)

**Why it matters:** Work orders are "the most time-consuming" part of the job. Volume is high, and vendors often run late without telling anyone, so PMs end up "babysitting" every open order.

**Current process:**
1. A tenant submits a request through the **tenant portal** (or an on-site manager or PM submits it after noticing something).
2. The PM opens the work order, edits it, and assigns it to a vendor (often the in-house maintenance team).
3. The PM has to **remember** to follow up with the vendor by email or by text from inside AppFolio.
4. Once the work is done, the PM marks the work order **Complete**.
5. PMs add **notes** to the work order as updates come in. Example from the call: *"The electrician went out and needs another unit ordered."*

**Pain points:**
- Remembering to follow up on every open work order. Some of the oldest open ones date back to **Sept 15**.
- Vendors don't report delays.
- After a note like "part ordered," nobody follows up on whether the part was ordered and installed.

**Key insight from Leila:** *Tenants respond fastest.* So the follow-up should go to the **tenant (reporter)**, not the vendor, which bypasses the vendor.

**Target rules (Leila's words, turned into rules):**
- Goal: **no work order open longer than 1 week** unless there's a known reason.
- **Non-urgent** work order:
  - **Day 3:** Follow up with the tenant: *"Has the vendor scheduled with you, or been out to take care of this?"*
  - **Day 5** (if no response): send a second follow-up.
  - **Day 7:** Still open, so it needs **personal attention**: the PM finds out why.
- If a **note is added** explaining a delay (for example, "part ordered"), **reset the clock**: follow up again **1 week from that note**.
- Channels: **text** (the phone number on the tenant's profile) and/or **email**.
- Only the tenant can confirm the work is done. Nothing else in the system confirms it.

**Example timeline:**

| Date | Event | What the automation does |
|---|---|---|
| Mon, Oct 5 | Tenant reports "kitchen light not working" (non-urgent); PM assigns it to in-house maintenance | Starts the clock |
| Thu, Oct 8 (day 3) | Still open | Texts and emails the tenant: *"Hi Jane, checking on your work order #1234 (kitchen light). Has someone been out to fix it? Reply YES if it's done or NO if not."* |
| Sat, Oct 10 (day 5) | No reply | Sends a second follow-up to the tenant |
| Mon, Oct 12 (day 7) | Still open | Alerts the PM: *"WO #1234 is 7 days old with no confirmation. Please check."* |
| Oct 12 | PM adds note: "Electrician says a new fixture must be ordered" | Resets the clock; next check-in Oct 19 asks whether it was ordered and installed |

**Open questions to answer (in AppFolio, or with Leila):**
- [ ] What are the rules for **urgent** work orders? (Only the non-urgent timing was discussed.)
- [ ] How does AppFolio flag urgent vs non-urgent? Is there a priority field?
- [ ] When a tenant replies "done," should the work order **auto-close**, or should the PM review it and close it? (Likely the PM reviews.)
- [ ] Who gets the day-7 escalation: the assigned PM, Leila, or both?
- [ ] What happens with work orders that have **no tenant**, such as those reported by staff, or for common areas and vacant units?
- [ ] Should weekends count toward the day count?
- [ ] Can Realm-X trigger on **"work order age"** and **"note added"**? If not, we need the API plus our own scheduler.

---

### 3.2 🔴 Legal notice drafting (pay-or-vacate and similar)

**Why it matters:** Leila said this "would save an enormous amount of time." Each PM sends **5–15 notices every month**.

**Notice types mentioned:** 30-day notice, 10-day notice, and a pay-or-vacate notice. The transcript says "13 day," which is **most likely Washington's 14-day pay-or-vacate notice; confirm**. Notices of the same type are identical except for the property, tenant, and amounts.

**Current process:**
1. The PM opens AppFolio, goes to the tenant's account, scrolls to **Unpaid Charges**, and reads the amounts.
2. The PM opens the community manager's OneDrive folder for the month (for example, `October 2026`).
3. The PM copies **last month's notice** for the same property (or the same tenant, if this is a repeat notice) so that some fields are already filled.
4. The PM manually updates the tenant name, property, unit, amounts per category, total, due date, and notice date.
5. The PM saves the Word document, then saves it as a **PDF**.
6. The PM **e-signs** the PDF in **Adobe** and saves it again.
7. The PM **emails** the notice to the **on-site manager**, who **physically posts** it on the unit.
8. The PM also **emails** the notice to the **tenant**.

**Worked example from the call** (how the ledger maps onto the notice):

| Ledger (Unpaid Charges) | Goes into the notice field | Value |
|---|---|---|
| Rent charge $1,595 + rent-related charge $79 | **Rent** | **$1,674** |
| 4 utility charges | **Utility reimbursement** (sum of the 4) | sum |
| $20 laundry charge | **Other** (with the date and description "Laundry") | $20 |
| (sum of all of the above) | **Total due** | total |

> ⚠️ Not every ledger line belongs on the notice. **Late fees** may not be legally includable. The PM must always review the numbers before signing.

**Target flow:**
1. The PM says: *"Canyon Ridge Unit 1 needs a 14-day notice."* (Or the system works from the delinquency report and drafts all notices at once.)
2. The AI looks up the tenant and their unpaid charges in AppFolio, groups them into Rent, Utilities, and Other, and fills in the right template.
3. The AI saves the draft to the correct OneDrive month folder.
4. The AI notifies the PM: *"Based on the delinquency report, 8 notices have been drafted and are ready for your review in /October 2026."*
5. The PM reviews the numbers, then signs and sends as usual (steps 5–8 above stay human for now).

**Requirements and risks:**
- **Unique identification is mandatory**: property **plus** unit (or tenant). We must never mix up two properties that have similarly named units.
- A human review step is mandatory, because these are legal documents.
- Templates differ by **property** (and by notice type). We need the full set.

**Open questions:**
- [ ] Get all notice templates (per type and per property) and the OneDrive folder structure.
- [ ] Which charge types are **excluded** (late fees? others?), and what are the RCW rules for each notice type?
- [ ] Can the AppFolio **API** (already provided) return unpaid charges per tenant? Or can Realm-X generate documents itself?
- [ ] Should the trigger be per unit on request, or a batch from the monthly delinquency report? (Both were mentioned.)
- [ ] Later phase: can we automate signing (Adobe Sign), emailing the on-site manager, and emailing the tenant?

---

### 3.3 🔴 Daily inbox briefing email (same idea as Tyler's)

**Why it matters:** PM inboxes are "bogged down" with high volume. Invoices get lost, and owner questions get buried. That leads to Teams back-and-forth like *"Did you see my email?"*

**How it differs from Tyler's version:** Tyler's briefing pulls from his **CRM, inbox, and Trello**. Leila wants **inbox only** to start ("keeping it simple").

**What the morning briefing should contain:**
- 🔴 **Owner questions**, always **top priority**. Owners must never have to chase a PM for an answer.
- **Tenant emails** since yesterday, with a count and a one-line summary of each complaint or issue.
- **Invoices**, with a count, plus an offer to move them to an invoices folder and mark or track them.
- Anything outstanding or high-priority.

**Delivery:** **An email at about 8 AM**, *not* a chat window. Leila is already in Outlook, so the briefing should arrive there (Tyler's is also emailed to him).

**Additional asks:**
- **Auto-sort** emails into Outlook **folders**: Invoices, Tenant issues, Owners, and so on.
- **Draft replies** to tenant emails, ready to review and send: *"You got 4 tenant emails since yesterday. Want draft replies?"*

**Example briefing:**
> **Good morning, Leila. Thursday, Oct 8**
> 🔴 **Owner questions (2):** Owner of Canyon Ridge asked about the roof repair timeline (received yesterday 4:12 PM, not yet answered)…
> **Tenant emails (4):** Unit 12, noise complaint · Unit 3, parking issue · … → draft replies are ready in your Drafts folder
> **Invoices (6):** moved to the **Invoices** folder (ABC Plumbing $480, …)

**Rollout:** Start with **Leila**, then replicate for each PM.

**⚠️ Blocker: account setup.** Seabend created **one shared ChatGPT account** and shared the password. If several inboxes connect to one account, **any user could ask it about someone else's inbox**. Seabend has its own Microsoft 365 tenant.
- [ ] **Our action:** Research the right setup (for example, a team or business plan with per-user seats, or Claude) so that each person's inbox stays private. Report back before the next call.

---

### 3.4 🟠 Move-out: reminder to on-site managers

**What already exists** (Leila built it in Realm-X, and it **works**):
1. The tenant submits a **notice to vacate** in the tenant portal, with the move-out date and reason.
2. The workflow automatically **emails the tenant** everything they need to know about the move-out process.
3. The workflow **creates the move-out inspection** in the **Inspections** tab, dated on the move-out date.
4. After move-out, the on-site manager does the walkthrough and fills in the inspection.

**What to add:** About **1 week before the inspection or move-out date**, send the **on-site manager** a reminder like this one:
> *"Unit 4B at Canyon Ridge is becoming vacant on Oct 31. The move-out inspection has already been created. Please schedule and complete your walkthrough on or by Oct 31."*
- Channels: **email and text**, both ("never hurts").
- Leila says they haven't had problems with people forgetting; she just wants to "cover all the bases."

**Also:** Leila describes her workflow as a "very basic outline." She wants it **optimized and made more thorough** in general.

**Example:** Notice is submitted for **Oct 31** → the inspection is created for **Oct 31** → the reminder goes to the on-site manager on **Oct 24**.

**Open questions:**
- [ ] How do we find the right on-site manager for each property in AppFolio?
- [ ] What else could the workflow do? For example: notify the PM, start a turnover or make-ready checklist, list the unit for leasing, or schedule a reminder to return the deposit. (Propose these; don't assume them.)

---

### 3.5 🟠 Lease renewal and month-to-month (MTM)

**Rules:**
- Seabend must send the renewal offer **at least 90 days** before the lease expires.
- The tenant must either **sign the renewal** or **give notice to vacate** at least **20 days** before the lease ends.
- If the tenant does neither, then on the **1st of the following month** the lease automatically becomes **month-to-month** at a **higher rent or with an added fee**. The notices along the way warn the tenant about this.

**What already exists:** A Realm-X workflow that **texts** tenants using AppFolio's provided template. Leila says it "seems to be working pretty well."

**What's still manual:**
- Chasing tenants by email and text after the 20-day deadline passes ("pound them").
- **The MTM conversion:** on the 2nd or 3rd of the month (depending on how much grace a tenant gets), staff pull a report of tenants who haven't signed. For each one, they open the tenant ledger, **add a new charge**, and **edit it to match the MTM amount**.

**Example timeline (lease ends Oct 31):**

| Date | Event |
|---|---|
| ~Aug 2 | Renewal offer sent (90 days' notice) |
| **Oct 11** | Deadline to sign or give notice to vacate (20 days before end) |
| Oct 12–31 | Tenant hasn't responded, so reminders go out by text and email (**today this is mostly manual**) |
| **Nov 1** | Lease becomes MTM automatically (higher rent or fee) |
| Nov 2–3 | **Manual:** pull the unsigned report and add the MTM charge to each tenant's ledger |

**Opportunities:**
- Review the existing workflow, then add escalating reminders between the deadline and lease end (by text **and** email).
- Automate the MTM step: generate the list of unsigned tenants and either prepare the charge changes for **PM approval** or apply them via the API. ⚠️ This changes money, so a human approves it.

**Open questions:**
- [ ] How is the MTM rent or fee calculated? Is it a fixed fee or a percentage, and does it vary per property?
- [ ] What's the grace-period rule? Who decides between the 2nd and the 3rd?
- [ ] Does the API or Realm-X allow creating or editing recurring charges?

---

### 3.6 🟡 PM task tracking (Outlook calendar plus Teams)

**Today:** Leila sends **calendar invites or blocks** to the PMs for anything with a deadline:
- **Daily items:** what every PM should touch every day. Leila doesn't check these unless she notices something missed.
- **Weekly items:** for example, the weekly leasing update to owners, and notices that must go out by a certain date.
- **Monthly and seasonal items.**

For weekly, monthly, and seasonal items, Leila **checks completion manually by pinging PMs in Teams**: *"Reminder, the leasing weekly needs to go out to owners today."*

**Our feedback:** It's already well organized. We asked whether she'd prefer a checklist app; she said **not for these items**.
**Status:** Discovery only. We'll revisit this after the AppFolio work. One idea: an automatic "was it done?" check instead of manual Teams pings.

---

### 3.7 🟡 Asana (bigger projects)

- Asana is used for **larger projects**. Leila couldn't figure out how to give us access yet and will keep working on it.
- She called it **"the next place we'd want to optimize"**, after AppFolio.

### 3.8 ⚪ Seasonal bulk notices

- Notices that go to an entire property. AppFolio already supports **bulk sending**, which is "pretty okay." Nothing is needed here.
- Leila mentioned an AppFolio **dashboard** they don't currently use that "might be something to utilize." We should look at it while exploring.

---

## 4. Already working with AI (don't rebuild; optimize later)

Seabend uses **one shared ChatGPT profile** called "Seabend," with several pinned projects:

| Use | How it works |
|---|---|
| **Weekly leasing report, rent survey, and market comps** | The PM and leasing agent fill in the report (traffic, leads, and so on). ChatGPT surveys **Zillow** for each vacant unit, pulls comparable nearby properties (price, days on market, amenities, finish, size), and adds the results to the report. The report goes **to owners every week** with price suggestions. This used to happen only when an owner asked "why isn't this unit renting?" Now owners get it proactively. PMs stay hands-on because "there is nuance with every unit." |
| **Tenant complaint responses** | Drafting replies |
| **RCW and legal Q&A** | A project for questions like "Can I legally serve this notice?" or "Can a tenant have 3 pets instead of 2 under the RCW?" |
| **Lease referencing** | A sample Seabend lease is uploaded. Staff ask, for example, "The tenant says we can't charge for cleaning at move-out. What section applies?" and get the relevant clause instead of reading the whole lease. |
| **General drafting** | Parking maps, responses, and other writing |

**What we suggested:** Use **connectors and plugins** (Outlook, OneDrive, and so on) and **skills**, meaning saved instructions invoked with a short command instead of pasting the same prompt every time. We'll demo skills on the next call, as we did for Tyler.

> **Our principle (stated on the call):** Success means people **actually use it daily**. Build simple and useful, not fancy.

---

## 5. Action items

### Ours
- [ ] **Email Leila** with (a) everything we need from Seabend and (b) **the email address to use for our AppFolio user**. She asked for this explicitly.
- [ ] **Explore AppFolio and Realm-X (read-only).** Check work orders, notes, inspections, tenant ledgers, the delinquency report, lease expirations, the communication tools (email and text), and the unused dashboard. **Enable nothing.**
- [ ] **Review Leila's existing workflows** (move-out and renewal) and list improvements.
- [ ] **Map Realm-X capabilities**: triggers (age, date, note added), actions (email, text, create inspection, update charges), and limits. Decide Realm-X vs API for each workflow.
- [ ] **Test the AppFolio API** (Seabend already provided it). Can it read unpaid charges per tenant, work orders and notes, and lease end dates? Can it write charges?
- [ ] **Research the ChatGPT or Claude setup** for multiple users with private inboxes, and recommend a subscription.
- [ ] **Prepare for the next call:** proposed solutions for #1–#5, plus a skills demo for the inbox briefing.
- [ ] Next call goal (Nirav): walk through **Leila's whole day** to find more bottlenecks.

### Seabend's
- [ ] **Leila:** Create our AppFolio user once we send the email address. She said she'd get it done the same day and may grant more access.
- [ ] **Leila:** Give us Asana access when she can.
- [ ] **Leila:** Share more use cases in depth as needed.
- [ ] **Leila:** Share the notice templates and OneDrive folder access (needed for #2; to be requested in our email).

---

## 6. Questions to ask on the Oct 14 call

1. Work orders: what are the urgent vs non-urgent rules, who gets escalations, should a tenant's "done" reply auto-close the order, and how are vacant or common-area orders handled?
2. Notices: what is the full list of types and templates, what's excluded (late fees), and should drafting be per unit or batched?
3. MTM: how are the rent and fee calculated, and what's the grace rule?
4. Is there a **test property or test tenant** in AppFolio that we could use to safely run workflows?
5. Inbox briefing: which folders, which senders count as "owners" or "vendors," and who goes first after Leila?
6. Which ChatGPT plan do they have now, and are they open to Claude or another plan?
7. What is "PDS"? (Is it the in-house maintenance team or a vendor?)

---

## 7. AppFolio exploration findings (9 Oct 2026, read-only, nothing changed)

**What our user (`Dev Chandan`) can see:** only the **flow run history** (`/realm/flows/runs`), which lists every run with its status and steps. Clicking **"Manage Realm-X Flows"** on the Dashboard just opens that same history page. **We can't reach the flow builder**, which most likely means our user lacks the permission for it.

**Seabend's existing flows (16):**
- Delinquency Outreach Kandice, Delinquency Outreach Katrina
- Move-Out Notice Submitted via Tenant Portal (Leila's)
- Renewal Follow-Up Before Lease Expires (Leila's)
- Accounting Performer - Invoice & Receipt Processing
- Review Campaign × 11 (one per property: Aspen, Canyon Ridge, Cedars & Daff, Clark Ave, Columbia, Mission View, Orchard, Pine Garden, Redondo, Rio Vista, Samish)
- **No work order flow exists yet.**

**Step types we saw in real runs:**

| Step | Seen in | Use for the work order flow |
|---|---|---|
| `Wait for N days` | Delinquency outreach ("Wait for 5 days") | ✅ The day-3, day-5 and day-7 waits |
| `Email Tenant` | Move-out flow | ✅ Check-in messages to the tenant |
| `Create Inspection for Unit` | Move-out flow | n/a |
| `Interrupt: <condition>` | Delinquency ("Interrupt: Delinquent Balance Fully Paid" ends the flow early) | ✅ **Stops the flow when the work order is completed**, which is exactly the exit rule we need |
| Text tenant | Leila said on the call that the renewal flow sends texts (we didn't open a run) | ✅ Text check-ins |

**So Realm-X can likely build the v1 work order flow:** Trigger (work order assigned) → Wait 3d → Email/Text tenant → Wait 2d → Email/Text tenant → Wait 2d → Notify PM, with **Interrupt: work order completed**.

**Still unknown:**
- Is there a work order trigger? Which ones exist?
- Is there a "notify staff/PM" action?
- **Is a new flow inactive until it's switched on?** This decides whether we can safely build it as a draft.

**Next step:** Ask Leila either to (a) grant our user permission to *build and edit* Realm-X Flows, or (b) share her screen on the 14 Oct call so we build it together and leave it **off**. Don't use the Realm-X Assistant's prompt-to-workflow feature for this, because the Assistant can take live actions (bulk email or text).

> Note: our AppFolio user shows times in GMT+5:30 (IST). Seabend's times are Pacific, so keep that in mind when reading run times or setting schedules.

---

*Notes: The transcript was auto-generated, so some words are garbled. Uncertain items are marked "confirm." The client's name appears as both Leila and Layla; please confirm the spelling. Speaker numbers were mapped to names from context.*
