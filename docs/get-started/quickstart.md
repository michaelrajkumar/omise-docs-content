---
title: "Quickstart"
slug: "quickstart"
description: "Get your first payment working in 5 minutes"
category: "get-started"
sidebar_label: "Quickstart"
position: 2
difficulty: "beginner"
tags: ["quickstart", "5-minute", "first-payment"]
---

# Quickstart

Get your first payment working in 5 minutes.

## 1. Sign Up

Create your free account at [dashboard.omise.co/signup](https://dashboard.omise.co/signup).

## 2. Get API Keys

Find your test keys in **Settings → Keys**:
- Public Key: `pkey_test_...`
- Secret Key: `skey_test_...`

## 3. Install SDK

```bash
# Node.js
npm install omise

# Python
pip install omise

# PHP
composer require omise/omise-php

# Ruby
gem install omise
```

## 4. Create a Charge

```javascript
const omise = require('omise')({
  secretKey: 'skey_test_your_key'
});

// Create a charge
const charge = await omise.charges.create({
  amount: 100000,  // 1,000.00 THB
  currency: 'THB',
  card: 'tokn_test_...'  // From Omise.js
});

console.log(charge.status);  // 'successful'
```

## 5. Test Cards

| Card Number | Result |
|-------------|--------|
| 4242424242424242 | Success |
| 4111111111160011 | Decline |
| 4111111111130014 | Lost card |

Use any future expiry date and any 3-digit CVV.

## Next Steps

- [Accept Credit Cards](/accept-credit-cards)
- [Set up Webhooks](/webhooks)
- [Go Live Checklist](/go-live)
