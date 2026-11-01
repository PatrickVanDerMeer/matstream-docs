# Arrange missing stock

**Department: Procurement.** Turn demand from confirmed Sales Orders into supplier Purchase Orders. This workflow ends when the order has been placed and Warehouse has the information needed to receive it.

A **Sales Order** records what the customer wants. A **Material Requirement (MR)** records what needs sourcing. A **Purchase Order (PO)** records what is ordered from a supplier. These are linked entities with their own properties and lifecycle states; placing a PO does not receive goods or ship the customer order.

## Before you start

- Sales has [confirmed the Sales Order](../features/sales-orders.md).
- Your account has purchasing permissions and access to the relevant requirements and suppliers.
- Articles have the correct units and supplier sources. Check supplier price, currency and ordering text before placing an order.

## 1. Identify how the shortage is sourced

Open the Sales Order's **Lines** tab and choose **Stock → Stock availability**. Review Required, Available, Reserved for this line and Shortage.

| Article setting | How demand is handled |
|---|---|
| **Is Stock Item** enabled | Confirmation reserves the full remaining customer demand. Stock replenishment raises purchasing requirements according to stock levels and min/max settings. |
| Other articles | Confirmation reserves available stock and creates MRs for the uncovered quantities beneath the relevant order lines. |

!!! tip "A negative available quantity records unmet demand"
    For a stock item with 10 on hand and customer demand of 12, confirmation can reserve 12 and show Available **−2**. That is a shortage to source, not permission to dispatch goods that have not arrived.

## 2. Check replenishment settings for stock items

On the Library article's **Stock** tab, check **Stock Unit**, **Is Stock Item**, **Minimum Stock Qty** and **Maximum Stock Qty**. Both limits must be entered: minimum must be nonnegative and maximum must exceed minimum.

Replenishment runs in the background. Projected stock is **On Hand − Reserved + On Order**. If projected stock is below minimum, the requirement brings it back to maximum. Purchasing quantity can therefore exceed the shortage on one Sales Order.

For example, On Hand 10, Reserved 12 and On Order 0 give projected stock −2. With Minimum 0 and Maximum 5, replenishment requests **7**, covering the shortage and restoring the target stock.

The current Sales Orders setup provides **Warehouse → Stock Replenishment**. If a requirement does not appear, ask the administrator to check the background job, min/max settings, folder configuration and permissions. Saving article settings does not run replenishment immediately. See [Stock management](../features/stock.md#automatic-stock-replenishment).

## 3. Create the Purchase Order

1. Open **Purchasing → Pending Material Requirements (MR)**.
2. Select the requirements to buy. Check their open quantities and source information.
3. Choose **Procurement → Create Purchase Order**.
4. Review suppliers, quantities, units and any grouping of identical articles.
5. Create the orders and inspect the result, including skipped requirements and warnings.

MatStream creates one PO per vendor. Resolve missing supplier/source information for skipped requirements rather than creating an unrelated line manually. See [Procurement](../features/procurement.md) for requirement quantities and supplier selection.

!!! tip "A requirement is not a supplier order"
    An MR makes demand visible. The buyer still needs to create, review and issue the PO. Do not assume the replenishment job has contacted the supplier.

## 4. Review and issue the supplier order

Open **Purchasing → Open Purchase Orders**. Check the Vendor, Supplier Contact where applicable, Supplier Email, supplier address, quantities, prices and ordering text.

When using the bundled vendor-portal Word template, [prepare the vendor share](vendor-purchase-order.md) **before** moving the PO to **Issued**. The standard issue automation attaches the PO PDF with its portal QR code and URL. Inspect the PDF and send the issued order through your normal purchasing process.

## Result and handoff

The supplier PO is placed and the outstanding quantities are visible. Give Warehouse the PO reference and any agreed delivery information. Warehouse continues with [Receive goods into the warehouse](receive-warehouse-goods.md).

If the customer cancels, coordinate with Sales before changing supplier orders. Cancelling a customer order does not automatically cancel purchasing commitments.
