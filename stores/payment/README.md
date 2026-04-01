# OpenFGA for Payment Platforms

## Use Case

This model represents the authorization needs of a **payment platform**, like Stripe, PayPal, or Square. These platforms manage payment links, transactions, payouts, refunds, and subscriptions across merchant organizations.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own payment processing, billing, and financial data.
- **Payment roles**: Organization-level roles (`admin`, `finance_manager`, `viewer`) control access. Admins have full access, finance managers can process payments and manage subscriptions, and viewers have read-only access.
- **Payment link management**: Creators can manage their own payment links. Finance managers have broader access across the organization.
- **Refund workflow**: Finance managers can issue refunds on payments. Only admins can approve standalone refund records, ensuring financial oversight.
- **Payout approval**: Payouts are restricted to finance managers and admins for viewing. Only admins can approve payouts, preventing unauthorized disbursements.
- **Subscription management**: Finance managers can edit and cancel subscriptions. Only admins can permanently delete subscription records.
- **Payout visibility**: Viewers cannot see payout details, restricting access to sensitive disbursement information to finance managers and admins.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `payment` directory, run `fga model test --tests store.fga.yaml`
