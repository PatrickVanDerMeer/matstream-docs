# Document Control

MatStream provides a robust foundation for document control — managing 
controlled documents with formal revision history, approval workflows, 
and organisation-wide visibility. Because documents are entities in 
MatStream, they benefit from exactly the same versioning, lifecycle, 
and relationship capabilities as any other item in the platform.

---

## What is document control?

Document control is the discipline of managing documents that are 
subject to formal approval, revision tracking, and access control. 
Typical controlled documents include:

- Work instructions and standard operating procedures (SOPs)
- Quality manuals and quality plans
- Engineering specifications and standards
- Test procedures and validation protocols
- Safety data sheets and regulatory documents
- Drawings and technical references

The key requirements of document control are:

- Every document has a formal revision history
- Changes must go through an approval process before release
- The current approved version is always clearly identified
- Superseded versions are retained but clearly marked as obsolete
- Access to documents is controlled by role

MatStream meets all of these requirements out of the box — configured 
to match your process, not a vendor-defined template.

## Install the worked example

The Setup Wizard can install the full model and an optional sample in one pass:

1. Open the workspace in **MatStream Manage** and go to **Setup**.
2. Select **Document Control Configuration**.
3. Leave **Document Control Sample Project** selected for the worked
   `P-2026-0042 - Cooling Water Pump Skid` scenario.
4. Review the selection and click **Apply**.

The configuration creates controlled documents, outgoing transmittals,
incoming submittals, document issue lines, three lifecycles, numbering and
revision schemes, print templates, and operational worklists.

---

## How MatStream handles document control

### Documents as entities

In MatStream, a controlled document is an entity — just like a part or 
assembly. Your administrator configures one or more **document 
categories** with the properties relevant to your document types. 
For example:

| Category | Typical properties |
|---|---|
| **Work instruction** | Process area, revision date, effective date, owner |
| **Quality manual** | Standard reference, scope, approval authority |
| **Engineering specification** | Applicable parts, standard, issue date |
| **Test procedure** | Test type, applicable product, equipment required |
| **Safety data sheet** | Chemical name, hazard classification, supplier |

Each category has its own lifecycle definition, revision scheme, and 
numbering scheme — configured independently to match the approval 
process for that document type.

### Revision control

Every controlled document in MatStream has a formal revision history. 
When a document is approved and released, MatStream assigns a permanent 
revision label — for example:

- Issue 1, Issue 2, Issue 3 (numeric scheme)
- Rev A, Rev B, Rev C (alphabetic scheme)

The revision label is immutable once assigned. Superseded revisions 
are retained in full — you can always view the exact content and 
approval history of any previous issue.

### Transmittals and submittals

A **Transmittal** records documents sent to work preparation, a client,
third party or supplier. A **Submittal** records an incoming package expected
from a supplier or third party. Both use **Document Issue Line** occurrences as
their contents.

When a transmittal moves from *Ready to Issue* to *Issued*, or a submittal from
*Expected* to *Received*, MatStream pins every line's definition link to its
current document version. If the document later advances from revision A to B,
the historic package continues to open and print A. This is the core audit
boundary: current library content may change, an issued package may not.

Package properties record the recipient, distribution channel, purpose, issue
date, response due date, external reference, internal owner and response
status. Live worklists expose open packages, expected and overdue responses,
and rejected lines requiring a new document revision.

### Approval workflows

Documents move through a configurable lifecycle. A typical document 
control lifecycle might look like:
Draft → In Review → Approved → Active → Superseded
↓
Rejected → Draft (rework required)

At each transition:
- Only authorised user groups can trigger the transition
- The transition is permanently logged with who approved and when
- Lifecycle state permissions prevent editing of approved documents

This gives you a fully auditable approval process that satisfies 
quality management system (QMS) requirements.

### File attachments

The actual document content — whether a Word file, PDF, or other 
format — is attached to the entity version as a file. MatStream:

- Links the file to the specific revision it belongs to
- Retains previous revision files permanently
- Displays PDF documents in the built-in PDF viewer without leaving 
  MatStream
- Tracks who uploaded each file and when

When a new revision is created, the previous file is preserved and 
a new file can be attached for the updated content.

### Linking documents to other entities

One of MatStream's most powerful document control capabilities is the 
ability to link documents to other entities. For example:

- A **work instruction** linked to the **process** it supports
- An **engineering specification** linked to the **parts** it applies to
- A **test procedure** linked to the **assembly** it validates
- A **safety data sheet** linked to the **raw material** it describes

These links are bidirectional — the part knows which specifications 
apply to it, and the specification knows which parts it covers. When 
a part is revised, you can instantly see which documents may need 
to be reviewed via the where-used relationship.

---

## Document numbering

MatStream automatically generates unique document numbers from 
configurable numbering schemes. For example:

| Document type | Numbering scheme | Example |
|---|---|---|
| Work instruction | `WI-{YYYY}-{NNN}` | WI-2026-001 |
| Quality manual | `QM-{NNN}` | QM-042 |
| Engineering spec | `ES-{YYYY}-{NNN}` | ES-2026-018 |
| Test procedure | `TP-{NNN}` | TP-007 |

No manual numbering. No risk of duplicate document numbers. Numbers 
are assigned automatically on creation.

---

## Access control

Document access in MatStream is controlled at multiple levels:

| Level | What it controls |
|---|---|
| **Group permissions** | Which user groups can view, edit, or approve documents |
| **Lifecycle state permissions** | What users can do based on the document's current state |
| **Property permissions** | Which groups can see sensitive fields |
| **Tab permissions** | Which groups can see specific sections of the document record |

For example, draft documents might be visible only to the authoring 
team, while approved documents are visible to the whole organisation.

---

## Satisfying QMS requirements

MatStream's document control capability is designed to satisfy the 
document control requirements found in quality management standards 
such as ISO 9001. Specifically:

| QMS requirement | How MatStream satisfies it |
|---|---|
| Documents must be approved before use | Lifecycle approval workflow with transition permissions |
| Current version must be identifiable | Revision label and lifecycle state clearly shown |
| Changes must be reviewed and approved | New revision goes through the same approval workflow |
| Obsolete documents must be retained | Superseded revisions are permanently retained |
| Access must be controlled | Role-based permissions at multiple levels |
| Changes must be traceable | Full transition history and version audit trail |

!!! note
    MatStream provides the technical foundation for document control 
    compliance. Whether it satisfies your specific regulatory 
    requirements depends on your industry, jurisdiction, and the 
    way you configure and use the platform. Consult your quality 
    manager or compliance advisor for guidance.

---

## A typical document control workflow in MatStream

Author creates a new Work Instruction entity
→ Number assigned automatically: WI-2026-047
→ Entity starts in Draft state
Author fills in properties
→ Process area, owner, effective date
Author attaches the document file
→ Word or PDF uploaded and linked to this version
Author links the work instruction to related entities
→ Linked to the assembly process it supports
Author submits for review
→ Lifecycle transitions to In Review
→ Transition logged: who submitted and when
Reviewer approves the document
→ Lifecycle transitions to Approved
→ Transition logged: who approved and when
Document goes live
→ Lifecycle transitions to Active
→ Revision Issue 1 assigned — permanent and immutable
→ Document becomes read-only to most users
One year later — process changes require an update
→ New revision created — starts in Draft
→ Author updates content, attaches new file
→ Goes through approval again
→ Released as Issue 2
→ Issue 1 automatically moves to Superseded — retained for reference


---

## Who uses document control in MatStream?

| Role | How they use MatStream |
|---|---|
| **Document author** | Creates and edits documents, attaches files, submits for review |
| **Reviewer** | Reviews document content and approves or rejects transitions |
| **Document controller** | Manages the document register, monitors approval status |
| **Quality manager** | Configures document categories, lifecycles, and access controls |
| **All staff** | Read-only access to current approved documents |

---

## Getting started with document control in MatStream

1. **Configure document categories** — define your document types with 
   the properties your process requires. See [Categories](../admin/categories.md).
2. **Configure lifecycle definitions** — define your document approval 
   workflow. See [Lifecycle](../entities/lifecycle.md).
3. **Configure numbering schemes** — define your document numbering 
   templates. See [Numbering schemes](../admin/numbering.md).
4. **Set up user groups** — define who can author, review, and approve. 
   See [Users & groups](../admin/users-groups.md).
5. **Create your first document** — follow the [Quick start](../getting-started/quick-start.md) 
   guide for a step-by-step walkthrough.

---

## Next steps

- [Quality Management](quality-management.md) — link quality records 
  to controlled documents and part revisions
- [PLM](plm.md) — manage engineering parts and assemblies alongside 
  your controlled documents
- [Core concepts](../platform/core-concepts.md) — the building blocks 
  of the platform
