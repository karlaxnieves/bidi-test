---
title: Query list of transactions
excerpt: >-
  This endpoint will return a list of transactions matching the search criteria.
  Please note that when this endpoint is called without any parameters and/or if
  neither date ranges (i.e. `from_date`, `to_date`) nor `external_id` are
  specified, transactions created within the last 24 hours will be returned by
  default.
api:
  file: digital-value-services-api.json
  operationId: getTransactions
hidden: false
---