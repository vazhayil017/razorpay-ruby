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

Addons allow you to charge one-time fees to a subscription. Use for setup fees, extra charges, or any one-time additions.

| Method | Description |
|--------|-------------|
| `Razorpay::Addon.fetch(id)` | Retrieves details of a specific addon by its unique identifier. Returns addon metadata including amount, quantity, and associated subscription. |
| `Razorpay::Addon.all(options = {})` | Lists all addons created in your account. Supports pagination via `count` and `skip` options. |
| `Razorpay::Addon.create(subscription_id, options)` | Creates a new addon for an existing subscription. Pass the subscription ID and addon details (item_id, quantity). The addon amount is charged on the next billing cycle. |
| `Razorpay::Addon.delete(id)` | Permanently deletes an addon. Once deleted, the addon cannot be recovered and will not be charged. |

---

### Card API (`lib/razorpay/card.rb`)

Card API allows you to retrieve saved card information and generate card fingerprints for tokenization.

| Method | Description |
|--------|-------------|
| `Razorpay::Card.fetch(id)` | Fetches details of a saved card by card ID. Returns card network, type (credit/debit), last 4 digits, and issuer information. |
| `Razorpay::Card.request_card_reference(options)` | Generates a card fingerprint/reference for network tokenization. Used to create a unique reference for a card across merchants. |

---

### Customer API (`lib/razorpay/customer.rb`)

Customer API allows you to create customer profiles to save payment methods and enable recurring payments.

| Method | Description |
|--------|-------------|
| `Razorpay::Customer.create(options)` | Creates a new customer with contact details (name, email, phone). Required for saving cards and enabling subscriptions. |
| `Razorpay::Customer.fetch(id)` | Retrieves customer details by customer ID. Returns saved contact information and GST details if present. |
| `Razorpay::Customer.edit(id, options = {})` | Updates existing customer information. Use to modify contact details or add GST information. |
| `Razorpay::Customer.all(options = {})` | Lists all customers. Supports pagination and filtering by email/phone. |
| `customer.fetchTokens` | (Instance) Retrieves all saved payment tokens (cards, UPI VPAs) for the customer. Essential for recurring payments. |
| `customer.fetchToken(tokenId)` | (Instance) Fetches details of a specific saved token by its ID. Returns token metadata and card/VPA details. |
| `customer.deleteToken(tokenId)` | (Instance) Removes a saved payment token. The customer will need to re-authenticate to save the card again. |
| `Razorpay::Customer.add_bank_account(id, options = {})` | Links a bank account to a customer for direct debit payments (NACH/eMandate). |
| `Razorpay::Customer.delete_bank_account(id, bankAccountId)` | Removes a linked bank account from the customer profile. |
| `Razorpay::Customer.request_eligibility_check(options = {})` | Checks customer eligibility for specific payment methods like EMI or Pay Later options. |
| `Razorpay::Customer.fetch_eligibility(eligibilityId)` | Retrieves the result of a previously initiated eligibility check. |

---

### Fund Account API (`lib/razorpay/fund_account.rb`)

Fund Account API manages beneficiary accounts for payouts. Link bank accounts or VPAs to receive money.

| Method | Description |
|--------|-------------|
| `Razorpay::FundAccount.create(options)` | Creates a fund account by linking a bank account or VPA to a contact. Required before initiating payouts. |
| `Razorpay::FundAccount.all(data = {})` | Lists all fund accounts. Filter by contact_id to get accounts for a specific beneficiary. |

---

### IIN API (`lib/razorpay/iin.rb`)

IIN (Issuer Identification Number) API provides card BIN information for pre-validation and routing.

| Method | Description |
|--------|-------------|
| `Razorpay::Iin.fetch(id)` | Fetches card properties (network, type, issuer, EMI availability) using the first 6-8 digits (IIN/BIN) of a card. Useful for showing bank logos and EMI options before payment. |

---

### Invoice API (`lib/razorpay/invoice.rb`)

Invoice API allows you to create, manage, and send professional invoices with automatic payment link generation.

| Method | Description |
|--------|-------------|
| `Razorpay::Invoice.create(options)` | Creates a draft or issued invoice with line items, taxes, and customer details. Automatically generates a payment link. |
| `Razorpay::Invoice.fetch(id)` | Retrieves complete invoice details including status, payment link, and line items. |
| `Razorpay::Invoice.all(options = {})` | Lists all invoices. Filter by status (draft, issued, paid, cancelled) or date range. |
| `Razorpay::Invoice.edit(id, options = {})` | Modifies a draft invoice. Cannot edit issued invoices—cancel and recreate instead. |
| `Razorpay::Invoice.issue(id)` | Issues a draft invoice, making it payable. Generates and activates the payment link. |
| `Razorpay::Invoice.cancel(id)` | Cancels an invoice. Use for voiding unpaid invoices. Paid invoices cannot be cancelled. |
| `Razorpay::Invoice.notify_by(id, medium)` | Sends invoice notification via 'email' or 'sms' to the customer. Includes payment link. |
| `Razorpay::Invoice.delete(id)` | Permanently deletes a draft invoice. Issued invoices must be cancelled, not deleted. |
| `invoice.edit(options = {})` | (Instance) Convenience method to edit the current invoice. |
| `invoice.issue` | (Instance) Issues the current draft invoice. |
| `invoice.cancel` | (Instance) Cancels the current invoice. |

---

### Item API (`lib/razorpay/item.rb`)

Item API manages reusable product/service items for invoices and subscriptions.

| Method | Description |
|--------|-------------|
| `Razorpay::Item.create(options)` | Creates a reusable item with name, amount, currency, and description. Use for consistent pricing across invoices. |
| `Razorpay::Item.fetch(id)` | Retrieves item details by ID. Returns pricing and metadata. |
| `Razorpay::Item.edit(id, options = {})` | Updates item properties. Set `active: false` to soft-delete. The gem auto-converts boolean to 1/0. |
| `Razorpay::Item.all(options = {})` | Lists all items. Filter by `active` status to exclude deleted items. |
| `Razorpay::Item.delete(id)` | Permanently deletes an item. Items linked to active subscriptions cannot be deleted. |

---

### OAuth Token API (`lib/razorpay/oauth_token.rb`)

OAuth API enables platform/marketplace authentication for accessing connected accounts.

| Method | Description |
|--------|-------------|
| `Razorpay::OAuthToken.get_auth_url(options)` | Generates the authorization URL for OAuth flow. Redirect merchants here to initiate account linking. Requires client_id, redirect_uri, scopes, and state. |
| `Razorpay::OAuthToken.get_access_token(options)` | Exchanges authorization code for access/refresh tokens. Call after merchant authorizes your app. |
| `Razorpay::OAuthToken.refresh_token(options)` | Gets a new access token using a refresh token. Use when access token expires (tokens valid ~30 days). |
| `Razorpay::OAuthToken.revoke_token(options)` | Invalidates an access or refresh token. Use when merchant disconnects or for security. |

---

### Payment Link API (`lib/razorpay/payment_link.rb`)

Payment Links are shareable URLs for collecting payments via SMS, email, or messaging apps.

| Method | Description |
|--------|-------------|
| `Razorpay::PaymentLink.create(options)` | Creates a payment link with amount, description, and optional customer details. Returns a shareable URL. Supports partial payments and expiry. |
| `Razorpay::PaymentLink.fetch(id)` | Retrieves payment link details including status (created, paid, expired, cancelled) and payments received. |
| `Razorpay::PaymentLink.edit(id, options = {})` | Updates payment link properties like expiry time, notes, or reminder settings. Amount cannot be changed. |
| `Razorpay::PaymentLink.all(options = {})` | Lists all payment links. Filter by status, reference_id, or date range. |
| `Razorpay::PaymentLink.cancel(id)` | Cancels an active payment link. Payments can no longer be made on this link. |
| `Razorpay::PaymentLink.notify_by(id, medium)` | Sends/resends payment link via 'email' or 'sms'. Useful for payment reminders. |

---

### Payment Methods API (`lib/razorpay/payment_method.rb`)

Retrieve available payment methods configured for your account.

| Method | Description |
|--------|-------------|
| `Razorpay::PaymentMethods.all(options = {})` | Returns all enabled payment methods (cards, UPI, netbanking, wallets, EMI) for your merchant account. Useful for building custom checkout UIs. |

---

### Plan API (`lib/razorpay/plan.rb`)

Plan API manages subscription billing plans with recurring pricing.

| Method | Description |
|--------|-------------|
| `Razorpay::Plan.create(options)` | Creates a subscription plan with billing period (daily/weekly/monthly/yearly), interval, and amount. Plans are templates for subscriptions. |
| `Razorpay::Plan.fetch(id)` | Retrieves plan details including pricing, period, and usage count. |
| `Razorpay::Plan.all(options = {})` | Lists all plans. Plans cannot be deleted—create new ones as needed. |

---

### QR Code API (`lib/razorpay/qr_code.rb`)

QR Code API creates UPI QR codes for in-store or digital payments.

| Method | Description |
|--------|-------------|
| `Razorpay::QrCode.create(options)` | Generates a QR code for UPI payments. Can be fixed amount (one-time) or dynamic (reusable). Set `fixed_amount: true` for single-use. |
| `Razorpay::QrCode.fetch(id)` | Retrieves QR code details including image URL, status (active/closed), and payments received. |
| `Razorpay::QrCode.all(options = {})` | Lists all QR codes. Filter by status or customer_id. |
| `qrcode.fetch_payments(options = {})` | (Instance) Lists all payments made on this QR code. Useful for reconciliation. |
| `qrcode.close` | (Instance) Closes the QR code. No further payments can be made. Use for expired offers or completed transactions. |

---

### Subscription API (`lib/razorpay/subscription.rb`)

Subscription API manages recurring billing with automatic charge collection.

| Method | Description |
|--------|-------------|
| `Razorpay::Subscription.create(options)` | Creates a subscription linking a customer to a plan. Handles authentication and automatic recurring charges. |
| `Razorpay::Subscription.fetch(id)` | Retrieves subscription details including current status, billing cycle, and pending amount. |
| `Razorpay::Subscription.all(options = {})` | Lists all subscriptions. Filter by plan_id, status, or customer. |
| `Razorpay::Subscription.cancel(id, options = {})` | Cancels a subscription. Set `cancel_at_cycle_end: true` to cancel at period end instead of immediately. |
| `subscription.cancel(options = {})` | (Instance) Cancels the current subscription. |
| `subscription.edit(options = {})` | (Instance) Modifies subscription—update quantity, plan changes (scheduled for next cycle). |
| `subscription.pending_update` | (Instance) Retrieves scheduled changes that will apply at next billing cycle. |
| `Razorpay::Subscription.cancel_scheduled_changes(id)` | Removes pending updates. Subscription continues with current settings. |
| `Razorpay::Subscription.pause(id, options = {})` | Temporarily pauses billing. Customer retains access based on your implementation. |
| `Razorpay::Subscription.resume(id, options = {})` | Resumes a paused subscription. Next charge date is recalculated. |
| `Razorpay::Subscription.delete_offer(id, offerId)` | Removes a linked offer/discount from the subscription. |

---

### Subscription Registration API (`lib/razorpay/subscription_registration.rb`)

Create authorization links for recurring payments setup (eMandate/UPI AutoPay).

| Method | Description |
|--------|-------------|
| `Razorpay::SubscriptionRegistration.create(options)` | Creates an authentication link for recurring payment authorization. Customer authorizes via this link; you receive a token for future charges. Used for eNACH, eMandate, and UPI AutoPay setup. |

---

### Token API (`lib/razorpay/token.rb`)

Token API manages saved payment instruments for recurring charges.

| Method | Description |
|--------|-------------|
| `Razorpay::Token.create(options)` | Creates a token by tokenizing card/bank details. Use for storing payment methods securely. |
| `Razorpay::Token.fetch(options)` | Retrieves token details. Unique: uses POST instead of GET—pass token ID in request body. |
| `Razorpay::Token.delete(options)` | Deletes a saved token. Future recurring charges will fail—customer must re-authorize. Uses POST method. |
| `Razorpay::Token.process_payment_on_alternate_pa_or_pg(options)` | Processes tokenized payment through an alternate PA/PG. For service provider token integrations. |

---

### Virtual Account API (`lib/razorpay/virtual_account.rb`)

Virtual Accounts enable B2B payments via bank transfers with automatic reconciliation.

| Method | Description |
|--------|-------------|
| `Razorpay::VirtualAccount.create(options)` | Creates a virtual account with a unique account number. Customers pay via NEFT/RTGS/IMPS to this account. |
| `Razorpay::VirtualAccount.fetch(id)` | Retrieves VA details including bank account number, IFSC, and payment status. |
| `Razorpay::VirtualAccount.all(options = {})` | Lists all virtual accounts. Filter by status (active/closed) or customer. |
| `Razorpay::VirtualAccount.close(id)` | Closes a virtual account. Use when invoice is paid or relationship ends. |
| `virtual_account.payments(options = {})` | (Instance) Lists all payments received on this VA. Essential for reconciliation. |
| `Razorpay::VirtualAccount.add_receiver(id, options = {})` | Adds additional receivers (bank accounts) to collect payments into the VA. |
| `Razorpay::VirtualAccount.allowed_payer(id, options = {})` | Restricts VA to accept payments only from specific bank accounts. For controlled B2B payments. |
| `Razorpay::VirtualAccount.delete_allowed_payer(id, payer_id)` | Removes a payer restriction, allowing payments from any account again. |

---

### Webhook API (`lib/razorpay/webhook.rb`)

Webhook API configures event notifications for real-time payment updates.

| Method | Description |
|--------|-------------|
| `Razorpay::Webhook.create(options, account_id = nil)` | Registers a webhook endpoint to receive event notifications. Pass `account_id` for linked account webhooks. Events include payment.captured, refund.created, etc. |
| `Razorpay::Webhook.all(options = {}, account_id = nil)` | Lists all configured webhooks. Use `account_id` to list webhooks for a connected account. |
| `Razorpay::Webhook.fetch(id, account_id)` | Retrieves webhook configuration including URL, events subscribed, and active status. |
| `Razorpay::Webhook.edit(options, id, account_id = nil)` | Updates webhook URL or subscribed events. Use to add/remove event types. |
| `Razorpay::Webhook.delete(id, account_id)` | Removes a webhook. You'll stop receiving notifications for configured events. |

---

### Payment API Extras (`lib/razorpay/payment.rb`)

Additional payment methods beyond basic capture/refund operations.

| Method | Description |
|--------|-------------|
| `Razorpay::Payment.all(options = {})` | Lists all payments. Filter by status, method, date range. Supports pagination via `count` and `skip`. |
| `Razorpay::Payment.create_recurring_payment(data = {})` | Creates a recurring payment using a saved token. Charges customer without interaction. |
| `Razorpay::Payment.create_json_payment(data = {})` | Creates a payment via JSON payload. Use for S2S (server-to-server) card payments. |
| `Razorpay::Payment.fetch_payment_downtime` | Lists current payment method downtimes. Check before retrying failed payments. |
| `Razorpay::Payment.fetch_payment_downtime_by_id(id)` | Gets details of a specific downtime event including affected methods and duration. |
| `Razorpay::Payment.fetch_card_details(id)` | Retrieves card details (network, type, issuer) for a payment made via card. |
| `Razorpay::Payment.fetch_multiple_refund(id, options = {})` | Lists all refunds for a specific payment. Use for partial refund tracking. |
| `Razorpay::Payment.otp_generate(id)` | Generates OTP for completing a pending card payment that requires authentication. |
| `payment.otp_submit(options)` | (Instance) Submits OTP to authenticate and complete the payment. |
| `payment.otp_resend` | (Instance) Requests a new OTP if the previous one expired or wasn't received. |
| `Razorpay::Payment.create_upi(data = {})` | Creates a UPI collect request or generates UPI intent link. |
| `Razorpay::Payment.validate_vpa(data = {})` | Validates a UPI VPA (e.g., name@upi) before initiating payment. Returns account holder name. |
| `Razorpay::Payment.expand_details(id, options = {})` | Fetches payment with expanded related entities (card, emi, offers). Pass `expand[]=card` etc. |
| `payment.edit(options = {})` | (Instance) Updates payment notes. Only notes can be modified post-creation. |
| `payment.bank_transfer` | (Instance) Retrieves bank transfer details for payments received via NEFT/RTGS/IMPS. |
| `payment.refund!(options = {})` | (Instance) Initiates refund and updates the payment object in place with new status. |
| `payment.capture!(options)` | (Instance) Captures authorized payment and updates the object with captured status. |

---

### Utility Extras (`lib/razorpay/utility.rb`)

Helper methods for signature generation and verification.

| Method | Description |
|--------|-------------|
| `Razorpay::Utility.generate_onboarding_signature(body, secret)` | Generates an encrypted signature for Route onboarding. Used when creating linked accounts programmatically via OAuth. Returns hex-encoded encrypted payload. |
