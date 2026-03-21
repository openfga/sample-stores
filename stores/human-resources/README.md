# OpenFGA for HR, Payroll & Directory Systems

## Use Case

This model represents the authorization needs of a **Human Resources Information System (HRIS)**, like BambooHR, Workday or Rippling. These platforms manage employee records, payroll, benefits, time-off, and organizational structure.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own employees, teams, and HR data.
- **Role-based access**: Organization-level roles (`admin`, `hr_manager`, `member`) control who can manage employees, view sensitive data, and approve payroll.
- **Employee self-service**: Employees can view their own records and sensitive fields (SSN, salary), while managers can only see non-sensitive data for their direct reports.
- **Manager hierarchy**: Direct managers can view (but not edit) employee records, while HR managers and admins can edit them.
- **Sensitive data isolation**: Personally identifiable information (SSN, date of birth) is gated behind `can_view_sensitive`, accessible only to the employee themselves and HR administrators.
- **Payroll & benefits**: Only HR managers and admins can view payroll runs and benefits. Only admins can approve payroll.
- **Time-off workflow**: Employees submit requests, designated approvers and HR managers can approve, and only the requester or admin can cancel.
- **Team hierarchy**: Teams support parent-child nesting — members of a parent team can view child teams. Group members and managers can view their groups, and only managers or admins can manage them.

The model, tuples, and tests are in [model.fga](./model.fga) and [model.fga.yaml](./model.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `human-resources` directory, run `fga model test --tests model.fga.yaml`
