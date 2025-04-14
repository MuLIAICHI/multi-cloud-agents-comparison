# Pydantic AI MCP Agent - Tool Integration Framework


This project demonstrates how to build an AI agent that integrates with Model Context Protocol (MCP) servers, allowing AI models to access external tools through a standardized interface. It uses Pydantic AI for the agent framework and MCP for tool integration, and the MCP configuration is similar to Claude Desktop/Windsurf/Cline.

## 🚀 Quick Start

If you want to very quickly integrate MCP servers into your own Pydantic AI agents, just follow these simple steps:

1. Copy `mcp_client.py` from this repo into your own project

2. Install the necessary dependencies:

```bash
pip install pydantic-ai mcp
```

3. Set up an `mcp_config.json` file in your project which follows the **exact** same structure as configuring MCP servers for Claude Desktop. Use `mcp_config_example.json` for guidance.

4. Set up your Pydantic AI agent like:

```python
import mcp_client
from pydantic_ai import Agent

async def get_pydantic_ai_agent():
    client = mcp_client.MCPClient()
    client.load_servers("mcp_config.json")
    tools = await client.start()
    return client, Agent(model='your-llm-here', tools=tools)
```

Then you can retrieve the client and agent in your main function like:

```python
client, agent = await get_pydantic_ai_agent()
```

This Pydantic AI agent will now have access to all the tools for the MCP servers you defined in `mcp_config.json`!

## ✨ Features

<table>
  <tr>
    <td width="50%">
      <strong>🔧 MCP Tool Integration</strong><br>
      Connect to any MCP-compatible tool server
    </td>
    <td width="50%">
      <strong>🤖 Pydantic AI Framework</strong><br>
      Leverage powerful Pydantic AI agent capabilities
    </td>
  </tr>
  <tr>
    <td>
      <strong>🔄 Dynamic Tool Discovery</strong><br>
      Automatically convert MCP tools to Pydantic AI tools
    </td>
    <td>
      <strong>💬 Interactive CLI</strong><br>
      Simple command-line interface for testing
    </td>
  </tr>
</table>

## 🏗️ Architecture

### Key Components

The system works by:

1. **Configuration**: Define MCP servers in `mcp_config.json`
2. **Connection**: Establish connections to MCP servers via stdio
3. **Tool Discovery**: Retrieve available tools from MCP servers
4. **Tool Conversion**: Transform MCP tools into Pydantic AI compatible tools
5. **Agent Initialization**: Create a Pydantic AI agent with the converted tools
6. **Execution**: Run the agent with user input, allowing it to call MCP tools

## 📦 Project Structure

```
pydantic-ai-mcp-agent/
├── mcp_client.py              # Client for connecting to MCP servers
├── pydantic_mcp_agent.py      # Main CLI application with interactive chat
├── mcp_config_example.json    # Example configuration for MCP servers
└── requirements.txt           # Python dependencies
```

## 📋 Prerequisites

- Python 3.9+
- Node.js (for MCP servers)
- OpenAI API key (or compatible API like OpenRouter, can use Ollama without an API key too)

## ⚙️ Setup Instructions

1. **Create and activate a virtual environment**:
   ```bash
   # On Windows
   python -m venv venv
   venv\Scripts\activate

   # On macOS/Linux
   python -m venv venv
   source venv/bin/activate
   ```

2. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

3. **Set up environment variables**:
   Copy the `.env.example` file to `.env` and fill in your API keys:
   - `PROVIDER`: LLM provider (OpenAI, OpenRouter, or Ollama)
   - `BASE_URL`: API base URL for your LLM provider
   - `LLM_API_KEY`: Your API key for the LLM provider
   - `MODEL_CHOICE`: The LLM model to use (e.g., gpt-4o-mini)

4. **Configure MCP servers**:
   Create or modify `mcp_config.json` (use `mcp_config_example.json` as an example) to define your MCP servers. Example:
   ```json
   {
     "mcpServers": {
       "serverName": {
         "command": "npx",
         "args": ["-y", "@modelcontextprotocol/server-filesystem", "./"],
         "env": {}
       }
     }
   }
   ```

5. **Run the CLI application**:
   ```bash
   python pydantic_mcp_agent.py
   ```

## 💻 Code Example: MCP Tool Conversion

Here's how the `mcp_client.py` converts MCP tools to Pydantic AI tools:

```python
def create_tool_instance(self, tool: MCPTool) -> PydanticTool:
    """Initialize a Pydantic AI Tool from an MCP Tool."""
    async def execute_tool(**kwargs: Any) -> Any:
        return await self.session.call_tool(tool.name, arguments=kwargs)

    async def prepare_tool(ctx: RunContext, tool_def: ToolDefinition) -> ToolDefinition | None:
        tool_def.parameters_json_schema = tool.inputSchema
        return tool_def
    
    return PydanticTool(
        execute_tool,
        name=tool.name,
        description=tool.description or "",
        takes_ctx=False,
        prepare=prepare_tool
    )
```

## 🔄 How to Extend

You can extend this project by:

1. **Adding more MCP servers**: Modify `mcp_config.json` to include additional servers
2. **Creating custom tools**: Develop your own MCP servers
3. **Enhancing the agent**: Modify the agent configuration for specialized use cases
4. **Adding memory capabilities**: Integrate with memory systems like Mem0

## 🔗 Learn More

- [Model Context Protocol (MCP)](https://github.com/modelcontextprotocol/mcp)
- [Pydantic AI](https://github.com/pydantic/pydantic-ai)
- [Comparison with n8n approach](https://medium.com/@yourusername)

---

<div align="center">
  <p>Created with ❤️ by <a href="https://github.com/MuLIAICHI">MuLIAICHI</a></p>
</div>