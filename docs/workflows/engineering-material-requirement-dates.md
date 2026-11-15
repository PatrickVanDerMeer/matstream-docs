# Work Preparation — Copy planning dates to Material Requirements

The motor and pump need to be ready for assembly on **22 January 2027**. Their calculated **Need By** is **21 January**. When the release automation creates their Material Requirements (MRs), it copies that date into **When Required**.

This is a snapshot when the requirement is raised. Recalculating the project does not silently replace a date the buyer has already entered.

## Configure the automation

The Engineering Project seed installs **BOM: Create Material Requirement** in **Configuration → Automations** and assigns it to the procurement release transition. Its parameters are editable by the workspace administrator:

| Parameter | Meaning |
| --- | --- |
| MRCategoryId | The category to create, normally Material Requirement Line |
| WhenRequiredPropertyId | The date property on that MR category to receive calculated Need By, normally When Required |

IDs vary between workspaces. Choose an enabled DateTime property assigned to the selected MR category. Renaming that property does not break the ID mapping. An invalid mapping is rejected before creating an MR.

The administrator can edit the script, mappings and lifecycle assignment, or disable the automation. The supplier-routing script **BOM: Route Released Item** also accepts WhenRequiredPropertyId for its Purchase route. Existing production routing remains the same.

![Administrator-editable MR category and date-property parameters](../images/engineering-project/136-mr-date-automation-mapping.jpg)

The scripting helper is:

```javascript
matstream.actions.createMaterialRequirement(
    Number(matstream.params.get("MRCategoryId")),
    Number(matstream.params.get("WhenRequiredPropertyId"))
);
```

The second argument selects the date field. Older one-argument scripts retain the existing default WhenRequired mapping. Use the two-argument form for an explicit administrator-managed mapping and missing-planning-date validation.

## Prepare and raise the requirements

1. Follow [Plan against project milestones](engineering-milestone-planning.md). Check Supply Method, Required By Task, quantity and unit on each project BOM position.
2. Recalculate the project and review the **Procurement** view. Confirm the motor and pump each have Need By **21 January 2027**.
3. Use the configured procurement release action. The MR action recalculates or verifies the current project plan before reading the source position's result. It creates an MR only for uncovered demand. If the configured date cannot be calculated, fix the planning inputs before retrying.
4. Open **Purchasing → Pending Material Requirements (MR)**. Check quantity, project/source and **When Required**.

![Motor and pump MRs in the buyer queue with calculated dates](../images/engineering-project/133-motor-pump-mr-required-dates.jpg)

| Local example | Required quantity | When Required |
| --- | --- | --- |
| Motor ITM-00021 | 1 pc | 21 January 2027 |
| Pump ITM-00012 | 1 pc | 21 January 2027 |

![Motor MR date copied from planning](../images/engineering-project/134-motor-mr-when-required.jpg)

![Pump MR date copied from planning](../images/engineering-project/135-pump-mr-when-required.jpg)

## What happens on a retry or date change?

- **No MR yet:** create the uncovered requirement and copy the calculated date.
- **An open MR has a blank date:** reuse that MR and fill its mapped date.
- **An open MR already has a date:** preserve it, including a buyer's deliberate change.
- **An MR has already entered an order:** leave its date unchanged; create a separate MR only if further uncovered demand requires one.
- **The component is fully covered:** do not raise another MR just to carry a date.

For example, if the buyer changes When Required from 21 to **25 January**, rerunning the action preserves 25 January. Clearing the field and rerunning allows the automation to fill the current calculated date. Simply recalculating the project does not update the MR.

A late supplier promise belongs to the PO line and affects the achievable forecast. It does not automatically move an MR's required date later: the required date continues to express when the material is needed.

## Local workflow test

The local test executed the configured script through the normal scripting service for the motor and pump. It verified creation, retries without duplicates, preservation of a buyer-entered date, filling a cleared date, and rejection of an invalid field mapping. Both MRs finish this test with When Required **21 January**.

The prior motor-delay simulation had one synthetic on-order motor. That simulated quantity was reversed with a labelled compensating entry before testing uncovered MR demand. No real order, supplier message or warehouse receipt was created. The two older assembly test MRs remain separate and were not backfilled.

Setup preserves installed administrator mappings, script changes and removed transition assignments on testing reruns. Installing the new rule can adopt an unchanged legacy seeded MR release action; a customized legacy action requires review instead of being overwritten.
