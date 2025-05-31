# DevTo Agent

A comprehensive multi-agent system for interacting with Dev.to (DevTo) platform, built using Google ADK (Agent Development Kit) and Model Context Protocol (MCP). This project enables automated content creation, article management, and user profile interactions with DevTo through both Agent-to-Agent (A2A) communication and MCP server implementations.

## 🚀 Project Overview

This project implements a sophisticated agent architecture that can:
- Fetch and manage DevTo articles by tags or authors
- Generate and post markdown content to DevTo
- Retrieve user profiles and reading lists
- Manage article comments and followers
- Provide both SSE (Server-Sent Events) and STDIO interfaces for different integration needs

## 📋 Table of Contents

- [Architecture Overview](#architecture-overview)
- [Project Structure](#project-structure)
- [Component Details](#component-details)
- [Setup and Installation](#setup-and-installation)
- [Usage](#usage)
- [API Reference](#api-reference)
- [Configuration](#configuration)
- [Contributing](#contributing)

## 🏗️ Architecture Overview

The project follows a modular architecture with three main communication patterns:

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Client App    │───▶│  A2A Server     │───▶│   DevTo API     │
│   (main.py)     │    │ (devto_agent)   │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       ▲
         │                       ▼                       │
         │              ┌─────────────────┐              │
         └─────────────▶│   MCP Server    │──────────────┘
                        │ (SSE/STDIO)     │
                        └─────────────────┘
```

### Key Components:

1. **A2A Agent Server**: High-level agent interface using Google ADK
2. **MCP Servers**: Low-level tool interface using Model Context Protocol
3. **DevTo Service**: Direct API integration with Dev.to
4. **Tool Connectors**: Bridge between agents and MCP servers

## 📁 Project Structure

```
devto-agent/
├── a2a_servers/              # Agent-to-Agent server implementations
│   ├── agent_server/         # Individual agent definitions
│   │   ├── devto_agent.py   # Main DevTo agent server
│   │   ├── host_agent.py    # Host agent coordinator
│   │   └── utils.py         # Agent utilities
│   ├── agents/              # Agent base classes and implementations
│   │   ├── adk_agent.py     # Google ADK agent wrapper
│   │   └── utils/           # Agent utilities
│   └── common/              # Shared components
│       ├── client/          # A2A client implementations
│       ├── server/          # A2A server implementations
│       └── types.py         # Common type definitions
├── connector/               # Tool connectors
│   └── tools/
│       └── devto_tools.py   # DevTo MCP tool connector
├── mcp_servers/             # Model Context Protocol servers
│   ├── sse/                 # Server-Sent Events implementation
│   │   └── devto_server.py  # SSE-based MCP server
│   └── stdio/               # Standard I/O implementation
│       └── devto_server.py  # STDIO-based MCP server
├── services/                # Core business logic
│   └── devto_service.py     # DevTo API service wrapper
├── test/                    # Test files
├── main.py                  # Main client application
└── pyproject.toml          # Project dependencies
```

## 🔧 Component Details

### 1. A2A Servers (`a2a_servers/`)

**Purpose**: Implements high-level agent interfaces using Google ADK framework.

#### Key Files:
- **`devto_agent.py`**: Main DevTo agent that handles:
  - Content generation for DevTo articles
  - Article fetching and management
  - User profile operations
  - Article posting with markdown support

- **`host_agent.py`**: Coordinator agent that can manage multiple sub-agents

- **`adk_agent.py`**: Wrapper class that integrates Google ADK with MCP tools

### 2. MCP Servers (`mcp_servers/`)

**Purpose**: Provides low-level tool interfaces using Model Context Protocol.

#### SSE Implementation (`sse/devto_server.py`):
```python
# Creates a Starlette web application with:
# - GET /sse: Server-Sent Events endpoint for real-time communication
# - POST /messages/: Message posting endpoint for client commands
```

#### STDIO Implementation (`stdio/devto_server.py`):
- Standard input/output based communication
- Suitable for command-line integrations
- Synchronous communication pattern

### 3. DevTo Service (`services/devto_service.py`)

**Purpose**: Direct integration with DevTo API.

#### Core Methods:
```python
class DevToService:
    def get_articles(page, per_page)           # Fetch paginated articles
    def get_articles_by_tag(tag)               # Filter by single tag
    def get_articles_by_tags(tags)             # Filter by multiple tags
    def get_article_content(article_id)        # Get full article content
    def post_article(title, body, tags)        # Create new article
    def get_user()                             # Current user info
    def get_user_articles()                    # User's articles
    def get_user_reading_list()                # User's saved articles
    def get_article_comments(article_id)       # Article comments
```

### 4. Tool Connectors (`connector/tools/`)

**Purpose**: Bridge between A2A agents and MCP servers.

#### DevTo Tools Connector:
- Establishes connection to MCP server
- Provides async tool interface for agents
- Handles connection lifecycle management

## ⚙️ Setup and Installation

### Prerequisites
- Python 3.10 or higher
- DevTo API key
- Google ADK access (for agent functionality)

### Installation Steps

1. **Clone the repository**:
   ```powershell
   git clone <repository-url>
   cd devto-agent
   ```

2. **Install dependencies**:
   ```powershell
   pip install -e .
   ```

3. **Environment Configuration**:
   Create a `.env` file in the project root:
   ```env
   DEVTO_API_KEY=your_devto_api_key_here
   DEVTO_BASE_URL=https://dev.to/api
   GOOGLE_API_KEY=your_google_api_key_here
   ```

4. **Verify Installation**:
   ```powershell
   python -c "import services.devto_service; print('Installation successful')"
   ```

## 🚀 Usage

### 1. Start the MCP Server (SSE)

```powershell
cd mcp_servers/sse
python devto_server.py
```
Server will start on `http://localhost:8000`

### 2. Start the A2A Agent Server

```powershell
cd a2a_servers/agent_server
python devto_agent.py
```
Agent server will start on `http://localhost:11000`

### 3. Run the Client Application

```powershell
python main.py
```

### Example Interactions

#### Generate and Post Article:
```python
# The agent can handle requests like:
"Create a technical article about Python async programming with relevant tags"

# The agent will:
# 1. Generate markdown content
# 2. Add appropriate tags (python, async, programming)
# 3. Post to DevTo
# 4. Return the published article URL
```

#### Fetch Articles by Tags:
```python
# Request: "Get the latest Python articles"
# Agent will fetch articles tagged with 'python' and return summaries
```

## 📚 API Reference

### A2A Agent Skills

The DevTo agent provides these skills:

1. **SKILL_DEVTO_CONTENT**: Generate markdown content for DevTo articles
2. **SKILL_DEVTO_ARTICLES**: Fetch articles with or without tag filters
3. **SKILL_DEVTO_USER_INFO**: Retrieve user profiles and statistics
4. **SKILL_POST_DEVTO_ARTICLE**: Create and publish articles

### MCP Tools

Available through the MCP server:

- `get_articles(page, per_page)`: Paginated article retrieval
- `get_articles_by_tag(tag)`: Tag-based filtering
- `get_article_content(article_id)`: Full article content
- `post_article(title, body, tags)`: Article publishing
- `get_user()`: User profile information
- `get_user_reading_list()`: Saved articles

## 🔧 Configuration

### Agent Configuration

Modify `devto_agent.py` to customize:
- Model selection (`MODEL = "gemini-2.0-flash"`)
- Agent instructions and behavior
- Skills and capabilities
- Server host/port settings

### MCP Server Configuration

Adjust `devto_server.py` for:
- Server endpoints and routes
- CORS settings
- Debug mode
- Message handling patterns

### DevTo Service Configuration

Update `devto_service.py` for:
- API rate limiting
- Error handling strategies
- Response caching
- Request timeouts

## 🔍 Troubleshooting

### Common Issues

1. **Module Import Errors**:
   ```python
   # Ensure proper sys.path configuration
   sys.path.append(os.path.abspath(os.path.join(os.path.dirname(__file__), '..', '..')))
   ```

2. **MCP Connection Failures**:
   - Verify MCP server is running on correct port
   - Check firewall settings
   - Ensure proper async/await usage

3. **DevTo API Errors**:
   - Validate API key in `.env` file
   - Check rate limiting
   - Verify API endpoint URLs

### Debug Mode

Enable debug logging:
```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Submit a pull request

### Development Guidelines

- Follow PEP 8 style guidelines
- Add docstrings for all public methods
- Include error handling for API calls
- Write tests for new features
- Update documentation as needed

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🙏 Acknowledgments

- Google ADK team for the agent development framework
- Model Context Protocol specification contributors
- DevTo API team for comprehensive API access
- Starlette framework for SSE implementation

---

For more detailed information about specific components, refer to the inline documentation in each module.