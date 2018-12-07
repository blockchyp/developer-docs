.. _terminal-rest-api:

Terminal REST API Reference
===========================

Every BlockChyp terminal has a small web server running a simple REST API.
Developers can use this API to capture card present payments and activate gift
cards. All other transactions are done directly through the Gateway API.

Terminal Activation
-------------------

A terminal must be activated and paired with a merchant account before it will
accept transactions.  To activate a terminal, login to the BlockChyp dashboard,
create a test merchant account, and click **ACTIVATE TERMINAL**.

Next, connect your terminal to the network and power it up.  Once the terminal
starts up, it will display a message reading **TOUCH TO ACTIVATE**.  Touch the
screen and in a few moments the terminal will display a six character activation
code.  Enter the code in the Dashboard's activation form along with a unique
name you'd like to use for the terminal.

The terminal will now be ready for transactions.


Web Server Ports
----------------

BlockChyp terminals listen for transactions on ports **8080** and **8443**.

- HTTP Port: 8080
- HTTPS Port: 8443

Because terminals usually run on private networks and SSL can be difficult to
implement on these networks, plain HTTP is supported.  Sensitive card authentication
data is not sent or received by terminals, but we still strongly encourage
developers and merchants to favor HTTPS on port 8443.


SSL/TLS
-------

Because terminals run on private networks, conventional SSL or TLS based on
the major certificate authorities is not supported.  When BlockChyp terminals are
activated, a unique certificate is generated and signed by BlockChyp's own
root certificate authority.

To support HTTPS terminal traffic, you'll need to make sure your http client
library supports TLS 1.2 and add the BlockChyp root terminal certificate to
the list of trusted root certificates for your platform.

**BlockChyp Root Certificate For Private Terminal Networks**::

  -----BEGIN CERTIFICATE-----
  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
  lx3y1Bdb/A3T0axxAwax4jhNQbDQ2dqyXN0=
  -----END CERTIFICATE-----

.. warning::  Don't globally trust the certificate above.  It should be trusted only by the HTTP client instances that communicate with payment terminals.  Use your platform default certificate bundles for all other HTTP communication, including with the BlockChyp gateway.

A Sample request
----------------

Before we get too much further, let's take a look at a basic request.  The
example below shows a simple form of a charge request.

You can refer to terminals by their IP address or host names if you've configured
them for your network.  We recommend avoiding the use of manual IP address
configuration in your systems as IP addresses can be obtained on demand from the
Payment Gateway's **Terminal Route** API.  This API returns the current private
IP address and provides for a more robust implementation in the event that
installers or merchants fail to statically configure IP addresses for terminals.

**POST: https://<IP_ADDRESS>/api/charge**::

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
  }


Authentication
--------------

All API requests against terminals require the standard BlockChyp authentication
credentials (API Key, Bearer Token, and Signing Key).

In Gateway API transactions, these values are passed in the HTTP headers.  In
Terminal API transactions, they're passed in the body of the request.  The
terminal will then use them to assemble headers for its own communication with
the gateway.

Transient Credentials
*********************

In addition to Terminal IP addresses, the Gateway's Terminal Route API
also returns a set of transient credentials that can be used as an alternative to
the credentials generated by the dashboard.  These credentials should be used instead
of merchant level credentials whenever possible, especially if merchants are
unable to use SSL or TLS.

Transient Credentials are derived from master credentials and inherit all their
permissions and settings, but they can only be used for terminal transactions from
a specific terminal.

Terminal Routing
----------------

As referenced above, the Gateway has a Terminal Route API that returns metadata
about a specific terminal, including the terminal's IP Address and transient
credentials.

The example below shows a typical terminal route request.

**GET: https://api.blockchyp.com/api/terminal-route?terminal=Cashier%20#1**::

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

This API call returns the IP address of the terminal, transient credentials we
recommend be used for API calls to the terminal, and the terminal's public key
information for use in verifying response signatures.

Consult the Gateway API Reference for more information about terminal routes.

Connectivity Test (/api/test)
-----------------------------

:HTTP Method: POST
:URI:  /api/test

This API performs a simple connectivity test with the terminal.  A successful
result also indicates that the credentials are suitable for performing a live
transaction.

Sample Request and Response::

  Request:

  GET /api/test HTTP/1.1
  Host: 192.168.50.245

  {
    "apiKey":"O3NLLDRAM3TIUYZDRUHVASCPZM",
    "bearerToken":"5X7PWK6RC2NSJUSH752RRPJWVU",
    "signingKey":"1b9a1c0f908de276f684ff678e4d15e471bb86be63bf398860ea770a66c345ab"
  }

  Response:

  HTTP/1.1 200 OK
  {
    "success":true,
    "errorMessage":""
  }

Charge (/api/charge)
--------------------

:HTTP Method: POST
:URI:  /api/charge

Executes a direct auth and capture.  The terminal prompts for a payment method,
the user presents a method of payment and the API returns the authorization status
and details about the payment method.

.. note::  Sensitive information like track data or account numbers are never returned by any BlockChyp API.


Sample Request and Response::

  Request:

  GET /api/charge HTTP/1.1
  Host: 192.168.50.245

  {
    "apiKey":"3KLZKRWVOSL2I5ZTKR7ANM23VA",
    "bearerToken":"LAAQPFNCQKDY5UGWDWTSUFFYWA",
    "signingKey":"092019fcff1fef3f93fa25aa2680b760748fa97f7ae0721807d91b55dc52aadf",
    "request": {

      // application defined transaction identifier, up to 64 characters in length
      // optional, but recommended since time out reversals won't work without it
      "transactionRef": "b944f032e997d944cdabb03cf1aa260ba3cde3d3b572b138eceb27bb41e54332",

      // flags this as a test transaction - no real money will change hands
      "test": false,

      // name of the terminal - optional
      "terminalName":"Desk Terminal",

      // ISO three character currency code, optional, defaults to USD
      "currencyCode": "USD",

      // total amount to authorize
      "amount":"12.67",

      // optional tip amount, if known
      "tipAmount":"0.00",

      // optional tax amount, if known
      "taxAmount":"0.00",

      // if true, the user will be prompted to add a tip before presenting
      // their payment card
      "promptForTip":false,

      // if true, the payment method will be tokenized for use in future
      // transactions
      "enroll": false,

      // an optional description for the transaction
      // for credit card transactions, this will appear on the statement
      "description": "Comic Books"
    }
  }

  Response:

  HTTP/1.1 200 OK
  {

    // whether or not the transaction when through
    "approved":true,

    // narrative description of the response
    "responseDescription": "Approved",

    // authorization code
    "authCode":"054321",

    // indicates whether or not the authorized amount was less than the requested amount
    "partialAuth":false,

    // the final requested amount
    // this could be more than the original request's amount if you prompted
    // the user for a tip.
    "requestedAmount":"12.67",

    // amount authorized by the payment network
    "authorizedAmount":"12.67",

    // tip amount, could be different if you prompted the user for a tip
    "tipAmount":"0.00",

    // tax amount from the original request echoed back
    "taxAmount":"0.00",

    // currency for the authorization
    "currencyCode":"USD",

    // for conventional credit card transactions, the BlockChyp assigned batch id
    "batchId": "UEOHSRX2MYI6RA2WSSDM7WZLHE",

    // original transaction reference, echoed back
    "transactionRef": "b944f032e997d944cdabb03cf1aa260ba3cde3d3b572b138eceb27bb41e54332",

    // original test flag setting, echoed back
    "test": false,

    // BlockChyp assigned transaction Id.  Use this in any subsequent void requests.
    "transactionId":"UEOHSRX2MYI6RA2LNSLM7WZLHE",

    // transaction type, echoed back
    "transactionType":"charge",

    // timestamp of the transaction in UTC
    "timestamp":"2018-12-07T21:25:37Z",

    // hash of the latest tick block on the BlockChyp clockchain
    // this is essentially blockchain time
    "tickBlock":"000a40ada947bd35886f19c8908cd84e521f713cc2637c0bf70b3b2ea63ffe7d",

    // could be CHIP, SWIPE, APPLEPAY, etc
    "entryMethod":"CHIP",

    // could be VISA, MC, DISC, AMEX, or GIFT
    "paymentType":"VISA",

    // masked account number with just the last four digits visible
    "maskedPan":"************0119",


    // reusable payment token, if requested by setting the enroll flag to "true"
    "token": "",

    // public key for BlockChyp gift cards
    "publicKey": "",


    "receiptSuggestions":{
      // EMV Application Identifier - required on all EMV receipts
      "AID":"A0000000031010",

      // Application Request Cryptogram - digital signature for an EMV transaction
      "ARQC":"6218309BF7D48CC7",

      // Issuer Application Data
      "IAD":"06010A03A0A800",

      // Terminal Verification Results
      "TVR":"8000008000",

      // Transaction Status Indicator
      "TSI":"6800",

      // if true, the system should print a signature line on the receipt
      "requestSignature":true
    }

  }


Preauth (/api/preauth)
----------------------

:HTTP Method: POST
:URI:  /api/preauth

Executes a preauthorization.  The terminal prompts for a payment method,
the user presents a method of payment and the API returns the authorization status
and details about the payment method.

.. note::  Must be captured later with a gateway **capture** transaction.

.. note::  Sensitive information like track data or account numbers are never returned by any BlockChyp API.


Sample Request and Response::

  Request:

  GET /api/preauth HTTP/1.1
  Host: 192.168.50.245

  {
    "apiKey":"3KLZKRWVOSL2I5ZTKR7ANM23VA",
    "bearerToken":"LAAQPFNCQKDY5UGWDWTSUFFYWA",
    "signingKey":"092019fcff1fef3f93fa25aa2680b760748fa97f7ae0721807d91b55dc52aadf",
    "request": {

      // application defined transaction identifier, up to 64 characters in length
      // optional, but recommended since time out reversals won't work without it
      "transactionRef": "b944f032e997d944cdabb03cf1aa260ba3cde3d3b572b138eceb27bb41e54332",

      // flags this as a test transaction - no real money will change hands
      "test": false,

      // name of the terminal - optional
      "terminalName":"Desk Terminal",

      // ISO three character currency code, optional, defaults to USD
      "currencyCode": "USD",

      // total amount to authorize
      "amount":"12.67",

      // optional tip amount, if known
      "tipAmount":"0.00",

      // optional tax amount, if known
      "taxAmount":"0.00",

      // if true, the user will be prompted to add a tip before presenting
      // their payment card
      "promptForTip":false,

      // if true, the payment method will be tokenized for use in future
      // transactions
      "enroll": false,

      // an optional description for the transaction
      // for credit card transactions, this will appear on the statement
      "description": "Comic Books"
    }
  }

  Response:

  HTTP/1.1 200 OK
  {

    // whether or not the transaction when through
    "approved":true,

    // narrative description of the response
    "responseDescription": "Approved",

    // authorization code
    "authCode":"054321",

    // indicates whether or not the authorized amount was less than the requested amount
    "partialAuth":false,

    // the final requested amount
    // this could be more than the original request's amount if you prompted
    // the user for a tip - which would almost never happen with a preauth
    "requestedAmount":"12.67",

    // amount authorized by the payment network
    "authorizedAmount":"12.67",

    // tip amount, could be different if you prompted the user for a tip
    "tipAmount":"0.00",

    // tax amount from the original request echoed back
    "taxAmount":"0.00",

    // currency for the authorization
    "currencyCode":"USD",

    // for conventional credit card transactions, the BlockChyp assigned batch id
    "batchId": "UEOHSRX2MYI6RA2WSSDM7WZLHE",

    // original transaction reference, echoed back
    "transactionRef": "b944f032e997d944cdabb03cf1aa260ba3cde3d3b572b138eceb27bb41e54332",

    // original test flag setting, echoed back
    "test": false,

    // BlockChyp assigned transaction Id.  Use this in any subsequent voids or capture requests.
    // Required to capture the transaction later.
    "transactionId":"UEOHSRX2MYI6RA2LNSLM7WZLHE",

    // transaction type, echoed back
    "transactionType":"preauth",

    // timestamp of the transaction in UTC
    "timestamp":"2018-12-07T21:25:37Z",

    // hash of the latest tick block on the BlockChyp clockchain
    // this is essentially blockchain time
    "tickBlock":"000a40ada947bd35886f19c8908cd84e521f713cc2637c0bf70b3b2ea63ffe7d",

    // could be CHIP, SWIPE, APPLEPAY, etc
    "entryMethod":"CHIP",

    // could be VISA, MC, DISC, AMEX, or GIFT
    "paymentType":"VISA",

    // masked account number with just the last four digits visible
    "maskedPan":"************0119",


    // reusable payment token, if requested by setting the enroll flag to "true"
    "token": "",

    // public key for BlockChyp gift cards
    "publicKey": "",


    "receiptSuggestions":{
      // EMV Application Identifier - required on all EMV receipts
      "AID":"A0000000031010",

      // Application Request Cryptogram - digital signature for an EMV transaction
      "ARQC":"6218309BF7D48CC7",

      // Issuer Application Data
      "IAD":"06010A03A0A800",

      // Terminal Verification Results
      "TVR":"8000008000",

      // Transaction Status Indicator
      "TSI":"6800",

      // if true, the system should print a signature line on the receipt
      "requestSignature":true
    }

  }


Gift Activate (/api/gift-activate)
----------------------------------

:HTTP Method: POST
:URI:  /api/gift-activate

Activates or adds value to a BlockChyp gift card.  The terminal will prompt
the user to swipe a gift card and the amount specified will be added to it.

.. note::  This only works with official BlockChyp gift cards.  All developer kits include test gift cards and merchant can order custom gift cards with their own branding.

Sample Request and Response::

  Request:

  GET /api/gift-activate HTTP/1.1
  Host: 192.168.50.245

  {
    "apiKey":"3KLZKRWVOSL2I5ZTKR7ANM23VA",
    "bearerToken":"LAAQPFNCQKDY5UGWDWTSUFFYWA",
    "signingKey":"092019fcff1fef3f93fa25aa2680b760748fa97f7ae0721807d91b55dc52aadf",
    "request": {

      // optional transaction ref
      "transactionRef": "b944f032e997d944cdabb03cf1aa260ba3cde3d3b572b138eceb27bb41e54332",

      // flags this as a test transaction - transaction will go on the test blockchain
      "test": false,

      // name of the terminal - optional
      "terminalName":"Desk Terminal",

      // ISO three character currency code, optional, defaults to USD
      "currencyCode": "USD",

      // total amount to add to the gift card
      "amount":"25.00"

    }
  }

  Response:

  HTTP/1.1 200 OK
  {

    // whether or not the transaction when through
    "approved":true,

    // narrative description of the response
    "responseDescription": "Approved",

    // amount added to the gift card
    "amount": "25.00",

    // total balance on the card after the amount has been added
    // also displayed on the terminal after authorization
    "currentBalance": "25.00",

    // currency for the authorization
    "currencyCode":"USD",

    // original transaction reference, echoed back
    "transactionRef": "b944f032e997d944cdabb03cf1aa260ba3cde3d3b572b138eceb27bb41e54332",

    // original test flag setting, echoed back
    "test": false,

    // BlockChyp assigned transaction id.  Use this in any subsequent voids or capture requests.
    // Required to capture the transaction later.
    "transactionId":"UEOHSRX2MYI6RA2LNSLM7WZLHE",

    // transaction type, echoed back
    "transactionType":"gift-activate",

    // timestamp of the transaction is UTC
    "timestamp":"2018-12-07T21:25:37Z",

    // hash of the latest tick block on the BlockChyp clockchain
    // this is essentially blockchain time
    "tickBlock":"000a40ada947bd35886f19c8908cd84e521f713cc2637c0bf70b3b2ea63ffe7d",

    // public key for the gift card
    "publicKey": "342a40ada947bd35886f19c8908cd84e521f713cc2637c0bf70b3b2ea63ffe7d",

  }

Time Out Reversals
------------------

The terminal communicates directly with the BlockChyp gateway to obtain authorization.
This is what keeps you out of PCI or EMV scope.  If a transaction times out or results
in an error, the terminal will automatically send a time out reversal - which you
or your merchants may see in the transaction logs.

If a request to the terminal times out, you can send a reversal yourself by using
the Gateway API's Reverse Endpoint.  Note that this requires the use of the **transactionRef**
field.

Consult the Payment Gateway REST API Reference for more detail.
