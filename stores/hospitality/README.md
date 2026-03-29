# OpenFGA for Hospitality Systems

## Use Case

This model represents the authorization needs of a **hotel management platform**, like Opera PMS, Mews, or Cloudbeds. These platforms manage hotels, rooms, reservations, guest profiles, and guest services across hotel chains and individual properties.

The model captures the following requirements:

- **Multi-tenancy**: A hotel group (organization) manages multiple hotel properties, each with their own staff, rooms, and operations.
- **Two-level role structure**: Organization-level roles (`admin`, `revenue_manager`) control chain-wide access. Hotel-level roles (`general_manager`, `front_desk`, `housekeeping`, `concierge`) control per-property operations. Hotel staff are also organization members for cross-property visibility.
- **Hotel property management**: General managers can edit hotel configuration, create rooms, reservations, and services. Only chain admins can delete hotel properties.
- **Reservation lifecycle**: Front desk staff can create, edit, and cancel reservations. Concierges can create reservations (e.g., for walk-ins) but editing and cancellation is handled by the front desk and management. Only admins can delete reservation records.
- **Room management**: Front desk and housekeeping staff can update room status (available, occupied, cleaning, maintenance). General managers inherit edit access. Only admins can delete rooms.
- **Guest profile protection**: Guest profiles are chain-wide records. All organization members can view guest profiles for cross-property service. Only admins can edit or delete guest records.
- **Service delivery**: Concierges can create and manage hotel services (spa, dining, transport). General managers have broader service management authority. All hotel staff can view available services.
- **Revenue data access**: Revenue managers and general managers can view financial data (ADR, RevPAR, occupancy). Regular staff cannot see revenue metrics.
- **Cross-property visibility**: Organization members can view resources at any hotel in the chain, but can only edit resources at hotels where they have an operational role.
- **Parent-scoped creation**: Room, reservation, and service creation are checked on the hotel (`can_create_room`, `can_create_reservation`, `can_create_service`) rather than on objects that do not exist yet.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `hospitality` directory, run `fga model test --tests store.fga.yaml`
