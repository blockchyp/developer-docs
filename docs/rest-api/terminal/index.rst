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
  MIIFAjCCAuqgAwIBAgIBATANBgkqhkiG9w0BAQsFADAgMR4wHAYDVQQDDBVCbG9j
  a0NoeXAgSW50ZXJuYWwgQ0EwIBcNMTgwMTAxMDAwMDA4WhgPNDc1NTExMjkwMDAw
  MDhaMCAxHjAcBgNVBAMMFUJsb2NrQ2h5cCBJbnRlcm5hbCBDQTCCAiIwDQYJKoZI
  hvcNAQEBBQADggIPADCCAgoCggIBANyWuVhDiqeCrHMxbTv5PN5UOZdR8n4PPwUV
  z0dALnLS7Lkl9nnuBxUK5XFGsZHBQ3GqSsWgA0HBUAAkKY/hzDIY+mrKOTMFMhoF
  SKmcNwmdt+NXuUtYwL5STsr1U/XnxcizsSEHcGP5LhIH16AY0XYMVzNTBXrylH7O
  Hf/pPJaVbuywAkiyrEV+lTo1mVTOCucGoNRPogluuyfbBCUH9bWBajbjHWdyiX58
  IV786JWkw5ogLXgDekrrzdVxQH1t2kN2PvXNHGOBlB0NL/QwKHxfbvgIu6EkyEXv
  vSuFclgaM3x38zcEaIS8id/wZYkwZXAqquR5Hi5fqPILC1xmRF+zC1GH1uJ+gsQu
  wqwaiwmD9Rcbm2ZOSVntQy5bCF7IzPlMHzMlt33dF9mZo9bJwFO1APdpeWy+Ooga
  n1k/yS2EPnkAv+DiRpNf2it6n86+X7Z4C6QGgP5+rfc53uxeaF8gPLgXViaHHTZD
  NflxaNjgKD0xAwB3Yhca8RQSjRPwKYk1FrbhTSAIidnwmA4jrV7juZ2RSWA99VzR
  O68OmE/7NygxGgo995pPc+s6DO6IOnZvT2tSs0b2UmEKT51/cf93lv+phX/69hTC
  ctMEYoIGNRAvcISA0lfTWHAbiRzMyagtuiRMttS7C+IshsgBrjHSHMsEYj8RhRnR
  0FvmChUNAgMBAAGjRTBDMB0GA1UdDgQWBBSBl1rnpf7Omve8fXPl9EltnlcqGTAS
  BgNVHRMBAf8ECDAGAQH/AgEBMA4GA1UdDwEB/wQEAwIBBjANBgkqhkiG9w0BAQsF
  AAOCAgEAkt9ywLJvM0TjEUjlC32niE8mNIPX5azHJ0++PlZ2Fc7ZKy4nntt2YErl
  l4qEOB8ED2VaLQuxx0O9H2oh1QsMuxT3rQ4SDNmQVH9vUYJWgIkYjY1zKubEyktv
  oZyi8xK5e0/ME//vU0ru6y0dmcFtDvpwm/JZPjoVKHK58JpCKH8xhVxQo7NxAIf8
  Ow+fr58plDQP1CbfjO1gJpFg7lQ282rz9n0Ju2mXm3guclcx74mDJGlzGLGCJCnu
  Qxta8Dv/Cg8+kNM36boORMChaoAgIerXL17EhyUh3ZsSaxEchqvCWtLv1+ekhGpF
  A08xS33r1GgQV/cyunuz3czQ0Y/7UjKluo6sbS0RmVtAWJA/DhwXgQlHlFyROmhG
  pcKXeLc7+LrBZxITVuQk8Mg9aceAnzBqjeTjQNPQJkOwqIFgDUXNNqvA5mhn/j25
  u8CcDY/0p5C4tFQc1npgQwJZAwRGEvFmXVDgEZ8FFkzhn74oxI99Xs1HGc9zO/uP
  GV0cahaj9xspMPMBe5Q2mNhVca6+RIZPSIcVbsgYy+2QDBep7NpraQgG7V0f2XTu
  uLBaPXbY9PZLFklSSZOLXAuuOk0G57lfyVFRNAZ2R3uQdkDpx90Ti6PDWj9M6x1p
  jD1XNpXvgH2k91jjsK67khN+4bWoFBsfrMYt6vgjtXyv0kf12y0=
  -----END CERTIFICATE-----

.. warning::  Don't globally trust the certificate above.  It should be trusted only by the HTTP client instances that communicate with payment terminals.  Use your platform default certificate bundles for all other HTTP communication, including with the BlockChyp gateway.

A Sample Request
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
    "responseDescription":"Approved",
    "transactionId":"NZ6FGYAYLYI6TLVWNSLM7WZLHE",
    "transactionRef":"cfd068099a4280f1f09a965d9cc522f25ef4e06a95c9a9461d59fa5deed62180",
    "transactionType":"charge","timestamp":"2019-01-15T00:42:36Z",
    "tickBlock":"000e61f8204a2a372cac288f833a8e0949dd50d0074d5133432dce4e78d97913",
    "authCode":"612797",
    "entryMethod":"CHIP",
    "paymentType":"VISA",
    "maskedPan":"************0010",
    "cardHolder":"Test/Card 01              ",
    "partialAuth":false,
    "altCurrency":false,
    "currencyCode":"USD",
    "requestedAmount":"20.55",
    "authorizedAmount":"20.55",
    "receiptSuggestions":{
      "AID":"A0000000031010",
      "ARQC":"E0A09074268A87F4",
      "IAD":"06010A03A0B800",
      "TVR":"0240008000",
      "TSI":"E800",
      "merchantName":"Test Merchant",
      "applicationLabel":"VISA CREDIT",
      "requestSignature":true,
      "maskedPan":"************0010",
      "authorizedAmount":"20.55",
      "transactionType":"charge",
      "entryMethod":"CHIP"
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
:Path:  /api/test

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
:Path:  /api/charge

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

      // puts the terminal directly in keyed entry mode
      // typically used for handling phone transactions (MOTO)
      "manual": false,

      // if you want the signature image returned with the response, specify
      // the image format you want here
      "sigFormat": "png|jpg|gif",

      // optional max width images should be scaled to
      "sigWidth": 600,

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
    // whether or not the transaction went through
    "approved":true,

    // narrative description of the response
    "responseDescription":"Approved",

    // authorization code
    "authCode":"612797",

    // BlockChyp assigned transaction id
    "transactionId":"NZ6FGYAYLYI6TLVWNSLM7WZLHE",

    // transaction type, echoed back
    "transactionType":"charge",

    // timestamp of the transaction in UTC
    "timestamp":"2019-01-15T00:42:36Z",

    // hash of the latest tick block on the BlockChyp clockchain
    // this is essentially blockchain time
    "tickBlock":"000e61f8204a2a372cac288f833a8e0949dd50d0074d5133432dce4e78d97913",

    // for conventional credit card transactions, the BlockChyp assigned batch id
    "batchId": "UEOHSRX2MYI6RA2WSSDM7WZLHE",

    // could be CHIP, SWIPE, APPLEPAY, etc
    "entryMethod":"CHIP",

    // could be VISA, MC, DISC, AMEX, or GIFT
    "paymentType":"VISA",

    // masked account number with just the last four digits visible
    "maskedPan":"************0010",

    // cardholder name
    "cardHolder":"Test/Card 01              ",

    // indicates whether or not the authorized amount was less than the requested amount
    "partialAuth":false,

    // original test flag setting, echoed back
    "test": false,

    // currency for the authorization
    "currencyCode":"USD",

    // the final requested amount
    // this could be more than the original request's amount if you prompted
    // the user for a tip.
    "requestedAmount":"20.55",

    // amount authorized by the payment network
    "authorizedAmount":"20.55",

    // tip amount, could be different if you prompted the user for a tip
    "tipAmount":"0.00",

    // tax amount from the original request echoed back
    "taxAmount":"0.00",

    // reusable payment token, if requested by setting the enroll flag to "true"
    "token": "",

    // public key for BlockChyp gift cards
    "publicKey": "",

    // sig file returned in hex if requested
    "sigFile":"89504e470d0a1a0a0000000d49484452000...",

    // data that developers should consider putting on their credit card receipts
    "receiptSuggestions":{

      // EMV Application Identifier - required on all EMV receipts
      "AID":"A0000000031010",

      "ARQC":"E0A09074268A87F4",
      "IAD":"06010A03A0B800",
      "TVR":"0240008000",
      "TSI":"E800",
      "merchantName":"Test Merchant",
      "applicationLabel":"VISA CREDIT",
      "requestSignature":true,
      "maskedPan":"************0010",
      "authorizedAmount":"20.55",
      "transactionType":"charge",
      "entryMethod":"CHIP"
    }
  }


Preauth (/api/preauth)
----------------------

:HTTP Method: POST
:Path:  /api/preauth

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

      // puts the terminal directly in keyed entry mode
      // typically used for handling phone transactions (MOTO)
      "manual": false,

      // if you want the signature image returned with the response, specify
      // the image format you want here
      "sigFormat": "png|jpg|gif",

      // optional max width images should be scaled to
      "sigWidth": 600,

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

  // whether or not the transaction went through
  "approved":true,

  // narrative description of the response
  "responseDescription":"Approved",

  // authorization code
  "authCode":"612797",

  // BlockChyp assigned transaction id
  "transactionId":"NZ6FGYAYLYI6TLVWNSLM7WZLHE",

  // transaction type, echoed back
  "transactionType":"preauth",

  // timestamp of the transaction in UTC
  "timestamp":"2019-01-15T00:42:36Z",

  // hash of the latest tick block on the BlockChyp clockchain
  // this is essentially blockchain time
  "tickBlock":"000e61f8204a2a372cac288f833a8e0949dd50d0074d5133432dce4e78d97913",

  // for conventional credit card transactions, the BlockChyp assigned batch id
  "batchId": "UEOHSRX2MYI6RA2WSSDM7WZLHE",

  // could be CHIP, SWIPE, APPLEPAY, etc
  "entryMethod":"CHIP",

  // could be VISA, MC, DISC, AMEX, or GIFT
  "paymentType":"VISA",

  // masked account number with just the last four digits visible
  "maskedPan":"************0010",

  // cardholder name
  "cardHolder":"Test/Card 01              ",

  // indicates whether or not the authorized amount was less than the requested amount
  "partialAuth":false,

  // original test flag setting, echoed back
  "test": false,

  // currency for the authorization
  "currencyCode":"USD",

  // the final requested amount
  // this could be more than the original request's amount if you prompted
  // the user for a tip.
  "requestedAmount":"20.55",

  // amount authorized by the payment network
  "authorizedAmount":"20.55",

  // tip amount, could be different if you prompted the user for a tip
  "tipAmount":"0.00",

  // tax amount from the original request echoed back
  "taxAmount":"0.00",

  // reusable payment token, if requested by setting the enroll flag to "true"
  "token": "",

  // public key for BlockChyp gift cards
  "publicKey": "",

  // sig file returned in hex if requested
  "sigFile":"89504e470d0a1a0a0000000d49484452000...",

  // data that developers should consider putting on their credit card receipts
  "receiptSuggestions":{

    // EMV Application Identifier - required on all EMV receipts
    "AID":"A0000000031010",

    "ARQC":"E0A09074268A87F4",
    "IAD":"06010A03A0B800",
    "TVR":"0240008000",
    "TSI":"E800",
    "merchantName":"Test Merchant",
    "applicationLabel":"VISA CREDIT",
    "requestSignature":true,
    "maskedPan":"************0010",
    "authorizedAmount":"20.55",
    "transactionType":"charge",
    "entryMethod":"CHIP"
  }
  }


Refund (/api/refund)
----------------------

:HTTP Method: POST
:Path:  /api/refund

Executes a refund without a prior transaction context.  We call this a "free range"
refund.

The terminal prompts for a payment method,
the user presents a method of payment and the API returns the authorization status
and details about the payment method.

.. note::  We strongly recommend deprecating transactions of this type.  Refunds with a transaction context are much more fraud resistant.

.. note::  Sensitive information like track data or account numbers are never returned by any BlockChyp API.

Sample Request and Response::

  Request:

  GET /api/refund HTTP/1.1
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

      // puts the terminal directly in keyed entry mode
      // typically used for handling phone transactions (MOTO)
      "manual": false,

      // if you want the signature image returned with the response, specify
      // the image format you want here
      "sigFormat": "png|jpg|gif",

      // optional max width images should be scaled to
      "sigWidth": 600,

      // ISO three character currency code, optional, defaults to USD
      "currencyCode": "USD",

      // total amount to refund
      "amount":"12.67",

      // an optional description for the transaction
      // for credit card transactions, this will appear on the statement
      "description": "Comic Books"
    }
  }

  Response:

  HTTP/1.1 200 OK
  {

  // whether or not the transaction went through
  "approved":true,

  // narrative description of the response
  "responseDescription":"Approved",

  // authorization code
  "authCode":"612797",

  // BlockChyp assigned transaction id
  "transactionId":"NZ6FGYAYLYI6TLVWNSLM7WZLHE",

  // transaction type, echoed back
  "transactionType":"refund",

  // timestamp of the transaction in UTC
  "timestamp":"2019-01-15T00:42:36Z",

  // hash of the latest tick block on the BlockChyp clockchain
  // this is essentially blockchain time
  "tickBlock":"000e61f8204a2a372cac288f833a8e0949dd50d0074d5133432dce4e78d97913",

  // for conventional credit card transactions, the BlockChyp assigned batch id
  "batchId": "UEOHSRX2MYI6RA2WSSDM7WZLHE",

  // could be CHIP, SWIPE, APPLEPAY, etc
  "entryMethod":"CHIP",

  // could be VISA, MC, DISC, AMEX, or GIFT
  "paymentType":"VISA",

  // masked account number with just the last four digits visible
  "maskedPan":"************0010",

  // cardholder name
  "cardHolder":"Test/Card 01              ",

  // indicates whether or not the authorized amount was less than the requested amount
  "partialAuth":false,

  // original test flag setting, echoed back
  "test": false,

  // currency for the authorization
  "currencyCode":"USD",

  // the final requested amount
  // this could be more than the original request's amount if you prompted
  // the user for a tip.
  "requestedAmount":"20.55",

  // amount authorized by the payment network
  "authorizedAmount":"20.55",

  // public key for BlockChyp gift cards
  "publicKey": "",

  // sig file returned in hex if requested
  "sigFile":"89504e470d0a1a0a0000000d49484452000...",

  // data that developers should consider putting on their credit card receipts
  "receiptSuggestions":{
      // EMV Application Identifier - required on all EMV receipts
      "AID":"A0000000031010",

      "ARQC":"E0A09074268A87F4",
      "IAD":"06010A03A0B800",
      "TVR":"0240008000",
      "TSI":"E800",
      "merchantName":"Test Merchant",
      "applicationLabel":"VISA CREDIT",
      "requestSignature":true,
      "maskedPan":"************0010",
      "authorizedAmount":"20.55",
      "transactionType":"refund",
      "entryMethod":"CHIP"
    }
  }


Gift Activate (/api/gift-activate)
----------------------------------

:HTTP Method: POST
:Path:  /api/gift-activate

Activates or adds value to a BlockChyp gift card.  The terminal will prompt
the user to swipe a gift card and the amount specified will be added to it.

.. note::  This only works with official BlockChyp gift cards.  All developer kits include test gift cards and merchants can order custom gift cards with their own branding.

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

    // whether or not the transaction went through
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

Signature Capture
---------------------

BlockChyp defaults to capturing written signatures for EMV cards with signature
CVM's and most magnetic stripe transactions.

By default these images are uploaded to the gateway and stored for later
retrieval in the dashboard.  This should be fine for most cases, but if you want
signature images returned as part of the response, add
`sigWidth` and `sigFormat` values to the request. These options, when used, will return the image in the
response as hex in whatever file format is specified in the sigFormat parameter.

As of this writing, jpeg, gif, and png formats are supported.  You can also scale the
max width of the image with `sigWidth`.  This will scale the image to whatever max
width you provide, preserving the original aspect ratio.

We do recommend that unless developers have a specific reason why they need to archive
signatures, that they just ignore all this and leave it to the dashboard.

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
