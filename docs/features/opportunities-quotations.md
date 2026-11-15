# Opportunities & quotations

The commercial side of a job, before it becomes a project: a **Client** you are selling to, an **Opportunity** you are trying to win, one or more **Quotations** you offer, and — once one is accepted — the **Project** that carries the work. Client and Supplier records use separate categories; see [Suppliers, clients and addresses](suppliers-clients.md).

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
| Payment Terms / Delivery Terms | The commercial conditions; Delivery Terms uses the seeded Incoterms 2020 dropdown |
| Sales Currency | The currency of the whole quotation |

The quotation's **name is its number**, allocated automatically — `Q2026 0001`.

### Lines

Each line carries a description, a **Quantity** and a **Unit Price**. **Line Total** is calculated for you and cannot be typed.

Lines can be added by hand or picked from the Library, so a quotation can quote catalogue items directly.

### Price Lists

Where the quotation's **Pricing** tab is configured, choose a [Price List](price-lists.md). The quotation date selects an enabled period, and a new line copies its item's price from that period. The period currency must match the quotation currency. If the item has no price in that period, the line is unpriced rather than using an older price.

Changing the Price List later does not silently rewrite saved lines. On an editable quotation, deliberately saving a different list or date refreshes existing line prices. Review the refreshed lines before issuing the quotation.

### Revising a quotation

Quotations move **Draft → Issued**, then **Accepted**, **Declined** or **Expired**.

There is deliberately no way back from Issued to Draft. The customer is holding a copy of the document, so changing it means a **new revision** — B, C, and so on — against the number they already have. Editing an issued quotation in place would leave two different papers claiming to be the same quotation.

*Expired* is kept separate from *Declined* on purpose: "they said no" and "we never heard" are different outcomes, and a pipeline that treated them as one could not tell them apart.

---

## Creating an output from an accepted quotation

An accepted quotation can trigger an ordinary stored automation on its lifecycle transition. The script chooses an enabled Folder- or Occurrence-family category and a fixed Explorer landing folder, then calls the controlled generic **Create Entity** action.

The output can therefore be a Project, Sales Order, Spare-Part Order or Service Order. The automation does not assume that every accepted quotation needs a Project.

The created entity receives a pinned **Generated From** link to the exact source version. The entity, its Explorer placement and that link are written in one transaction.

The action refuses rather than guesses when:

- the configured category is missing, disabled or not an Explorer category;
- the configured landing folder is missing or belongs to another workspace;
- the output name is blank;
- the exact source version no longer exists.

Refreshing does not create another output. Automation retry protection uses the lifecycle event identifier. An administrator can deliberately use the configured Accepted → Draft recovery transition, delete a test output, and accept again; that is a new event and produces a replacement.

---

## What a company shows

The party configuration provides tabs listing what points at a record: **Opportunities**, **Quotations**, **Projects** and **Purchase Orders**, where the corresponding features are installed. Supplier and Client categories start from this shared configuration and can be configured independently. Contacts are a Uses tab because a contact person sits *under* the party rather than referring to it.

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

### Importing Outlook email

Instead of retyping an email as an activity, file the message itself. On an **Activities** tab, click **Import Email** in the ribbon (the button is shown when you can edit the tab, and administrators can place or hide it with the tab's [ribbon configuration](../admin/display-tabs.md)).

1. In the **Import Outlook email** dialog, click to choose one or more `.msg` (Outlook) or `.eml` (Internet email) files. You can select up to 100 files at once; one file may be at most 100 MB and a batch at most 500 MB.
2. Click **Import email**. Each file is listed with its result: **imported**, **already filed**, or the reason it failed.
3. Close the dialog. The messages appear on the tab.

How filed email behaves:

- **Replies are grouped into one conversation.** The messages of a thread are shown under a collapsible conversation row that counts its messages. To move an activity or message into a conversation yourself, select it and use **Group email** if your tab's ribbon offers that action.
- **A message is filed once.** MatStream recognises a message by its email Message-ID, so importing the same message again reports **already filed** and creates no duplicate.
- **Open a message in place.** Opening an imported email shows a **View email** dialog with the sender and recipients, the sent date, the subject, the attachment names and the body text.
- A failed file does not stop the others. Fix or remove the failing file and import it again.

Importing needs the **File upload**, **Entity create** and **Entity update** grants. See [Group grants](../admin/group-grants-dialog.md).

### The timeline

The timeline for an item merges two things: activities somebody logged, and events the system recorded — moved to Issued, timesheet approved, file checked in — newest first.

They are kept apart in storage on purpose. A logged activity is testimony: somebody chose what it says, and can edit it. A system event is evidence: written as part of the thing it describes, and editable by nobody.

An event type the timeline has never seen still appears, described from its own name, rather than being silently left out. A gap in a history is worse than an untidy line in one.

> **Note:** The timeline is available through the API; it is not yet on an item's tabs.

---

## Permissions

| Grant | Effect |
|---|---|
| Entity create | Manually create opportunities, quotations and activities |
| Entity update | Edit them |
| Entity state change | Move an opportunity through the pipeline, or issue and settle a quotation |

Category and lifecycle-state permissions apply as they do to any other item. For a lifecycle automation, the user must be allowed to perform the exact transition. The configured automation then runs through its narrow action API; it does not give that user general permission to create arbitrary Projects or orders.

---

## Known limitations

- **No print template for quotations.** The field mapping is ready; what is missing is a Word document with the merge fields in it. Until one exists, a quotation cannot be printed or sent as a PDF from MatStream.
- Financial posting is not part of project creation. Configured field mappings can populate the new project through the acceptance script, as described below.

## Automatically copy quotation fields to the project

In **Configuration → Automations**, open **Create Project from Quotation**. The editable example uses `FieldMappings` parameters to copy fields into the new Project. New configuration seeds mappings for Client, Customer Contact, Description and Currency where available; Client and contact are required by the example. Administrators can change these choices.

Use **Created entity field mappings** beneath Parameters to select source/target properties, optional fallback defaults and **Required**. If needed, choose **Configure created entity fields** first. An empty source uses its configured default; without a default, optional mappings leave the target's initial value alone and required mappings stop creation. Defaults can also be used without a source. Choose only targets assigned to the Project category. Field identities survive renaming.

For an existing automation, the editor's API Reference contains **Quotation to Project example**. Use that script deliberately when updating an older installation; existing administrator-authored scripts are not silently replaced. Configure the target category/folder and field mappings before enabling it. The example creates the project, its mapped properties and the exact source-version link together. The editor's Run action is a mock preview, not a real quotation test.

Transition-specific parameter overrides take precedence over the library script's parameters. Review existing overrides if library mapping edits seem ineffective. Fresh configuration inherits the script parameters rather than seeding a duplicate override.

The script can read a property by `pd_<PropertyDefID>`. With `FieldMappings` configured, source values are read from the triggering quotation version. A mapping failure creates no partially populated project, but the quotation transition may already have succeeded: inspect automation run history and the project result before retrying. Deliberately accepting again is a new event, not a retry of the original acceptance.

Do not map quotation validity to delivery date or sales price to project budget unless that is explicitly your business policy. Assembly placement remains a separate step.
- **The timeline has no tab**, only an API call.
- Sales currency is a per-item field. There is no workspace base currency and no conversion, so a workspace quoting in more than one currency must total each separately.
