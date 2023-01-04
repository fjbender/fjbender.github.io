# Replay Failed Webhook Calls


# Replay Failed Webhook Calls

Sometimes it happens that certain [webhook calls](https://docs.mollie.com/overview/webhooks) fail and the merchant doesn't really know why. We don't log the response to the webhook call anywhere, but anyone can replay the webhook payloads (including the merchant themselves) to see any eventual error messages that might come back. Fire up a Terminal and run `curl` like so:

```
$ curl -vv -d "id=ord_2yr3ld" -X POST https://backend.sporkify.net/webhooks/mollie-order/5c46d208c6513
Note: Unnecessary use of -X or --request, POST is already inferred.
*   Trying 172.67.9.147:443...
* Connected to backend.sporkify.net (127.0.0.1) port 443 (#0)
* ALPN: offers h2
* ALPN: offers http/1.1
*  CAfile: /etc/ssl/cert.pem
*  CApath: none
* (304) (OUT), TLS handshake, Client hello (1):
* (304) (IN), TLS handshake, Server hello (2):
* (304) (IN), TLS handshake, Unknown (8):
* (304) (IN), TLS handshake, Certificate (11):
* (304) (IN), TLS handshake, CERT verify (15):
* (304) (IN), TLS handshake, Finished (20):
* (304) (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / AEAD-AES256-GCM-SHA384
* ALPN: server accepted h2
* Server certificate:
*  subject: C=US; ST=California; L=San Francisco; O=Cloudflare, Inc.; CN=sporkify.net
*  start date: Mar  8 00:00:00 2022 GMT
*  expire date: Mar  8 23:59:59 2023 GMT
*  subjectAltName: host "backend.sporkify.net" matched cert's "*.sporkify.net"
*  issuer: C=US; O=Cloudflare, Inc.; CN=Cloudflare Inc ECC CA-3
*  SSL certificate verify ok.
* Using HTTP2, server supports multiplexing
* Copying HTTP/2 data in stream buffer to connection buffer after upgrade: len=0
* h2h3 [:method: POST]
* h2h3 [:path: /webhooks/mollie-order/5c46d208c6513]
* h2h3 [:scheme: https]
* h2h3 [:authority: backend.sporkify.net]
* h2h3 [user-agent: curl/7.84.0]
* h2h3 [accept: */*]
* h2h3 [content-length: 13]
* h2h3 [content-type: application/x-www-form-urlencoded]
* Using Stream ID: 1 (easy handle 0x7ff03a80c600)
> POST /webhooks/mollie-order/5c46d208c6513 HTTP/2
> Host: backend.sporkify.net
> user-agent: curl/7.84.0
> accept: */*
> content-length: 13
> content-type: application/x-www-form-urlencoded
>
* We are completely uploaded and fine
* Connection state changed (MAX_CONCURRENT_STREAMS == 256)!
< HTTP/2 403
< date: Wed, 04 Jan 2023 16:06:25 GMT
< content-type: text/plain; charset=UTF-8
< content-length: 16
< x-frame-options: SAMEORIGIN
< referrer-policy: same-origin
< cache-control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
< expires: Thu, 01 Jan 1970 00:00:01 GMT
< server: cloudflare
< cf-ray: 784538aa4c867276-HAM
<
* Connection #0 to host backend.sporkify.net left intact
error code: 1337
```

We might still not know what the error code means in all cases, but at least that's info to move forward with.

Note that this doesn't work if the merchant has some form of [Basic Auth](/webhook-401-htaccess) in front of their shop.
