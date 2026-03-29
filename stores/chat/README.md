# OpenFGA for Chat & Messaging Systems

## Use Case

This model represents the authorization needs of a **Chat / Messaging platform**, like Slack, Microsoft Teams, Discord, or Google Chat. These platforms manage conversations (channels, DMs, group chats), messages, threads, groups, and membership.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own conversations, users, and groups.
- **Conversation membership**: Only conversation members can view messages and post new ones. Non-members cannot see the conversation or its content.
- **Group-based channels**: Conversations can grant membership to entire groups using usersets (`group#member`), so adding a user to the "Engineering" group automatically gives them access to the `#engineering-general` channel.
- **Direct messages**: DMs are modeled as conversations with individual user members, ensuring only participants can see the conversation.
- **Channel ownership**: Channel owners can edit channel settings, delete the channel, and manage membership (add/remove members). Regular members can only view and post.
- **Message authorship**: Message senders can edit and delete their own messages. Other conversation members can view and reply to messages but cannot edit them.
- **Thread replies**: Reply permissions follow conversation membership — anyone who can view the conversation can reply to messages within it.
- **Admin override**: Organization admins can view, edit, and delete any conversation or message, and manage membership across all channels, supporting moderation and compliance needs.
- **Non-member isolation**: Users who are not members of a conversation cannot view it, post in it, or see any of its messages — enforcing strict channel privacy.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `chat` directory, run `fga model test --tests store.fga.yaml`
