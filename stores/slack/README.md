# OpenFGA Slack Sample Store

* **Title**: **Slack** 
* **Documentation**: https://openfga.dev/docs/modeling/advanced/slack
* **Playground**: https://play.fga.dev/sandbox/?store=slack

## Table of Content
- [Use-Case](#use-case)
  - [Requirements](#requirements)
  - [Scenario](#scenario)
  - [Expected Outcomes](#expected-outcomes)
- [Modeling in OpenFGA](#modeling-in-openfga)
  - [Model](#model)
- [Try It Out](#try-it-out)

## Use-Case

### Requirements

This model is inspired by Slack and some of their publicly documented roles and permissions.

> As reference, Slack's publicly available docs were used:
>
> - [Role Management at Slack](https://slack.engineering/role-management-at-slack/)
> - [Types of Roles in Slack](https://slack.com/intl/en-ca/help/articles/360018112273-Types-of-roles-in-Slack)
> - [Permissions by Role in Slack](https://slack.com/intl/en-ca/help/articles/201314026-Permissions-by-role-in-Slack)
> - [Manage a Workspace on Enterprise Grid](https://slack.com/intl/en-ca/help/articles/115005225987-Manage-a-workspace-on-Enterprise-Grid)
> - [Manage channel posting permissions](https://slack.com/intl/en-ca/help/articles/360004635551-Manage-channel-posting-permissions-)


Workspace Roles:

- **Guest**: This type of user is limited in their ability to use Slack, and is only permitted to see one or multiple delegated channels.
- **Member**: This is the base type of user that does not have any particular administrative abilities, but has basic access to the organization's Slack workspaces. When an administrative change needs to be made, these users need the support of admins and owners to make the changes.
- **Legacy Admin**: This type of user is the basic administrator of any organization, and can make a wide variety of administrative changes across Slack, such as renaming channels, archiving channels, setting up preferences and policies, inviting new users, and installing applications. Users with this role perform the majority of administrative tasks across a team.

System Roles:

- **Channels Admin**: This type of user has the permission to archive channels, rename channels, create private channels, and convert public channels into private channels.

Channel Settings:

- **Visibility**:
  - **Public**: Visible to all members and open to join
  - **Private**: Visible to admins and invited members
- [**Posting Permissions**](https://slack.com/intl/en-ca/help/articles/360004635551-Manage-channel-posting-permissions-):
  - **Open**: Anyone can post
  - **Limited**: Only allowed members can post


### Scenario

There are users, workspaces and channels.
- There are five users: Amy, Bob, Catherine, David, Emily
- There is a workspace called Sandcastle. It has three channels: #general, #marketing_internal and #proj_marketing_campaign
- #proj_marketing_campaign channel is a public channel, anyone who is a member of the Sandcastle workspace can write in it
- #general channel is a public channel, anyone who is a member of the Sandcastle workspace can view it
- In the Sandcastle workspace:
  - Amy has a legacy admin role
  - Bob has a channels admin role
  - Catherine and Emily are members
  - David is a guest
- Catherine and Emily are normal members of the Sandcastle workspace, they can view all public channels, as well as channels they have been invited to
- David is a guest user with only view and write access to #proj_marketing_campaign (one of the public channels)
- Bob and Emily are in a private channel #marketing_internal, which only they can view and post to
- All members of the Sandcastle workspace can view the #general channel, but only Amy and Emily can post to it

### Expected Outcomes

- Amy should be a channels admin  on the Sandcastle workspace
- Amy should **not** be a channels admin  on the Sandcastle workspace
- Emily should be a writer on the #marketing_internal channel
- David should **not** be a writer on the #marketing_internal channel
- David should be a writer on the #proj_marketing_campaign channel
- Bob should **not** be a writer on the #general channel

## Modeling in OpenFGA

### Model

```python
model
  # We are using the 1.1 schema with type restrictions
  schema 1.1

# There are users
type user

# There are workspaces
type workspace
  relations
    # users can have the legacy admin role on workspaces
    define legacy_admin: [user]
    # users can have the new channels admin role on workspaces (anyone with a legacy admin role also gets all the channels admin role access) 
    define channels_admin: [user] or legacy_admin
    # users can have the member role on workspaces, admins automatically get all access granted to members
    define member: [user] or legacy_admin or channels_admin
    # users can have the guest role on workspaces
    define guest: [user]

# There are channels
type channel
  relations
    # channels have a parent workspace
    define parent_workspace: [workspace]
    # users can be writers on channels
    define writer: [user,workspace#member]
    # users can be viewers on channels, writers inherit any access viewers have
    define commenter: [user,workspace#member] or writer
```

See the tuples and tests in the [store.yaml](./store.yaml) file.

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `slack` directory, run `fga model test --tests store.yaml`
