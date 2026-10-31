# Multi-Tenant RBAC with Tenant Guard

This sample store demonstrates how to enforce **tenant isolation as a model property** in an OpenFGA Role-Based Access Control (RBAC) architecture.

## Use-Case

In a standard multi-tenant RBAC system (such as [multitenant-rbac](../multitenant-rbac)), tenants are modeled as `organization` objects with associated roles, groups, and resources. However, in an unguarded model, OpenFGA evaluates tuples strictly based on graph connectivity: if a mistaken or malicious tuple assigns a role from Tenant A to an organization relation on Tenant B, OpenFGA's `Check` API will honor that relationship. Tenant boundaries in that scenario rely entirely on conventions within the calling application.

This store shows a **tenant-guard** pattern:
Every permission on an organization or tenant-owned resource requires, in addition to the role-derived relation, that the principal be a member of the resource's owning organization (`... and member`). With this guard in place, any accidental or malicious cross-tenant binding grants zero permissions to users outside that tenant.

## How the guard works

- `organization#member` is the tenant roster, and it accepts **direct users only** (`[user]`). Groups and roles never confer tenant membership; they only derive capabilities for principals who are already on the roster. Every capability on an organization (`admin`, `document_manager`, …) is defined as `(<role assignees> or <inherited>) and member`, so a principal must be on the roster *and* hold a role to exercise anything.
- Resources (`document`) derive permissions only `from organization`, so the guard applies to every tenant-owned resource without repeating it per resource type.
- Because the roster is direct-only, removing a user's single `member` tuple revokes all of their access in that organization at once, regardless of roles or groups — a useful property for offboarding. (If the roster also accepted `group#member`, a user could remain a member through a group and this would not hold; that is why the model does not allow it.)
- `role#organization` and `group#organization` record which tenant owns a role or group. They are **not** used in evaluation; they exist so the application can validate relationship writes (see below).

## What this model guarantees

- **Cross-tenant isolation against foreign role bindings**: If a role from Organization A is mistakenly bound to a capability on Organization B (e.g., `role:acme-document-management#assignee` bound to `organization:globex#document_manager`), users who belong only to Organization A are denied (`can_view: false`, `can_edit: false`).
- **Resource permission derivation through tenant chain**: Resources (`document`) derive their permissions strictly through their parent organization relation (`define organization: [organization]`).
- **Scoped admin privileges**: An organization admin in Tenant A holds no administrative or resource permissions in Tenant B.
- **Safe reverse queries**: Reverse queries (`list_objects` and `list_users`) respect the intersection guard:
  - `list_objects` for a user in Tenant A will never return resources belonging to Tenant B, even in the presence of invalid cross-tenant role bindings.
  - `list_users` on Tenant B's resources strictly returns Tenant B users and omits foreign principals.
- **Support for legitimate multi-tenant users**: Users who legitimately hold memberships in multiple organizations can access resources in each organization according to their assigned roles in each tenant, without permission leaking across boundaries.
- **Groups cannot enlarge the roster**: since `member` only accepts `user`, a mis-nested or cross-tenant group (such as the deliberate `group:acme-engineering#member → group:globex-engineering` tuple in the fixtures) can never add anyone to a tenant's membership. The type restriction makes such a tuple unwritable.

## What it does not guarantee and why

While the tenant guard provides defense-in-depth at authorization check time, the OpenFGA Domain Specific Language (DSL) has specific structural boundaries that must be complemented by **write-time validation** when creating relationships:

1. **Role and group tenant affinity cardinality**:
   The model declares `define organization: [organization]` on `role` and `group` to express ownership, but the language cannot enforce cardinality ("exactly one owning organization", "never orphaned").
   * *Enforce when writing relationships*: Reject any role or group creation tuple that lacks a valid owning organization.

2. **Cross-tenant role binding when a user belongs to both tenants**:
   Because the DSL cannot express cross-object attribute unification (i.e., verifying that `role.organization == resource.organization` dynamically during traversal), a user who legitimately belongs to *both* Tenant A and Tenant B could inherit permissions if Tenant A's role is bound to Tenant B's organization.
   * *Enforce when writing relationships*: Validate that when binding `role:X#assignee` to `organization:Y`, `role:X` is owned by `organization:Y`.

3. **Group and role nesting cycles / cross-tenant nesting**:
   OpenFGA's engine tolerates cyclic definitions gracefully (via internal cycle detection), but nesting roles or groups across tenants (e.g., `group:acme-eng#member` inside `group:globex-eng`) violates multi-tenant boundaries.
   * *Enforce when writing relationships*: Enforce a Directed Acyclic Graph (DAG) and reject nesting across different organization scopes at write time.

## Try It Out

1. Ensure the [FGA CLI](https://github.com/openfga/cli#installation) is installed.
2. In this store's directory, run:

```bash
fga model test --tests store.fga.yaml
```

## Related Stores

- [multitenant-rbac](../multitenant-rbac): The baseline multi-tenant RBAC pattern showing dynamic role assignments and nested groups.
- [custom-roles](../custom-roles): Demonstrates custom role definitions scoped to organizations.
