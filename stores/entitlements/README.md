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
# There are organizations
type organization
  relations
    # organizations have members
    define member as self
# There are plans
type plan
  relations
    # plans have subscriber organizations
    define subscriber as self
    # any member of an organization subscribed to a plan becomes a "subscriber member"
    define subscriber_member as member from subscriber
# There are features
type feature
  relations
    # features have associated plans
    define associated_plan as self
    # users with access to a feature are those who have "subscriber member" status on the associated plan
    define access as subscriber_member from associated_plan
```

> Note: The OpenFGA API accepts a JSON syntax for the authorization model that is different from the DSL shown above
>       To switch between the two syntaxes, you can use the [@openfga/syntax-transformer npm package](https://www.npmjs.com/package/@openfga/syntax-transformer) or the [Auth0 FGA Playground](https://play.fga.dev)

### Tuples

| User                | Relation        | Object              | Description |
|---------------------|-----------------|---------------------|-------------|
| plan:enterprise     | associated_plan | feature:draft_prs   | aa          |
| plan:team           | associated_plan | feature:draft_prs   | aa          |
| plan:enterprise     | associated_plan | feature:issues      | aa          |
| plan:free           | associated_plan | feature:issues      | aa          |
| plan:team           | associated_plan | feature:issues      | aa          |
| plan:enterprise     | associated_plan | feature:sso         | aa          |
| anne                | member          | organization:alpha  | aa          |
| beth                | member          | organization:brayer | aa          |
| charles             | member          | organization:cups   | aa          |
| organization:cups   | subscriber      | plan:enterprise     | aa          |
| organization:alpha  | subscriber      | plan:free           | aa          |
| organization:brayer | subscriber      | plan:team           | aa          |

### Assertions

| User    | Object            | Relation | Allowed? |
|---------|-------------------|----------|----------|
| anne    | feature:issues    | access   | Yes      |
| anne    | feature:draft_prs | access   | No       |
| anne    | feature:sso       | access   | No       |
| beth    | feature:issues    | access   | Yes      |
| beth    | feature:draft_prs | access   | Yes      |
| beth    | feature:sso       | access   | No       |
| charles | feature:issues    | access   | Yes      |
| charles | feature:draft_prs | access   | Yes      |
| charles | feature:sso       | access   | Yes      |

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

3. Navigate into the entitlements sample store
```
cd stores/entitlements
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
