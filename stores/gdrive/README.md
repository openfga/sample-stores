# OpenFGA Google Drive Sample Store

* **Title**: **Google Drive** 
* **Documentation**: https://openfga.dev/docs/modeling/advanced/gdrive
* **Playground**: https://play.fga.dev/sandbox/?store=gdrive

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

> Note: This is only a contrived subset of Google Drive's permissions meant to showcase how a system like Google Drive could be modeled

### Requirements

- There are users, groups, folders and documents
- Users can be members of groups
- Folders have owners
- Folders can have parent folders
- Folders have viewers, a folder's viewers are whoever has been directly granted viewer access, the viewers of the document's parent folders, or the owners of the document
- Users need to have the create file permission on a folder in order to create a file inside it (only the folder owner has this permission)
- Documents have owners
- Documents can have parent folders
- Documents can have viewers
- Only a document's direct owner can change ownership
- A document's owner or owners of the document's parent folder can share the document
- A document's owner or owners of the document's parent folder can write to the document
- The users who can read a document are those with viewer access to the document, those with viewer access to the document's parent folder or the document's owner

### Scenario

- Anne is a member of the Contoso group
- Beth is a member of the Contoso group
- Charles is a member of the Fabrikam group
- The "Product 2021" folder contains the "Public Roadmap" document
- The "Product 2021" folder contains the "2021 Roadmap" document
- Members of the Fabrikam group are viewers of the "Product 2021" folder
- Anne is a viewer of the "Product 2021" folder
- Beth is a viewer of the "2021 Roadmap" document
- Everyone is a viewer of the "Public Roadmap" document

### Expected Outcomes

- Anne can write to the "2021 Roadmap" document
- Beth **cannot** change the owner of the "2021 Roadmap" document
- Charles can read the "2021 Roadmap" document
- Charles **cannot** write to the "2021 Roadmap" document
- Daniel **cannot** read the "2021 Roadmap" document
- Daniel can read the "Public Roadmap" document
- Anne can write to the "Public Roadmap" document
- Charles **cannot** write the "Public Roadmap" document

## Modeling in OpenFGA
### Model

```python
# there are group
type group
  relations
    # a group can have members
    define member as self
# there are folders
type folder
  relations
    # folders can have owners
    define owner as self
    # folders can have parent folders
    define parent as self
    # folders can have viewers; viewers are:
    # - those with whom the folder has been directly shared (self)
    # - those who are ownwers of the folder (owner)
    # - those who are viewers of the parent of the folder (viewer from parent)
    define viewer as self or owner or viewer from parent
    # folders have the create file permissions; only owners can have this permission and it cannot be directly granted (no self)
    define can_create_file as owner
# there are documents
type doc
  relations
    # documents have owners
    define owner as self
    # documents have parent folders
    define parent as self
    # documents have viewers
    define viewer as self
    # documents have the change owner permission; only owners can have this permission and it cannot be directly granted (no self)
    define can_change_owner as owner
    # documents have the share permission; only owners or the owners of the parent folder (owner from parent) have this permissions and it cannot be directly granted (no self)
    define can_share as owner or owner from parent
    # documents have the write permission; only owners or the owners of the parent folder (owner from parent) have this permissions and it cannot be directly granted (no self)
    define can_write as owner or owner from parent
    # documents have the read permission; only direct viewers, direct owners or viewers of the parent folder have this permissions and it cannot be directly granted (no self)
    define can_read as viewer or owner or viewer from parent
```

> Note: The OpenFGA API accepts a JSON syntax for the authorization model that is different from the DSL shown above
>       To switch between the two syntaxes, you can use the [@openfga/syntax-transformer npm package](https://www.npmjs.com/package/@openfga/syntax-transformer) or the [Auth0 FGA Playground](https://play.fga.dev)

You can see a representation of this model in the JSON syntax accepted by the OpenFGA API in [authorization-model.json](./authorization-model.json).

### Tuples

| User                  | Relation | Object              | Description                                                            |
|-----------------------|----------|---------------------|------------------------------------------------------------------------|
| anne                  | member   | group:contoso       | Anne is a member of the Contoso group                                  |
| beth                  | member   | group:contoso       | Beth is a member of the Contoso group                                  |
| charles               | member   | group:fabrikam      | Charles is a member of the Fabrikam group                              |
| folder:product-2021   | parent   | doc:public-roadmap  | The "Product 2021" folder contains the "Public Roadmap" document       |
| folder:product-2021   | parent   | doc:2021-roadmap    | The "Product 2021" folder contains the "2021 Roadmap" document         |
| group:fabrikam#member | viewer   | folder:product-2021 | Members of the Fabrikam group are viewers of the "Product 2021" folder |
| anne                  | owner    | folder:product-2021 | Anne is a viewer of the "Product 2021" folder                          |
| beth                  | viewer   | doc:2021-roadmap    | Beth is a viewer of the "2021 Roadmap" document                        |
| *                     | viewer   | doc:public-roadmap  | Everyone is a viewer of the "Public Roadmap" document                  |

These are represented in this file: [tuples.json](./tuples.json).

### Assertions

| User    | Relation     | Object             | Allowed? |
|---------|--------------|--------------------|----------|
| anne    | write        | doc:2021-roadmap   | Yes      |
| beth    | change_owner | doc:2021-roadmap   | No       |
| charles | read         | doc:2021-roadmap   | Yes      |
| charles | write        | doc:2021-roadmap   | No       |
| daniel  | read         | doc:2021-roadmap   | No       |
| daniel  | read         | doc:public-roadmap | Yes      |
| anne    | write        | doc:public-roadmap | Yes      |
| charles | write        | doc:public-roadmap | No       |

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

3. Navigate into the gdrive sample store
```
cd stores/gdrive
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
    while read body; do
        curl -X POST $FGA_API_URI/stores/$STORE_ID/write -d $body
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
