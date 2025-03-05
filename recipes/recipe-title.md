---
title: Recipe Title
description: Recipe Description
hidden: false
recipe:
  color: '#018FF4'
  icon: 🦉
---
```node Node
const express = require('express');
const crypto = require('crypto');

const app = express();

// Your callback password from Confirmo settings
const CALLBACK_PASSWORD = "yourCallbackPassword";

app.use(express.text({ type: '*/*' }));

app.post('/webhook', (req, res) => {
    const signature = req.header('bp-signature');
    const payload = req.body;

    // Check if signature exists
    if (!signature) {
        return res.status(400).send('Signature missing');
    }

    // Validate the signature
    if (!isValidSignature(payload, signature)) {
        return res.status(401).send('Invalid signature');
    }

    // Process the webhook payload
    // For example, parse the JSON and update your data
    console.log('Received valid webhook:', payload);
    
    // Return a 200 OK response to acknowledge successful processing
    res.status(200).send('OK');
});

function isValidSignature(payload, receivedSignature) {
    const payloadWithPassword = payload + CALLBACK_PASSWORD;
    const hash = crypto.createHash('sha256').update(payloadWithPassword, 'utf8').digest('hex');
    return hash.toLowerCase() === receivedSignature.toLowerCase();
}
```

```json Response Example
{"success":true}
```

# test

<!-- node@ -->

testing step 1