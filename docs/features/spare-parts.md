# Spare parts

The **Spare Parts** tab records parts recommended for startup, maintenance or another service period. These requirements are separate from the assembly's build BOM: they do not increase BOM quantities, BOM cost rollups or production requirements.

An item definition supplies the recommendations. Independently owned project occurrences can inherit those recommendations and override them for their installation conditions.

!!! note "Availability"
    This feature requires the spare-parts application update, its database migration and Engineering setup. An administrator can then configure the categories, properties and tabs. The names on this page are the seeded defaults; your workspace may use different labels.

## Service periods are configurable

**Service Period** is a selection property on each spare-part requirement. Engineering setup initially offers **Startup**, **1-year maintenance** and **2-year maintenance**. These are editable options, not fixed columns or start/end dates.

Administrators maintain the option values, selected text and list descriptions under **Configuration → Property Definitions**. Engineers select the applicable period for each requirement. Agree in the option descriptions whether a period means cumulative stock for that duration or a particular maintenance event; MatStream does not infer that meaning from its name.

## Define the recommended spare parts

1. Open the part, assembly or other configured item in the **Library**.
2. Select **Spare Parts**.
3. Choose **Add from Library** and select the item required as a spare.
4. Enter **Service Period**, **Spare Quantity**, **Spare Unit** and any **Spare Notes**.
5. Save the item.

Each row is a separate requirement for one referenced Library item and one service period. You can use the same part in multiple rows with different quantities or periods.

| Spare part | Service Period | Spare Quantity | Spare Unit |
|---|---|---:|---|
| Seal kit | Startup | 2 | pcs |
| Seal kit | 1-year maintenance | 3 | pcs |
| Bearing set | 2-year maintenance | 1 | set |

The requirement has its own properties. Its reference to the spare's Library item is separate from its relationship to the owning assembly.

## Override recommendations for a project

Open the independently owned part or assembly occurrence in the project and select **Spare Parts**. MatStream creates separate project requirement rows linked to the definition's recommendation rows, without copying the inherited property values into local overrides.

For example, the definition recommends two startup seal kits, but a desert installation needs four:

| Installation | Recommended Quantity | Local Spare Quantity | Effective Spare Quantity |
|---|---:|---:|---:|
| Standard project occurrence | 2 | Empty | 2 |
| Desert project occurrence | 2 | 4 | 4 |

The grid shows the effective **Spare Quantity**. **Recommended Quantity** shows the source value, and **Quantity Overridden** identifies a local quantity override.

MatStream uses its existing property inheritance rules:

- Enter a local value to override the recommendation for this occurrence.
- Clear the local value to inherit again.
- Enter **0** to indicate that none are required for this occurrence. Zero is a real override, not an empty value.
- Add a row from the Library to create a project-only requirement with no source recommendation.

The definition and other project occurrences retain their own values. The same inheritance mechanism can apply to Service Period, Spare Unit and Spare Notes, according to their category-property settings.

An inherited recommendation cannot be removed through **Remove** on the project tab. Set its quantity to zero instead. Definition recommendations and project-only rows can be removed, subject to permissions and version rules.

!!! warning "Shared inherited BOM lines"
    Independent spare requirements currently apply to project-owned occurrences. A shared inherited BOM line must first become a project copy through MatStream's existing project-copy workflow. Until then, it has no independent spare requirement rows and is excluded from the project spare-parts list. Do not treat that list as complete for an installation containing uncopied shared inherited lines.

## Definition changes and source revisions

Inherited values follow the applicable source revision; local overrides remain in place. Pinned references retain their selected source revision. Changing the source reference between pinned and following modes reconciles the project requirement while preserving its local values and earlier requirement versions.

If a recommendation is removed from the definition, the project requirement remains and **Source Recommendation Removed** is shown. Review that row rather than assuming its demand has been cancelled automatically. Project-only requirements have no source recommendation to follow.

## Create a project spare-parts list

Engineering setup provides a **Project Spare Parts List** filter category. It is a live filter folder targeting **Spare Part Requirement** occurrences with **Parent subtree** scope.

1. In Explorer, select the project folder that should contain the list.
2. Choose **New**, open **Filter Items** and select **Project Spare Parts List**.
3. Name and save the filter folder.
4. Enter the folder to view the matching project requirements.
5. Use the column chooser to include the service period, effective quantity, unit, owner and project total.

The filter scopes itself to its parent. Place it directly beneath the project for a project-wide list, or beneath a project subfolder for a narrower list. A Parent subtree filter without a usable parent placement matches nothing.

Although spare requirements are outside the BOM, the filter finds them through their owning project occurrences. It includes inherited effective values, local overrides and project-only requirements. Library recommendations are not added as duplicate project demand. You can filter by the effective Service Period or Spare Quantity. Zero-quantity rows remain visible unless you add a criterion to exclude them.

### Quantities in the list

| Column | Meaning |
|---|---|
| **Requirement Owner** | The item or project occurrence owning the requirement |
| **Spare Quantity** | Effective spare quantity per installed owner instance |
| **Recommended Quantity** | Source recommendation before a local quantity override |
| **Quantity Overridden** | Whether a source-backed requirement has a local quantity value |
| **Installed Quantity** | Product of the owner's current placement quantities through its structural ancestry |
| **Total Required Quantity** | Effective Spare Quantity × Installed Quantity |
| **Source Recommendation Removed** | The original recommendation is no longer present in the owner's applicable definition |

For three installed pumps with an effective startup requirement of four seal kits per pump, **Spare Quantity** is **4**, **Installed Quantity** is **3**, and **Total Required Quantity** is **12**.

Each requirement remains a separate row with its owner and service period. The list does not automatically consolidate identical parts or convert units. When making a consolidated purchasing list in Excel, group compatible units and service periods deliberately.

## Export the list to Excel

- From the live filter folder, use the Explorer **Export → Excel** action.
- From a **Filter Result** display tab, use **Export Excel** above the results.

The export retrieves the full matching result set across pages, using the selected columns and their headings. It exports effective project values and numeric quantities; part numbers remain text. Normal read permissions apply.

The export limit is **100,000 rows**. If the limit is reached, MatStream reports that the file is incomplete. Narrow the filter and export again when a complete list is required.

## Administrator configuration

Engineering setup supplies these starting definitions:

| Configuration | Seeded default |
|---|---|
| Requirement category | **Spare Part Requirement**, family **Occurrence** |
| Editable properties | **Spare Quantity**, **Service Period**, **Spare Unit**, **Spare Notes** |
| Quantity property purpose | **Requirement Quantity** |
| Property inheritance source | **Source requirement** |
| Shared display-tab definition | **Spare Part Requirements**, displayed as **Spare Parts** |
| Uses relationship | **Requirements outside the BOM** |
| Host categories | Part, Assembly, Weld Assembly, Equipment, Instrument and BOM Position |
| Project report | **Project Spare Parts List**, a Filter Item folder using **Parent subtree** |

Use the property-definition editor to change labels, descriptions, service-period options and unit suggestions. In the category's **Properties** settings, configure inheritance, required/read-only settings and property access. Assign **Requirement Quantity** to the numeric property used for demand totals; its label does not determine its role.

In **Configuration → Display Tabs**, choose **Uses** and **Requirements outside the BOM**, set the default occurrence category and configure the permitted Library definition categories, row categories, columns and ribbon actions. Requirement rows are added from the Library; structural **New**, file creation and the read-only definition-row inheritance setting are not used for this relationship. Configure category-tab assignments and permissions separately.

Rerunning Engineering setup preserves existing property settings, service-period options, tab configuration and report configuration. Existing renamed requirement categories, tabs and reports are reused through their configured relationships and property references.

## Related

- [Uses & Where Used](where-used.md)
- [Filter Items & Snapshots](filter-items.md)
- [Display Tabs](../admin/display-tabs.md)
- [Property definitions](../admin/properties.md)
- [Workspace setup](../getting-started/workspace-setup.md)
