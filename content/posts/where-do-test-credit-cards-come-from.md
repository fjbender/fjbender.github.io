---
title: "Where Do Test Credit Cards Come From"
date: 2023-01-02T08:40:35+01:00
draft: false
---

# Where Do Test Credit Cards Come From?
After an interesting discussion [in the Discord](https://discord.com/channels/1037712581407817839/1037752718149820536/1054673350087168020) (you can [sign up here](https://discord.gg/CHPsxAAMzB)), I wanted to look a bit more into where Test Cards even come from.

## tl;dr
They are proprietary per payment gateway. 

## And now what?
Assuming you're familiar with the [three and/or four party card schemes](https://www.brimstone-consulting.com/three-and-four-party-card-schemes), you might be asking yourself, which of the parties involved controls the test cards. Rolling the process up backwards:
* The schemes/licensors: Would make sense. It's where basically everything ends up at some point anyway, so why not have centralized test cards per brand/licensor?
* Issuer: Could also be, although every issuer (_vulgo_ bank) would need to create their own cards, so downstream actors would need to know which card goes to which issuer (they know that, in theory, though).
* Gateway and/or Acquirer: First (or second) in line, so they could handle test cards efficiently, so upstream actors don't need to be bothered with test stuff

And the more you think about it, the more sense it makes to have test cards defined on a gateway level. Many test cards include a certain semantic about expected transaction state (e.g. by convention `4242 4242 4242 4242` or `4111 1111 1111 1111` would always create a successful transaction of sorts). Transaction state is simulated easily within the gateway's system and doesn't have to leave this context. Additionally, you might want to use test card numbers to control other behavior within the gateway (even functionalities that are not available on all payment gateways). Let's look at a few examples:
* PAYONE: They have test cards [with 3DS](https://docs.payone.com/pages/releaseview.action?pageId=6390627) and some [without 3DS](https://docs.payone.com/pages/releaseview.action?pageId=1213979), the latter set with a lot of mappings towards gateway-specific errors.
* Stripe: There's a [plethora of test cards](https://stripe.com/docs/testing#cards) available from Stripe, grouped by network/scheme, country, card type, fraud prevention logic, etc.
* Mollie: There's [one test card per network](https://docs.mollie.com/overview/testing#testing-different-types-of-cards), the transaction state is not controlled by the test number, but rather by either selecting an outcome in the payment screen, which is also quite convenient.

While it might be possible that some of the cards, if not all, work on other gateways (they usually are [technically valid](https://en.wikipedia.org/wiki/Luhn_algorithm)), a card that triggers an error on Stripe might work just fine on Mollie.
