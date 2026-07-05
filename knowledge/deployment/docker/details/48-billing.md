---
title: "Docker 计费"
source: "https://docs.docker.com/billing/"
version: "latest"
---

# Docker 计费

> 原始文档来源：https://docs.docker.com/billing/

---

## Use 3D Secure authentication for Docker billing

# Use 3D Secure authentication for Docker billing

Docker supports 3D Secure (3DS), an extra layer of authentication required
for certain credit card payments. If your bank or card issuer requires 3DS, you
may need to verify your identity before your payment can be completed.

## How it works

When a 3DS check is triggered during checkout, your bank or card issuer
may ask you to verify your identity. This can include:

- Entering a one-time password sent to your phone
- Approving the charge through your mobile banking app
- Answering a security question or using biometrics

The exact verification steps depend on your financial institution's
requirements.

## When you need to verify

You may be asked to verify your identity when performing any of the following
actions:

- Starting a [paid subscription](/subscription/manage/)
- Changing your [billing cycle](/billing/cycle/) from monthly to annual
- [Upgrading your subscription](/subscription/manage/#upgrade-plans)
- [Adding seats](/admin/organization/manage/manage-seats/) to an existing subscription

If 3DS is required and your payment method supports it, the verification prompt
will appear during checkout.

## Troubleshooting payment verification

If you're unable to complete your payment due to 3DS:

1. Retry your transaction. Make sure you're completing the verification
   prompt in the same browser tab.
1. Use a different payment method. Some cards may not support 3DS properly
   or be blocked.
1. Contact your bank. Your bank may be blocking the payment or the 3DS
   verification attempt.

> [!NOTE]
>
> Disabling ad blockers or browser extensions that block pop-ups can help
> the 3DS prompt display correctly.

---

## Change your billing cycle

# Change your billing cycle

You can choose between a monthly or annual billing cycle when purchasing a
subscription. If you have a monthly billing cycle, you can choose to
switch to an annual billing cycle.

If you're on a monthly plan, you can switch to a yearly plan at any time.
However, switching from a yearly to a monthly cycle isn't supported.

When you change your billing cycle:

- Your next billing date reflects the new cycle. To find your next billing date,
  see [View renewal date](/billing/cycle/history/#view-renewal-date).
- Your subscription's start date resets. For example, if the monthly
  subscription started on March 1 and ended on April 1, switching the billing
  duration on March 15, 2024, resets the new start date to March 15, 2024, with
  an end date of March 15, 2025.
- Any unused portion of your monthly subscription is prorated and applied as
  credit toward an annual subscription. For example, if your monthly cost is $10
  and you're used value is $5, when you switch to an annual cycle ($100), the
  final charge is $95 ($100-$5).

## Change personal account to an annual cycle

Pay by invoice is not available for subscription upgrades or changes.

To change your billing cycle:

1. Sign in to [Docker Home](https://app.docker.com/) and select
   your organization.
1. Select **Billing**.
1. On the plans and usage page, select **Switch to annual billing**.
1. Verify your billing information.
1. Select **Continue to payment**.
1. Verify payment information and select **Upgrade subscription**. If you choose to pay using a US bank account, you must verify the account. For more information, see [Verify a bank account](/billing/payment-method/#verify-a-bank-account).

The billing plans and usage page will now reflect your new annual plan details.

## Change organization to an annual cycle

You must be an organization owner to make changes to the payment information.

Pay by invoice is not available for subscription upgrades or changes.

Follow these steps to switch from a monthly to annual billing cycle for your
organization's Docker subscription:

1. Sign in to [Docker Home](https://app.docker.com/) and select
   your organization.
1. Select **Billing**.
1. On the plans and usage page, select **Switch to annual billing**.
1. Verify your billing information.
1. Select **Continue to payment**.
1. Verify payment information and select **Upgrade subscription**. If you choose to pay using a US bank account, you must verify the account. For more information, see [Verify a bank account](/billing/payment-method/#verify-a-bank-account).

---

## Manage your billing information

# Manage your billing information

You can update the billing information for your personal account or for an
organization. When you update your billing information, these changes apply to
future billing invoices. The email address you provide for a billing account is
where Docker sends all invoices and other billing related communications.

> [!NOTE]
>
> Existing invoices, whether paid or unpaid, cannot be updated.
> Changes only apply to future invoices.

## Manage billing information

### Personal account

To update your billing information:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Billing**.
1. Select **Billing information** from the left-hand navigation.
1. On your billing information card, select **Change**.
1. Update your billing contact and billing address information.
1. Optional. To add or update a VAT ID, select the **I'm purchasing as a business** checkbox and enter your Tax ID.

   > [!IMPORTANT]
   >
   > Your VAT number must include your country prefix. For example, if you are
   > entering a VAT number for Germany, you would enter `DE123456789`.

1. Select **Update**.

### Organization

> [!NOTE]
>
> You must be an organization owner to make changes to the billing information.

To update your billing information:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Billing**.
1. Select **Billing information** from the left-hand navigation.
1. On your billing information card, select **Change**.
1. Update your billing contact and billing address information.
1. Optional. To add or update a VAT ID, select the **I'm purchasing as a business** checkbox and enter your Tax ID.

   > [!IMPORTANT]
   >
   > Your VAT number must include your country prefix. For example, if you are
   > entering a VAT number for Germany, you would enter `DE123456789`.

1. Select **Update**.

## Update your billing email address

Docker sends the following billing-related emails:

- Confirmations (new subscriptions, paid invoices)
- Notifications (card failure, card expiration)
- Reminders (subscription renewal)

You can update the email address that receives billing invoices at any time.

### Personal account

To update your billing email address:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Billing**.
1. Select **Billing information** from the left-hand navigation.
1. On your billing information card, select **Change**.
1. Update your billing contact information and select **Update**.

### Organizations

To update your billing email address:

1. Sign in to [Docker Home](https://app.docker.com/) and select
   your organization.
1. Select **Billing**.
1. Select **Billing information** from the left-hand navigation.
1. On your billing information card, select **Change**.
1. Update your billing contact information and select **Update**.

---

## Billing FAQs

# Billing FAQs

### What happens if my subscription payment fails?

If your subscription payment fails, there is a grace period of 15 days,
including the due date. Docker retries to collect the payment 3 times using the
following schedule:

- 3 days after the due date
- 5 days after the previous attempt
- 7 days after the previous attempt

Docker also sends an email notification
`Action Required - Credit Card Payment Failed` with an attached unpaid invoice
after each failed payment attempt.

Once the grace period is over and the invoice is still not paid, the
subscription downgrades to a free subscription and all paid features are
disabled.

### Can I manually retry a failed payment?

No. Docker retries failed payments on a [retry schedule](/billing/faqs/#what-happens-if-my-subscription-payment-fails).

To ensure a retired payment is successful, verify your default payment is
updated. If you need to update your default payment method, see
[Manage payment method](/billing/payment-method/#manage-payment-method).

### Does Docker collect sales tax and/or VAT?

Docker collects sales tax and/or VAT from the following:

- For United States customers, Docker began collecting sales tax on July 1, 2024.
- For European customers, Docker began collecting VAT on March 1, 2025.
- For United Kingdom customers, Docker began collecting VAT on May 1, 2025.

To ensure that tax assessments are correct, make sure that your billing
information and VAT/Tax ID, if applicable, are updated. See
[Update the billing information](/billing/details/).

If you're exempt from sales tax, see
[Register a tax certificate](/billing/tax-certificate/).

### Does Docker offer academic pricing?

For academic pricing, contact the
[Docker Sales Team](https://www.docker.com/company/contact).

### Can I use pay by invoice for upgrades or additional seats?

No. Pay by invoice is only available for renewing annual subscriptions, not for
purchasing upgrades or additional seats. You must use card payment or US bank
accounts for these changes.

For a list of supported payment methods, see
[Add or update a payment method](/billing/payment-method/).

---

## Invoices and billing history

# Invoices and billing history

Learn how to view and pay invoices, view your billing history, and verify
your billing renewal date. All monthly and annual subscriptions are
automatically renewed at the end of the subscription term using your default
payment method.

## View an invoice

Your invoice includes the following:

- Invoice number
- Date of issue
- Due date
- Your "Bill to" information
- Amount due (in USD)
- Pay online: Select this link to pay your invoice online
- Description of your order, quantity if applicable, unit price, and
  amount (in USD)
- Subtotal, discount (if applicable), and total

The information listed in the "Bill to" section of your invoice is based on
your billing information. Not all fields are required. The billing information
includes the following:

- Name (required): The name of the administrator or company
- Address (required)
- Email address (required): The email address that receives all billing-related
  emails for the account
- Phone number
- Tax ID or VAT

You can鈥檛 make changes to a paid or unpaid billing invoice. When you update
your billing information, this change won't update an existing invoice.

If you need
to update your billing information, make sure you do so before your
subscription renewal date when your invoice is finalized.

For more information, see [Update billing information](/billing/history/details/).

## Pay an invoice

> [!NOTE]
>
> Pay by invoice is only available for subscribers on an annual billing cycle.
> To change your billing cycle, see [Change your billing cycle](/billing/cycle/).

If you've selected pay by invoice for your subscription, you'll receive email
reminders to pay your invoice at 10 days before the due date, on the due date,
and 15 days after the due date.

You can pay an invoice from the Docker Billing Console:

1. Sign in to [Docker Home](https://app.docker.com/) and choose your organization.
1. Select **Billing**.
1. Select **Invoices** and locate the invoice you want to pay.
1. In the **Actions** column, select **Pay invoice**.
1. Fill out your payment details and select **Pay**.

When your payment has processed, the invoice's **Status** column will update to
**Paid** and you will receive a confirmation email.

If you choose to pay using a US bank account, you must verify the account. For
more information, see [Verify a bank account](/billing/payment-method/#verify-a-bank-account).

### View renewal date

You receive your invoice when the subscription renews. To verify your renewal
date:

1. Sign in to [Docker Home Billing](https://app.docker.com/billing).
1. Find your renewal date and amount on your subscription plan card.

## Include your VAT number on your invoice

> [!NOTE]
>
> If the VAT number field is not available, complete the
> [Contact Support form](https://hub.docker.com/support/contact/). This field
> may need to be manually added.

To add or update your VAT number:

1. Sign in to [Docker Home](https://app.docker.com/) and choose your
   organization.
1. Select **Billing**.
1. Select **Billing information** from the left-hand menu.
1. Select **Change** on your billing information card.
1. Ensure the **I'm purchasing as a business** checkbox is checked.
1. Enter your VAT number in the Tax ID section.

   > [!IMPORTANT]
   >
   > Your VAT number must include your country prefix. For example, if you are
   > entering a VAT number for Germany, you would enter `DE123456789`.

1. Select **Update**.

Your VAT number will be included on your next invoice.

## View billing history

You can view your billing history and download past invoices for a personal
account or organization.

### Personal account

To view billing history:

1. Sign in to [Docker Home](https://app.docker.com/) and choose your
   organization.
1. Select **Billing**.
1. Select **Invoices** from the left-hand menu.
1. Optional. Select the **Invoice number** to open invoice details.
1. Optional. Select the **Download** button to download an invoice.

### Organization

You must be an owner of the organization to view the billing history.

To view billing history:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
1. Select **Billing**.
1. Select **Invoices** from the left-hand menu.
1. Optional. Select the **invoice number** to open invoice details.
1. Optional. Select the **download** button to download an invoice.

---

## Add or update a payment method

# Add or update a payment method

Docker supports different payment methods for your paid personal
account or organization. This page describes supported payment types, how to make payments from [Docker Home](https://app.docker.com/), and how to set up pay by invoice.

## Supported payment types

You can add a payment method or update your account's existing payment method
at any time. All charges are in United States dollars (USD). The following payment methods are supported:

| Category      | Payment type                                                            |
| ------------- | ----------------------------------------------------------------------- |
| Cards         | Visa, MasterCard, American Express, Discover, JCB, Diners, UnionPay     |
| Wallets       | Stripe Link                                                             |
| Bank accounts | Automated Clearing House (ACH) transfer with a verified US bank account |

## Prerequisites

Certain payment methods require additional steps before selecting them as a payment method:

- You must [verify a bank account](/billing/payment-method/#verify-a-bank-account) before choosing a bank account.
- You must have a Docker Business or Docker Team plan to [pay by invoice](/billing/payment-method/#enable-and-disable-pay-by-invoice).
- You must be an existing Stripe Link customer, or fill out the card information form to use Link payments.

## Manage payment method

Paid personal accounts and organizations follow the same procedures to add, update, or remove payment methods.

### Add payment method

1. Sign in to [Docker Home](https://app.docker.com/).
1. Select your account name for personal accounts, or select your organization name for organization accounts.
1. Select **Billing**, then **Payment methods**.
1. Select **Add payment method** and enter your new payment information:
   - For first time setup, fill in your billing information.
   - To purchase as a business, provide your tax ID.
1. Choose to add a card, a US bank account, or a Link payment.
   - To pay with card, fill out the card information form.
   - To pay with a US bank account:
     - Verify your **Email** and **Full name**.
     - If your bank is listed, select your bank's name.
     - If your bank is not listed, select **Search for your bank**.
   - To pay through Link, select an existing payment and choose **Use this card**.
1. Finish adding the payment method by selecting **Add payment method**.

### Set default payment method

After adding one or more payment methods, you can set one as a default method.

1. From **Billing**, go to **Payment methods**.
1. Find the payment method you want to set as default from the **Payment method** table.
1. Select the three dots, then choose **Set as default**.

### Remove payment method

To remove a single payment method:

1. From **Billing**, go to **Payment methods**.
1. Find the payment method you want to remove from the **Payment method** table.
1. Select the three dots, then choose **Remove**.

To remove your default payment method, first set a different payment method as default, or [downgrade to a free subscription](/subscription/plans/docker/#cancel-a-docker-plan).

## Enable and disable pay by invoice

> [!TIP]
> Do you need to pay by invoice? [Upgrade to a Docker Business or Docker Team plan](https://www.docker.com/pricing?ref=Docs&refAction=DocsBillingPaymentMethod) and choose the annual subscription.

Pay by invoice requires you to pay upfront for your first subscription period using a payment card or ACH bank transfer. At renewal time, instead of automatic payment, you'll receive an invoice via
email that you must pay manually.

Follow these steps to enable or disable pay by invoice:

1. Sign in to [Docker Home](https://app.docker.com/) and select your
   organization.
2. Select **Billing**, then **Payment methods**.
3. Select **Pay by invoice**, then select the pay by invoice toggle to enable or disable.
4. Confirm your billing contact details. If you need to change them, select
   **Change** and enter your new details.

Pay by invoice is not available for
subscription upgrades or changes.

## Verify a bank account

There are two ways to verify a bank account as a payment method:

- Instant verification: Docker supports several major banks for instant
  verification.
- Manual verification: All other banks must be verified manually.

### Instant verification

To verify your bank account instantly, you must sign in to your bank account
from the Docker billing flow:

1. Choose **US bank account** as your payment method.
1. Verify your **Email** and **Full name**.
1. If your bank is listed, select your bank's name or
   select **Search for your bank**.
1. Sign in to your bank and review the terms and conditions. This agreement
   allows Docker to debit payments from your connected bank account.
1. Select **Agree and continue**.
1. Select an account to link and verify, and select **Connect account**.

When the account is verified, you will see a success message in the pop-up
modal.

### Manual verification

To verify your bank account manually, you must enter the micro-deposit amount
from your bank statement:

1. Choose **US bank account** as your payment method.
1. Verify your **Email** and **First and last name**.
1. Select **Enter bank details manually instead**.
1. Enter your bank details: **Routing number** and **Account number**.
1. Select **Submit**.
1. You will receive an email with instructions on how to manually verify.

Manual verification uses micro-deposits. You鈥檒l see a small deposit
(such as $0.01) in your bank account within 1鈥�2 business days. Open your manual
verification email and enter the amount of this deposit to verify your account.

## Failed payments

If your payment fails, select **Pay now**. This redirects you from Docker Hub so you can manually retry the payment through Stripe.

You have a grace period of 15 days
including the due date when your payment fails. Docker retries to collect the payment 3 times using the
following schedule:

- 3 days after the due date
- 5 days after the previous attempt
- 7 days after the previous attempt

Docker also sends an email notification
`Action Required - Credit Card Payment Failed` with an attached unpaid invoice
after each failed payment attempt.

Once the grace period is over and the invoice is still not paid, the
subscription downgrades to a free subscription and all paid features are
disabled.

---

## Submit a tax exemption certificate

# Submit a tax exemption certificate

If you're a customer in the United States and are exempt from sales tax, you
can submit a valid tax exemption certificate to Docker Support.

If you're a global customer subject to VAT, make sure to include your
[VAT number](/billing/history/#include-your-vat-number-on-your-invoice)
along with your country prefix when you update your billing profile.

## Prerequisites

Before submitting your certificate:

- The customer name must match the name on the certificate.
- The certificate must list Docker Inc. as the Seller or Vendor, with all
  relevant fields completed.
- The certificate must be signed, dated, and not expired.
- You must include the Docker ID or namespace(s) for all accounts to
  apply the certificate to.

> [!IMPORTANT]
>
> You can use the same certificate for multiple namespaces, if applicable.

## Contact information

Use the following contact information on your certificate:

Docker, Inc.
3790 El Camino Real #1052
Palo Alto, CA 94306
(415) 941-0376

## Register a tax certificate

1. [Submit a Docker Support ticket](https://hub.docker.com/support/contact?topic=Billing&subtopic=Tax%20information) to initiate the process to register a tax certificate.
1. Enter **Tax certificate** as the support ticket **Subject**.
1. In the **Details** field, enter **Submitting a tax certificate**.
1. Instructions will populate on how to submit a tax certificate.
1. Fill out all required fields on the support form.
1. In the file upload section, add the tax certificate by dragging and dropping
   the file, or selecting **Browse files**.
1. Select **Submit**.

Docker's support team will reach out to you if any additional information is
required. You'll receive an e-mail confirmation from Docker once your tax
exemption status is applied to your account.

---
