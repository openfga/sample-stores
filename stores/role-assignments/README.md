# Custom Roles with Role Assignments

## Use Case

This example shows an alternative method for defining custom roles that differs from the one described in the [Custom Roles](../custom-roles/README.md) example.

This approach demonstrates how to create a reusable custom role, like a "Project Admin", that can be used across multiple instances and different resources. For example, the "Project Admin" role can apply to all projects in a company, but each project can have its own distinct set of admins.

You'll define a `role` type where you list all the permissions that the role can have:

```
type role
  relations
    define can_view_project: [user:*]
    define can_edit_project: [user:*]
```

To create a Project Admin role where users can view and edit projects, write two tuples using `user:*` as a flag to enable that permission:

```
- user: user:*
  relation: can_view_project
  object: role:project-admin  

- user: user:*
  relation: can_edit_project
  object: role:project-admin
```

Add a `role_assignment` type to assign users to the role:

```
type role_assignment
  relations
    define assignee: [user]
    define role: [role]

    define can_view_project: assignee and can_view_project from role
    define can_edit_project: assignee and can_edit_project from role
```

Write tuples to establish both relations for a specific project:

```
- user: user:anne
  relation: assignee
  object: role_assignment:project-admin-openfga

- user: role:project-admin  
  relation: role
  object: role_assignment:project-admin-openfga
```

Define the `project` type as:

```
type project
  relations
     define role_assignment : [role_assignment]
     define can_edit_project : can_edit_project from role_assignment
     define can_view_project : can_view_project from role_assignment
```

And link the `role_assignment` to the project:

```
- user: role_assignment:project-admin-openfga
  relation: role_assignment
  object: project:openfga
```

The [store.fga.yaml](./store.fga.yaml) file has a full example of this scenario for a multi-tenant use case.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation) installed.

2. In the `role-assignments` directory, run `fga model test --tests store.yaml`
