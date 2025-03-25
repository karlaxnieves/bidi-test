---
title: Confirm a transaction asynchronously
excerpt: >
  If an **asynchronous** transaction was created without setting the
  `auto_confirm` flag, this endpoint will have to be called to confirm the
  transaction. Once successfully confirmed, the transfer order will be submitted
  to the operator to be processed.


  Please note that only unexpired transactions can be confirmed, as denoted in
  the `confirmation_expiration_date` field of the transaction. Beyond this, the
  only allowed change is to [cancel the
  transaction](/#tag/Transactions/paths/~1transactions~1{transaction_id}~1cancel/post),
  so as to release the held balance.
api:
  file: digital-value-services-api.json
  operationId: postTransactionConfirmAsync
hidden: false
---