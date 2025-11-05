# 🧠🤖 Deep Agents

Using an LLM to call tools in a loop is the simplest form of an agent. 
This architecture, however, can yield agents that are "shallow" and fail to plan and act over longer, more complex tasks. 
Applications like "Deep Research", "Manus", and "Claude Code" have gotten around this limitation by implementing a combination of four things:
a **planning tool**, **sub agents**, access to a **file system**, and a **detailed prompt**.

<img src="deep_agents.png" alt="deep agent" width="600"/>

`deepagents` is a Python package that implements these in a general purpose way so that you can easily create a Deep Agent for your application.

**Acknowledgements: This project was primarily inspired by Claude Code, and initially was largely an attempt to see what made Claude Code general purpose, and make it even more so.**

## Installation

```bash
# Make sure you have Python 3.11+ installed
pip install deepagents
```

### Prerequisites
- Python 3.11 or higher
- An Anthropic API key (for Claude models)
- Optional: Tavily API key (for web search functionality)

## Usage

### Basic Example

This example shows how to create a simple research agent with web search capability:

```python
import os
from typing import Literal

# First, install the required package
# pip install tavily-python

from tavily import TavilyClient
from deepagents import create_deep_agent

# Initialize the Tavily client with your API key
# You can get an API key from https://tavily.com/
tavily_client = TavilyClient(api_key=os.environ["TAVILY_API_KEY"])

# Define a search tool for the agent to use
def internet_search(
    query: str,
    max_results: int = 5,
    topic: Literal["general", "news", "finance"] = "general",
    include_raw_content: bool = False,
):
    """Run a web search"""
    return tavily_client.search(
        query,
        max_results=max_results,
        include_raw_content=include_raw_content,
        topic=topic,
    )

# Create instructions for the agent
research_instructions = """You are an expert researcher. Your job is to conduct thorough research, and then write a polished report.

You have access to a few tools.

## `internet_search`

Use this to run an internet search for a given query. You can specify the number of results, the topic, and whether raw content should be included.
"""

# Create the agent with the search tool and instructions
agent = create_deep_agent(
    [internet_search],
    research_instructions,
)

# Invoke the agent with a user query
result = agent.invoke({"messages": [{"role": "user", "content": "what is langgraph?"}]})
```

### Advanced Example

For a more complex implementation, see [examples/research/research_agent.py](examples/research/research_agent.py).

The agent created with `create_deep_agent` is a LangGraph graph, so you can use all LangGraph features like streaming, human-in-the-loop interactions, memory, and LangGraph Studio integration.

## Creating a Custom Deep Agent

The `create_deep_agent` function accepts several parameters to customize your agent:

### Required Parameters

#### 1. `tools`
A list of functions or LangChain `@tool` objects that the agent can use.

```python
# Example of defining a custom tool
@tool
def search_database(query: str) -> str:
    """Search the database for information related to the query."""
    # Implementation here
    return results
    
# Pass tools to the agent
agent = create_deep_agent([search_database, another_tool], instructions)
```

#### 2. `instructions`
A string containing instructions for the agent. This defines the agent's behavior and capabilities.

```python
instructions = """You are a data analysis expert. Your job is to analyze data and provide insights.
You have access to database search tools and can create visualizations."""

agent = create_deep_agent(tools, instructions)
```

### Optional Parameters

#### 3. `subagents`
A list of dictionaries defining specialized subagents that the main agent can delegate tasks to.

```python
data_analysis_subagent = {
    "name": "data-analyst",  # How the main agent will call this subagent
    "description": "Specialized in data analysis tasks",  # Description shown to the main agent
    "prompt": "You are a data analysis expert...",  # Instructions for this subagent
    "tools": ["search_database"]  # Tools this subagent can access (optional)
}

agent = create_deep_agent(
    tools,
    instructions,
    subagents=[data_analysis_subagent]
)
```

#### 4. `model`
By default, `deepagents` uses Claude Sonnet 4. You can specify a different model:

```python
# Using OpenAI's GPT-4
from langchain_openai import ChatOpenAI

model = ChatOpenAI(model="gpt-4")
agent = create_deep_agent(
    tools,
    instructions,
    model=model
)

# Using Ollama (requires additional packages)
# pip install langchain langchain-ollama
from langchain_ollama import ChatOllama

model = ChatOllama(model="llama3")
agent = create_deep_agent(
    tools,
    instructions,
    model=model
)
```

## Built-in Features

The `deepagents` package includes several powerful built-in features that make it effective for complex tasks:

### 1. Sophisticated System Prompt

The package includes a [detailed system prompt](src/deepagents/prompts.py) inspired by Claude Code. This prompt:
- Provides instructions for using all built-in tools
- Guides the agent in planning and executing complex tasks
- Can be partially customized through the `instructions` parameter

### 2. Planning Tool

A built-in planning tool allows the agent to:
- Create and manage todo lists
- Break down complex tasks into manageable steps
- Track progress on multi-stage tasks

Example of how the agent uses this:
```
Agent: I'll create a plan for researching quantum computing.
[Agent uses planning tool]
1. Define quantum computing basics
2. Research current quantum computing technologies
3. Identify major companies and research institutions
4. Analyze recent breakthroughs
5. Compile findings into a comprehensive report
```

### 3. Virtual File System

The agent has access to a simulated file system with these tools:
- `ls`: List files in the virtual filesystem
- `read_file`: Read content from a file
- `write_file`: Create or overwrite a file
- `edit_file`: Make changes to an existing file

This virtual file system is isolated from your actual file system for safety and allows multiple agents to work concurrently without conflicts.

```python
# How to access files created by the agent
agent = create_deep_agent(...)
result = agent.invoke({"messages": [{"role": "user", "content": "Create a report on AI trends"}]})

# Access files created by the agent
report_content = result["files"].get("ai_trends_report.md", "File not found")
print(report_content)
```

### 4. Sub-Agent System

The agent can delegate tasks to specialized sub-agents:
- A general-purpose sub-agent is always available
- Custom sub-agents can be defined for specific tasks
- Sub-agents help with "context quarantine" to manage conversation context efficiently

This architecture allows the main agent to maintain focus while delegating specialized tasks to appropriate sub-agents.

## Using MCP Tools

The `deepagents` library supports Model Context Protocol (MCP) tools through the [Langchain MCP Adapter library](https://github.com/langchain-ai/langchain-mcp-adapters).

### Setup

First, install the required package:
```bash
pip install langchain-mcp-adapters
```

### Example Implementation

```python
import asyncio
from langchain_mcp_adapters.client import MultiServerMCPClient
from deepagents import create_deep_agent

async def main():
    # Initialize the MCP client
    mcp_client = MultiServerMCPClient(
        # Configure with your MCP servers
        servers=["http://localhost:8000"]
    )
    
    # Get available tools from MCP servers
    mcp_tools = await mcp_client.get_tools()

    # Create a deep agent with MCP tools
    agent = create_deep_agent(
        tools=mcp_tools,
        instructions="You are an assistant with access to various external tools."
    )

    # Stream the agent's responses
    async for chunk in agent.astream(
        {"messages": [{"role": "user", "content": "What's the weather in New York?"}]},
        stream_mode="values"
    ):
        if "messages" in chunk:
            chunk["messages"][-1].pretty_print()

# Run the async function
asyncio.run(main())
```

## Getting Help

If you encounter issues or have questions:
- Check the [examples directory](examples/) for implementation examples
- Review the [source code](src/deepagents/) for detailed implementation details
- File an issue on the GitHub repository

## Roadmap
- [ ] Allow users to customize full system prompt
- [ ] Improve code quality (type hinting, docstrings, formatting)
- [ ] Enhance the virtual filesystem with subdirectory support
- [ ] Create an example of a deep coding agent
- [ ] Benchmark the [deep research agent example](examples/research/research_agent.py)
- [ ] Add human-in-the-loop support for tools
