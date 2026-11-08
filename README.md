# No Border CMC — Payment Website

## Stripe setup

### 1. Create a Stripe account

Go to [https://stripe.com](https://stripe.com) and click **Start now**.
Enter your email address and create a password.

### 2. Select the appropriate entity type

Stripe will ask whether you are registering as an **individual** or a **company / association**.

- If No Border CMC is a registered Swiss association (*association loi 1901* or *Verein*), select **Company** and enter the association details.
- If you are collecting payments personally, select **Individual**.

> **Important:** Stripe requires a legal entity to activate payouts. If No Border CMC is not yet formally registered, you can still test payments, but you will not be able to receive payouts until verification is complete.

### 3. Complete identity and business verification

Stripe will ask for:

- Full legal name of the responsible person
- Date of birth
- Home address
- A copy of a government-issued ID (passport or national ID card)
- For associations: the association's registered name, address, and registration number if applicable

This is a legal requirement (KYC — Know Your Customer). Stripe uses it to comply with Swiss financial regulations.

### 4. Connect a Swiss bank account for payouts

In the Stripe Dashboard, go to **Settings → Bank accounts and scheduling**.

Add your Swiss IBAN. Stripe will make a small verification deposit (or ask you to confirm the account details).

Payouts are transferred automatically on a rolling schedule (typically 7 days after each payment for new accounts, then faster once your account is established).

### 5. Activate TWINT

TWINT is available through Stripe for Swiss businesses.

To activate it:

1. In the Stripe Dashboard, go to **Settings → Payment methods**.
2. Find **TWINT** in the list.
3. Click **Activate** and follow the instructions.

**What Stripe requires for TWINT activation:**

- Your Stripe account must be registered in Switzerland (Swiss address and Swiss bank account).
- Your business must be verified (step 3 above must be complete).
- TWINT is only available in CHF.

Official documentation: [https://stripe.com/docs/payments/twint](https://stripe.com/docs/payments/twint)

### 6. Configure customer email receipts

In the Stripe Dashboard, go to **Settings → Emails**.

Enable **Successful payments** under *Customer emails*. Stripe will automatically send a receipt to the customer's email address after each successful payment.

### 7. Create a CHF Payment Link with a customer-defined amount

> **Read Section C before doing this.** There is an important limitation regarding TWINT and custom amounts.

1. In the Stripe Dashboard, go to **Payment Links** → **Create payment link**.
2. Click **Add a product**.
3. Click **Create a new product**.
4. Give it a name (e.g. *No Border CMC — Contribution*).
5. Under **Price**, select **Customer chooses price** (also called *Pay what you want*).
6. Set the currency to **CHF**.
7. Set a minimum amount if desired (e.g. CHF 5).
8. Click **Save product**, then **Create link**.
9. Copy the link. It looks like: `https://buy.stripe.com/xxxxxxxxxxxxxxxx`

### 8. Enable available payment methods on the Payment Link

When creating or editing the Payment Link, click **Payment methods**.

Enable:
- **Card** (Visa, Mastercard, etc.) — always available
- **TWINT** — available if activated (see Section C for the limitation)

### 9. Test the payment flow

Before going live, test the entire flow using Stripe's test mode.

In test mode, use these card numbers (no real money is charged):

| Card number | Result |
|---|---|
| `4242 4242 4242 4242` | Successful payment |
| `4000 0000 0000 9995` | Declined payment |

Use any future expiry date, any 3-digit CVC, and any postal code.

TWINT cannot be tested with a real phone in test mode. Stripe provides a simulated TWINT button in the test checkout.

### 10. Switch to live mode

When you are ready to accept real payments:

1. In the Stripe Dashboard, click the toggle at the top left that says **Test mode** — switch it to **Live mode**.
2. Create a new Payment Link in live mode (Payment Links created in test mode do not work in live mode).
3. Copy the new live Payment Link URL.
4. Update `index.html` with the live URL (see Section E).

---

## C. Important Stripe limitations

### Customer-defined amounts and TWINT

**This is the key limitation you must understand before configuring Stripe.**

As of 2025, Stripe Payment Links with **Customer chooses price** (custom amount) **do not support TWINT**.

TWINT requires a fixed amount to be set before the payment is initiated. This is a technical requirement of the TWINT protocol, not a Stripe policy choice.

**Your options:**

#### Option 1 — Two separate Payment Links (recommended)

Create two Payment Links:

| Link | Amount | Payment methods |
|---|---|---|
| Link A | Customer chooses | Card only |
| Link B | Fixed amount (e.g. CHF 20, CHF 50) | Card + TWINT |

Add two buttons to `index.html`:
- **PAY BY CARD** → Link A
- **PAY BY TWINT** → Link B (with a fixed amount)

This is the simplest reliable solution.

#### Option 2 — Fixed amount, all methods

Create a single Payment Link with a fixed amount (e.g. CHF 30).
Enable both Card and TWINT.
Visitors cannot choose a different amount.

#### Option 3 — Stripe Checkout with a backend (not recommended here)

A custom backend can pass a dynamic amount to Stripe Checkout at the moment of payment. This supports both custom amounts and TWINT, but requires a server. This is outside the scope of this project.

**Recommendation:** Use Option 1. It requires no backend and covers both use cases.

### Minimum and maximum amounts

- Stripe enforces a minimum charge of **CHF 0.50**.
- There is no enforced maximum, but very large amounts may trigger additional verification.
- TWINT has its own limits set by the customer's bank (typically CHF 3,000 per transaction).

### Official references

- Stripe Payment Links: [https://stripe.com/docs/payment-links](https://stripe.com/docs/payment-links)
- TWINT on Stripe: [https://stripe.com/docs/payments/twint](https://stripe.com/docs/payments/twint)
- Supported payment methods by country: [https://stripe.com/docs/payments/payment-methods/overview](https://stripe.com/docs/payments/payment-methods/overview)
Current Swiss pricing (verify at the link below — fees may change):

| Payment method | Fee |
|---|---|
| European card | 1.5% + CHF 0.30 |
| Non-European card | 3.25% + CHF 0.30 |
| TWINT | 1.5% + CHF 0.30 |

Example: a CHF 50 payment by European card costs approximately CHF 1.05 in Stripe fees. You receive CHF 48.95.

Official Stripe pricing for Switzerland: [https://stripe.com/ch/pricing](https://stripe.com/ch/pricing)
