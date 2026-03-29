# OpenFGA for Calendar Platforms

## Use Case

This model represents the authorization needs of a **Calendar platform**, like Google Calendar, Microsoft Outlook, or Calendly. These platforms manage calendars, events, scheduling links, recordings, and webinars across organizations.

The model captures the following requirements:

- **Multi-tenancy**: Multiple organizations, each with their own calendars, events, and scheduling resources.
- **Calendar roles**: Organization-level roles (`admin`, `scheduler`, `viewer`) control access. Admins have full access, schedulers can create and manage events, and viewers have read-only access.
- **Calendar ownership**: Calendar owners can view, edit, and share their calendars. Only admins can delete calendars.
- **Event management**: Event organizers can edit, delete, and invite attendees. Schedulers can manage events across the organization. Attendees get view access to events they are invited to.
- **Scheduling links**: Link owners and schedulers can manage booking pages. Viewers cannot access scheduling links.
- **Recording access**: Recordings are viewable by organization viewers, schedulers, and admins. Only admins can delete recordings.
- **Webinar publishing**: Organizers and admins can publish webinars. Viewers can see webinars but cannot modify or publish them.

The model, tuples, and tests are in [model.fga](./model.fga) and [store.fga.yaml](./store.fga.yaml).

## Try It Out

1. Make sure you have the [FGA CLI](https://github.com/openfga/cli/?tab=readme-ov-file#installation)

2. In the `calendar` directory, run `fga model test --tests store.fga.yaml`
