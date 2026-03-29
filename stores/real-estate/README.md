# OpenFGA for Real Estate Systems

## Use Case

This model represents the authorization needs of a **real estate platform**, like Zillow, Realtor.com, or a brokerage MLS system. These platforms manage properties, listings, transactions, inspections, and neighborhood market data across real estate brokerages.

The model captures the following requirements:

- **Multi-tenancy**: Multiple brokerages (organizations), each with their own agents, listings, and transaction records.
- **Real estate roles**: Organization-level roles (`admin`, `broker`, `agent`, `appraiser`) control access. Admins have full control, brokers oversee all listings and transactions, agents manage their own listings, and appraisers handle inspections.
- **Agent-scoped listings**: Listing agents can edit and close their own listings. They cannot modify listings belonging to other agents. Brokers can manage any listing across the brokerage.
- **Transaction access**: Buyer and seller agents can edit transactions they are involved in. The listing agent and broker also have edit access through the listing chain. Financial data (sale price, commission) is restricted to parties on the transaction.
- **Property management**: Properties are brokerage-wide records. Brokers and admins can edit property details. All brokerage members can view properties for market analysis.
- **Inspection reports**: Appraisers create and edit inspection reports linked to properties. All brokerage members can view inspections. Only admins can delete inspection records.
- **Neighborhood data**: Market and neighborhood data is viewable by all brokerage members for comparable analysis. Only admins can edit neighborhood records.
- **Parent-scoped creation**: Listing and inspection creation are checked on the organization (`can_create_listing`, `can_create_inspection`) rather than on objects that do not exist yet.
- **Financial data isolation**: Transaction financial details (commissions, sale prices) are only visible to the agents involved in the deal and brokerage management, not to general staff.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `real-estate` directory, run `fga model test --tests store.fga.yaml`
