.. _sdk-guide:

SDK Developers Guide
====================

Congratulations on embarking on the noble quest to create your own BlockChyp SDK.
We thank you and we honor you and we wish you well.

This document will give you some advice and tips about how to think the BlockChyp
way and make your noble efforts successful.

.. note::  This is the obligatory section where we encourage you not to develop your own SDK if there are already good SDKs available for your platform.  As of this writing the courteous and efficient BlockChyp staff supports Java and Javascript SDKs for your enjoyment.  More are on the way.

Simplicity First
----------------

BlockChyp's core objective is simplicity.  If you've ever done an integration with a
card present EMV payment system, you've been through hell.  These systems are -
more often than not - overly complex, poorly documented, and when you run into
trouble, straight answers from the payment companies are rare.

Our goal is the make sure developers using an SDK have to do as little as
possible.  Business logic and all the logic about how transactions are routed
should be totally encapsulated by the SDK.

A developer should be able to pass a simple payment request into your SDK, and
your SDK should automatically figure out whether or not to route the transaction
to the BlockChyp gateway or to a terminal.  When a terminal is referenced by name
or IP, your SDK should make use of the Gateway's Route API to locate the terminal
on the local network or relay the transaction to the terminal through the cloud.

Getting a Terminal
------------------

It'll be difficult to write a payment terminal integration SDK without a payment
terminal.  Sign up for a BlockChyp developer account `here <https://dashboard.dev.blockchyp.com>`_
and order a BlockChyp Developer Kit.  The kit will include a real life payment
terminal and a selection of test cards.  Don't forget to give us your T-Shirt size.

.. warning::   For the time being access to BlockChyp is by invitation only.  Contact us for an invite code.


Blockchain Stuff
----------------

Fear not if you've never done any blockchain coding before; you won't be doing
any now, either. It's true, the BlockChyp payment gateway and terminals use a ton of
blockchain stuff under the hood, but it's totally encapsulated by the gateway
and terminal firmware.  You and your developers don't need to worry about any of it.

Certain transaction types, like gift card and cryptocurrency transactions, will
return some blockchain information - like transaction hashes - but you can treat
them just like normal passthrough identifiers.


The Transactions
----------------

BlockChyp SDKs should support the following basic transactions:

- **Charge**: This is a direct capture transaction in which a transaction is authorized and captured in a single step.  Most common.
- **Preauth**: This transaction authorizes a card and delays capture.  Used a lot in hospitality transactions where the final capture amount might be different or if regulations require delaying capture until shipment.
- **Capture**: Captures a previously authorized pre-authorization. (Did that sound repetitive?)
- **Void**:  Cancels a pre-authorization.
- **Reverse**:  Executes a time out reversal.
- **Ping**: Tests connectivity with a terminal.
- **Enroll**: Collects a payment method and enrolls it in the token vault without authorization.
- **Refund**: Executes a refund.
- **Gift Activate**: Activates or recharges a BlockChyp gift card.  (Terminal Only).
- **Close Batch**: Closes the current batch. (Not required if batches auto close.)

A Basic Example
---------------

Let's start with a simple example transaction.  This will unveil a few key BlockChyp concepts we'll expand on later.

Your client application calls your SDK's "charge" function and passes in the following information, encoded here as hipster friendly JSON:

**Charge Request**::

  {
    "amount": "20.55",
    "terminalName": "Cashier #1",
  }

Note that the only required elements are amount and terminalName.  And because
this is a terminal request, we'll first need to translate the terminal name
(Cashier #1) into a network address on the local network.  This is done by
using the Gateway's route API as shown in this next example (with some standard
headers removed for clarity).

**Resolving The Terminal Route**::

  Request:

  GET /api/terminal-route?terminal=Cashier%20#1 HTTP/1.1
  Host: api.blockchyp.com
  Accept: application/json, text/plain, */*
  Authorization: Dual JTLQJNLSM4IGCKJC2MK7COW2VA:CINR73MIHX337KMRHW7BI5I2AM:7721b505f6cc4540e471d03e42388e3a5a1567b29dedf589ef881995e9ca74cc
  Nonce: MNAYY7YCXME215ZBZ96BW7D1JFPX5VBX563EGAGH4E6KA8RV8BE0
  Timestamp: 2018-11-20T18:04:25Z

  Response:

  HTTP/1.1 200 OK
  {
    "success":true,
    "terminalName":"Cashier #1",
    "ipAddress":"192.168.50.245",
    "cloudRelayEnabled": false,
    "transientCredentials": {
      "apiKey": "CINR73MIHX337KMRHW7BI5I2AM",
      "bearerToken": "JTLQJNLSM4IGCKJC2MK7COW2VA",
      "signingKey": "c7722b911f9821e742f248af8449f12f06304c18b48b902f7cdef3d9dea7ed34"
    },
    "publicKey":"112hvhQwGa22QJSuqZwdMT5BhBNcrE9pwfHzFicx4ZMLkAe6chRi",
    "rawKey": {
      "curve":"P256",
      "x":"e09f8673361cc828cda624221d5f2b517c4c4285d959e502511b531f324ece0a",
      "y":"cced17b1d95dcbcc5bf2b2f06ba4bdb4b482bd0e081ac54fb49b6db2ab40a5b4"
    }
  }

Let's unpack some of this...

The route API is a simple get request that accepts the name assigned to the payment terminal
at activation.  Most of the work here is in the headers.  All BlockChyp Gateway API
requests require a Timestamp, Nonce, and Authorization header. The **Authorization** header
uses BlockChyp's Dual Authentication format, in which the API Key and the
Bearer Token are concatenated along with the HMAC signature for the request.  Details
about API authorization headers can be found below.

The timestamp must be within 10 minutes of current universal time and the nonce
must be a random string. BlockChyp generated nonces are 32 bytes Base 32 encoded
because we think Base 32 is prettier than Base 64.
Note that the Gateway does check all nonces against recent requests for uniqueness, so
repeated nonces will cause requests to 403.

The response to a **route** request includes meta data about the terminal that can
then be used to execute direct API calls against the terminal.  The most useful piece
of information is the IP Address.  This is the terminal's IP address on the local private network.
We recommend that merchants statically configure terminal IP's, but a lot of them
just won't, so BlockChyp terminals periodically check in with the gateway and register
their current local IP Address.

Transient Credentials are also returned in the API response.  We recommend that you
pass these keys into any terminal requests as these credentials are only valid for
the given terminal and provide greater security in the event that merchants don't use
TLS on the internal network or TLS traffic is somehow compromised.

Finally, we have public key information about the terminal.  Every BlockChyp terminal
has its own unique Elliptic Curve key pair that it uses to sign transactions and API responses.
Every terminal response has a signature you can use to verify the integrity of transactions.
This can help you protect against or at least detect man-in-the-middle attacks.

Also note that you'll want to cache the route response for a reasonable period
of time.  You don't want to call route over and over for every request.  We recommend
caching route requests for at least an hour.

**Running A Transaction**

Now that you know where the terminal actually is, you can take some poor schmo's money.  This part's easy.
Just wrap the client's original request inside a Terminal Request as shown below.  Note
that the terminal API is on ports 8000 and 8443.  We highly recommend using HTTPS
on port 8443.

**Terminal Transaction Example**::

  Request:

  POST /api/charge HTTP/1.1
  Host: 192.168.50.245

  {
    "apiKey": "CINR73MIHX337KMRHW7BI5I2AM",
    "bearerToken": "JTLQJNLSM4IGCKJC2MK7COW2VA",
    "signingKey": "c7722b911f9821e742f248af8449f12f06304c18b48b902f7cdef3d9dea7ed34"
    "request":   {
      "amount": "20.55",
      "terminalName": "Cashier #1",
     }
  }

  Response:

  HTTP/1.1 200 OK
  {
    "approved":true,
    "partialAuth": false,
    "authorizedAmount": "20.55",
    "requestedAmount": "20.55",
    "tipAmount": "0.00",
    "taxAmount": "0.00",
    "currencyCode": "USD",
    "entryMethod": "CHIP",
    "paymentType": "VISA",
    "maskedPan": "************0119,"
    "transactionType": "charge",
    "authCode": "010119",
    "sig": "c7722b911f9821e742f248af8449f12f06304c18b48b902f7cdef3d9dea7ed34",
    "receiptSuggestions:" {
      "AID": "A0000000031010",
      "ARQC": "649A5C5FCA0CFD24",
      "IAD": "B17C939DEA2B3A5D3030",
      "ARC": "3030"
    }

In this example, the original request from the client is wrapped in an outer JSON
structure called a Terminal Request and then sent directly to the local terminal
as a POST request.

Note that terminal requests don't have authorization headers - those are only for the gateway.
But the terminal will need to communicate with the gateway, so credentials are
passed in as part of the message body because the terminal will use those credentials
in its own communication with the BlockChyp gateway during authorization.  When
the terminal sends the charge request on to the gateway -- presumably after the
customer has inserted a card -- the terminal will create the authorization headers
in addition to signing the request with its asymmetric encryption keys.

This approach ensures that the merchant for whom the transaction is being authorized
is double enforced.  The transaction signature created by the terminal ensures that
the terminal belongs to the proper merchant.  And the API credentials passed into
the terminal with each request ensure that the point-of-sale system or client application
has the merchant's permission to run transactions against the terminal.  The gateway
ensures that both the terminal and the API credentials belong to the same merchant, or
the transaction is rejected.



Transaction Routing
-------------------

Transactions can be sent to the BlockChyp gateway or a BlockChyp terminal, depending
on the type and context.

For example, gift card transactions must always be sent to a terminal.  Charge transactions
could be sent to the terminal or the gateway, depending on context.  If the charge
transaction includes a token, mag stripe, or primary account number; the transaction
can (and must) be routed directly to the gateway.  But in most cases, the transaction only
has a terminal name or IP address, and the transaction will need to be sent to a terminal.

[Terminal Route Diagram]

Gateway Credentials
-------------------

BlockChyp merchant credentials don't have silly things like MID's.  A given merchant
can have any number of unique credentials with restricted permissions.  In
BlockChyp, credentials take the form of three values:

- **API Key**: Static value that identifies the merchant.
 (``e.g.: CINR73MIHX337KMRHW7BI5I2AM``)
- **Bearer Token**:  A magic token that goes along with the API Key.
 (``e.g.: JTLQJNLSM4IGCKJC2MK7COW2VA``)
- **Signing Key**: Magic signing key used to create HMAC headers for API requests.
 (``e.g.: c7722b911f9821e742f248af8449f12f06304c18b48b902f7cdef3d9dea7ed34``)

What are all these credentials for?  We're glad you asked. Here at BlockChyp HQ,
we believe in defense in depth and we use three credentials instead of two in
order to deal with offsetting security issues.

Bearer Tokens are passed in the authorization header and checked against the Bearer Token
on file for the API Key in our database.  We store the bearer tokens in ASIC resistant
salted one way hashes, as I'm sure you're already doing for all your sensitive tokens and passwords.
(If not, you better stop worrying about making an SDK and go fix that.  Now.)
So, the tokens are safe in our database, but they still fly over the network
where they might be intercepted if TLS 1.2 ever lets us down.

This is why we also have HMAC headers.  We require each API request to include an
HMAC hash generated with the Signing Key.  This protects API credentials in the event that
gateway traffic is intercepted.  The bearer tokens protect credentials in the event that
the symmetric encryption keys used to encrypt the signing keys are breached.

They work together.  It's called teamwork.


SSL On Local Networks
---------------------

Sensitive PCI stuff like card numbers and mag stripes will never be returned from
terminal API calls, and terminals won't accept any requests that contain this sensitive
information.  However, merchant API credentials (either transient or fixed) must
be sent to the terminal with each request.  The damage that can be done with API
credentials is limited and if you use the transient terminal credential approach
(which we highly recommend), it's even more limited.

But we still recommend that you send all your API requests to terminals over HTTPS.

SSL (actually TLS these days) is tricky on closed point-of-sale networks, but that shouldn't
stop you from trying.  BlockChyp terminals run HTTP on port 8000 and HTTPS on port 8443.

When BlockChyp terminals are activated, they generate a unique TLS certificate ultimate signed
by BlockChyp's internal root certificate authority.  When you set up your HTTP client
to communicate with a BlockChyp terminal, make sure you instruct the client to trust
the following root certificate:

**BlockChyp Root Certificate For Private Terminal Networks**::

  -----BEGIN CERTIFICATE-----
  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
  lx3y1Bdb/A3T0axxAwax4jhNQbDQ2dqyXN0=
  -----END CERTIFICATE-----

.. warning::  Don't globally trust the certificate above.  It should be trusted only by the HTTP client instances that communicate with payment terminals.  Use your platform default certificate bundles for all other HTTP communication, including with the BlockChyp gateway.

Transaction Types
-----------------

The core BlockChyp transactions fall into a few different categories with similar data structures.

**Authorization Transactions** are used to capture a payment method through the
gateway or via a payment terminal.  These are the only transactions types that
deal directly with payment methods.

**Authorization Transactions**
******************************

- Charge
- Preauth
- Refund
- Reverse

**Authorization Request**::

  {
    // primary currency for the transaction
    "currencyCode": "USD",

    // string encoded amount in the primary currency
    "amount": "20.55",

    // name assigned to the terminal at activation
    "terminalName": "Cashier #1",

    // reusable payment token obtained from a previous enroll transaction
    "token": "XXXXXXXX", // reusable token

    // magnetic stripe tracks for conventional transactions
    "track1": "",  // MSR track 1
    "track2": "",  // MSR track 2

    // primary account number for keyed or e-commerce transactions
    "pan": "4111111111111111",

    // verification fields for keyed or e-commerce transactions
    "cardholderName": "John Doe",
    "expMonth": "12", // expiration month (MM)
    "expYear": "2020", // expiration year (YYYY)
    "cvv": "000", // CVV code
    "address": "5453 Ridgeline, Suite 160, Kennewick, WA  00000",
    "postalCode": "00000",

    // if true, the payment method will also be saved and tokenized after
    // authorization
    "enroll": false,

    // passthrough transaction identifier defined by the application.
    "transactionRef": "0000000012",

    // for terminal transactions, the consumer will be prompted to add a tip
    "promptForTip": false,

    // optional tax amount
    "taxAmount": "0.00",

    // tip amount, if known at authorization time
    "tipAmount": "0.00",

    // optional description for the consumer's credit card statement
    "description": "Adventures Underground Richland"

    // flags the transaction as a test transaction
    // only valid with test api credentials
    "test": false,

    // If the merchant has set foreign exchange or cryptocurrency
    // prices, they can be passed in here.  Otherwise cryptocurrency
    // and foreign exchange spot prices are used.
    // Only valid for terminal transactions.
    "altPrices": {
      "BTC": "23098",  //optional Bitcoin price (in Satoshis)
      "ETH": "234"     //optional Ethereum price
    }
  }

Note that some fields in the authorization request are mutually exclusive. An
authorization request can have either a **terminal name**, **token**, **track data**, or
**primary account number**.

Request with terminal names are routed to terminals.  All other transactions are
routed directly to the BlockChyp gateway.  The CVV, expiration data, address, postal
code and cardholder name are relevant only for transactions using the primary
account number.

**Note that we strongly recommend that developers avoid sending track data or
primary account numbers.  Doing so will trigger the BlockChyp Scope Alert feature
and flag the merchant account as being in scope for PCI.**

All authorization request have the same response format as shown below:

**Authorization Response**::

  {
    // Indicates whether or not the transaction was approved.
    "approved": true,

    // Flag indicating that the approval was a partial authorization.
    "partialAuth": false,

    // Narrative description of the response.
    "responseDescription": "Approved",

    // Transaction ID assigned by BlockChyp.  Needed for voids and preauth
    // capture transactions.
    "transactionId": "ASDASERERE", // BlockChyp assigned transaction ID.

    // Payment token returned for transactions that request vault enrollment
    "token": "..."

    // The application assigned transaction reference returned in the response
    "transactionRef": "0000000012",

    // Amount authorized.  Would be less than the requested amount for
    // partial authorizations.
    "authorizedAmount": "20.55",

    // Echos back the original requested amount or
    "requestedAmount": "20.55",

    // Returns the original tip amount in the request or the tip amount
    // entered by the consumer if promptForTip was set to true
    "tipAmount": "0.00",

    // The original tax amount for the transaction.
    "taxAmount": "0.00",

    // The currency code echoed back.  Could be different if the consumer
    // paid in cryptocurrency.
    "currencyCode": "USD",

    //The card entry method.  e.g. CHIP, SWIPE, KEYED, APPLEPAY, TOKEN, NFC.
    "entryMethod": "CHIP",

    //The payment method type. e.g. VISA, MC, AMEX, DISC, GIFT, GRAFT.
    "paymentType": "VISA",

    //Masked account number
    "maskedPan": "************0119,"

    //Transaction Type
    "transactionType": "charge",

    // Authorization Code from the card issuer.
    "authCode": "010119",

    // Indicates whether the transactions triggers the BlockChyp scope alert
    // feature for the merchant.
    "scopeAlert": false,

    // For BlockChyp cards (usually gift cards), the card's compressed
    // public key.
    "publicKey": "...",

    // ECDSA signature for terminal transactions, signed by the terminal.
    "sig": "c7722b911f9821e742f248af8449f12f06304c18b48b902f7cdef3d9dea7ed34",

    // A list of EMV tags we recommend developers put on their receipts.
    "receiptSuggestions:" {
      // Application ID.  Required on all receipts per EMV.
      "AID": "A0000000031010",

      // Application Request Cryptogram
      "ARQC": "649A5C5FCA0CFD24",

      // Issuer Application Data
      "IAD": "B17C939DEA2B3A5D3030",

      // Authorization Response Code
      "ARC": "3030",

      // Transaction Certificate
      "TC": "B17C939DEA2B3A5D3030"
    }
  }

**Vault Enrollment**
********************

The **Enroll Transaction** is similar to authorization transactions, except that
amounts are not relevant since this transaction type is just used to turn a payment
method into a reusable token.

**Enrollment Request**::

  {

    // name assigned to the terminal at activation
    "terminalName": "Cashier #1",

    // magnetic stripe tracks for conventional transactions
    "track1": "",  // MSR track 1
    "track2": "",  // MSR track 2

    // primary account number for keyed or e-commerce transactions
    "pan": "4111111111111111",

    // verification fields for keyed or e-commerce transactions
    "cardholderName": "John Doe",
    "expMonth": "12", // expiration month (MM)
    "expYear": "2020", // expiration year (YYYY)
    "cvv": "000", // CVV code
    "address": "5453 Ridgeline, Suite 160, Kennewick, WA  00000",
    "postalCode": "00000",

    // passthrough transaction identifier defined by the application.
    "transactionRef": "0000000012",

    // flags the transaction as a test transaction
    // only valid with test api credentials
    "test": false

  }

The response to an enroll transaction is shown below:

**Enrollment Response**::

  {
    // Indicates whether or not the transaction was approved.
    "approved": true,

    // Narrative description of the response.
    "responseDescription": "Approved",

    // Transaction ID assigned by BlockChyp.  Needed for voids and preauth
    // capture transactions.
    "transactionId": "ASDASERERE", // BlockChyp assigned transaction ID.

    // Payment token returned for transactions that request vault enrollment
    "token": "..."

    // The application assigned transaction reference returned in the response
    "transactionRef": "0000000012",

    //The card entry method.  e.g. CHIP, SWIPE, KEYED, APPLEPAY, TOKEN, NFC.
    "entryMethod": "CHIP",

    //The payment method type. e.g. VISA, MC, AMEX, DISC, GIFT, GRAFT.
    "paymentType": "VISA",

    //Masked account number
    "maskedPan": "************0119,"

    //Transaction Type
    "transactionType": "enroll",

    // Indicates whether the transactions triggers the BlockChyp scope alert
    // feature for the merchant.
    "scopeAlert": false,

    // For BlockChyp cards (usually gift cards), the card's compressed
    // public key.
    "publicKey": "...",

    // ECDSA signature for terminal transactions, signed by the terminal.
    "sig": "c7722b911f9821e742f248af8449f12f06304c18b48b902f7cdef3d9dea7ed34",

    // A list of EMV tags we recommend developers put on their receipts.
    "receiptSuggestions:" {
      // Application ID.
      "AID": "A0000000031010",
    }
  }


**Preauth Capture**
*******************

Capture is used to capture a pre-auth.  Must refer to the transaction ID returned
in the original preauth.

**Capture Request**::

  {

    // primary currency for the transaction
    "currencyCode": "USD",

    // transaction id
    "transactionId": "....",

    // string encoded amount in the primary currency
    "amount": "20.55",

    // passthrough transaction identifier defined by the application.
    "transactionRef": "0000000012",

    // optional tax amount
    "taxAmount": "0.00",

    // tip amount, if known at authorization time
    "tipAmount": "0.00",

    // flags the transaction as a test transaction
    // only valid with test api credentials
    "test": false,

  }

**Capture Response**::

  {
    // Indicates whether or not the transaction was approved.
    "approved": true,

    // Narrative description of the response.
    "responseDescription": "Approved",

    // Transaction ID assigned by BlockChyp.  Needed for voids and preauth
    // capture transactions.
    "transactionId": "ASDASERERE", // BlockChyp assigned transaction ID.

    // The application assigned transaction reference returned in the response
    "transactionRef": "0000000012",

    //The card entry method.  e.g. CHIP, SWIPE, KEYED, APPLEPAY, TOKEN, NFC.
    "entryMethod": "CHIP",

    //The payment method type. e.g. VISA, MC, AMEX, DISC, GIFT, GRAFT.
    "paymentType": "VISA",

    //Masked account number
    "maskedPan": "************0119,"

    //Transaction Type
    "transactionType": "capture",

    // For BlockChyp cards (usually gift cards), the card's compressed
    // public key.
    "publicKey": "...",

  }


**Void Preauth**
****************

Voids are used to discard a previous preauth.  They're like captures in reverse.

**Void Request**::

  {

    // primary currency for the transaction
    "currencyCode": "USD",

    // transaction id
    "transactionId": "....",

    // passthrough transaction identifier defined by the application.
    "transactionRef": "0000000012",

    // flags the transaction as a test transaction
    // only valid with test api credentials
    "test": false,

  }

**Void Response**::

  {
    // Indicates whether or not the transaction was approved.
    "approved": true,

    // Narrative description of the response.
    "responseDescription": "Approved",

    // Transaction ID assigned by BlockChyp.  Needed for voids and preauth
    // capture transactions.
    "transactionId": "ASDASERERE", // BlockChyp assigned transaction ID.

    // The application assigned transaction reference returned in the response
    "transactionRef": "0000000012",

    //The card entry method.  e.g. CHIP, SWIPE, KEYED, APPLEPAY, TOKEN, NFC.
    "entryMethod": "CHIP",

    //The payment method type. e.g. VISA, MC, AMEX, DISC, GIFT, GRAFT.
    "paymentType": "VISA",

    //Masked account number
    "maskedPan": "************0119,"

    //Transaction Type
    "transactionType": "void",

    // For BlockChyp cards (usually gift cards), the card's compressed
    // public key.
    "publicKey": "...",

  }

**Terminal Ping**
*****************

Simple test transaction that allows connectivity with a terminal to be tested.

**Ping Request**::

  {

    // primary currency for the transaction
    "terminalName": "Cashier #1",

    // passthrough transaction identifier defined by the application.
    "transactionRef": "0000000012",

    // flags the transaction as a test transaction
    // only valid with test api credentials
    "test": false,

  }

**Ping Response**::

  {
    // Indicates whether or not the ping worked.
    "success": true,

    // ISO 8601 formatted timestamp
    "timestamp": "2008-09-15T15:53:00Z",

    // returns the name of the merchant paired with the terminal
    "merchantName": "Adventures Underground",

    // returns the name assigned to the terminal
    "terminalName": "Cashier #1",

    // Indicates whether or not the terminal request was routed through
    // the cloud or not.
    "cloudRelayed": false

  }


**Gift Activate**
*****************

This transaction is used to activate or add value to a BlockChyp gift card.
Valid with terminals only.

**Gift Activate Request**::

  {

    // primary currency for the transaction
    "currencyCode": "USD",

    // amount to add to the gift card
    "amount": "50.00",

    // name assigned to the terminal at activation
    "terminalName": "Cashier #1",

    // passthrough transaction identifier defined by the application.
    "transactionRef": "0000000012",

    // flags the transaction as a test transaction
    // only valid with test api credentials
    "test": false,

  }

Gift card activation transactions return the following response:

**Gift Activation Response**::

  {
    // Indicates whether or not the transaction was approved.
    "approved": true,

    // Narrative description of the response.
    "responseDescription": "Approved",

    // Transaction ID assigned by BlockChyp.  Needed for voids and preauth
    // capture transactions.
    "transactionId": "ASDASERERE", // BlockChyp assigned transaction ID.

    // The application assigned transaction reference returned in the response
    "transactionRef": "0000000012",

    // Amount added to the gift card balance
    "amount": "50.00",

    // Total balance on the gift card after the transaction.
    "currentBalance": "50.00",

    // The currency code echoed back.
    "currencyCode": "USD",

    //Transaction Type
    "transactionType": "gift_activate",

    // The card's compressed public key.
    "publicKey": "...",

    // ECDSA signature for the transaction.
    "sig": "c7722b911f9821e742f248af8449f12f06304c18b48b902f7cdef3d9dea7ed34",

  }

**Close Batch**
***************

This transaction forces closure of the current credit card batch if there is one.
BlockChyp cards and cryptocurrency work differently and aren't part batch based.

This is an optional transaction since batches will close automatically.  This
transaction should only be used for merchants with unusual hours or for those
open 24 hours a day.

**Close Batch Request**::

  {

    // passthrough transaction identifier defined by the application.
    "transactionRef": "0000000012",

    // flags the transaction as a test transaction
    // only valid with test api credentials
    "test": false,

  }

The close batch response includes simple approval data and a summary of
transaction volume by card brand.

**Close Batch Response**::

  {

    // Transaction ID assigned by BlockChyp.  Needed for voids and preauth
    // capture transactions.
    "transactionId": "ASDASERERE", // BlockChyp assigned transaction ID.

    // batch identifier
    "batchId": "12321321321",

    // passthrough transaction identifier defined by the application.
    "transactionRef": "0000000012",

    // flags the transaction as a test transaction
    // only valid with test api credentials
    "test": false,

    // currency code for the batch
    "currencyCode": "USD",

    // captured total
    "capturedTotal": "1234.45",

    // open preauthorization
    "openPreauths": "345.34",

    //captured total breakdown by card brand
    "cardBrands": {
      "VISA": "234.45",
      "MC": "400.00",
      "AMEX": "300.00",
      "DISC": "300.00"
    }

  }

Asynchronous Transactions
-------------------------

By default, BlockChyp transactions are synchronous with configurable timeouts.
For some scenarios, like pay-at-the-table, this may not be the best option and
authorizations will need to be asynchronous.  SDK's should expose async versions
of charge, preauth, enroll, and refund. For example...

- asyncCharge()
- asyncPreauth()
- asyncRefund()
- asyncEnroll()

These methods should be valid for terminal based transactions only and developers
are required to set a transactionRef value for these transactions.  Since the
async methods return before a transaction response, the transaction ref can be used to
look up a response.

SDK's should expose a method called **txStatus()** that can lookup a transaction by its
id or transactionRef.

Developers can poll this method to determine the outcome of a transaction.  SDK's
developers are also encouraged to make use of language specific concurrency features
to notify clients applications when a transaction finally completes.  For example,
a Javascript SDK could take advantage of promises or callbacks.  The async methods
in our Go SDK accept channels as parameters and these channels are notified when
transactions complete.
