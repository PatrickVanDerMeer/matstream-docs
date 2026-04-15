Here's the draft for solutions/quality-management.md:
markdown# Quality Management

MatStream provides a flexible foundation for managing quality records — 
non-conformances, corrective actions, audit findings, customer complaints, 
and any other quality-related item your organisation needs to track, 
approve, and resolve.

Because quality records are entities in MatStream, they are fully 
integrated with the rest of your data — linked directly to the specific 
part revisions, documents, suppliers, or processes they relate to.

---

## What is quality management in MatStream?

Quality management in MatStream means treating quality records as 
first-class entities — with the same structured properties, lifecycle 
control, revision history, and relationship tracking as any other item 
in the platform.

This is what sets MatStream apart from standalone quality management 
systems: your non-conformance is not an isolated record in a separate 
tool — it is linked directly to the exact part revision it affects, 
the supplier who supplied it, the work instruction that was not followed, 
and the corrective action raised to address it.

Everything is connected. Everything is traceable.

---

## Typical quality entity types

Your administrator configures the quality entity types that match your 
organisation's quality management process. Common examples include:

| Category | What it represents |
|---|---|
| **Non-conformance (NCR)** | A record of a product or process that does not meet requirements |
| **Corrective action (CAPA)** | A planned or completed action to address the root cause of a non-conformance |
| **Audit finding** | An observation or finding raised during an internal or external audit |
| **Customer complaint** | A complaint received from a customer, linked to the affected product |
| **Supplier deviation** | A formal request to accept material or parts outside specification |
| **Inspection record** | The result of an incoming, in-process, or final inspection |

Each category carries the properties, lifecycle, and numbering scheme 
appropriate to that record type — all configured by your administrator.

---

## Non-conformance management

A non-conformance (NCR) is the most common quality record type. In 
MatStream an NCR is an entity that captures:

- What the non-conformance is — description, detection date, detection 
  method
- Where it was found — incoming inspection, in-process, final inspection, 
  customer return
- What it affects — linked directly to the specific part revision, 
  batch, or assembly
- Who is responsible — assigned to a user or group for disposition
- How it was resolved — disposition decision, linked corrective action

### NCR lifecycle

A typical NCR lifecycle in MatStream:
Open → Under investigation → Disposition → Closed
↓                     ↓
Escalated            Rejected → Rework required

At each transition:
- Only authorised groups can trigger the transition
- The transition is permanently logged
- Lifecycle state permissions control who can edit the record

### Linking NCRs to affected items

The most powerful aspect of NCR management in MatStream is the ability 
to link the NCR directly to the entities it affects:

- **Affected part revision** — the exact revision of the part that 
  failed, not just the part number
- **Affected assembly** — the assembly that contained the non-conforming 
  part
- **Supplier** — the supplier who provided the non-conforming material
- **Work instruction** — the procedure that was not followed
- **Corrective action** — the CAPA raised to address the root cause

These links are bidirectional. From the part record, you can instantly 
see all NCRs ever raised against any revision of that part — giving 
you a complete quality history for every item in your catalogue.

---

## Corrective and preventive actions (CAPA)

A corrective action (CAPA) is an entity in MatStream that captures:

- The root cause of the problem being addressed
- The planned corrective action and target date
- Who is responsible for implementing the action
- Evidence of completion — attached files, linked records
- Effectiveness review — was the action effective?

### CAPA lifecycle

A typical CAPA lifecycle:
Draft → In progress → Pending review → Closed — Effective
↓
Closed — Ineffective → New CAPA required

CAPAs are linked to the NCRs that triggered them — giving you full 
traceability from problem detection through root cause analysis to 
resolution and effectiveness review.

---

## Audit management

Audit findings are entities in MatStream — linked to the audit they 
belong to, the process or document they relate to, and any corrective 
actions raised as a result.

A typical audit finding entity might carry:

- Finding type (observation, minor non-conformance, major non-conformance)
- Reference standard clause
- Description of the finding
- Linked process or document
- Required corrective action — linked to a CAPA entity
- Target closure date and actual closure date

Audit entities can also represent the audit itself — with findings 
linked as child entities, giving you a complete audit record in one place.

---

## Customer complaint management

Customer complaints are entities in MatStream — linked to the affected 
product, the customer, and any NCRs or CAPAs raised in response.

A typical customer complaint workflow:

Complaint received and logged as an entity
→ Number assigned automatically: CC-2026-0034
→ Linked to the affected product entity
→ Linked to the customer entity
Initial assessment completed
→ Severity and priority properties filled in
→ Assigned to responsible engineer
Investigation completed
→ Root cause identified
→ NCR raised if a product non-conformance is confirmed
→ CAPA raised if a systemic issue is identified
Customer response issued
→ Response document attached as a file
→ Lifecycle transitions to Resolved
Complaint closed
→ Effectiveness of resolution confirmed
→ Lifecycle transitions to Closed


---

## Supplier quality management

Suppliers can be entities in MatStream — linked to the purchased parts 
they supply, the NCRs raised against their components, and any supplier 
deviation requests submitted to them.

This gives you a complete supplier quality record:

- All purchased parts supplied by this supplier
- All NCRs raised against components from this supplier
- All approved deviations granted to this supplier
- Supplier performance visible across the entire catalogue

---

## Traceability across the quality system

The real power of quality management in MatStream is cross-domain 
traceability — the ability to follow a thread from any point in the 
quality system to any other:
Customer complaint
└── Linked to affected product revision
└── Linked to non-conformance (NCR)
└── Linked to affected part revision
└── Linked to corrective action (CAPA)
└── Linked to updated work instruction
└── Linked to parts the instruction applies to

Every link is bidirectional and permanent. At any point you can navigate 
the full chain — from complaint to corrective action to updated procedure 
to affected parts — without leaving MatStream.

---

## Quality records and document control

Quality management and document control work naturally together in 
MatStream. Quality records can be linked directly to the controlled 
documents they relate to:

- An NCR linked to the work instruction that was not followed
- A CAPA linked to the updated procedure that implements the fix
- An audit finding linked to the quality manual clause it references

When a procedure is revised as a result of a corrective action, the 
link between the CAPA and the new document revision is maintained — 
giving you a complete corrective action closure record.

See [Document Control](document-control.md) for more details.

---

## Satisfying QMS requirements

MatStream's quality management capability is designed to support the 
requirements of quality management standards such as ISO 9001. 
Specifically:

| QMS requirement | How MatStream satisfies it |
|---|---|
| Non-conformances must be recorded and controlled | NCR entities with lifecycle control and audit trail |
| Root causes must be identified and addressed | CAPA entities linked to NCRs with effectiveness review |
| Corrective actions must be tracked to closure | CAPA lifecycle with closure state and linked evidence |
| Customer complaints must be managed | Customer complaint entities with full traceability |
| Records must be retained | Immutable version history and permanent transition log |
| Traceability must be maintained | Bidirectional links across all quality record types |

!!! note
    MatStream provides the technical foundation for quality management 
    system compliance. Whether it satisfies your specific regulatory 
    requirements depends on your industry, jurisdiction, and the way 
    you configure and use the platform. Consult your quality manager 
    or compliance advisor for guidance.

---

## Who uses quality management in MatStream?

| Role | How they use MatStream |
|---|---|
| **Quality engineer** | Creates and manages NCRs, CAPAs, and audit findings |
| **Production engineer** | Raises NCRs for in-process non-conformances |
| **Supplier quality manager** | Manages supplier NCRs and deviation requests |
| **Quality manager** | Reviews and approves dispositions, monitors open actions |
| **Management** | Views quality performance data and open action status |
| **Administrator** | Configures quality entity categories and lifecycles |

---

## Getting started with quality management in MatStream

1. **Configure quality categories** — define your NCR, CAPA, and other 
   quality record types. See [Categories](../admin/categories.md).
2. **Configure lifecycle definitions** — define your quality approval 
   workflows. See [Lifecycle](../entities/lifecycle.md).
3. **Configure numbering schemes** — define your quality record numbering 
   templates. See [Numbering schemes](../admin/numbering.md).
4. **Set up user groups** — define who can raise, review, and close 
   quality records. See [Users & groups](../admin/users-groups.md).
5. **Link to your engineering data** — connect quality records to the 
   parts, documents, and suppliers they relate to.

---

## Next steps

- [Inventory Management](inventory-management.md) — manage products, 
  stock, and supplier data alongside your quality records
- [Document Control](document-control.md) — manage controlled documents 
  linked to your quality records
- [PLM](plm.md) — manage engineering parts and assemblies
- [Core concepts](../platform/core-concepts.md) — the building blocks 
  of the platform