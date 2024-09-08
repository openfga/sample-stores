# OpenFGA Developer Portal Example

## Use-Case

It models a developer portal for a B2B SaaS application, where customers can define applications that will make calls to your APIs. 

For this example, we have the following requirements:

- Each customer should be able to define regular members and administrators. 
- Each customer should be able to define multiple applications.
- Each customer's administrator should be able to:
    - Manage customer's members (invite, remove, view)
    - Manage applications (create, update, delete)
    - Manage application's credentials
    - Configure application's permissions
- Regular members should be able to:
    - View members
    - View application details
- Each application should be provisioned access to specific components they can use. For example, the B2B SaaS application could have a Purchase and Payments components. If the customer only paid for the Purchase component, their applications should not be able to access the Payments component.

The model, tuples and unit tests are detailed in [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `developer-portal` directory, run `fga model test --tests store.yaml`
