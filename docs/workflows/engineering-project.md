# Engineering Project Workflow

!!! warning "Workflow plan — proposed process"
    This plan includes configuration and development still needed. It does not claim every action or automation is already available. The capability assessment is based on repository inspection on 14 November 2026; the target workspace still needs end-to-end validation.

Start with a customer quotation and finish with manufactured, tested and documented assemblies. Keep quotation, project, BOM, approvals, transmittals, purchases, required vendor documents (RVDs), payment milestones and quality exceptions traceable.

The first practical step is now documented with a local workflow test and screenshots: [Sales — Quotation and project](engineering-quotation-project.md). The next tested steps are [Engineering — Add the assembly](engineering-add-assembly.md) and [Engineering — Select transmittal documents](engineering-project-documents.md). The next tested step is [Engineering — Generate transmittal PDF](engineering-transmittal-pdf.md). [Engineering — Share the issued transmittal](engineering-transmittal-portal.md) now covers fixed package portal access. [Client — Review and return commented PDFs](engineering-transmittal-review.md) now covers approval records and returned files. [Engineering — Resolve client comments](engineering-comment-resolution.md) adds ownership, checking and traceability to revised drawings and replacement issues. [Engineering — Update and reissue drawings](engineering-reissue-drawings.md) now records the local revision B reissue to client and Work Preparation, including the gaps found. The project drawing revision action has since been fixed and tested with administrator-controlled revision schemes. [Select the transmittal’s project](engineering-transmittal-project-reference.md) now covers reusable references without copying unique project numbers, including administrator settings and the PDF test. Later stages below remain a plan until exercised in the workflow.

[Purchasing — Select a project supplier and agree terms](engineering-supplier-selection.md) documents the local motor and pump agreements, PO creation. [Purchasing — Hand ordered requirements to project procurement](engineering-procurement-handover.md) now covers the corrected lifecycle switch and successful motor/pump retest.

## Worked example

The two supplied drawings are revision 0 and represent one system with a manufactured child assembly:

| Item | Role | Quantity |
|---|---|---|
| ITM-00000 | Lube Oil System Piping; top-level assembly | 1 pc |
| ITM-00006 | Lube Oil Header; system BOM position 1 | 1 pc |
| ITM-00012 | Oil Pump with Filterset, 4kW; long-lead purchase, position 2 | 1 pc |
| ITM-00021 | Electric Motor 3Ph, 4kW 4Pole 50Hz; long-lead purchase, position 14 | 1 pc |
| ITM-00017 | Oil Cooler; purchased component, position 3 | 1 pc |

The existing Inventor guide below supplies the CAD authoring and publishing part of this larger workflow. New issue labels must follow the configured revision scheme.

## Responsibilities and control rules

| Role | Responsibility |
|---|---|
| Sales / Project Management | Commercial scope, quotation, client decisions, project schedule and handover |
| Engineering | CAD, BOM, tags, revisions, technical review and release |
| Document Control | Transmittals, exact issued versions, distribution and responses |
| Work Preparation | Make/buy, material demand, work instructions, readiness and replacement coordination |
| Procurement | Supplier offers, POs, delivery commitments and payment requests |
| Supplier | Goods and required documents |
| Warehouse | Receipt, identification, quarantine, allocation, location and issue |
| Quality | Document acceptance, inspection and non-conformance disposition |
| Production | Fabrication, assembly, execution evidence and discrepancy reporting |
| Finance / authorised payer | Invoice checks, payment approval and execution |

Assign named people and backups. A department is not automatically a subscription recipient. Apply independent review where required by workspace policy.

- Project occurrences carry customer demand; Library definitions remain reusable articles. Store customer tags on project occurrences/documents unless the reusable design itself changes.
- Client approval, transmittal purpose and assembly production release are separate decisions.
- Authorise early long-lead procurement explicitly before client approval; preliminary drawings never authorise fabrication.
- Delivery, document submission, Quality acceptance, technical completion, payment eligibility and actual payment remain separately visible.
- Notifications prompt action. Current approved records determine whether work may proceed.
- Do not create duplicate demand for ITM-00006 when it already belongs beneath ITM-00000.

## Preparation and sample-data checks

Prepare a practice workspace with the engineering/piping sample, quotations, projects, document control, procurement, stock, planning, tasks and external sharing. Verify the actual Setup catalogue selections and dependencies. Use a fresh project with existing Library articles.

Check both drawings and BOM versions. Header demand is 3 tees, 2 one-inch half couplings, 1 half-inch half coupling, 2 three-quarter-inch half couplings and 496 mm pipe. System demand includes 1135 mm hose and 1673 mm tubing. Configure stock units and distinguish lengths from pieces.

Prepare a controlled shortage for the motor and pump, valid supply sources, currencies, prices, lead times, delivery addresses and supplier contacts. Confirm a combined supplier pump/motor package would not duplicate motor scope. Set project production/delivery dates and configure role permissions and test client/supplier identities.

### RVDs confirmed in sample seed code

Use one enabled RVD occurrence category for all requirement types. See the [RVD category limitation](../features/procurement.md#required-vendor-documents-category-limitation). Multiple-category routing is outside this workflow's P0 scope.

| Article | Seeded requirements |
|---|---|
| ITM-00012 pump/filter set | General Arrangement Drawing; Technical Datasheet; Installation, Operation and Maintenance Manual; Factory Acceptance Test Report; Declaration of Conformity; Recommended Spare Parts List |
| ITM-00021 motor | Technical Datasheet; Installation, Operation and Maintenance Manual; Factory Acceptance Test Report; Declaration of Conformity |
| ITM-00006 header | Material Certificates; Inspection and Test Plan; Pressure Test Certificate |
| ITM-00000 system | General Arrangement Drawing; Inspection and Test Plan; Pressure Test Certificate |

These assignments exist in `PipingEngineeringPart.cs`; `EngineeringSampleArticles.cs` maps the original asset numbers to the ITM stock numbers. This verifies the seed implementation, not an installed database. Inspect the actual item version and materialised PO-line requirements before ordering. Reusable Library RVD definitions are file-less placeholders; supplier files belong to occurrence requirements. Quality must confirm project-specific adequacy and approve any waiver explicitly.

## Detailed workflow

### 1. Sales — quotation

1. Select/create Client, contact and Opportunity.
2. Create a Quotation for one ITM-00000 system including the header. Record scope, exclusions, quantity, price, currency, validity, delivery target and customer payment terms.
3. Attach preliminary technical information and unresolved assumptions, including customer tags.
4. Review and issue the quotation, preserving the exact offered version.
5. Record acceptance with customer order/written evidence.

**Output:** accepted scope. Customer payment terms are separate from supplier 30/50/20 terms. Validate the output template: current quotation help records a missing print template.

### 2. Project Management — project from accepted quotation

1. Configure acceptance automation to create a Project in the intended Explorer folder.
2. Accept and verify one project with its pinned **Generated From** link to the accepted quotation version. Reuse an output already created.
3. Populate client, owner, scope, contacts, delivery/start dates and milestones. The generic creation action creates, places and links the output; automatic copying of all quotation properties/lines is not established.
4. Prepare project areas for BOM, drawings, transmittals, procurement, tasks, quality and handover.

**Output:** traceable project with clear ownership and schedule.

### 3. Engineering — add the assembly and project BOM

Follow the illustrated [Add the assembly walkthrough](engineering-add-assembly.md). The local test added ITM-00000 and inspected the system/header BOM. The Base-category issue was fixed with an administrator-editable, seeded rule for Assembly, Weld Assembly, Item and Part inserted directly under Project. Unit defaults are also administrator-editable and seeded: assigned Stock Unit first, fallback pc, engineer review and unit required. The original occurrence now shows 1 pc, and a fresh reviewed insertion passed. Project BOM materialisation and definition pinning have since been tested during [production/procurement release](engineering-production-release.md). The follow-up sets the project Required Date and verifies MR creation. [Milestone-based planning](engineering-milestone-planning.md) now connects the motor and pump to the assembly-start date, with an administrator-configurable fixed-date default and task/milestone picker. The [Material Requirement dates step](engineering-material-requirement-dates.md) now copies calculated Need By into When Required using an administrator-configurable script mapping, with motor/pump creation and retry checks.

1. Add a project occurrence linked to ITM-00000, quantity 1 pc.
2. Verify the header and purchased children, positions, quantities, units and definition versions.
3. Confirm how project child occurrences are materialised. Generate demand from project occurrences, not reusable Library definitions.
4. Review make/buy: manufacture the header; procure equipment and other materials through approved sources.
5. Check PDFs against the BOM. Use the Inventor guide for authoring/corrections and publishing.

**Output:** initial project baseline without duplicate header demand.

### 4. Document Control — client approval transmittal

**Agreed target workflow:** the engineer creates a Transmittal with properties and expected response (For Approval, For Information, For Quotation, etc.). Its Project Documents tab shows a project-scoped list with an engineer-editable filter, checkboxes and Choose columns. Add selected documents as persistent issue lines, review/issue the pinned package, generate a PDF from the configured transmittal template and attach it to the record. Share the PDF and issued files through the portal with named clients, departments or third parties. Returned commented PDFs are separate response evidence linked to the transmittal/line, never replacements of the originals. The engineer edits the working filter and chooses columns while preparing the package. Administrator UI controls starting defaults, permissions, default columns, purposes/descriptions, response rules, templates, generation actions and permissions. Selection, PDF generation, fixed portal sharing and client responses are now implemented and tested locally; follow the step guides. Later revision and production release gates remain to be exercised.

1. Create a Transmittal with client recipient, **For Approval**, owner, issue date, response due date, distribution channel and reference.
2. Add a Document Issue Line resolving the controlled document/version containing ITM-00000.pdf. A loose filename is insufficient.
3. Review and issue through **Ready to Issue → Issued**; verify version pinning.
4. Share the package using Share/Manage Access and the recipient portal. Test as the client: issued drawing accessible, internal pricing, other projects and unissued revisions inaccessible.
5. Distribute the invitation through the agreed process and track response.
6. Follow [Client review responses](engineering-transmittal-review.md): submit the decision and commented PDF, then inspect the separate response record. Downloading is not approval.

**Output:** auditable revision-0 issue and tested client access. Client transmittal sharing/review needs separate validation from supplier PO sharing.

### 5. Document Control / Work Preparation — preliminary internal transmittal

1. Create a separate package containing ITM-00000 and ITM-00006 controlled drawings.
2. Use **For Work Preparation**, explicitly permitting planning/authorised long-lead purchasing only.
3. Issue, verify pinned versions and assign a Work Preparation review task.
4. Review manufacturability, interfaces, quantities, lead times, RVDs and preliminary work instructions.

**Output:** planning proceeds while production remains blocked.

### 6. Procurement — supplier quotations

1. Request offers for ITM-00012 and ITM-00021 against the current technical and document requirements.
2. Record supplier, reference/revision, date/validity, price/currency, lead time, promised delivery, deviations, RVD commitments and 30/50/20 terms.
3. Engineering/Work Preparation checks technical compliance; Quality checks documents; Procurement compares commercial offers.
4. Approve the winning offer and attach it to the relevant item. Retain a project/source/PO-line reference identifying exactly which offer governs this purchase. Protect confidential prices from client access.
5. Record early-procurement authority and change/cancellation exposure before placing orders.

**Output:** approved source and winning offer per item. Preserve other projects' commercial choices on shared Library records.

### 7. Work Preparation — long-lead MRs

1. Set a configurable long-lead property on project pump/motor occurrences and record need dates.
2. Generate one MR per occurrence through the configured fulfilment/lifecycle action.
3. Verify project, source occurrence, article/version, quantity/unit, supplier source, need date and offer reference.
4. Check RVD requirements before PO creation and verify their later materialisation on each line.
5. Check existing demand and stock/reservations before repeating actions. Schedule remaining material demand as well.

**Output:** two traceable MRs without duplicate procurement.

### 8. Procurement / Quality — configure subscriptions before issue

1. Resolve buyer, Sales/project contact and Quality reviewer, with read access and backups.
2. Arrange Sales notification for the line's **Delivery Complete** event and Quality notification for supplier RVD upload/replacement.
3. Automatically assign these watches at line creation/issue once a supported runner exists. Define how membership/staff changes affect recipients.
4. Test channels, expiry, own-change handling, repeat behavior and exact item links.

**Output:** subscriptions exist before supplier activity. This consolidates the repeated subscription steps in the initial outline.

**Gap:** current subscriptions watch general state/version/revision changes. Target-state filters, automatic assignment and child-upload routing require validation or implementation. A PO-header watch does not establish notification for child uploads. Avoid stopping after the first broad state change when intermediate states precede delivery.

### 9. Procurement — create and share POs

1. Create lines from MRs; group by supplier as appropriate.
2. Check quantities, prices, currencies, addresses, contact/email, need dates, technical baseline and winning offer.
3. Verify line RVD checklists and applicable documents are included in the supplier share.
4. Finish the payment schedule below before issue.
5. Review, issue, check generated output and share with the named supplier.
6. Supplier signs in with the invited identity and confirms delivery commitments; file the acknowledgement.

**Output:** issued supplier order with agreed requirements.

### 10. Procurement / Finance — 30/50/20 payment planning

Proposed basis: agreed PO net value/currency, with explicit tax, invoice, rounding and retention rules. This is supplier payment planning, unrelated to MatStream subscription billing.

| Milestone | Share | Eligibility |
|---|---|---|
| On order | 30% | Authorised issued PO plus contractually required acknowledgement/invoice |
| Delivered | 50% | Accepted complete delivery for the agreed milestone scope, supported by receipts |
| Documentation complete | 20% | Delivery complete AND all required documents accepted AND no blocking quality hold |

Example only: EUR 10,000 net gives EUR 3,000 / EUR 5,000 / EUR 2,000; these are not seeded equipment prices.

1. Record percentage, basis/amount, currency, line/PO scope, condition, planned date, status, approver, invoice and actual payment details.
2. Proposed default: track line readiness, but release a PO-level milestone only when every applicable line qualifies. One delivered motor cannot release a whole-order milestone.
3. Validate 100% and reconciled amounts, placing rounding in the final milestone.
4. Track **Not eligible → Eligible → Requested → Approved → Paid**, with hold/rejection reasons.
5. Handle partial deliveries, changes, cancellations and credits explicitly; never silently rewrite approved/paid amounts.

**Output:** reviewable payment plan. Procurement requests payment; the authorised payer approves/executes it. Eligibility does not transfer money or post an accounting invoice.

### 11. Client — return remarks and record the review result

1. Open the drawing row in the For Approval portal package and download its issued PDF.
2. Add remarks to that PDF, then upload it against the same issued drawing row. Preserve the issued original and its exact revision.
3. Choose the response result, such as Approved, Approved with Comments or Revise and Resubmit. The workspace administrator controls available results, descriptions, lifecycle mappings and attachment requirements.
4. Notify the subscribed engineer or document controller when the review response changes state. Their subscription must be possible before the client responds.

**Output:** traceable client response and returned PDF against the issued drawing. Direct response lifecycle states and drawing subscriber notifications are now implemented and tested locally; see [Client review and returned PDFs](engineering-transmittal-review.md).

### 12. Engineering — assess comments, revise and release in Vault

1. Read the returned PDF and consult the Lead Engineer or Project Manager about what to change and what not to change. Record the disposition as useful traceability.
2. Update Inventor, affected drawings and BOM. For this example, apply the agreed motor/pump tags.
3. Ask the Lead Engineer and a colleague to review and approve through the engineering process.
4. Set the approved drawing to Released in Vault.
5. Upload the released PDF as a new version of the existing MatStream drawing record, with the correct new revision. Preserve earlier versions and issued packages.

**Output:** engineering-approved revision uploaded to MatStream. Technical review and release happen in Inventor/Vault; MatStream does not duplicate this with automatic client-evidence release gates. Transition permissions and history provide accountability; an independent checker is not a mandatory application rule. The current PDF-only demonstration does not prove the Inventor/Vault work was completed.

### 13. Document Control — distribute the released revision

1. Create new transmittals for Client, Work Preparation and Purchasing as applicable. Select the appropriate [Distribution Channel record](engineering-distribution-channels.md), maintained by the project team, to supply contacts and group recipients.
2. Select the correct uploaded revision, configure the purpose and recipients for each channel and review Document Issue Lines.
3. Generate and inspect each covering PDF, issue the package and share it through the applicable channel.
4. Link replacement issues where useful and retain earlier packages and their pinned files.

**Output:** the released revision reaches the required parties with a traceable distribution history. An issue-purpose label does not perform Vault release.

### 14. Engineering / Work Preparation — prepare manufacture

1. Confirm the workshop and purchasing teams are using the intended distributed revision of ITM-00000 and ITM-00006.
2. Follow [Release project assemblies](engineering-production-release.md): use the administrator-configured transition to materialize an inherited project BOM position if needed and pin its engineering definition version before production/procurement automation. Existing copies are reused. These handoff states do not repeat Vault technical approval.
3. Finalise work instructions, inspection/test plans and welding instructions where applicable.
4. Complete material requirements and manufacturing tasks against the schedule. Material availability remains a separate gate.

**Output:** manufacturing handoff based on the released engineering documents.

### 15. Supplier / Warehouse — goods delivery and receipt

1. Deliver with PO/delivery references; documents may arrive before or after goods.
2. Warehouse checks identity, quantity, damage and traceability, then records receipt/location.
3. Record partial, damaged and rejected receipts accurately; quarantine doubtful stock and exclude it from readiness.
4. Use the supported receipt process. Map actual configured states: Delivered and Received are not automatically interchangeable.
5. Notify Sales when complete-delivery criteria qualify; enable the 50% milestone only for its accepted scope.

**Output:** traceable physical receipt. Delivery alone cannot qualify final completion/payment.

### 16. Supplier / Quality — RVD submission and acceptance

1. Supplier uploads into the correct line requirement. The existing portal changes **Required → Submitted**.
2. Notify Quality with project, line, requirement and exact file/version.
3. Review item/tag/serial/batch, revision, document type and acceptance criteria.
4. Accept/reject each document with reviewer/date/reason; request corrections where needed.
5. Configure Accepted as read-only for suppliers. Quality must reopen a requirement through an authorised transition before supplier correction; reopening invalidates completion and replacement evidence requires review again.
6. Documentation is complete only when all applicable requirements are accepted or authoritatively waived. Define zero-requirement behavior so a missing checklist cannot accidentally qualify.

**Output:** accepted evidence, distinct from file submission. Internal header/system manufacturing evidence is separate from supplier pump/motor RVDs.

**Gap:** Accepted/Rejected lifecycle states and replacement reset to Submitted already exist. Validate Quality permissions and review usability; add configurable submission-state mapping, upload notifications, waiver policy and aggregate completion where missing. The current reset resolves the literal state name Submitted and must respect administrator customisation.

### 17. Automation / Procurement — complete lines and request final payment

Proposed decision rule:

```text
DeliveryComplete = accepted received quantity >= current required quantity
DocumentationComplete = every applicable RVD accepted or authorised waived
TechnicalComplete = DeliveryComplete AND DocumentationComplete
                    AND no blocking quality hold AND line not cancelled
FinalPaymentEligible = every applicable line TechnicalComplete
                       AND commercial milestone/invoice checks satisfied
```

1. Re-evaluate after receipt/correction, document review/replacement/waiver, quantity change, cancellation and quality-hold change.
2. Read persisted evidence and update atomically through supported APIs/transitions, handling either event order.
3. Set configured line **Complete** once while retaining independent delivery/document statuses.
4. Create one final-payment action for Procurement per eligibility cycle, linking the evidence.
5. Audit rule/configuration version, inputs, result and failures; retry without duplicate transitions/tasks.
6. Later invalidation holds unpaid requests and alerts the owner. Already paid milestones create an exception, not rewritten payment history.

**Gap:** current scripts support revision changes, lifecycle switching, assignment and notifications. They do not establish this cross-item receipt/RVD evaluator or a generic PO-line transition to Complete. Specify the supported aggregation reads, guarded transition/action and evidence-change runners before implementing the script. Earlier help stating these lifecycle helpers are unsupported is stale.

### 18. Work Preparation / Production — readiness and start-date messages

Use two separate signals:

- **Ready for Production:** released/approved baseline, operation materials accepted/allocated, work instructions/resources available and quality prerequisites satisfied.
- **Production Start Due:** date reached; alert even when blocked and list blockers. The date itself never authorises production.

Evaluate per operation: header fabrication can precede pump arrival if its own prerequisites qualify. Final assembly has its own gate. Send a handover task with exact drawings, BOM, allocations/locations and instructions. Notify once per relevant readiness/baseline cycle and handle rescheduling explicitly.

**Gap:** validate or implement dependency-change evaluation and a real scheduled runner; a Scheduled Job context option alone is not a timer.

### 19. Production / Quality / Work Preparation — wrong fitting

1. Stop use, identify and quarantine the fitting.
2. Create a Q-call / Non-conformance linked to project, affected occurrence/version, original PO line and receipt. Attach photos, required/actual specification and impact.
3. Assign Work Preparation for replacement coordination; Quality owns disposition. Record return/rework/scrap/concession separately.
4. Confirm correct specification and create a linked replacement MR/PO without silently increasing design demand.
5. Link replacement to NCR/original supply; record chargeable, warranty or credit treatment.
6. Subscribe Work Preparation and Production to accepted receipt/ready-to-collect. Broad state watches can support a rehearsal with manual review.
7. Monitor replacement due date and update production planning.

**Output:** controlled replacement; wrong stock cannot satisfy readiness.

### 20. Warehouse / Production — replacement arrival and pickup

1. Receive against the replacement line; verify specification, quantity, traceability and required inspection/documents.
2. Allocate to the project and record location. Receipt alone does not allow pickup while inspection is pending.
3. Set configured accepted/available state and notify both teams with item, quantity, location, project and NCR reference.
4. Record issue/collection to prevent double pickup; recheck readiness before restarting.
5. Verify corrective result and close the NCR through authorised disposition, preserving original/replacement history.

**Output:** correct fitting reaches Production with closure evidence.

### 21. Production / Quality / Project Management — finish and hand over

These closing activities extend the supplied outline to reach a finished product:

1. Manufacture ITM-00006 to the released package; record certificates, weld traceability, inspection and tests.
2. Assemble ITM-00000 with accepted equipment/header; capture installed tags/serials and deviations.
3. Execute the approved inspection/test plan and resolve NCRs/punch items. Use approved engineering test parameters, not unreviewed sample defaults.
4. Record as-built changes and issue controlled documents where required.
5. Compile drawings, BOM, supplier evidence, manufacturing/test records and manuals/certificates for handover.
6. Issue the final client transmittal via the validated portal route and record acceptance and contractual dispatch/delivery.
7. Close tasks/payment actions, reconcile costs/payments and archive under configured retention.

**Output:** completed, documented assembly and explicit project closure.

## Capability and implementation backlog

Established means supporting source/help was inspected; validate means scenario testing remains; proposed means required behavior is not yet evidenced. No live workspace execution has been verified.

| Priority | Capability | Assessment / next action |
|---|---|---|
| P0 | Catalogue, drawings, BOM and RVD sample | Required fix: project RVD additions/authorised exclusions must propagate through MR to PO with provenance and controlled freeze/change handling; validate existing catalogue and units |
| P0 | Accepted quotation creates project | Required fix: acceptance script automatically copies Client, contact and administrator-mapped relevant quotation fields; extend existing generic create/link path |
| P0 | Transmittal pinning | Established issue action; validate document references, rights and retention |
| P0 | Client portal and response capture | Validate exact share/review route and version access; implement missing UI |
| P0 | Preliminary versus production authority | Configure purposes and enforce approval/release gates |
| P0 | Long-lead/offer traceability | Required fix: project-selected supplier and agreed offer terms drive procurement with durable provenance; retain independent Library defaults |
| P0 | Supplier RVD submission | Required fixes: administrator-selected submission state and durable supplier upload/replacement signal; reuse existing upload/checklist/access mechanisms |
| P0 | Quality acceptance/roll-up | Required fixes: evidence-bound documentation aggregate, configurable semantics and authorised-change invalidation; use existing Accepted read-only permissions and Quality reopening |
| P0 | Specific/automatic subscriptions | Required script-first fixes: target-state/upload matching, child ownership routing, automatic assignment, supported APIs/runners and documented administrator examples; reuse delivery services |
| P0 | Payment milestones | Required shared Payment Milestone category/Uses tab for Project invoice planning and PO payment planning; script-driven schedules/eligibility and configurable fields, lifecycles, approvals and evidence |
| P0 | Script completion | Proposed aggregation APIs, transition action, event triggers, retry and hold rules |
| P1 | Production readiness/date alerts | Proposed dependency evaluation and scheduled runner validation |
| P1 | NCR/replacement | Validate category/lifecycle, assignees, quarantine and replacement demand |
| P1 | Quotation print output | Current help records missing template; verify and provide if needed |
| P1 | Worklists/handover | Configure overdue client responses/RVDs, review queue, payment readiness, production blockers, replacement arrivals and closure |

### Automation event contract

Required triggers: quotation accepted; PO line created/issued; delivery complete; RVD submitted/replaced/reviewed/waived; receipt corrected; quantity/cancellation/hold changed; operation readiness changed; production date reached; replacement accepted/allocated.

For every trigger define persisted evidence, evaluator, recipients/action, retry/deduplication key and audit history. Never infer child-event routing from a header subscription. Record missing recipients and automation failures in an actionable queue. Notifications must link to the relevant project/line/document and verify recipient read access.

### Administrator configuration requirements

All new defaults and business choices must be editable by a workspace administrator through application UI: recipients, labels/descriptions, long-lead classification, RVD sets, purposes, state names, percentages/basis/scope, readiness conditions, channels/timing, waiver authority and hold policies.

Use existing configuration UI where sufficient and implement missing configuration screens as part of the feature. Keep scripts/parameters editable in the automation editor. Production Setup is a one-time initialisation. Test reruns are outside this workflow assessment; no seed-tracking or restore feature is planned.

Prefer existing entity/property/link mechanisms. New numeric IDs/references use SQL int / C# int where possible; preserve the PropertyValueID bigint / long exception. Any conversion must validate values/identity state and preserve data, keys, relationships and index options through repeatable migrations.

## Acceptance and demonstration plan

**The workflow itself is our test.** Start at quotation creation and proceed through the story. For each step record what works, what needs configuration and what needs development, using the actual records and expected handoff. Check catalogue, document control and permissions when encountered, rather than as a separate preliminary exercise. Automated checks provide supporting evidence; they do not replace the walkthrough.

| Scenario | Required evidence |
|---|---|
| Happy path | One accepted quotation/project, linked BOM/MRs/POs, reviewed evidence, completion and authorised payment request |
| Revision history | Original packages still open revision 0; production packages open intended new revisions |
| Portal isolation | Client sees only issued content; supplier sees only its shared PO/upload locations; wrong identity denied |
| RVD seed | Pump six and motor four requirements before approved project changes; actual PO checklist matches |
| Goods before documents | Delivery progress/50% follows scope; completion/final 20% blocked until acceptance |
| Documents before goods | Review can finish first; completion remains blocked pending accepted receipt |
| Rejected/replaced evidence | Submission alone never qualifies; replacement requires review; invalidation holds unpaid eligibility |
| Partial/quarantined receipt | Unaccepted quantities do not satisfy delivery or production readiness |
| Notifications | Correct Sales/Quality/Production recipients, exact evidence links and no premature first-change termination |
| Retry/concurrency | Repeated/simultaneous events yield one completion/request; failures visible and retryable |
| Date reminder | Blockers visible; date alone cannot authorise work |
| Wrong fitting | Linked NCR/replacement, quarantine, both-team location message and recorded collection |
| Payments | 100% reconciled; whole-PO milestones wait for applicable lines; actual payment separately approved |
| Final handover | Tested assembly, resolved exceptions, controlled evidence and close decision |

## Planned departmental chapters

Keep this page as the extensive process plan. Add guides beneath Engineering Project Workflow as capabilities are validated: Sales quotation/project; Engineering assembly/BOM; Document Control approvals/transmittals; Work Preparation demand; Procurement offers/POs/payments; Supplier submissions; Engineering revision/release; Warehouse receipt/pickup; Quality review; Production/NCR; Project Management handover/close.

Each chapter needs prerequisites, owner, exact UI actions, expected outcome, handoff, exceptions and evidence. Reuse existing Inventor and supplier portal guides. Record movies only after corresponding acceptance checks pass.

## Evidence and related help

Repository sources, relative to the repository root:

- `MatStream.Web.Manage/Features/Setup/Templates/Parts/EngineeringSampleArticles.cs`: stock mapping, quantities, descriptions and ordering instructions.
- `MatStream.Web.Manage/Features/Setup/Templates/Parts/EngineeringSampleCatalogue.cs`: Library catalogue, sources and drawing attachments.
- `MatStream.Web.Manage/Features/Setup/Templates/Parts/PipingEngineeringPart.cs`: project occurrence and exact RVD assignments.
- `MatStream.Web.Manage/Features/Setup/Templates/Parts/RequiredVendorDocumentsPart.cs`: reusable requirements and occurrence assignment model.
- `MatStream.API/MatStream.Application/Services/Tenant/SubscriptionEventConsumer.cs`: lifecycle, version and revision event handling.
- `MatStream.Web.Manage/Features/Setup/Templates/Parts/CommercialTermsPart.cs`: existing Payment Terms field/options.
- `MatStream.Web.Manage/wwwroot/SetupSeedFiles/Piping/ITM-00000.pdf` and `ITM-00006.pdf`: visually reviewed source drawings.

Related: [Quotations](../features/opportunities-quotations.md), [Document Control](../solutions/document-control.md), [Procurement](../features/procurement.md), [Supplier portal](supplier-portal.md), [Subscriptions](../features/subscriptions.md), [Automation support](../admin/automations-admin.md), [Planning](../features/planning.md), [Quality](../solutions/quality-management.md).

## Engineering steps

### Inventor assembly and BOM

Create an assembly with existing fittings and a new part, fill in its information in Inventor, create a drawing with a parts list, and publish the drawing and BOM to MatStream using iLogic.

[Follow the Inventor assembly and BOM guide](inventor-assembly-bom.md)
