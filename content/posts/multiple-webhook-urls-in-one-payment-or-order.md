---
title: "Multiple Webhook Urls in One Payment or Order"
date: 2023-01-17T08:37:09+01:00
draft: false
author: "Florian Bender, Savannah van Lit"
---

# Multiple Webhook URLs in One Payment or Order

## The problem for merchants

Sometimes, a merchant wants to inform multiple systems about the status changes (or the inception) of a payment (or order - we use payment synonymously throughout this article).

For instance, the merchant might use a SaaS Order Management System (OMS) and Shopware 6. Whenever a new payment reaches the state of `paid`, the merchant wants this information to go into their SaaS OMS, where they then pull the payment and use the information in the `metadata` field to pull the order from the Shopware API. This approach is beneficial, because the merchant doesn't have to write complicated export logic in Shopware, but rather just pulls orders that definitively are paid.

This, however, does not work currently. The merchant might overwrite the `webhookUrlGenerator` component in the Shopware 6 plugin to divert webhooks to their SaaS OMS, but that would break a lot of functionality in Shopware, as the webhook is not available there anymore.

## The problem for technology integrators

With the increasing focus on technology (SaaS) integrations, the restrictions caused by webhooks not calling to multiple platforms have become more obvious. Consequently, platforms not creating transactions are left without real-time updates on transactions, customers, subscriptions and more. Examples of platforms that need real-time data updates within the SaaS landscape are: 

* Marketing Automation tools (Active Campaign, Klaviyo)
* Workflow automation systems (Zapier, Make)
* CRM-systems (Freshworks, Hubspot)

At present, if we were to build a non-transaction creating Technology integration we would be forced to construct a (mirror) middleware database as a workaround. The needed Mollie data (transactions, customers etc.) would be stored within this data base. By means of the List function within our API the data would be updated every X amount of time, depending on the need of the integration. 

Although this might be a temporary solution, this set up has the following drawbacks: 

Increased Security risk - As we are currently not able to host the mirror data bases within Mollie we are obliged to host the databases with a third party (third party integrator or contractor of third party integrator). This set-up increases the risk of a data breach and therefore reputation risk of Mollie. "Mitigation of this risk is executed carefully and diligently, but that uses time and resources and slows down the process.

No real-time updates - As the amount of data that is processed within a List request is too great to do an (almost) constant pull request, the requests are carried out within certain timeframes. Consequently, the data shown within the integration and possible actions depending on the changes of data will always have a lag. 

Strenuous use of Mollie data base - Due to the fact that pulling targeted updates is not possible, we are compelled to update all available data via a List request. This results in unnecessary use of the Mollie data base. 

Fall-off of historic data due to pagination - As the Mollie API employs Pagination with the List request, we are restricted to updates of the most recent 250 transactions within an organisation. This limits the quality of our integrations for larger customers.

## A workable solution (today)

Enter [MWM2](https://github.com/fjbender/mwm2). MWM2 is the second iteration of the Mollie Webhook Multiplexer. It's a simple PHP/Symfony application, that reads in a configuration file containing a list of endpoints that should receive webhooks from Mollie. Users can configure whether path or query parameters should be preserved, and which HTTP Verb should be used (`POST` is very likely). The merchant then reconfigures the `webhookUrl` for their payments to point towards MWM2, and MWM2 clones and sends the webhooks to multiple systems described in the config file. The application is written in such a way that it's easily extendable, for instance if the user wants to manipulate URLs or webhook data according to other variables.

The application uses [Symfony Messenger](https://symfony.com/doc/current/messenger.html) to put webhooks into a RabbitMQ, Redis, SQL, or whatever queue so they don't need to be process synchronously (which considerably speeds up inbound webhook processing, and also ensures ultimate delivery, retries, and more for target systems). See the repository's `README.md` for details.

## A workable solution (tomorrow)

The limitations described above can be solved by introducing a Hooks or Events API to our current API offering. Put simply, a Hooks API would list all events occurring within the Mollie account that would trigger a webhook. By subscribing to this endpoint, the merchant or partner will be able to detect all important updates within their account and build the necessary automations within their platform, instead of constantly pulling potentially empty endpoints. Additionally, a Hooks or Events API (similar to those of Stripe and Adyen) is not something new; looking at our competitors, we can consider this to be a market standard. 

For the actual implementation within Mollie there are multiple possibilities. Here are a few examples: 

1. *Events:* 
An Events API will list all events within the Mollie account that would trigger a webhook. This API will list all events without any filtering from Mollie’s side. The event will contain parameters that will make it possible for the merchant to categorise. For example, a payment paid will generate a payment.paid. Using the events parameters, the customer is able to create customised pull requests or any business logic necessary. 


2. *Events with filtering on Mollie side:*
Similar to the permission scope settings that can be created within the Mollie dashboard, the Events API could also be implemented with business logic on Mollie's side. The setting will create a filtering on organisation or profile level for events listed when the merchant calls the events API. 

3. *Webhook sent to multiple addresses:* 
Creating the possibility to set up multiple webhooks is important. Here it is also important to add the possibility for the customer to manually set up webhooks or within the connect flow of a new integration to set up a new webhook for certain needed events. 

It should be noted that simply accepting multiple values will not work as there will be scenarios where software A would create the payments/customers etc. but software B would be pulling the data and would need the updates. Software A should not be responsible for creating the callback to software B.
