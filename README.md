# 🚀 MCP Practice Project

A comprehensive **Model Context Protocol (MCP)** implementation demonstrating multi-server architecture with LangChain integration. This project showcases how to build and connect multiple MCP servers to create powerful AI agent workflows.

[![Python](https://img.shields.io/badge/Python-3.13+-blue.svg)](https://python.org)
[![MCP](https://img.shields.io/badge/MCP-1.0+-green.svg)](https://modelcontextprotocol.io)


## 📋 Table of Contents

- [What is MCP?](#what-is-mcp)
- [Project Architecture](#project-architecture)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [API Reference](#api-reference)
- [Examples](#examples)
- [Contributing](#contributing)
- [License](#license)

## 🤔 What is MCP?

The **Model Context Protocol (MCP)** is an open standard that enables AI assistants to securely connect to external data sources and tools. It provides a standardized way for AI models to interact with various systems, databases, and services.

### Key Benefits:
- 🔒 **Secure**: Standardized authentication and authorization
- 🔌 **Modular**: Easy to add new tools and data sources
- 🚀 **Scalable**: Support for multiple servers and clients
- 🛠️ **Extensible**: Rich ecosystem of tools and integrations

## 🏗️ Project Architecture

```
MCPPractice/
├── 📁 .venv/                 # Virtual environment
├── 📄 client.py              # Multi-server MCP client with LangChain
├── 📄 mathserver.py          # Math operations MCP server
├── 📄 weather.py             # Weather service MCP server
├── 📄 main.py                # Basic MCP server (legacy)
├── 📄 pyproject.toml         # Project dependencies
├── 📄 requirement.txt        # Additional requirements
└── 📄 README.md              # This file
```

### Architecture Flow:
```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   LangChain     │    │  Multi-Server    │    │   MCP Servers   │
│   Agent         │◄──►│  MCP Client      │◄──►│                 │
│                 │    │                  │    │ • Math Server   │
│ • ChatGroq      │    │ • Tool Discovery │    │ • Weather Server│
│ • ReAct Agent   │    │ • Request Routing│    │                 │
└─────────────────┘    └──────────────────┘    └─────────────────┘
```

## ✨ Features

### 🔢 Math Server (`mathserver.py`)
- **Addition**: Add two numbers
- **Multiplication**: Multiply two numbers  
- **Subtraction**: Subtract two numbers
- **Transport**: stdio (command-line interface)

### 🌤️ Weather Server (`weather.py`)
- **Weather Lookup**: Get weather information for any location
- **Transport**: streamable-http (web interface)
- **Async Support**: Non-blocking weather requests

### 🤖 Multi-Server Client (`client.py`)
- **LangChain Integration**: Seamless AI agent workflows
- **Tool Discovery**: Automatic tool detection from multiple servers
- **Groq Integration**: High-performance LLM inference
- **ReAct Agent**: Reasoning and acting capabilities

## 📋 Prerequisites

Before you begin, ensure you have the following installed:

- **Python 3.13+** ([Download](https://python.org/downloads/))
- **uv** package manager ([Install Guide](https://docs.astral.sh/uv/getting-started/installation/))
- **Git** ([Download](https://git-scm.com/downloads))

### API Keys Required:
- **Groq API Key** ([Get one here](https://console.groq.com/))

## 🚀 Installation

### Step 1: Clone the Repository
```bash
git clone https://github.com/yourusername/MCPPractice.git
cd MCPPractice
```

### Step 2: Install Dependencies
```bash
# Install uv if you haven't already
curl -LsSf https://astral.sh/uv/install.sh | sh

# Activate uv in your shell
source $HOME/.local/bin/env

# Install project dependencies
uv sync
```

### Step 3: Set Up Environment Variables
```bash
# Create .env file
echo "GROQ_API_KEY=your_groq_api_key_here" > .env
```

## ⚙️ Configuration

### Environment Variables
Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key_here
```

### Server Configuration
The client automatically configures two MCP servers:

```python
{
    "math": {
        "command": "python",
        "args": ["mathserver.py"],
        "transport": "stdio"
    },
    "weather": {
        "url": "http://localhost:8000/mcp",
        "transport": "streamable-http"
    }
}
```

## 🎯 Usage

### Quick Start

1. **Activate Virtual Environment**:
   ```bash
   source .venv/bin/activate
   ```

2. **Start Weather Server** (in a separate terminal):
   ```bash
   python weather.py
   ```

3. **Run the Multi-Server Client**:
   ```bash
   python client.py
   ```

### Individual Server Usage

#### Math Server
```bash
# Run math server directly
python mathserver.py

# Test with MCP client
echo '{"jsonrpc": "2.0", "id": 1, "method": "tools/call", "params": {"name": "add", "arguments": {"a": 5, "b": 3}}}' | python mathserver.py
```

#### Weather Server
```bash
# Start weather server
python weather.py

# Server will be available at http://localhost:8000/mcp
```

## 📚 API Reference

### Math Server Tools

| Tool | Description | Parameters | Return Type |
|------|-------------|------------|-------------|
| `add` | Add two numbers | `a: int, b: int` | `int` |
| `multiply` | Multiply two numbers | `a: int, b: int` | `int` |
| `subtract` | Subtract two numbers | `a: int, b: int` | `int` |

### Weather Server Tools

| Tool | Description | Parameters | Return Type |
|------|-------------|------------|-------------|
| `get_weather` | Get weather for location | `location: str` | `str` |

## 💡 Examples

### Example 1: Math Operations
```python
# The client automatically uses the math server
response = await agent.ainvoke({
    "messages": [{"role": "user", "content": "What is (3 + 5) * 12?"}]
})
# Output: 96
```

### Example 2: Weather Query
```python
# The client automatically uses the weather server
response = await agent.ainvoke({
    "messages": [{"role": "user", "content": "What's the weather in NYC?"}]
})
# Output: The weather in NYC is sunny
```

### Example 3: Complex Workflow
```python
# The agent can combine tools from multiple servers
response = await agent.ainvoke({
    "messages": [{"role": "user", "content": "If it's sunny in NYC, calculate 15 * 8"}]
})
# Agent will check weather first, then perform math if conditions are met
```

## 🛠️ Development

### Adding New Tools

1. **Create a new MCP server**:
   ```python
   from mcp.server.fastmcp import FastMCP
   
   mcp = FastMCP("MyServer")
   
   @mcp.tool()
   def my_tool(param: str) -> str:
       """My custom tool"""
       return f"Processed: {param}"
   
   if __name__ == "__main__":
       mcp.run(transport="stdio")
   ```

2. **Add to client configuration**:
   ```python
   client = MultiServerMCPClient({
       "myserver": {
           "command": "python",
           "args": ["myserver.py"],
           "transport": "stdio"
       }
   })
   ```

### Project Structure
- Use `uv` for dependency management
- Follow PEP 8 style guidelines
- Add type hints for all functions
- Include docstrings for all public methods

## 🤝 Contributing

We welcome contributions! Please follow these steps:

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/amazing-feature`
3. **Commit your changes**: `git commit -m 'Add amazing feature'`
4. **Push to the branch**: `git push origin feature/amazing-feature`
5. **Open a Pull Request**

### Development Setup
```bash
# Install development dependencies
uv sync --dev

# Run tests (when available)
uv run pytest

# Format code
uv run black .
uv run isort .
```

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Model Context Protocol](https://modelcontextprotocol.io/) - The foundation of this project
- [LangChain](https://langchain.com/) - AI application framework
- [Groq](https://groq.com/) - High-performance LLM inference
- [FastMCP](https://github.com/jlowin/fastmcp) - Simplified MCP server creation

## 📞 Support

- 📧 **Email**: your.email@example.com
- 🐛 **Issues**: [GitHub Issues](https://github.com/yourusername/MCPPractice/issues)
- 💬 **Discussions**: [GitHub Discussions](https://github.com/yourusername/MCPPractice/discussions)

---

⭐ **Star this repository** if you found it helpful!

**Made with ❤️ for the MCP community**