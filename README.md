# IPRally Power Platform Connectors

Custom Power Platform / Copilot Studio connectors for IPRally's MCP servers.

## Connectors

- [IPRally Search MCP](./IPRallySearchMCP) — patent similarity search, Boolean search, and full patent document lookup via `https://mcp.iprally.com/mcp`.

## Repository layout

Each connector lives in its own directory containing:

- `README.md` — description, prerequisites, authentication, usage, and known issues specific to that connector.
- `apiDefinition.swagger.json` — the OpenAPI (Swagger 2.0) definition imported as a Power Platform custom connector.
- `apiProperties.json` — connection/auth properties (OAuth settings, brand color, etc.) for the same connector.

This mirrors the per-connector folder convention used by [microsoft/PowerPlatformConnectors](https://github.com/microsoft/PowerPlatformConnectors).

## Adding a new connector

1. Create a new directory named after the connector.
2. Add `apiDefinition.swagger.json`, `apiProperties.json`, and a `README.md`, following the structure in [`IPRallySearchMCP/README.md`](./IPRallySearchMCP/README.md).
3. Validate with `paconn validate --api-def <connector>/apiDefinition.swagger.json` before committing.
