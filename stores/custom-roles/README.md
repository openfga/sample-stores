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
type org
  relations
    define owner as self
    define member as self or owner
    define asset_creator as self or owner
    define asset_editor as self or owner
    define role_assigner as self or owner
    define role_creator as self or owner
    define team_assigner as self or owner
    define team_creator as self or owner
    define asset_viewer as self or asset_commenter
    define asset_category_creator as self or owner
    define asset_commenter as self or asset_editor
type team
  relations
    define member as self
type role
  relations
    define assignee as self
type asset-category
  relations
    define org as self
    define asset_creator as self or asset_creator from org
    define editor as self or asset_editor from org
    define commenter as self or editor or asset_commenter from org
    define viewer as self or commenter or asset_viewer from org
type asset
  relations
    define category as self
    define edit as self or editor from category
    define comment as self or edit or commenter from category
    define view as self or comment or viewer from category
```

> Note: The OpenFGA API accepts a JSON syntax for the authorization model that is different from the DSL shown above
>       To switch between the two syntaxes, you can use the [@openfga/syntax-transformer npm package](https://www.npmjs.com/package/@openfga/syntax-transformer) or the [Auth0 FGA Playground](https://play.fga.dev)

You can see a representation of this model in the JSON syntax accepted by the OpenFGA API in [authorization-model.json](./authorization-model.json).

### Tuples

| User                              | Relation      | Object                         | Description                                                                                           |
|-----------------------------------|---------------|--------------------------------|-------------------------------------------------------------------------------------------------------|
| carlos                            | owner         | org:contoso                    | Carlos is an owner of the Contoso organization                                                        |
| anne                              | member        | org:contoso                    | Anne is a member of the Contoso organization                                                          |
| beth                              | member        | org:contoso                    | Beth is a member of the Contoso organization                                                          |
| carlos                            | member        | org:contoso                    | Carlos is a member of the Contoso organization                                                        |
| daniel                            | member        | org:contoso                    | Daniel is a member of the Contoso organization                                                        |
| edith                             | member        | org:branding-contractor-1      | Edith is a member of the Branding Contractor 1 organization                                           |
| anne                              | member        | team:design                    | Anne is a member of the Design team                                                                   |
| beth                              | member        | team:marketing                 | Beth is a member of the Marketing team                                                                |
| daniel                            | member        | team:qa                        | Daniel is a member of the QA team                                                                     |
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

| User   | Relation      | Object                       | Allowed? |
|--------|---------------|------------------------------|----------|
| carlos | role_creator  | org:contoso                  | Yes      |
| anne   | view          | asset:website-hero-image     | Yes      |
| beth   | edit          | asset:website-hero-image     | No       |
| beth   | edit          | asset:homepage               | Yes      |
| carlos | edit          | asset:homepage               | Yes      |
| daniel | edit          | asset:homepage               | No       |
| daniel | view          | asset:homepage               | Yes      |
| edith  | view          | asset:homepage               | No       |
| edith  | asset_creator | asset-category:website-media | Yes      |

These are represented in this file: [assertions.json](./assertions.json).

## Try It Out

To try this out, you need the following tools installed:
- `docker`
- `git`
- `curl`
- `jq`

1. Ensure you have [openfga](https://github.com/openfga/openfga) up and running.
```
docker run -p 127.0.0.1:8080:8080 openfga/openfga run
```

2. Clone this repository
```
git clone https://github.com/openfga/sample-stores.git openfga-sample-stores && cd openfga-sample-stores
```

3. Navigate into the custom-roles sample store
```
cd stores/custom-roles
```

4. Loading data into your store
```shell
FGA_API_URI=http://localhost:8080

# Create the store
STORE_ID=$(curl -X POST $FGA_API_URI/stores -d @store.json | jq -r '.id')
# Post the authorization model
AUTHORIZATION_MODEL_ID=$(curl -X POST $FGA_API_URI/stores/$STORE_ID/authorization-models -d @authorization-model.json | jq -r '.authorization_model_id')
# Write the tuples (Note: max tuples per write request = 10)
cat tuples.json | jq -c '. | _nwise(10) | { "writes": { "tuple_keys": . } }' | (
    while read id; do
        curl -X POST $FGA_API_URI/stores/$STORE_ID/write -d $id
    done
)
# Update the assertions for that authorization model
cat assertions.json | jq '. |= { "assertions": .  }' | curl -X PUT $FGA_API_URI/stores/$STORE_ID/assertions/$AUTHORIZATION_MODEL_ID -d @-

# Run sample checks (here we are just looping through our assertions and issuing checks for them)
cat assertions.json | jq -c '. | _nwise(1) | { "tuple_key": { "user": .[0].tuple_key.user, "relation": .[0].tuple_key.relation, "object": .[0].tuple_key.object } }' | (
    while read body; do
        FGA_ALLOWED=$(curl -s -X POST $FGA_API_URI/stores/$STORE_ID/check -d $body | jq '.allowed')
        FGA_USER=$(echo $body | jq -rc '.tuple_key.user')
        FGA_RELATION=$(echo $body | jq -rc '.tuple_key.relation')
        FGA_OBJECT=$(echo $body | jq -rc '.tuple_key.object')
        echo "check(user=$FGA_USER, relation=$FGA_RELATION, object=$FGA_OBJECT); allowed: $FGA_ALLOWED"
    done
)
```
