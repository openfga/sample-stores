# OpenFGA Expenses Sample Store

* **Title**: **Expenses** 
* **Documentation**:
* **Playground**: https://play.fga.dev/sandbox/?store=expenses

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

There is a system in which you have:

- employees and reports
- an employee can be another employee's manager
- an employee's managers are their direct managers and anyone who is a manager of one of their managers (all the way up the chain)
- employees can submit reports
- employees can approve reports, the only employees who can approve a report are those who are managers of the employee that submitted the report

### Scenario

- There are four employees: Daniel, Matt, Sam, and Emily
- Matt is Daniel's manager
- Sam is Matt's manager
- Emily is Sam's manager
- Daniel has submitted a report for Daniel's chair
- Sam has submitted a report for Sam's chair

### Expected Outcomes

- Matt is Daniel's manager
- Emily can approve the report for Daniel's chair
- Daniel cannot approve the report for Daniel's chair

## Modeling in OpenFGA

For more on modeling, check out:
- [OpenFGA Configuration Language](https://openfga.dev/docs/configuration-language)
- [OpenFGA Modeling Guides](https://openfga.dev/docs/modeling)

### Model
```python
# there are employees
type employee
  relations
    # employees have managers; an employee's manager is anyone who is their direct manager as well as their managers' managers
    define manager as self or manager from manager
# there are reports
type report
  relations
    # reports have submitters; a report's submitter is anyone who has submitted the report
    define submitter as self
    # reports have approvers; a report's approver is anyone who's a manager of a submitter
    # note that an employee cannot be directly be assigned to be an approver (self is not allowed)
    define approver as manager from submitter
```

> Note: The OpenFGA API accepts a JSON syntax for the authorization model that is different from the DSL shown above
>       To switch between the two syntaxes, you can use the [@openfga/syntax-transformer npm package](https://www.npmjs.com/package/@openfga/syntax-transformer) or the [Auth0 FGA Playground](https://play.fga.dev)

You can see a representation of this model in the JSON syntax accepted by the OpenFGA API in [authorization-model.json](./authorization-model.json).

### Tuples

| User            | Relation  | Object               | Description                                    |
|-----------------|-----------|----------------------|------------------------------------------------|
| employee:matt   | manager   | employee:daniel      | Matt is Daniel's manager                       |
| employee:sam    | manager   | employee:matt        | Sam is Matt's manager                          |
| employee:emily  | manager   | employee:sam         | Emily is Sam's manager                         |
| employee:daniel | submitter | report:daniel-chair1 | Daniel has submitted the "Daniel Chair" report |
| employee:sam    | submitter | report:sam-chair1    | Sam has submitted the "Sam Chair" report       |

These are represented in this file: [tuples.json](./tuples.json).

### Assertions

| User            | Relation | Object               | Allowed? |
|-----------------|----------|----------------------|----------|
| employee:matt   | manager  | employee:daniel      | Yes      |
| employee:emily  | approver | report:daniel-chair1 | Yes      |
| employee:daniel | approver | report:daniel-chair1 | No       |

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

3. Navigate into the expenses sample store
```
cd stores/expenses
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
