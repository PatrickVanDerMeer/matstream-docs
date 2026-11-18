# Engineering Project Workflow

!!! info "Implementation status — reconciled 17 November 2026"
    The workflow features through **as-built documentation and final client handover are implemented and tested locally**, with linked walkthroughs and screenshots below. This includes production material consumption, reservation release, inspections, completed-output receipt and final handover recording. The remaining work is a complete fresh-workspace rehearsal with separate users and real approved evidence, plus assessment of dispatch, commercial settlement and project closure. Local synthetic tests do not represent physical manufacture or contractual acceptance.

Start with a customer quotation and finish with manufactured, tested and documented assemblies. The workflow itself is our test: at each step distinguish working behavior, configuration needed and development needed. All new business settings remain workspace-admin-configurable; use editable automation scripts where possible. The latest implementation adds the as-built package and final client handover, following production completion, receipt and material issue.

## Current progress

| Workflow area | Status and evidence | Still to do |
|---|---|---|
| Quotation → Project | Field-copy script, admin mappings/defaults and local [walkthrough](../engineering-quotation-project.md) complete | Validate quotation print output and wider role scenarios |
| Project BOM and units | Configurable BOM Position creation, Stock Unit/fallback/required review and Units of Quantity dialog; [local placement](../engineering-add-assembly.md) tested | Full fresh-workspace rehearsal |
| Transmittals | [Selection/filter/columns](../engineering-project-documents.md), [PDF/history/issue consistency](../engineering-transmittal-pdf.md) and [pinned portal packages](../engineering-transmittal-portal.md) tested | Full historical access/retention and distinct-user checks |
| Client remarks and drawing reissue | [Direct response states and returned PDF](../engineering-transmittal-review.md), notifications, revision fix, project references and [three-channel reissue](../engineering-reissue-drawings.md) tested | Real Inventor/Vault release and separate client-user rehearsal; download-only review-status wording |
| Production/procurement release | [Materialise/pin and production-order creation](../engineering-production-release.md) tested locally for both assemblies | Real manufacturing readiness and separate-role release rehearsal; local review/release decisions were simulated |
| Planning and MRs | [Milestone dates, target versus achievable delay and MR date mapping](../engineering-milestone-planning.md) tested | Repeat late-motor scenario on the actual workflow POs as part of readiness |
| Supplier selection and project RVDs | [Agreed supplier/terms](../engineering-supplier-selection.md), [Ordered lifecycle handover](../engineering-procurement-handover.md) and [frozen project requirements](../engineering-project-rvd-requirements.md) tested | Controlled later checklist/commercial amendments; full supplier-role rehearsal |
| Supplier documents and Quality | [Rename-safe upload/events](../engineering-supplier-document-uploads.md) and [live accepted-RVD status](../engineering-po-documentation-status.md) implemented and locally tested | Separate supplier file-transfer/read-only Accepted/Quality-reopen walkthrough |
| Sales/Quality subscriptions | [Script proposals, consent, Remember and User Settings](../engineering-subscription-proposals.md) tested | Real team access/membership and email/task transport; handle already-issued orders explicitly |
| Payment planning/eligibility | [Shared Project/PO 30/50/20 milestones and automatic PO eligibility](../engineering-payment-milestones.md) tested | Invoice/settlement evidence and controlled amendments; Project invoice eligibility is manual |
| PO-line completion | [Full delivery + accepted RVDs + no hold; automatic completion/reopening](../engineering-po-line-completion.md) tested on motor/pump | Actual goods/Quality decisions and wider goods-disposition integration beyond Completion Hold |
| Production readiness | [Prerequisites, editable script and scheduled personal notifications](../engineering-production-readiness.md) tested locally | Full BOM/resources and separate-role rehearsal; late motor on original workflow POs |
| Wrong fitting and replacement | [Non-conformance, held prerequisite, replacement MR/PO and arrival/pickup messages](../engineering-fitting-replacement.md) tested locally | Separate-role and real quarantine/return/pickup rehearsal |
| Production completion / inspections | [Plans, inspection records, review permissions and live completion criterion](../engineering-production-completion.md) passed native and browser local tests for both assemblies, including uploads, review, completion and screenshots | Separate-role rehearsal; actual physical inspections |
| Production material issue | [Scripted pinned BOM/header consumption and atomic MR reservation release](../engineering-production-material-issue.md); partial and full native/browser tests | Full physical BOM coverage, returns and separate-role rehearsal |
| Finished-goods receipt | [Scripted full-output receipt, original revision, stock unit and duplicate protection](../engineering-production-receipt.md) tested natively and through positive browser receipts for both assemblies, with screenshots | Separate Warehouse-user rehearsal |
| Final handover | [As-built sections, issued package, portal acknowledgement and scripted handover journal](../engineering-as-built-handover.md) tested locally with screenshots | Real dossier/physical delivery, separate-role rehearsal and project closure |

“Tested locally” is feature evidence, not a claim that one fresh project has completed the full real-world story. Earlier tests used separate local planning/procurement fixtures and the current test account in multiple roles. Separate identities, actual receipt/inspection and a complete freshly seeded rehearsal remain outstanding.

## Recommended next step

Run **one complete rehearsal from quotation to final handover in a freshly seeded workspace**, using separate client, supplier and departmental users. The individual features have passed local tests; the next check is that all handoffs work together on the same project with the intended permissions.

Follow the linked guides in the progress table. Use the complete BOM, approved drawings and genuine inspection/document evidence for a real project. Test the late-motor scenario, supplier corrections and replacement fitting along the way. Finish with the [as-built package and client handover](../engineering-as-built-handover.md).

After that rehearsal, assess physical dispatch/delivery, remaining invoices/payments, open tasks and the authorised project-close decision. These are separate from recording the documentation handover. Temporary stock and approvals used in local tests were restored; the existing test orders are not certified ready for manufacture.

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

Use one enabled RVD occurrence category for all requirement types. See the [RVD category limitation](../../features/procurement.md#required-vendor-documents-category-limitation). Multiple-category routing is outside this workflow's P0 scope.

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
3. Verify automatically copied Client/contact and the administrator-mapped fields/defaults against the accepted quotation. Complete any remaining owner, scope, schedule and milestone fields. Automatic quotation-line/BOM conversion is not part of this field-copy feature.
4. Prepare project areas for BOM, drawings, transmittals, procurement, tasks, quality and handover.

**Output:** traceable project with clear ownership and schedule.

### 3. Engineering — add the assembly and project BOM

Follow the illustrated [Add the assembly walkthrough](../engineering-add-assembly.md). The local test added ITM-00000 and inspected the system/header BOM. The Base-category issue was fixed with an administrator-editable, seeded rule for Assembly, Weld Assembly, Item and Part inserted directly under Project. Unit defaults are also administrator-editable and seeded: assigned Stock Unit first, fallback pc, engineer review and unit required. The original occurrence now shows 1 pc, and a fresh reviewed insertion passed. Project BOM materialisation and definition pinning have since been tested during [production/procurement release](../engineering-production-release.md). The follow-up sets the project Required Date and verifies MR creation. [Milestone-based planning](../engineering-milestone-planning.md) now connects the motor and pump to the assembly-start date, with an administrator-configurable fixed-date default and task/milestone picker. The [Material Requirement dates step](../engineering-material-requirement-dates.md) now copies calculated Need By into When Required using an administrator-configurable script mapping, with motor/pump creation and retry checks.

1. Add a project occurrence linked to ITM-00000, quantity 1 pc.
2. Verify the header and purchased children, positions, quantities, units and definition versions.
3. Confirm how project child occurrences are materialised. Generate demand from project occurrences, not reusable Library definitions.
4. Review make/buy: manufacture the header; procure equipment and other materials through approved sources.
5. Check PDFs against the BOM. Use the Inventor guide for authoring/corrections and publishing.

**Output:** initial project baseline without duplicate header demand.

### 4. Document Control — client approval transmittal

**Agreed target workflow:** the engineer creates a Transmittal with properties and expected response (For Approval, For Information, For Quotation, etc.). Its Project Documents tab shows a project-scoped list with an engineer-editable filter, checkboxes and Choose columns. Add selected documents as persistent issue lines, generate and attach the PDF from the configured transmittal template, then review/issue and pin the package. Share the PDF and issued files through the portal with named clients, departments or third parties. Returned commented PDFs are separate response evidence linked to the transmittal/line, never replacements of the originals. The engineer edits the working filter and chooses columns while preparing the package. Administrator UI controls starting defaults, permissions, default columns, purposes/descriptions, response rules, templates, generation actions and permissions. Selection, PDF generation, fixed portal sharing and client responses are now implemented and tested locally; follow the step guides. Revision handling and materialise/pin release have since been tested locally; actual Vault review/release and production handover remain separate.

1. Create a Transmittal with client recipient, **For Approval**, owner, issue date, response due date, distribution channel and reference.
2. Add a Document Issue Line resolving the controlled document/version containing ITM-00000.pdf. A loose filename is insufficient.
3. Generate the PDF, then review and issue through **Ready to Issue → Issued**; consistency checks and version pinning apply.
4. Share the package using Share/Manage Access and the recipient portal. Test as the client: issued drawing accessible, internal pricing, other projects and unissued revisions inaccessible.
5. Distribute the invitation through the agreed process and track response.
6. Follow [Client review responses](../engineering-transmittal-review.md): submit the decision and commented PDF, then inspect the separate response record. Downloading is not approval.

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
3. Configure the seeded lifecycle-triggered subscription proposals. Each user chooses Yes/No and may Remember the answer; users can change it in User Settings. Already-issued orders need an explicit plan because proposals are not retroactive.
4. Test channels, expiry, own-change handling, repeat behavior and exact item links.

**Output:** subscriptions exist before supplier activity. This consolidates the repeated subscription steps in the initial outline.

**Implemented and locally tested:** delivery-only and RVD-upload event matching, child-document routing and consent-based script proposals. Validate real department access/membership and selected transport channels; email/task delivery was not exercised by the local consent test.

### 9. Procurement — create and share POs

1. Create lines from MRs; group by supplier as appropriate.
2. Check quantities, prices, currencies, addresses, contact/email, need dates, technical baseline and winning offer.
3. Verify line RVD checklists and applicable documents are included in the supplier share.
4. Finish the payment schedule below before issue.
5. Review, issue, check generated output and share with the named supplier.
6. Supplier signs in with the invited identity and confirms delivery commitments; file the acknowledgement.

**Output:** issued supplier order with agreed requirements.

### 10. Procurement / Finance — 30/50/20 payment planning

The [shared Payment Milestone planning step](../engineering-payment-milestones.md) is implemented: enter the agreed basis/currency and schedule date, then create the editable 30/50/20 example on a Project or PO. Issued-PO eligibility is now evaluated by an administrator-configurable script using whole-order delivery and accepted RVD evidence; plan creation itself only records the agreement. This is supplier payment planning, unrelated to MatStream subscription billing.

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

**Output:** traceable client response and returned PDF against the issued drawing. Direct response lifecycle states and drawing subscriber notifications are now implemented and tested locally; see [Client review and returned PDFs](../engineering-transmittal-review.md).

### 12. Engineering — assess comments, revise and release in Vault

1. Read the returned PDF and consult the Lead Engineer or Project Manager about what to change and what not to change. Record the disposition as useful traceability.
2. Update Inventor, affected drawings and BOM. For this example, apply the agreed motor/pump tags.
3. Ask the Lead Engineer and a colleague to review and approve through the engineering process.
4. Set the approved drawing to Released in Vault.
5. Upload the released PDF as a new version of the existing MatStream drawing record, with the correct new revision. Preserve earlier versions and issued packages.

**Output:** engineering-approved revision uploaded to MatStream. Technical review and release happen in Inventor/Vault; MatStream does not duplicate this with automatic client-evidence release gates. Transition permissions and history provide accountability; an independent checker is not a mandatory application rule. The current PDF-only demonstration does not prove the Inventor/Vault work was completed.

### 13. Document Control — distribute the released revision

1. Create new transmittals for Client, Work Preparation and Purchasing as applicable. Select the appropriate [Distribution Channel record](../engineering-distribution-channels.md), maintained by the project team, to supply contacts and group recipients.
2. Select the correct uploaded revision, configure the purpose and recipients for each channel and review Document Issue Lines.
3. Generate and inspect each covering PDF, issue the package and share it through the applicable channel.
4. Link replacement issues where useful and retain earlier packages and their pinned files.

**Output:** the released revision reaches the required parties with a traceable distribution history. An issue-purpose label does not perform Vault release.

### 14. Engineering / Work Preparation — prepare manufacture

1. Confirm the workshop and purchasing teams are using the intended distributed revision of ITM-00000 and ITM-00006.
2. Follow [Release project assemblies](../engineering-production-release.md): use the administrator-configured transition to materialize an inherited project BOM position if needed and pin its engineering definition version before production/procurement automation. Existing copies are reused. These handoff states do not repeat Vault technical approval.
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
6. Use the live documentation status: every included frozen requirement needs accepted current evidence. Project exclusions are recorded before freezing; a general post-order waiver/amendment workflow is not supplied by this feature. Administrators configure explicit empty-checklist behavior; a missing checklist cannot qualify.

**Output:** accepted evidence, distinct from file submission. Internal header/system manufacturing evidence is separate from supplier pump/motor RVDs.

**Implemented and locally tested:** upload reset uses configured state IDs, upload/replacement events notify eligible subscribers, and the live documentation script verifies current evidence. Separate supplier/Quality permission rehearsal and a controlled waiver/amendment process remain open.

### 17. Automation / Procurement — complete lines and request final payment

The [automatic PO-line completion step](../engineering-po-line-completion.md) is implemented. The stored workspace script combines full receipt quantities, acceptance of every required current RVD and Completion Hold. It moves only lines in the configured managed lifecycle states:

```text
Awaiting evidence → Complete: full delivery + all required RVDs accepted + no hold
Complete → Awaiting evidence: any required evidence stops qualifying or a hold is set
Cancelled: unchanged by the worker
```

Events and configurable reconciliation re-read saved evidence. Normal lifecycle transitions enforce permissions and a live completion criterion, record history and notify ordinary lifecycle subscribers. The motor and pump were verified locally in both directions. Receiving goods alone or uploading an unaccepted file does not complete the line.

The existing [payment milestone evaluator](../engineering-payment-milestones.md) separately rechecks whole-PO evidence following those events. Technical completion is not payment approval or proof of payment. Request/approve/pay remain authorized lifecycle steps; the new completion feature does not create an extra payment-request task or transfer money. Invoice/settlement evidence checks, automatic quality-disposition integration beyond Completion Hold and any additional reminders/escalation remain separate follow-up work.

### 18. Work Preparation / Production — readiness and start-date messages

Implemented: [Production readiness and start-date messages](../engineering-production-readiness.md). Each production order has a reviewed Prerequisites Uses tab and planning activity. An editable script evaluates allocated physical materials, preceding orders, holds and optional accepted supplier-document evidence. A real background worker publishes changes and dated warnings through personal subscriptions. Mappings, labels, states, defaults and timing remain admin-configurable.

Local tests showed the header can become ready while final assembly stays blocked, and delivered actual in-app ready/start warnings. Temporary stock and dates were restored. The signal never starts production or replaces Inventor/Vault release. Full BOM/instructions/resources and the original project's late-motor PO scenario remain to be rehearsed.

### 19. Production / Quality / Work Preparation — wrong fitting

Implemented and locally tested: [Replace a wrong fitting](../engineering-fitting-replacement.md). Create a Non-conformance linked to the original MR/PO line and held production prerequisite. Record the defect, owner and disposition. The request transition runs an editable script to create one source-linked replacement MR, keeping design demand and the pinned item/RVD baseline intact. Normal MR purchasing creates the replacement PO.

### 20. Warehouse / Production — replacement arrival and pickup

The normal receipt service and scheduled evaluator advance the case to Arrived after full receipt; personal lifecycle subscriptions notify Production and Work Preparation. Ready for Pickup has a live criterion for receipt, inspection, reservation, optional required documents, no hold and pickup instructions. Corrected receipt or withdrawn evidence reopens the appropriate state. Collected and Closed remain authorised lifecycle actions. The [collection script](../engineering-resume-production.md) consumes the fitting stock and releases the consumed MR reservation. Hold clearance and disposition remain explicit authorised team decisions before production resumes.

Local fitting tests produced a real in-app arrival/pickup message. Synthetic stock, receipt and Quality evidence were restored; no real collection or production occurred. Later [collection and restart tests](../engineering-resume-production.md) also passed; temporary stock and state changes were restored. A production prerequisite hold is not a global inventory quarantine; separate-role and actual stock-disposition rehearsal remain.

### 21. Production / Quality / Project Management — finish and hand over

Follow [As-built documentation package and final client handover](../engineering-as-built-handover.md) for the implemented selection, checklist, PDF, portal acknowledgement and handover record, with local screenshots.

These closing activities extend the supplied outline to reach a finished product:

1. Manufacture ITM-00006 to the released package; record certificates, weld traceability, inspection and tests. Follow the [production completion and inspection records](../engineering-production-completion.md) walkthrough for each production order.
2. Assemble ITM-00000 with accepted equipment/header; capture installed tags/serials and deviations.
3. Execute the approved inspection/test plan and resolve NCRs/punch items. Use approved engineering test parameters, not unreviewed sample defaults.
4. Record as-built changes and issue controlled documents where required.
5. Compile drawings, BOM, supplier evidence, manufacturing/test records and manuals/certificates for handover.
6. Generate and issue the final transmittal, share its pinned files, obtain the configured whole-package client response and run **Record final handover**. These documentation handover actions are implemented and tested locally.
7. Separately record physical dispatch/delivery and contractual obligations as applicable. Close tasks/payment actions, reconcile costs/payments and make the authorised project-close decision. These closing activities remain to be assessed in the complete rehearsal.

**Output:** a traceable as-built documentation handover. Physical delivery, settlement and project closure require their own evidence and decisions.

## Capability and implementation backlog

Current local implementation and evidence are summarised above. The table below distinguishes implemented slices from remaining validation and development; local tests do not imply production deployment or completed manufacture.

| Priority | Capability | Assessment / next action |
|---|---|---|
| P0 | Catalogue / project RVDs | Frozen project additions/exclusions and MR → PO propagation implemented/tested. Controlled later amendments and fresh rehearsal remain. |
| P0 | Quotation → Project | Field-copy script and admin mappings/defaults implemented/tested. Quotation print output remains to validate. |
| P0 | Document control / portal | Selection, PDF/version checks, pinned portal access, direct response states and reissue implemented/tested. Historical retention/access, distinct identities and recorded download-only wording remain. |
| P0 | Early-buy / production authority | Existing permissions plus materialise/pin/order creation tested. Technical review/release stays in Vault; real production-authority rehearsal remains. |
| P0 | Supplier / terms | Project selection and agreed terms to PO implemented/tested; wider commercial amendment controls remain. |
| P0 | Supplier documents / Quality | Rename-safe upload and current accepted-RVD aggregate implemented/tested; distinct supplier/Quality rehearsal and waiver handling remain. |
| P0 | Subscriptions | Consent-based generic script proposals and Sales/Quality examples implemented/tested; real teams, email/tasks and old orders remain to rehearse. |
| P0 | Payment Milestones | Shared planning and issued-PO eligibility implemented/tested; invoice/settlement evidence and controlled amendments remain. |
| P0 | Script completion | Delivery/RVD/hold completion and reopening implemented/tested; wider goods-disposition integration and any additional escalation remain. |
| Implemented / tested locally | Production readiness/date alerts | Owned prerequisites, editable script, real worker and personal notifications; full production rehearsal remains. |
| Implemented / tested locally | NCR/replacement/pickup | Category, assignment script, linked replacement MR/PO, receipt worker and consented arrival/pickup updates; real disposition and collection remain team actions. |
| Validated locally | Collection / restart | Collected, explicit hold clearance, ready header and Released/In Progress work; [walkthrough](../engineering-resume-production.md). Replacement collection now posts stock issue and reservation release; [General material/header issue](../engineering-production-material-issue.md) is now locally tested; full physical BOM coverage and returns remain separate. |
| Implemented / tested locally | Production completion / inspections | Separate header and final plans/records, accepted evidence, quantity/hold/prerequisite checks and authorised completion. Separate-role and actual inspection rehearsal remain. |
| Implemented / tested locally | Production material issue | Pinned BOM/header stock consumption, partial issues and atomic reservation release; full physical BOM and returns rehearsal remain. |
| Implemented / tested locally | Finished-goods receipt | Both assemblies received through native and browser tests, with duplicate protection and stock-ledger evidence; separate Warehouse-user rehearsal remains. |
| Implemented / tested locally | As-built / final client handover | Configurable sections, pinned dossier, PDF register, whole-package client response and scripted audit journal; [walkthrough and screenshots](../engineering-as-built-handover.md). Real dossier and separate-role rehearsal remain. |
| Remaining assessment | Dispatch / settlement / project closure | Physical delivery, financial reconciliation, outstanding tasks and authorised close decision; documentation handover does not perform these actions. |
| Other | Administration usability | Automations Used in / Hide / Show hidden remains planned; not a readiness prerequisite. |

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

## Departmental walkthroughs

The progress table links the implemented departmental guides, from quotation/project creation through procurement, document control, production, warehouse receipt and final client documentation handover. Use this overview to follow their order. Extend the guides with findings from the complete rehearsal; dispatch, settlement and project closure remain separate follow-up topics.

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

Related: [Quotations](../../features/opportunities-quotations.md), [Document Control](../../solutions/document-control.md), [Procurement](../../features/procurement.md), [Supplier portal](../supplier-portal.md), [Subscriptions](../../features/subscriptions.md), [Automation support](../../admin/automations-admin.md), [Planning](../../features/planning.md), [Quality](../../solutions/quality-management.md).

## Engineering steps

### Inventor assembly and BOM

Create an assembly with existing fittings and a new part, fill in its information in Inventor, create a drawing with a parts list, and publish the drawing and BOM to MatStream using iLogic.

[Follow the Inventor assembly and BOM guide](../inventor-assembly-bom.md)

Review [project-specific RVD requirements](../engineering-project-rvd-requirements.md) before MR creation; the frozen decisions flow to each PO line.

The implemented procurement steps include [supplier document upload and reviewer notification](../engineering-supplier-document-uploads.md), with administrator-configured submission states that survive renaming.

Quality can now check the [PO-line documentation status](../engineering-po-documentation-status.md): a live script evaluates the frozen required checklist against current accepted evidence.

Sales and Quality can now receive [personal subscription proposals](../engineering-subscription-proposals.md) after configured lifecycle changes, with remembered Yes/No choices editable in User Settings.

Plan customer invoicing and supplier payments with the shared [Payment Milestones category and 30/50/20 Uses tabs](../engineering-payment-milestones.md). Creating the schedule records the agreement; eligibility, invoice issue and actual payment remain separate decisions.

Project Management and Purchasing can now create [shared Payment Milestone plans](../engineering-payment-milestones.md) on projects and POs, with an administrator-editable 30/50/20 example. Issued-PO eligibility now updates automatically from whole-order evidence; approval and actual payment remain separate.

Purchasing and Quality can now [complete PO lines automatically](../engineering-po-line-completion.md) from full delivery and accepted project-specific RVDs. Completion Hold and invalidation return a managed line to Awaiting evidence; payment approval and actual payment stay separate.

### Collect the replacement and resume production

The [collection and restart walkthrough](../engineering-resume-production.md) tests Collected, explicit prerequisite hold clearance, readiness, Released/In Progress and independent final-assembly blockers, with screenshots. These existing actions work locally. Replacement collection now consumes its warehouse stock and releases its MR reservation; [General material/header issue](../engineering-production-material-issue.md) is now locally tested; full physical BOM coverage and returns remain separate.


The [finished-output warehouse receipt](../engineering-production-receipt.md) is now an explicit stored script action. It does not consume the input BOM. [Issue the manufactured header into final assembly](../engineering-production-material-issue.md) to avoid treating both loose and incorporated material as available stock; this action is now implemented and locally tested.
