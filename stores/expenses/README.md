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
model
  # We are using the 1.1 schema with type restrictions
  schema 1.1

# there are employees
type employee
  relations
    # employees have managers; an employee's manager is anyone who is their direct manager
    define manager: [employee]
    # employees can be managed by their direct managers as well as whoever can manage their direct managers
    define can_manage as manager or can_manage from manager

# there are reports
type report
  relations
    # reports have submitters; a report's submitter is anyone who has submitted the report
    define submitter: [employee]
    # reports have approvers; a report's approver is anyone who can manage the submitter of the report
    # note that an employee cannot be directly be assigned to be an approver
    define approver: can_manage from submitter
```

See the tuples and tests in the [store.yaml](./store.yaml) file.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `entitlements` directory, run `fga model test --tests store.yaml`
