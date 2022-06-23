# OpenFGA Sample Stores

[![Discord Server](https://img.shields.io/discord/759188666072825867?color=7289da&logo=discord "Discord Server")](https://discord.com/channels/759188666072825867/930524706854031421)
[![Twitter](https://img.shields.io/twitter/follow/openfga?color=%23179CF0&logo=twitter&style=flat-square "@openfga on Twitter")](https://twitter.com/openfga)

This repo contains some OpenFGA [sample stores configurations](#sample-stores).

## Table of Contents

- [About OpenFGA](#about-openfga)
- [Sample Stores](#sample-stores)
- [Creating your store and loading sample data](#creating-your-store-and-loading-sample-data)
- [Resources](#resources)

## About OpenFGA

[OpenFGA](https://github.com/openfga/openfga) is an open source Fine-Grained Authorization solution based on Google's Zanzibar. It was created by the Auth0 FGA team and welcomes community contribution. OpenFGA is designed to make it easy for application builders to quickly add fine-grained authorization to their applications. It offers an HTTP API and has SDKs for programming languages including [JavaScript](https://github.com/openfga/js-sdk), [GoLang](https://github.com/openfga/go-sdk) and [.NET](https://github.com/openfga/dotnet-sdk). More SDKs and integrations such as Rego are planned for the future. OpenFGA is designed and optimized for reliability and low latency at a high scale.

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
- `docker`
- `git`
- `curl`
- `jq`

1. Ensure you have [openfga](https://github.com/openfga/openfga) up and running.
```
docker run -p 127.0.0.1:8080:8080 openfga/openfga run
```

2. Clone this repository
```
git clone https://github.com/openfga/sample-stores.git openfga-sample-stores && cd openfga-sample-stores
```

3. Navigate into the sample store you choose (e.g. `github`)
```
cd stores/github
```

4. Loading data into your store
```shell
FGA_API_URI=http://localhost:8080

# Create the store
STORE_ID=$(curl -X POST $FGA_API_URI/stores -d @store.json | jq -r '.id')
# Post the authorization model
AUTHORIZATION_MODEL_ID=$(curl -X POST $FGA_API_URI/stores/$STORE_ID/authorization-models -d @authorization-model.json | jq -r '.authorization_model_id')
# Write the tuples (Note: max tuples per write request = 10)
cat tuples.json | jq -c '. | _nwise(10) | { "writes": { "tuple_keys": . } }' | (
    while read id; do
        curl -X POST $FGA_API_URI/stores/$STORE_ID/write -d $id
    done
)
# Update the assertions for that authorization model
cat assertions.json | jq '. |= { "assertions": .  }' | curl -X PUT $FGA_API_URI/stores/$STORE_ID/assertions/$AUTHORIZATION_MODEL_ID -d @-

# Run a sample check
cat assertions.json | jq '.[0] | { "tuple_key": { "user": .tuple_key.user, "relation": .tuple_key.relation, "object": .tuple_key.object } }' | curl -X POST $FGA_API_URI/stores/$STORE_ID/check -d @-# Run sample checks (here we are just looping through our assertions and issuing checks for them)

# Run checks for all assertions
cat assertions.json | jq -c '. | _nwise(1) | { "tuple_key": { "user": .[0].tuple_key.user, "relation": .[0].tuple_key.relation, "object": .[0].tuple_key.object } }' | (
    while read body; do
        FGA_ALLOWED=$(curl -s -X POST $FGA_API_URI/stores/$STORE_ID/check -d $body | jq '.allowed')
        FGA_USER=$(echo $body | jq -rc '.tuple_key.user')
        FGA_RELATION=$(echo $body | jq -rc '.tuple_key.relation')
        FGA_OBJECT=$(echo $body | jq -rc '.tuple_key.object')
        echo "check(user=$FGA_USER, relation=$FGA_RELATION, object=$FGA_OBJECT); allowed: $FGA_ALLOWED"
    done
)
```

## Resources

- [OpenFGA Documentation](https://openfga.dev)
- [OpenFGA on GitHub](https://github.com/openfga/)
- [OpenFGA on Twitter](https://twitter.com/OpenFGA/)
- [Zanzibar Academy](https://zanzibar.academy)
- [Authorization in Software Podcast](https://authorizationinsoftware.auth0.com/public/49/Authorization-in-Software-f9b69587)
- [Discord Community](https://discord.gg/pvbNmqC)
- [Auth0 FGA Playground](https://play.fga.dev)

## Author

[OpenFGA Team](https://github.com/openfga)

## License

[Apache-2.0](./LICENSE)
