# OpenFGA Sample Stores

[![Join our community](https://img.shields.io/badge/slack-cncf_%23openfga-40abb8.svg?logo=slack)](https://openfga.dev/community)
[![Twitter](https://img.shields.io/twitter/follow/openfga?color=%23179CF0&logo=twitter&style=flat-square "@openfga on Twitter")](https://x.com/openfga)

This repository contains [sample store models](#sample-stores) for [OpenFGA](https://openfga.dev).

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Sample Store Models](#sample-store-models)
- [Creating your store and loading sample data](#creating-your-store-and-loading-sample-data)
- [OpenFGA models in open source projects](#openfga-models-in-open-source-projects)
- [Modeling Resources](#modeling-resources)

## Sample Store Models

- [Modeling ABAC with ReBAC](./stores/abac-with-rebac)
- [Advanced Entitlements](./stores/advanced-entitlements)
- [Banking](./stores/banking)
- [Condition Data Types](./stores/condition-data-types)
- [Custom Roles](./stores/custom-roles)
- [Developer Portal](./stores/developer-portal)
- [Entitlements](./stores/entitlements)
- [Expenses](./stores/expenses)
- [GitHub](./stores/github)
- [Google Drive](./stores/gdrive)
- [Group Resource Attributes](./stores/groups-resource-attributes)
- [IoT](./stores/iot)
- [IP-Based Access](./stores/ip-based-access)
- [Modeling Guide](./stores/modeling-guide)
- [Modeling Multi-Tenant Role-Based Access Control](./stores/multitenant-rbac)
- [Modular Models](./stores/modular)
- [Role Assignments](./stores/role-assignments)
- [Slack](./stores/slack)
- [Super-Admin](./stores/superadmin)
- [Temporal Access](./stores/temporal-access)

## Authoring Models using Coding Assistants

This repository includes a [copilot-instructions](.github/copilot-instructions.md) file that can help you author OpenFGA models. It can be used in VS Code or in any AI-assisted IDE. You can also use it as a prompt, or system prompt, in any LLM. 

Some prompts you can try:

- Create an OpenFGA authorization model for an insurance company.
- Create an OpenFGA authorization model for B2B SaaS project management system.
- Create an OpenFGA authorization model for <Product Name> (e.g. create a model for Figma).

It will create a model in an `.fga` file, and a `.fga.yaml` with tuples/tests, and use the [CLI](https://github.com/openfga/cli) to run the tests.

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

## OpenFGA models in open source projects

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
- [Zanzibar Academy](https://zanzibar.academy)
- [Implementing Multi-Tenancy in Chroma: Part 2 - Authorization Model with OpenFGA](https://cookbook.chromadb.dev/strategies/multi-tenancy/authorization-model-with-openfga/)
- [Introduction into OpenFGA](https://www.ericksegaar.com/2024/03/04/introduction-into-openfga/)


