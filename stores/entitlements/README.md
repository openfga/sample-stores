# OpenFGA Entitlements Sample Store

* **Title**: **Entitlements** 
* **Documentation**: https://openfga.dev/docs/modeling/advanced/entitlements
* **Playground**: https://play.fga.dev/sandbox/?store=entitlements

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

This is a model for entitlements of a service such as GitHub's. It is based it on some plans and features mentioned on [GitHub's pricing page](https://github.com/pricing)

There are users, organizations, plans and features.
- organizations can have members
- organizations can subscribe to plans
- plans grant access to certain features
- if an organization subscribes to a plan, members of that organization get access to the features of that plan

### Scenario

There are three plans, offering some features:
- Free
  - Issues
- Team
  - _Everything from the free plan_
  - Draft Pull Requests
- Enterprise
  - _Everything from the team plan_
  - SAML Single Sign-On

- There are three organizations, each subscribed to a certain plan

  - Alpha Beta Gamma (`alpha`), subscribed to the free plan
  - Bayer Water Supplies (`bayer`), subscribed to the team plan
  - Cups and Dishes (`cups`), subscribed to the enterprise plan

- There are three users, each a member of one of the organizations
  - Anne, a member of Alpha Beta Gamma
  - Beth, a member of Bayer Water Supplies
  - Charles, a member of Cups and Dishes

<img src="https://openfga.dev/assets/images/entitlements-requirements-fdd4048edc4d4b3b78785f4c0671e0b1.svg" style="max-width: 30rem;" alt="Image showing an entitlements scenario">

### Expected Outcomes

- Anne should have access to the Issues feature
- Anne should have access to the Draft Pull Requests feature
- Anne **should not** have access to the Single Sign-on feature
- Beth should have access to the Issues feature
- Beth should have access to the Draft Pull Requests feature
- Beth **should not** have access to the Single Sign-on feature
- Charles should have access to the Issues feature
- Charles should have access to the Draft Pull Requests feature
- Charles should have access to the Single Sign-on feature

## Modeling in OpenFGA

### Model

```python
model
  # We are using the 1.1 schema with type restrictions
  schema 1.1

# There are users
type user

# There are organizations
type organization
  relations
    # organizations have members (who can only be users)
    define member: [user]

# there are subscription plans
type plan
  relations
    # plans have subscriber organizations
    define subscriber: [organization]
    # any member of an organization subscribed to a plan becomes a "subscriber member"
    define subscriber_member: member from subscriber

# There are features
type feature
  relations
    # features have associated plans
    define associated_plan: [plan]
    # users with access to a feature are those who have "subscriber member" status on the associated plan
    define can_access: subscriber_member from associated_plan
```

> Note: The OpenFGA API accepts a JSON syntax for the authorization model that is different from the DSL shown above
>       To switch between the two syntaxes, you can use the [@openfga/syntax-transformer npm package](https://www.npmjs.com/package/@openfga/syntax-transformer) or the [Auth0 FGA Playground](https://play.fga.dev)

### Tuples

| User                | Relation        | Object              | Description                                                   |
|---------------------|-----------------|---------------------|---------------------------------------------------------------|
| plan:enterprise     | associated_plan | feature:draft_prs   | The Enterprise plan grants access to the Draft PRs feature    |
| plan:team           | associated_plan | feature:draft_prs   | The Team plan grants access to the Draft PRs feature          |
| plan:enterprise     | associated_plan | feature:issues      | The Enterprise plan grants access to the Issues feature       |
| plan:free           | associated_plan | feature:issues      | The Free plan grants access to the Issues feature             |
| plan:team           | associated_plan | feature:issues      | The Team plan grants access to the Issues feature             |
| plan:enterprise     | associated_plan | feature:sso         | The Enterprise plan grants access to the SSO feature          |
| user:anne           | member          | organization:alpha  | Anne is a member of the Alpha organization                    |
| user:beth           | member          | organization:brayer | Beth is a member of the Brayer organization                   |
| user:charles        | member          | organization:cups   | Charles is a member of the Cups organization                  |
| organization:cups   | subscriber      | plan:enterprise     | The Cups organization has subscribed to the Enterprise plan   |
| organization:alpha  | subscriber      | plan:free           | The Alpha organization has subscribed to the Enterprise plan  |
| organization:brayer | subscriber      | plan:team           | The Brayer organization has subscribed to the Enterprise plan |

### Assertions

| User         | Object            | Relation   | Allowed? |
|--------------|-------------------|------------|----------|
| user:anne    | feature:issues    | can_access | Yes      |
| user:anne    | feature:draft_prs | can_access | No       |
| user:anne    | feature:sso       | can_access | No       |
| user:beth    | feature:issues    | can_access | Yes      |
| user:beth    | feature:draft_prs | can_access | Yes      |
| user:beth    | feature:sso       | can_access | No       |
| user:charles | feature:issues    | can_access | Yes      |
| user:charles | feature:draft_prs | can_access | Yes      |
| user:charles | feature:sso       | can_access | Yes      |

## Try It Out

Use `entitlements` as the SAMPLE_STORE, and follow the rest of the instructions on [Try it out section in the main README](https://github.com/openfga/sample-stores#try-it-out).
