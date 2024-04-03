# OpenFGA Developer Portal Example

## Use-Case

If your application provides a way to generate API keys to applications to call your APIs, you'll want to create a dashboard where users can manage API keys, and define each API key's permissions.

For this example, we have the following requirements

- We want to support multiple customers, and each one should be able to define regular members and administrators for their account.
- Each customer should be able to define multiple applications.
- Each customer's administrator should be able to:
    - Manage organization members (invite, remove, view)
    - Manage applications (create, update, delete)
    - Manage application's credentials
    - Configure application's permissions
- Regular members should be able to:
    - View members
    - View application details
- Each API should be provisioned access to specific components they can use. For example, the B2B SaaS application could have a Purchase and Payments components. If the customer only paid for the Purchase component, their applications should not be able to access the Payments one.



See the model, tuples and tests in the [store.yaml](./store.fga.yaml) file.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `developer-portal` directory, run `fga model test --tests store.yaml`
