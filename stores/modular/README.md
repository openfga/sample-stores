# OpenFGA Modular Model Sample Store

## Use-Case

This example showcases how to use [modular models](https://openfga.dev/docs/modeling/modular-models) to organize your model across multiple files.

It models an application with three modules:

- A `core` [module](./core.fga) defines the entity types that are shared across components.
- A `wiki` [module](./wiki.fga) defines the entity types for the wiki component (e.g. spaces and pages).
- A `issue tracker` module defines the entity types for the issue tracking component. This module is defined in 2 separate files, one for [projects](./issue-tracker/projects.fga) and another for [tickets](./issue-tracker/tickets.fga)

An `fga.mod` [manifest file](./fga.mod) declares all the modules for the model. The model can be written to an OpenFGA store with the `fga model write --file fga.mod --store-id <store_id>` CLI command.

If you then try to get the model with the `fga model get --format fga --store-id <store_id>` command, OpenFGA will be displayed it as a single combined model, with annotations describing the module that defined each type:

```dsl
model
  schema 1.2

type group # module: core, file: core.fga
  relations
    define member: [user]

type organization # module: core, file: core.fga
  relations
    define admin: [user]
    define member: [user] or admin
    define can_create_project: admin # extended by: module: issue-tracker, file: issue-tracker/projects.fga
    define can_create_space: admin # extended by: module: wiki, file: wiki.fga

type user # module: core, file: core.fga

type project # module: issue-tracker, file: issue-tracker/projects.fga
  relations
    define organization: [organization]
    define viewer: member from organization

type ticket # module: issue-tracker, file: issue-tracker/tickets.fga
  relations
    define owner: [user]
    define project: [project]

type page # module: wiki, file: wiki.fga
  relations
    define owner: [user]
    define space: [space]

type space # module: wiki, file: wiki.fga
  relations
    define can_view_pages: member from organization
    define organization: [organization]
```

The model, tuples and unit tests are detailed in [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `modular` directory, run `fga model test --tests store.fga.yaml`
