# OpenFGA for Issue Tracking Systems

## Use Case

This model represents the authorization needs of a **Issue Tracking/Project Management system**, like Jira, Linear, Asana. These platforms manage tickets, issues, tasks, collections/projects, comments, and attachments across support and engineering workflows.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own teams, collections, tickets, and contacts.
- **Agent and admin roles**: Organization-level roles (`admin`, `agent`, `member`) control access. Admins have full control, agents can manage tickets and contacts, and members have limited access.
- **Collection hierarchy**: Collections (projects/boards) support parent-child nesting with permission inheritance. Viewers of a parent collection automatically gain visibility into child collections.
- **Ticket access control**: Tickets inherit visibility from their collection. Assignees, creators, reporters, and team members can view tickets. Agents and admins get organization-wide ticket access.
- **Team-based assignment**: Tickets can be assigned to teams. All team members gain edit access, and team leads can close tickets assigned to their team.
- **Comment ownership**: Comment authors can edit and delete their own comments. Other ticket viewers can see comments but not modify them. Admins can delete any comment.
- **Attachment lifecycle**: Attachment visibility follows the parent ticket. Only the uploader or an admin can delete attachments.
- **Contact management**: Only agents and admins can view and manage external contacts (customers, leads), keeping customer data restricted from regular members.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `issue-tracking` directory, run `fga model test --tests store.fga.yaml`
