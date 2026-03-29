# OpenFGA for Knowledge Management Systems

## Use Case

This model represents the authorization needs of a **Knowledge Management System (KMS)**, like Confluence, Notion, or Guru. These platforms manage spaces, pages, and comments to organize team knowledge and documentation.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own knowledge bases, pages, and discussions.
- **Knowledge roles**: Organization-level roles (`admin`, `editor`, `viewer`) control access. Admins have full access, editors can create and modify content, and viewers have read-only access.
- **Space management**: Space owners can edit and manage space membership. Only admins can delete spaces, protecting organizational knowledge.
- **Page authorship**: Page authors can edit and delete their own pages. Editors can modify any page in the organization. Only admins can publish pages, enabling a review workflow.
- **Comment ownership**: Comment authors can edit and delete their own comments. Viewers can read all comments but cannot modify others' comments.
- **Publishing workflow**: Only admins can publish pages, ensuring content quality review before making knowledge articles official.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `kms` directory, run `fga model test --tests store.fga.yaml`
