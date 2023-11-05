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
model
  # We are using the 1.1 schema with type restrictions
  schema 1.1

# There are users
type user

# there are organizations
type organization
  relations
    # Organizations can have users who own them
    define owner: [user]
    # Organizations can have members (any owner of the organization is automatically a member)
    define member: [user] or owner
    # Organizations has a set of base permissions, such as repository admin, writer and reader
    define repo_admin: [user, organization#member]
    define repo_reader: [user, organization#member]
    define repo_writer: [user, organization#member]

# there are teams
type team
  relations
    # teams have members
    define member: [user, team#member]

# there are repositories
type repo
  relations
    # repositories have organizations who own them
    define owner: [organization]
    # repository have admins, they can be assigned or inherited (anyone who has the repository admin role on the owner organization is an owner on the repo)
    define admin: [user, team#member] or repo_admin from owner
    # maintainers on a repo are anyone who is directly assigned or anyone who is an owner on the repo
    define maintainer: [user, team#member] or admin
    # repo writers are users who are directly assigned, anyone who is a maintainer or anyone who has the repository writer role on the owner organization
    define writer: [user, team#member] or maintainer or repo_writer from owner
    # triagers on a repo are anyone who is directly assigned or anyone who is a writer on the repo
    define triager: [user, team#member] or writer
    # repo readers are users who are directly assigned, anyone who is a triager or anyone who has the repository reader role on the owner organization
    define reader: [user, team#member] or triager or repo_reader from owner
```

See the tuples and tests in the [store.yaml](./store.fga.yaml) file.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `github` directory, run `fga model test --tests store.yaml`
