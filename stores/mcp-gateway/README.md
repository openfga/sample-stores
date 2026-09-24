# OpenFGA for MCP Gateways

## Use Case

This folder demonstrates three different models for MCP Gateway use cases:

*   **An employee-facing MCP Gateway:** Control which MCP server tools users can call through a gateway. Permissions are based on user identity and role membership.
*   **A customer-facing multi-tenant MCP Gateway:** Where you write your own agents and want to control what the agents can do when interacting with each tenant's tools. Permissions are based on agent identity.
*   **An intent-based authorization scenario:** An iteration of the previous one showing how to apply the same ideas for authorizing calls based on inferred intent.

These examples depend on the experimental **Dynamic Conditions** feature, whose syntax, behavior, API, and tuple format may change. The FGA CLI enables inline expressions automatically when running model tests. Testing with an OpenFGA server requires OpenFGA v1.21.0 or later with the experimental feature enabled explicitly:

```bash
openfga run --experimentals inline_expressions
```

## Employee Authorization

**Requirements:**

*   **Gateway-level policies:** Allow definition of policies that are well-known to the gateway developer so they can be defined with static conditions. In the example, we add an IP restriction policy.
*   **Broad tool access:** Allow access to all read-only or read-write tools per MCP server, user, or role.
*   **Specific tool access:** Allow access to specific tools per user or role.
*   **Parameter-based access:** Allow calling tools with specific parameter values per user or role. This is implemented using Dynamic Conditions.

You can view the [mcp-gateway.fga.yaml](mcp-gateway.fga.yaml) example configuration.

## Multi-Tenant Agent Authorization

**Requirements:**

*   Agents operate on behalf of an organization.
*   Agents can be granted permissions to call tools, including calls with specific parameter values.

For example, you can configure that the `triage-bot` can only send Slack messages to `#product-announcements` for the organization `Acme`.

You can view the [multi-tenant-mcp-gateway.fga.yaml](multi-tenant-mcp-gateway.fga.yaml) example configuration.

## Intent-Based Authorization

This builds on the previous example. Organization membership and tool ownership are persisted, but tool-call grants are supplied at request time instead of being persisted.

**Requirements:**

*   Agents operate on behalf of an organization.
*   Agents begin with no persisted tool-call grants.
*   An intent inference engine (out of scope for this example) will infer which tools the agent should call and with which parameters. This information is sent to OpenFGA as contextual tuples.

This pattern also applies to intent-based authorization. Permissions can come from an intent-inference engine or a token that grants the agent fine-grained contextual permissions.

The model test writes its test-local `caller` tuples to a temporary datastore. A production application would instead include those grants as contextual tuples in its authorization check.

You can view the [multi-tenant-mcp-gateway-intent.fga.yaml](multi-tenant-mcp-gateway-intent.fga.yaml) example configuration.

## Try It Out

1. Make sure you have the [FGA CLI v0.81 or later](https://github.com/openfga/cli/?tab=readme-ov-file#installation) installed.

2. In the `mcp-gateway` directory, run `fga model test --tests <store-file>.fga.yaml`
