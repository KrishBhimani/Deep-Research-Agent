# 🧠🤖 Deep Research Agent

A sophisticated AI research platform that combines a powerful "Deep Agent" backend with an intuitive web interface. Built on LangGraph and Next.js, this system enables complex, multi-step research tasks with sub-agent spawning, task management, and file system capabilities.

![Deep Agents Architecture](deepagents/deep_agents.png)

## 🏗️ Architecture Overview

The Deep Research Agent implements a **"Deep Agent" architecture** inspired by Claude Code, featuring:

- **Planning System**: Built-in todo management for complex task orchestration
- **Sub-Agent System**: Hierarchical agents for specialized tasks and context quarantine
- **Virtual File System**: Mock filesystem for safe, concurrent operations
- **Rich Web Interface**: Real-time chat interface with task tracking and file management

## 📁 Project Structure

```
Deep-Research-Agent/
├── deepagents/              # Backend - Python Deep Agent Framework
│   ├── src/deepagents/      # Core agent implementation
│   ├── examples/research/   # Research agent example
│   └── pyproject.toml       # Python dependencies
├── deep-agents-ui/          # Frontend - Next.js Web Interface
│   ├── src/app/            # Next.js app directory
│   ├── src/components/     # React components
│   └── package.json        # Node.js dependencies
└── README.md               # This file
```

## 🚀 Quick Start

### Prerequisites

- **Python 3.11+** for the backend
- **Node.js 18+** for the frontend
- **API Keys**: Anthropic Claude API key (and optionally Tavily for web search)

### Backend Setup

1. **Navigate to the research example directory:**
   ```bash
   cd Deep-Research-Agent/deepagents/examples/research
   ```

2. **Install dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

3. **Set environment variables:**
   Copy the example environment file and update with your API keys:
   ```bash
   cp .env.example .env
   ```
   Then edit `.env` with your actual API keys:
   ```env
   ANTHROPIC_API_KEY=your-anthropic-api-key
   TAVILY_API_KEY=your-tavily-api-key  # Optional for web search
   ```

4. **Start the LangGraph development server:**
   ```bash
   langgraph dev
   ```

### Frontend Setup

1. **Navigate to the frontend directory:**
   ```bash
   cd Deep-Research-Agent/deep-agents-ui
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Set environment variables:**
   Copy the example environment file and update with your configuration:
   ```bash
   cp .env.example .env.local
   ```
   Then edit `.env.local` with your actual configuration:
   ```env
   NEXT_PUBLIC_DEPLOYMENT_URL=http://127.0.0.1:2024  # or your server url
   NEXT_PUBLIC_AGENT_ID=<your agent ID from langgraph.json>
   ```

4. **Start the development server:**
   ```bash
   npm run dev
   ```

5. **Open your browser:**
   Navigate to `http://localhost:3000`

## 🛠️ Technology Stack

### Backend (deepagents)
- **Framework**: LangGraph (v0.2.6+) - Stateful multi-actor LLM applications
- **Language**: Python 3.11+
- **LLM Integration**: LangChain with Anthropic Claude (claude-sonnet-4-20250514)
- **Architecture**: React Agent pattern with state management

### Frontend (deep-agents-ui)
- **Framework**: Next.js 15.4.6 with React 19
- **Language**: TypeScript 5
- **Styling**: Tailwind CSS 4 + SCSS modules
- **UI Components**: Radix UI primitives
- **State Management**: LangGraph SDK React hooks
- **Real-time**: WebSocket streaming via LangGraph SDK

## ✨ Key Features

### Backend Features
- **🎯 Planning System**: Automatic todo creation and management for complex tasks
- **🤖 Sub-Agent Spawning**: Specialized agents for research, critique, and custom tasks
- **📁 Virtual File System**: Safe, concurrent file operations without filesystem conflicts
- **🔄 State Management**: Persistent state across tool calls and agent interactions
- **🛠️ Extensible Tools**: Easy integration of custom tools and APIs
- **📊 MCP Support**: Compatible with Model Context Protocol tools

### Frontend Features
- **💬 Real-time Chat**: Streaming conversation interface with the deep agent
- **📋 Task Tracking**: Live todo list updates and progress monitoring
- **📄 File Management**: View and manage virtual files created by agents
- **🔧 Tool Call Visualization**: Real-time display of agent tool usage
- **🧵 Thread History**: Persistent conversation threads
- **👥 Sub-Agent Monitoring**: Track sub-agent activities and outputs
- **📱 Responsive Design**: Modern, mobile-friendly interface

## 🎯 Use Cases

### Research & Analysis
- **Academic Research**: Comprehensive literature reviews and analysis
- **Market Research**: Industry analysis with multiple data sources
- **Technical Documentation**: In-depth technical guides and reports
- **Competitive Analysis**: Multi-faceted business intelligence

### Content Creation
- **Report Writing**: Structured, well-researched documents
- **Article Generation**: Long-form content with citations
- **Documentation**: Technical and user documentation
- **Presentations**: Research-backed presentation materials

### Development & Planning
- **Project Planning**: Complex project breakdown and task management
- **Code Analysis**: Multi-file codebase analysis and documentation
- **System Design**: Architecture planning and documentation
- **Process Optimization**: Workflow analysis and improvement

## 🔧 Configuration

### Backend Configuration

**Custom Models:**
```python
from deepagents import create_deep_agent
from langchain_openai import ChatOpenAI

# Use OpenAI instead of Claude
model = ChatOpenAI(model="gpt-4")
agent = create_deep_agent(
    tools=[your_tools],
    instructions="Your instructions",
    model=model
)
```

**Custom Sub-Agents:**
```python
custom_subagent = {
    "name": "data-analyst",
    "description": "Specialized in data analysis tasks",
    "prompt": "You are a data analysis expert...",
    "tools": ["data_processing_tool"]
}

agent = create_deep_agent(
    tools=[your_tools],
    instructions="Your instructions",
    subagents=[custom_subagent]
)
```

### Frontend Configuration

**Environment Variables:**
```env
# Required
NEXT_PUBLIC_DEPLOYMENT_URL=http://127.0.0.1:2024
NEXT_PUBLIC_AGENT_ID=research

# Optional
NEXT_PUBLIC_AUTH_ENABLED=false
```

**Deployment Configuration:**
Edit `src/lib/environment/deployments.ts` for custom deployment settings.

## 📚 API Reference

### Backend API

**Creating a Deep Agent:**
```python
from deepagents import create_deep_agent

agent = create_deep_agent(
    tools=[list_of_tools],           # Required: Tools for the agent
    instructions="System prompt",    # Required: Agent instructions
    model=custom_model,             # Optional: Custom LLM model
    subagents=[custom_subagents],   # Optional: Specialized sub-agents
    state_schema=CustomState        # Optional: Custom state schema
)
```

**Built-in Tools:**
- `write_todos`: Task planning and management
- `write_file`: Create files in virtual filesystem
- `read_file`: Read files with line numbers and pagination
- `edit_file`: Edit files with string replacement
- `ls`: List files in virtual filesystem
- `task`: Delegate to sub-agents

### Frontend API

**Chat Hook:**
```typescript
const { messages, isLoading, sendMessage, stopStream } = useChat(
  threadId,
  setThreadId,
  onTodosUpdate,
  onFilesUpdate
);
```

**Type Definitions:**
```typescript
interface ToolCall {
  id: string;
  name: string;
  args: any;
  result?: string;
  status: "pending" | "completed" | "error";
}

interface TodoItem {
  id: string;
  content: string;
  status: "pending" | "in_progress" | "completed";
}
```

## 🔄 Development Workflow

### Backend Development
1. **Create Custom Tools**: Implement functions with proper type hints
2. **Define Sub-Agents**: Create specialized agents for specific tasks
3. **Test Locally**: Use the research example as a starting point
4. **Deploy**: Use LangGraph Cloud or custom deployment

### Frontend Development
1. **Component Development**: Create React components in `src/app/components/`
2. **Styling**: Use SCSS modules + Tailwind CSS
3. **State Management**: Leverage LangGraph SDK hooks
4. **Testing**: Test with local backend instance

## 🚀 Deployment

### Backend Deployment
```bash
# Using LangGraph Cloud
langgraph deploy --config langgraph.json

# Or custom deployment
python -m uvicorn your_app:app --host 0.0.0.0 --port 2024
```

### Frontend Deployment
```bash
# Build for production
npm run build

# Deploy to Vercel, Netlify, or custom hosting
npm run start
```

## 🤝 Contributing

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/amazing-feature`
3. **Make your changes**: Follow the existing code style
4. **Add tests**: Ensure your changes are tested
5. **Commit your changes**: `git commit -m 'Add amazing feature'`
6. **Push to the branch**: `git push origin feature/amazing-feature`
7. **Open a Pull Request**

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](deepagents/LICENSE) file for details.

## 🙏 Acknowledgements

- **Claude Code**: Primary inspiration for the Deep Agent architecture
- **LangGraph**: Powerful framework for building stateful LLM applications
- **LangChain**: Comprehensive LLM integration library
- **Next.js**: React framework for the web interface
- **Radix UI**: Accessible UI component primitives

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/your-repo/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-repo/discussions)
- **Documentation**: See individual README files in `deepagents/` and `deep-agents-ui/`

---
