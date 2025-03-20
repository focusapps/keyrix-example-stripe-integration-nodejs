# Example Keyrix + Stripe integration
The following web app is written in Node.js and shows how to integrate
[Keyrix](https://keyrix.focusapps.app) and [Stripe](https://stripe.com) together
using webhooks. Much more could be done to automate e.g. license
revocation when a subscription is canceled, etc.

> **This example application is not 100% production-ready**, but it should
> get you 90% of the way there. You may need to add additional logging,
> error handling, as well as listening for additional webhook events.
>
> If you are looking for next.js example, just implement the `/keyrix-webhooks` api route in your app.

## Running the app

First up, configure a few environment variables or rename the `.env.example` to `.env`:
```bash
# Stripe publishable key
export STRIPE_PUBLISHABLE_KEY="YOUR_STRIPE_PUBLISHABLE_KEY"

# Stripe secret key (don't share this!)
export STRIPE_SECRET_KEY="YOUR_STRIPE_SECRET_KEY"

# The Stripe plan to subscribe new customers to
export STRIPE_PRICE_ID="YOUR_STRIPE_PRICE_ID"

# Keyrix product token (don't share this!)
export KEYRIX_PRODUCT_TOKEN="YOUR_KEYRIX_PRODUCT_TOKEN"

# Your Keyrix account ID
export KEYRIX_ACCOUNT_ID="YOUR_KEYRIX_ACCOUNT_ID"

# The Keyrix policy to use when creating licenses for new users
# after they successfully subscribe to a plan
export KEYRIX_POLICY_ID="YOUR_KEYRIX_POLICY_ID"
```

You can either run each line above within your terminal session before
starting the app, or you can add the above contents to your `~/.bashrc`
file and then run `source ~/.bashrc` after saving the file.

Next, install dependencies with [`yarn`](https://yarnpkg.comg):
```
yarn
```

Then start the app:
```
yarn start
```

## Configuring the webhooks

For local development

- ngrok
create an [`ngrok`](https://ngrok.com) tunnel to your
local development server:

```bash
ngrok http 8080
```

Next up, add the generated `ngrok` URL to your Stripe and Keyrix accounts to
listen for webhooks.

1. **Stripe:** add `https://{YOUR_NGROK_URL}/stripe-webhooks` to https://dashboard.stripe.com/account/webhooks

you should add these event types depending on whether one-time or recurring:

- `checkout.session.completed`
- `charge.refunded`
- `customer.created`
- `customer.subscription.created`
- `customer.subscription.updated`
- `customer.subscription.deleted`

1. **Keyrix:** add `https://{YOUR_NGROK_URL}/keyrix-webhooks` to https://keyrix-admin.focusapps.app/webhook-endpoints

you should add these event types:

- `user.created`

> **In a production environment, you would use your actual server info in place of
> the `ngrok` URLs above.**

## Testing the integration

Visit the following url: http://localhost:8080 and fill out the purchase form.

## Common Issues

### Incorrect ENV variables

In case of errors, please double check all of your environment variables.
If one of the variables are incorrect, it may cause API authentication
issues.

### Protected account

**Please note that this example requires that your Keyrix account is
set to unprotected**, because this example handles user creation
on the front-end. You can update this setting on your [account's
settings page](https://keyrix-admin.focusapps.app/settings). If you would prefer
to keep your account protected, the logic for user creation would
need to be moved to a server-side URL.

### Other issues

Here's a few things to double check when a problem arises:

1. Make sure you're using the correct account ID (find yours [here](https://keyrix-admin.focusapps.app/settings))
1. Make sure you're using a product token or admin token (the token should start with `prod-` or `admi-`)
1. Make sure you're using the correct policy ID (it should be a UUID)
1. Make sure that your Stripe environment variables are correct
1. Make sure all dependencies have been installed via `yarn install`
1. Make sure you have correctly configured webhooks for both Keyrix _and_ Stripe
1. Make sure that the webhook URL is accessible from the public internet via `ngrok`

## Questions?

Reach out at [keyrix@focusapps.app](mailto:keyrix@focusapps.app) if you have any
questions or concerns!
