# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Global Macro Database analytics project built with Evidence.dev. The project analyzes macro-economic data from the Global Macro Database (https://www.globalmacrodata.com/data.html) to create interactive dashboards and reports.

## Architecture

- **Framework**: Evidence.dev (business intelligence framework)
- **Data Source**: DuckDB database containing global macro data
- **Visualization**: Evidence components (LineChart, BarChart, DataTable, etc.)
- **File Structure**:
  - `pages/` - Evidence markdown pages with SQL queries and visualizations
  - `sources/gmd/` - Data source configuration and SQL queries
  - `GMD.csv` - Raw global macro database data
  - `evidence.config.yaml` - Evidence framework configuration

## Common Development Commands

```bash
# Install dependencies
npm install

# Start development server (opens at /)
npm run dev

# Start development server for Codespaces (binds to all interfaces)
npm run dev:codespaces

# Run data sources (refresh data connections)
npm run sources

# Build the project
npm run build

# Build with strict mode (fails on warnings)
npm run build:strict

# Run sources with strict validation
npm run sources:strict

# Preview built site
npm run preview
```

## GitHub Codespaces Setup

This project is fully configured for GitHub Codespaces:

### Quick Start in Codespaces
1. Open the repository in GitHub
2. Click the "Code" button → "Codespaces" → "Create codespace on main"
3. Wait for the container to build (includes Node.js 22 and Evidence extension)
4. Run: `npm install && npm run sources && npm run dev:codespaces`
5. Access the app via the forwarded port (3000)

### Codespaces Configuration
- **Container**: Node.js 22 with Evidence VS Code extension
- **Ports**: 3000 (Evidence dev server) and 4213 (Evidence preview) are auto-forwarded
- **Resources**: 4 CPUs, 4GB RAM, 32GB storage
- **Extensions**: Evidence and Svelte extensions pre-installed

## Data Architecture

The project uses a DuckDB database (`sources/gmd/gmd.duckdb`) as the primary data source:
- Connection configured in `sources/gmd/connection.yaml`
- Main query in `sources/gmd/gmd.sql` (selects all data from `gmd` table)
- Raw data in `GMD.csv` contains global macro-economic indicators

## Evidence Development Patterns

### SQL Queries
- Queries are written in markdown code blocks with `sql query_name` syntax
- Results are automatically available as `{query_name}` in components
- Query chaining supported using `${previous_query}` syntax

### Components
- Use Evidence core components: `<LineChart>`, `<BarChart>`, `<DataTable>`, `<Value>`, etc.
- Data binding: `data={query_name}`
- Formatting with `fmt` attributes (e.g., `fmt=usd`, `fmt=pct`)

### Pages
- Markdown files in `pages/` directory
- Frontmatter for metadata and configuration
- Mix of markdown, SQL queries, and Evidence components

## Theme Configuration

Custom color palettes and themes are configured in `evidence.config.yaml`:
- Light/dark mode support with theme switcher
- Custom color scales for data visualization
- Professional blue-focused color scheme

## Testing and Validation

- Use `npm run build` to validate the entire project
- Use `npm run sources:strict` to validate data connections
- Evidence has built-in data validation and type checking

## Environment Requirements

- Node.js >= 18.0.0
- npm >= 7.0.0
- DuckDB support (handled by Evidence)

## Deployment Notes

This is an Evidence project template that can be deployed to various platforms. The build process generates static files that can be served from any web server.