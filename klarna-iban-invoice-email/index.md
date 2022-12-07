# Klarna Iban Invoice Email


# What does the Klarna invoice email look like

Many merchants ask about the consumer experience when using Klarna invoice, specifically how the consumer recieves the IBAN and bank details.

We will not return the IBAN on API level (as e.g. RatePay does), as Klarna does not follow this type of white label approach. They want to position their brand directly towards the consumer.

At a minimum, the consumer will receive an email that looks like this

![](/images/klarna-1.jpeg)
![](/images/klarna-2.jpeg)
![](/images/klarna-3.jpeg)

Additionally, if the consumer uses the Klarna App, they will find their due payment there as well, and can also choose to pay via Direct Debit, move the due date, pay in installments, and much more.

There is no need to print the IBAN from Klarna on the merchant invoice. This will only confuse consumers, as they are typically used to this. Merchant should clearly state on their invoices that payment is needs to go to Klarna, and not the merchant's bank account.
