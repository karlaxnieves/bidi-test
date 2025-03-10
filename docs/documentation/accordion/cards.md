---
title: cards
deprecated: false
hidden: false
metadata:
  robots: index
---
<Cards columns={2}>
  <Card title="OAuth Access Tokens" icon="fa-shield">
    Supported on ALL endpoints and required for any call that accesses customer-specific data. Require frequent renewal (every 15 minutes).
  </Card>

  <Card title="API Keys" icon="fa-key">
    Long-lived keys supported on select endpoints that don't access customer-specific data, such as getting People or Company data and analysis.
  </Card>
</Cards>