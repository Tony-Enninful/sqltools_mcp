# SQLTools MCP

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![MCP Protocol](https://img.shields.io/badge/MCP-1.0.0-orange.svg)](https://modelcontextprotocol.io)

[[English](README_EN.md)] | [中文]

**SQLTools MCP** 是一个全能型数据库访问服务，基于 [Model Context Protocol (MCP)](https://modelcontextprotocol.io) 开发。

它的核心痛点解决能力是：**无需为每种数据库安装独立的 MCP Server，只需这一个服务，即可让 AI 助手同时支持 MySQL、Postgres、SQL Server、达梦 (DM8) 以及 SQLite。**

## ✨ 核心能力

- 🔌 **全能数据库适配**：
  - **MySQL** / **MariaDB**
  - **PostgreSQL**
  - **SQL Server (MSSQL)**
  - **达梦 (DM8)** (国内主流国产数据库)
  - **SQLite** (本地文件数据库)
- 🔄 **一键切换**：同一个 AI 会话中可以随时通过 `connect_database` 切换到不同的数据库环境。
- 🛡️ **生产安全**：具备 SQL 注入防护和针对破坏性操作（DROP/TRUNCATE 等）的预警提示。
- 📊 **优化体验**：支持大数据量分页加载，失败时提供智能修复建议。

## 📦 快速安装

```bash
# 克隆并进入目录
git clone https://github.com/huangzt/sqltools_mcp
cd sqltools-mcp

# 建议在虚拟环境中安装
pip install -e .

# 安装您需要的驱动
pip install pymysql          # MySQL
pip install psycopg2-binary  # PostgreSQL
pip install pymssql          # SQL Server
pip install jaydebeapi       # DM8 (需要 Java 环境)
```

## ⚙️ 配置指南

### 环境变量
你可以在启动 MCP 服务时设置以下环境变量来实现自动连接：

| 变量名 | 说明 | 默认值 |
|--------|------|--------|
| `DB_TYPE` | 数据库类型 (mysql, postgres, mssql, dm8, sqlite) | `sqlite` |
| `DB_HOST` | 数据库主机地址 | `localhost` |
| `DB_PORT` | 端口号 (0 则使用各协议默认端口) | `0` |
| `DB_USER` | 数据库用户名 | - |
| `DB_PASSWORD` | 数据库密码 | - |
| `DB_NAME` | 数据库名 (SQLite 为文件绝对路径) | - |

## 🚀 AI 客户端配置

### 1. Google Antigravity
编辑 `~/.gemini/antigravity/mcp_config.json`:

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
编辑配置文件：
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
在 **Settings -> Features -> MCP** (Cursor) 或 **Settings -> MCP** (Windsurf) 中添加：
- **Name**: `sqltools`
- **Type**: `command`
- **Command**: `python -m sqltools_mcp.server`

### 4. Roo Code (原 Roo Cline)
在 VS Code 中打开 Roo Code 设置，在 **MCP Config** 中添加：

```json
"sqltools": {
  "command": "python",
  "args": ["-m", "sqltools_mcp.server"]
}
```

### 5. Zed
编辑 `~/.zed/settings.json`:

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
编辑 `~/.continue/config.json`:

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

## 🛠️ 工具详情 (Tools)

AI 模型可以通过以下英文接口与数据库交互：

### 1. `connect_database`
连接或切换到目标数据库。
- **参数**: `dbtype` (必填), `host`, `port`, `username`, `password`, `dbname`.
- **特性**: 自动断开旧连接，验证新连接可用性。

### 2. `execute_sql`
执行 SQL 查询。
- **参数**: `query` (必填), `timeout`.
- **特性**: 支持 SELECT 和 DML 语句；自动处理数据类型转换。

### 3. `list_tables`
列出数据库中的所有表。
- **参数**: `schema`, `limit` (默认 100), `offset` (默认 0).
- **特性**: 支持分页，返回表类型和行数估计。

### 4. `describe_table`
查看特定表的结构。
- **参数**: `table_name` (必填), `schema`.
- **特性**: 返回详尽的列信息：名称、类型、是否可空、主键标志、默认值等。

### 5. `get_connection_status`
检查当前连接状态。
- **特性**: 返回当前连接的协议类型和基本配置（不含密码）。

## 🛡️ 安全性

本项目在 `SECURITY.md` 中详细列出了安全措施，包括表名转义和破坏性操作提醒，确保 AI 在操作数据库时的基本安全性。

## 📄 开源协议
基于 [MIT License](LICENSE) 开源。
