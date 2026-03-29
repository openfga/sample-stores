# OpenFGA for Healthcare Systems

## Use Case

This model represents the authorization needs of a **healthcare platform**, like Epic, Cerner, or Athenahealth. These platforms manage patient records, clinical encounters, diagnoses, treatments, medications, and provider networks across healthcare organizations.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own patients, providers, facilities, and clinical data.
- **Healthcare roles**: Organization-level roles (`admin`, `provider`, `nurse`, `medical_records_staff`) control access. Admins have full access, providers manage clinical care, nurses support care delivery, and medical records staff handle administrative record-keeping.
- **Patient record protection**: Patient records are only accessible to the primary provider, care team members, medical records staff, and admins. Providers not involved in a patient's care cannot view their records, enforcing minimum necessary access.
- **Sensitive data isolation**: Protected health information (allergies, blood type, date of birth) is gated behind `can_view_sensitive`, accessible to the primary provider, care team, medical records staff, and admins.
- **Care team access**: Nurses and other staff on a patient's care team can view records and sensitive data, and create encounters, but cannot edit patient records or order treatments.
- **Encounter management**: Encounters are linked to both patients and facilities. Attending providers can edit encounters and create treatment orders. Patient viewers inherit read access to all encounters.
- **Diagnosis and treatment chains**: Diagnoses inherit visibility from the patient. Treatments inherit visibility from the encounter. Diagnosing and ordering providers can edit their respective records.
- **Facility management**: Facility directors can edit facility information. All organization members can view facilities. Only admins can delete them.
- **Medication formulary**: The medication catalog is viewable by providers and nurses for clinical reference. Only admins can modify the formulary.
- **Parent-scoped creation**: Encounter creation is checked on the patient (`can_create_encounter`), and treatment creation is checked on the encounter (`can_create_treatment`), rather than on objects that do not exist yet.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `healthcare` directory, run `fga model test --tests store.fga.yaml`
