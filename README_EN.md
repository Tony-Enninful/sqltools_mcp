# SQLTools MCP

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![MCP Protocol](https://img.shields.io/badge/MCP-1.0.0-orange.svg)](https://modelcontextprotocol.io)

[English] | [[中文](README.md)]

**SQLTools MCP** is an all-in-one database access service built on the [Model Context Protocol (MCP)](https://modelcontextprotocol.io).

The core value is: **Stop installing separate MCP servers for every single database. This one service provides AI assistants with unified support for MySQL, Postgres, SQL Server, Dameng (DM8), and SQLite.**

## ✨ Features

- 🔌 **Unified Database Support**:
  - **MySQL** / **MariaDB**
  - **PostgreSQL**
  - **SQL Server (MSSQL)**
  - **Dameng (DM8)** (Major Chinese enterprise DB)
  - **SQLite** (Local file DB)
- 🔄 **Hot Switching**: Switch between different database environments or types anytime within the same AI session via `connect_database`.
- 🛡️ **Production Ready**: Built-in SQL injection protection and warning prompts for destructive operations (DROP, TRUNCATE, etc.).
- 📊 **Optimized UX**: Supports pagination for large tables and provides smart fix suggestions for connection errors.

## 📦 Quick Install

```bash
# Clone the repository
git clone https://github.com/huangzt/sqltools_mcp
cd sqltools-mcp

# Install in editable mode
pip install -e .

# Install required drivers as needed
pip install pymysql          # MySQL
pip install psycopg2-binary  # PostgreSQL
pip install pymssql          # SQL Server
pip install jaydebeapi       # DM8 (Requires Java/JRE)
```

## ⚙️ Configuration Guide

### Environment Variables
You can set the following environment variables when starting the MCP service for auto-connection:

| Variable | Description | Default |
|----------|-------------|---------|
| `DB_TYPE` | Database type (mysql, postgres, mssql, dm8, sqlite) | `sqlite` |
| `DB_HOST` | Database host address | `localhost` |
| `DB_PORT` | Port number (0 uses protocol default) | `0` |
| `DB_USER` | Database username | - |
| `DB_PASSWORD` | Database password | - |
| `DB_NAME` | Database name (Absolute file path for SQLite) | - |

## 🚀 AI Client Configuration

### 1. Google Antigravity
Edit `~/.gemini/antigravity/mcp_config.json`:

```json
{
  "mcpServers": {
    "sqltools": {
      "command": "python",
      "args": ["-m", "sqltools_mcp.server"],
      "env": {
        "DB_TYPE": "sqlite",
        "DB_NAME": "/path/to/your/db.sqlite"
      }
    }
  }
}
```

### 2. Claude Desktop
Edit configuration file:
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **Windows**: `%APPDATA%\Claude\claude_desktop_config.json`

```json
{
  "mcpServers": {
    "sqltools": {
      "command": "python",
      "args": ["-m", "sqltools_mcp.server"],
      "env": {
        "DB_TYPE": "mysql",
        "DB_HOST": "localhost",
        "DB_USER": "root",
        "DB_PASSWORD": "password",
        "DB_NAME": "test"
      }
    }
  }
}
```

### 3. Cursor / Windsurf
Add in **Settings -> Features -> MCP** (Cursor) or **Settings -> MCP** (Windsurf):
- **Name**: `sqltools`
- **Type**: `command`
- **Command**: `python -m sqltools_mcp.server`

### 4. Roo Code (formerly Roo Cline)
Open Roo Code settings in VS Code, and add to **MCP Config**:

```json
"sqltools": {
  "command": "python",
  "args": ["-m", "sqltools_mcp.server"]
}
```

### 5. Zed
Edit `~/.zed/settings.json`:

```json
{
  "context_protocols": [
    {
      "mcp": {
        "servers": {
          "sqltools": {
            "command": "python",
            "args": ["-m", "sqltools_mcp.server"]
          }
        }
      }
    }
  ]
}
```

### 6. Continue (VS Code / JetBrains)
Edit `~/.continue/config.json`:

```json
{
  "mcpServers": [
    {
      "name": "sqltools",
      "command": "python",
      "args": ["-m", "sqltools_mcp.server"]
    }
  ]
}
```

## 🛠️ Tools

AI models can interact with your databases using these tools:

### 1. `connect_database`
Connect or switch to a target database.
- **Parameters**: `dbtype` (required), `host`, `port`, `username`, `password`, `dbname`.
- **Features**: Automatically disconnects old connections and verifies terminal availability.

### 2. `execute_sql`
Execute SQL queries.
- **Parameters**: `query` (required), `timeout`.
- **Features**: Supports SELECT and DML statements; auto-handles data type conversions.

### 3. `list_tables`
List all tables in the database.
- **Parameters**: `schema`, `limit` (default 100), `offset` (default 0).
- **Features**: Supports pagination, returns table types and row count estimates.

### 4. `describe_table`
Inspect the structure of a specific table.
- **Parameters**: `table_name` (required), `schema`.
- **Features**: Returns detailed column info: name, type, nullability, primary key flags, etc.

### 5. `get_connection_status`
Check the current connection status.
- **Features**: Returns the current connection protocol and basic config.

## 🛡️ Security

Check `SECURITY.md` for details on protection measures like identifier escaping and destructive operation warnings.

## 📄 License
Licensed under [MIT License](LICENSE).
