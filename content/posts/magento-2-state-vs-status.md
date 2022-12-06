---
title: "Magento 2 State vs Status"
date: 2022-12-06T08:30:05+01:00
draft: true
---
# Magento 2: Status vs. State

Ah yes, the joy of Magento's "Status vs. State". It's actually funny, if it wasn't so sad. And also the slight dyslexia (and/or lack of command of the Latin language) of the person who wrote the response doesn't help either.

Let's think this through. Capitalized nouns indicate Magento concepts. `fixed width font` indicates a fixed technical term.

## There is State:

Orders have States. State denotes what technically currently is happening to the order (eg. `processing` meaning an Order is being prepared but isn't complete yet). There actually is a fairly simple default state machine for Order States: `new` -> `processing` -> `complete`. Those are the mandatory States. The others are there for eventualities, like waiting for async payment, or for fully refunded Orders. The States are fixed, you can't add new arbitrary ones. They trigger certain events within Magento as well, like Invoice creation. States can have a label, but that is typically only visible in the backend.

## Then there is Status (which the provider of the answer spells "states"):

A Status builds on top of the core concept of Order State, and is intended to give more information to consumers or store owners about what's happening to an Order, beyond the scope of the limited Order State Machine. It's merely a label and has no bearing on the core business processes happening to an order (e.g. you cannot generate an Invoice from an Order hitting a certain Status alone, you'd need to transition it to a State in order to trigger that. You can set Order Status conditionally, e.g. only for Orders with a selected Payment Method. Let's consider two examples:

1. Order with Credit Card: The Order is created (State `new`), we confirm the Payment, and State now is `processing`. A potential Status here could be "Payment received". The Order would get shipped and State would turn to `completed`.
1. Order on invoice (not BNPL, let's assume classic invoice payment): The Order is created (State `new`), but no payment has been made yet. Order State progresses directly to `processing`, but Status here would rather be something like "Order has been shipped". We could even read out the package delivery API and set Status to "Order delivered at dd-mm-yyyy, awaiting invoice payment", while the Order State remains in `processing`. Only when the invoice payment is received, we set State to completed.

Hope the examples illustrate why you need Status while maintaining the same State Machine.

## Further reading
* https://magecomp.com/blog/difference-magento-order-status-order-state-magento-2/
* https://experienceleague.adobe.com/docs/commerce-admin/stores-sales/order-management/orders/order-status.html

