# Comparison of Wallet Technology on the Web

Status: Draft by Ian Jacobs

Digital wallets for payments are widely used around the world. Juniper Research [reports](https://www.juniperresearch.com/press/over-two-thirds-of-the-global-population-to-own-a-digital-wallet-by-2029/) that by 2029, two-thirds of the global population will have a digital wallet. 

During checkout online, one way to support interaction with a digital wallet for payment is to redirect the user away from the merchant Web site, to another Web site or native app or even the operating system. This is generally considered a poor user experience, and merchants generally prefer that users remain at their site rather than leave it to make a payment.

A number of Web technologies are being used (or should soon be available) to support digital wallet interactions where the user stays “within” the merchant context. All of them involve modal windows/overlays in some capacity:

* JavaScript and a popup window (e.g., PayPal express, some implementations of EMV&reg; SRC). Note that on mobile, the popup window appears in a new tab, so the user experience is very similar to a redirect.
* [Payment Request](https://github.com/w3c/payment-request/) in conjunction with a payment app (native or Web-based payment handler)
* In development: [Digital credentials API](https://www.w3.org/TR/digital-credentials/) (see, for example,  [Chrome origin trial info](https://developer.chrome.com/blog/digital-credentials-api-origin-trial))
* In development: [Facilitated Payment Link Type in HTML](https://wicg.github.io/paymentlink/)

Below we compare some characteristics of these approaches.

## Considerations that may impact capabilities

### Does data need to flow from the wallet to the merchant via the browser?

Some payment flows typically involve the exchange of data between the user and the merchant, via the browser. Examples include providing credit card, debit card, or bank account information to the merchant (or their payment service provider), or a payment wallet that is able to provide information beyond the payment instrument itself (e.g., a user’s chosen shipping address). In other cases, full payment processing is done in this page, or information is returned which the merchant can submit on an Payment Authorization channel.

Other payment flows typically involve “backend” integration between a payment system and the merchant. In these cases, the payment flow typically involves the user interacting with a bank or other payment service provider to initiate a payment, which is then carried out without needing to return information via the browser to the merchant. Merchants typically receive payment status information via the backend.

There is some correlation between these two user flows and what are called “pull payments” (initiated by the merchant with user-provided information) and “push payments” (initiated by the user with merchant-provided information). We make the connection here because it may be possible to optimize the user experience (generally) for the two approaches to payments using different API designs.

### How does the browser facilitate handing off the flow to the user’s wallet?

In-store payment experiences involve a variety of transport layers to support interactions between a merchant device and a user device:

* Card readers (magnetic stripes, chip readers)
* NFC (e.g., mobile wallets, contactless payments with cards)
* QR codes

When shopping online, cross device handoffs may be necessary but are not considered a good user experience. In some cases, same-device handoffs can also be painful (e.g., a QR code displayed in a mobile browser that must be scanned by the same device to complete payment).

It is thus desirable for a browser capability to minimize the friction of transferring the user from the merchant context to the wallet context (whether Web-based or native).

### How does the browser navigate the tension between user experience and privacy?

One way to reduce payment friction during checkout is to simplify the choice of payment methods shown to the user to those that are readily available. On the one hand, payment method providers likely want their brand to be visible, but in some cases, they don’t want the user to experience friction with their brand during checkout (e.g., by having to set up a payment app). If the merchant can query the user’s environment silently in order to determine which brands to offer, this can raise privacy issues (cf. the [W3C Statement: Principles for Privacy on the Web](https://www.w3.org/TR/2025/STMT-privacy-principles-20250515/#principles-for-privacy-on-the-web))

Several Web APIs handle this situation by presenting options to users in “browser-owned UX,” so that the user can consent to a particular choice without silently sharing information with either the merchant or other party. Examples include:

* FedCM
* Credential Management API
* Facilitated Payment Link Type in HTML

## Comparison of APIs (as of August 2025)

|                | JS/popup  | PR/PH APIs | DC API  | Facilitated payment link |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| Front-end data flow to merchant supported?  | Yes | Yes | Yes | No |
| Wallet implementation on same device  | Only Web | Web (via PH API) and Native| Only Native (for now) | Only native (for now) |
| Payment method availability shared silently with merchant before consent to pay? | No | Yes (via canMakePayment) | No | No |

## Use case support observations

### Payment Request API

* Particularly suited for scenarios where the merchant site wishes to pass custom data to the payment app and receive a response (via the browser) from the payment app.
* Can work with both Web apps (via Payment Handler) and native payment apps (when supported by browser and mobile OS)
* Requires merchants to integrate with the Payment Request API via imperative JavaScript calls, handling logic, state, returned JS promises, etc.

### Facilitated payment link

* Does not support data flow through the browser back to the merchant. 
* Particularly well-suited for push payments. 
* This can simplify life for developers, who can simply declare in the head of the HTML document which wallets the merchant supports.
* The browser offers relevant wallets to the user, possibly using both context of the site and the user’s available/linked wallet. The exact UX may differ on desktop and mobile. This approach has several benefits:
   * The user does not have to scan a QR code from a page displayed in a mobile browser.
   * The merchant website does not learn whether or not a payment app has been presented to the user by the browser until the user selects a wallet.
   * The payment app does not learn that a merchant is trying to initiate a payment until the user selects a wallet.

