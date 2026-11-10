# Suppliers, clients and addresses

Use **Supplier** records for businesses you buy from and **Client** records for businesses you sell to. The Setup Wizard creates separate Definition categories for them. **Company** remains available for your own company and parties that have not been classified.

## Create a supplier or client

1. Open **Library → New**.
2. Choose **Supplier** or **Client**.
3. Enter a name, complete the contact and commercial details, and save.

If a business has both roles, create two records. Library names must be unique across the workspace, so use distinct names such as **Acme (Supplier)** and **Acme (Client)**. Each record has its own contacts, addresses, documents and history.

The standard **Vendor** picker and an item's **Suppliers** tab search Supplier records. The **Client** picker searches Client records. Document-control party pickers can include Company, Supplier and Client. Administrators can change these filters in the relevant property definition or display tab.

## Named addresses

Open the party's **Addresses** tab and choose **New**. Choose a suggested address name or type a descriptive label, such as **Main Address**, **Warehouse** or **Invoice Address**. The label helps colleagues distinguish locations; it is separate from the postal address.

Complete Street, House Number, Address Line 2, Postal Code, City and Country. **Address** can hold a complete formatted address instead; filled Address text takes precedence over the structured fields. Set **Default for Delivery** and/or **Default for Invoicing** where appropriate.

Setup seeds **Office** as Address Type and **Netherlands** as Country. An administrator can change the choices in **Configuration → Property Definitions**, and change the defaults in **Configuration → Categories → Company Address → Properties**. Suggested names and naming guidance are configurable on the category's **General** tab.

Company Address records are direct occurrences owned by the party. Setup makes them visible in Library search. Filter the Library to **Company Address** and use **Parent** and **Path** to identify the party that owns each result. See [Library search](library.md#finding-occurrences).

## Upgrade an existing workspace

After deploying the updated applications, rerun the applicable configuration in **Manage → Setup Wizard**. The final **Separate suppliers and clients** step creates the two categories from the shared company configuration and adds missing properties and tabs. Each category can then be configured independently through **Configuration → Categories**. Keep its Supplier or Client business role when changing the display name.

Existing Company records marked only **Is Supplier** or only **Is Client** move to the corresponding category. Their entity IDs, contacts, addresses and existing links stay attached. Records marked **Is Own Company** stay in Company.

A Company marked as both client and supplier is preserved and listed in the setup warnings. Create separate records and review its sales and purchasing references before moving links to the appropriate record. Unclassified companies also stay in Company.

Setup upgrades recognizable standard picker filters. Custom administrator filters remain unchanged; review them if they still search only Company. Existing category settings and property overrides are preserved when setup is rerun.

## Related

- [Workspace setup](../getting-started/workspace-setup.md)
- [Categories](../admin/categories.md)
- [Procurement](procurement.md)
- [Sales Orders](sales-orders.md)
