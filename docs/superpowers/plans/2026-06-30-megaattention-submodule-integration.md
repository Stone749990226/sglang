# megaAttention Submodule Integration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 将本地 SGLang 仓库改造成基于 `release/v0.5.14` 的 megaAttention 集成开发仓库。

**Architecture:** SGLang 保持主仓库，`3rdparty/megaAttention` 作为 git submodule 固定到用户的 GitHub 仓库。SGLang 侧负责集成代码、启动路径、测试与 benchmark；megaAttention 子模块继续负责 Hopper SM90 fused kernel 主线。

**Tech Stack:** Git branch、Git remote、Git submodule、SGLang v0.5.14、megaAttention。

---

### Task 1: SGLang Git 基线

**Files:**
- Modify: `.git/config`

- [x] **Step 1: 将官方远端改名为 upstream**

Run: `git remote rename origin upstream`
Expected: `upstream` 指向 `git@github.com:sgl-project/sglang.git`。

- [x] **Step 2: 添加用户 GitHub fork 作为 origin**

Run: `git remote add origin git@github.com:Stone749990226/sglang.git`
Expected: `origin` 指向用户自己的 SGLang 仓库。

- [x] **Step 3: 拉取 v0.5.14 release 分支**

Run: `git fetch upstream refs/heads/release/v0.5.14:refs/remotes/upstream/release/v0.5.14`
Expected: 本地存在 `upstream/release/v0.5.14`。

- [x] **Step 4: 创建集成分支**

Run: `git switch -c megaattention-v0.5.14 upstream/release/v0.5.14`
Expected: 当前分支为 `megaattention-v0.5.14`。

### Task 2: megaAttention Submodule

**Files:**
- Create: `.gitmodules`
- Create: `3rdparty/megaAttention`

- [x] **Step 1: 添加 submodule**

Run:

```bash
git submodule add git@github.com:Stone749990226/megaAttention.git 3rdparty/megaAttention
```

Expected: `.gitmodules` 记录 `3rdparty/megaAttention`，子模块 checkout 到 megaAttention 的默认分支。

- [x] **Step 2: 初始化递归依赖**

Run:

```bash
git submodule update --init --recursive 3rdparty/megaAttention
```

Expected: `3rdparty/megaAttention` 及其自身 third_party 依赖可被 agent 和测试读取。

### Task 3: Agent 规则

**Files:**
- Modify: `AGENTS.md`

- [x] **Step 1: 保留现有 SGLang 学习规则**

Expected: 根目录 `AGENTS.md` 中已有的 SGLang 学习讲解规则不被删除。

- [x] **Step 2: 增加 megaAttention 集成规则**

Expected: `AGENTS.md` 明确要求修改 `3rdparty/megaAttention` 前读取子模块自己的 `AGENTS.md` 和核心设计文档。

### Task 4: 验证

**Files:**
- Read: `.gitmodules`
- Read: `AGENTS.md`

- [x] **Step 1: 检查 git 状态**

Run: `git status --short --branch`
Expected: 当前分支为 `megaattention-v0.5.14`，只有预期新增或修改文件。

- [x] **Step 2: 检查 submodule 状态**

Run: `git submodule status --recursive`
Expected: 输出包含 `3rdparty/megaAttention` 以及其递归依赖。

- [x] **Step 3: 检查远端**

Run: `git remote -v`
Expected: `origin` 是用户 GitHub，`upstream` 是官方 SGLang。
