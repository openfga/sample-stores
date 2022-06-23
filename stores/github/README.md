# OpenFGA GitHub Sample Store

* **Title**: **GitHub** 
* **Documentation**: https://openfga.dev/docs/modeling/advanced/github
* **Playground**: https://play.fga.dev/sandbox/?store=github

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

This model is based on GitHub's permission model according to their [documentation](https://docs.github.com/en/organizations/managing-access-to-your-organizations-repositories/repository-roles-for-an-organization#repository-roles-for-organizations).

* Users can be admins, maintainers, writers, triagers or readers of repositories (each level inherits all access of the level lower than it. e.g. admins inherit maintainer access and so forth)
* Teams can have members
* Organizations can have members
* Organizations can own repositories
* Users can have repository admin access on organizations, and thus have admin access to all repositories owned by that organization

### Scenario

There are users, organizations, teams and repositories

- There are five users: Anne, Beth, Charles, Diane and Erik
- There is an OpenFGA organization that owns the openfga/openfga repository
- There is an openfga/core team and a openfga/backend team
- Members of the openfga/backend team are members of the openfga/core team
- Members of the openfga/core team are admins of the openfga/openfga repository
- Erik is a member of the OpenFGA organization
- Diane is a member of the openfga/backend team
- Charles is a member of the openfga/core team
- Anne is a reader on the openfga/openfga repository
- Beth is a writer on the openfga/openfga repository
- The OpenFGA organization has been configured with the "repository admin" base permission, which means all the organization members have the admin role on all the repositories the organization owns 

### Expected Outcomes

- Anne is a reader on the openfga/openfga repository
- Anne **is not** a triager on the openfga/openfga repository
- Diane is an admin on the openfga/openfga repository
- Erik is a reader on the openfga/openfga repository
- Charles is a writer on the openfga/openfga repository
- Beth **is not** an admin on the openfga/openfga repository

## Modeling in OpenFGA

### Model

```python
# there are organizations
type org
  relations
    # Organizations can have users who own them
    define owner as self
    # Organizations can have members (any owner of the org is automatically a member)
    define member as self or owner
    # Organizations has a set of permissions, such as repository admin, writer and reader
    define repo_admin as self
    define repo_reader as self
    define repo_writer as self
# there are teams
type team
  relations
    # teams have members
    define member as self
# there are repositories
type repo
  relations
    # repositories have organizations who own them
    define owner as self
    # repository have admins, they can be assigned or inherited (anyone who has the repository admin role on the owner org is an owner on the repo)
    define admin as self or repo_admin from owner
    # maintainers on a repo are anyone who is directly assigned or anyone who is an owner on the repo
    define maintainer as self or admin
    # repo writers are users who are directly assigned, anyone who is a maintainer or anyone who has the repository writer role on the owner org
    define writer as self or maintainer or repo_writer from own
    # triagers on a repo are anyone who is directly assigned or anyone who is a writer on the repo
    define triager as self or writerer
    # repo readers are users who are directly assigned, anyone who is a triafer or anyone who has the repository reader role on the owner org
    define reader as self or triager or repo_reader from owner
```

> Note: The OpenFGA API accepts a JSON syntax for the authorization model that is different from the DSL shown above
>       To switch between the two syntaxes, you can use the [@openfga/syntax-transformer npm package](https://www.npmjs.com/package/@openfga/syntax-transformer) or the [Auth0 FGA Playground](https://play.fga.dev)

You can see a representation of this model in the JSON syntax accepted by the OpenFGA API in [authorization-model.json](./authorization-model.json).

### Tuples

| User                        | Relation   | Object               | Description                                                                                     |
|-----------------------------|------------|----------------------|-------------------------------------------------------------------------------------------------|
| org:openfga                 | owner      | repo:openfga/openfga | The OpenFGA organization is the owner of the openfga/openfga repository                         |
| org:openfga#member          | repo_admin | org:openfga          | Members of the OpenFGA organization have a repository admin base permission on the organization |
| erik                        | member     | org:openfga          | Erik is a member of the OpenFGA organization                                                    |
| team:openfga/core#member    | admin      | repo:openfga/openfga | The openfga/core tean members are admins on the openfga/openfga repository                      |
| anne                        | reader     | repo:openfga/openfga | Anne is a reader on the on the openfga/openfga repository                                       |
| beth                        | writer     | repo:openfga/openfga | Beth is a writer on the openfga/openfga repository                                              |
| charles                     | member     | team:openfga/core    | Charles is a member of the openfga/core team                                                    |
| team:openfga/backend#member | member     | team:openfga/core    | Members of the openfga/backend team are members of the openfga/core team                        |
| diane                       | member     | team:openfga/backend | Diane is a member of the openfga/backend team                                                   |

These are represented in this file: [tuples.json](./tuples.json).

### Assertions

| User    | Relation | Object               | Allowed? |
|---------|----------|----------------------|----------|
| anne    | reader   | repo:openfga/openfga | Yes      |
| anne    | triager  | repo:openfga/openfga | No       |
| diane   | admin    | repo:openfga/openfga | Yes      |
| erik    | reader   | repo:openfga/openfga | Yes      |
| charles | writer   | repo:openfga/openfga | Yes      |
| beth    | admin    | repo:openfga/openfga | No       |

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

3. Navigate into the github sample store
```
cd stores/github
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
