---
title: "Magento 2 Webhooks of External Changes"
date: 2022-12-20T16:22:58+01:00
draft: false
---

# Magento 2: Webhooks of External Changes

Apparently external changes are not recognized corrently by the Magento 2 extension. If, for example, you have a setup according to the [ideal flow](/sequence-diagram-of-a-general-mollie-setup) and the ERP partially ships an order, those line items are not updated by the webhook Mollie then sends. Even worse, it might lead to 500 errors that both confuse Mollie technical support and merchants alike.

Updating the order state (see also [state vs. status](/magento-2-state-vs-status/)) might also create unwanted API calls towards Mollie, because an observer might trigger a capture or refund, so that's also not ideal.

An _idea_ (not implemented) would be to have some general modes in webhook handling in combinations with a downstream ERP: 

1. Leading mode: All state changes (both order lines and orders) are to be sent to Mollie as Captures/Refunds; incoming webhooks are not incorporated into state changes
1. Consuming mode: Incoming webhooks trigger updates in order line or order state; incoming state changes are purely informational

_Leading mode_ would be useful for setups where there is no direct connection between the Order Management System and Mollie. _Consuming Mode_ is, subsequently, for setups where the Order Management System drives changes at the Mollie API.

We'd need to make a plan how this ties in with connected processes, e.g. generation of invoices etc.