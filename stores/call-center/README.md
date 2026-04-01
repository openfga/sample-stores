# OpenFGA for Unified Communications Platforms

## Use Case

This model represents the authorization needs of a **call center software platform**, like RingCentral, Zoom Phone, 8x8, or Vonage. These platforms manage calls, contacts, call comments, and recordings across communication teams.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own call logs, contacts, and recordings.
- **Communication roles**: Organization-level roles (`admin`, `supervisor`, `agent`) control access. Admins have full access, supervisors can monitor and manage teams, and agents handle day-to-day communications.
- **Call visibility**: Call participants and agents can view call logs. Only admins can delete call records.
- **Contact management**: Agents can create and edit contacts. Supervisors and admins can delete contacts, providing oversight over the contact directory.
- **Comment authorship**: Comment authors can edit and delete their own call notes. Supervisors can edit any comment for quality assurance. Only authors and admins can delete comments.
- **Recording privacy**: Call recordings are restricted to supervisors and admins for compliance and quality monitoring. Agents cannot access recordings. Only admins can delete recordings.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `call-center` directory, run `fga model test --tests store.fga.yaml`
