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

That's it.


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
the major certificate authorities is not support.  When BlockChyp terminals are
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
IP address and provides for a more robust implementation in the even that
installers or merchants fail to statically configure IP addresses for terminals.

**POST: https://192.168.50.245/api/charge**::

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
credentials (API Key, Bearer Token, Signing Key).

In Gateway API transactions, these values are passed in the HTTP headers.  In
Terminal API transactions, they're passed in the body of the request.

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

The example below shows a typical terminal route transaction.

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
:URI:  /api/test



Preauth (/api/preauth)
----------------------

Gift Activate (/api/gift-activate)
----------------------------------
