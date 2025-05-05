# Linear MCP Server

[![npm version](https://img.shields.io/npm/v/@linear/sdk.svg)](https://www.npmjs.com/package/@linear/sdk)

A [Model Context Protocol](https://github.com/modelcontextprotocol) server for the [Linear API](https://developers.linear.app/docs/graphql/working-with-the-graphql-api).

This server provides integration with Linear's issue tracking system through MCP, allowing LLMs to interact with Linear issues.

## Installation & Setup

This server is designed to be run locally after cloning the repository.

1.  **Clone the Repository:**
    ```bash
    git clone <your-repository-url>
    cd linear-mcp-server-fork # Or your repository name
    ```

2.  **Install Dependencies:**
    ```bash
    npm install
    ```

3.  **Configure API Key:** Create a `.env` file in the root directory and add your Linear API key:
    ```bash
    # .env
    LINEAR_API_KEY=your_linear_api_key_here
    ```
    *   Get keys from: `https://linear.app/<YOUR-TEAM>/settings/api`

4.  **Build the Server:**
    ```bash
    npm run build
    ```

5.  **Configure your MCP Client (e.g., Cursor, Claude Desktop):**
    Point your client to run the built server script. Example for Cursor (`.cursor/mcp.json`):
    ```json
    {
      "mcpServers": {
        "linear": {
          "command": "node",
          "args": [
            "/path/to/your/linear-mcp-server-fork/build/index.js" // Adjust path as needed
          ],
          "env": {
            // Optionally override .env key here, or rely on .env file
            // "LINEAR_API_KEY": "your_linear_api_key_here"
          }
        }
      }
    }
    ```
    *(Adapt the configuration for your specific MCP client)*

## Components

### Tools

1.  **`linear_create_issue`**: Create a new Linear issue.
    *   Required: `title`, `teamId`
    *   Optional: `description` (markdown), `priority` (0-4), `status`, `parentId`

2.  **`linear_update_issue`**: Update an existing issue.
    *   Required: `id` (UUID or Identifier like `PROJ-123`)
    *   Optional: `title`, `description`, `priority` (0-4), `status`, `parentId`

3.  **`linear_search_issues`**: Search issues with flexible filtering. Handles direct identifier lookup (e.g., `PROJ-123`) first, then falls back to text search.
    *   Optional:
        *   `query` (string): Identifier or text for title/description
        *   `teamId`, `status`, `assigneeId`, `labels` (string[]), `priority`, `estimate`, `includeArchived`, `limit` (default: 10)

4.  **`linear_get_user_issues`**: Get issues assigned to a user.
    *   Optional: `userId` (omit for authenticated user), `includeArchived`, `limit` (default: 50)

5.  **`linear_add_comment`**: Add comments to issues.
    *   Required: `issueId` (UUID or Identifier like `PROJ-123`), `body` (markdown)
    *   Optional: `createAsUser`, `displayIconUrl`

6.  **`linear_get_issue`**: Retrieve details for a specific issue, **including comments**.
    *   Required: `id` (UUID or Identifier like `PROJ-123`)

7.  **`linear_get_organization_details`**: Retrieve organization details (teams, users).
    *   No required arguments.

8.  **`linear_archive_issue`**: Archive (soft-delete) an issue.
    *   Required: `id` (UUID or Identifier like `PROJ-123`)

### Resources (Examples for `ReadResourceRequest`)

*   `linear-issue:///{issueId}` - View individual issue details (using UUID).
*   `linear-team:///{teamId}/issues` - View team issues.
*   `linear-user:///{userId}/assigned` - View user's assigned issues.
*   `linear-organization:` - View organization info.
*   `linear-viewer:` - View current user context.

*(Note: Reading resources might not reflect all recent code changes, e.g., comment fetching in `linear-issue:///` might require specific implementation)*

## Usage examples

Some example prompts you can use with your MCP client:

1.  "Show me my high-priority issues" → `linear_get_user_issues` or `linear_search_issues` with appropriate filters.
2.  "Create a bug report for the login system titled 'Login fails with incorrect password'." → `linear_create_issue` with title, description, teamId, priority.
3.  "Find issue PROJ-123" → `linear_search_issues` with `query: "PROJ-123"`.
4.  "Get the details and comments for issue PROJ-456" → `linear_get_issue` with `id: "PROJ-456"`.
5.  "Add a comment to PROJ-789 saying 'Investigating this now.'" → `linear_add_comment` with `issueId: "PROJ-789"` and body.
6.  "What teams are in this organization?" → `linear_get_organization_details`.

## Development

1.  **Install Dependencies:**
    ```bash
    npm install
    ```
2.  **Configure API Key:** Create/update `.env` file:
    ```bash
    LINEAR_API_KEY=your_api_key_here
    ```
3.  **Build:**
    ```bash
    npm run build
    ```
4.  **Watch Mode (Auto-rebuild on changes):**
    ```bash
    npm run watch
    ```
    *(Remember to restart the server process in your MCP client after builds/changes if not running directly from source)*

## License

This MCP server is licensed under the MIT License. This means you are free to use, modify, and distribute the software, subject to the terms and conditions of the MIT License. For more details, please see the LICENSE file in the project repository.
