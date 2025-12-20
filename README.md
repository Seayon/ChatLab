# ChatLab

欢迎来到 ChatLab，它是一个免费、开源、本地化的，专注于分析聊天记录的应用。通过 AI Agent 和灵活的 SQL 引擎，你可以自由地拆解、查询甚至重构你的社交数据。

我们拒绝将你的隐私上传云端，而是把强大的分析能力直接塞进你的电脑。

它提供了对多平台的聊天记录进行导入、分析、可视化和 AI 助理探索的能力。

项目基于 Electron + Vue 3 + TypeScript 构建，内置大文件流式处理、Worker 多线程、AI Agent、聊天记录合并等能力。

该项目为个人开源项目，核心功能仅开发了两周，目前还处于早期阶段，因此可能还包含了众多不足，若您在使用当中遇到了任何问题，欢迎通过 GitHub Issue 或联系我反馈。

## 核心特性

- 多格式导入：内置解析器可识别多种导出聊天记录的格式，并统一转为标准模型
- 深度分析：群聊/私聊拥有总览、榜单、语录、成员、AI 实验室等多个仪表盘，涵盖活跃度、时间规律、龙王/夜猫/复读等指标
- AI 助手：集成 DeepSeek、通义千问等 LLM，支持关键词检索、RAG 回答、SQL 实验室、Function Calling 工具
- 流式导入与合并：即使是百万级消息量也能通过流式解析、延迟索引、临时数据库缓存实现稳定导入与冲突检测
- 跨平台发行：electron-builder 提供 macOS、Windows、Linux 三端打包脚本

## 系统架构

### Electron 主进程

- `electron/main/index.ts` 负责应用生命周期、窗口管理、自定义协议注册
- `electron/main/ipc/` 按功能拆分 IPC 模块（窗口、聊天、合并、AI、缓存），确保数据交换安全可控
- `electron/main/ai/` 集成多家 LLM，内置 Agent 管道、提示词拼装、Function Calling 工具注册

### Worker 与数据管线

- `electron/main/worker/` 中的 `workerManager` 统筹 Worker 线程，`dbWorker` 负责路由消息
- `worker/query/*` 承担活跃度、AI 搜索、高级分析、SQL 实验室等查询；`worker/import/streamImport.ts` 提供流式导入
- `parser/` 目录采用嗅探 + 解析三层架构，能在恒定内存下处理 GB 级日志文件

### 渲染进程

- Vue 3 + Nuxt UI + Tailwind CSS 负责可视化页面。`src/pages` 存放各业务页面，`src/components/analysis`、`src/components/charts` 等目录提供复用组件
- `src/stores` 通过 Pinia 管理会话、布局、AI 提示词等状态；`src/composables/useAIChat.ts` 封装 AI 对话流程
- 预加载脚本 `electron/preload/index.ts` 暴露 `window.chatApi/mergeApi/aiApi/llmApi`，确保渲染进程与主进程通信安全隔离

## AI 与数据模型

- 每次导入会生成独立 SQLite 数据库，表包括 `meta`、`member`、`member_name_history`、`message`
- 导入流程：解析文件 → 临时数据库写入 → Worker 流式导入 → 统一 Schema → 分析/AI 查询
- AI 对话记录使用独立 SQLite（`ai_conversation`、`ai_message`），并且通过 `window.aiApi` 暴露搜索、上下文、RAG、Function Calling 等接口
- 若需修改表结构，务必同时更新 `electron/main/database/core.ts` 与 `electron/main/worker/import/streamImport.ts`

## 常见问题

若 Electron 在安装或启动时出现依赖异常，可尝试：

```bash
npm install electron-fix -g
electron-fix start
```

## License

MIT
