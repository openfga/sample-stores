# OpenFGA Modeling Attribute-Based Access Control with Relationship-Based Access Control

## Use-Case

This example demonstrates how to model Attribute-Based access control scenarios with relationship-based access Control constructs.

In ABAC, you make authorization decisions based on different attributes of the user, the resource, and the environment. Depending on the kind of attribute, you can model them pretty easily in ReBAC or not.

If the attribute is a **reference to another entity**, e.g. the equivalent of a foreign key in a relational database, then you can model it as a relation. For example, the user's department can be modeled as:

```
type user

type department
    relations
        define member : [user]

type folder
    relations
        define department : [department]
        define viewer : member from department
```

If the attribute is a **category with a few values**, e.g. a boolean, a category like `status', then you can model it by defining a relationship for each possible value, where the resource is related to itself.

For example, the following model defines a policy where the user can only view a document if their email is verified, you'll add an email_verified relation to the user type:

```
type user
    relations
        define email_verified : [user]
        
type document
    relations
        define viewer : [user]
        define can_view : email_verified from viewer
```

If you want to model a document status, you'll add a relation for each possible status with the document itself:

```
type user
        
type document
    relations
        define published : [document]
        define draft : [document]

        define viewer : [user]

        define can_view : viewer from published
```

If the attribute is a discrete variable with many possible values, e.g. birth date, age, IP address, a currency amount, then it's not possible to be modeled with pure ReBAC and you need to resort to using [OpenFGA conditions](https://openfga.dev/docs/modeling/conditions). 

If you can model your attribute as a relation, you should do so, as it will make your model simpler and more efficient.

The example in [store.fga.yaml](./store.fga.yaml) showcases the two examples above.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `abac-with-rebac` directory, run `fga model test --tests store.fga.yaml`


