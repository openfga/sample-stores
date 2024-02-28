# OpenFGA Groups Resource Attributes  Sample Store

* **Title**: **Temporal Access** 

## Use-Case

It enables members of specific groups to access content depending on resource attributes. For example, you can specify that members of the 'marketing' team can only view documents that are 'published' but members of the 'content' team can also view 'draft' documents.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `group-resource-attributes` directory, run `fga model test --tests store.fga.yaml`
