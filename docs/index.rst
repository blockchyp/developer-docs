.. BlockChyp Developer Documentation master file, created by
   sphinx-quickstart on Mon Nov 19 14:27:35 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

BlockChyp Developer Center
=============================================================

Welcome to BlockChyp's developer documentation center.

SDKs and Clients
-----------------

- `Go SDK <https://github.com/blockchyp/blockchyp-go>`_
- `Windows and Linux Binaries <https://github.com/blockchyp/blockchyp-go>`_
- `Python SDK <https://github.com/blockchyp/blockchyp-python>`_
- `Javascript SDK <https://github.com/blockchyp/blockchyp-js>`_
- `Java SDK <https://github.com/blockchyp/blockchyp-java>`_

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


Terminal Integration
--------------------

Most developers are interested in integrating with BlockChyp terminals to run card present transactions.

E-Commerce Integration
----------------------

BlockChyp also does that fancy in browser tokenization you've heard so much about.

Roll Your Own SDK
--------------------

If there's not an SDK for your favorite language or platform, checkout out our
SDK developers guide for our advice about how to make your own.

.. toctree::
   :maxdepth: 1

   sdk-guide/index





.. toctree::
   :maxdepth: 2
   :caption: Contents:



Indices and Tables
==================

* :ref:`genindex`
* :ref:`search`
