.. _sdk-guide:

SDK Developers Guide
====================

Congratulations on embarking on the noble quest to create your own BlockChyp SDK.
We thank you and we honor you and we wish you well.

This document will give you some advice and tips about how to think the BlockChyp
way and make your noble efforts successful.

.. note::  This is the obligatory section where we encourage you not to develop your own SDK if there are already good SDK's available for your platform.  As of this writing BlockChyp has developed and supports Java and Javascript SDK's for your enjoyment.  More are on the way.

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

The Transactions
----------------

BlockChyp SDKs should support the following basic transactions:

- Charge: This is a direct capture transaction where a transaction is authorized and captured in a single step.
- Preauth: This transactions authorizes a card and delays capture.  Used a lot in hospitality transactions.
- Void: 
