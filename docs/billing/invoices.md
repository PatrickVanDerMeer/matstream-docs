# Invoices

MatStream generates an invoice for every billing event. Invoices are 
sent to the billing email address on your organisation profile.

---

## Viewing your invoices

1. Open **MatStream Manage**
2. Go to **Billing** → **Invoices**
3. Your invoice history is listed with the invoice number, period, 
   and total amount

---

## Downloading a PDF

Click **Download** next to any invoice to save a PDF copy. The PDF 
includes your billing address, VAT number (if applicable), and a 
full breakdown of line items.

---

## Invoice timing

| Event | When invoice is generated |
|---|---|
| First payment (upgrade) | Immediately on upgrade |
| Monthly recurring | 7 days before your billing date |

You receive the invoice by email as soon as it is generated. For 
recurring invoices, this gives you 7 days to review before the charge 
is processed automatically.

---

## Invoice statuses

| Status | Meaning |
|---|---|
| Draft | Generated, awaiting the charge date |
| Paid | Payment confirmed |
| Failed | Payment could not be processed — see below |

---

## Failed payments

If a payment fails (for example, an expired card), MatStream will:

1. Send you an email immediately with a link to retry the payment
2. Send a reminder after 3 days if still unpaid
3. Suspend your workspace after 7 days
4. Archive your workspace after 30 days if payment is still not received

To retry a payment, click the link in the failure email. You will be 
taken to the payment page where you can complete the charge with an 
updated payment method.

!!! warning
    If your workspace is suspended, your team loses access to MatStream. 
    Settling the outstanding invoice immediately reactivates the workspace.

---

## VAT invoices

If you have a valid EU VAT number registered on your organisation 
profile, your invoices will show:

- Your VAT number
- MatStream's VAT number
- VAT applied at 0% with the note: *"VAT reverse charged — Art. 196 EU VAT Directive"*

See [Organisation settings](organisation.md) to add or update your VAT number.