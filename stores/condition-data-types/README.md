# OpenFGA Condition Data Types Store

* **Title**: **Condition Data Types Store** 

## Use-Case

OpenFGA's Conditional Relationship Tuples are defined using [Google's CEL expressions](https://github.com/google/cel-spec/blob/master/doc/langdef.md).

This example showcases the different data types and expressions that are supported in OpenFGA conditions and how to use them. 

It takes advantage of Conditional Relationship Tuples, which are available in OpenFGA v1.4.0-rc1 and above.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `condition-data-types` directory, run `fga model test --tests store.fga.yaml`
