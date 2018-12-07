.. _gateway-rest-api:

Payment Gateway REST API Reference
==================================

BlockChyp consists of two REST API's: the small API that runs on every BlockChyp
payment terminal and the Gateway API.

The BlockChyp Gateway was designed to be pretty simple.  It should be familiar to
anyone who's done a previous payment gateway implementation.  The gateway provides
endpoints for conventional MSR and keyed transactions, but we strongly encourage
developers to focus on using the terminal for initial authorizations and use
the gateway strictly for managing authorizations and getting terminal route
information.
