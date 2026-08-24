# IPRally Search MCP Connector

[IPRally](https://www.iprally.com) is an AI-powered patent search and IP analytics platform. This connector exposes IPRally's remote MCP server (`https://mcp.iprally.com/mcp`) so Microsoft Copilot Studio agents and Power Automate flows can search patents, run Boolean prior-art queries, and retrieve full patent bibliographies using natural language.

The server implements the [Model Context Protocol](https://modelcontextprotocol.io/) Streamable HTTP transport (`x-ms-agentic-protocol: mcp-streamable-1.0`). When imported into Copilot Studio, the connector surfaces IPRally's MCP tools (for example `searchPatents`, `booleanSearch`, `getPatentDocument`, `readIprallyDoc`, `sendFeedback`) to the agent. The tool list is not described in `apiDefinition.swagger.json` — Copilot Studio discovers it dynamically at runtime via the MCP protocol itself.

## Prerequisites

- An IPRally account with API/MCP access enabled.

## Authentication

The connector uses OAuth 2.0 (Authorization Code with PKCE) against IPRally's identity provider (`login.iprally.com`). When creating a connection, you're redirected to log in with your IPRally account and grant access; the connection requests the `offline_access` scope so it keeps working without repeated logins.

`apiProperties.json` ships with a placeholder Client ID (`<<Please add your Client ID here>>`). A company admin can create the OAuth credentials in IPRally under [Settings → Company → MCP](https://app.iprally.com/#/settings/company/mcp): create an MCP client to get a Client ID and Client Secret, and fill them in before running `paconn create` / `paconn update`. Do not commit real credentials back to this repository.

Power Platform generates a Redirect URL for your connector instance (of the form `https://global.consent.azure-apim.net/redirect/...`). Add it to the MCP client's allowed callback URLs on the same settings page.

## Usage

1. Import `apiDefinition.swagger.json` and `apiProperties.json` into your Power Platform environment as a custom connector (via the Power Apps/Power Automate custom connector UI, or `paconn create`).
2. Create a connection and sign in with your IPRally account.
3. Add the connector to a Copilot Studio agent under **Tools → Add a tool**, or reference it from a Power Automate flow.
4. In Copilot Studio, IPRally's MCP tools appear in the agent's tool list and can be enabled or disabled individually.

## Known issues and limitations

- The MCP server returns `401` if the access token is missing or expired — reconnect the connection.
- `getPatentDocument` returns `422` when the given publication number isn't in IPRally's index.

For help, contact [support@iprally.com](mailto:support@iprally.com).
