# OpenFGA Advanced Entitlements Sample Store

## Use-Case

This entitlements model is inspired on [Notion's subscription plans](https://www.notion.so/pricing).

Entitlements limits can be defined based on feature usage like number of collaborators, rows synced, page history days.

The model, tuples and unit tests are detailed in [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `advanced-entitlements` directory, run `fga model test --tests store.fga.yaml`
