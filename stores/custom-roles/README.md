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
  - [Tuples](#tuples)
  - [Assertions](#assertions)
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
    define assignee: [user,team,org]
type org
  relations
    define asset_category_creator: [role] or owner
    define asset_commenter: [role] or asset_editor
    define asset_creator: [role] or owner
    define asset_editor: [role] or owner
    define asset_viewer: [role] or asset_commenter
    define member: [user] or owner
    define owner: [user]
    define role_assigner: [role] or owner
    define role_creator: [role] or owner
    define team_assigner: [role] or owner
    define team_creator: [role] or owner
type asset-category
  relations
    define asset_creator: [role] or asset_creator from org
    define commenter: [role] or editor or asset_commenter from org
    define editor: [role] or asset_editor from org
    define org: [org]
    define viewer: [role] or commenter or asset_viewer from org
type asset
  relations
    define category: [asset-category]
    define comment: [role] or edit or commenter from category
    define edit: [role] or editor from category
    define view: [role] or comment or viewer from category
```

> Note: The OpenFGA API accepts a JSON syntax for the authorization model that is different from the DSL shown above
>       To switch between the two syntaxes, you can use the [@openfga/syntax-transformer npm package](https://www.npmjs.com/package/@openfga/syntax-transformer) or the [Auth0 FGA Playground](https://play.fga.dev)

You can see a representation of this model in the JSON syntax accepted by the OpenFGA API in [authorization-model.json](./authorization-model.json).

### Tuples

| User                              | Relation      | Object                         | Description                                                                                           |
|-----------------------------------|---------------|--------------------------------|-------------------------------------------------------------------------------------------------------|
| user:carlos                       | owner         | org:contoso                    | Carlos is an owner of the Contoso organization                                                        |
| user:anne                         | member        | org:contoso                    | Anne is a member of the Contoso organization                                                          |
| user:beth                         | member        | org:contoso                    | Beth is a member of the Contoso organization                                                          |
| user:carlos                       | member        | org:contoso                    | Carlos is a member of the Contoso organization                                                        |
| user:daniel                       | member        | org:contoso                    | Daniel is a member of the Contoso organization                                                        |
| user:edith                        | member        | org:branding-contractor-1      | Edith is a member of the Branding Contractor 1 organization                                           |
| user:anne                         | member        | team:design                    | Anne is a member of the Design team                                                                   |
| user:beth                         | member        | team:marketing                 | Beth is a member of the Marketing team                                                                |
| user:daniel                       | member        | team:qa                        | Daniel is a member of the QA team                                                                     |
| org:contoso                       | org           | asset-category:website-content | The "Website Content" asset category is in the Contoso organization                                   |
| org:contoso                       | org           | asset-category:website-media   | The "Website Media" asset category is in the Contoso organization                                     |
| org:branding-contractor-1#member  | assignee      | role:media-asset-creator       | Members of the Branding Contractor 1 organization are assigned the "Media Asset Creator" role         |
| role:content-qa#assignee          | asset_viewer  | org:contoso                    | Assignees of the "Content QA" role are asset viewers on the Contoso organization                      |
| role:content-manager#assignee     | asset_creator | asset-category:website-content | Assignees of the "Content Manager" role are can create assets in the "Website Content" asset category |
| role:content-manager#assignee     | editor        | asset-category:website-content | Assignees of the "Content Manager" role are editors on the "Website Content" asset category           |
| role:media-asset-manager#assignee | viewer        | asset-category:website-content | Assignees of the "Media Asset Manager" role are viewers on the "Website Content" asset category       |
| role:media-asset-creator#assignee | asset_creator | asset-category:website-media   | Assignees of the "Content QA" role can create assets on the "Website Media" asset category            |
| role:media-asset-manager#assignee | asset_creator | asset-category:website-media   | Assignees of the "Media Asset Manager" role can create assets on the "Website Media" asset category   |
| role:media-asset-manager#assignee | editor        | asset-category:website-media   | Assignees of the "Media Asset Manager" role are editors on the "Website Media" asset category         |
| role:content-manager#assignee     | viewer        | asset-category:website-media   | Assignees of the "Content Manager" role are viewers on the "Website Media" asset category             |
| asset-category:website-content    | category      | asset:homepage                 | "Website Content" is the asset category of the "Homepage" asset                                       |
| asset-category:website-media      | category      | asset:website-hero-image       | "Website Media" is the asset category of the "Website Hero Image" asset                               |
| team:marketing#member             | assignee      | role:content-manager           | Members of the Marketing team are assigned the "Content Manager" role                                 |
| team:qa#member                    | assignee      | role:content-qa                | Members of the QA team are assigned the "Content QA" role                                             |
| team:design#member                | assignee      | role:media-asset-manager       | Members of the Design team are assigned the "Media Asset Manager" role                                |

These are represented in this file: [tuples.json](./tuples.json).

### Assertions

| User        | Relation      | Object                       | Allowed? |
|-------------|---------------|------------------------------|----------|
| user:carlos | role_creator  | org:contoso                  | Yes      |
| user:anne   | view          | asset:website-hero-image     | Yes      |
| user:beth   | edit          | asset:website-hero-image     | No       |
| user:beth   | edit          | asset:homepage               | Yes      |
| user:carlos | edit          | asset:homepage               | Yes      |
| user:daniel | edit          | asset:homepage               | No       |
| user:daniel | view          | asset:homepage               | Yes      |
| user:edith  | view          | asset:homepage               | No       |
| user:edith  | asset_creator | asset-category:website-media | Yes      |

These are represented in this file: [assertions.json](./assertions.json).

## Try It Out

Use `custom-roles` as the SAMPLE_STORE, and follow the rest of the instructions on [Try it out section in the main README](https://github.com/openfga/sample-stores#try-it-out).
