# OpenFGA for Advertising Platforms

## Use Case

This model represents the authorization needs of an **Advertising platform**, like Google Ads, Meta Ads, TikTok Ads, or LinkedIn Ads. These platforms manage campaigns, ad groups, ads, creatives, and performance reports across advertising accounts.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own advertising campaigns, creatives, and reporting data.
- **Advertising roles**: Organization-level roles (`admin`, `campaign_manager`, `analyst`) control access to ad resources. Admins have full access, campaign managers can create and edit campaigns, and analysts have read-only access.
- **Ad approval workflow**: Campaign managers create ads, but only admins can approve them for publishing, ensuring quality control before ads go live.
- **Campaign publishing**: Only admins can publish campaigns (go live), preventing unauthorized changes to active advertising spend.
- **Analyst read-only access**: Analysts can view all campaigns, ads, creatives, and reports but cannot modify anything, enabling safe access to performance data.
- **Creative ownership**: Creators can view and edit their own creatives. Campaign managers have broader access across the organization.
- **Report access control**: Analysts and campaign managers can view reports, campaign managers can create reports, but only admins can delete them.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `ads` directory, run `fga model test --tests store.fga.yaml`
