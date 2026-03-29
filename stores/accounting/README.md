# OpenFGA for Accounting Systems

## Use Case

This model represents the authorization needs of an **accounting platform**, like QuickBooks Online, Xero, or FreshBooks. These platforms manage charts of accounts, invoices, expenses, payments, journal entries, and financial statements.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own chart of accounts, contacts, and financial records.
- **Separation of duties**: Three distinct roles — `admin` (full control + approvals), `accountant` (day-to-day bookkeeping), and `auditor` (read-only access to all financial data). Regular members have no access to accounting data.
- **Read-only audit access**: Auditors can view all financial data (accounts, invoices, expenses, payments, journal entries, financial statements) but cannot modify anything, supporting compliance and external audit requirements.
- **Expense workflow**: Employees submit expenses they can view and edit; accountants and admins can approve them.
- **Purchase order approval chain**: Requesters create and edit purchase orders, designated approvers authorize them, and admins have override approval authority.
- **Journal entry posting**: Accountants can create and edit journal entries, but only admins can post them to the general ledger, preventing unauthorized modifications to the books.
- **Invoice lifecycle**: Creators can manage their invoices, but voiding requires admin authority.
- **Financial statement protection**: Balance sheets, income statements, and cash flow statements are read-only resources accessible only to accountants, admins, and auditors.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `accounting` directory, run `fga model test --tests store.fga.yaml`
