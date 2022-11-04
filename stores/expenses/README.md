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
    # employees have managers; an employee's manager is anyone who is their direct manager
    define manager as self
    # employees can be managed by their direct managers as well as whoever can manage their direct managers
    define can_manage as manager or can_manage from manager
# there are reports
type report
  relations
    # reports have submitters; a report's submitter is anyone who has submitted the report
    define submitter as self
    # reports have approvers; a report's approver is anyone who can manage the submitter of the report
    # note that an employee cannot be directly be assigned to be an approver (self is not allowed)
    define approver as can_manage from submitter
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

| User            | Relation   | Object               | Allowed? |
|-----------------|------------|----------------------|----------|
| employee:matt   | can_manage | employee:daniel      | Yes      |
| employee:emily  | approver   | report:daniel-chair1 | Yes      |
| employee:daniel | approver   | report:daniel-chair1 | No       |

These are represented in this file: [assertions.json](./assertions.json).

## Try It Out

Use `expenses` as the SAMPLE_STORE, and follow the rest of the instructions on [Try it out section in the main README](https://github.com/openfga/sample-stores#try-it-out).
