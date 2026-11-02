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
