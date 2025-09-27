# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目概述

CUI (Common Agent UI) 是一个基于 Claude Code SDK 的现代 Web UI 平台，用于管理和运行 AI 智能体。它提供了一个浏览器界面，支持多模型并行会话、任务管理、推送通知等功能。

## 开发命令

### 核心命令
```bash
# 开发模式启动服务
npm run dev

# 开发模式启动前端
npm run dev:web

# 构建项目
npm run build

# 生产模式启动
npm run start

# 代码检查
npm run lint

# 类型检查
npm run typecheck
```

### 测试命令
```bash
# 运行所有测试
npm test

# 运行单元测试
npm run unit-tests

# 运行集成测试
npm run integration-tests

# 调试模式测试（显示详细日志）
npm run test:debug

# 测试覆盖率
npm run test:coverage

# 监听模式测试
npm run test:watch

# 测试UI界面
npm run test:ui
```

## 项目架构

### 核心组件

**CUIServer** (`src/cui-server.ts`) - 主要的服务器类，负责：
- Express 服务器初始化和配置
- 中间件链设置（CORS、认证、日志、错误处理）
- 路由注册和服务管理
- 静态文件服务和 WebSocket 支持

**服务层架构** (`src/services/`)：
- `claude-process-manager.ts` - Claude CLI 进程管理，处理与 Claude CLI 的交互
- `stream-manager.ts` - 流式响应管理，处理 SSE 连接
- `conversation-status-manager.ts` - 会话状态跟踪
- `config-service.ts` - 配置管理（存储在 `~/.cui/config.json`）
- `permission-tracker.ts` - 权限请求管理
- `file-system-service.ts` - 文件系统操作
- `notification-service.ts` - 推送通知服务
- `web-push-service.ts` - Web Push 通知
- `gemini-service.ts` - Gemini 2.5 Flash 语音识别服务

**路由层** (`src/routes/`)：
- RESTful API 路由，处理会话、配置、权限、文件系统等
- 流式响应端点，支持 Server-Sent Events
- 认证和 CORS 中间件

### 前端架构

**React + TypeScript** (`src/web/`)：
- 现代化的聊天界面，支持工具使用可视化
- 实时流式响应渲染
- 多会话管理和任务跟踪
- 主题切换和偏好设置
- PWA 支持（渐进式 Web 应用）

### 测试架构

项目使用 **Vitest** 进行测试，具有以下特点：
- 优先使用真实实现而非模拟（项目指导原则）
- 综合的单元测试覆盖率（90%+ 目标）
- 使用 `tests/__mocks__/claude` 模拟 Claude CLI 进行一致性测试
- 测试中使用静默日志（LOG_LEVEL=silent）减少噪音

## 关键配置

### 环境变量
- `NODE_ENV` - 运行环境（development/production/test）
- `LOG_LEVEL` - 日志级别（debug/info/warn/error/silent）
- `GOOGLE_API_KEY` - Gemini API 密钥（用于语音识别）
- `ANTHROPIC_API_KEY` - Anthropic API 密钥

### 配置文件位置
- 主配置：`~/.cui/config.json`
- 会话数据：`~/.cui/session-info.db`
- Claude 历史：`~/.claude/`（自动扫描）

### 重要设置
- 服务器端口：默认 25239（可配置）
- 认证令牌：启动时生成，存储在配置文件中
- 工作目录：支持多工作目录管理

## 开发注意事项

### 代码规范
- 使用 TypeScript 严格模式
- 遵循 ESLint 规则
- 导入使用 `@/` 别名（指向 `src/`）
- 优先使用 async/await 而非 Promise 链

### 测试原则
- 编写有意义的测试名称和全面的测试覆盖
- 测试中使用静默日志减少噪音
- 服务器测试使用随机端口避免冲突
- 测试中正确清理资源和进程

### 错误处理
- 所有 API 错误通过 `CUIError` 类统一处理
- 使用全局错误处理器中间件
- 流式响应错误需要特殊处理

### 会话管理
- 每个会话都有唯一的 ID 和状态跟踪
- 支持会话分支和恢复功能
- 会话数据持久化到 SQLite 数据库

## 特殊功能

### 工具使用可视化
- 支持所有 Claude Code 工具的可视化渲染
- 包括文件编辑、搜索、网络请求等
- 实时显示工具执行状态和结果

### 推送通知
- 支持 ntfy 和 Web Push 两种方式
- 任务完成和权限请求时发送通知
- 需要用户在设置中配置

### 语音识别
- 使用 Gemini 2.5 Flash 提供高精度语音识别
- 需要配置 GOOGLE_API_KEY
- 仅在 HTTPS 环境下可用（iOS 限制）

### 多模型支持
- 通过 claude-code-router 支持多种模型提供商
- 包括 OpenRouter、Ollama 等
- 用户可在设置中选择模型提供商