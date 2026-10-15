# Fastero MCP server

Query your databases and build dashboards, Streamlit apps and reports your team logs in to, from
GitHub Copilot, Claude or another MCP client.

Fastero is a hosted service. This repository holds the connection instructions; the server itself
runs at:

```
https://api.fastero.com/api/v1/mcp
```

It is listed in the official MCP Registry as `com.fastero/fastero`.

## What you can do

Ask in plain words. Your assistant works inside your Fastero workspace, and what it builds stays
there for your team:

- **Explore your data.** List your connections and tables, run read-only SQL, and read the
  definitions your team agreed on, such as what counts as revenue.
- **Build dashboards.** Save queries and turn them into charts, tables and number cards on a
  dashboard your colleagues open with their own login.
- **Share and schedule.** Invite teammates, publish a public read-only link to a dashboard, or email
  a dashboard, with a PDF attached, to people in your workspace every Monday.
- **Ship apps and code.** Deploy a Streamlit app behind your Fastero login, and run notebooks and
  Python projects now or on a schedule, with your secrets injected.
- **Watch the numbers.** Get an email when a number crosses a line.

## Connect

You need a Fastero account: [sign up](https://app.fastero.com/signup?utm_source=github&utm_medium=listing&utm_campaign=aud2-agents).

### VS Code (GitHub Copilot)

Open the Command Palette and run **MCP: Add Server**, choose **HTTP**, and paste the address above.
Or add it to your `mcp.json`:

```json
{
  "servers": {
    "fastero": {
      "type": "http",
      "url": "https://api.fastero.com/api/v1/mcp"
    }
  }
}
```

Start the server. When VS Code asks whether it may sign in, choose **Allow**, then sign in to
Fastero, pick a workspace if asked, and approve. In Copilot Chat, use Agent mode.

### Claude

In claude.ai, open **Customize → Connectors**, choose **+ Add → Add custom connector**, name it and
paste the address. If you are asked for an OAuth client, choose **Register automatically**. Then
sign in to Fastero, pick a workspace if asked, and approve. On a Team or Enterprise plan, an owner adds the
connector under **Organization settings → Connectors**, and each member then connects.

The connector also works in Claude Desktop, and Claude Code signed in with the same Claude account
picks it up automatically.

### Other MCP clients

Other clients that support remote MCP servers with OAuth sign-in should work with the same address.
Fastero supports dynamic client registration, so there is no app to register first. If your client
cannot complete the sign-in, use an API key.

### With an API key

For scripts, automation, or a client that cannot sign in: in Fastero, open **Settings → Developers →
AI Agent Access → Create key**, and choose what the key may do. **Build dashboards** ticks reading,
SQL, new connections, and creating and editing. Creating and editing includes alerts, switching
existing schedules and triggers on or off, and changing the code of an app or Python project that
already runs or is scheduled, which then runs the new code. Running code and setting up schedules;
public links, dashboard emails and invites; and Secrets each have their own box. Send the key as
`Authorization: Bearer <key>`.

In VS Code, so the key is asked for once and kept out of the file:

```json
{
  "inputs": [
    { "type": "promptString", "id": "fastero-key", "description": "Fastero API key", "password": true }
  ],
  "servers": {
    "fastero": {
      "type": "http",
      "url": "https://api.fastero.com/api/v1/mcp",
      "headers": { "Authorization": "Bearer ${input:fastero-key}" }
    }
  }
}
```

In Claude Code signed in with an API key:

```
claude mcp add --scope user --transport http fastero https://api.fastero.com/api/v1/mcp --header "Authorization: Bearer <key>"
```

Keys last up to 90 days, and Fastero notifies you in the app 14 and 3 days before one expires.

### More than one workspace

Every workspace also has an address of its own,
`https://api.fastero.com/api/v1/mcp/org/<workspace id>`. **AI Agent Access** shows it with a copy
button. In claude.ai, add one connector per workspace.

## Security

- You sign in and approve what the assistant may do. It acts as you, in the one workspace you chose,
  and never beyond what your own account may do there.
- Connection passwords, Git tokens and Secret values are never returned by any tool. Code you run in
  Fastero receives your Secrets, and a Streamlit app linked to a connection receives that
  connection's login, so anything that code prints is visible to the assistant.
- Your connectors and keys are listed, per workspace, under **Settings → Developers → AI Agent
  Access**, and revoking one ends it at once.

## Example requests

- "Show me the tables in my sales database."
- "Build a dashboard called Sales overview: revenue and orders for the last month, revenue by month
  for the last two years, and the ten products with the most revenue."
- "Email this dashboard to me every Monday at 8am London time."
- "Deploy the Streamlit app in this folder to Fastero and give me the link."
- "Alert me when daily signups drop below 20."

## Help

Questions and feedback: [fastero.com/contact](https://fastero.com/contact).
