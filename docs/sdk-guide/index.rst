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

Fear not if you've never done any blockchain coding before; you won't be doing any now, either.
The BlockChyp payment gateway and terminals use a ton of blockchain stuff under the
hood, but it's totally encapsulated by the gateway and terminal firmware.  You
and your developers don't need to worry about any of it.

Certain transaction types, like gift card and cryptocurrency transactions, will
return some blockchain information - like transaction hashes - but you can treat
them just like normal passthrough identifiers.


The Transactions
----------------

BlockChyp SDKs should support the following basic transactions:

- **Charge**: This is a direct capture transaction in which a transaction is authorized and captured in a single step.  Most common.
- **Preauth**: This transactions authorizes a card and delays capture.  Used a lot in hospitality transactions where the final capture amount might be different or if regulations require delaying capture until shipment.
- **Capture**: Captures a previously authorized pre-authorization. (Did that sound repetitive?)
- **Void**:  Cancels a previous pre-authorization.
- **Reverse**:  Executes a time out reversal.
- **Ping**: Tests connectivity with a terminal.  It's basically ping for terminals.
- **Enroll**: Collects a payment method and enrolls it in the token vault without authorization.
- **Refund**: Executes a refund.
- **Gift Activate**: Activates or recharges a BlockChyp gift card.  (Terminal Only).
- **Close Batch**: Closed the current batch. (Not required if batches auto close.)

Transaction Routing
-------------------

Transactions can be sent to the BlockChyp gateway or a BlockChyp terminal, depending
on the type and context.

For example, gift card transactions must always be sent to a terminal.  Charge transactions
could be sent to the terminal or the gateway, depending on context.  If the charge
transaction includes a token, mag stripe, or primary account number; the transaction
can (and must) be routed directly to the gateway.  But in most cases, the transaction only
has a terminal name or IP address, the transaction will need to be sent to a terminal.

Terminal Route Image

Authentication
--------------

BlockChyp merchant credentials don't have silly things like MID's.  A given merchant
can have any number of unique credentials with restricted permissions.  In
BlockChyp, credentials take the form of three values:

- **API Key**: Static value that identifies the merchant.
 (``e.g.: CINR73MIHX337KMRHW7BI5I2AM``)
- **Bearer Token**:  A magic token that goes along with the API Key.
 (``e.g.: JTLQJNLSM4IGCKJC2MK7COW2VA``)
- **Signing Key**: Magic signing key used to create HMAC headers for API requests.
 (``e.g.: c7722b911f9821e742f248af8449f12f06304c18b48b902f7cdef3d9dea7ed34``)

What are all these credentials for?  We're glad you asked. Here at BlockChyp HQ,
we believe in defense in depth and we use three credentials instead of two in
order to deal with offsetting security issues.

Bearer Tokens are passed in the authorization header and checked against the Bearer Token
on file for the API Key in our database.  We store the bearer tokens in ASIC resistant
salted one way hashes, as I'm sure you're already doing for all your sensitive tokens and passwords.
(If not, you better stop worrying about making an SDK and go fix that.  Now.)
So, the tokens are safe in our database, but they still fly over the network
where they might be intercepted if TLS 1.2 ever lets us down.

This is why we also have HMAC headers.  We require each API request to include an
HMAC hash generated with the Signing Key.  This protects API credentials in the event that
gateway traffic is intercepted.  The bearer tokens protect credentials in the event that
the symmetric encryption keys used to encrypt the signing keys are breached.

They work together.  It's called teamwork.


SSL On Local Networks
---------------------

Sensitive PCI stuff like card numbers and mag stripes will never be returned from
terminal API calls, and terminals won't accept any requests that contain this sensitive
information.  However, merchant API credentials (either transient or fixed) must
be sent to the terminal with each request.  The damage that can be done with API
credentials is limited and if you use the transient terminal credential approach
(which we highly recommend), it's even more limited.

But we still recommend that you send all your API requests to terminals over HTTPS.

SSL (actually TLS these days) is tricky on closed point-of-sale networks, but that shouldn't
stop you from trying.  BlockChyp terminals run HTTP on port 8000 and HTTPS on port 8443.

When BlockChyp terminals are activated, they generate a unique TLS certificate ultimate signed
by BlockChyp's internal root certificate authority.  When you set up your HTTP client
to communicate with a BlockChyp terminal, make sure you instruct the client to trust
the following root certificate:

**BlockChyp Root Certificate For Private Terminal Networks**::

  -----BEGIN CERTIFICATE-----
  MIIGFjCCA/6gAwIBAgIJALDiHqCHT1NfMA0GCSqGSIb3DQEBCwUAMF4xCzAJBgNV
  BAYTAlVTMRMwEQYDVQQIDApXYXNoaW5ndG9uMRIwEAYDVQQHDAlLZW5uZXdpY2sx
  EjAQBgNVBAoMCUJsb2NrQ2h5cDESMBAGA1UEAwwJQmxvY2tDaHlwMB4XDTE4MTEx
  NDE4MjA1NloXDTI4MTExMTE4MjA1NlowXjELMAkGA1UEBhMCVVMxEzARBgNVBAgM
  Cldhc2hpbmd0b24xEjAQBgNVBAcMCUtlbm5ld2ljazESMBAGA1UECgwJQmxvY2tD
  aHlwMRIwEAYDVQQDDAlCbG9ja0NoeXAwggIiMA0GCSqGSIb3DQEBAQUAA4ICDwAw
  ggIKAoICAQDClGWLcgZeG0ZYlc96NcY5glo2xMPIHBZWgGN3gJggoDizsG7vdYE6
  qnHClgaMFApvM/5i4xKCGLDcmtWPGwtwyMm0Vz/L3I3mQLeM6Ygh1BmqYiORTX1E
  eByGvqi0caKiMvu1JcSi/vHxR7SdBt5HisIaH2aOQAxFFXNiU5WpCzUIDB97OcFV
  /z3HHX1VtxwAMQCdBUbotrnhUffZ2y2hG2pgPH1eACF8VaWY45AmZYSzSPPVZI5E
  U5/mwNrsIlW3A6nq5XK29KCJwwOxtWVwoaKbZyhjzcNtSO1YiZhCvRSMqPeodZ2d
  aYoPucHOUbiHo6IJDCea/Oao48diuFC95IqWW8ysFG6DIdKglYw6ZuKNOgQd9Tfc
  fT4i7Ymdh9ovgLQqwEO6lGa80XmyNo6DIDxrEquKop7VaMK461ggU/nE6Uaj0Bua
  CSqzsxVY1IA2CNC1tph7J8x1SprQV7hjQm+9G4REYILRgZU4gYNLqtJu3DEOZzW6
  oChRBXzylqWTT89n4ZQxCtQfr8IT968YmiR6mQgwGj84kuhXTdKr4tFAunr61fsb
  yfY+QAYqbkoyP4trFJXbxyXL4cwZSxtVanNpC+Xbn3P1q42CCbi0LhO0+WnL3Y2y
  k61SCS4Oy1nm7a6INY9JOXkYudtcVd1rkeF7FdlASJ8FHX36N543AwIDAQABo4HW
  MIHTMA4GA1UdDwEB/wQEAwIBBjAPBgNVHRMECDAGAQH/AgEAMB0GA1UdDgQWBBRD
  nhpcg+DqoL9LiCcfE5RLxwwR6zCBkAYDVR0jBIGIMIGFgBRDnhpcg+DqoL9LiCcf
  E5RLxwwR66FipGAwXjELMAkGA1UEBhMCVVMxEzARBgNVBAgMCldhc2hpbmd0b24x
  EjAQBgNVBAcMCUtlbm5ld2ljazESMBAGA1UECgwJQmxvY2tDaHlwMRIwEAYDVQQD
  DAlCbG9ja0NoeXCCCQCw4h6gh09TXzANBgkqhkiG9w0BAQsFAAOCAgEAbAnyHFNU
  REvCOiKfMZLuiFdjYfp4lZGBVqwOB601s95ZWoDaAQ0i71KvPcQimUPF1Uwinbqy
  MWW27fxyKuCkl8AhlFltf42DN6McUVJK99i1aHVpq3KZZtYCnyHKj/k5YtJCZT2n
  rC/TaiLYFCL6ziscvbM4xd+VWv2xOgck5qkbw5KR8w3LuAOdzXDBiFp1XuEWpZWW
  piPEf4iPZrpV+bTJPqG9Y2xbPE3OZSSWQi0HAGP+jbiqSPK/ozlNOEOuwLNQlVWe
  tBY3nbe+UYabONUOJzxG2kKTmt8WAcVXU6skBP2MotGV0JeQer0fuUMlAWxipYFS
  Vh3gjrAfZ1gbARbykVHp6t3lvLXewj86LjD/zAh+8smS7sWPs30TJKaeWueFcPta
  rh10pVFE2wN+euDVO4t4Kx/O0sksiOhpM9744pk7SjJ3rXWXPNkoWVDonkWD0RVr
  pBcA892hcq7Kq9UznbMxfARBuKv1oyyMJqaoJXA1RGIzr0+Hna8YJYlD+zzTUVJ/
  bgcKrUgfNu+mQwF7c8UEK92f32XRTJ5PQfbL58ZYdWhJnU7q4B9m6sNPFosfPbOL
  aqGzz4Mc40qJgCWNrGwB+H9LHjOAiV7nXy//HsXMxzjprhwDD0+N3wV+M4H1gGpz
  lx3y1Bdb/A3T0axxAwax4jhNQbDQ2dqyXN0=
  -----END CERTIFICATE-----

.. warning::  Don't globally trust the certificate above.  It should be trusted only by the HTTP client instances that communicate with payment terminals.  Use your platform default certificate bundles for all other HTTP communication, including with the BlockChyp gateway.
