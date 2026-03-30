# OpenFGA Sample Stores

This repository contains sample authorization models for [OpenFGA](https://openfga.dev).

## OpenFGA Modeling Guidelines

When authoring, reviewing, or refactoring OpenFGA models, follow the best practices
from the [OpenFGA Best Practices Skill](https://github.com/openfga/agent-skills).

The full modeling guidelines are available at:
https://raw.githubusercontent.com/openfga/agent-skills/main/skills/openfga/AGENTS.md

Key principles:
- Define all relevant entity types, and always declare `schema 1.1`.
- Define relations on resource/object types, not on `user`.
- Keep model and tuple data separate: model defines possible relationships, tuples define actual facts.
- Use explicit direct relations for assignable roles, with clear type restrictions.
- Use concentric inheritance (`or`) so higher-privilege roles imply lower-privilege roles.
- Define permissions as `can_<action>` relations, and check those in application code.
- Do not directly assign users to `can_*` relations; assign roles, then derive permissions from roles.
- For shared role logic across `can_*`, order from most restrictive to least restrictive (for example `can_delete` -> `can_edit` -> `can_view`).
- Use `X from Y` for parent-child inheritance to avoid duplicating tuples and role logic.
- Propagate relevant parent roles to child resources through computed relations when children need them.
- Put create permissions on parent/container types (for example `can_create_<child>`), not on not-yet-existing child objects.
- Use usersets (`type#relation`) for group-based access.
- Use wildcards (`type:*`) only for intentional global/public or boolean-flag-like behavior, and avoid over-broad grants.
- Follow consistent naming: snake_case relations and `can_` permission prefixes.
- Test every model change by running the tests`fga model test --tests store.fga.yaml`.

For the complete set of rules, install the skill:

```
npx skills add openfga/agent-skills
```
