# OpenFGA Sample Stores

[![Join our community](https://img.shields.io/badge/slack-cncf_%23openfga-40abb8.svg?logo=slack)](https://openfga.dev/community)
[![Twitter](https://img.shields.io/twitter/follow/openfga?color=%23179CF0&logo=twitter&style=flat-square "@openfga on Twitter")](https://twitter.com/openfga)

This repository contains [sample store models](#sample-stores) for [OpenFGA](https://openfga.dev).

## Table of Contents

- [Sample Store Models](#sample-store-models)
- [Creating your store and loading sample data](#creating-your-store-and-loading-sample-data)
- [Modeling resources](#modeling-resources)
- [OpenFGA models in open source projects](#openfga-models-in-open-source-projects)
- [Resources](#resources)

## Sample Store Models

- [GitHub](./stores/github)
- [Google Drive](./stores/gdrive)
- [Expenses](./stores/expenses)
- [IoT](./stores/iot)
- [Slack](./stores/slack)
- [Custom Roles](./stores/custom-roles)
- [Entitlements](./stores/entitlements)
- [Advanced Entitlements](./stores/advanced-entitlements)
- [Banking](./stores/banking)
- [IP Based Access](./stores/ip-based-access)
- [Super-Admin](./stores/superadmin)
- [Temporal Access](./stores/temporal-access)
- [Group Resource Attributes](./stores/groups-resource-attributes)
- [Conditions Data Types](./stores/condition-data-types)
- [Developer Portal](./stores/developer-portal)
- [Modeling ABAC with ReBAC](./stores/abac-with-rebac)
- [Modeling multi-tenant Role-Based Access Control](./stores/multitenant-rbac)

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

## Modeling Resources

The OpenFGA [Getting Started](https://openfga.dev/docs/modeling/getting-started) documentation is a great way to learn about FGA modeling. These two articles illustrate how you can create a model following the documentation steps:

- [Implementing Multi-Tenancy in Chroma: Part 2 - Authorization Model with OpenFGA](https://cookbook.chromadb.dev/strategies/multi-tenancy/authorization-model-with-openfga/)
- [Introduction into OpenFGA](https://www.ericksegaar.com/2024/03/04/introduction-into-openfga/)


## OpenFGA models in open source projects

- [canonical/lxd](https://github.com/canonical/lxd/blob/main/lxd/auth/drivers/openfga_model.openfga)
- [canonical/identity-platform-login-ui](https://github.com/canonical/identity-platform-login-ui/blob/main/internal/authorization/schema.openfga)
- [canonical/jimm](https://github.com/canonical/jimm/blob/v3/openfga/authorisation_model.fga)
- [lxc/incus](https://github.com/lxc/incus/blob/main/internal/server/auth/driver_openfga_model.openfga)
- [mindersec/minder](https://github.com/mindersec/minder/blob/main/internal/authz/model/minder.fga)
- [datum-cloud/datum](https://github.com/datum-cloud/datum/blob/main/fga/model/datum.fga)
- [instill-ai/pipeline-backend](https://github.com/instill-ai/pipeline-backend/blob/main/pkg/acl/model.go)
- [virtool/virtool](https://github.com/virtool/virtool/blob/c621ba6a22bfe3464fe59287337029e42295cfcb/virtool/authorization/openfga.py#L123)
- [concrete-utopia/utopia](https://github.com/concrete-utopia/utopia/blob/master/utopia-remix/fga/model.fga)
- [Cross-Lab-Project/crosslab](https://github.com/Cross-Lab-Project/crosslab/blob/main/services/authorization/relation_model.fga)
- [grafana/grafana](https://github.com/grafana/grafana/tree/main/pkg/services/authz/zanzana/schema)
- [sourcegraph/sourcegraph-public-snapshot](https://github.com/sourcegraph/sourcegraph-public-snapshot/blob/main/cmd/enterprise-portal/service/iam_model.fga)
- [klothoplatform/infracopilot](https://github.com/klothoplatform/infracopilot/blob/main/src/auth_service/model.fga)
- [ZEISS/knox](https://github.com/ZEISS/knox/blob/main/schema/auth.fga)
- [Lakekeeper](https://github.com/lakekeeper/lakekeeper/blob/main/authz/openfga/v1/schema.fga)


If you are using OpenFGA in your open source project, please let us know by opening a PR to add your model to this list.

## Resources

- [OpenFGA Documentation](https://openfga.dev)
- [OpenFGA on GitHub](https://github.com/openfga/)
- [OpenFGA on Twitter](https://twitter.com/OpenFGA/)
- [Zanzibar Academy](https://zanzibar.academy)
- [Authorization in Software Podcast](https://authorizationinsoftware.auth0.com/public/49/Authorization-in-Software-f9b69587)
- [OpenFGA Community](https://openfga.dev/community)
- [FGA Playground](https://play.fga.dev)
