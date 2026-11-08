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

---

## D. GitHub Pages deployment

No command line is required. Everything is done through the GitHub website.

### 1. Create a GitHub account

Go to [https://github.com](https://github.com) and create a free account if you do not have one.

### 2. Create a new repository

1. Click the **+** icon at the top right → **New repository**.
2. Name it `nobordercmc` (or any name you prefer).
3. Set it to **Public** (required for free GitHub Pages).
4. Click **Create repository**.

### 3. Upload the project files

1. On the repository page, click **Add file** → **Upload files**.
2. Upload all files, preserving the folder structure:
   ```
   index.html
   style.css
   README.md
   img/logo.png
   img/background.png
   ```
   To upload the `img/` folder, drag the entire folder into the upload area.
3. Click **Commit changes**.

### 4. Enable GitHub Pages

1. Go to the repository **Settings** tab.
2. In the left sidebar, click **Pages**.
3. Under **Source**, select **Deploy from a branch**.
4. Under **Branch**, select **main** and the folder **/ (root)**.
5. Click **Save**.

### 5. Access the published website

After a minute or two, GitHub Pages will publish your site.

The URL will be:
```
https://YOUR_GITHUB_USERNAME.github.io/YOUR_REPOSITORY_NAME/
```

Example: `https://johndoe.github.io/nobordercmc/`

You will see this URL in **Settings → Pages** once it is ready.

### 6. Connect a custom domain (optional, later)

If you want a custom domain (e.g. `nobordercmc.com`):

1. Buy the domain from a registrar (e.g. Infomaniak, Namecheap, Gandi).
2. In your domain registrar's DNS settings, add a CNAME record pointing to `YOUR_GITHUB_USERNAME.github.io`.
3. In GitHub → Settings → Pages → Custom domain, enter your domain.
4. Enable **Enforce HTTPS**.

GitHub Pages custom domain documentation: [https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)

---

## E. Connect Stripe to the website

Open `index.html` in a text editor (Notepad on Windows, TextEdit on Mac, or any code editor).

Find this line:

```html
<a
  href="YOUR_STRIPE_PAYMENT_LINK"
```

Replace `YOUR_STRIPE_PAYMENT_LINK` with the actual URL you copied from the Stripe Dashboard.

Example result:

```html
<a
  href="https://buy.stripe.com/abc123def456"
```

Save the file and re-upload it to GitHub (same process as step 3 in Section D — upload the updated `index.html` and commit).

The change will be live within a minute.

---

## F. Test checklist

Work through this list before announcing the website publicly.

- [ ] **Desktop display** — open the website on a computer. Logo and background image load correctly. PAY button is visible and readable.
- [ ] **Mobile display** — open the website on a phone. Layout fits the screen. Button is easy to tap.
- [ ] **Logo loads** — `img/logo.png` is visible. No broken image icon.
- [ ] **Background loads** — `img/background.png` covers the full screen.
- [ ] **PAY button** — clicking it opens the Stripe payment page (not a 404 or error).
- [ ] **Test payment** — complete a test payment using card number `4242 4242 4242 4242`, any future expiry, any CVC. Payment succeeds.
- [ ] **Customer receipt** — after the test payment, check the email address you entered. A receipt from Stripe should arrive within a few minutes.
- [ ] **TWINT availability** — if you created a TWINT Payment Link (Option 1 from Section C), verify the TWINT button appears on the Stripe payment page.
- [ ] **Live payment** — after switching to live mode and updating the URL in `index.html`, make a real payment of CHF 1 to confirm everything works end to end.
- [ ] **Stripe Dashboard** — confirm the test and live payments appear in the Stripe Dashboard under **Payments**.

---

## G. Maintenance and security

### No backend maintenance

This website has no server, no database, and no software to update.
GitHub Pages serves static files. There is nothing to patch or maintain on the hosting side.

### Never commit Stripe secret keys

Your Stripe account has two types of keys:

| Key | Starts with | Use |
|---|---|---|
| Publishable key | `pk_live_` or `pk_test_` | Can be public (used in browser-side code) |
| Secret key | `sk_live_` or `sk_test_` | **Never share. Never put in a file uploaded to GitHub.** |

This project does not use any Stripe keys at all — only a Payment Link URL, which is safe to publish.

If you ever add more advanced Stripe features in the future, never put a secret key (`sk_`) in any file that is uploaded to GitHub or any public location.

### Managing payments and refunds

All payment management is done in the **Stripe Dashboard** at [https://dashboard.stripe.com](https://dashboard.stripe.com):

- View all payments under **Payments**.
- Issue refunds by clicking a payment and selecting **Refund**.
- Export payment data under **Reports**.
- Manage payouts under **Balances**.

### GitHub Pages only hosts the static website

GitHub Pages cannot run server-side code. It only serves the HTML, CSS and image files. Payment processing happens entirely on Stripe's infrastructure.

---

## H. Costs

### GitHub Pages

**Free** for public repositories.

Documentation: [https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages)

### Custom domain (optional)

If you register a custom domain (e.g. `nobordercmc.com`), the cost is typically **CHF 10–20 per year** depending on the registrar and the domain extension (`.com`, `.ch`, `.org`, etc.).

Infomaniak domain registration: [https://www.infomaniak.com/en/domains](https://www.infomaniak.com/en/domains)

### Stripe transaction fees

Stripe charges a fee on each successful payment. There is no monthly fee.

Current Swiss pricing (verify at the link below — fees may change):

| Payment method | Fee |
|---|---|
| European card | 1.5% + CHF 0.30 |
| Non-European card | 3.25% + CHF 0.30 |
| TWINT | 1.5% + CHF 0.30 |

Example: a CHF 50 payment by European card costs approximately CHF 1.05 in Stripe fees. You receive CHF 48.95.

Official Stripe pricing for Switzerland: [https://stripe.com/ch/pricing](https://stripe.com/ch/pricing)

### No additional hosting required

GitHub Pages is free. There is no server to rent or maintain.

---

## Project file structure

```
/
├── index.html          ← Welcome page
├── style.css           ← Visual design
├── README.md           ← This file
└── img/
    ├── background.png  ← Full-page background (add your file here)
    └── logo.png        ← No Border CMC logo (add your file here)
```

---

## Quick start summary

1. Add `img/logo.png` and `img/background.png` to the `img/` folder.
2. Set up Stripe and create a Payment Link (Section B).
3. Replace `YOUR_STRIPE_PAYMENT_LINK` in `index.html` with your Stripe URL (Section E).
4. Upload everything to a GitHub repository (Section D).
5. Enable GitHub Pages (Section D, step 4).
6. Test (Section F).
7. Share the URL.
