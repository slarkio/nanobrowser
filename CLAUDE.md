# CLAUDE.md

此文件为 Claude Code (claude.ai/code) 在此仓库中工作时提供指导。

## 项目概述

Nanobrowser 是一个开源的 AI 网页自动化工具，以 Chrome 扩展的形式运行。它使用多智能体系统，专门的 AI 智能体（规划器、导航器、验证器）协作完成网页自动化任务。

## 开发命令

### 安装和设置
```bash
pnpm install                    # 安装依赖
```

### 构建命令
```bash
pnpm build                      # 为 Chrome 构建（生产环境）
pnpm build:firefox              # 为 Firefox 构建（生产环境）
pnpm zip                        # 构建并创建 Chrome 分发包
pnpm zip:firefox                # 构建并创建 Firefox 分发包
```

### 开发模式
```bash
pnpm dev                        # 启动开发服务器，支持热重载
pnpm dev:firefox                # 启动 Firefox 开发服务器
```

### 代码质量
```bash
pnpm lint                       # 运行 ESLint 并修复问题
pnpm lint:fix                   # 运行 ESLint 激进修复
pnpm prettier                   # 使用 Prettier 格式化代码
pnpm type-check                 # 运行 TypeScript 类型检查
```

### 测试
```bash
pnpm e2e                        # 运行端到端测试（Chrome）
pnpm e2e:firefox                # 运行端到端测试（Firefox）
```

### 清理
```bash
pnpm clean                      # 清理所有构建输出和依赖
pnpm clean:bundle               # 仅清理构建输出
pnpm clean:install              # 清理并重新安装依赖
```

## 架构概述

### 多智能体系统
核心功能围绕三个专门的 AI 智能体：

- **规划智能体** (`chrome-extension/src/background/agent/agents/planner.ts`): 高级任务规划和策略制定
- **导航智能体** (`chrome-extension/src/background/agent/agents/navigator.ts`): 网页交互和 DOM 操作
- **验证智能体** (`chrome-extension/src/background/agent/agents/validator.ts`): 任务验证和确认

### 核心组件

1. **后台脚本** (`chrome-extension/src/background/index.ts`): 主要的 service worker，管理智能体执行和 Chrome 扩展 API

2. **浏览器上下文** (`chrome-extension/src/background/browser/context.ts`): 管理浏览器状态、标签页切换和调试连接

3. **智能体执行器** (`chrome-extension/src/background/agent/executor.ts`): 编排智能体交互和任务执行流程

4. **侧边栏面板** (`pages/side-panel/src/SidePanel.tsx`): 基于 React 的用户交互和聊天界面

5. **选项页面** (`pages/options/src/Options.tsx`): 配置 LLM 提供商和智能体模型的设置界面

6. **内容脚本** (`pages/content/src/index.ts`): 注入脚本，用于 DOM 操作和页面交互

### 工作区结构
- `chrome-extension/`: 核心扩展逻辑和后台脚本
- `pages/`: 前端 React 应用（侧边栏、选项、内容）
- `packages/`: 共享工具和存储管理
- 使用 Turborepo 进行 monorepo 管理，配合 pnpm workspaces

### 智能体通信
智能体通过结构化事件系统通信，状态包括：
- `TASK_START`, `TASK_OK`, `TASK_FAIL`, `TASK_CANCEL`
- `STEP_START`, `STEP_OK`, `STEP_FAIL`
- `ACT_START`, `ACT_OK`, `ACT_FAIL`

### 存储和配置
- 使用 Chrome 存储 API 进行数据持久化
- 支持多种 LLM 提供商（OpenAI、Anthropic、Gemini、Ollama 等）
- 可配置的智能体模型和通用设置

### 开发注意事项
- 使用 TypeScript、React 和 Vite 构建
- 使用 Tailwind CSS 进行样式设计
- 支持 Chrome 和 Firefox（条件构建）
- 实现 Chrome 调试器协议用于高级页面交互
- 集成语音转文本和对话历史功能

## Fork 项目开发准则

**重要**：此项目为 fork 项目，开发时需遵循以下准则：

- **避免修改原始文件**：尽量不直接修改现有的核心源代码文件，以避免与上游仓库产生冲突
- **优先扩展机制**：通过插件、配置文件、新增模块等方式实现功能
- **创建新文件**：如需修改现有功能，优先考虑创建新文件覆盖或扩展原有实现
- **保持兼容性**：确保修改不破坏原有功能和接口
- **合并友好**：保持代码结构便于后续与上游仓库合并