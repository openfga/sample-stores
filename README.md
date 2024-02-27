# OpenFGA Sample Stores

This repo contains some OpenFGA [sample stores](#sample-stores).

[![Join our community](https://img.shields.io/badge/slack-cncf_%23openfga-40abb8.svg?logo=slack)](https://openfga.dev/community)
[![Twitter](https://img.shields.io/twitter/follow/openfga?color=%23179CF0&logo=twitter&style=flat-square "@openfga on Twitter")](https://twitter.com/openfga)

## Table of Contents

- [About OpenFGA](#about-openfga)
- [Sample Stores](#sample-stores)
- [Creating your store and loading sample data](#creating-your-store-and-loading-sample-data)
- [Resources](#resources)

## About OpenFGA
[OpenFGA](https://openfga.dev) is an open source Fine-Grained Authorization solution inspired by [Google's Zanzibar paper](https://research.google/pubs/pub48190/). It was created by the FGA team at [Auth0](https://auth0.com) based on [Auth0 Fine-Grained Authorization (FGA)](https://fga.dev), available under [a permissive license (Apache-2)](https://github.com/openfga/rfcs/blob/main/LICENSE) and welcomes community contributions.

OpenFGA is designed to make it easy for application builders to model their permission layer, and to add and integrate fine-grained authorization into their applications. OpenFGA’s design is optimized for reliability and low latency at a high scale.

## Sample Stores

Some sample stores are:
- [GitHub](./stores/github)
- [Google Drive](./stores/gdrive)
- [Expenses](./stores/expenses)
- [IoT](./stores/iot)
- [Slack](./stores/slack)
- [Custom Roles](./stores/custom-roles)

## Creating your store and loading sample data

To try this out, you need the following tools installed:
- The `fga` CLI, follow the [installation instructions](https://github.com/openfga/cli/?tab=readme-ov-file#installation) here to get it
- `git`

1.  Clone this repository
```
git clone https://github.com/openfga/sample-stores.git openfga-sample-stores && cd $_
```

2. Use the `fga` CLI to test the [sample store](#sample-stores) you choose (e.g. `github`, `custom-roles`, etc..)
```
SAMPLE_STORE=github
fga model test --tests "stores/${SAMPLE_STORE}/store.yaml"
```


## Resources

- [OpenFGA Documentation](https://openfga.dev)
- [OpenFGA on GitHub](https://github.com/openfga/)
- [OpenFGA on Twitter](https://twitter.com/OpenFGA/)
- [Zanzibar Academy](https://zanzibar.academy)
- [Authorization in Software Podcast](https://authorizationinsoftware.auth0.com/public/49/Authorization-in-Software-f9b69587)
- [OpenFGA Community](https://openfga.dev/community)
- [FGA Playground](https://play.fga.dev)

## Author

[OpenFGA Team](https://github.com/openfga)

## License

[Apache-2.0](./LICENSE)
