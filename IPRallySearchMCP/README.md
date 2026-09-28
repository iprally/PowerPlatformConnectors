# IPRally Search MCP Connector

[IPRally](https://www.iprally.com) is an AI-powered patent search and IP analytics platform. This connector exposes IPRally's remote MCP server (`https://mcp.iprally.com/mcp`) so Microsoft Copilot Studio agents and Power Automate flows can search patents, run Boolean prior-art queries, and retrieve full patent bibliographies using natural language.

The server implements the [Model Context Protocol](https://modelcontextprotocol.io/) Streamable HTTP transport (`x-ms-agentic-protocol: mcp-streamable-1.0`). When imported into Copilot Studio, the connector surfaces IPRally's MCP tools (for example `searchPatents`, `booleanSearch`, `getPatentDocument`, `readIprallyDoc`, `sendFeedback`) to the agent. The tool list is not described in `apiDefinition.swagger.json` — Copilot Studio discovers it dynamically at runtime via the MCP protocol itself.

## Prerequisites

- An IPRally account with API/MCP access enabled.

## Authentication

The connector uses OAuth 2.0 (Authorization Code with PKCE) against IPRally's identity provider (`login.iprally.com`). When creating a connection, you're redirected to log in with your IPRally account and grant access; the connection requests the `offline_access` scope so it keeps working without repeated logins.

IPRally's identity provider requires two things on every login:

- PKCE (`code_challenge` with `code_challenge_method=S256`).
- The MCP server as the token audience (`resource=https://mcp.iprally.com/mcp`).

Both are set in the OAuth settings in `apiProperties.json`. The connector works only when it is created from both `apiDefinition.swagger.json` and `apiProperties.json`.

## Credentials

A company admin creates the OAuth credentials in IPRally under [Settings → Company → MCP](https://app.iprally.com/#/settings/company/mcp). Create an MCP client there to get a Client ID and a Client Secret.

- Put the Client ID in `apiProperties.json` in place of `<<Please add your Client ID here>>`.
- Pass the Client Secret to `paconn` with `--secret`. It does not go in any file.

Do not commit real credentials back to this repository.

## Usage

1. Install the Power Platform Connectors CLI and log in:

   ```bash
   pip install paconn
   paconn login
   ```

2. Create the custom connector from both files:

   ```bash
   paconn create \
     --api-def IPRallySearchMCP/apiDefinition.swagger.json \
     --api-prop IPRallySearchMCP/apiProperties.json \
     --secret "<your client secret>"
   ```

   `paconn` asks for the Power Platform environment if you do not pass `-e`. Use `paconn update` with the same arguments to change an existing connector.

3. Open the new custom connector in Power Apps or Power Automate and copy the Redirect URL from its **Security** tab. It has the form `https://global.consent.azure-apim.net/redirect/...`. Add it to the MCP client's allowed callback URLs on the IPRally settings page.
4. Create a connection and sign in with your IPRally account.
5. Add the connector to a Copilot Studio agent under **Tools → Add a tool**, or reference it from a Power Automate flow.
6. In Copilot Studio, IPRally's MCP tools appear in the agent's tool list and can be enabled or disabled individually.

Do not create the connector by importing only `apiDefinition.swagger.json` in the custom connector UI. That import does not read `apiProperties.json`, so the connector sends no PKCE and no audience, and every login fails.

## Troubleshooting

If the login page shows "Oops!, something went wrong", open **See details for this error**:

- **Callback URL mismatch**: the connector's Redirect URL is not in the MCP client's allowed callback URLs. Add it on the IPRally settings page (Usage step 3).
- **The PKCE protocol extension is required**: the connector was not created from `apiProperties.json`. Delete it and create it again with `paconn create` (Usage step 2).
- **The userinfo audience is not allowed for third party clients**: the connector sends PKCE but not the `resource` parameter. Create it again from the unchanged `apiProperties.json`.

## Known issues and limitations

- The MCP server returns `401` if the access token is missing or expired — reconnect the connection.
- `getPatentDocument` returns `422` when the given publication number isn't in IPRally's index.

For help, contact [support@iprally.com](mailto:support@iprally.com).
