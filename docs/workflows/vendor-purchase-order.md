# Share a Purchase Order with a vendor

Use the vendor portal to provide the issued PO PDF, applicable documents, and required vendor document uploads through one link. The bundled **PO - Word template - A4 - Portrait** prints a QR code with the same clickable URL directly below it.

## 1. Prepare the order and documents

Create the Purchase Order and complete **Vendor** and **Supplier Email**. Use the email address of the person who will open the portal. In a practice workspace, use an email account you control.

Place the applicable documents beneath the PO or its order lines, and make sure the required vendor document rows exist beneath the relevant lines. The portal follows the shared order's document structure. A document that is only visible through a Library definition is not automatically shared; place the document under the PO or its line if the vendor needs it.

## 2. Prepare the vendor share

Use **Share** on the Purchase Order:

1. Check the recipient email. MatStream fills it from **Supplier Email**.
2. Choose the expiry and optionally add a message.
3. Click **Create link**.

Supplier access is automatic. The **Suppliers** user group controls reading items, downloading the PO and applicable documents, and uploading required vendor documents. The buyer does not select a group, access mode, categories, or properties when sharing a PO.

The link covers the PO's current document structure and its lines, including required vendor document placeholders. It does not provide Library or workspace access, or expose editable property fields. An administrator manages the Suppliers group's permissions; disabling that group or removing a permission takes effect on subsequent portal requests.

The normal Share action sends the portal invitation. Printing does not send another invitation or create extra access. Keep one suitable active share for this PO and supplier email. Expired, revoked, view-only, download-blocked, wrong-vendor, or disabled-group shares cannot be used for the printed vendor link.

## 3. Issue and generate the PDF

Move the order to **Issued**. The standard issue automation generates the PO PDF and attaches it beneath the order. It uses the prepared portal share for both the QR code and the URL. You can also select the Word PO template under **Export** and generate a PDF.

Check the PDF before mailing it: the QR code and text should point to the same portal. If generation reports a vendor-share setup problem, correct the Supplier Email, share, or external group permissions and retry PDF generation.

Mail or otherwise share the issued PDF with the vendor. Forwarding the PDF does not authorize another email address to use the portal.

For the recipient's steps, share the [supplier workflow guide](supplier-portal.md).

## 4. Vendor downloads and uploads

The vendor opens the printed URL or scans the QR code and signs in with the address named on the share. A vendor who does not yet have an account can create one through the sign-in flow.

In the portal, the vendor can:

- Open the shared Purchase Order and download its attached PO PDF.
- Browse its lines and download the applicable documents placed beneath the shared order structure.
- Open a required vendor document row and upload the requested file. A successful upload follows the existing required-document workflow, such as **Required → Submitted**.

The share does not provide access to the rest of the workspace or Library. Its visibility settings, group permissions, expiry, and revocation continue to apply after the PDF has been sent.

## Setup and template upgrades

Publish the updated **Tenant API**, **MatStream.Web.Manage**, and **MatStream.Web.Assembly**, then rerun the workspace's procurement configuration (for example **Sales Orders Configuration**). Setup creates the standard **Suppliers** group with item-read, file-download, and file-upload permissions and makes a newly created group available for external sharing. If a Suppliers group already exists, setup preserves its enabled and external-sharing settings; an administrator must enable it for portal sharing if needed. Known bundled Word PO templates are upgraded; customized Word templates are preserved. A customized template can use `%%Image:SupplierPortalQrCode%%` and `%%SupplierPortalUrl%%`, mapped to the `SupplierPortalQrCode` and `SupplierPortalUrl` header system keys respectively.

This QR block is part of the bundled Word PO template. The Excel PO template does not include the vendor portal block.
