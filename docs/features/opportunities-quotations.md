# Opportunities & quotations

The commercial side of a job, before it becomes a project: a **Company** you are selling to, an **Opportunity** you are trying to win, one or more **Quotations** you offer, and — once one is accepted — the **Project** that carries the work.

Everything here is ordinary MatStream configuration. An opportunity is a folder with properties and a lifecycle, a quotation is a numbered item with lines and revisions, and both get their tabs and views from mechanisms the rest of the workspace already uses.

---

## Opportunities

An opportunity is one pursuit: one client, one thing you are trying to win. Quotations, documents, tasks, contacts and activities all hang underneath it.

| Field | Meaning |
|---|---|
| Client | The company you are selling to |
| Customer Contact | The person you are dealing with there |
| Opportunity Owner | Who in your team is pursuing it |
| Opportunity Source | How it arrived — referral, tender, existing customer |
| Estimated Value | What you think it is worth |
| Probability % | How likely you think it is |
| Weighted Revenue | Estimated Value × Probability, calculated for you |
| Expected Close Date | When you expect to know |
| Sales Currency | The currency the whole pursuit is quoted in |

Opportunities are numbered automatically — `OPP2026 0001` and upward.

### The pipeline

Opportunities move **Qualifying → Proposal → Negotiation → Won** or **Lost**, with **On Hold** available from either middle stage.

Two things about that lifecycle are deliberate:

- **You can record a loss from any live stage.** Most opportunities die before anyone negotiates, and a pipeline that could only record a loss at the last stage would overstate every earlier one.
- **You can step back.** Proposal returns to Qualifying, Negotiation to Proposal. Sales does not move in one direction, and a lifecycle that pretends otherwise gets worked around by leaving everything in Proposal.

### Open Opportunities

The **Sales** folder contains an **Open Opportunities** view — everything that is neither Won nor Lost, with owner, probability, value, weighted revenue and expected close date. It is a live view rather than a saved report, so it always shows what is true now.

---

## Quotations

A quotation is what you offered, at what price, until when. It sits under the opportunity it was offered for.

| Field | Meaning |
|---|---|
| Client / Customer Contact | Who it is addressed to |
| Opportunity | The pursuit it belongs to |
| Valid Until | When the offer expires |
| Payment Terms / Delivery Terms | The commercial conditions |
| Sales Currency | The currency of the whole quotation |

The quotation's **name is its number**, allocated automatically — `Q2026 0001`.

### Lines

Each line carries a description, a **Quantity** and a **Unit Price**. **Line Total** is calculated for you and cannot be typed.

Lines can be added by hand or picked from the Library, so a quotation can quote catalogue items directly.

### Revising a quotation

Quotations move **Draft → Issued**, then **Accepted**, **Declined** or **Expired**.

There is deliberately no way back from Issued to Draft. The customer is holding a copy of the document, so changing it means a **new revision** — B, C, and so on — against the number they already have. Editing an issued quotation in place would leave two different papers claiming to be the same quotation.

*Expired* is kept separate from *Declined* on purpose: "they said no" and "we never heard" are different outcomes, and a pipeline that treated them as one could not tell them apart.

---

## Turning an accepted quotation into a project

An accepted quotation becomes a project, carrying its client, contact, currency and contract value across.

The new project records **which version of the quotation it was sold on** — the number, the revision letter, and the exact version. That matters because quotations get revised after they are won, and a project that simply pointed at "the quotation" would silently start describing a document nobody agreed to.

The conversion refuses rather than guesses:

- The quotation must be **Accepted**.
- It must have lines.
- **Every line must have a Line Total.** An unpriced line stops the conversion and names itself, because a contract value that quietly omits the lines somebody had not finished is too low and looks complete.
- The total cannot be negative.

Converting the same quotation twice returns the project that was already made rather than a second one. Converting a *later revision* does make a new project — a re-accepted revision is a different sale.

> **Note:** This runs through the API today; there is no button for it yet.

---

## What a company shows

A **Company** gains four tabs listing what points at it: **Opportunities**, **Quotations**, **Projects** and **Purchase Orders**. Contacts are already a Uses tab, because a contact person sits *under* the company rather than referring to it.

---

## Activities & the timeline

An **Activity** is something a person did about a thing: a call, an email, a meeting, a site visit, a note. Activities tabs appear on opportunities, companies and projects.

| Field | Meaning |
|---|---|
| Activity Type | Call, Email, Meeting, Site Visit or Note |
| Activity Date | When it **happened** — not when you typed it |
| Contact | Who it was with |
| Activity Outcome | What came of it |
| Duration (minutes) | Optional |

Activities are log entries: they have no versions and no lifecycle, because they happened and there is nothing for them to progress through.

### The timeline

The timeline for an item merges two things: activities somebody logged, and events the system recorded — moved to Issued, timesheet approved, file checked in — newest first.

They are kept apart in storage on purpose. A logged activity is testimony: somebody chose what it says, and can edit it. A system event is evidence: written as part of the thing it describes, and editable by nobody.

An event type the timeline has never seen still appears, described from its own name, rather than being silently left out. A gap in a history is worse than an untidy line in one.

> **Note:** The timeline is available through the API; it is not yet on an item's tabs.

---

## Permissions

| Grant | Effect |
|---|---|
| Entity create | Create opportunities, quotations and activities; convert an accepted quotation into a project |
| Entity update | Edit them |
| Entity state change | Move an opportunity through the pipeline, or issue and settle a quotation |

Category and lifecycle-state permissions apply as they do to any other item — a workspace can restrict who edits a quotation once it is Issued in the ordinary way.

---

## Known limitations

- **No print template for quotations.** The field mapping is ready; what is missing is a Word document with the merge fields in it. Until one exists, a quotation cannot be printed or sent as a PDF from MatStream.
- **Quotation → project has no button**, only an API call.
- **The timeline has no tab**, only an API call.
- Sales currency is a per-item field. There is no workspace base currency and no conversion, so a workspace quoting in more than one currency must total each separately.
