---
title: "Multiple Webhook Urls in One Payment or Order"
date: 2023-01-17T08:37:09+01:00
draft: true
author: "Florian Bender, Savannah van Lit"
---

# Multiple Webhook URLs in One Payment or Order

## The problem for merchants

Sometimes, a merchant wants to inform multiple systems about the status changes (or the inception) of a payment (or order - we use payment synonymously throughout this article).

For instance, the merchant might use a SaaS Order Management System (OMS) and Shopware 6. Whenever a new payment reaches the state of `paid`, the merchant wants this information to go into their SaaS OMS, where they then pull the payment and use the information in the `metadata` field to pull the order from the Shopware API. This approach is beneficial, because the merchant doesn't have to write complicated export logic in Shopware, but rather just pulls orders that definitively are paid.

This, however, does not work currently. The merchant might overwrite the `webhookUrlGenerator` component in the Shopware 6 plugin to divert webhooks to their SaaS OMS, but that would break a lot of functionality in Shopware, as the webhook is not available there anymore.

## The problem for technology integrators

(Savannah's research goes here)

## A workable solution (today)

Enter [MWM2](https://github.com/fjbender/mwm2). MWM2 is the second iteration of the Mollie Webhook Multiplexer. It's a simple PHP/Symfony application, that reads in a configuration file containing a list of endpoints that should receive webhooks from Mollie. Users can configure whether path or query parameters should be preserved, and which HTTP Verb should be used (`POST` is very likely). The merchant then reconfigures the `webhookUrl` for their payments to point towards MWM2, and MWM2 clones and sends the webhooks to multiple systems described in the config file. The application is written in such a way that it's easily extendable, for instance if the user wants to manipulate URLs or webhook data according to other variables.

The application uses [Symfony Messenger](https://symfony.com/doc/current/messenger.html) to put webhooks into a RabbitMQ, Redis, SQL, or whatever queue so they don't need to be process synchronously (which considerably speeds up inbound webhook processing, and also ensures ultimate delivery, retries, and more for target systems). See the repository's `README.md` for details.

## A workable solution (tomorrow)

(Savannah's solution description goes here, if possible)