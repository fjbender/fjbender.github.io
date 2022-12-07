# Server to Server Credit Card Tokenization


# Server to Server Card Tokenization with Mollie
Mollie in theory has an endpoint for server-to-server card tokenization. In order to have this explained to you, you have to provide a valid PCI DSS Attestation of Compliance. 

This endpoint is, unfortunately, not suitable for card tokenization from an App context, as the `Access-Control-Allow-Origin` [HTTP Header](/technology-fundamentals-http/) prevents this (I think, I don't really do frontend). See the Further Reading sections for In-App alternatives.

Upon further consideration, it might certainly be possible to ignore the `Access-Control-Allow-Origin` Header in App contexts - someone would need to try this out and tell me about it.

## Further reading
* Mollie Demo Checkout for iOS: https://github.com/mollie/demo-checkout-ios
* Mollie Demo Checkout for Android: https://github.com/mollie/demo-checkout-android
* Mollie Demo Checkout Backend: https://github.com/mollie/demo-checkout-backend
