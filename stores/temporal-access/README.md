# OpenFGA Temporal Access Sample Store

## Use-Case

An application needs to grant access to users to a document during a specific period of time. 

We can model it with OpenFGA using [conditional relationship tuples](https://openfga.dev/docs/modeling/conditions).

The model, tuples and unit tests are detailed in [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `temporal-access` directory, run `fga model test --tests store.fga.yaml`
