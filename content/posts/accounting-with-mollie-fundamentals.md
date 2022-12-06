---
title: "Accounting With Mollie Fundamentals"
date: 2021-10-21T11:55:19+01:00
draft: false
---

# Accounting with Mollie

In the following, I will explain how, from my point of view, accounting for Mollie payments should be done by default (my accounts are only borrowed from SKR04 as an example)


## Chart of accounts
1. 4000 Income
1. 69999 Customer
1. 1300 Receivables from Mollie
1. 1460 Payments in transit
1. 1700 Bank
1. 6850 Costs of monetary transactions

![](/images/accounting-1.png)

### Import of orders and payments (accounts receivable), triggered by the event "New order"
Import of orders from Shop/ERP: Here a posting record Debtor to Revenue is created, for example: 100€ 69999 to 4000:
![](/images/accounting-2.png)

Import of payment from Mollie (here it is important that the payment has also already been made, so there is also a capture at Klarna): Receivables against Mollie to debtor, 100€ 1300 to 69999:
![](/images/accounting-3.png)

The debtor is now credited and the receivable against Mollie has increased.

Of course, this is now repeated several times for all sales and payments.

### Import of a settlement, triggered by the event "Mollie creates a settlement"
Retrieving the settlement from the API, here a posting record Payment in transit to Receivables against Mollie is created, for example 95€ 1460 to 1300:
![](/images/accounting-4.png)

So now we expect a payment receipt from Mollie on the bank in the amount of 95€. The missing 5€ are exemplarily the fees that Mollie has withheld in the context of this payment. However, these are not yet fixed, since we have not yet received a receipt from Mollie for the fees.

The payments or captures contained in the settlement can be retrieved and reconciled via the corresponding endpoints below the settlement.

This is now repeated whenever Mollie generates a new settlement according to the payout frequency.

### Posting of the Mollie disbursement, triggered by the event "A Mollie disbursement transaction is in bank import"
Here, a posting record bank to disbursement in transit is created, for example €95 1700 to 1460:
![](/images/accounting-5.png)

So the expected settlement is now posted, we do not expect any further payment from Mollie until a new settlement is generated.

### Posting of payment fees, triggered by the event "Mollie issues an invoice" on the first business day of a month for the period of the previous month

Retrieving the invoice via the API or the PDF document from the dashboard. This is where the posting record Cost of Money Transactions to Receivables against Mollie is created, for example €5 6850 to 1300:
![](/images/accounting-6.png)

Most likely, there is also an "Invoice Compensation" line item on the invoice that adjusts rounding differences in your favor when calculating the charges.
