.. BlockChyp Developer Documentation master file, created by
   sphinx-quickstart on Mon Nov 19 14:27:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

BlockChyp Developer Center
=============================================================

Welcome to BlockChyp's developer documentation center.

Getting Started
---------------------

To get started with BlockChyp, sign up for a developer account on the
`BlockChyp Web Site <https://github.com/blockchyp/blockchyp-go>`_ and order a
BlockChyp Developer Kit.  The kit will include a fully functioning payment terminal,
along with chip, mag stripe, and gift card test cards.

Next, examine the list of SDK's and Clients and follow the instructions for your
technology ecosystem.


SDKs and Clients
-----------------

All supported BlockChyp SDK's are open source and hosted on Github.

- `Go SDK <https://github.com/blockchyp/blockchyp-go>`_
- `Windows and Linux Binaries <https://github.com/blockchyp/blockchyp-go>`_
- `Python SDK <https://github.com/blockchyp/blockchyp-python>`_
- `Javascript SDK <https://github.com/blockchyp/blockchyp-js>`_
- `Java SDK <https://github.com/blockchyp/blockchyp-java>`_

Receipt Requirements
------------------------

BlockChyp API responses include information that most developers will use to
created printed receipts.  Consider the following transaction response, shown
here as JSON:

**Sample Response**::

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
      "applicationLabel": "Visa Credit",
      "ARQC": "649A5C5FCA0CFD24",
      "IAD": "B17C939DEA2B3A5D3030",
      "ARC": "3030"
    }
  }

The response includes a number of fields consumers are used to seeing on receipts
such as masked PAN and auth code.

It also includes a receiptSuggestion section that collects all EMV fields
we recommend putting on your receipts.

Not all card brands have the same requirements, but if you always include the
following data elements on all receipts, your receipts will be EMV compliant.

- **authCode**
- **maskedPan**
- **AID**
- **applicationLabel**
- **ARQC**
- **TC**

REST API's
----------

We recommend developers use SDK's instead of the REST API's directly, but this is
just to make things simpler.  Our SDK's deal with HMAC headers and locating
terminals for you.  But if you prefer to roll your own, we won't stop you.
The REST API's aren't complicated.  Check out the API docs below:

.. toctree::
   :maxdepth: 2

   rest-api/terminal/index

.. toctree::
   :maxdepth: 2

   rest-api/gateway/index


Test Cards
-----------------------

All BlockChyp developer kits include a set of test cards, but BlockChyp is
designed to be compatible with most common test card sets.  Any valid test
card can be used with BlockChyp.

.. warning::  This section describes BlockChyp's default test account behavior.  Developers and partners can alter and extend the test card configuration.  If these test cards and trigger amounts don't work as expected, check your adverse event configuration in the BlockChyp dashboard.

Simulating Approvals
*************************

The BlockChyp test system will send back an approval response for any valid
Visa, Mastercard, American Express, or Discover test card numbers except as
described below.


Adverse Events
********************

We hope for approval in life, but that doesn't always happen.  BlockChyp has an
extensible system for simulation adverse events like declines, partial
authorizations, and other unexpected responses.

BlockChyp supporst the following adverse event types:

:decline: Authorization declined by the payment network.

:error: Simulates a network or system error.

:fraud:  Simulates a response flagged for probable fraud.

:partial-auth: Simulates a partial authorization in which the authorized amount is less than the requested amount.

:timeout:  Simulates a transaction that takes a long time to respond, potentially triggering a time out.

:no-response:  Simulates a transaction that fails to return a response at all.

The table below shows the default trigger conditions for adverse events in BlockChyp.
Events can be triggered by either card number or amount.
These can be changed or modified for your account via the BlockChyp dashboard.

+--------------------+-------------------+-----------------+-------------------------------+
| Event Type         | Trigger Card      |  Trigger Amount |  Notes                        |
+--------------------+-------------------+-----------------+-------------------------------+
| decline            | 4111111111111129  |  201.00         |                               |
+--------------------+-------------------+-----------------+-------------------------------+
| error              | 6011000990139424  |  00.11          |                               |
+--------------------+-------------------+-----------------+-------------------------------+
| fraud              | 371449635398431   |  66.00          |                               |
+--------------------+-------------------+-----------------+-------------------------------+
| partial-auth       | 5105105105105100  |  55.00          | Authorized Amount: $25.00     |
+--------------------+-------------------+-----------------+-------------------------------+
| no-response        | 4111111111111137  |  72.00          | Returns no response.          |
+--------------------+-------------------+-----------------+-------------------------------+
| timeout            | 5555555555554444  |  68.00          | Gateway hangs for 30 seconds. |
+--------------------+-------------------+-----------------+-------------------------------+

Roll Your Own SDK
--------------------

If there's not an SDK for your favorite language or platform, checkout out our
SDK developers guide for our advice about how to make your own.

Running EMV Certification Tests
------------------------------------

.. note::  **This section is for advanced developers only. EMV Certification is not required to use BlockChyp**.


Merchants and developers who use BlockChyp do not require EMV certification and
the default test API provides abbreviated responses suitable for most purposes.
But, these simplified responses may not satisfy all the card log requirements for real
EMV tests.

If you happen to have an Collis or UL EMV Test Tool and want to run real EMV
certification tests against BlockChyp, contact us at nerds@blockchyp.com.  We'll
connect your test merchant account to a real EMV certification back end.


.. toctree::
   :maxdepth: 1

   sdk-guide/index





.. toctree::
   :maxdepth: 2
   :caption: Contents:
