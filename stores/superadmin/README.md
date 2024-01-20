# OpenFGA Super Admin Example 

## Use-Case

In B2B SaaS applications, each customer usually has one or more admin users that have full permission over the account.

It's also common that employees of the SaaS company can perform specific actions across different accounts, for configuration or help desk purposes. 

Let's assume we want to build a simple multi-tenant SaaS Project management system, with the following roles:

- **System Administrator**: Can perform any action in the system.
- **Help-Desk User**: Can be granted permissions to view any resource for a specific customer, for 1 hour.
- **Customer Administrator**: Can perform any action for any resource linked to a specific customer.
- **System Management Service App**: A service application that can perform any action in the system.

The application will have two kinds of resources:
- **Project**
- **Tasks**

See the model, tuples and tests in the [store.yaml](./store.fga.yaml) file.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `slack` directory, run `fga model test --tests store.yaml`
