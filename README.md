# OpenFGA Sample Stores

[![Join our community](https://img.shields.io/badge/slack-cncf_%23openfga-40abb8.svg?logo=slack)](https://openfga.dev/community)
[![X](https://img.shields.io/twitter/follow/openfga?style=flat-square&label=%40openfga%20on%20X&color=23179CF0)](https://x.com/openfga)

This repository contains sample store models for [OpenFGA](https://openfga.dev).

## Table of Contents

- [Authorization Patterns](#authorization-patterns)
- [OpenFGA Features](#openfga-features)
- [Industry Examples](#industry-examples)
- [OpenFGA Models in Open Source Projects](#openfga-models-in-open-source-projects)
- [Authoring Models using Coding Assistants](#authoring-models-using-coding-assistants)
- [Creating your store and loading sample data](#creating-your-store-and-loading-sample-data)
- [Modeling Resources](#modeling-resources)

## Authorization Patterns

Examples that demonstrate how to model authorization for well-known products and common patterns.

| Example | Description |
|---------|-------------|
| [Modeling Guide](./stores/modeling-guide) | Step-by-step introduction to OpenFGA modeling |
| [Multi-Tenant RBAC](./stores/multitenant-rbac) | Multi-tenant role-based access control |
| [GitHub](./stores/github) | Repository, organization, and team permissions |
| [Google Drive](./stores/gdrive) | File, folder, and shared drive permissions |
| [Entitlements](./stores/entitlements) | Feature entitlements and plan-based access |
| [Slack](./stores/slack) | Workspace, channel, and messaging permissions |
| [Role Assignments](./stores/role-assignments) | Role assignment patterns for resource-specific roles |
| [Temporal Access](./stores/temporal-access) | Time-limited access grants with expiration |
| [Super-Admin](./stores/superadmin) | Super-admin override patterns |

## OpenFGA Features

Examples that demonstrate specific OpenFGA features and capabilities.

| Example | Description |
|---------|-------------|
| [Modeling ABAC with ReBAC](./stores/abac-with-rebac) | Combine attribute-based and relationship-based access control |
| [Condition Data Types](./stores/condition-data-types) | CEL condition expressions with different data types |
| [Custom Roles](./stores/custom-roles) | User-defined roles with dynamic permission assignment |
| [Advanced Entitlements](./stores/advanced-entitlements) | Complex entitlement and feature-gating patterns |
| [Group Resource Attributes](./stores/groups-resource-attributes) | Group-based access with resource attributes |
| [IP-Based Access](./stores/ip-based-access) | Network-based access restrictions using conditions |
| [Modular Models](./stores/modular) | Splitting models into modules for team collaboration |

## Industry Examples

Full authorization models for different verticals and industries. Each includes a model, sample tuples, tests, and a README explaining the use case.

| Example | Description |
|---------|-------------|
| [Accounting](./stores/accounting) | Charts of accounts, invoices, expenses, payments, journal entries |
| [Advertising](./stores/ads) | Campaigns, ad groups, ads, creatives, reports |
| [Applicant Tracking](./stores/applicant-tracking-system) | Jobs, candidates, applications, interviews, offers |
| [Banking](./stores/banking) | Accounts, transactions, and financial operations |
| [Calendar](./stores/calendar) | Calendars, events, scheduling links, recordings, webinars |
| [Call Center](./stores/call-center) | Calls, contacts, comments, recordings |
| [Chat & Messaging](./stores/chat) | Conversations, messages, groups, membership |
| [CRM](./stores/crm) | Accounts, contacts, leads, opportunities, pipeline |
| [Developer Portal](./stores/developer-portal) | API keys, applications, and developer access |
| [E-Commerce](./stores/ecommerce) | Stores, products, customers, orders, reviews |
| [Expenses](./stores/expenses) | Expense reports, approvals, and reimbursements |
| [File Storage](./stores/file-storage) | Drives, folders, files with hierarchical permissions |
| [Healthcare](./stores/healthcare) | Patients, encounters, diagnoses, treatments, medications |
| [Hospitality](./stores/hospitality) | Hotels, rooms, reservations, guest services |
| [Human Resources](./stores/human-resources) | Employees, teams, payroll, benefits, time-off |
| [IoT](./stores/iot) | Device management and telemetry access |
| [Issue Tracking](./stores/issue-tracking) | Collections, tickets, comments, attachments |
| [Knowledge Base](./stores/knowledge-base) | Containers, articles, attachments, public content |
| [Knowledge Management](./stores/kms) | Spaces, pages, comments with publishing workflow |
| [Learning Management](./stores/lms) | Courses, classes, content, activities, grading |
| [Manufacturing](./stores/manufacturing) | Production lines, machines, work orders, quality reports |
| [Payment](./stores/payment) | Payments, payouts, refunds, subscriptions |
| [Real Estate](./stores/real-estate) | Properties, listings, transactions, inspections |

## Authoring Models using Coding Assistants

AI coding assistants can help you author OpenFGA models. The modeling guidelines are maintained in the [OpenFGA Best Practices Skill](https://github.com/openfga/agent-skills).

Some prompts you can try:

- Create an OpenFGA authorization model for an insurance company.
- Create an OpenFGA authorization model for B2B SaaS project management system.
- Create an OpenFGA authorization model for \<Product Name\> (e.g. create a model for Figma).

It will create a model in an `.fga` file, and a `.fga.yaml` with tuples/tests, and use the [CLI](https://github.com/openfga/cli) to run the tests.

### Claude Code

Install the skill, then prompt Claude Code with a modeling request:

```sh
npx skills add openfga/agent-skills
```

Example prompt: `/openfga Create an OpenFGA authorization model for an insurance company.`

### GitHub Copilot

Guidance is auto-loaded from [`.github/copilot-instructions.md`](.github/copilot-instructions.md) when you open this repository in VS Code with [Copilot](https://code.visualstudio.com/docs/copilot/setup-simplified) enabled.

### Other Tools (Cursor, generic LLMs)

Reference the full guidelines from `openfga/agent-skills`:
- **AGENTS.md:** https://raw.githubusercontent.com/openfga/agent-skills/main/skills/openfga/AGENTS.md
- **Cursor rules:** Copy AGENTS.md content into `.cursorrules`

You can also use the [DeepWiki MCP](https://docs.devin.ai/work-with-devin/deepwiki-mcp) or the [Context7 MCP](https://github.com/upstash/context7) to provide AI agents with OpenFGA context to help you implement OpenFGA using different SDKs.

## Creating your store and loading sample data

To try this out, you need the following tools installed:
- The `fga` CLI, follow the [installation instructions](https://github.com/openfga/cli/?tab=readme-ov-file#installation) here to get it
- `git`

1.  Clone this repository
  ```sh
  git clone https://github.com/openfga/sample-stores.git openfga-sample-stores && cd $_
  ```

2. Use the `fga` CLI to test the store you choose (e.g. `github`, `custom-roles`, etc..)
  ```sh
  SAMPLE_STORE=github
  fga model test --tests "stores/${SAMPLE_STORE}/store.fga.yaml"
  ```

## OpenFGA Models in Open Source Projects

- [Linux Foundation](https://github.com/linuxfoundation/lfx-v2-helm/blob/main/charts/lfx-platform/templates/openfga/model.yaml)
- [canonical/lxd](https://github.com/canonical/lxd/blob/main/lxd/auth/drivers/openfga_model.openfga)
- [canonical/identity-platform-login-ui](https://github.com/canonical/identity-platform-login-ui/blob/main/internal/authorization/schema.openfga)
- [canonical/jimm](https://github.com/canonical/jimm/blob/v3/openfga/authorisation_model.fga)
- [grafana/grafana](https://github.com/grafana/grafana/tree/main/pkg/services/authz/zanzana/schema)
- [lxc/incus](https://github.com/lxc/incus/blob/main/internal/server/auth/driver_openfga_model.openfga)
- [mindersec/minder](https://github.com/mindersec/minder/blob/main/internal/authz/model/minder.fga)
- [theopenlane/core](https://github.com/theopenlane/core/blob/main/fga/model/model.fga)
- [instill-ai/pipeline-backend](https://github.com/instill-ai/pipeline-backend/blob/main/pkg/acl/model.go)
- [virtool/virtool](https://github.com/virtool/virtool/blob/c621ba6a22bfe3464fe59287337029e42295cfcb/virtool/authorization/openfga.py#L123)
- [concrete-utopia/utopia](https://github.com/concrete-utopia/utopia/blob/master/utopia-remix/fga/model.fga)
- [Cross-Lab-Project/crosslab](https://github.com/Cross-Lab-Project/crosslab/blob/main/services/authorization/relation_model.fga)
- [sourcegraph/sourcegraph-public-snapshot](https://github.com/sourcegraph/sourcegraph-public-snapshot/blob/main/cmd/enterprise-portal/service/iam_model.fga)
- [klothoplatform/infracopilot](https://github.com/klothoplatform/infracopilot/blob/main/src/auth_service/model.fga)
- [ZEISS/knox](https://github.com/ZEISS/knox/blob/main/schema/auth.fga)
- [Lakekeeper](https://github.com/lakekeeper/lakekeeper/tree/main/authz/openfga/v3.2)

If you are using OpenFGA in your open source project, please let us know by opening a PR to add your model to this list.

## Modeling Resources
- [Modeling Guide](https://www.youtube.com/watch?v=5Lwy9aHXXHE&list=PLUR5l-oTFZqWaDdhEOVt_IfPOIbKo1Ypt)
- [OpenFGA Documentation](https://openfga.dev/docs/modeling)
