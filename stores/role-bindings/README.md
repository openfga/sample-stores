# Custom Roles with Role Bindings

## Use-Case

This example shows a way to enable end-users to define custom roles that's different from the one described in the [Custom Roles](../custom-roles/README.md) example.

This approach works better when you want to define multiple instances of the same role. For example, a "Project Admin" role, where the permissions for a "Project Admin" are always the same, but the project admins for each project can be different.  

You'll define a `role` type where you'd list all the permissions that the role can have:

```
type role
    relations
    define can_edit_project: [user:*]
    define can_view_project: [user:*]
```

To create a role where users can only edit projects, write one tuple using `user:*` as a flag to enable that permission:

```
- user: user:*
  relation: can_edit_project
  object: role:project-editor
```

Add a `role_binding` type where to bind users to the role:

```
type role_binding
    relations
    define assignee: [user]
    define role: [role]

    define can_edit_project: assignee and can_edit_project from role
    define can_view_project: assignee and can_view_project from role
```

Write tuples to establish both relations for a specific project:

```
- user: user:anne
  relation: assignee
  object: role_binding:project-openfga-editor

- user: role:project-editor  
  relation: assignee
  object: role_binding:project-openfga-editor 
```

Define the `project` type as:

````
type project
  relations
     define role_binding : [role_binding]
     define can_edit_project : can_edit_project from role_binding
     define can_view_project : can_view_project from role_binding
```

And link the `role_binding` to the project:

```
- user: role_binding:project-openfga-editor
  relation: role_binding
  object: project:openfga
```

Check a full example with tests in the [store.yaml](./store.fga.yaml) file.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `role-bindings` directory, run `fga model test --tests store.yaml`
