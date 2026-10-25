# Sales Order flow

This guide follows the standard **Sales Orders** setup created by the workspace wizard, from a new customer order to shipment. Your administrator may have changed the available fields, buttons, or permissions.

**Flow:** Draft → Confirmed → Pick → Pack → Shipped. If only part of the order can be delivered, use **Shipped Incomplete** and finish the remaining delivery later.

## Before you start

The **Sales Orders Configuration** must have been applied through the workspace setup wizard. It provides the order categories, lifecycle, pricing, stock locations, and purchasing configuration. Optional sample data provides articles and sample prices for trying the flow.

Your user needs the appropriate permissions for sales, warehouse, or purchasing work. Articles should have the correct stock unit, stock settings, and prices before you use them on an order.

## 1. Create a Sales Order

In **Explorer**, open **Sales → Sales Orders** and create a **Sales Order**. It starts in **Draft** and receives an order number automatically.

Enter the order date, external order number, order source, delivery address, and currency. Select the appropriate **Price List** if you use price-list pricing. The seeded order sources are **BOL.COM**, **Website**, and **Manual**; choosing a source records where the order came from.

## 2. Add the order lines

Open the order's **Lines** tab and add articles from the Library. For each line, review the quantity, unit, and unit price. MatStream calculates the line total and shows the order total at the bottom of the list.

Check the prices and delivery address while the order is still in Draft.

## 3. Review stock availability

On the **Lines** tab, open **Stock → Stock availability**. Review the required quantity, available stock, reservations, and shortages for each article.

Available stock means stock on hand minus existing reservations. Correct any unit or configuration errors before confirming the order. Reviewing this window does not itself reserve stock.

## 4. Confirm the order

Change the Sales Order from **Draft** to **Confirmed**. MatStream confirms the lines and handles their stock demand:

- **Stock items:** reserves the remaining order demand. Shortages are handled through stock replenishment; available stock can become negative.
- **Other articles:** reserves available stock and creates Material Requirements for the shortages, beneath the relevant order lines.

Confirmation records demand; it does not mean that every article is physically ready to ship.

## 5. Arrange missing stock

If there is a shortage, purchasing arranges replenishment or processes the Material Requirements, depending on the article's stock settings. Receive the purchased goods into the appropriate stock location before fulfilling the missing quantities.

Reopen **Stock availability** to review the latest situation.

## 6. Pick the articles

Use the Sales Order's **Pick** scanner action, or an authorized picking action link, to record the articles collected for the order. Check each article and quantity against the order lines.

You can pick in several batches. The picked quantity cannot exceed the ordered quantity.

## 7. Pack the delivery

Use **Pack** to record the picked articles placed in the delivery. Check the delivery address and the physical contents before proceeding.

Packing can also happen in batches, but you cannot pack more than has been picked. Picking and packing record progress; shipment is confirmed separately on the order.

## 8. Confirm shipment

Change the Sales Order state to:

- **Shipped** when every ordered quantity has been packed for shipment.
- **Shipped Incomplete** when you are shipping the packed portion and quantities remain outstanding.

MatStream records the newly shipped quantities, updates the stock records, and calculates the **Back Order Quantity**. Fully delivered lines become **Complete**; lines with outstanding quantities become **Incomplete**.

## 9. Finish any remaining delivery

For an order marked **Shipped Incomplete**, arrange the missing articles and return the order to **Confirmed** to continue fulfilment. Pick and pack the outstanding quantities, then confirm the next shipment.

Once all quantities are delivered, move the order to **Shipped**.

## Cancel an unshipped order

If an order will not proceed, change it from **Draft** or **Confirmed** to **Cancelled**. Cancellation releases its stock reservations. Do not use cancellation to undo goods that have already been shipped.
