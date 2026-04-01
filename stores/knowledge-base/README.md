# OpenFGA for Knowledge Base Systems

## Use Case

This model represents the authorization needs of a **knowledge base/wiki platform**, like Confluence or Notion. These platforms manage articles, pages, containers (spaces, folders, databases), groups, and access permissions for documentation and knowledge sharing.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own knowledge base spaces, articles, and user groups.
- **Container hierarchy**: Containers (spaces, folders, sections, categories, databases) support parent-child nesting with permission inheritance. Granting access to a parent space cascades to all nested containers and articles.
- **Article visibility inheritance**: Articles inherit view and edit permissions from their parent container. Authors get additional publish, archive, and delete rights.
- **Public content**: Articles can be made public using wildcards (`user:*`), enabling unauthenticated or organization-wide read access for FAQ pages, public documentation, or help center content.
- **Group-based access**: Groups can be granted editor or viewer access to containers and articles, enabling team-based knowledge management (e.g., "docs-team" can edit the engineering space).
- **Author ownership**: Article authors can publish, archive, and delete their own articles. Organization admins can also perform these actions on any article.
- **Attachment lifecycle**: Attachments follow the visibility of their parent article. Only the uploader or an admin can delete attachments.
- **Organization-wide readability**: All organization members can view containers and articles by default, supporting a culture of knowledge sharing. Editing is restricted to designated editors, groups, and admins.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `knowledge-base` directory, run `fga model test --tests store.fga.yaml`
