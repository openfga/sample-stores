# OpenFGA Custom Roles Sample Store

* **Title**: **Custom Roles** 
* **Documentation**: https://openfga.dev/docs/modeling/custom-roles
* **Playground**: https://play.fga.dev/sandbox/?store=custom-roles

## Table of Content
- [Use-Case](#use-case)
  - [Requirements](#requirements)
  - [Scenario](#scenario)
  - [Expected Outcomes](#expected-outcomes)
- [Modeling in OpenFGA](#modeling-in-openfga)
  - [Model](#model)
- [Try It Out](#try-it-out)

## Use-Case

### Requirements

There are users, organizations, teams and assets.

- Assets can be categorized
- Orgs can create their own roles and asset categories

### Scenario

- Carlos is an owner of the Contoso organization
- Anne is a member of the Contoso organization
- Beth is a member of the Contoso organization
- Carlos is a member of the Contoso organization
- Daniel is a member of the Contoso organization
- Edith is a member of the Branding Contractor 1 organization
- Anne is a member of the Design team
- Beth is a member of the Marketing team
- Daniel is a member of the QA team
- The "Website Content" asset category is in the Contoso organization
- The "Website Media" asset category is in the Contoso organization
- Members of the Branding Contractor 1 organization are assigned the "Media Asset Creator" role
- Assignees of the "Content QA" role are asset viewers on the Contoso organization
- Assignees of the "Content Manager" role are can create assets in the "Website Content" asset category
- Assignees of the "Content Manager" role are editors on the "Website Content" asset category
- Assignees of the "Media Asset Manager" role are viewers on the "Website Content" asset category
- Assignees of the "Content QA" role can create assets on the "Website Media" asset category
- Assignees of the "Media Asset Manager" role can create assets on the "Website Media" asset category
- Assignees of the "Media Asset Manager" role are editors on the "Website Media" asset category
- Assignees of the "Content Manager" role are viewers on the "Website Media" asset category
- "Website Content" is the asset category of the "Homepage" asset
- "Website Media" is the asset category of the "Website Hero Image" asset
- Members of the Marketing team are assigned the "Content Manager" role
- Members of the QA team are assigned the "Content QA" role
- Members of the Design team are assigned the "Media Asset Manager" role

### Expected Outcomes

- Carlos is a role creator on the Contoso organization
- Anne can view the "Website Hero Image" asset
- Beth **cannot** edit the "Website Hero Image" asset
- Beth can edit the Homepage asset
- Carlos can edit the Homepage asset
- Daniel **cannot** edit the Homepage asset
- Daniel can view the Homepage asset
- Edith **cannot** view the Homepage asset
- Edith is an asset creator on "Website Media" asset category

## Modeling in OpenFGA

### Model
```python
model
  # We are using the 1.1 schema with type restrictions
  schema 1.1
type user
type team
  relations
    define member: [user]
type role
  relations
    define assignee: [user,team#member,org#member]
type org
  relations
    define asset_category_creator: [role#assignee] or owner
    define asset_commenter: [role#assignee] or asset_editor
    define asset_creator: [role#assignee] or owner
    define asset_editor: [role#assignee] or owner
    define asset_viewer: [role#assignee] or asset_commenter
    define member: [user] or owner
    define owner: [user]
    define role_assigner: [role#assignee] or owner
    define role_creator: [role#assignee] or owner
    define team_assigner: [role#assignee] or owner
    define team_creator: [role#assignee] or owner
type asset-category
  relations
    define asset_creator: [role#assignee] or asset_creator from org
    define commenter: [role#assignee] or editor or asset_commenter from org
    define editor: [role#assignee] or asset_editor from org
    define org: [org]
    define viewer: [role#assignee] or commenter or asset_viewer from org
type asset
  relations
    define category: [asset-category]
    define comment: [role#assignee] or edit or commenter from category
    define edit: [role#assignee] or editor from category
    define view: [role#assignee] or comment or viewer from category
```

See the tuples and tests in the [store.yaml](./store.yaml) file.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `custom-roles` directory, run `fga model test --tests store.yaml`
