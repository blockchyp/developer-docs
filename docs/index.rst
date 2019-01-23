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
`BlockChyp Web Site <https://www.blockchyp.com>`_ and order a
BlockChyp Developer Kit.  The kit will include a fully functioning payment terminal,
along with chip, mag stripe, and gift card test cards.

Next, examine the list of SDK's and Clients and follow the instructions for your
technology ecosystem.


SDKs and Clients
-----------------

All supported BlockChyp SDK's are open source and hosted on Github.

- `Go SDK <https://github.com/blockchyp/blockchyp-go>`_
- `Python SDK <https://github.com/blockchyp/blockchyp-python>`_
- `Javascript SDK <https://github.com/blockchyp/blockchyp-js>`_
- `Java SDK <https://github.com/blockchyp/blockchyp-java>`_

What If My Platform Isn't Listed?
----------------------------------

If we don't have a client or SDK for your language or platform listed, you have
plenty of other options.

Use The Rest API's
*********************

The SDK's aren't required. We just provide them to make things a little bit simpler
and faster for developers.  The BlockChyp Gateway and all BlockChyp terminals
run simple REST API's you can integrate with directly.  Checkout the `Terminal REST API Reference <rest-api/terminal/index.html>`_
and the `Payment Gateway REST API Reference <rest-api/gateway/index.html>`_ for more information.

Use The Command Line
*********************

The BlockChyp Go SDK includes additional build scripts that create Windows and
Linux command line executables that can be invoked via shell commands.

More documentation on the command line can be found on the `Go SDK's Github Page <https://github.com/blockchyp/blockchyp-go>`_

Build An SDK
**********************

If you're using a modern programming language that we just
haven't gotten around to yet (like Ruby, Swift, or Rust), we'd prefer to work with you on creating an
open source SDK for your platform.  This allows other developers in your technology ecosystem
to benefit going forward.

To develop your own SDK, check out the `SDK Developers Guide <sdk-guide/index.html>`_
for some high level concepts and guidance.  Then use the REST API references as
you work through the various terminal and gateway transactions.

The `Go SDK <https://github.com/blockchyp/blockchyp-go>`_ is open source and serves
as a fairly good example of what should go into a new SDK.

If you're thinking of developing a new SDK, feel free to contact us at nerds@blockchyp.com.
We'll talk through the trade offs with you and maybe pitch in ourselves.

Standard Codes
------------------------

BlockChyp has a set of standard strings for things like Card Types and Entry Methods.

If there were industry wide standards for this sort of thing, BlockChyp would
gladly embrace them, but unfortunately that's not the way things work.  The lists
below enumerate the keys you'll need to bake into your system as constants for BlockChyp.

Card Types
**********************

This list enumerates the codes for card types and payment methods.

:VISA: Visa

:MC: MasterCard

:AMEX: American Express

:DINERS: Diners Club International

:DISC: Discover

:CUP: China  Union Pay

:JCB: Japan Card Bureau

:BC_GIFT: BlockChyp Blockchain Gift Card

Entry Methods
*********************

This list enumerates the codes for entry methods.

:CHIP: EMV chip contact

:CONTACTLESS: EMV contactless

:ECOM: E-Commerce

:SWIPE: Magnetic Stripe Read

:KEYED: Manually keyed

:APPLEPAY: ApplePay

:ANDROID: Android Pay

:SAMSUNG:  Samsung Pay

:WALLET:  Mobile Crypto Wallet

Currency Codes
*********************

This list enumerates the currency codes defined by BlockChyp.

:USD: United States Dollar

:BTC: Bitcoin Main Fork

:ETH: Ethereum

:BCP: BlockChyp's internal cryptocurrency




Receipt Requirements
------------------------

BlockChyp API responses include information that most developers will use to
create printed receipts.  Consider the following transaction response, shown
here as JSON:

**Sample Response**::

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

The response includes a number of fields consumers are used to seeing on receipts
such as masked PAN and auth code.

It also includes a receiptSuggestions section that collects all EMV fields
we recommend putting on your receipts.

Not all card brands have the same requirements, but if you always include the
following data elements on all receipts, your receipts will be compliant with
all card brand receipt requirements and recommendations.

- **authCode**
- **maskedPan**
- **AID**
- **applicationLabel**
- **ARQC**
- **TC**

REST API's
----------

We recommend developers use SDK's instead of the REST API's directly as it will
make implementation go faster.  Our SDK's deal with HMAC authentication and locating
terminals for you.  But if you prefer to do a direct REST API integration, we won't stop you.
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
Visa, Mastercard, American Express, or Discover test card number except as
described below.


Adverse Events
********************

We hope for approval in life, but that doesn't always happen.  BlockChyp has an
extensible system for simulating adverse events like declines, partial
authorizations, and other unexpected responses.

BlockChyp supports the following adverse event types:

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

If there's not an SDK for your favorite language or platform, check out our
SDK developers guide for our advice about how to make your own.

.. toctree::
   :maxdepth: 2

   sdk-guide/index

Running EMV Certification Tests
------------------------------------

.. note::  **This section is for advanced developers only. EMV Certification is not required to use BlockChyp**.


Merchants and developers who use BlockChyp do not require EMV certification and
the default test API provides abbreviated responses suitable for most purposes.
But, these simplified responses may not satisfy all the card log requirements for real
EMV tests.

If you happen to have a Collis or UL EMV Test Tool and want to run real EMV
certification tests against BlockChyp, contact us at nerds@blockchyp.com.  We'll
connect your test merchant account to a real EMV certification back end.



.. toctree::
   :maxdepth: 2
   :caption: Contents:
