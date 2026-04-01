# OpenFGA for Manufacturing Systems

## Use Case

This model represents the authorization needs of a **manufacturing execution system (MES)**, like SAP Manufacturing, Oracle Manufacturing Cloud, or Siemens Opcenter. These platforms manage production lines, machines, work orders, quality reports, parts catalogs, and supplier relationships.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own production infrastructure, parts catalog, and supplier network.
- **Manufacturing roles**: Organization-level roles (`admin`, `plant_manager`, `engineer`, `quality_inspector`, `operator`, `procurement`) control access. Each role has distinct responsibilities — engineers manage equipment, operators run machines, quality inspectors manage reports, and procurement manages the supply chain.
- **Production line management**: Production lines have supervisors and inherit organization roles. Supervisors and plant managers can edit line configuration and create work orders. Only admins can delete lines.
- **Machine access control**: Machines belong to production lines. Assigned operators can edit their machine (log status, report issues). Engineers can edit and delete any machine for maintenance. Supervisors and plant managers inherit edit access from the production line.
- **Operator isolation**: Operators can only edit machines they are explicitly assigned to. They can view all machines on the production line but cannot modify unassigned equipment.
- **Work order lifecycle**: Work orders are produced on a production line. Creators and assignees can edit them. Supervisors and plant managers can approve work orders. Only admins can delete them.
- **Quality reporting**: Quality inspectors can create, edit, and approve quality reports. The inspector who authored a report can edit it. All organization members can view reports, supporting a culture of quality transparency.
- **Parts catalog**: Engineers and procurement staff can edit parts (specifications, stock levels). All organization members can view the catalog.
- **Supplier management**: Only procurement staff and admins can edit supplier records. All members can view the supplier directory.
- **Parent-scoped creation**: Work order creation is checked on the production line (`can_create_work_order`). Part, supplier, and quality report creation are checked on the organization (`can_create_part`, `can_create_supplier`, `can_create_quality_report`).

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `manufacturing` directory, run `fga model test --tests store.fga.yaml`
