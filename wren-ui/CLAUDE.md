# CLAUDE.md

本文件为Claude Code (claude.ai/code)在此代码库中工作时提供指导 / This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 基本命令 / Essential Commands

### 开发 / Development
- `yarn dev` - 启动开发服务器（UTC时区，需要Node.js 18） / Start development server (UTC timezone, requires Node.js 18)
- `yarn build` - 构建应用程序（分配8GB内存用于大型构建） / Build the application (allocates 8GB memory for large builds)
- `yarn start` - 启动生产服务器（UTC时区） / Start production server (UTC timezone)
- `yarn migrate` - 运行数据库迁移 / Run database migrations
- `yarn rollback` - 回滚数据库迁移 / Rollback database migrations

### 质量保证 / Quality Assurance
- `yarn lint` - 运行TypeScript类型检查和ESLint / Run TypeScript type checking and ESLint
- `yarn check-types` - 仅运行TypeScript类型检查 / Run TypeScript type checking only
- `yarn test` - 运行Jest单元测试 / Run Jest unit tests
- `yarn test:e2e` - 运行Playwright E2E测试（需要服务运行） / Run Playwright E2E tests (requires services running)
- `yarn test:e2e --headed` - 运行E2E测试并显示浏览器界面 / Run E2E tests with browser UI
- `yarn test:e2e --ui` - 交互式E2E测试开发 / Interactive E2E test development

### 代码生成 / Code Generation
- `yarn generate-gql` - 生成GraphQL类型和操作 / Generate GraphQL types and operations

## 架构概览 / Architecture Overview

### 核心结构 / Core Structure
这是一个**Next.js应用程序**，作为WrenAI的UI层，WrenAI是一个语义层和AI驱动的分析平台 / This is a **Next.js application** that serves as the UI layer for WrenAI, a semantic layer and AI-powered analytics platform. 架构遵循模块化设计，具有清晰的关注点分离 / The architecture follows a modular design with clear separation of concerns:

**前端（Next.js + Apollo Client） / Frontend (Next.js + Apollo Client)**:
- `src/pages/` - 基于文件路由的Next.js页面 / Next.js pages with file-based routing
- `src/components/` - 按功能组织的可重用React组件 / Reusable React components organized by feature
- `src/hooks/` - 自定义React hooks / Custom React hooks
- `src/styles/` - 使用styled-components和Less的全局样式 / Global styles using styled-components and Less

**后端（GraphQL + Apollo Server） / Backend (GraphQL + Apollo Server)**:
- `src/apollo/server/` - Apollo Server GraphQL API
- `src/apollo/server/resolvers/` - GraphQL解析器 / GraphQL resolvers
- `src/apollo/server/services/` - 业务逻辑服务 / Business logic services
- `src/apollo/server/repositories/` - 数据访问层 / Data access layer
- `migrations/` - Knex.js数据库迁移 / Knex.js database migrations

### 关键服务集成 / Key Services Integration
UI与三个核心服务通信 / The UI communicates with three core services:
- **Wren Engine** (端口8080) - SQL查询执行和语义建模 / (port 8080) - SQL query execution and semantic modeling
- **Wren AI Service** (端口5555) - AI驱动的自然语言处理 / (port 5555) - AI-powered natural language processing
- **Ibis Server** (端口8000) - 数据源连接 / (port 8000) - Data source connectivity

### 数据库支持 / Database Support
通过环境变量支持SQLite（默认）和PostgreSQL / Supports both SQLite (default) and PostgreSQL via environment variables:
- `DB_TYPE=sqlite` 配合 `SQLITE_FILE` 路径 / with `SQLITE_FILE` path
- `DB_TYPE=pg` 配合 `PG_URL` 连接字符串 / with `PG_URL` connection string

### 路径别名 / Path Aliases
- `@/*` → `src/*`
- `@server` → `src/apollo/server/index.ts`  
- `@server/*` → `src/apollo/server/*`

### 环境配置 / Environment Configuration
关键环境变量定义在 `src/apollo/server/config.ts` 中 / Key environment variables are defined in `src/apollo/server/config.ts`:
- 服务端点（ENGINE、AI、IBIS） / Service endpoints (ENGINE, AI, IBIS)
- 数据库配置 / Database configuration
- 功能标志（实验性Rust引擎） / Feature flags (experimental Rust engine)
- 遥测和加密设置 / Telemetry and encryption settings

### 开发环境设置要求 / Development Setup Requirements
1. Node.js 18
2. 首次启动前运行 `yarn migrate` / Run `yarn migrate` before first start
3. 如果通过Docker使用服务，设置 `OTHER_SERVICE_USING_DOCKER=true` / Set `OTHER_SERVICE_USING_DOCKER=true` if using services via Docker
4. 设置 `EXPERIMENTAL_ENGINE_RUST_VERSION=false`（或true启用实验性Rust引擎） / Set `EXPERIMENTAL_ENGINE_RUST_VERSION=false` (or true to enable experimental Rust engine)
5. E2E测试需要所有WrenAI服务运行且3000端口可用 / E2E tests require all WrenAI services running and port 3000 available

### 组件组织 / Component Organization
组件按功能区域组织 / Components are organized by feature areas:
- `chart/` - 数据可视化组件 / Data visualization components
- `diagram/` - 模式关系图表 / Schema relationship diagrams  
- `modals/` - 对话框组件 / Dialog components
- `pages/` - 页面特定组件 / Page-specific components
- `selectors/` - 输入/选择组件 / Input/selection components
- `table/` - 数据表格组件 / Data table components

### 测试策略 / Testing Strategy
- **单元测试 / Unit tests**: Jest配合React Testing Library / Jest with React Testing Library
- **E2E测试 / E2E tests**: Playwright支持多数据源配置 / Playwright with multiple data source configurations
- **类型检查 / Type checking**: TypeScript关闭严格模式 / TypeScript with strict mode disabled
- **代码检查 / Linting**: ESLint配合Next.js和Prettier配置 / ESLint with Next.js and Prettier configurations

### 重要开发说明 / Important Development Notes
- 项目间切换：更改数据库文件路径并重新运行 `yarn migrate` / Project switching: Change database file path and re-run `yarn migrate`
- 服务集成：可通过Docker运行其他服务，同时从源码开发UI层 / Service integration: Run other services via Docker while developing UI from source
- 部署要求：在建模页面点击部署按钮将项目部署到wren-ai-service / Deployment requirement: Click deploy button in modeling page to deploy project to wren-ai-service