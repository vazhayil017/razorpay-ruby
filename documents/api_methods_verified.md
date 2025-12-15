# Razorpay API Methods (Verified)

**Purpose:** Document legitimate Razorpay API methods supported by the `razorpay-ruby` gem, validated against source code.

**Gem Source:** `razorpay/razorpay-ruby` (GitHub)
**Documentation Source:** Razorpay Official API Docs & Gem Source Code

> Notes on payload types:
>
> - Methods that call **POST/PATCH/PUT** generally send `options` as the HTTP body. You can pass a Ruby `Hash` (recommended) or a JSON `String` if you prefer, depending on your app and headers.
> - Methods that call **GET list/all** generally send `options` as **query parameters**. For these, pass a `Hash` (do **not** pass `.to_json`).

---

## Table of Contents

1. [Initialization](#initialization)
2. [Orders API](#orders-api)
3. [Payments API](#payments-api)
4. [Transfers API](#transfers-api)
5. [Refunds API](#refunds-api)
6. [Disputes API](#disputes-api)
7. [Merchant Settlements API](#merchant-settlements-api)
8. [Linked Accounts API](#linked-accounts-api)
9. [Stakeholders API](#stakeholders-api)
10. [Product Configuration API](#product-configuration-api)
11. [Documents API](#documents-api)
12. [Webhook & Verification Utilities](#webhook--verification-utilities)
13. [Error Handling](#error-handling)
14. [Appendix: Additional Verified Methods Present in the Gem](#appendix-additional-verified-methods-present-in-the-gem)

---

## Initialization

### 1. Setup with Key/Secret (Standard Auth)

**Method:** `Razorpay.setup(key_id, key_secret)`

```ruby
require "razorpay"

Razorpay.setup(
  ENV["RAZORPAY_KEY_ID"],
  ENV["RAZORPAY_KEY_SECRET"]
)
```

### 2. Setup with OAuth Access Token (Platform/Marketplace)

**Method:** `Razorpay.setup_with_oauth(access_token)`

```ruby
Razorpay.setup_with_oauth(ENV["RAZORPAY_ACCESS_TOKEN"])
```

### 3. Custom Headers

**Method:** `Razorpay.headers = { ... }`

Useful for passing `X-Razorpay-Account` headers for operations on behalf of linked accounts.

```ruby
Razorpay.headers = {
  "X-Razorpay-Account" => "acc_merchantId123",
  "CUSTOM_APP_HEADER" => "Platform/1.0"
}
```

---

## Orders API

### 1. Create Order

**Method:** `Razorpay::Order.create(params)`

```ruby
order = Razorpay::Order.create(
  amount: 10000,
  currency: "INR",
  receipt: "order_12345",
  notes: { custom_order_id: "12345" }
)
```

### 2. Fetch Order

**Method:** `Razorpay::Order.fetch(order_id)`

```ruby
order = Razorpay::Order.fetch("order_NmkDx2MyWuLwto")
```

### 3. List Orders

**Method:** `Razorpay::Order.all(options = {})`

```ruby
orders = Razorpay::Order.all(count: 5)
```

### 4. Fetch Payments for an Order

**Method:** `Razorpay::Order.fetch(order_id).payments`

```ruby
payments = Razorpay::Order.fetch("order_NmkDx2MyWuLwto").payments
```

### 5. Edit Order

**Method:** `Razorpay::Order.edit(order_id, options = {})`

```ruby
Razorpay::Order.edit(
  "order_NmkDx2MyWuLwto",
  { notes: { updated_key: "new_value" } }
)
```

### 6. Fetch Transfers for an Order (Route)

**Method:** `Razorpay::Order.fetch_transfer_order(order_id)`

```ruby
order_with_transfers = Razorpay::Order.fetch_transfer_order("order_XXXXXXXXXXXXXX")
# order_with_transfers["transfers"]["items"] ...
```

### 7. View RTO Review

**Method:** `Razorpay::Order.view_rto(order_id)`

```ruby
rto = Razorpay::Order.view_rto("order_XXXXXXXXXXXXXX")
```

### 8. Edit Fulfillment

**Method:** `Razorpay::Order.edit_fulfillment(order_id, options = {})`

```ruby
fulfillment = Razorpay::Order.edit_fulfillment(
  "order_XXXXXXXXXXXXXX",
  {
    payment_method: "upi",
    shipping: { waybill: "123456789", status: "rto", provider: "Bluedart" }
  }
)
```

---

## Payments API

### 1. Fetch Payment

**Method:** `Razorpay::Payment.fetch(payment_id)`

**Example:**

```ruby
payment = Razorpay::Payment.fetch("pay_NmkEFmMw3u25h4")
```

### 2. Capture Payment

**Method:** `Razorpay::Payment.capture(payment_id, options)` OR `payment_instance.capture(options)`

**Example:**

```ruby
# Option 1: Class method
Razorpay::Payment.capture("pay_NmkEFmMw3u25h4", { amount: 864200, currency: "INR" })

# Option 2: Instance method
payment = Razorpay::Payment.fetch("pay_NmkEFmMw3u25h4")
payment.capture(amount: 864200, currency: "INR")
```

### 3. Create Transfer for Payment (Route)

**Method:** `Razorpay::Payment.fetch(payment_id).transfer(options)`

**Example:**

```ruby
payment = Razorpay::Payment.fetch("pay_NmkEFmMw3u25h4")
payment.transfer(
  transfers: [{
    account: "acc_linkedAccount123",
    amount: 50_000,
    currency: "INR",
    on_hold: 1
  }]
)
```

### 4. Fetch Transfers for a Payment

**Method:** `Razorpay::Payment.fetch(payment_id).fetch_transfer`

**Example:**

```ruby
transfers = Razorpay::Payment.fetch("pay_NmkEFmMw3u25h4").fetch_transfer
```

### 5. Refund Payment

**Method:** `Razorpay::Payment.fetch(payment_id).refund(options)`

**Example:**

```ruby
payment = Razorpay::Payment.fetch("pay_NmkEFmMw3u25h4")
payment.refund(amount: 50_000)
```

### 6. List Refunds for a Payment

**Method:** `Razorpay::Payment.fetch(payment_id).refunds`

**Example:**

```ruby
refunds = Razorpay::Payment.fetch("pay_NmkEFmMw3u25h4").refunds
```

### 7. Fetch Specific Refund (for a Payment)

**Method:** `Razorpay::Payment.fetch(payment_id).fetch_refund(refund_id)`

**Example:**

```ruby
refund = Razorpay::Payment.fetch("pay_NmkEFmMw3u25h4").fetch_refund("rfnd_abc123")
```

---

## Transfers API

### 1. Create Direct Transfer

**Method:** `Razorpay::Transfer.create(options)`

**Example:**

```ruby
Razorpay::Transfer.create(
  account: "acc_linkedAccount123",
  amount: 10_000,
  currency: "INR"
)
```

### 2. Fetch Transfer

**Method:** `Razorpay::Transfer.fetch(transfer_id)`

**Example:**

```ruby
transfer = Razorpay::Transfer.fetch("trf_abc123")
```

### 3. Edit Transfer

**Method:** `transfer.edit(options)` (instance method)

**Example:**

```ruby
transfer = Razorpay::Transfer.fetch("trf_abc123")
transfer.edit(on_hold: 1)
```

### 4. Reverse Transfer

**Method:** `transfer.reverse(options)` (instance method)

**Example:**

```ruby
transfer = Razorpay::Transfer.fetch("trf_abc123")
transfer.reverse(amount: 5_000)
```

### 5. List Transfers

**Method:** `Razorpay::Transfer.all(options = {})`

**Example:**

```ruby
transfers = Razorpay::Transfer.all(recipient_settlement_id: "setl_XXXXXXXXXXXX")
```

### 6. List Reversals

**Method:** `Razorpay::Transfer.reversals(transfer_id)`

**Example:**

```ruby
reversals = Razorpay::Transfer.reversals("trf_XXXXXXXXXXXXXX")
```

### 7. Fetch Settlement Details (Linked Account expand)

**Method:** `Razorpay::Transfer.fetch_settlements`

**Example:**

```ruby
settlement_transfers = Razorpay::Transfer.fetch_settlements
```

---

## Refunds API

### 1. Fetch Refund

**Method:** `Razorpay::Refund.fetch(refund_id)`

**Example:**

```ruby
refund = Razorpay::Refund.fetch("rfnd_abc123")
```

### 2. List Refunds

**Method:** `Razorpay::Refund.all(options = {})`

**Example:**

```ruby
refunds = Razorpay::Refund.all(count: 10)
```

### 3. Create Refund (Standalone)

**Method:** `Razorpay::Refund.create(options)`

**Example:**

```ruby
Razorpay::Refund.create(payment_id: "pay_NmkEFmMw3u25h4", amount: 2000)
```

### 4. Edit Refund

**Method:** `Razorpay::Refund.fetch(refund_id).edit(options = {})`

**Example:**

```ruby
Razorpay::Refund.fetch("rfnd_XXXXXXXXXXXXXX").edit(
  notes: { notes_key_1: "Note update" }
)
```

---

## Disputes API

**Method source:** `lib/razorpay/dispute.rb`

### 1. Fetch Dispute

**Method:** `Razorpay::Dispute.fetch(dispute_id)`

**Example:**

```ruby
dispute = Razorpay::Dispute.fetch("disp_XXXXXXXXXXXXX")
```

### 2. List Disputes

**Method:** `Razorpay::Dispute.all(options = {})`

**Example:**

```ruby
disputes = Razorpay::Dispute.all(count: 10)
```

### 3. Accept Dispute

**Method:** `Razorpay::Dispute.accept(dispute_id, options = {})`

**Example:**

```ruby
dispute = Razorpay::Dispute.accept("disp_XXXXXXXXXXXXX", {})
```

### 4. Contest Dispute

**Method:** `Razorpay::Dispute.contest(dispute_id, options)`

**Example:**

```ruby
payload = {
  billing_proof: ["doc_EFtmUsbwpXwBG9", "doc_EFtmUsbwpXwBG8"],
  action: "submit"
}
dispute = Razorpay::Dispute.contest("disp_XXXXXXXXXXXXX", payload)
```

---

## Merchant Settlements API

**Method source:** `lib/razorpay/settlement.rb`

### 1. Fetch Settlement

**Method:** `Razorpay::Settlement.fetch(settlement_id)`

**Example:**

```ruby
settlement = Razorpay::Settlement.fetch("setl_DGlQ1Rj8os78Ec")
```

### 2. List Settlements

**Method:** `Razorpay::Settlement.all(options = {})`

**Example:**

```ruby
settlements = Razorpay::Settlement.all(count: 10)
```

### 3. Fetch Reconciliation Report (Combined)

**Method:** `Razorpay::Settlement.reports(options = {})`

**Example:**

```ruby
report = Razorpay::Settlement.reports(year: 2022, month: 12)
```

**Endpoint:** `GET /v1/settlements/recon/combined`

### 4. Create On-demand Settlement

**Method:** `Razorpay::Settlement.create(options = {})`

**Note:** If `options` is a `Hash` and includes `:settle_full_balance`, the gem converts the boolean to `1`/`0`.

```ruby
ondemand = Razorpay::Settlement.create(
  amount: 1221,
  settle_full_balance: false,
  description: "Urgent Payout"
)
```

### 5. Fetch All On-demand Settlements

**Method:** `Razorpay::Settlement.fetch_all_ondemand_settlement(options = {})`

**Example:**

```ruby
ondemand_list = Razorpay::Settlement.fetch_all_ondemand_settlement(count: 10)
```

### 6. Fetch On-demand Settlement by ID

**Method:** `Razorpay::Settlement.fetch_ondemand_settlement_by_id(id, options = {})`

**Example:**

```ruby
ondemand = Razorpay::Settlement.fetch_ondemand_settlement_by_id(
  "setl_DGlQ1Rj8os78Ec",
  { "expand[]" => "ondemand_payouts" }
)
```

---

## Linked Accounts API (Razorpay Route)

### 1. Create Linked Account

**Method:** `Razorpay::Account.create(options)`

**Example:**

```ruby
Razorpay::Account.create(
  email: "vendor@example.com",
  phone: "9876543210",
  legal_business_name: "Vendor Corp",
  business_type: "partnership",
  profile: {
    category: "healthcare",
    addresses: {
      registered: { city: "Mumbai", state: "Maharashtra", country: "IN" }
    }
  }
)
```

### 2. Fetch Linked Account

**Method:** `Razorpay::Account.fetch(account_id)`

**Example:**

```ruby
account = Razorpay::Account.fetch("acc_linkedAccount123")
```

### 3. Update Linked Account

**Method:** `Razorpay::Account.edit(account_id, options)`

**Example:**

```ruby
Razorpay::Account.edit("acc_linkedAccount123", email: "new@example.com")
```

### 4. Delete Linked Account

**Method:** `Razorpay::Account.delete(account_id)`

**Example:**

```ruby
Razorpay::Account.delete("acc_linkedAccount123")
```

### 5. Upload Account Documents (KYC)

**Method:** `Razorpay::Account.upload_account_doc(account_id, options)`

**Example:**

```ruby
# Requires valid file object and type params per API
Razorpay::Account.upload_account_doc("acc_id", { ... })
```

### 6. Fetch Account Documents

**Method:** `Razorpay::Account.fetch_account_doc(account_id)`

**Example:**

```ruby
docs = Razorpay::Account.fetch_account_doc("acc_linkedAccount123")
```

---

## Stakeholders API

**Validated source:** `lib/razorpay/stakeholder.rb`  
**API version used by gem:** `v2`

### 1. Create Stakeholder

**Method:** `Razorpay::Stakeholder.create(account_id, options)`

```ruby
stakeholder = Razorpay::Stakeholder.create(
  "acc_linkedAccount123",
  { name: "Director Name", email: "director@example.com" }
)
```

### 2. Fetch Stakeholder

**Method:** `Razorpay::Stakeholder.fetch(account_id, stakeholder_id)`

```ruby
stakeholder = Razorpay::Stakeholder.fetch(
  "acc_linkedAccount123",
  "sth_stakeholder123"
)
```

### 3. List Stakeholders

**Method:** `Razorpay::Stakeholder.all(account_id)`

```ruby
stakeholders = Razorpay::Stakeholder.all("acc_linkedAccount123")
```

> Note: Unlike other `all` methods, this method only accepts `account_id` and does not support additional query parameters. It always calls `GET /v2/accounts/{account_id}/stakeholders` with an empty query object.

### 4. Edit Stakeholder

**Method:** `Razorpay::Stakeholder.edit(account_id, stakeholder_id, options = {})`

```ruby
stakeholder = Razorpay::Stakeholder.edit(
  "acc_linkedAccount123",
  "sth_stakeholder123",
  { email: "newemail@example.com" }
)
```

### 5. Upload Stakeholder Documents

**Method:** `Razorpay::Stakeholder.upload_stakeholder_doc(account_id, stakeholder_id, options)`

```ruby
Razorpay::Stakeholder.upload_stakeholder_doc(
  "acc_linkedAccount123",
  "sth_stakeholder123",
  {
    file: File.new("/path/to/document.jpeg"),
    document_type: "aadhar_front"
  }
)
```

### 6. Fetch Stakeholder Documents

**Method:** `Razorpay::Stakeholder.fetch_stakeholder_doc(account_id, stakeholder_id)`

```ruby
docs = Razorpay::Stakeholder.fetch_stakeholder_doc(
  "acc_linkedAccount123",
  "sth_stakeholder123"
)
```

---

## Product Configuration API

### 1. Request Configuration

**Method:** `Razorpay::Product.request_product_configuration(account_id, options)`

**Example:**

```ruby
Razorpay::Product.request_product_configuration(
  "acc_linkedAccount123",
  { product_name: "payment_gateway", tnc_accepted: true }
)
```

### 2. Fetch Configuration

**Method:** `Razorpay::Product.fetch(account_id, product_id)`

**Example:**

```ruby
Razorpay::Product.fetch("acc_linkedAccount123", "acc_prd_product123")
```

### 3. Fetch T&C

**Method:** `Razorpay::Product.fetch_tnc(product_name)`

**Example:**

```ruby
Razorpay::Product.fetch_tnc("payment_gateway")
```

### 4. Edit Product Configuration

**Method:** `Razorpay::Product.edit(account_id, product_id, options = {})`

**Example:**

> Note: This method accepts options as a JSON string (use `.to_json`) when passing complex nested data via PATCH requests.

```ruby
config = Razorpay::Product.edit(
  "acc_linkedAccount123",
  "acc_prd_product123",
  {
    settlements: {
      account_number: "1234567890",
      ifsc_code: "HDFC0001234",
      beneficiary_name: "Health Labs Pvt Ltd"
    }
  }.to_json
)
```

---

## Documents API

**Validated source:** `lib/razorpay/document.rb`

### 1. Create Document

**Method:** `Razorpay::Document.create(options)`

```ruby
doc = Razorpay::Document.create(
  file: File.new("/path/to/sample_uploaded.jpeg"),
  purpose: "dispute_evidence"
)
```

### 2. Fetch Document

**Method:** `Razorpay::Document.fetch(document_id)`

```ruby
doc = Razorpay::Document.fetch("doc_NiyXWXXXXXXXXX")
```

---

## Webhook & Verification Utilities

### 1. Verify Webhook Signature

**Method:** `Razorpay::Utility.verify_webhook_signature(body, signature, secret)`

**Behavior:** Returns `true` on success, raises `SecurityError` on failure.

**Important:** Unlike other Razorpay SDKs, the Ruby gem raises a standard `SecurityError` for signature verification failures, not a custom Razorpay exception.

```ruby
begin
  Razorpay::Utility.verify_webhook_signature(payload_body, signature_header, secret)
rescue SecurityError => e
  # Handle invalid signature specifically
  Rails.logger.error "Signature verification failed: #{e.message}"
rescue Razorpay::Error => e
  # Handle other Razorpay errors
end
```

### 2. Verify Payment Signature

**Method:** `Razorpay::Utility.verify_payment_signature(attributes)`

**Behavior:** Returns `true` on success, raises `SecurityError` on failure.

```ruby
begin
  Razorpay::Utility.verify_payment_signature(
    razorpay_order_id: "order_123",
    razorpay_payment_id: "pay_123",
    razorpay_signature: "sig_123"
  )
rescue SecurityError => e
  Rails.logger.error "Payment signature verification failed: #{e.message}"
end
```

### 3. Verify Payment Link Signature (also present in gem)

**Method:** `Razorpay::Utility.verify_payment_link_signature(attributes)`

```ruby
Razorpay::Utility.verify_payment_link_signature(
  razorpay_payment_link_id: "plink_123",
  razorpay_payment_link_reference_id: "ref_123",
  razorpay_payment_link_status: "paid",
  razorpay_signature: "sig_123"
)
```

---

## Error Handling

The gem maps API errors to specific Ruby exceptions.

**Note:** Signature verification failures raise standard `SecurityError` (not a Razorpay-specific exception).

### Exception Classes (present in gem requires)

- `Razorpay::BadRequestError`
- `Razorpay::GatewayError`
- `Razorpay::ServerError`
- `Razorpay::Error` (base/catch-all)

---

## Appendix: Additional Verified Methods Present in the Gem

All methods below have been verified against source code in `lib/razorpay/*.rb`.

---

### Addon API (`lib/razorpay/addon.rb`)

- `Razorpay::Addon.fetch(id)` — Fetch an addon by ID
- `Razorpay::Addon.all(options = {})` — List all addons
- `Razorpay::Addon.create(subscription_id, options)` — Create addon for a subscription
- `Razorpay::Addon.delete(id)` — Delete an addon

---

### Card API (`lib/razorpay/card.rb`)

- `Razorpay::Card.fetch(id)` — Fetch card details by ID
- `Razorpay::Card.request_card_reference(options)` — Request card fingerprint/reference

---

### Customer API (`lib/razorpay/customer.rb`)

- `Razorpay::Customer.create(options)` — Create a customer
- `Razorpay::Customer.fetch(id)` — Fetch customer by ID
- `Razorpay::Customer.edit(id, options = {})` — Update customer details
- `Razorpay::Customer.all(options = {})` — List all customers
- `customer.fetchTokens` — Fetch all tokens for a customer (instance method)
- `customer.fetchToken(tokenId)` — Fetch specific token for customer (instance method)
- `customer.deleteToken(tokenId)` — Delete a token (instance method)
- `Razorpay::Customer.add_bank_account(id, options = {})` — Add bank account to customer
- `Razorpay::Customer.delete_bank_account(id, bankAccountId)` — Delete bank account
- `Razorpay::Customer.request_eligibility_check(options = {})` — Check customer eligibility
- `Razorpay::Customer.fetch_eligibility(eligibilityId)` — Fetch eligibility details

---

### Fund Account API (`lib/razorpay/fund_account.rb`)

- `Razorpay::FundAccount.create(options)` — Create a fund account
- `Razorpay::FundAccount.all(data = {})` — List all fund accounts

---

### IIN API (`lib/razorpay/iin.rb`)

- `Razorpay::Iin.fetch(id)` — Fetch card properties using token IIN

---

### Invoice API (`lib/razorpay/invoice.rb`)

- `Razorpay::Invoice.create(options)` — Create an invoice
- `Razorpay::Invoice.fetch(id)` — Fetch invoice by ID
- `Razorpay::Invoice.all(options = {})` — List all invoices
- `Razorpay::Invoice.edit(id, options = {})` — Edit an invoice
- `Razorpay::Invoice.issue(id)` — Issue an invoice
- `Razorpay::Invoice.cancel(id)` — Cancel an invoice
- `Razorpay::Invoice.notify_by(id, medium)` — Send invoice notification (email/sms)
- `Razorpay::Invoice.delete(id)` — Delete an invoice
- `invoice.edit(options = {})` — Instance method to edit
- `invoice.issue` — Instance method to issue
- `invoice.cancel` — Instance method to cancel

---

### Item API (`lib/razorpay/item.rb`)

- `Razorpay::Item.create(options)` — Create an item
- `Razorpay::Item.fetch(id)` — Fetch item by ID
- `Razorpay::Item.edit(id, options = {})` — Edit an item (converts `:active` boolean to 1/0)
- `Razorpay::Item.all(options = {})` — List all items
- `Razorpay::Item.delete(id)` — Delete an item

---

### OAuth Token API (`lib/razorpay/oauth_token.rb`)

- `Razorpay::OAuthToken.get_auth_url(options)` — Generate OAuth authorization URL
- `Razorpay::OAuthToken.get_access_token(options)` — Get access token
- `Razorpay::OAuthToken.refresh_token(options)` — Refresh an access token
- `Razorpay::OAuthToken.revoke_token(options)` — Revoke a token

---

### Payment Link API (`lib/razorpay/payment_link.rb`)

- `Razorpay::PaymentLink.create(options)` — Create a payment link
- `Razorpay::PaymentLink.fetch(id)` — Fetch payment link by ID
- `Razorpay::PaymentLink.edit(id, options = {})` — Edit a payment link
- `Razorpay::PaymentLink.all(options = {})` — List all payment links
- `Razorpay::PaymentLink.cancel(id)` — Cancel a payment link
- `Razorpay::PaymentLink.notify_by(id, medium)` — Send payment link notification

---

### Payment Methods API (`lib/razorpay/payment_method.rb`)

- `Razorpay::PaymentMethods.all(options = {})` — Fetch all available payment methods

---

### Plan API (`lib/razorpay/plan.rb`)

- `Razorpay::Plan.create(options)` — Create a subscription plan
- `Razorpay::Plan.fetch(id)` — Fetch plan by ID
- `Razorpay::Plan.all(options = {})` — List all plans

---

### QR Code API (`lib/razorpay/qr_code.rb`)

- `Razorpay::QrCode.create(options)` — Create a QR code (converts `:fixed_amount` boolean to 1/0)
- `Razorpay::QrCode.fetch(id)` — Fetch QR code by ID
- `Razorpay::QrCode.all(options = {})` — List all QR codes
- `qrcode.fetch_payments(options = {})` — Fetch payments for a QR code (instance method)
- `qrcode.close` — Close a QR code (instance method)

---

### Subscription API (`lib/razorpay/subscription.rb`)

- `Razorpay::Subscription.create(options)` — Create a subscription
- `Razorpay::Subscription.fetch(id)` — Fetch subscription by ID
- `Razorpay::Subscription.all(options = {})` — List all subscriptions
- `Razorpay::Subscription.cancel(id, options = {})` — Cancel a subscription
- `subscription.cancel(options = {})` — Instance method to cancel
- `subscription.edit(options = {})` — Edit subscription (instance method)
- `subscription.pending_update` — Retrieve scheduled changes (instance method)
- `Razorpay::Subscription.cancel_scheduled_changes(id)` — Cancel scheduled changes
- `Razorpay::Subscription.pause(id, options = {})` — Pause a subscription
- `Razorpay::Subscription.resume(id, options = {})` — Resume a subscription
- `Razorpay::Subscription.delete_offer(id, offerId)` — Delete an offer from subscription

---

### Subscription Registration API (`lib/razorpay/subscription_registration.rb`)

- `Razorpay::SubscriptionRegistration.create(options)` — Create authorization link for recurring payments

---

### Token API (`lib/razorpay/token.rb`)

- `Razorpay::Token.create(options)` — Create a token
- `Razorpay::Token.fetch(options)` — Fetch token details (POST-based)
- `Razorpay::Token.delete(options)` — Delete a token (POST-based)
- `Razorpay::Token.process_payment_on_alternate_pa_or_pg(options)` — Process payment on alternate PA/PG

---

### Virtual Account API (`lib/razorpay/virtual_account.rb`)

- `Razorpay::VirtualAccount.create(options)` — Create a virtual account
- `Razorpay::VirtualAccount.fetch(id)` — Fetch virtual account by ID
- `Razorpay::VirtualAccount.all(options = {})` — List all virtual accounts
- `Razorpay::VirtualAccount.close(id)` — Close a virtual account
- `virtual_account.payments(options = {})` — Fetch payments for VA (instance method)
- `Razorpay::VirtualAccount.add_receiver(id, options = {})` — Add receiver to VA
- `Razorpay::VirtualAccount.allowed_payer(id, options = {})` — Add allowed payer
- `Razorpay::VirtualAccount.delete_allowed_payer(id, payer_id)` — Delete allowed payer

---

### Webhook API (`lib/razorpay/webhook.rb`)

- `Razorpay::Webhook.create(options, account_id = nil)` — Create a webhook
- `Razorpay::Webhook.all(options = {}, account_id = nil)` — List all webhooks
- `Razorpay::Webhook.fetch(id, account_id)` — Fetch webhook by ID
- `Razorpay::Webhook.edit(options, id, account_id = nil)` — Edit a webhook
- `Razorpay::Webhook.delete(id, account_id)` — Delete a webhook

---

### Payment API Extras (`lib/razorpay/payment.rb`)

- `Razorpay::Payment.all(options = {})` — List all payments
- `Razorpay::Payment.create_recurring_payment(data = {})` — Create recurring payment
- `Razorpay::Payment.create_json_payment(data = {})` — Create payment via JSON
- `Razorpay::Payment.fetch_payment_downtime` — Fetch payment downtime details
- `Razorpay::Payment.fetch_payment_downtime_by_id(id)` — Fetch downtime by ID
- `Razorpay::Payment.fetch_card_details(id)` — Fetch card details for payment
- `Razorpay::Payment.fetch_multiple_refund(id, options = {})` — Fetch multiple refunds for payment
- `Razorpay::Payment.otp_generate(id)` — Generate OTP for payment
- `payment.otp_submit(options)` — Submit OTP (instance method)
- `payment.otp_resend` — Resend OTP (instance method)
- `Razorpay::Payment.create_upi(data = {})` — Create UPI payment
- `Razorpay::Payment.validate_vpa(data = {})` — Validate VPA
- `Razorpay::Payment.expand_details(id, options = {})` — Fetch payment with expanded details
- `payment.edit(options = {})` — Edit payment (instance method)
- `payment.bank_transfer` — Fetch bank transfer details (instance method)
- `payment.refund!(options = {})` — Refund and update payment (instance method)
- `payment.capture!(options)` — Capture and update payment (instance method)

---

### Utility Extras (`lib/razorpay/utility.rb`)

- `Razorpay::Utility.generate_onboarding_signature(body, secret)` — Generate signature for onboarding
