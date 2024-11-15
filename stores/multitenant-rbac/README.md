# OpenFGA Multi-Tenant Role Based Access Example

## Use-Case

This example demonstrates how to implement an advanced Role Based Access Control scenario for a B2B application, which the following requirements:

- **Multi-tenancy**: You can define multiple tenants, modeled as `organizations`. Each tenant can have their own users, groups, roles and resources.
- **Predefined roles**: Each organization has an 'admin' role that can't be removed
- **Custom roles**: Each organization can define additional roles
- **Nested groups**: You can assign user to groups, and specify that all members of a group are members of another group.
- **Role<->Group assignment**: Roles can be assigned to individual users, members of a group users, or to assignees of a different role. 
- **Coarse grained resource access**: Access to the organization's resource is defined purely on roles. In this example we do not allow defining fine-grained permissions per document, to showcase how OpenFGA provides significant value even if you don't need fine-grained permissions.

The model, tuples and unit tests are detailed in [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `multi-tenant-rbac` directory, run `fga model test --tests store.fga.yaml`
