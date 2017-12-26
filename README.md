# StarterSubscriptionBundle (WIP)

## Assumptions

- That every user will only have one subscription.
- That access can be defined as a list of features.
- That the end date of a subscriptions should determine when the user loses access.
- That we want a system that can use multiple payment providers, stripe, amazon, etc.
- That the payment provider will handle subscription recurring billing.
- That customer's will only be able to change plans within a payment provider.
- That the customer will only be able to pay with currencies backed by a government, aka no bitcoin
- That doctrine single inheritance table are better then the parent child tables because of less joins.

## Definitions

- PaymentProvider: A third party that will process the user's payment information.  Stripe, Amazon, iTunes, etc.
- Plan: It is the object the user is subscribing to.  It represents time interval and amount the user will be charged on a regular interval.
- Access:  A list of things the user will get to use by subscribing to a plan.  Plan to Access is a many to many relationship.  
- Subscription: Represents that state of the current plan the user is subscribed to.  This object will have an end date that determines the length of the plan.
- Transaction: A payment made by the user.

## User Stories
```
As a < type of user >, I want < some goal > so that < some reason >
```
### Customer User Stories
- As a customer, I want to see a list of plans so that I can see if I want to subscribe.
- As a customer, I want to subscribe to a plan so that I can use the product.
- As a customer, I want to get a free trial so that I can try the product.
- As a customer, I want to resubscribe from a delinquent subscription so that I can use the product again.
- As a customer, I want to use the product so that I can get some benefit.
- As a customer, I want to upgrade my subscription, so that I can use more features.
- As a customer, I want to go from monthly to yearly so that I can save 20%.
- As a customer, I want to go from yearly to monthly so that I don't want pay it all at once.
- As a customer, I want to cancel my subscription so that because I don't want to use the product.
- As a customer, I want to resubscribe so that I can use the product again.
- As a customer, I want to update my credit card information so I don't lose access to the product.
- As a customer, I want to know if my credit card is about to expire, so that I don't lose access.
- As a customer, I want to update my subscription with credit card information because my current card is bad.

### Customer Service Agent

- As a customer service agent, I want to be able to view the customer's status so that I can better diagnose the problem.
- As a customer service agent, I want to issue a free month so that I can make up for a mistake we made.  (Not possible with iTunes)
- As a customer service agent, I want to resync the subscription so that the user can get access.
- As a customer service agent, I want to issue a receipt to the customer so that customer can file that with their accounting.
- As a customer service agent, I want to give a free subscription away so that an mvp can see what we are doing.
- As a customer service agent, I want to issue a refund to make a customer happy and cut down on charge backs.
- As a customer service agent, I want to cancel a user's subscription because I just issued a refund and the user should not have access.
- As a customer service agent, I want to downgrade / upgrade a subscription because the customer is having trouble.

### Product Person

- As a product person I want to add a new feature so that we can sell more subscriptions.
- As a product person I want to control the plans displayed on the website.
- As a product person I want to adjust the price of the plan.
- As a product person I want to encourage user's to subscribe with a free trail
- As a product person I want notify customer's of their card about to expire so that people don't become delinquent.

## Database Schema

![Subscription Database](/docs/subscription_database.png)

### Vendors Specific Subscription Fields

- Stripe
    - stripe_customer_id: A string representing the customer's account in stripe.
    - stripe_subscription_id: A string representing the id of the stripe subscription.
    - stripe_status: Represents the stripe status of a subscription.
    - stripe_card_type: MasterCard, Visa, Discover, etc
    - stripe_card_expiration_date: When the card will expire.
    - stripe_card_last_4: The last 4 digits of the credit card.
    - stripe_card_expiration_date: The credit card expiration date.
- iTunes
    - itunes_reciept_blob: The giant encoded receipt that stores all the transactions for a user.
    - itunes_original_purchase_date: The date the subscription was first purchased
    - itunes_original_transaction_id: The original transaction id of the subscription.  Compare this stripe subscription id.  This transaction id will be attached to all recurring purchases.
- Android
    - android_token: The token sent by the app to verify the receipt.  Used in all transaction calls to get info on the subscription.
    - android_cancel_reason: A number representing why the user cancelled
    - android_developer_payload: A developer-specified string that contains supplemental information about an order.	
    - android_payment_state: The payment state of the subscription:
        - Payment pending
        - Payment received
        - Free trial
    - android_start_time: Time at which the subscription was granted.
    - android_cancellation_time: The time at which the subscription was canceled by the user.
       
### Plan Table

- provider_plan_id: The payment provider's plan id, this would be the stripe plan id, itunes product id, etc.
- show_plan: 1 means to show the plan on the frontend of the site.
- price: Is an whole number to prevent weird division errors.

### Questions we want to answer

- Does the user have access to a feature?
- How much time does the user have on their subscription?
- What transaction has the customer made?
- Can customer change to a new plan?
- If possible can we know if the user's card is about to expire? 
- Has the user had a subscription in past?
- What is user's previous payment history?

## Payment Providers

- Stripe
- iTunes
- Android

## Actions

- Subscribe
- Upgrade / Downgrade
- Update Card
- Resubscribe
- Cancel
- Refund
- Sync with payment provider


## Resources:

- Android:
    - [Android Api Subscriptions](https://developers.google.com/android-publisher/api-ref/purchases/subscriptions#resource)
    - [Android Subscription Workflow](https://developer.android.com/google/play/billing/billing_subscriptions.html)
- iTunes:
    - [iTunes Receipt Validation](https://developer.apple.com/library/content/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html)
- Stripe
    - [Stripe API](https://stripe.com/docs/api)
    - [Stripe Subscription Docs](https://stripe.com/docs/subscriptions/quickstart)
