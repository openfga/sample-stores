# OpenFGA for CRM Systems

## Use Case

This model represents the authorization needs of a **Customer Relationship Management (CRM) system**, like Salesforce, or HubSpot. These platforms manage accounts (companies), contacts, leads, opportunities, engagements, notes, and tasks across the sales pipeline.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own sales pipeline, accounts, and customer data.
- **Sales roles**: Organization-level roles (`admin`, `sales_manager`, `sales_rep`) control pipeline visibility. Admins have full access, sales managers can see and edit all deals, and sales reps only see records they own.
- **Account ownership**: Each account (company) has an owner who can view and edit it. Sales managers have organization-wide account visibility.
- **Contact-to-account inheritance**: Contacts inherit visibility from their parent account, so the account owner automatically sees all contacts at that company.
- **Opportunity pipeline**: Opportunities are linked to accounts. Owners can edit and close deals, while sales managers have organization-wide oversight. Only owners and managers can close deals.
- **Lead lifecycle**: Lead owners can manage and convert leads. Sales managers can also convert leads, enabling pipeline oversight. Only admins can delete leads.
- **Engagement tracking**: Call logs, meetings, and emails are visible to their owner and sales managers. Only the owner can edit their engagement records.
- **Note privacy**: Note authors can edit their own notes and delete them. Sales managers can view notes but cannot edit others' notes.
- **Task management**: Task owners can view, edit, and complete tasks. Only the owner can mark a task as complete, preventing unauthorized status changes.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `crm` directory, run `fga model test --tests store.fga.yaml`
