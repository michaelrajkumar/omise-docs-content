---
title: "Getting Started with Omise"
slug: "getting-started"
description: "Learn how to integrate Omise payment gateway into your application"
category: "get-started"
sidebar_label: "Getting Started"
position: 1
difficulty: "beginner"
tags: ["quickstart", "integration", "api", "setup"]
---

# Getting Started with Omise

Welcome to the Omise API documentation. This guide will help you get started with integrating Omise payment solutions into your application.

## Prerequisites

Before you begin, make sure you have:

- An Omise account ([Sign up](https://dashboard.omise.co/signup))
- Your API keys (available in your dashboard under Settings > Keys)
- Basic knowledge of RESTful APIs
- An application or website to integrate payments

## API Keys

Omise provides two sets of API keys:

### Test Mode Keys
- Start with `skey_test_` (Secret Key) and `pkey_test_` (Public Key)
- Use for development and testing
- No real money is charged
- Test card numbers are available for various scenarios

### Live Mode Keys
- Start with `skey_live_` (Secret Key) and `pkey_live_` (Public Key)
- Use for production
- Real transactions are processed
- Keep your secret key secure - never expose it client-side

## Authentication

All API requests must be authenticated using HTTP Basic Authentication:

```bash
curl https://api.omise.co/charges \
  -u skey_test_your_secret_key:
```

The secret key acts as the username, and the password is left empty (note the trailing colon).

## Making Your First Charge

Here's a quick example of how to create a charge:

### Step 1: Create a Token (Client-side)

First, tokenize the card details using Omise.js (client-side):

```javascript
Omise.setPublicKey('pkey_test_your_public_key');

Omise.createToken('card', {
  name: 'John Doe',
  number: '4242424242424242',
  expiration_month: '12',
  expiration_year: '2025',
  security_code: '123'
}, function(statusCode, response) {
  if (response.object === 'token') {
    // Send response.id to your server
    console.log(response.id);
  }
});
```

### Step 2: Create the Charge (Server-side)

Use the token to create a charge on your server:

```bash
curl https://api.omise.co/charges \
  -u skey_test_your_secret_key: \
  -d "amount=100000" \
  -d "currency=THB" \
  -d "card=tokn_test_token_from_step_1"
```

```json
{
  "object": "charge",
  "id": "chrg_test_5h2rur6ug2fqv8uo0qx",
  "amount": 100000,
  "currency": "THB",
  "status": "successful",
  "authorized": true,
  "captured": true
}
```

## SDK Libraries

Omise provides official libraries for popular languages:

| Language | Installation |
|----------|--------------|
| Ruby | `gem install omise` |
| Python | `pip install omise` |
| PHP | `composer require omise/omise-php` |
| Node.js | `npm install omise` |
| Go | `go get github.com/omise/omise-go` |
| Java | Available via Maven |
| .NET | Available via NuGet |

## Next Steps

- [Accept Credit Cards](/accept-credit-cards) - Learn how to accept credit card payments
- [Alternative Payment Methods](/payment-methods) - Explore local payment options
- [API Reference](/api-reference) - Complete API documentation
- [Webhooks](/webhooks) - Set up real-time notifications
- [Testing Guide](/testing) - Test your integration

## Support

Need help? Check out these resources:

- [API Status](https://status.omise.co) - Service health status
- [Support Portal](https://support.omise.co) - Contact our support team
- [GitHub](https://github.com/omise) - Open source libraries and examples
