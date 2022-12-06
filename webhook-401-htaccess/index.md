# Webhook 401 Htaccess


# Webhook calls fail with 401 Unauthorized

The merchant probably is using a `.htaccess` file to restrict access to their stage environment. In order for our webhooks to get delivered, the merchant needs to add our IP addresses to the allow-list.

## How to add IP addresses to the allow list
See [here](https://stackoverflow.com/questions/7667004/htaccess-using-password-or-ip-whitelist).

## What are Mollie's IP addresses?
See [our help center](https://help.mollie.com/hc/en-us/articles/213470829-Which-IP-addresses-does-Mollie-use-From-which-IP-range-can-I-expect-requests-)
