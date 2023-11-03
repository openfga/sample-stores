# OpenFGA Banking Sample Store

* **Title**: **Bankings** 

## Use-Case

It models a banking system, with account managers and account owners, where they have different limits for doing bank transactions. The limit can be overruled for a specific transaction.

It takes advantage of Conditional Relationship Tuples, which are available in OpenFGA v1.4.0-rc1 and above.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `banking` directory, run `fga model test --tests store.fga.yaml`
