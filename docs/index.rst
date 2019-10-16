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

Next, examine the list of SDKs and Clients and follow the instructions for your
technology ecosystem.


SDKs and Clients
-----------------

All supported BlockChyp SDKs are open source and hosted on Github.


- `.NET / C# SDK <https://github.com/blockchyp/blockchyp-csharp>`_
- `Go SDK <https://github.com/blockchyp/blockchyp-go>`_
- `Javascript SDK <https://github.com/blockchyp/blockchyp-js>`_
- `Java SDK <https://github.com/blockchyp/blockchyp-java>`_

What If My Platform Isn't Listed?
----------------------------------

If we don't have a client or SDK for your language or platform listed, you have
plenty of other options.

Use The Rest API's
*********************

The SDKs aren't required. We just provide them to make things a little bit simpler
and faster for developers.  The BlockChyp Gateway and all BlockChyp terminals
run simple REST APIs you can integrate with directly.  Checkout the `Terminal REST API Reference <rest-api/terminal/index.html>`_
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

Gift Cards
------------

The BlockChyp gift card system is quite a bit different from conventional gift cards.
Conventional gift cards, just like credit cards, typically rely on 16 digit
primary account numbers.  These numbers are relatively easy to guess via brute force,
which makes them vulnerable to a host of security problems.  But unlike conventional
credit card numbers, gift cards aren't protected by PCI and often implemented by
white listing gift card BIN ranges.  They're usually the most insecure part of any
payment system.

BlockChyp gift cards do have 16 digit numbers printed on them for familiarity and
convenience, but these numbers are for identification purposes only.  Under the hood
every BlockChyp gift card has a unique set of P256 elliptic curve keys.

The elliptic curve keys are used to open a new blockchain when new gift cards are
activated and as the card is redeemed and recharged, the keys are used to sign
transactions.  This means that shared secrets (i.e. primary account numbers) are
never transmitted over the network with a BlockChyp gift card transaction.  The
transaction could be intercepted or sent in the clear and not compromise the gift card.

We've gone through a lot of trouble to insulate developers from dealing with blockchain
details.  BlockChyp gift card transactions are like any other transaction with the exception
of how gift cards are identified.  Authorization requests for gift card transactions
return the elliptic curve public key in the publicKey field.  If you need to keep track of gift
cards in your SaaS or point-of-sale systems, use the public key.  A remaining balance is
also returned with every gift card authorization.

Although you don't have to understand blockchain technology to work with BlockChyp
gift cards, the merchant and developer dashboards to expose methods of browsing and
viewing the raw blockchain for the curious.

Third Party Gift Cards
*************************

For merchants with existing gift card liability, these gift cards can be easily
imported into BlockChyp.  Simply provide your liability report to your BlockChyp
representative.  BlockChyp will review the liability data and import the gift cards
into your BlockChyp account

Third party gift cards are redeemed just like BlockChyp gift cards.  Simply swipe
them on the terminal at the present card prompt.

BIN Range White Listing
--------------------------

In the event that developers need to process certain card types outside of BlockChyp,
it is possible to white list BIN ranges and have the raw card data returned.  Configuring
a merchant account for white listing does require a security review by BlockChyp.
If you need to white list a BIN range, contact BlockChyp support to complete the setup.

Once the configuration is complete, white listed card responses will look this like this...

White List Sample Response::

  {
    "responseDescription": "WHITELISTED",
    "transactionId": "",
    "transactionRef": "26476425504",
    "transactionType": "charge",
    "timestamp": "",
    "tickBlock": "",
    "test": false,
    "approved": false,
    "authCode": "",
    "sigFile": "",
    "receiptSuggestions": {
      "authorizedAmount": "0",
      "transactionType": "charge",
      "cashBackAmount": "0"
    },
    "partialAuth": false,
    "altCurrency": false,
    "fsaAuth": false,
    "currencyCode": "",
    "requestedAmount": "0",
    "authorizedAmount": "0",
    "tipAmount": "0",
    "taxAmount": "0",
    "requestedCashBackAmount": "0",
    "authorizedCashBackAmount": "0",
    "whiteListedCard": {
      "bin": "778273",
      "track1": "B7782730094745057^SQUARE GIFT CARD^99127069345",
      "track2": "7782730094745057=99127069345",
      "pan": "7782730094745057"
    },
    "storeAndForward": false
  }

In order to make the customer experience seamless, a white listed response will
return to the POS while leaving the terminal screen unchanged.  We recommend
that developers call the BlockChyp message API to display an approved or declined
response on the terminal screen after the white listed card has been processed.

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

REST APIs
----------

We recommend developers use SDKs instead of the REST APIs directly as it will
make implementation go faster.  Our SDKs deal with HMAC authentication and locating
terminals for you.  But if you prefer to do a direct REST API integration, we won't stop you.
The REST APIs aren't complicated.  Check out the API docs below:

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

Store and Forward Testing
--------------------------

BlockChyp terminals include the ability to store transactions for later authorization
in the event of a network outage.  To test this feature, ensure your test merchant
account has a non-zero floor limit set on the merchant profile.  You can then test store
and forward by unplugging the terminal from the network in the middle of a transaction or
you can simply use a special trigger amount with all sevens (e.g. 0.77, 7.77, 77.77) and
the terminal will simulate a network outage and process the transaction as a store and
forward transaction.

Terminals attempt to forward offline transactions every two minutes until they
are successfully processed.  To test a declined store and forward transaction, try
running an amount with all sevens below the floor limit with the decline test card.


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
