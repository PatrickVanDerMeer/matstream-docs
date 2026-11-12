# Item-change subscriptions

Subscribe to items when you want to hear about state changes, new versions or revision changes. These subscriptions watch entity changes; they are separate from your MatStream billing plan.

## Subscribe from Explorer

1. Select one or more items in **Explorer** and choose **Subscribe**.
2. Under **Tell me when**, choose **State changes**, **New versions**, and/or **Revision changes**.
3. Under **Tell me by**, select **Task in My Tasks**, **Email**, and/or **In app**. At least one change type and one delivery method are required.
4. Set **Until**, or select **No expiry**.
5. Optionally select **Stop after the first reported change**, **Also report my own changes**, and enter a note.
6. Click **Subscribe** and review the result, especially for a multiple-item selection.

Creating a subscription requires `subscription.create`. With `subscription.assign`, the **For** picker lets you subscribe another workspace person. The subscriber must be able to read the watched item; a subscription does not grant item access. New-version subscriptions can generate frequent reports for active designs.

## Notification bell (in-app delivery)

When a subscription delivers **In app**, the message lands in the **bell** icon in the page header.

- A red badge shows how many notifications are unread (capped at **99+**). MatStream checks for new ones about once a minute, and again when you switch workspace.
- Click the bell to open **Notifications**, your 50 most recent. Unread ones are highlighted; each shows a subject, an optional description and the time.
- Click a notification to mark it read and open the item in [Explorer](explorer.md).
- **Mark all read** in the dialog's footer clears the badge at once. It appears only while something is unread.

## See who is subscribed to an item

Open the item's details dialog and use the **Subscribers** button on the **Subscribe** ribbon tab. The **Subscribers** dialog lists, for the item:

| Column | Meaning |
|---|---|
| Person | The subscriber, and *Added by* when someone else subscribed them |
| Changes | The change types watched — state changes, new versions, revision changes — and *first only* when the subscription stops after the first report |
| Delivery | How they are told: task, email or in app |
| Until | The expiry date, or *No expiry* |

If nobody is subscribed, the dialog says so. Listing subscribers requires read access to the item.

## Manage your subscriptions

Open **My Subscriptions**, including from the Dashboard's **Watching** tile. The list groups subscriptions created together and shows change types, delivery methods, expiry, status and report count.

- **Cancel** stops an active or expired subscription; **Cancel batch** handles a group together.
- **Renew** or **Renew batch** renews expired subscriptions and requires `subscription.create`.
- **Retry** appears when delivery needs attention. Read the error and address its cause before retrying.

The Watching tile lists active subscriptions only. Use the full page to inspect expired, cancelled or completed entries. Notifications are processed by background delivery; a saved subscription is not proof that an email has already arrived.

## Related

- [Dashboard](dashboard.md)
- [Explorer](explorer.md)
- [Tasks](tasks.md)
