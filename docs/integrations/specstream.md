---
catalog_title: SpecStream
catalog_description: Search the live web, read pages, and write cited reports
catalog_icon: /integrations/assets/specstream.png
catalog_tags: ["search", "mcp"]
---

# SpecStream MCP tool for ADK

<div class="language-support-tag">
  <span class="lst-supported">Supported in ADK</span><span class="lst-python">Python</span><span class="lst-typescript">TypeScript</span>
</div>

The [SpecStream MCP Server](https://specstream.ai/docs/mcp) connects your ADK
agent to [SpecStream](https://specstream.ai/), a web search and page reading
API. This integration gives your agent tools to search the live web, read the
text of web pages and documents, and write research reports where each cited
sentence links to the page it came from.

## Use cases

- **Answer Questions from the Live Web**: Let your agent look up current facts,
  prices, people, and events, and return each answer with the page it was read
  from.

- **Read Pages and Documents**: Pass URLs from a search result or from the user
  and get back the main text of up to 20 web pages, PDFs, Word, or Excel files
  as Markdown or plain text.

- **Write Cited Research Reports**: Have your agent research a question across
  many pages and return a Markdown report, or JSON that matches a JSON Schema,
  with numbered citations for each claim.

## Prerequisites

- Create a [SpecStream account](https://specstream.ai/workspace)
- Create an API key under **Agents & API** in the SpecStream workspace. Refer
  to the [documentation](https://specstream.ai/docs/mcp) for more information.

## Use with agent

=== "Python"

    === "Remote MCP Server"

        ```python
        from google.adk.agents import Agent
        from google.adk.tools.mcp_tool import McpToolset
        from google.adk.tools.mcp_tool.mcp_session_manager import StreamableHTTPConnectionParams

        SPECSTREAM_API_KEY = "YOUR_SPECSTREAM_API_KEY"

        root_agent = Agent(
            model="gemini-flash-latest",
            name="specstream_agent",
            instruction="Help users find information on the live web and cite the pages you used",
            tools=[
                McpToolset(
                    connection_params=StreamableHTTPConnectionParams(
                        url="https://api.specstream.ai/mcp",
                        headers={
                            "Authorization": f"Bearer {SPECSTREAM_API_KEY}",
                        },
                    ),
                    tool_filter=["search_web", "read_pages"],
                )
            ],
        )
        ```

=== "TypeScript"

    === "Remote MCP Server"

        ```typescript
        import { LlmAgent, MCPToolset } from "@google/adk";

        const SPECSTREAM_API_KEY = "YOUR_SPECSTREAM_API_KEY";

        const rootAgent = new LlmAgent({
            model: "gemini-flash-latest",
            name: "specstream_agent",
            instruction: "Help users find information on the live web and cite the pages you used",
            tools: [
                new MCPToolset(
                    {
                        type: "StreamableHTTPConnectionParams",
                        url: "https://api.specstream.ai/mcp",
                        transportOptions: {
                            requestInit: {
                                headers: {
                                    Authorization: `Bearer ${SPECSTREAM_API_KEY}`,
                                },
                            },
                        },
                    },
                    ["search_web", "read_pages"],
                ),
            ],
        });

        export { rootAgent };
        ```

!!! tip "Choosing tools"

    The examples above use a tool filter to give the agent only web search and
    page reading. To let the agent write research reports, add
    `research_price`, `start_research`, and `get_research` to the filter.
    Remove the filter to expose every tool the server offers.

## Available tools

Tool | Description
---- | -----------
`search_web` | Search the live web and return an answer, the result pages read, and the page each value came from
`read_pages` | Read up to 20 web pages or documents (PDF, Word, Excel) and return each one's main text as Markdown or plain text
`list_web_searches` | List past web searches on the account with their answers and sources
`get_web_search` | Open one past web search by its ID
`research_price` | Show the price of a research report at each depth before it runs
`start_research` | Start a research report on a question from the live web, with numbered citations
`get_research` | Read the status of a research run and the report once it is completed

## Additional resources

- [SpecStream MCP Server Documentation](https://specstream.ai/docs/mcp)
- [SpecStream Search API Documentation](https://specstream.ai/docs/search-api)
- [SpecStream Extract API Documentation](https://specstream.ai/docs/extract-api)
