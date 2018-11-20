.. _sdk-guide:

SDK Developers Guide
====================

Congratulations on embarking on the noble quest to create your own BlockChyp SDK.
We thank you and we honor you and we wish you well.

This document will give you some advice and tips about how to think the BlockChyp
way and make your noble efforts successful.

.. note::  This is the obligatory section where we encourage you not to develop your own SDK if there are already good SDKs available for your platform.  As of this writing BlockChyp has developed and supports Java and Javascript SDKs for your enjoyment.  More are on the way.

Simplicity First
----------------

BlockChyp's mission is simplicity.  If you've ever done an integration with a
card present EMV payment system, you've been through hell.  These systems are -
more often than not - overly complex, poorly documented, and when you run into
trouble, straight answers from the payment companies are rare.

Our goal is the make sure the developer using an SDK has to do as little as
possible.  Business logic and all the logic about how transactions are routed
should be totally encapsulated by the SDK.

A developer should be able to pass a simple payment request into your SDK, and
your SDK should automatically figure out whether or not the route the transaction
to the BlockChyp gateway or to a terminal.  When a terminal is referenced by name,
you SDK should make use of the Gateway's Route API to locate the terminal on the
local network or relay the transaction to the terminal through the cloud. (Also
known as the freaking Internet.)

Blockchain Stuff
----------------

Fear not if you've never done any blockchain coding before; you won't be doing any now.
The BlockChyp payment gateway and terminals use a ton of blockchain stuff under the
hood, but it's totally encapsulated by the gateway and terminal firmware.  You
and your developers don't need to worry about any of it.

Certain transaction types, like gift card and cryptocurrency transactions, will
return some blockchain information - like transaction hashes - but you can treat
them just like normal passthrough identifiers.


The Transactions
----------------

BlockChyp SDKs should support the following basic transactions:

- Charge: This is a direct capture transaction in which a transaction is authorized and captured in a single step.  Most common.
- Preauth: This transactions authorizes a card and delays capture.  Used a lot in hospitality transactions where the final capture amount might be different or if regulations require delaying capture until shipment.
- Capture: Captures a previously authorized pre-authorization. (Did that sound repetitive?)
- Void:  Cancels a previous pre-authorization.
- Reverse:  Executes a time out reversal.
- Ping: Tests connectivity with a terminal.  It's basically ping for terminals.
- Enroll: Collects a payment method and enrolls it in the token vault without authorization.
- Refund: Executes a refund.
- Gift Activate: Activates or recharges a BlockChyp gift card.  (Terminal Only).

Transaction Routing
-------------------

Transactions can be sent to the BlockChyp gateway or a BlockChyp terminal, depending
on the type and context.

For example, gift cards transactions must always be sent to a terminal.  Charge transactions
could be sent to the terminal or the gateway, depending on context.  If the charge
transaction includes a token, mag stripe, or primary account number; the transaction
can be routed directly to the gateway.  But in most cases, the transaction only
has a terminal name or IP address, the transaction will need to be sent to a terminal.

Terminal Route Image
