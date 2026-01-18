# Stripe Payment Setup for Vibecode

> Step-by-step guide to connecting Stripe for subscription billing and checkout

You've built your app in Vibecode and you're ready to accept payments. Whether you're selling subscriptions, one-time purchases, or premium features, Stripe makes it easy to handle payments securely. This guide walks you through connecting Stripe to your Vibecode app so you can start accepting payments.

## What is Stripe Integration?

Stripe is a payment processing platform that handles the complex parts of accepting payments—security, compliance, fraud prevention, and payment methods. When you integrate Stripe with your Vibecode app, you're connecting your app to Stripe's infrastructure so customers can pay you securely.

Think of it like setting up a cash register in a physical store. You need the register itself (Stripe), you need to connect it to your point-of-sale system (your Vibecode app), and you need to configure it with your business information (API keys and webhooks). Once everything is connected, payments flow smoothly from your customers to your account.

Once integrated, Stripe handles everything from processing credit cards to managing subscriptions, sending receipts, and handling refunds. Your app just needs to trigger the right Stripe actions at the right times.

### Why Use Stripe?

You might be considering other payment processors, but Stripe offers some key advantages:

* **Developer-friendly** — Stripe's API is well-documented and straightforward to integrate
* **Global reach** — Accept payments from customers worldwide in multiple currencies
* **Subscription management** — Built-in tools for recurring billing, plan changes, and cancellations
* **Security** — PCI compliance is handled by Stripe, so you don't need to worry about storing sensitive card data
* **Vibecode integration** — Works seamlessly with Vibecode's environment variable system

Now, if you're ready to start accepting payments, follow the step-by-step guide below!

## Step 1: Create a Stripe Account

First, you'll need a Stripe account. Visit [stripe.com](https://stripe.com) and sign up. Complete all the verification steps in the Stripe Dashboard—this includes confirming your email, verifying your identity, and setting up your business information.

![Create Account](01-create-account.png)
![Create Project](01b-create-project.png)
![Name Your Account](01c-name-account.png)
![Use Existing Account](01d-existing-account.png)

Stripe will guide you through the verification process. Once complete, you'll have access to your Stripe Dashboard where you can manage products, view payments, and configure settings.

## Step 2: Get Your API Keys

Your API keys are how your Vibecode app authenticates with Stripe. Think of them like a username and password—your app needs them to communicate with Stripe's servers.

**Important:** In the top right of your Stripe Dashboard, make sure you're switched to **Live Account** (not Sandbox). Using test keys in production will cause your payments to fail.

![API Keys](02-api-keys.png)

Your API keys should be visible on the right side of the Stripe homepage. If you don't see them:

1. Click **Developers → API keys** in the bottom left
2. You'll see two keys:
   - **Publishable key** (starts with `pk_`) — Safe to use in frontend code
   - **Secret key** (starts with `sk_`) — Keep this secret, only use in backend code

Copy both keys—you'll need them in the next step.

  <Tip>
  **Test vs. Live keys:** Stripe gives you separate keys for testing and production. Make sure you're copying the **Live** keys (not Test mode keys) for your deployed app. Test keys will work during development but won't process real payments.
</Tip>

## Step 3: Add API Keys to Vibecode

Now you'll add your Stripe keys to your Vibecode project's environment variables. Environment variables are secure storage for sensitive information like API keys—they're accessible to your app's code but not exposed in your source files.

1. Navigate to [vibecodeapp.com](https://vibecodeapp.com) and open your project
2. Click **"Env Var"** on the bottom bar (you may need to scroll to find it)

![Vibecode ENV Variables](03-env-variables.png)

**For the Frontend:**
1. Click on **Frontend**, then click **"add variable"**
2. Enter the key name: `VITE_STRIPE_PUBLISHABLE_KEY`
3. Enter the value: your Publishable key (the `pk_...` you copied earlier)

![Publishable Key](03-publishable-key.png)

**For the Backend:**
1. Click on **Backend**, then click **"add variable"**
2. Enter the key name: `STRIPE_SECRET_KEY`
3. Enter the value: your Secret key (the `sk_...` you copied earlier)

![Secret Key](03-secret-key.png)

  <Warning>
  **Keep your Secret key safe:** Never share your Secret key or commit it to version control. It has full access to your Stripe account. The Publishable key is safe to use in frontend code, but the Secret key should only be used in backend/server code.
</Warning>

## Step 4: Create Your Product

Before customers can purchase anything, you need to create a product in Stripe. A product represents what you're selling—whether that's a subscription plan, a one-time purchase, or access to premium features.

In the Stripe Dashboard:

1. Navigate to **Product Catalog → Add a product**
2. Enter a product name (e.g., "Pro Plan")
3. Add a description (optional but recommended)
4. Select **Recurring** billing
5. Set your price (e.g., $4.99)
6. Choose billing period: **Monthly** or **Yearly**
7. Click **Add product**
8. Click on your newly created product in the product catalog

![Products](04-products.png)
![Create Product](04b-create-product.png)
![Add Product](04c-add-product.png)
![Product Catalog](04d-product-catalog.png)

You can create multiple products for different plans or features. Each product will have its own price and can be configured independently.

## Step 5: Get Your Price IDs

Every price in Stripe has a unique ID that your app uses to reference it. You'll need to copy this ID and add it to your Vibecode environment variables so your app knows which product to charge for.

1. In your product page, click the **3 dots** menu on the right side of the price
2. Click **"Copy price ID"**
3. Return to [vibecodeapp.com](https://vibecodeapp.com)
4. Open **Env Var**
5. Click **"Backend"**
6. Click **"Add Variable"**
7. Enter the key name: `STRIPE_PRICE_ID_MONTHLY`
8. Enter the value: the price ID you just copied

![Copy Price ID](05-copy-price-id.png)
![Price ID Monthly](05b-price-id-monthly.png)

<Note>
  **Multiple pricing options:** If you offer both monthly and yearly plans, repeat this process for your yearly price using the key name `STRIPE_PRICE_ID_YEARLY`. Your app can then offer customers a choice between billing periods.
</Note>

## Step 6: Set Up Webhooks

Webhooks are how Stripe notifies your app when important events happen—like when a payment succeeds, a subscription is updated, or a customer cancels. Without webhooks, your app wouldn't know when these events occur, which could lead to customers having access they shouldn't (or vice versa).

Think of webhooks like a notification system. When something happens in Stripe (a payment completes, a subscription renews), Stripe sends a message to your app saying "Hey, this thing happened." Your app can then react accordingly—granting access, sending a confirmation email, or updating a database.

**In Stripe Dashboard:**

1. In the bottom left, click **"Developers"**
2. Click **"Webhooks"**
3. Click **"Add destination"**
4. Choose **"Your Account"**
5. In the search bar, select these 3 events:
   - `checkout.session.completed` — Fires when a customer completes a checkout
   - `customer.subscription.updated` — Fires when a subscription changes (plan upgrade, renewal, etc.)
   - `customer.subscription.deleted` — Fires when a subscription is cancelled
6. Click **"Continue"**
7. Choose **"Webhook endpoint"**
8. Enter your endpoint URL (ask the Vibecode agent for this URL)
9. Click **"Create destination"**
10. On the right side, locate the **"Signing Secret"**
11. Copy it to your clipboard

![Webhook Step 1](06-webhook-1.png)
![Webhook Step 2](06-webhook-2.png)
![Webhook Step 3](06-webhook-3.png)
![Webhook Step 4](06-webhook-4.png)
![Webhook Step 5](06-webhook-5.png)
![Webhook Step 6](06-webhook-6.png)
![Webhook Step 7](06-webhook-7.png)
![Webhook Step 8](06-webhook-8.png)

**Add to Vibecode:**

1. Return to [vibecodeapp.com](https://vibecodeapp.com)
2. Open **Env Var**
3. Click **"Backend"**
4. Click **"Add Variable"**
5. Enter the key name: `STRIPE_WEBHOOK_SECRET`
6. Enter the value: the Signing Secret you just copied (starts with `whsec_`)

<Warning>
  **Webhook security:** The Signing Secret is used to verify that webhook requests are actually coming from Stripe and haven't been tampered with. Never share this secret or expose it in your frontend code. Always verify webhook signatures in your backend before processing webhook events.
</Warning>

## Environment Variable Summary

Here's a quick reference of all the environment variables you've set up:

| Name | Location | Example |
|------|-----------|-----------|
| `VITE_STRIPE_PUBLISHABLE_KEY` | Frontend | `pk_live_...` |
| `STRIPE_SECRET_KEY` | Backend | `sk_live_...` |
| `STRIPE_PRICE_ID_MONTHLY` | Backend | `price_...` |
| `STRIPE_PRICE_ID_YEARLY` | Backend | `price_...` (optional) |
| `STRIPE_WEBHOOK_SECRET` | Backend | `whsec_...` |

All of these should be set in your Vibecode project's environment variables before you deploy. If you're deploying your app, make sure these are configured in your deployment's environment settings as well.

## Troubleshooting

**Can't find my API keys?**

* Make sure you're logged into the correct Stripe account
* Check that you've switched to **Live Account** mode (not Test mode) in the top right
* Try navigating directly to Developers → API keys in the bottom left

**Payments not working?**

* Verify you're using **Live** API keys, not test keys
* Check that your environment variables are set correctly (no typos in key names)
* Make sure your Secret key is in Backend, not Frontend
* Review your Stripe Dashboard logs for error messages

**Webhooks not firing?**

* Verify your webhook endpoint URL is correct (ask the Vibecode agent if unsure)
* Check that you've selected the correct events in Stripe
* Make sure `STRIPE_WEBHOOK_SECRET` is set in your Backend environment variables
* Review the Webhooks tab in Stripe Dashboard for delivery attempts and errors

**Subscription updates not reflecting in my app?**

* Check that your webhook handler is processing `customer.subscription.updated` events
* Verify your webhook endpoint is receiving and processing requests (check logs)
* Make sure your app logic correctly handles subscription state changes

<Note>
  **Need help?** Reach out via Live support chat in Vibecode for the fastest support. You can also check Stripe's [documentation](https://stripe.com/docs) for detailed API reference and troubleshooting guides.
</Note>
