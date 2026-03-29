# OpenFGA for File Storage Systems

## Use Case

This model represents the authorization needs of a **File Storage platform**, like Google Drive, Box, OneDrive, or SharePoint. These platforms manage drives, folders, files, groups, and sharing permissions across an organization.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own drives, folders, files, and user groups.
- **Drive-level access**: Drives have owners, writers, and readers. Organization members get default read access to shared drives.
- **Folder hierarchy with inheritance**: Folders support parent-child nesting. Permissions cascade from parent folders to child folders and files — granting access to a parent folder automatically grants access to everything inside it.
- **File permissions from folders**: Files inherit reader, writer, and owner permissions from their parent folder, reducing the number of permission tuples needed.
- **Group-based sharing**: Groups (e.g., "Engineering") can be granted read or write access to folders and files using usersets (`group#member`), enabling efficient team-based sharing.
- **Owner propagation**: Folder ownership cascades to child folders and files, so the owner of a top-level folder owns the entire subtree.
- **Granular file operations**: Separate permissions for viewing (`can_view`), editing (`can_edit`), downloading (`can_download`), and deleting (`can_delete`) files.
- **Admin override**: Organization admins can delete any drive, regardless of individual ownership.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `file-storage` directory, run `fga model test --tests store.fga.yaml`
