### Stripe Payment Service

stripeController.js

```js
// create a .env file to store your API keys and secrets
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);

const stripeController = async (req, res) => {
  // While using Stripe, you have to use the smallest unit of currency.
  // For example, if you want to accept payments in USD,
  // price: 1999 would be equal to $19,99
  const { purchase, total_amount, shipping_fee } = req.body;

  const calculateOrderAmount = () => {
    return total_amount + shipping_fee;
  };

  const paymentIntent = await stripe.paymentIntents.create({
    amount: calculateOrderAmount(),
    currency: 'usd',
  });

  res.json({ clientSecret: paymentIntent.client_secret });
};

module.exports = stripeController;
```

App.js

```js
require('dotenv').config();
require('express-async-errors');

const express = require('express');
const app = express();

// controller
const stripeController = require('./controllers/stripeController');

// stripe
app.post('/stripe', stripeController);
```

A payment request should never be sent directly from the front-end to the Stripe. Since the data on the front-end can easily be manipulated, we should always check with our database first and then send a payment request by creating a payment intent. For more detailed information you can visit the Stripe docs : https://stripe.com/docs/payments/quickstart
